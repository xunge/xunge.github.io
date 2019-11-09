---
title: 搭建本地 Anaconda 镜像
tags:
  - anaconda
categories:
  - 经验值
date: 2019-07-06 19:54:04
updated: 2019-07-06 19:54:04
---


Anaconda是一个免费开源的Python等语言的发行版本，致力于简化包管理和部署，可以大大提高环境搭建效率。

然而Anaconda国外源在国内下载速度较慢，虽然国内有清华源可以大大提高下载速度（2019年4月清华源曾因版权原因关闭，但在5月已重新开放），但是肯定没有搭建一个本地源速度快。本文将详细介绍如何将Anaconda镜像安装在本地，以供本机以及局域网内的其他电脑访问。

<!-- more -->

## 下载所有镜像文件到本地

搭建本地镜像肯定需要将所有镜像文件下载到本地。

这里感谢清华开源下载镜像文件的Python[代码](https://github.com/tuna/tunasync-scripts/blob/master/anaconda.py)，这里进行了一定的修改，代码如下。

可以看到代码中的路径改为了国内的清华源， repos 只选择了 `main` 和 `free`，arches 选择了 `linux-64` 和 `win-64`，当然也可以选择同步注释代码中的更多系统和版本。博主下载了这些文件共189.1 GB（2019年6月），也就是说只需要占用不到 200 GB 的磁盘空间，无需下载即可使用Anaconda安装Python包，还是很实用的。

具体代码如下：

```python
#!/usr/bin/env python3
import os
import json
import hashlib
import tempfile
import shutil
import logging
import subprocess as sp
from pathlib import Path
from email.utils import parsedate_to_datetime

import requests
from pyquery import PyQuery as pq

DEFAULT_CONDA_REPO_BASE = "https://repo.continuum.io"
DEFAULT_CONDA_CLOUD_BASE = "https://conda.anaconda.org"

CONDA_REPO_BASE_URL = os.getenv("CONDA_REPO_URL", "https://repo.continuum.io")
CONDA_CLOUD_BASE_URL = os.getenv("CONDA_COULD_URL", "https://conda.anaconda.org")

WORKING_DIR = os.getenv("TUNASYNC_WORKING_DIR")

CONDA_REPOS = ("main", "free")
# CONDA_REPOS = ("main", "free", "r", "mro", "pro")

CONDA_ARCHES = (
    "linux-64", "win-64"
)
# CONDA_ARCHES = (
#     "noarch", "linux-64", "linux-32", "linux-armv6l", "linux-armv7l",
#     "linux-ppc64le", "osx-64", "osx-32", "win-64", "win-32"
# )

CONDA_CLOUD_REPOS = (
    "conda-forge/linux-64", "conda-forge/osx-64", "conda-forge/win-64", "conda-forge/noarch",
    "msys2/win-64", "msys2/noarch",
    "bioconda/noarch", "bioconda/linux-64", "bioconda/osx-64",
    "menpo/linux-64", "menpo/osx-64", "menpo/win-64", "menpo/win-32", "menpo/noarch",
    "pytorch/linux-64", "pytorch/osx-64", "pytorch/win-64", "pytorch/win-32", "pytorch/noarch", "peterjc123/win-64", "peterjc123/noarch",
)

logging.basicConfig(
    level=logging.INFO,
    format="[%(asctime)s] [%(levelname)s] %(message)s",
)


def md5_check(file: Path, md5: str=None):
    m = hashlib.md5()
    with file.open('rb') as f:
        while True:
            buf = f.read(1*1024*1024)
            if not buf:
                break
            m.update(buf)
    return m.hexdigest() == md5


def curl_download(remote_url: str, dst_file: Path, md5: str=None):
    sp.check_call([
        "curl", "-o", str(dst_file),
        "-sL", "--remote-time", "--show-error",
        "--fail", remote_url,
    ])
    if md5 and (not md5_check(dst_file, md5)):
        return "MD5 mismatch"


def sync_repo(repo_url: str, local_dir: Path, tmpdir: Path):
    logging.info("Start syncing {}".format(repo_url))
    local_dir.mkdir(parents=True, exist_ok=True)

    repodata_url = repo_url + '/repodata.json'
    bz2_repodata_url = repo_url + '/repodata.json.bz2'

    tmp_repodata = tmpdir / "repodata.json"
    tmp_bz2_repodata = tmpdir / "repodata.json.bz2"

    curl_download(repodata_url, tmp_repodata)
    curl_download(bz2_repodata_url, tmp_bz2_repodata)

    with tmp_repodata.open() as f:
        repodata = json.load(f)

    packages = repodata['packages']
    for filename, meta in packages.items():
        file_size, md5 = meta['size'], meta['md5']

        pkg_url = '/'.join([repo_url, filename])
        dst_file = local_dir / filename

        if dst_file.is_file():
            stat = dst_file.stat()
            local_filesize = stat.st_size

            if file_size == local_filesize:
                logging.info("Skipping {}".format(filename))
                continue

            dst_file.unlink()

        for retry in range(3):
            logging.info("Downloading {}".format(filename))
            err = curl_download(pkg_url, dst_file, md5=md5)
            if err is None:
                break
            logging.error("Failed to download {}: {}".format(filename, err))

    shutil.move(str(tmp_repodata), str(local_dir / "repodata.json"))
    shutil.move(str(tmp_bz2_repodata), str(local_dir / "repodata.json.bz2"))


def sync_installer(repo_url, local_dir: Path):
    logging.info("Start syncing {}".format(repo_url))
    local_dir.mkdir(parents=True, exist_ok=True)

    def remote_list():
        r = requests.get(repo_url)
        d = pq(r.content)
        for tr in d('table').find('tr'):
            tds = pq(tr).find('td')
            if len(tds) != 4:
                continue
            fname = tds[0].find('a').text
            md5 = tds[3].text
            yield (fname, md5)

    for filename, md5 in remote_list():
        pkg_url = "/".join([repo_url, filename])
        dst_file = local_dir / filename

        if dst_file.is_file():
            r = requests.head(pkg_url)
            remote_filesize = int(r.headers['content-length'])
            remote_date = parsedate_to_datetime(r.headers['last-modified'])
            stat = dst_file.stat()
            local_filesize = stat.st_size
            local_mtime = stat.st_mtime

            if remote_filesize == local_filesize and remote_date.timestamp() == local_mtime:
                logging.info("Skipping {}".format(filename))
                continue

            dst_file.unlink()

        for retry in range(3):
            logging.info("Downloading {}".format(filename))
            err = curl_download(pkg_url, dst_file, md5=md5)
            if err is None:
                break
            logging.error("Failed to download {}: {}".format(filename, err))


def main():
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("--working-dir", default=WORKING_DIR)
    args = parser.parse_args()

    if args.working_dir is None:
        raise Exception("Working Directory is None")

    working_dir = Path(args.working_dir)

    # for dist in ("archive", "miniconda"):
    #     remote_url = "{}/{}".format(CONDA_REPO_BASE_URL, dist)
    #     local_dir = working_dir / dist
    #     try:
    #         sync_installer(remote_url, local_dir)
    #     except Exception:
    #         logging.exception("Failed to sync installers of {}".format(dist))

    for repo in CONDA_REPOS:
        for arch in CONDA_ARCHES:
            remote_url = "{}/pkgs/{}/{}".format(CONDA_REPO_BASE_URL, repo, arch)
            local_dir = working_dir / "pkgs" / repo / arch

            tmpdir = tempfile.mkdtemp()
            try:
                sync_repo(remote_url, local_dir, Path(tmpdir))
            except Exception:
                logging.exception("Failed to sync repo: {}/{}".format(repo, arch))
            finally:
                shutil.rmtree(tmpdir)

    # for repo in CONDA_CLOUD_REPOS:
    #     remote_url = "{}/{}".format(CONDA_CLOUD_BASE_URL, repo)
    #     local_dir = working_dir / "cloud" / repo

    #     tmpdir = tempfile.mkdtemp()
    #     try:
    #         sync_repo(remote_url, local_dir, Path(tmpdir))
    #     except Exception:
    #         logging.exception("Failed to sync repo: {}".format(repo))
    #     finally:
    #         shutil.rmtree(tmpdir)


if __name__ == "__main__":
    main()
```

## 建立索引

下载的文件会在 `pkgs` 根目录下，我们需要运行以下命令

```
conda index pkgs/*
```

运行需要较长时间，运行完成后会在 `free` 和 `main` 文件夹内生成 `noarch` 文件夹。

## 搭建 http 文件服务器

为了使局域网内的用户都可访问本地Anaconda镜像，我们首先搭建一个本地http服务器，参考这篇 [博客](https://www.jianshu.com/p/e1a6219167cf)

在 ubuntu 系统下运行下面命令

```
sudo apt install apache2
```

apache2 的配置文件是 `/etc/apache2/apache2.conf`。

服务器默认的访问路径在 `/var/www/html` 目录下。

创建软链接，例如我们的镜像 pkgs 文件夹在 `/home/ubuntu/mirror/anaconda/pkgs` ，在 `/var/www/html` 目录下通过命令 `ln -s /home/ubuntu/mirror/anaconda/pkgs/ anaconda/pkgs` 创建一个软连接。就可以通过 `http://192.168.1.10/anaconda/pkgs` 访问到文件目录。

## 使用本地镜像

通过以下命令设置Anaconda的镜像路径：

```
conda config --add channels http://192.168.1.10/anaconda/pkgs/free/
conda config --add channels http://192.168.1.10/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

然后编辑配置文件 `.condarc`，一般在 `~/.condarc`，去掉最后的 `- defaults`

至此，本地镜像的配置完成，我们可以离线安装Anaconda管理包了，速度不是一般的快。

## 定时运行 anaconda.py 以更新镜像 (2019.08.11 更新)

为了使得镜像及时更新，我们可以使用 Linux 的 crontab 服务定时更新 anaconda.py 脚本。具体方法如下：

运行 `crontab –e` 编写一条定时任务：

```
0 1    * * 6    /usr/bin/python /home/ubuntu/anaconda.py --working-dir /home/ubuntu/anaconda-mirror > /home/ubuntu/auto.log
```

意思是每周六的凌晨 1:00 执行 anaconda.py 脚本。

其他关于 crontab 的详细说明见文档说明 [19. crontab 定时任务](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html) 

运行 `crontab –l` 进行验证。

运行 `service cron restart` 重启服务。