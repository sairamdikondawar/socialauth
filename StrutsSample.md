# Introduction #

This is the step by step guide to show how to develop a Struts application using socialauth library. If you just want to see the application working, please see our demo

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

With the prerequisites out of the way, we are ready to begin development. Since Eclipse is our choice of the development environment, we have shown examples using Eclipse, but you can create your own structure manually as well.

## Step 1. Create the structure ##

Create a dynamic web project and add struts dependencies. You can download struts dependencies from [here](http://archive.apache.org/dist/struts/struts-1.2.7/). <br>
Download <a href='http://socialauth.googlecode.com/files/socialauth-java-sdk-4.0.zip'>socialauth-java-sdk-4.0.zip</a> and copy socialauth-3.0.jar from dist folder and all jars from  dependencies folder into the WEB-INF/lib directory. Now your project should look as follows<br>
<br>
<img src='http://socialauth.googlecode.com/svn/wiki/images/struts-sample-structure.png' />

The web.xml file doesn't have any special entries other than struts, but reproducing it here for completeness.<br>
<br>
<pre><code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;<br>
&lt;web-app id="WebApp_ID" version="2.4" <br>
	xmlns="http://java.sun.com/xml/ns/j2ee" <br>
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" <br>
	xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"&gt;<br>
	&lt;display-name&gt;socialauthdemo&lt;/display-name&gt;<br>
	&lt;welcome-file-list&gt;<br>
		&lt;welcome-file&gt;index.jsp&lt;/welcome-file&gt;<br>
	&lt;/welcome-file-list&gt;<br>
	&lt;servlet&gt;<br>
		&lt;servlet-name&gt;action&lt;/servlet-name&gt;<br>
		&lt;display-name&gt;socialauthdemo&lt;/display-name&gt;<br>
		&lt;description&gt;Application for socialauth demo&lt;/description&gt;<br>
		&lt;servlet-class&gt;org.apache.struts.action.ActionServlet&lt;/servlet-class&gt;<br>
		&lt;init-param&gt;<br>
			&lt;param-name&gt;config&lt;/param-name&gt;<br>
			&lt;param-value&gt;/WEB-INF/struts-config.xml&lt;/param-value&gt;<br>
		&lt;/init-param&gt;<br>
		&lt;init-param&gt;<br>
			&lt;param-name&gt;debug&lt;/param-name&gt;<br>
			&lt;param-value&gt;2&lt;/param-value&gt;<br>
		&lt;/init-param&gt;<br>
		&lt;init-param&gt;<br>
			&lt;param-name&gt;detail&lt;/param-name&gt;<br>
			&lt;param-value&gt;2&lt;/param-value&gt;<br>
		&lt;/init-param&gt;<br>
		&lt;load-on-startup&gt;2&lt;/load-on-startup&gt;<br>
	&lt;/servlet&gt;<br>
	<br>
	&lt;servlet-mapping&gt;<br>
		&lt;servlet-name&gt;action&lt;/servlet-name&gt;<br>
		&lt;url-pattern&gt;*.do&lt;/url-pattern&gt;<br>
	&lt;/servlet-mapping&gt;<br>
		  <br>
	&lt;taglib&gt;<br>
		&lt;taglib-uri&gt;struts-bean&lt;/taglib-uri&gt;<br>
	    &lt;taglib-location&gt;/WEB-INF/struts-bean.tld&lt;/taglib-location&gt;<br>
	&lt;/taglib&gt;<br>
 <br>
  	&lt;taglib&gt;<br>
    	&lt;taglib-uri&gt;struts-html&lt;/taglib-uri&gt;<br>
    	&lt;taglib-location&gt;/WEB-INF/struts-html.tld&lt;/taglib-location&gt;<br>
  	&lt;/taglib&gt;<br>
  <br>
  	&lt;taglib&gt;<br>
    	&lt;taglib-uri&gt;struts-logic&lt;/taglib-uri&gt;<br>
    	&lt;taglib-location&gt;/WEB-INF/struts-logic.tld&lt;/taglib-location&gt;<br>
  	&lt;/taglib&gt;<br>
  	<br>
  	&lt;error-page&gt;<br>
  		&lt;exception-type&gt;java.lang.Exception&lt;/exception-type&gt;<br>
  		&lt;location&gt;/jsp/error.jsp&lt;/location&gt;<br>
  	&lt;/error-page&gt;<br>
  	<br>
     <br>
&lt;/web-app&gt;<br>
<br>
</code></pre>


<h2>Step 2. Coding the index page</h2>

Now let us code the index page with the following code.  In this page, we just create various icons that the user can click upon and attach them with the action that will process the authentication.<br>
<br>
<pre><code>&lt;html&gt;<br>
    &lt;head&gt;<br>
        &lt;title&gt;SocialAuth Demo&lt;/title&gt;<br>
        &lt;script&gt;<br>
            function validate(obj){<br>
                var val = obj.id.value;<br>
                if(trimString(val).length &lt;= 0){<br>
                    alert("Please enter OpenID URL");<br>
                    return false;<br>
                }else{<br>
                    return true;<br>
                }<br>
            }<br>
            function trimString(tempStr)<br>
            {<br>
               return tempStr.replace(/^\s*|\s*$/g,"");<br>
            }<br>
        &lt;/script&gt;<br>
    &lt;/head&gt;<br>
    &lt;body&gt;<br>
        &lt;table cellpadding="10" cellspacing="10" align="center"&gt;<br>
            &lt;tr&gt;&lt;td colspan="8"&gt;&lt;h3 align="center"&gt;Welcome to Social Auth Demo&lt;/h3&gt;&lt;/td&gt;&lt;/tr&gt;<br>
            &lt;tr&gt;&lt;td colspan="8"&gt;&lt;p align="center"&gt;Please click on any icon.&lt;/p&gt;&lt;/td&gt;&lt;/tr&gt;<br>
            &lt;tr&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=facebook"&gt;&lt;img src="images/facebook_icon.png" alt="Facebook" <br>
                title="Facebook" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=twitter"&gt;&lt;img src="images/twitter_icon.png" alt="Twitter" <br>
                title="Twitter" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=google"&gt;&lt;img src="images/gmail-icon.jpg" alt="Gmail" <br>
                title="Gmail" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=yahoo"&gt;&lt;img src="images/yahoomail_icon.jpg" alt="YahooMail" <br>
                title="YahooMail" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=hotmail"&gt;&lt;img src="images/hotmail.jpeg" alt="HotMail" <br>
                title="HotMail" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=linkedin"&gt;&lt;img src="images/linkedin.gif" alt="Linked In" <br>
                title="Linked In" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=foursquare"&gt;&lt;img src="images/foursquare.jpeg" alt="FourSquare" <br>
                title="FourSquare" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
                &lt;td&gt;&lt;a href="socialAuth.do?id=myspace"&gt;&lt;img src="images/myspace.jpeg" alt="MySpace" <br>
                title="MySpace" border="0"&gt;&lt;/img&gt;&lt;/a&gt;&lt;/td&gt;<br>
            &lt;/tr&gt;<br>
            &lt;tr&gt;<br>
                &lt;td colspan="8" align="center"&gt;<br>
                    &lt;form action="socialAuth.do" onsubmit="return validate(this);"&gt;<br>
                        or enter OpenID url: &lt;input type="text" value="" name="id"/&gt;<br>
                        &lt;input type="submit" value="Submit"/&gt; <br>
                    &lt;/form&gt;<br>
                &lt;/td&gt;<br>
            &lt;/tr&gt;<br>
            <br>
        &lt;/table&gt;<br>
    &lt;/body&gt;<br>
&lt;/html&gt;<br>
<br>
</code></pre>

<h2>Step 3. Creating the form bean</h2>
Create a simple form bean that will be used to store the socialauth manager into session.<br>
<br>
<pre><code>package com.auth.form;<br>
<br>
import org.apache.struts.action.ActionForm;<br>
import org.brickred.socialauth.SocialAuthManager;<br>
public class AuthForm extends ActionForm {	<br>
	String id;	<br>
	SocialAuthManager socialAuthManager;<br>
	public String getId() {<br>
		return id;<br>
	}	<br>
	public void setId(final String id) {<br>
		this.id = id;<br>
	}<br>
	public SocialAuthManager getSocialAuthManager() {<br>
		return socialAuthManager;<br>
	}<br>
<br>
	public void setSocialAuthManager(final SocialAuthManager socialAuthManager) {<br>
		this.socialAuthManager = socialAuthManager;<br>
	}<br>
}<br>
<br>
</code></pre>

<h2>Step 4. Writing the authentication action</h2>
Now we develop the action that is called by the link we created in the step above. This action creates a instance of SocialAuthConfig which loads the configuration for providers. Then it creates a instance of the SocialAuthManager and calls the getAuthenticationUrl() method to find the URL to redirect to. It also saves the instance of socialauth manager in form bean which has session scope. Finally the action redirects to the URL obtained from the getAuthenticationUrl() and the user sees the login page of facebook, gmail or yahoo.<br>
<br>
Please notice that any provider will also need a URL from the application to which it will forward after successful authentication. In the sample below this URL is socialAuthSuccessAction.do which corresponds to another action that we will develop in the next step.<br>
<br>
<pre><code>package com.auth.actions;<br>
<br>
import java.io.InputStream;<br>
import javax.servlet.http.HttpServletRequest;<br>
import javax.servlet.http.HttpServletResponse;<br>
import org.apache.commons.logging.Log;<br>
import org.apache.commons.logging.LogFactory;<br>
import org.apache.struts.action.Action;<br>
import org.apache.struts.action.ActionForm;<br>
import org.apache.struts.action.ActionForward;<br>
import org.apache.struts.action.ActionMapping;<br>
import org.apache.struts.util.RequestUtils;<br>
import org.brickred.socialauth.SocialAuthConfig;<br>
import org.brickred.socialauth.SocialAuthManager;<br>
<br>
import com.auth.form.AuthForm;<br>
<br>
<br>
public class SocialAuthenticationAction extends Action {<br>
<br>
	final Log LOG = LogFactory.getLog(SocialAuthenticationAction.class);<br>
<br>
	@Override<br>
	public ActionForward execute(final ActionMapping mapping,<br>
			final ActionForm form, final HttpServletRequest request,<br>
			final HttpServletResponse response) throws Exception {<br>
<br>
		AuthForm authForm = (AuthForm) form;<br>
<br>
		String id = authForm.getId();<br>
		SocialAuthManager manager;<br>
		if (authForm.getSocialAuthManager() != null) {<br>
			manager = authForm.getSocialAuthManager();<br>
		} else {<br>
			InputStream in = SocialAuthenticationAction.class.getClassLoader()<br>
					.getResourceAsStream("oauth_consumer.properties");<br>
			SocialAuthConfig conf = SocialAuthConfig.getDefault();<br>
			conf.load(in);<br>
			manager = new SocialAuthManager();<br>
			manager.setSocialAuthConfig(conf);<br>
			authForm.setSocialAuthManager(manager);<br>
		}<br>
<br>
		String returnToUrl = RequestUtils.absoluteURL(request,<br>
				"/socialAuthSuccessAction.do")<br>
				.toString();<br>
		String url = manager.getAuthenticationUrl(id, returnToUrl);<br>
<br>
		LOG.info("Redirecting to: " + url);<br>
		if (url != null) {<br>
			ActionForward fwd = new ActionForward("openAuthUrl", url, true);<br>
			return fwd;<br>
		}<br>
		return mapping.findForward("failure");<br>
	}<br>
}<br>
<br>
</code></pre>

<h2>Step 5. Creating the success action</h2>

Now we create a success action whose path was given in above action. This action verifies the user when the external provider forwards the user back to our application. It gets the instance of the SocialAuthManager from session and calls connect() method and returns AuthProvider object. We can call various method of returned provider object to get the profile or contacts.<br>
<br>
<pre><code>package com.auth.actions;<br>
<br>
import java.io.File;<br>
import java.io.FileOutputStream;<br>
import java.io.ObjectOutputStream;<br>
import java.util.ArrayList;<br>
import java.util.HashMap;<br>
import java.util.Iterator;<br>
import java.util.List;<br>
import java.util.Map;<br>
import java.util.Map.Entry;<br>
import javax.servlet.http.HttpServletRequest;<br>
import javax.servlet.http.HttpServletResponse;<br>
import javax.servlet.http.HttpSession;<br>
import org.apache.commons.lang.StringUtils;<br>
import org.apache.commons.logging.Log;<br>
import org.apache.commons.logging.LogFactory;<br>
import org.apache.struts.action.Action;<br>
import org.apache.struts.action.ActionForm;<br>
import org.apache.struts.action.ActionForward;<br>
import org.apache.struts.action.ActionMapping;<br>
import org.brickred.socialauth.AuthProvider;<br>
import org.brickred.socialauth.Contact;<br>
import org.brickred.socialauth.Profile;<br>
import org.brickred.socialauth.SocialAuthManager;<br>
import com.auth.form.AuthForm;<br>
<br>
public class SocialAuthSuccessAction extends Action {<br>
<br>
	final Log LOG = LogFactory.getLog(SocialAuthSuccessAction.class);<br>
<br>
	@Override<br>
	public ActionForward execute(final ActionMapping mapping,<br>
			final ActionForm form, final HttpServletRequest request,<br>
			final HttpServletResponse response) throws Exception {<br>
<br>
		AuthForm authForm = (AuthForm) form;<br>
		SocialAuthManager manager = null;<br>
		if (authForm.getSocialAuthManager() != null) {<br>
			manager = authForm.getSocialAuthManager();<br>
		}<br>
		if (manager != null) {<br>
			List&lt;Contact&gt; contactsList = new ArrayList&lt;Contact&gt;();<br>
			Profile profile = null;<br>
			try {<br>
				Map&lt;String, String&gt; paramsMap = new HashMap&lt;String, String&gt;();<br>
				for (Map.Entry&lt;String, String[]&gt; entry : request.getParameterMap().entrySet()) {<br>
					String key = entry.getKey();<br>
					String values[] = entry.getValue();<br>
					paramsMap.put(key, values[0].toString()); // Only 1 value is<br>
				}<br>
				AuthProvider provider = manager.connect(paramsMap);<br>
<br>
				profile = provider.getUserProfile();<br>
				contactsList = provider.getContactList();<br>
				if (contactsList != null &amp;&amp; contactsList.size() &gt; 0) {<br>
					for (Contact p : contactsList) {<br>
						if (StringUtils.isEmpty(p.getFirstName())<br>
								&amp;&amp; StringUtils.isEmpty(p.getLastName())) {<br>
							p.setFirstName(p.getDisplayName());<br>
						}<br>
					}<br>
				}<br>
			} catch (Exception e) {<br>
				e.printStackTrace();<br>
			}<br>
			request.setAttribute("profile", profile);<br>
			request.setAttribute("contacts", contactsList);<br>
<br>
			return mapping.findForward("success");<br>
		}<br>
		// if provider null<br>
		return mapping.findForward("failure");<br>
	}<br>
}<br>
<br>
<br>
</code></pre>

<h2>Step 6. Displaying the results</h2>

We use a simple display page to show the results. First, we simply iterate over all the profile information obtained from the provider. Second, we iterate over all contacts obtained from the provider.<br>
<br>
<pre><code>&lt;html&gt;<br>
&lt;head&gt;<br>
	&lt;title&gt;SocialAuth Demo&lt;/title&gt;<br>
	&lt;style&gt;<br>
		.sectiontableheader {background-color:#C8D7E3;color:#293D6B;font-size:8pt;font-weight:bold;padding:2px;}<br>
		.sectiontableentry2 {background:none repeat scroll 0 0 #F7F7F7;padding:2px;}<br>
		.sectiontableentry1 {background:none repeat scroll 0 0 #FFFFF0;padding:2px;}<br>
	&lt;/style&gt;<br>
	&lt;script&gt;<br>
    	function updateStatus(){<br>
        	var btn = document.getElementById('btnUpdateStatus');<br>
        	btn.disabled=true;<br>
			var msg = prompt("Enter your status here:");<br>
			if(msg == null || msg.length == 0){<br>
				btn.disabled=false;<br>
		    	return false;<br>
	        }<br>
			msg = "statusMessage="+msg;<br>
			var req = new XMLHttpRequest();<br>
			req.open("POST", "&lt;%=request.getContextPath()%&gt;/socialAuthUpdateStatusAction.do");<br>
			req.setRequestHeader("Accept", "text/xml");<br>
			req.setRequestHeader("Content-type", "application/x-www-form-urlencoded");<br>
			req.setRequestHeader("Content-length", msg.length);<br>
			req.setRequestHeader("Connection", "close");<br>
			req.onreadystatechange = function () {<br>
				if (req.readyState == 4) {<br>
					if(req.responseText.length &gt; 0) {<br>
							alert(req.responseText);<br>
							btn.disabled=false;<br>
					}<br>
				}<br>
			};<br>
			req.send(msg);<br>
    	}<br>
	&lt;/script&gt;<br>
&lt;/head&gt;<br>
&lt;body&gt;<br>
&lt;%@page import="org.brickred.socialauth.Profile,java.util.*;" %&gt;<br>
&lt;%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %&gt;<br>
&lt;%@ page isELIgnored="false"%&gt;<br>
&lt;h2 align="center"&gt;Authentication has been successful.&lt;/h2&gt;<br>
&lt;br/&gt;<br>
&lt;div align="center"&gt;&lt;a href="index.jsp"&gt;Back&lt;/a&gt;&lt;/div&gt;<br>
&lt;br /&gt;<br>
&lt;h3 align="center"&gt;Profile Information&lt;/h3&gt;<br>
&lt;table cellspacing="1" cellspacing="4" border="0" bgcolor="e5e5e5" width="60%" align="center"&gt;<br>
	&lt;tr class="sectiontableheader"&gt;<br>
		&lt;th&gt;Profile Field&lt;/th&gt;<br>
		&lt;th&gt;Value&lt;/th&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry1"&gt;<br>
		&lt;td&gt;Email:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.email}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry2"&gt;<br>
		&lt;td&gt;First Name:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.firstName}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry1"&gt;<br>
		&lt;td&gt;Last Name:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.lastName}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry2"&gt;<br>
		&lt;td&gt;Country:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.country}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry1"&gt;<br>
		&lt;td&gt;Language:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.language}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry2"&gt;<br>
		&lt;td&gt;Full Name:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.fullName}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry1"&gt;<br>
		&lt;td&gt;Display Name:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.displayName}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry2"&gt;<br>
		&lt;td&gt;DOB:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.dob}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry1"&gt;<br>
		&lt;td&gt;Gender:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.gender}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry2"&gt;<br>
		&lt;td&gt;Location:&lt;/td&gt;<br>
		&lt;td&gt;&lt;c:out value="${profile.location}"/&gt;&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry1"&gt;<br>
		&lt;td&gt;Profile Image:&lt;/td&gt;<br>
		&lt;td&gt;<br>
			&lt;c:if test="${profile.profileImageURL != null}"&gt;<br>
				&lt;img src='&lt;c:out value="${profile.profileImageURL}"/&gt;'/&gt;<br>
			&lt;/c:if&gt;<br>
		&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
	&lt;tr class="sectiontableentry2"&gt;<br>
		&lt;td&gt;Update status:&lt;/td&gt;<br>
		&lt;td&gt;<br>
			&lt;input type="button" value="Click to Update Status" onclick="updateStatus();" <br>
            id="btnUpdateStatus"/&gt;		<br>
		&lt;/td&gt;<br>
	&lt;/tr&gt;<br>
&lt;/table&gt;<br>
&lt;h3 align="center"&gt;Contact Details&lt;/h3&gt;<br>
&lt;table cellspacing="1" cellspacing="4" border="0" bgcolor="e5e5e5" align="center" width="60%"&gt;<br>
	&lt;tr class="sectiontableheader"&gt;<br>
		&lt;th width="15%"&gt;Name&lt;/th&gt;<br>
		&lt;th&gt;Email&lt;/th&gt;<br>
		&lt;th&gt;Profile URL&lt;/th&gt;<br>
	&lt;/tr&gt;<br>
	&lt;c:forEach var="contact" items="${contacts}" varStatus="index"&gt;<br>
		&lt;tr class='&lt;c:if test="${index.count % 2 == 0}"&gt;sectiontableentry2&lt;/c:if&gt;<br>
        &lt;c:if test="${index.count % 2 != 0}"&gt;sectiontableentry1&lt;/c:if&gt;'&gt;<br>
			&lt;td&gt;&lt;c:out value="${contact.firstName}"/&gt; &lt;c:out value="${contact.lastName}"/&gt;&lt;/td&gt;<br>
			&lt;td&gt;&lt;c:out value="${contact.email}"/&gt;&lt;/td&gt;<br>
			&lt;td&gt;<br>
                &lt;a href='&lt;c:out value="${contact.profileUrl}"/&gt;' target="_new"&gt;<br>
                    &lt;c:out value="${contact.profileUrl}"/&gt;<br>
                &lt;/a&gt;<br>
            &lt;/td&gt;<br>
		&lt;/tr&gt;<br>
	&lt;/c:forEach&gt;<br>
&lt;/table&gt;<br>
&lt;/body&gt;<br>
&lt;/html&gt;<br>
<br>
</code></pre>

<h2>Step 7. Joining it together with struts configuration</h2>
Now we join our authentication action and success action to the JSP pages.<br>
<br>
<pre><code>&lt;!DOCTYPE struts-config PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 1.2//EN"<br>
"http://jakarta.apache.org/struts/dtds/struts-config_1_2.dtd"&gt;<br>
&lt;struts-config&gt;<br>
<br>
    &lt;form-beans&gt;<br>
            &lt;form-bean name="authForm" type="com.auth.form.AuthForm" /&gt;<br>
    &lt;/form-beans&gt;<br>
    <br>
    &lt;action-mappings&gt;<br>
    <br>
        &lt;action path="/socialAuth" type="com.auth.actions.SocialAuthenticationAction" <br>
            name="authForm" scope="session"&gt;<br>
                &lt;forward name="failure" path="/jsp/error.jsp" /&gt;<br>
        &lt;/action&gt;<br>
        <br>
        &lt;action path="/socialAuthSuccessAction" type="com.auth.actions.SocialAuthSuccessAction" <br>
            name="authForm" scope="session"&gt;<br>
                &lt;forward name="success" path="/jsp/authSuccess.jsp" /&gt;<br>
                &lt;forward name="failure" path="/jsp/error.jsp" /&gt;<br>
        &lt;/action&gt;<br>
        <br>
       &lt;/action-mappings&gt;<br>
<br>
&lt;/struts-config&gt;<br>
<br>
</code></pre>

<h2>Step 8. oauth_consumer.properties file</h2>
Created a <a href='SampleProperties.md'>oauth_consumer.properties</a> file using the keys obtained by registering your applications and put this file in your class path.<br>
<br>
<h1>Conclusion</h1>

You can get the entire source code of the sample by browsing the source code.<br>
Hope this guide would have been of help. If you have any questions, please file an issue.