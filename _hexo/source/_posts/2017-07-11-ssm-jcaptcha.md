---
title: SSM 框架下的 jcaptcha 验证码实例
tags:
  - SSM
  - jcaptcha
categories: 经验值
thumbnail: https://img.xungejiang.com/static/images/17-7-11/jcapcha.jpg
updated: '2017-07-11 09:01:42'
date: 2017-07-11 09:01:42
---


最近把 SSM(Spring + Spring MVC + MyBatis) 的 Maven 项目搭建好了，并完成了登录和注册功能。其中注册功能使用 jcaptcha 加入了验证码，并使用 AJAX 完成了基本的验证功能。

本文主要介绍一下 `jcaptcha` 验证码的实现 (IDEA , 附源码)。

<!--more-->

![](https://img.xungejiang.com/static/images/17-7-11/luntan.png)



项目源码：

[https://github.com/xunge/SSM-jcaptcha](https://github.com/xunge/SSM-jcaptcha)

参考：

[jcaptcha 官网](http://jcaptcha.sourceforge.net/)

[IDEA 搭建 SSM](http://blog.csdn.net/u011403655/article/details/46843331)

[jcaptcha 验证码](http://ojeta.iteye.com/blog/2111963)

由于 jcaptcha 有个缺陷，就是无法使用 AJAX 进行验证，因为一旦验证就会清除 session，这就导致如果使用 AJAX 验证后，如果输入的验证码错误，就无法重复验证，只有刷新网页才可重新使用。

这里参考 [这篇博客](http://blog.csdn.net/lovesomnus/article/details/50487486)，将清除 session 的操作提取出来，便可以使用 AJAX 进行验证了。

## 项目介绍

1. 注册页面使用 `jcaptcha` 实现了验证码功能，并使用AJAX技术实时验证。
2. 注册页面的邮箱输入完成，光标移开输入框后，使用AJAX技术到后台数据库进行查找，如果已经注册过则提示该邮箱已被注册。

## maven 依赖

```xml
<dependency>
    <groupId>com.octo.captcha</groupId>
    <artifactId>jcaptcha-all</artifactId>
    <version>1.0-RC6</version>
    <exclusions>
        <exclusion>
            <groupId>quartz</groupId>
            <artifactId>quartz</artifactId>
        </exclusion>
        <exclusion>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
        </exclusion>
        <exclusion>
            <groupId>commons-pool</groupId>
            <artifactId>commons-pool</artifactId>
            </exclusion>
        <exclusion>
            <groupId>hsqldb</groupId>
            <artifactId>hsqldb</artifactId>
        </exclusion>
        <exclusion>
            <groupId>net.sf.ehcache</groupId>
            <artifactId>ehcache</artifactId>
        </exclusion>
        <exclusion>
            <groupId>concurrent</groupId>
            <artifactId>concurrent</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.springframework</groupId>
            <artifactId>spring</artifactId>
        </exclusion>
        <exclusion>
            <groupId>xerces</groupId>
            <artifactId>xercesImpl</artifactId>
        </exclusion>
        <exclusion>
            <groupId>xerces</groupId>
            <artifactId>xmlParserAPIs</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## jcaptcha 配置文件：spring-jcaptcha.xml

在 `resources/spring` 下新建 `spring-jcaptcha.xml` 。

该文件主要控制验证码的样式，可根据官网适当修改。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"
       default-lazy-init="true">

    <!--<bean id="captchaService" class="com.octo.captcha.service.multitype.GenericManageableCaptchaService">
        <constructor-arg index="0" ref="imageEngine"/>
        <constructor-arg type="int" index="1" value="180"/>
        <constructor-arg type="int" index="2" value="100000"/>
    </bean>-->
    <!--这里我为了使用 AJAX 验证验证码，使用了自己创建的 captchaService-->
    <bean id="captchaService" class="com.xunge.springemp.service.impl.CustomGenericManageableCaptchaService">
        <constructor-arg index="0"><ref bean="imageEngine"/></constructor-arg>
        <constructor-arg index="1"><value>180</value></constructor-arg>
        <constructor-arg index="2"><value>180000</value></constructor-arg>
    </bean>

    <bean id="imageEngine" class="com.octo.captcha.engine.GenericCaptchaEngine">
        <constructor-arg index="0">
            <list>
                <ref bean="captchaFactory"/>
            </list>
        </constructor-arg>
    </bean>

    <bean id="captchaFactory" class="com.octo.captcha.image.gimpy.GimpyFactory">
        <constructor-arg>
            <ref bean="wordgen"/>
        </constructor-arg>
        <constructor-arg>
            <ref bean="wordtoimage"/>
        </constructor-arg>
    </bean>

    <bean id="wordgen" class= "com.octo.captcha.component.word.wordgenerator.RandomWordGenerator">
        <!--可选字符-->
        <constructor-arg>
            <value>aabbccddeefgghhkkmnnooppqqsstuuvvwxxyyzz</value>
        </constructor-arg>
    </bean>

    <bean id="wordtoimage" class="com.octo.captcha.component.image.wordtoimage.ComposedWordToImage">
        <constructor-arg index="0">
            <ref bean="fontGenRandom"/>
        </constructor-arg>
        <constructor-arg index="1">
            <ref bean="backGenUni"/>
        </constructor-arg>
        <constructor-arg index="2">
            <ref bean="decoratedPaster"/>
        </constructor-arg>
    </bean>

    <bean id="fontGenRandom" class="com.octo.captcha.component.image.fontgenerator.RandomFontGenerator">
        <!--最小字体-->
        <constructor-arg index="0">
            <value>26</value>
        </constructor-arg>
        <!--最大字体-->
        <constructor-arg index="1">
            <value>34</value>
        </constructor-arg>
        <constructor-arg index="2">
            <list>
                <bean class="java.awt.Font">
                    <constructor-arg index="0"><value>Arial</value></constructor-arg>
                    <constructor-arg index="1"><value>0</value></constructor-arg>
                    <constructor-arg index="2"><value>32</value></constructor-arg>
                </bean>
            </list>
        </constructor-arg>
    </bean>
    <bean id="backGenUni" class="com.octo.captcha.component.image.backgroundgenerator.UniColorBackgroundGenerator">
        <!--背景宽度-->
        <constructor-arg index="0">
            <value>110</value>
        </constructor-arg>
        <!--背景高度-->
        <constructor-arg index="1">
            <value>50</value>
        </constructor-arg>
    </bean>

    <bean id="decoratedPaster" class="com.octo.captcha.component.image.textpaster.DecoratedRandomTextPaster">
        <!--最大字符长度-->
        <constructor-arg type="java.lang.Integer" index="0">
            <value>4</value>
        </constructor-arg>
        <!--最小字符长度-->
        <constructor-arg type="java.lang.Integer" index="1">
            <value>4</value>
        </constructor-arg>
        <!--文本颜色-->
        <constructor-arg index="2">
            <ref bean="colorGen"/>
        </constructor-arg>
        <!--文本混淆-->
        <constructor-arg index="3">
            <list>
                <!--<ref bean="baffleDecorator"/>-->
            </list>
        </constructor-arg>
    </bean>
    <bean id="baffleDecorator" class="com.octo.captcha.component.image.textpaster.textdecorator.BaffleTextDecorator">
        <constructor-arg type="java.lang.Integer" index="0"><value>1</value></constructor-arg>
        <constructor-arg type="java.awt.Color" index="1"><ref bean="colorWrite"/></constructor-arg>
    </bean>
    <bean id="colorGen" class="com.octo.captcha.component.image.color.SingleColorGenerator">
        <constructor-arg type="java.awt.Color" index="0">
            <ref bean="colorBlack"/>
        </constructor-arg>
    </bean>
    <bean id="colorWrite" class="java.awt.Color">
        <constructor-arg type="int" index="0">
            <value>255</value>
        </constructor-arg>
        <constructor-arg type="int" index="1">
            <value>255</value>
        </constructor-arg>
        <constructor-arg type="int" index="2">
            <value>255</value>
        </constructor-arg>
    </bean>
    <bean id="colorBlack" class="java.awt.Color">
        <constructor-arg type="int" index="0">
            <value>50</value>
        </constructor-arg>
        <constructor-arg type="int" index="1">
            <value>50</value>
        </constructor-arg>
        <constructor-arg type="int" index="2">
            <value>50</value>
        </constructor-arg>
    </bean>
</beans>
```

## web.xml 代码

因为是在 `resources/spring` 下新建 `spring-jcaptcha.xml` ， 所以 `web.xml` 无需重新配置。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
  <display-name>spring</display-name>

  <servlet>
    <servlet-name>spring</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>
        classpath:spring/spring-*.xml
      </param-value>
    </init-param>
  </servlet>

  <servlet-mapping>
    <servlet-name>spring</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```

## JcaptchaImageCreater.java

在 `controller` 下新建 `JcaptchaImageCreater.java`，用来生成验证码图片。

```java
import java.awt.image.BufferedImage;
import java.io.ByteArrayOutputStream;
import java.io.IOException;

import javax.imageio.ImageIO;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.octo.captcha.service.image.ImageCaptchaService;

@Controller
@RequestMapping("/captcha")
public class JcaptchaImageCreater {

    private Logger logger = LoggerFactory.getLogger(getClass());

    @Autowired
    private ImageCaptchaService imageCaptchaService;

    @RequestMapping
    public void handleRequest(HttpServletRequest request, HttpServletResponse response) {
        try {
            ByteArrayOutputStream jpegOutputStream = new ByteArrayOutputStream();
            String captchaId = request.getSession().getId();
            BufferedImage challenge = imageCaptchaService.getImageChallengeForID(captchaId, request.getLocale());

            response.setHeader("Cache-Control", "no-store");
            response.setHeader("Pragma", "no-cache");
            response.setDateHeader("Expires", 0L);
            response.setContentType("image/jpeg");

            ImageIO.write(challenge, "jpeg", jpegOutputStream);
            byte[] captchaChallengeAsJpeg = jpegOutputStream.toByteArray();

            ServletOutputStream respOs = response.getOutputStream();
            respOs.write(captchaChallengeAsJpeg);
            respOs.flush();
            respOs.close();
        } catch (IOException e) {
            logger.error("generate captcha image error: {}", e.getMessage());
        }
    }

}
```

## CustomGenericManageableCaptchaService.java 重写 GenericManageableCaptchaService.java

在 `service` 下新建 `CustomGenericManageableCaptchaService.java`，将 removeCaptcha 方法提出来，便可以使用 AJAX 进行验证。

```java
import com.octo.captcha.engine.CaptchaEngine;
import com.octo.captcha.service.CaptchaServiceException;
import com.octo.captcha.service.multitype.GenericManageableCaptchaService;

/**
 * @Description: TODO
 * @author Somnus
 * @date 2015年11月24日 下午1:21:50
 * @version V1.0
 */
public class CustomGenericManageableCaptchaService extends GenericManageableCaptchaService{

    /**
     * @param captchaEngine
     * @param minGuarantedStorageDelayInSeconds
     * @param maxCaptchaStoreSize
     */
    public CustomGenericManageableCaptchaService(CaptchaEngine captchaEngine, int minGuarantedStorageDelayInSeconds,
                                                 int maxCaptchaStoreSize) {
        super(captchaEngine, minGuarantedStorageDelayInSeconds, maxCaptchaStoreSize);
        // TODO Auto-generated constructor stub
    }
    /**
     * 修改验证码校验逻辑，默认的是执行了该方法后，就把sessionid从store当中移除<br/>
     * 然而在ajax校验的时候，如果第一次验证失败，第二次还得重新刷新验证码，这种逻辑不合理<br/>
     * 现在修改逻辑，只有校验通过以后，才移除sessionid。 Method Name：validateResponseForID .
     *
     * @param ID
     * @param response
     * @return
     * @throws CaptchaServiceException
     *             the return type：Boolean
     */
    @Override
    public Boolean validateResponseForID(String ID, Object response)
            throws CaptchaServiceException {
        if (!this.store.hasCaptcha(ID)) {
            throw new CaptchaServiceException(
                    "Invalid ID, could not validate unexisting or already validated captcha");
        }
        Boolean valid = this.store.getCaptcha(ID).validateResponse(response);
        //源码的这一句是没被注释的，这里我们注释掉，在下面暴露一个方法给我们自己来移除sessionId
        //this.store.removeCaptcha(ID);
        return valid;
    }

    /**
     * 移除session绑定的验证码信息.
     * Method Name：removeCaptcha .
     * @param sessionId
     * the return type：void
     */
    public void removeCaptcha(String sessionId){
        if(sessionId!=null && this.store.hasCaptcha(sessionId)){
            this.store.removeCaptcha(sessionId);
        }
    }
}
```

## LoginController

在 `controller` 下新建 `LoginController`，进行用户注册和检查验证码的方法。

```java
import javax.servlet.http.HttpServletRequest;

import com.octo.captcha.service.image.ImageCaptchaService;
import com.xunge.springemp.service.impl.CustomGenericManageableCaptchaService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.SessionAttributes;
import org.springframework.web.servlet.ModelAndView;

import com.xunge.springemp.dao.UserDAO;
import com.xunge.springemp.pojo.User;
import com.xunge.springemp.service.IUserService;

@Controller
@SessionAttributes("username")
public class LoginController {

	@Autowired
	private IUserService userService;

	@Autowired
	private ImageCaptchaService imageCaptchaService;

	@Autowired
	private CustomGenericManageableCaptchaService customGenericManageableCaptchaService;

	@Autowired
	private UserDAO userDAO;

	@RequestMapping("userAdd")
	public ModelAndView doAdd(User user, String captcha, HttpServletRequest request) throws Exception {

		Boolean isResponseCorrect = imageCaptchaService.validateResponseForID(request.getSession().getId(), captcha);
		if (isResponseCorrect) {
			userDAO.addUser(user);
			customGenericManageableCaptchaService.removeCaptcha(request.getSession().getId());
			ModelAndView mv = new ModelAndView("personal");
			return mv;
		} else {
			ModelAndView mv = new ModelAndView("register");
			return mv;
		}
	}

	@RequestMapping("/checkCaptcha")
	public @ResponseBody int checkCaptcha(String captcha, HttpServletRequest request) throws Exception {
		Boolean isResponseCorrect = imageCaptchaService.validateResponseForID(request.getSession().getId(), captcha);

		if (isResponseCorrect == false) {
			return 0;
		} else {
			return 1;
		}
	}
}

```

## 前端代码

```html
<input type="text" class="form-control input-lg input_size input-captcha" id="captcha" name="captcha" maxlength="4" placeholder="请输入验证码" />
<img class="img-captcha" src="captcha" onclick="this.src='captcha?d='+new Date().getTime()" />
```

## JS 代码

前端 AJAX 验证使用 JQuery 的 validate，进行表单的验证更美观。

```javascript
$("#regform").validate({
    rules: {
        captcha: {
            required: true,
            remote: {
                url:"checkCaptcha.do",
                type:"get",
                contentType: "application/json;charset=utf-8",
                data:{
                    captcha:function(){return $("#captcha").val();}
                },
                dataFilter: function(data, type) {
                    if (data == 0)
                        return false;
                    else
                        return true;
                }
            }
        }
    },
    messages: {
        captcha: {
            required: "请输入验证码",
            remote: "验证码错误"
        }
    },
});
```

## 总结

更详细信息可以参考源码。

