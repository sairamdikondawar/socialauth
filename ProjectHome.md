![http://socialauth.googlecode.com/svn/wiki/images/java.png](http://socialauth.googlecode.com/svn/wiki/images/java.png)

## For latest source code and wiki, please visit <a href='https://github.com/3pillarlabs/socialauth/'>SocialAuth on GitHub</a> ##

## Click here to <a href='https://sourceforge.net/projects/socialauth/files/latest/download'>Download SocialAuth-4.7-SDK</a> ##

SocialAuth is a Java library ([.NET port](http://code.google.com/p/socialauth-net/) & [Android version](http://code.google.com/p/socialauth-android/) available) for you if your web application requires:
  * Authenticating users through external oAuth providers like Gmail, Hotmail, Yahoo, Twitter, Facebook, LinkedIn, Foursquare, MySpace, Salesforce, Yammer as well as through OpenID providers like myopenid.com.

  * Easy user registration. All you need to do is create a page where users can click on buttons for the above providers or other supported providers. Just call SocialAuth and you can get all their profile details.

  * Importing contacts from Google, Yahoo or Hotmail. Support for importing friends from Facebook, followers from Twitter and contacts from LinkedIn is available, but currently Facebook, Twitter and LinkedIn do not provide email addresses. UPDATE: Hotmail has stopped providing email addresses.

See our [SocialAuth demo in action](http://labs.3pillarglobal.com/socialauthdemo/) !

## Whats new in Version 4.7 ? ##
  1. !Amazon Provider
  1. Stackexhange Provider
  1. Facebook API v2.2 updated
  1. Option to save raw response for Profile and Contact
  1. Option to add custom properties for a provider

## Whats new in Version 4.6 ? ##
  1. LinkedIn OAuth2 Provider
  1. Updated Mendeley API
  1. Resolved Yammer Issue


## Whats new in Version 4.5 ? ##
  1. Updated Yahoo API
  1. Updated Foursquare API
  1. Serialized SocialAuthTemplate object in socialauth-spring.

## Whats new in Version 4.4 ? ##
  1. Updated Twitter API
  1. Changes in command line utility to generate access token
  1. Added !Nimble Provider

## Whats new in Version 4.3 ? ##
  1. Added GitHub Provider
  1. Added Flickr Provider
  1. Added GooglePlus Feed Plugin
  1. Added GooglePlus Album Plugin
  1. Added Facebook wall demo
  1. Jackson support in Command line utility to generate and save access token



## Whats new in Version 4.2 ? ##
  1. Refresh token functionality for Facebook
  1. Added GooglePlus provider
  1. Added Instagram provider
  1. Twitter API v1.1 implemented
  1. Command line utility to generate and save access token
  1. All core projects are maven-ise now and jars are available on maven repository.
  1. All demos except seam and grails are maven-ise now.
  1. Struts2 socialauth demo.
  1. OAuth endpoint (RequestToken URL, Authorization URL and AccessToken URL) can be configured through properties file now.
  1. Response object returned by UpdateStatus method.
  1. Bug fixes


## Getting Started ##
We support several frameworks, Struts, Spring MVC as well as JBoss Seam based applications. For easy integration, we provide downloadable jars as well as Maven dependency. [Let us get started](https://github.com/3pillarlabs/socialauth/wiki/Getting-Started-with-implementing-SocialAuth) with using socialauth.

Please [report any issues](http://code.google.com/p/socialauth/issues/entry) and we promise to get back

## How it works? ##
  1. You get the API keys from providers like Facebook, Google and Yahoo. For this, you need to have a public domain on which you plan to deploy the application. It is important to note that your application can only run on the domain which you provided while getting the keys. If you want to run it locally, [please see the steps here](HowToRunApplicationWithLocalhostOnWindows.md). ![http://socialauth.googlecode.com/svn/wiki/images/socialauth_flow_diagram_1.jpg](http://socialauth.googlecode.com/svn/wiki/images/socialauth_flow_diagram_1.jpg)
  1. You make a request for authentication by using SocialAuth library. The library redirects the user to Facebook, Yahoo or other provider’s website where they enter the credentials.
  1. The provider redirects the user back to your application with a token appended. Now you call the SocialAuth library and pass it this request token.
  1. Now you can call SocialAuth library to get information about the user, and contacts from the provider.

![http://socialauth.googlecode.com/svn/wiki/images/socialauth_flow_diagram_2.jpg](http://socialauth.googlecode.com/svn/wiki/images/socialauth_flow_diagram_2.jpg)

## Why SocialAuth ##
There are so many libraries out there which implement OpenID and oAuth, so why another library?
There many practical challenges that we faced while doing the implementation of above use cases. None of them is insurmountable but the developer could spend a couple of weeks solving these, which we actually did and hence decided to make things better for the community.
  * There are many libraries for implementing Open ID and many for implementing oAuth. It becomes a difficult exercise to choose one that will do the integration quickly with the providers you want.
  * Some libraries do not implement all the features and it becomes known only in the later stages of implementation – for example we found out that openid4java does not implement the hybrid protocol. We also found out that it is not easy to integrate dyuproject library.
  * Even after implementing using the library, it does not work out of the box for all providers. There are always certain things specific to a certain provider. For example the scopes are different as well as some steps in authorization may be different.
  * Getting the actual data, for example contacts of a user is out of the scope of these protocols and hence most libraries do not implement this functionality.

So what we implemented is a wrapper that leverages these existing libraries, and works out of the box without requiring you to face the above challenges. You get the same interface to deal with integration of every provider.

## Acknowledgements ##

The inspiration for getting more through Open ID came through [this example](http://github.com/ajasmin/seam-openid-with-name-and-email).

Sincere thanks to [![](http://socialauth.googlecode.com/svn/wiki/images/livecomplete-logo.png)](http://livecomplete.com) for support in development of this library.

## Share it with your friends!!! ##
&lt;wiki:gadget url="http://hosting.gmodules.com/ig/gadgets/file/113501407083818715381/Opti365ShareWebpageHelper.xml" border="0"/&gt;
