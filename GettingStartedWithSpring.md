# Using SocialAuth with Spring MVC #

## Prerequisites ##
Authenticating using the external oAuth providers requires that we register our application with the providers and obtain a key/secret from them that will be configured in our application. So following steps are needed to be set up before we can begin.

  1. Public domain - You will need a public domain for testing. You should have a public domain because most of the providers require a public domain to be specified when you register an application with them.
  1. Get the API Keys: You can get the API keys from the following URLs.
    * Google [(show screenshot)](Google.md) - http://code.google.com/apis/accounts/docs/RegistrationForWebAppsAuto.html
    * Yahoo [(show screenshot)](Yahoo.md) - https://developer.apps.yahoo.com/dashboard/createKey.html
    * Twitter  - http://twitter.com/apps
    * Facebook  - http://www.facebook.com/developers/apps.php
    * Hotmail [(show screenshot)](Hotmail.md) - http://msdn.microsoft.com/en-us/library/cc287659.aspx
    * FourSquare - [(show screenshot)](FourSquare.md) - https://foursquare.com/oauth/
    * MySpace - [(show screenshot)](MySpace.md) - http://developer.myspace.com/Apps.mvc
    * Linkedin - [(show screenshot)](Linkedin.md) - https://www.linkedin.com/secure/developer
  1. You can now develop the application using keys and secrets obtained above and deploy the application on your public domain. However, most people need to [test the application on a local development machine](HowToRunApplicationWithLocalhostOnWindows.md) using the API keys and secrets obtained above.
  1. We do not recommend it at all, but if you do not want to obtain your own keys and secrets while testing, you can use the keys and secrets that we obtained by registering "opensource.brickred.com" for our demo.  [Follow the same steps](HowToRunApplicationWithLocalhostOnWindows.md) as above but with domain as "opensource.brickred.com" and [keys from our sample](SampleProperties.md).

## Include jars ##

Download [socialauth-java-sdk-4.0.zip](http://socialauth.googlecode.com/files/socialauth-java-sdk-4.0.zip) and include the following in your project from dist and dependencies folder

  1. socialauth-4.0.jar
  1. socialauth-spring-2.0.jar
  1. jars from dependencies folder

## Implementation ##

socialauth-spring.jar contains two classes which support Spring application. SocialAuthWebController is the main controller for managing socialauth-provider connection flow. It redirects to the actual provider for login and handles the callback. Once the user provides credentials and the provider redirects back to your application, one of the callback methods is called. Other class is SocialAuthTemplate which is the wrapping bean for the socialauth manager.

  1. Create properties.xml file which contains all the application key/secret for different providers.
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
               http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
            http://www.springframework.org/schema/util 
            http://www.springframework.org/schema/util/spring-util-3.0.xsd
            http://www.springframework.org/schema/context 
            http://www.springframework.org/schema/context/spring-context-3.0.xsd">
        
    <util:properties id="socialAuthProperties">
        <prop key="www.google.com.consumer_key">opensource.brickred.com</prop>
        <prop key="www.google.com.consumer_secret">YC06FqhmCLWvtBg/O4W/aJfj</prop>
        <prop key="api.login.yahoo.com.consumer_key">dj0yJmk9VTdaSUVTU3RrWlRzJmQ9WVdrOWNtSjZNMFpITm1VbW
          NHbzlNQS0tJnM9Y29uc3VtZXJzZWNyZXQmeD1iMA--</prop>
        <prop key="api.login.yahoo.com.consumer_secret">1db3d0b897dac60e151aa9e2499fcb2a6b474546</prop>
        <prop key="twitter.com.consumer_key">E3hm7J9IQbWLijpiQG7W8Q</prop>
        <prop key="twitter.com.consumer_secret">SGKNuXyybt0iDdgsuzVbFHOaemV7V6pr0wKwbaT2MH0</prop>
        <prop key="graph.facebook.com.consumer_key">152190004803645</prop>
        <prop key="graph.facebook.com.consumer_secret">64c94bd02180b0ade85889b44b2ba7c4</prop>
        <prop key="consent.live.com.consumer_key">000000004403D60E</prop>
        <prop key="consent.live.com.consumer_secret">cYqlii67pTvgPD4pdB7NUVC7L4MIHCcs</prop>
        <prop key="api.linkedin.com.consumer_key">
            9-mmqg28fpMocVuAg87exH-RXKs70yms52GSFIqkZN25S3m96kdPGBbuSxdSBIyL
        </prop>
        <prop key="api.linkedin.com.consumer_secret">
            e6NBqhDYE1fX17RwYGW5vMp25Cvh7Sbw9t-zMYTIW_T5LytY5OwJ12snh_YftgE4
        </prop>
        <prop key="foursquare.com.consumer_key">JQKEM1PHWFW4YF2YPEQBRRESXE3SBGNCYJWWDTZKF3IZNJ3V</prop>
        <prop key="foursquare.com.consumer_secret">4IILLDFDVPP2LC554S4KXKETQNTDKPDSEVCKVHA2QEHKYBEQ</prop>
        <prop key="api.myspace.com.consumer_key">29db395f5ee8426bb90b1db65c91c956</prop>
        <prop key="api.myspace.com.consumer_secret">
            0fdccc829c474e42867e16b68cda37a4c4b7b08eda574fe6a959943e3e9be709
        </prop>
    </util:properties>
</beans>
```
  1. Do the following in root-context.xml file
    * Import properties file in root-context.xml
```
<import resource="properties.xml" />
```
    * Add a bean entry named "socialAuthConfig" and specify the class as "org.brickred.socialauth.SocialAuthConfig" and set property applicationProperties.
```
<bean id="socialAuthConfig" class="org.brickred.socialauth.SocialAuthConfig">
    <property name="applicationProperties"><ref bean="socialAuthProperties"/></property>
</bean>
```
    * Add a bean entry named "socialAuthManager" and specify the class as "org.brickred.socialauth.SocialAuthManager" and set property socialAuthConfig.
```
<bean id="socialAuthManager" class="org.brickred.socialauth.SocialAuthManager" scope="session">
    <property name="socialAuthConfig"><ref bean="socialAuthConfig"/></property>
     <aop:scoped-proxy/>
</bean>
```
    * Add a bean entry named socialAuthTemplate, with  scope set to session on root-context.xml file.
```
<bean id="socialAuthTemplate" class="org.brickred.socialauth.spring.bean.SocialAuthTemplate"
scope="session">
    <aop:scoped-proxy/>
</bean>
```
    * Add a bean entry named "socialAuthWebController" and specify the class as "org.brickred.socialauth.controller.SocialAuthWebController". This required three argument to pass in constructor definition. First is application URL with context (provide your own), second is success page URL and third one is access denied page URL (user will redirect to When user denied the permission.)
```
<bean id="socialAuthWebController"
class="org.brickred.socialauth.spring.controller.SocialAuthWebController">
    <constructor-arg value="http://opensource.brickred.com/socialauth-spring-demo" />
    <constructor-arg value="authSuccess.do" />
    <constructor-arg ref="jsp/permissionDenied.jsp" />
</bean>
```
  1. In web.xml file passed the path of spring configuration file “root-context.xml” in inti-param.
```
<init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/root-context.xml</param-value>
</init-param>
```
  1. In your login page, you can ask the controller to initiate connection by having the user click on a URL of the form: "/socialauth{pattern}?id={providerId}" where  pattern depends on your configuration, for example ".do" and the providerId may be one of facebook, foursquare, google, hotmail, linkedin, myspace, openid, twitter OR yahoo.
  1. On Success page you can get the provider object by using
```
SocialAuthManager manager = socialAuthTemplate.getSocialAuthManager();
AuthProvider provider = manager.getCurrentAuthProvider();
```
  1. You can get profile object and contact list to display by using the provider object.
```
provider.getContactList()
provider.getUserProfile()
```

[Step by Step Spring MVC application using socialauth](SpringSample.md)