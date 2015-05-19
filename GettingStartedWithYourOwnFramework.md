# Getting Started #

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


## Download the jar ##
  1. Download the [socialauth-java-sdk-4.0.zip](http://socialauth.googlecode.com/files/socialauth-java-sdk-4.0.zip)
  1. Include socialauth.jar and other required jar like socialauth-spring with jars from dependency folder in your project

## Implementation ##

Using the socialauth.jar consists of three main steps:

  * Create a properties like the sample [oauth\_consumer.properties](SampleProperties.md) using the consumer key and secrets obtained above. This file should be included in your classpath.

  * User chooses provider - Create a page where you ask the user to choose a provider. When the user clicks on a provider, you should create an instance of the SocialAuthConfig and SocialAuthManager in your handling code and redirect to the URL obtained by calling the function getAuthenticationUrl(). Remember to store SocialAuthManager object into the session.

```
  //Create an instance of SocialAuthConfgi object
   SocialAuthConfig config = SocialAuthConfig.getDefault();

  //load configuration. By default load the configuration from oauth_consumer.properties. 
  //You can also pass input stream, properties object or properties file name.
   config.load();

  //Create an instance of SocialAuthManager and set config
  SocialAuthManager manager = new SocialAuthManager();
  manager.setSocialAuthConfig(config);

  // URL of YOUR application which will be called after authentication
  String successUrl= "http://opensource.brickred.com/socialauthdemo/socialAuthSuccessAction.do";

  // get Provider URL to which you should redirect for authentication.
  // id can have values "facebook", "twitter", "yahoo" etc. or the OpenID URL
  String url = manager.getAuthenticationUrl(id, successUrl);

  // Store in session
  session.setAttribute("authManager", manager);
```

  * Provider redirects back - When you redirect the user to the provider URL, the provider would validate the user, either by asking for username / password or by existing session and will then redirect the user back to you application URL mentioned above, i.e. "http://opensource.brickred.com/socialauthdemo/socialAuthSuccessAction.do". Now you can connect to provider and obtain profile or other information using the following code

```

  // get the social auth manager from session
  SocialAuthManager manager = (SocialAuthManager)session.getAttribute("authManager");

  // call connect method of manager which returns the provider object. 
  // Pass request parameter map while calling connect method. 
   AuthProvider provider = manager.connect(SocialAuthUtil.getRequestParametersMap(request));

  // get profile
  Profile p = provider.getUserProfile();

  // you can obtain profile information
  System.out.println(p.getFirstName());

  // OR also obtain list of contacts
  List<Contact> contactsList = provider.getContactList();
		
```

  * Use Plugin - SocialAuth library now has plugin for getting feeds from Facebook, Twitter and Linkedin. It also has plugin for getting Albums from Facebook and Twitter.

```

if (provider.isSupportedPlugin(org.brickred.socialauth.plugin.FeedPlugin.class)) {
    FeedPlugin p = provider.getPlugin(org.brickred.socialauth.plugin.FeedPlugin.class);
    List<Feed> feeds = p.getFeeds();
}
if (provider.isSupportedPlugin(org.brickred.socialauth.plugin.AlbumsPlugin.class)) {
    AlbumsPlugin p = provider.getPlugin(org.brickred.socialauth.plugin.AlbumsPlugin.class);
    List<Album> albums = p.getAlbums();
}

```


That is all you have to do, really. But this is just a code snippet that we put in to show you how this works.