# Using SocialAuth with Seam 2.0 #

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

## Include the jars ##

Download [socialauth-java-sdk-4.0.zip](http://socialauth.googlecode.com/files/socialauth-java-sdk-4.0.zip) and include the following from dist and dependencies folder

  1. socialauth-4.0.jar
  1. socialauth-seam-2.0-beta1.jar
  1. jars from dependencies folder


## Implementation ##

Socialauth-seam.jar library consists of just two fundamental classes which support JBoss seam application. SocialAuth bean is the seam component responsible for calling the corresponding requested provider like facebook, yahoo or google. SocialAuthPhaseListener is the listener that processes the redirection from a provider.

  1. Add socialauth component definition in your seam component.xml file
```
<component name="socialauth" class="org.brickred.socialauth.seam.SocialAuth"/>
```
  1. Add the view in your web.xml which will be displayed after successful authentication using SocialAuth
```
<context-param>
        <param-name>successUrl</param-name>
        <param-value>/success.xhtml</param-value>
 </context-param>
```
  1. Register the phase listener in faces-config.xml
```
<lifecycle>
        <phase-listener>org.brickred.socialauth.seam.SocialAuthPhaseListener</phase-listener>
</lifecycle>
```
  1. Inject SocialAuth Component in the component you will use for processing authentication
```
@In(create = true)
SocialAuth socialauth;
```
  1. In the same component above, use the following code to pass the provider you would like to use, for example, "facebook", "yahoo" or any other. This may happen by detecting which icon the user clicked on, or by just passing a static string containing provider id if you are using just a single provider
```
//get success url from context param
ExternalContext context = javax.faces.context.FacesContext.getCurrentInstance().getExternalContext();
String viewUrl = context.getInitParameter("successUrl");

//set success URL in socialauth component
socialauth.setViewUrl(viewUrl);
 
//set provider id in socialauth component
socialauth.setId("facebook");
```
  1. Create Success view page success.xhtml, it renders on successful login and displays various information about the logged in user. You can get profile object and contact list to display by using the socialauth bean as follows.
```
//profile object
socialauth.profile

//get contacts list
socialauth.contactList
```


[Step by Step JSF or JBoss Seam 2.0 application using socialauth](SeamSample.md)
