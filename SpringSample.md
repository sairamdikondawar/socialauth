**Spring Example**
Demo application exploring socialauth library.



# Introduction #

This is the step by step guide to show how to develop a Spring application using socialauth library. This requires the jars for socialauth-core and socialauth-spring.

# Prerequisites #
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

# Step 1. Create the structure #

Create a dynamic web project and add spring dependencies as well as add socialauth-4.0.jar and socialauth-spring-2.0.jar, dependencies from SDK into the WEB-INF/lib directory. Now your project should look as follows

http://socialauth.googlecode.com/svn/wiki/images/spring_structure.JPG

# Step 2. Web.xml file #

Create a web.xml file and configure the dispatcher servlet. In our case, the 'dispatcher' servlet is nothing but an instance of type 'org.springframework.web.servlet.DispatcherServlet' and we passed the path of spring configuration file “root-context.xml” in inti-param. In this example, we map all URLs that ends with **.do to dispatcher servlet.**

```

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns="http://java.sun.com/xml/ns/javaee" 
	xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" 
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" 
	version="2.5">
  <description>Spring MVC Test Application</description>
  <display-name>socialauth-spring-demo</display-name>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  <servlet>
    <description>Spring MVC Dispatcher Servlet</description>
    <servlet-name>springsocialauthdemo</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/root-context.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>springsocialauthdemo</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>
</web-app>

```

# Step 3. properties.xml file #

This file contains key/value pair to store the application key and secret for different providers.

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
        <prop key="api.login.yahoo.com.consumer_key">dj0yJmk9VTdaSUVTU3RrWlRzJmQ9WVdrOWNtSjZNMFpITm1VbWNHbzlNQS
                0tJnM9Y29uc3VtZXJzZWNyZXQmeD1iMA--</prop>
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


# Step 4. root-context.xml file #

This is a spring configuration file for our application. This file contains the following bean definition:-
  1. Import the properties.xml file.
  1. Add a bean entry named "socialAuthConfig" and specify the class as "org.brickred.socialauth.SocialAuthConfig" and set property applicationProperties. This will be set in SocialAuthManager.
  1. Add a bean entry named "socialAuthManager" and specify the class as "org.brickred.socialauth.SocialAuthManager" and set property socialAuthConfig. This bean is automatically injected into the “socialAuthWebController”. We have the scope set to session for this manager.
  1. Add a bean entry named “socialAuthTemplate” and specify the class as “org.brickred.socialauth.spring.bean.SocialAuthTemplate”, which is part of socialauth-spring.jar. This bean is automatically injected into the “socialAuthWebController”. On the “socialAuthTemplate” bean, we have the scope set to session.
  1. Add a bean entry named “socialAuthWebController” and specify the class as “org.brickred.socialauth.spring.controller.SocialAuthWebController”, which is also a part of socialauth-spring.jar. This required three arguments to pass in constructor definition. First is application url with context, second is success page url and third one is access denied page URL (user will redirect to When user denied the permission.)

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:aop="http://www.springframework.org/schema/aop" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-3.0.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">

    <context:component-scan base-package="org.brickred.controller" />
    <!-- To enable @RequestMapping process on type level and method level -->
    <bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping" />
    <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter"/>

    <import resource="properties.xml" />
    <bean id="socialAuthConfig" class="org.brickred.socialauth.SocialAuthConfig">
        <property name="applicationProperties"><ref bean="socialAuthProperties"/></property>
    </bean>
    
    <bean id="socialAuthManager" class="org.brickred.socialauth.SocialAuthManager" scope="session">
        <property name="socialAuthConfig"><ref bean="socialAuthConfig"/></property>
        <aop:scoped-proxy/>
    </bean>
    
    <bean id="socialAuthTemplate" class="org.brickred.socialauth.spring.bean.SocialAuthTemplate" scope="session">
        <aop:scoped-proxy/>
    </bean>
    
    <bean id="socialAuthWebController" class="org.brickred.socialauth.spring.controller.SocialAuthWebController">
        <constructor-arg value="http://opensource.brickred.com/socialauth-spring-demo" />
        <constructor-arg value="authSuccess.do" />
        <constructor-arg value="jsp/accessDenied.jsp" />
    </bean>
    
</beans>
```


# Step 5. Coding the index page #

Now let us code the index page with the following code. In this page, we just create various icons that the user can click upon and attach them with the action that will process the authentication. Here attached action in “socialauth.do” which maps to the “org.brickred.socialauth.spring.controller.SocialAuthWebController”.

```
<html>
  <head>
    <title>SocialAuth Demo</title>
    <script>
      function validate(obj){
        var val = obj.id.value;
        if(trimString(val).length <= 0){
          alert("Please enter OpenID URL");
          return false;
        }else{
          return true;
        }
      }
      function trimString(tempStr)
      {
         return tempStr.replace(/^\s*|\s*$/g,"");
      }
    </script>
  </head>
  <body>
    <table cellpadding="10" cellspacing="10" align="center">
      <tr><td colspan="8"><h3 align="center">Welcome to Social Auth Demo</h3></td></tr>
      <tr><td colspan="8"><p align="center">Please click on any icon.</p></td></tr>
      <tr>
        <td>
          <a href="socialauth.do?id=facebook">
            <img src="images/facebook_icon.png" alt="Facebook" title="Facebook" border="0"/>
          </a>
        </td>
        <td>
          <a href="socialauth.do?id=twitter">
            <img src="images/twitter_icon.png" alt="Twitter" title="Twitter" border="0"/>
          </a>
        </td>
        <td>
          <a href="socialauth.do?id=google">
            <img src="images/gmail-icon.jpg" alt="Gmail" title="Gmail" border="0"/>
          </a>
        </td>
        <td>
          <a href="socialauth.do?id=yahoo">
            <img src="images/yahoomail_icon.jpg" alt="YahooMail" title="YahooMail" border="0"/>
          </a>
        </td>
        <td>
          <a href="socialauth.do?id=hotmail">
            <img src="images/hotmail.jpeg" alt="HotMail" title="HotMail" border="0"/>
          </a>
        </td>
        <td>
          <a href="socialauth.do?id=linkedin">
            <img src="images/linkedin.gif" alt="Linked In" title="Linked In" border="0"/>
          </a>
        </td>
        <td>
          <a href="socialauth.do?id=foursquare">
            <img src="images/foursquare.jpeg" alt="FourSquare" title="FourSquare" border="0"/>
          </a>
        </td>
        <td>
          <a href="socialauth.do?id=myspace">
            <img src="images/myspace.jpeg" alt="MySpace" title="MySpace" border="0"/>
          </a>
        </td>
      </tr>
      <tr>
        <td colspan="8" align="center">
          <form action="socialauth.do" onsubmit="return validate(this);">
            or enter OpenID url: <input type="text" value="" name="id"/>
            <input type="submit" value="Submit"/> 
          </form>
        </td>
      </tr>
    </table>
  </body>
</html>
```

# Step 6. Creating the success controller #

Now we create a success action whose path is given in “SocialAuthWebController” constructor in root-context.xml. It gets the instance of the requested provider from “socialAuthTemplate” to get required information from providers like user profile, contacts etc.

```

package org.brickred.controller;

import java.util.ArrayList;
import java.util.List;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.brickred.socialauth.AuthProvider;
import org.brickred.socialauth.Contact;
import org.brickred.socialauth.SocialAuthManager;
import org.brickred.socialauth.spring.bean.SocialAuthTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class SuccessController {

	@Autowired
	private SocialAuthTemplate socialAuthTemplate;

	@RequestMapping(value = "/authSuccess")
	public ModelAndView getRedirectURL(final HttpServletRequest request)
			throws Exception {
		ModelAndView mv = new ModelAndView();
		List<Contact> contactsList = new ArrayList<Contact>();
		SocialAuthManager manager = socialAuthTemplate.getSocialAuthManager();
		AuthProvider provider = manager.getCurrentAuthProvider();
		contactsList = provider.getContactList();
		if (contactsList != null && contactsList.size() > 0) {
			for (Contact p : contactsList) {
				if (!StringUtils.hasLength(p.getFirstName())
						&& !StringUtils.hasLength(p.getLastName())) {
					p.setFirstName(p.getDisplayName());
				}
			}
		}
		mv.addObject("profile", provider.getUserProfile());
		mv.addObject("contacts", contactsList);
		mv.setViewName("/jsp/authSuccess.jsp");

		return mv;
	}

}

```

# Step 7. Displaying the result with authSuccess.jsp #

It is the view returns by success controller. It shows the user profile and contacts. It also has update status button.

```
<html>
<head>
	<title>SocialAuth Demo</title>
	<style>
	.sectiontableheader {background-color:#C8D7E3;color:#293D6B;font-size:8pt;font-weight:bold;padding:2px;}
	.sectiontableentry2 {background:none repeat scroll 0 0 #F7F7F7;padding:2px;}
	.sectiontableentry1 {background:none repeat scroll 0 0 #FFFFF0;padding:2px;}
	</style>
	<script>
    	function updateStatus(){
        	var btn = document.getElementById('btnUpdateStatus');
        	btn.disabled=true;
			var msg = prompt("Enter your status here:");
			if(msg == null || msg.length == 0){
				btn.disabled=false;
		    	return false;
	        }
			msg = "statusMessage="+msg;
			var req = new XMLHttpRequest();
			req.open("POST", "<%=request.getContextPath()%>/updateStatus.do");
			req.setRequestHeader("Accept", "text/xml");
			req.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
			req.setRequestHeader("Content-length", msg.length);
			req.setRequestHeader("Connection", "close");
			req.onreadystatechange = function () {
				if (req.readyState == 4) {
					if(req.responseText.length > 0) {
							alert(req.responseText);
							btn.disabled=false;
					}
				}
			};
			req.send(msg);
    	}
	</script>
</head>
<body>
<%@page import="org.brickred.socialauth.Profile,java.util.*;" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page isELIgnored="false"%>
<h2 align="center">Authentication has been successful.</h2>
<br/>
<div align="center"><a href="index.jsp">Back</a></div>
<br />
<h3 align="center">Profile Information</h3>
<table cellspacing="1" cellspacing="4" border="0" bgcolor="e5e5e5" width="60%" align="center">
	<tr class="sectiontableheader">
		<th>Profile Field</th>
		<th>Value</th>
	</tr>
	<tr class="sectiontableentry1">
		<td>Email:</td>
		<td><c:out value="${profile.email}"/></td>
	</tr>
	<tr class="sectiontableentry2">
		<td>First Name:</td>
		<td><c:out value="${profile.firstName}"/></td>
	</tr>
	<tr class="sectiontableentry1">
		<td>Last Name:</td>
		<td><c:out value="${profile.lastName}"/></td>
	</tr>
	<tr class="sectiontableentry2">
		<td>Country:</td>
		<td><c:out value="${profile.country}"/></td>
	</tr>
	<tr class="sectiontableentry1">
		<td>Language:</td>
		<td><c:out value="${profile.language}"/></td>
	</tr>
	<tr class="sectiontableentry2">
		<td>Full Name:</td>
		<td><c:out value="${profile.fullName}"/></td>
	</tr>
	<tr class="sectiontableentry1">
		<td>Display Name:</td>
		<td><c:out value="${profile.displayName}"/></td>
	</tr>
	<tr class="sectiontableentry2">
		<td>DOB:</td>
		<td><c:out value="${profile.dob}"/></td>
	</tr>
	<tr class="sectiontableentry1">
		<td>Gender:</td>
		<td><c:out value="${profile.gender}"/></td>
	</tr>
	<tr class="sectiontableentry2">
		<td>Location:</td>
		<td><c:out value="${profile.location}"/></td>
	</tr>
	<tr class="sectiontableentry1">
		<td>Profile Image:</td>
		<td>
			<c:if test="${profile.profileImageURL != null}">
				<img src='<c:out value="${profile.profileImageURL}"/>'/>
			</c:if>
		</td>
	</tr>
	<tr class="sectiontableentry2">
		<td>Update status:</td>
		<td>
			<input type="button" value="Click to Update Status" 
                onclick="updateStatus();" id="btnUpdateStatus"/>		
		</td>
	</tr>
</table>
<h3 align="center">Contact Details</h3>
<table cellspacing="1" cellspacing="4" border="0" bgcolor="e5e5e5" align="center" width="60%">
	<tr class="sectiontableheader">
		<th width="15%">Name</th>
		<th>Email</th>
		<th>Profile URL</th>
	</tr>
	<c:forEach var="contact" items="${contacts}" varStatus="index">
		<tr class='<c:if test="${index.count % 2 == 0}">sectiontableentry2</c:if>
                    <c:if test="${index.count % 2 != 0}">sectiontableentry1</c:if>'>
			<td>
                <c:out value="${contact.firstName}"/> <c:out value="${contact.lastName}"/>
            </td>
			<td><c:out value="${contact.email}"/></td>
			<td><a href='<c:out value="${contact.profileUrl}"/>' target="_new">
                    <c:out value="${contact.profileUrl}"/>
                </a>
            </td>
		</tr>
	</c:forEach>
</table>
</body>
</html>
```

# Step 8. Create the update status controller #

It gets the instance of the requested provider from “socialAuthTemplate” and calls the updateStatus() method and returns the view statusSuccess.jsp with the updated status success/failure message.

```
package org.brickred.controller;

import javax.servlet.http.HttpServletRequest;

import org.brickred.socialauth.AuthProvider;
import org.brickred.socialauth.SocialAuthManager;
import org.brickred.socialauth.exception.SocialAuthException;
import org.brickred.socialauth.spring.bean.SocialAuthTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class UpdateStatusController {
	@Autowired
	private SocialAuthTemplate socialAuthTemplate;

	@RequestMapping(value = "/updateStatus", method = RequestMethod.POST)
	public ModelAndView getRedirectURL(final HttpServletRequest request)
			throws Exception {
		ModelAndView mv = new ModelAndView();
		SocialAuthManager manager = socialAuthTemplate.getSocialAuthManager();
		AuthProvider provider = manager.getCurrentAuthProvider();
		String statusMsg = request.getParameter("statusMessage");
		try {
			provider.updateStatus(statusMsg);
			mv.addObject("Message", "Status Updated successfully");
		} catch (SocialAuthException e) {
			mv.addObject("Message", e.getMessage());
			e.printStackTrace();
		}
		mv.setViewName("/jsp/statusSuccess.jsp");

		return mv;
	}
}
```


# Step 9. statusSuccess.jsp #

It shows the updated status success/failure message.

```

<%
if(request.getAttribute("Message")!=null){
	out.print(request.getAttribute("Message"));
}%>

```

You can get the entire source code of the sample by browsing the source code. Hope this guide would have been of help. If you have any questions, please [report any issues](http://code.google.com/p/socialauth/issues/entry).