# Using SocialAuth ServletFilter #

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

Download [socialauth-java-sdk-4.0.zip](http://socialauth.googlecode.com/files/socialauth-java-sdk-4.0.zip) and include the following from dist and dependencies folder

  1. socialauth-4.0.jar
  1. socialauth-filter-2.2.jar
  1. jars from dependencies folder

## Implementation ##

socialauth-filter.jar contains a filter class and set of helper classes which are responsible for smoothly interaction between your web application and socialauth library. SocialAuthSecurityFilter is the filter class for managing socialauth-provider connection flow. It redirects to the actual provider for login and handles the callback. Once the user provides credentials and the provider redirects back to your application, one of the callback methods is called. This jar consists of other important classes (i.e. DefaultSASFHelper & SASFStaticHelper) which actually wrap low level socialauth-library objects.

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
	<param-value>socialauth_filter.properties</param-value>
</context-param>
```
  1. socialauth\_filter.properties: This file consists of properties which are used to configure socialauth-filter api. Please note that this file should be included in your class path.
```
oauth_consumers = oauth_consumer.properties #file contains keys
filter.url = /SAF #filter url-mapping
error.page.url = /jsp/error.jsp #error page
webapp.success.action = /socialAuthSuccessAction.do #callback URL on successful authentication
```
  1. Create a property file like the sample [oauth\_consumer.properties](SampleProperties.md) using the consumer key and secrets obtained above. This file should be included in your classpath.
  1. Forward your auth provider requests to filter as shown in below example code.
```
/*struts sample code*/
String filterUrl = "/SAF/SocialAuth?id=" + id;
ActionForward fwd = new ActionForward("openAuthUrl", filterUrl, true);
```
  1. On successful authentication, filter api stores communication object (i.e. SASFHelper) in session so it can be obtained for accessing other profile information on successful page.
```
SASFHelper helper = SASFStaticHelper.getHelper(request);
Profile profile = helper.getUserProfile();
List<Contact> contactsList= helper.getContactList();
```

**Download the demo project from** http://socialauth.googlecode.com/svn/trunk/socialauth-struts-filter-demo