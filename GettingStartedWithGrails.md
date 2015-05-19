# Introduction #

This is the step by step guide to show how to develop a Struts application using socialauth library.

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
    * Salesforce - [(show screenshot)](Salesforce.md)
    * Yammer - [(show screenshot)](Yammer.md) - https://www.yammer.com/client_applications/new
  1. You can now develop the application using keys and secrets obtained above and deploy the application on your public domain. However, most people need to [test the application on a local development machine](HowToRunApplicationWithLocalhostOnWindows.md) using the API keys and secrets obtained above.
  1. We do not recommend it at all, but if you do not want to obtain your own keys and secrets while testing, you can use the keys and secrets that we obtained by registering "opensource.brickred.com" for our demo.  [Follow the same steps](HowToRunApplicationWithLocalhostOnWindows.md) as above but with domain as "opensource.brickred.com" and [keys from our sample](SampleProperties.md).

# Development #


## Step 1. Create the structure ##

open a command prompt and run the following command to create the application
```
grails create-app socialauthdemo
```

## Step 2. Add dependencies ##

Copy the following jars in your application lib folder from [socialauth-java-sdk-2.3.zip](http://socialauth.googlecode.com/files/socialauth-java-sdk-2.3.zip)

<table cellpadding='3' border='1' cellspacing='0'>
<blockquote><tr>
<blockquote><td>socialauth-2.3.jar</td>
<td>dependencies</td>
<td>socialauth-filter-2.2.jar</td>
</blockquote></tr>
</table></blockquote>

## Step 3. Create oauth\_consumer.properties file ##
Created a [oauth\_consumer.properties](SampleProperties.md) file using the keys obtained by registering your applications. Please note that this file should be included in your class path so you can put this file in socialauthdemo\src\java folder


## Step 4. Create properties.ini file ##
Properties.ini: This file consists of properties which are used to configure socialauth-filter api. Please note that this file should be included in your class path so you can put this file in socialauthdemo\src\java folder
```
oauth_consumers = oauth_consumer.properties
filter.url = /SAF
error.page.url = /error.gsp
webapp.success.action = /socialAuthSuccess
```

## Step 5. Install Template ##
Execute the following command to get web.xml file.
```
grails install-templates
```


## Step 6. web.xml ##
You will get web.xml file in "socialauthdemo\src\templates\war" folder.
  1. Add filter definition in web.xml.
```
<filter>
  <filter-name>SocialAuthSecurityFilter</filter-name>
  <filter-class>de.deltatree.social.web.filter.impl.SocialAuthSecurityFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>SocialAuthSecurityFilter</filter-name>
  <url-pattern>/SAF/*</url-pattern>
</filter-mapping>

```
  1. Add properties file in context param, it is used to configure filter settings.
```
<context-param>
	<param-name>properties</param-name>
	<param-value>properties.ini</param-value>
</context-param>
```
## Step 7. Create SocialAuthController ##

Create socialAuth controller by using following command from your application root directory
```
grails create-controller org.brickred.socialAuth
```

After creating a controller, add the following code in your controller
```
package org.brickred

import org.brickred.socialauth.SocialAuthManager
import org.codehaus.groovy.grails.web.pages.ext.jsp.GroovyPagesPageContext

import de.deltatree.social.web.filter.api.SASFHelper
import de.deltatree.social.web.filter.api.SASFStaticHelper

class SocialAuthController {

    def index = {
		println "id:::"+params.id;
		redirect(uri: "/SAF/SocialAuth?id=${params.id}")
	}
	
	def signout = {
		SASFHelper helper = SASFStaticHelper.getHelper(request)
		SocialAuthManager socialAuthManager
		if(helper!=null){
			socialAuthManager = helper.getAuthManager()
			if(socialAuthManager != null){
				socialAuthManager.disconnectProvider(params.id)
			}else{
				flash.message = "Unable to logout from "+params.id
			}
		}else{
			flash.message = "Unable to logout from "+params.id
		}
		redirect(uri: "/index.gsp")
	}
}

```

## Step 8. Create SocialAuthSuccessController ##

Create socialAuthSuccess controller by using following command from your application root directory
```
grails create-controller org.brickred.socialAuthSuccess
```

After creating a controller, add the following code in your controller
```
package org.brickred

import org.brickred.socialauth.Contact
import org.brickred.socialauth.Profile

import de.deltatree.social.web.filter.api.SASFHelper
import de.deltatree.social.web.filter.api.SASFStaticHelper

class SocialAuthSuccessController {

    def index = {
		SASFHelper helper = SASFStaticHelper.getHelper(request);
		Profile profile = helper.getProfile();
		List<Contact> contactsList = helper.getContactList();
		println profile;
		[profile:profile,contacts:contactsList]
	}
}
```

## Step 9. Create SocialAuthUpdateStatusController ##

Create socialAuthUpdateStatus controller by using following command from your application root directory
```
grails create-controller org.brickred.socialAuthUpdateStatus
```

After creating a controller, add the following code in your controller
```
package org.brickred

import org.brickred.socialauth.AuthProvider
import org.brickred.socialauth.SocialAuthManager
import org.brickred.socialauth.exception.SocialAuthException

import de.deltatree.social.web.filter.api.SASFHelper
import de.deltatree.social.web.filter.api.SASFStaticHelper

class SocialAuthUpdateStatusController {

    def index = {
		def statusMsg = params.statusMessage
		def callbackStatus
		def callbackMesg
		if (statusMsg == null || statusMsg.trim().length() == 0) {
			callbackMesg =  "Status can't be left blank.";
		}else{

			SASFHelper helper = SASFStaticHelper.getHelper(request);
			SocialAuthManager manager = helper.getAuthManager();
	
			AuthProvider provider = null;
			if (manager != null) {
				provider = manager.getCurrentAuthProvider();
			}
			if (provider != null) {
				try {
					provider.updateStatus(statusMsg);
					callbackMesg =  "Status Updated successfully";
				} catch (SocialAuthException e) {
					callbackMesg =  e.getMessage();
					e.printStackTrace();
				}
			}else{
				callbackMesg =  "Unable to upload status"
			}
		}
		[callbackMesg:callbackMesg]
	}
}
```

## Step 10. Create index.gsp ##
Create index.gsp as your application home page

```
<html>
<%@page import="de.deltatree.social.web.filter.api.SASFStaticHelper"%>
<%@page import="de.deltatree.social.web.filter.api.SASFHelper"%>
<%@page import="org.brickred.socialauth.SocialAuthManager"%>
<%@page import="org.codehaus.groovy.grails.web.pages.ext.jsp.GroovyPagesPageContext" %>
<head>
    <title>BrickRed SocialAuth Demo </title>
    <style type="text/css">
        .style1{text-align: justify;}
    </style>
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
    <%
		SASFHelper helper = SASFStaticHelper.getHelper(request);
		SocialAuthManager socialAuthManager;
		if(helper!=null){
			socialAuthManager = helper.getAuthManager();
			if(socialAuthManager != null){
				GroovyPagesPageContext pageContext = new GroovyPagesPageContext(pageScope);
				pageContext.setAttribute("socialAuthManager",socialAuthManager);
			}
		}
	%>
    <g:set var="facebook" value="false" />
	<g:set var="twitter" value="false" />
	<g:set var="google" value="false" />
	<g:set var="yahoo" value="false" />
	<g:set var="hotmail" value="false" />
	<g:set var="linkedin" value="false" />
	<g:set var="foursquare" value="false" />
	<g:set var="myspace" value="false" />
	<g:if test="${socialAuthManager != null }">
		<g:each var="item" in="${socialAuthManager.connectedProvidersIds}">
		 	<g:if test="${'facebook'.equals(item)}">
		    	<g:set var="facebook" value="true" />
		  	</g:if>
		  	<g:if test="${'google'.equals(item)}">
		    	<g:set var="google" value="true" />
		  	</g:if>
		  	<g:if test="${'twitter'.equals(item)}">
		    	<g:set var="twitter" value="true" />
		  	</g:if>
		  	<g:if test="${'yahoo'.equals(item)}">
		    	<g:set var="yahoo" value="true" />
		  	</g:if>
		  	<g:if test="${'hotmail'.equals(item)}">
		    	<g:set var="hotmail" value="true" />
		  	</g:if>
		  	<g:if test="${'linkedin'.equals(item)}">
		    	<g:set var="linkedin" value="true" />
		  	</g:if>
		  	<g:if test="${'myspace'.equals(item)}">
		    	<g:set var="myspace" value="true" />
		  	</g:if>
		  	<g:if test="${'foursquare'.equals(item)}">
		    	<g:set var="foursquare" value="true" />
		  	</g:if>
		</g:each>
	</g:if>
<div id="main">
    <div id="text" >
	<table cellpadding="10" cellspacing="10" align="center">
		<tr><td colspan="8"><h3 align="center">Welcome to Social Auth Demo</h3></td></tr>
		<tr><td colspan="8"><p align="center">Please click on any icon.</p></td></tr>
		<g:if test="${flash.message}">
			<tr><td colspan="8">
				<div>${flash.message}</div>
			</td></tr>
		</g:if>
		<tr>
			<td>
				<a href="socialAuth?id=facebook">
				 <img src="images/facebook_icon.png" alt="Facebook" title="Facebook" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${facebook.equals('true')}">
					<a href="socialAuth/signout?id=facebook&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${facebook.equals('false')}">
					<a href="socialAuth?id=facebook">Signin</a><br/>
				</g:if>
			</td>
			<td>
				<a href="socialAuth?id=twitter">
				  <img src="images/twitter_icon.png" alt="Twitter" title="Twitter" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${twitter.equals('true')}">
					<a href="socialAuth/signout?id=twitter&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${twitter.equals('false')}">
					<a href="socialAuth?id=twitter">Signin</a><br/>
				</g:if>
			</td>
			<td>
				<a href="socialAuth?id=google">
				  <img src="images/gmail-icon.jpg" alt="Gmail" title="Gmail" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${google.equals('true')}">
					<a href="socialAuth/signout?id=google&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${google.equals('false')}">
					<a href="socialAuth?id=google">Signin</a><br/>
				</g:if>
			</td>
			<td>
				<a href="socialAuth?id=yahoo">
				  <img src="images/yahoomail_icon.jpg" alt="YahooMail" title="YahooMail" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${yahoo.equals('true')}">
					<a href="socialAuth/signout?id=yahoo&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${yahoo.equals('false')}">
					<a href="socialAuth?id=yahoo">Signin</a><br/>
				</g:if>
			</td>
			<td>
				<a href="socialAuth?id=hotmail">
				  <img src="images/hotmail.jpeg" alt="HotMail" title="HotMail" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${hotmail.equals('true')}">
					<a href="socialAuth/signout?id=hotmail&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${hotmail.equals('false')}">
					<a href="socialAuth?id=hotmail">Signin</a><br/>
				</g:if>
			</td>
			<td>
				<a href="socialAuth?id=linkedin">
				  <img src="images/linkedin.gif" alt="Linked In" title="Linked In" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${linkedin.equals('true')}">
					<a href="socialAuth/signout?id=linkedin&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${linkedin.equals('false')}">
					<a href="socialAuth?id=linkedin">Signin</a><br/>
				</g:if>
			</td>
			<td>
				<a href="socialAuth?id=foursquare">
				  <img src="images/foursquare.jpeg" alt="FourSquare" title="FourSquare" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${foursquare.equals('true')}">
					<a href="socialAuth/signout?id=foursquare&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${foursquare.equals('false')}">
					<a href="socialAuth?id=foursquare">Signin</a><br/>
				</g:if>
			</td>
			<td>
				<a href="socialAuth?id=myspace">
				  <img src="images/myspace.jpeg" alt="MySpace" title="MySpace" border="0"/>
				</a>
				<br/><br/>
				<g:if test="${myspace.equals('true')}">
					<a href="socialAuth/signout?id=myspace&mode=signout">Signout</a><br/>
				</g:if>
				<g:if test="${myspace.equals('false')}">
					<a href="socialAuth?id=myspace">Signin</a><br/>
				</g:if>
			</td>
		</tr>
		<tr>
			<td colspan="8" align="center">
				<form action="socialAuth" onsubmit="return validate(this);">
					or enter OpenID url: <input type="text" value="" name="id"/>
					<input type="submit" value="Submit"/> 
				</form>
			</td>
		</tr>
				
	</table>
	</div>
</div>
    
</body>
</html>
```

## Step 11. Create gsp to show results ##
Create index.gsp in "socialauthdemo\grails-app\views\socialAuthSuccess" folder to show the profile and contacts.

```
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>BrickRed SocialAuth Demo </title>
    <style type="text/css">
        .style1{text-align: justify;}
        .sectiontableheader {background-color:#C8D7E3;
              color:#293D6B;font-size:8pt;
              font-weight:bold;padding:2px;}
    .even   {background:none repeat scroll 0 0 #F7F7F7;padding:2px;}
    .odd {background:none repeat scroll 0 0 #FFFFF0;padding:2px;}
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
      req.open("POST", "socialAuthUpdateStatus");
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
    
<div id="main">
             
<div id="text" >
  <h2 align="center">Authentication has been successful.</h2>
  <br/>
  <div align="center"><a href="index.gsp">Back</a></div>
  <br>
  <h3 align="center">Profile Information</h3>
  <table cellspacing="1" cellspacing="4" border="0" bgcolor="e5e5e5" width="60%" align="center">
    <tr class="sectiontableheader">
      <th>Profile Field</th>
      <th>Value</th>
    </tr>
    <tr class="odd">
      <td>Email:</td>
      <td>${profile.email}</td>
    </tr>
    <tr class="even  ">
      <td>First Name:</td>
      <td>${profile.firstName}</td>
    </tr>
    <tr class="odd">
      <td>Last Name:</td>
      <td>${profile.lastName}</td>
    </tr>
    <tr class="even  ">
      <td>Country:</td>
      <td>${profile.country}</td>
    </tr>
    <tr class="odd">
      <td>Language:</td>
      <td>${profile.language}</td>
    </tr>
    <tr class="even  ">
      <td>Full Name:</td>
      <td>${profile.fullName}</td>
    </tr>
    <tr class="odd">
      <td>Display Name:</td>
      <td>${profile.displayName}</td>
    </tr>
    <tr class="even  ">
      <td>DOB:</td>
      <td>${profile.dob}</td>
    </tr>
    <tr class="odd">
      <td>Gender:</td>
      <td>${profile.gender}</td>
    </tr>
    <tr class="even  ">
      <td>Location:</td>
      <td>${profile.location}</td>
    </tr>
    <tr class="odd">
      <td>Profile Image:</td>
      <td>
        <g:if test="${profile.profileImageURL != null}">
          <img src='${profile.profileImageURL}'/>
        </g:if>
      </td>
    </tr>
    <tr class="even  ">
      <td>Update status:</td>
      <td>
        <input type="button" value="Click to Update Status" onclick="updateStatus();" id="btnUpdateStatus"/>    
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
    <g:each var="contact" in="${contacts}" status="index">
      <tr class='<g:if test="${index % 2 == 0}">even  </g:if><g:if test="${index % 2 != 0}">odd</g:if>'>
        <td>${contact.firstName} ${contact.lastName}</td>
        <td>${contact.email}</td>
        <td><a href='${contact.profileUrl}' target="_new">${contact.profileUrl}</a></td>
      </tr>
    </g:each>
  </table>
   
</div>
</div>
</body>
</html>
```

## Step 12. Show Update status response ##
Create index.gsp in the "socialauthdemo\grails-app\views\socialAuthUpdateStatus" folder to show the update status response
```
${callbackMesg}
```

## Step 13. Project Directory Structure ##
Project directory structure will be as given below:-<br><br>
<img src='http://socialauth.googlecode.com/svn/wiki/images/socialauth_grails.jpg' />

<h1>Conclusion</h1>

You can get the entire source code of the sample by browsing the source code or from following location<br>
<a href='http://socialauth.googlecode.com/svn/trunk/socialauth-grails-demo'>http://socialauth.googlecode.com/svn/trunk/socialauth-grails-demo</a>

Hope this guide would have been of help. If you have any questions, please file an issue.