**JBOSS Seam Example**
Demo application exploring socialauth library.





# Introduction #

This article is devoted to the sample seam example where we exploring the open id integration using **socialauth**library. Currently **socialauth** library affirms Gmail, Hotmail, Yahoo, Twitter, Facebook, LinkedIn, Foursquare, MySpace as well as through OpenID providers like myopenid.com. This library is the wrapper on different providers and saves a lot of effort while designing/adding functionality to a new/existing application for open id authentication.



# Prerequisites #

Prior to dig into the actual code, following are few prerequisites which are required on a developer machine.

  1. Install Java-IDE (preferred eclipse 3.3 or latter)([download](http://www.eclipse.org/downloads/)) <br />[Installation instructions](http://ist.berkeley.edu/as-ag/tools/howto/install-eclipse-win.html)
  1. Install JBossTools-2.0  eclipse plug-in  or above <br />([Update site and installation instructions](http://www.jboss.org/tools/download/))
  1. Install jboss-seam-2.2.0.GA ([download](https://olex.openlogic.com/packages/jboss-seam/)) or later in case you are using eclipse 3.4 or later.<br />Installation is just to download and unzip it in local drive.
  1. apache-ant-1.8.0 or latter ([download](http://ant.apache.org/bindownload.cgi))<br />Installation is just to download and unzip it in local drive.
  1. Download the demo project from<br />http://code.google.com/p/socialauthsourcebrowse#svn%2Ftrunk%2FSocialAuthSeam
  1. Download JBoss 5.1 ([download](http://www.jboss.org/jbossas/downloads/))<br />Installation is just to download and unzip it in local drive.
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



# Step 1: Setup a Seam Project #

Please make sure that JBoss Tool plug-in is installed in eclipse. This tool helps user to create a default seam project template and also provide the interface to create various seam artefacts. Please follow below steps to setup a demo project:

  1. Select File->New->Project and following screen will be appeared.<br />http://socialauth.googlecode.com/svn/wiki/images/seam_pic1.JPG<br />
  1. Click on Next button and fill in the resultant screen as following.<br />http://socialauth.googlecode.com/svn/wiki/images/seam_pic2.JPG
  1. Follow the wizard instructions as following<br />http://socialauth.googlecode.com/svn/wiki/images/seam_pic3.JPG<br />http://socialauth.googlecode.com/svn/wiki/images/seam_pic4.JPG<br />http://socialauth.googlecode.com/svn/wiki/images/seam_pic5.JPG<br />http://socialauth.googlecode.com/svn/wiki/images/seam_pic6.JPG<br />
> > Please select the seam runtime environment (i.e. path of jboss-seam-2.2.0.GA installed directory) and click on Finish button.
  * Above wizard will create a default seam project (i.e. **‘socialauthseam’**) with all seam configurations in the current workspace.



### Seam Project Structure ###

Jbosstool wizard creates primary project and a test project for unit testing. In this document our main focus is on primary project. Please see the structure of primary project in below screenshot.


> http://socialauth.googlecode.com/svn/wiki/images/seam_pic7.JPG

Since JBossTool generates a default seam project so we need to following modifications in default project to align it with the demo application.

Please note that JBoss Tool 3.0 brings out hot and main folders instead of action and model folders which are created with JBoss Tool 2.0. Hence configure the project artefacts as per following mappings in case you are using JBoss Tool 3.0.

action folder -> hot folder

model folder -> main folder



### Modifications ###

Please make the following changes to seam project for aligning it to as per demo application.

  * Delete following files from webcontent
    1. Home.xhtml
    1. login.page.xhtml
    1. login. xhtml
    1. layout\display.xhtml
    1. layout\edit.xhtml
    1. layout\loginout.xhtml
    1. layout\template.xhtml
    1. layout\menu.xhtml

  * Comment the persistence unit in META-INF\persistence.xhtml.<br />Since our application does not use db, so we don’t need to define persistence unit at all.



# Step 2: Setup seam configuration files #

  * components.xml : This file configures Seam and application own components. Generally, we prefer to configure components with annotations (i.e. @Name) but still we can declare it here as well.<br /> Just see the file below, it’s having a component declaration for **"socialauth"** which refers to social auth component class **`org.brickred.socialauth.seam.SocialAuth`**. This component is part of socialauth-seam library and exposes services like login, logout, contact list etc to the demo application classes.

```

<?xml version="1.0" encoding="UTF-8"?>
<components xmlns="http://jboss.com/products/seam/components"
            xmlns:core="http://jboss.com/products/seam/core"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation=
                "http://jboss.com/products/seam/core 
                 http://jboss.com/products/seam/core-2.0.xsd 
                 http://jboss.com/products/seam/components 
                 http://jboss.com/products/seam/components-2.0.xsd">
  <core:init jndi-pattern="#{ejbName}/local"/>
	<component name="socialauth" class="org.brickred.socialauth.seam.SocialAuth"/>
    
</components>


```

  * **seam.properties** : Includes settings which can be used in components.xml. Apart from this, it is a marker for Seam that an archive (WAR or JAR) includes Seam components. So just create an empty file and keep it in the root of the class path.

  * **faces-config.xml** : It’s a normal JSF configuration file. Since default rendering of seam is Java Server Faces so we need this file in WEB-INF folder. This file also comprises settings for message resource bundles and life cycles listeners. Please note that you don’t need to define navigation rules here because we are having pages.xml config file for that.
> > Following are few configurations which we have used in demo application:
    * `<view-handler>`: Since JSF provides a high level of plugging features. So this tag is used to specify a custom view handler.
    * `<message-bundle>`: Used to specify message resource bundle.
    * `<lifecycle>`: Have used to configure phase listener

```

<?xml version="1.0" encoding="UTF-8"?>
<faces-config version="1.2"
              xmlns="http://java.sun.com/xml/ns/javaee"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
                    http://java.sun.com/xml/ns/javaee/web-facesconfig_1_2.xsd">
        
    <!-- Facelets support -->
    <application>
        <view-handler>com.sun.facelets.FaceletViewHandler</view-handler>
    </application>

    <application>
    	<message-bundle>
    		org.brickred.socialauthseam.bundle.MessageResources
    	</message-bundle>
    	<locale-config>
    		<default-locale>en</default-locale>
    	</locale-config>
    </application>
	
    <lifecycle>
        <phase-listener>org.brickred.socialauth.seam.SocialAuthPhaseListener</phase-listener>
    </lifecycle>
</faces-config>


```


> Please note that class org.brickred.socialauth.seam.SocialAuthPhaseListener is part of socialauth-seam library. We just need to configure it here.

  * **pages.xml** : This file consists of navigation and security rules of a seam project. It privileges the user to declare exceptions which are called by seam framework on corresponding error occurrences.
> > In context to this demo application, we have configured 'success' view id which renders on successful login and displays various information about the logged in user.

```

<?xml version="1.0" encoding="UTF-8"?>
<pages xmlns="http://jboss.com/products/seam/pages" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://jboss.com/products/seam/pages http://jboss.com/products/seam/pages-2.2.xsd"
	no-conversation-view-id="/openid.xhtml" login-view-id="/home.xhtml">

	<page view-id="/success.xhtml"/>
	<exception>
		<end-conversation/>
		<redirect view-id="/error.xhtml">
			<message>Unexpected failure</message>
		</redirect>
	</exception>
</pages>

```


  * **web.xml** : This is standard web configuration file. This contains  standard seam configuration and a context parameter **"successUrl"** for interfacing with socialauth library. The **"successUrl"** context parameter value corresponds to the success view id where the application control is redirected by open id provider on successful authentication.

```

<?xml version="1.0" encoding="UTF-8"?>

<web-app version="2.5"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
<!-- Seam -->
	<listener>
		<listener-class>org.jboss.seam.servlet.SeamListener</listener-class>
	</listener>
	<context-param>
		<param-name>javax.faces.DEFAULT_SUFFIX</param-name>
		<param-value>.xhtml</param-value>
	</context-param>
	<context-param>
		<param-name>facelets.DEVELOPMENT</param-name>
		<param-value>true</param-value>
	</context-param>
	*<context-param>
		<param-name>successUrl</param-name>
		<param-value**>/success.xhtml</param-value>
	</context-param>*
	<servlet>
		<servlet-name>Faces Servlet</servlet-name>
		<servlet-class>javax.faces.webapp.FacesServlet</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<filter>
		<filter-name>Seam Filter</filter-name>
		<filter-class>org.jboss.seam.servlet.SeamFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>Seam Filter</filter-name>
		<url-pattern>*.seam</url-pattern>
	</filter-mapping>
	<servlet-mapping>
		<servlet-name>Faces Servlet</servlet-name>
		<url-pattern>*.seam</url-pattern>
	</servlet-mapping>
	<session-config>
		<session-timeout>10</session-timeout>
	</session-config>
</web-app>

```



  * **jboss-web.xml** : Standard configuration file for defining jndi and other data source details for JBoss server.



# Step 3: Obtain consumer keys and secrets and Setup application configuration file #


> This step is very important and the library will NOT work without this.
Create a properties like the sample [oauth\_consumer.properties](SampleProperties.md) using the consumer key and secrets obtained above. This file should be included in your classpath, for example WEB-INF/classes directory



# Step 4: Add Libraries #

Please make sure that below jar files to be copied correctly from the demo project into WEB-INF\lib folder of your application. In case you are using JBossTools-3.0 then please replace the WEB-INF\library with demo project library to avoid the jars duplication.

| commons-beanutils.jar | commons-codec.jar | commons-digester.jar |
|:----------------------|:------------------|:---------------------|
| jboss-el.jar          | jboss-seam-debug.jar |jboss-seam-ui.jar     |
| jboss-seam.jar        | jsf-facelets.jar  | log4j-1.2.15.jar     |
| richfaces-api.jar     | richfaces-impl.jar | richfaces-ui.jar     |
| openid4java.jar       | openxri-client.jar | openxri-syntax.jar   |
| htmlparser.jar        | json-20080701.jar |commons-httpclient.jar|
|socialauth-2.1.jar     | socialauth-seam-2.0-beta1.jar | dependencies-2.0.zip |

Please note that **"socialauth.jar"**, **socialauth-seam.jar**, **dependencies.zip** should be copied in WEB-INF\lib path. It is the social auth library which revealed the different open id providers to this demo application. This file is bundled with the demo application.

**External libraries:**

Please make sure that you successfully copy the external-lib folder from demo project and keep it in build path. This folder contains JSF implementation jars which have to be in build path for project compilation. This folder resides in root of the demo application.



# Step 5: Adding resource bundles #

Resource bundle is available in package org.brickred.socialauthseam.bundle under src folder.Please copy the resource bundles from there and configure it in faces-config.xml file. Please make sure that resource bundle file names are same as configured in `<message-bundle>` tag in faces-config.xml file.
<br />



# Step 6: Writing the template.xhtml #

Main template layout helps in rendering of other view pages.

```

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" 
    xmlns:ui="http://java.sun.com/jsf/facelets"
    xmlns:h="http://java.sun.com/jsf/html" 
    xmlns:f="http://java.sun.com/jsf/core"
    xmlns:rich="http://richfaces.org/rich"
    xmlns:s="http://jboss.com/products/seam/taglib"
    xmlns:a="http://richfaces.org/a4j"
    xmlns:c="http://java.sun.com/jstl/core">
	<f:view>
		<f:loadBundle basename="org.brickred.socialauthseam.bundle.MessageResources" var="msg"/>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
			<title>Social Auth</title>
			<style type="text/css">
				.showBold{
					font-weight:bold;
				}
				
				.grey{background-color: gray;color:black;}
			</style>
			<link rel="stylesheet" type="text/css" href="./stylesheet/style.css" />
		</head>
		<body>
			<div id="logo">
				<div id="slogan">&nbsp;</div>
			</div>
			<ui:insert name="mainForm">Main Form Goes Here</ui:insert>
			<div id="footer">
				<div id="left_footer"><b>&#169; 2010 BrickRed All Rights Reserved&nbsp;.</b></div>
				<div id="right_footer"><b>BrickRed Technologies Pvt. Ltd</b></div>
			</div>
		</body>
	</f:view>
</html>

```

Note: Please add css and images files from demo application into current project.



# Step 7: Writing the index.xhtml. #

Please redirect to the “home.seam” once user hits index page of the application.

```

<html>
	<head>
		<meta http-equiv="Refresh" content="0; URL=home.seam">
	</head>
</html>

```



# Step 8: Writing the home.xhtml #

Application home page comprises login links for different providers. When user clicks on any button, the updateId() method of Authenticator class will be invoked which sets the requested provider id like "facebook", "twitter" etc. in SocialAuth component. After that login() method of SocialAuth component will be called.

```

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
    xmlns:ui="http://java.sun.com/jsf/facelets"
    xmlns:h="http://java.sun.com/jsf/html"
    xmlns:f="http://java.sun.com/jsf/core">

  <ui:composition template="layout/template.xhtml">

    <ui:define name="mainForm">

        <div id="main">
            <div id="sidebar">
                <h1>
                  <a href="http://www.brickred.com/">
                    <img alt="" src="images/logo.png" style="border: 0px" />
                  </a>
                </h1>
            </div>
            
            <div id="text" >
              <h:form>
                 <table cellpadding="10" cellspacing="10" align="center">
                   <tr>
                    <td colspan="8"><h3 align="center">${msg['label.welcome']}</h3></td>
                  </tr>
                  <tr>
                    <td colspan="8"><p align="center">${msg['label.pleaseClick']}</p></td>
                   </tr>
                  <tr>
                    <td>
                      <h:commandLink id="facebook" name="facebook"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/facebook_icon.png"/>
                      </h:commandLink>
                    </td>
                    <td>
                      <h:commandLink id="twitter" name="twitter"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/twitter_icon.png"/>
                      </h:commandLink>
                    </td>
            
                    <td>
                      <h:commandLink id="google" name="google"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/gmail-icon.jpg"/>
                      </h:commandLink>
                    </td>
                    <td>
                      <h:commandLink id="yahoo" name="yahoo"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/yahoomail_icon.jpg"/>
                      </h:commandLink>
                    </td>
            
                    <td>
                        <h:commandLink id="hotmail" name="hotmail"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/hotmail.jpeg"/>
                        </h:commandLink>
                    </td>
                    <td>
                      <h:commandLink id="linkedin" name="linkedin"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/linkedin.gif"/>
                      </h:commandLink>
                    </td> 
                    <td>
                      <h:commandLink id="foursquare" name="foursquare"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/foursquare.jpeg"/>
                      </h:commandLink>
                    </td>
                    <td>
                      <h:commandLink id="myspace" name="myspace"  action="#{socialauth.login}" 
                              actionListener="#{socialauthenticator.updateId}">
                          <h:graphicImage value="/images/myspace.jpeg"/>
                      </h:commandLink>
                    </td>
                  </tr>
                </table>
              </h:form>
              
              <h:form id="my" name="my" >
                <table cellpadding="10" cellspacing="10" align="center">
                  <tr>
                    <td class="showBold">${msg['label.openid']}</td>
                    <td>
                      <h:inputText value="#{socialauthenticator.openID}" 
                        id="idfield" required="true" requiredMessage="Please enter valid input" />
                    </td>
                    <td>
                      <h:commandButton id="openid" name="openid"  action="#{socialauth.login}" 
                                 value="Openid" actionListener="#{socialauthenticator.updateId}"/>
                    </td>
                  </tr>
                  <tr>
                    <td colspan="2" style="text-align:center;color:red">
                      <h:messages/>
                    </td>
                    <td>&nbsp;</td>
                  </tr>
                </table>
              </h:form>
  
              <p class="additional">
                   ${msg['msg.footerMsg1']}
              </p>
            </div>
        </div>
    </ui:define>
  </ui:composition>
</html>

```


# Step 9: Creating the Authenticator component #


In the above home.xhtml, a actionListener is attached with various buttons which are associated with  handler method- updateId() of Authenticator component class. This class comprises Jboss seam component i.e. "socialauth" bundled with socialauth-spring library that helps to delegate the authentication to OpenId/ oAuth providers like Yahoo, Facebook, LinkedIn etc. The method updateID() is called from the UI (home.xhtml) which set the coressponding value of selected Openid/oAuth provider in socialauth component.<br />It also sets the viewUrl in SocialAuth component from conext parameter **"successUrl"** which is declared in web.xml file.

```

package org.brickred.socialauthseam.session;

import javax.faces.event.ActionEvent;
import org.brickred.socialauth.seam.SocialAuth;
import org.jboss.seam.annotations.In;
import org.jboss.seam.annotations.Name;
import org.jboss.seam.log.LogProvider;
import org.jboss.seam.log.Logging;

/**
* This is the main component class, it is referred in application pages and
* provides the navigation functionality to the user.
* @author lakhdeeps@brickred.com
*/

@Name("socialauthenticator")
public class Authenticator {

	private transient LogProvider log = Logging.getLogProvider(SocialAuth.class);
       
        //Inject SocialAuth component
	@In(create = true)
	SocialAuth socialauth;

	/**
	* Variable for storing open id from main form
	*/
	private String openID;

	/**
	* Track the user interaction with main page and set the state of components accordingly.
	* @param ActionEvent
	*/

	public void updateId(ActionEvent ae) {

		String btnClicked = ae.getComponent().getId();

                //Retrieve the successUrl context parameter
		ExternalContext context = javax.faces.context.FacesContext.getCurrentInstance().getExternalContext();
                String viewUrl = context.getInitParameter("successUrl");
               
                //set success view in SocialAuth component
                socialauth.setViewUrl(viewUrl);
		if (btnClicked.indexOf("facebook") != -1){
			socialauth.setId("facebook");
		}else if (btnClicked.indexOf("twitter") != -1){
			socialauth.setId("twitter");
		}else if (btnClicked.indexOf("yahoo") != -1){
			socialauth.setId("yahoo");
		}else if (btnClicked.indexOf("hotmail") != -1){
			socialauth.setId("hotmail");
		}else if (btnClicked.indexOf("google") != -1){
			socialauth.setId("google");
		}else if (btnClicked.indexOf("linkedin") != -1){
			socialauth.setId("linkedin");
		}else if (btnClicked.indexOf("foursquare") != -1){
			socialauth.setId("foursquare");
		}else if (btnClicked.indexOf("myspace") != -1){
			socialauth.setId("myspace");
		}else{
			socialauth.setId(openID);
		}
	}

	/**
	* Redirect the user back to the main page from success view.
	* @param ActionEvent
	*/

	public String mainPage() {
		return "/home.xhtml";
	}

	public String getOpenID() {
		return openID;
	}

	public void setOpenID(String openID) {
		this.openID = openID;
	}

}

```



# Step 10: Writing the success.xhtml #

Success view page, it shows profile and contacts list after successful login. socialauth.profile and socialauth.contactList is used to show the user profile and contacts list respectively. E.g "${socialauth.profile.email}" is used to show the email of user.

```

<!DOCTYPE composition PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
    xmlns:ui="http://java.sun.com/jsf/facelets"
    xmlns:h="http://java.sun.com/jsf/html"
    xmlns:f="http://java.sun.com/jsf/core"
    xmlns:rich="http://richfaces.org/rich"
    xmlns:s="http://jboss.com/products/seam/taglib"
    xmlns:a="http://richfaces.org/a4j"
    xmlns:c="http://java.sun.com/jstl/core">
    <ui:composition template="layout/template.xhtml">
        <ui:define name="mainForm">
            <div id="main">
                <div id="sidebar">
                    <h1><a href="http://www.brickred.com/">
                        <img alt="" src="images/logo.png" style="border: 0px" />
                        </a>
                    </h1>
                </div>
                <div id="text" >
                    <h2 align="center">${msg.get("msg.authenticationSuccessfull")}</h2>
                    <br/>
                    <div align="center">
                        <h:form>
                            <h:commandLink action="#{socialauthenticator.mainPage}" value="Back" 
                            actionListener="socialauth.logout" />
                        </h:form>
                    </div>
                    <br />
                    <h3 align="center">${msg.get("label.profileInfo")}</h3>
                    <c:if test="${not empty socialauth.profile}">
                        <table cellspacing="1" border="0" bgcolor="e5e5e5" width="60%" align="center">
                            <tr class="sectiontableheader">
                                <th>${msg.get("label.field")}</th>
                                <th>${msg.get("label.value")}Value</th>
                            </tr>
                            <tr class="sectiontableentry1">
                                <td>${msg.get("label.email")}:</td>
                                <td>${socialauth.profile.email}</td>
                            </tr>
                            <tr class="sectiontableentry2">
                                <td>${msg.get("label.firstName")}:</td>
                                <td>${socialauth.profile.firstName}</td>
                            </tr>
                            <tr class="sectiontableentry1">
                                <td>${msg.get("label.lastName")}:</td>
                                <td>${socialauth.profile.lastName}</td>
                            </tr>
                            <tr class="sectiontableentry2">
                                <td>${msg.get("label.country")}:</td>
                                <td>${socialauth.profile.country}</td>
                            </tr>
                            <tr class="sectiontableentry1">
                                <td>${msg.get("label.language")}:</td>
                                <td>${socialauth.profile.language}</td>
                            </tr>
                            <tr class="sectiontableentry2">
                                <td>${msg.get("label.fullName")}:</td>
                                <td>${socialauth.profile.fullName}</td>
                            </tr>
                            <tr class="sectiontableentry1">
                                <td>${msg.get("label.displayName")}:</td>
                                <td>${socialauth.profile.displayName}</td>
                            </tr>
                            <tr class="sectiontableentry2">
                                <td>${msg.get("label.dob")}:</td>
                                <td>${socialauth.profile.dob}</td>
                            </tr>
                            <tr class="sectiontableentry1">
                                <td>${msg.get("label.gender")}:</td>
                                <td>${socialauth.profile.gender}</td>
                            </tr>
                            <tr class="sectiontableentry2">
                                <td>${msg.get("label.location")}:</td>
                                <td>${socialauth.profile.location}</td>
                            </tr>
                            <tr class="sectiontableentry1">
                                <td>${msg.get("label.profileImage")}:</td>
                                <td>
                                    <c:if test="${socialauth.profile.profileImageURL != null}">
                                        <img src="${socialauth.profile.profileImageURL}"/>
                                    </c:if>
                                </td>
                            </tr>
                            <tr class="sectiontableentry2">
                                <td>${msg.get("label.updateStatus")}:</td>
                                <td>
                                    <a:form id="updateForm">
                                        <input type="hidden" name="statusMessage" id="statusMessage"/>
                                        <a:commandButton value="Click to Update Status" 
                                        action="#{socialAuthUpdateStatus.updateStatus}" onclick="preUpdateStatus();" 
                                        id="btnUpdateStatus" reRender="statusPanel" 
                                        oncomplete="enableUpdateStatusButton();"/>
                                    </a:form>
                                    <h:panelGroup id="statusPanel" >
                                        <h:outputText value="#{socialAuthUpdateStatus.status}" 
                                        rendered="#{not empty socialAuthUpdateStatus.status}" style="color:red"/>
                                    </h:panelGroup>
                                </td>
                            </tr>
                        </table>
                        <h3 align="center">${msg.get("label.contactDet")}</h3>
                        <table cellspacing="4" border="0" bgcolor="e5e5e5" align="center" width="60%">
                            <tr class="sectiontableheader">
                                <th width="15%">${msg.get("label.fullName")}</th>
                                <th>${msg.get("label.email")}</th>
                                <th>${msg.get("label.profileURL")}</th>
                            </tr>
                            <c:if test="${not empty socialauth.contactList}">
                                <ui:repeat value="#{socialauth.contactList}" var="contact">
                                    <tr class="sectiontableentry1">
                                        <td>${contact.firstName}</td>
                                        <td>${contact.email}</td>
                                        <td>
                                            <a href="${contact.profileUrl}" target="_new">${contact.profileUrl}</a>
                                        </td>
                                    </tr>
                                </ui:repeat>
                            </c:if>
                            <c:if test="${empty socialauth.contactList}">
                                <tr>
                                    <td colspan="4">
                                        ${msg.get("msg.noContactAvailable")}
                                    </td>
                                </tr>
                            </c:if>
                        </table>
                    </c:if>
                    <br /><br /><br /><br /><br /><br /><br /><br /><br />
                    <p class="additional">
                        ${msg.get("msg.footerMsg1")}
                    </p>
                </div>
            </div>
            <script>
                function disableUpdateStatusButton(){
                    var btn = document.getElementById('updateForm:btnUpdateStatus');
                    btn.disabled=true;
                }
                function enableUpdateStatusButton(){
                    var btn = document.getElementById('updateForm:btnUpdateStatus');
                    btn.disabled=false;
                }
                function preUpdateStatus(){
                    disableUpdateStatusButton();
                    var msg = prompt("Enter your status here:");
                    if(msg == null || msg.length == 0){
                        btn.disabled=false;
                        return false;
                    }
                    document.getElementById('statusMessage').value=msg;
                }
            </script>
        </ui:define>
    </ui:composition>
</html>


```


# Step 11: Creating the UpdateStatus component #

Seam component which sets the profile status for a particular user account. It delegate status update task by calling  setStatus() and updateStatus() methods on SocialAuth component. It comprises of single method updateStatus() which is called by UpdateStatus button on success view page.

```
package org.brickred.socialauthseam.session;
import javax.faces.context.FacesContext;
import javax.servlet.http.HttpServletRequest;
import org.brickred.socialauth.seam.SocialAuth;
import org.jboss.seam.annotations.In;
import org.jboss.seam.annotations.Name;
import org.jboss.seam.log.LogProvider;
import org.jboss.seam.log.Logging;

/**
 * This class used to update the user status on various public sites.
 * 
 * @author lakhdeeps@brickred.com
 * 
 */

@Name("socialAuthUpdateStatus")
public class UpdateStatus {
    private transient LogProvider log = Logging
            .getLogProvider(SocialAuth.class);

    @In(create = true)
    SocialAuth socialauth;

    String statusText;
    
    /**
     * Method which updates the status on profile.
     * 
     * @param ActionEvent
     * @throws Exception 
     */

    public void updateStatus() throws Exception {
        final HttpServletRequest request = (HttpServletRequest) FacesContext.getCurrentInstance()
        .getExternalContext().getRequest();
        String statusText= request.getParameter("statusMessage");
        if(statusText!=null && !statusText.equals("")){
            socialauth.setStatus(statusText);
            socialauth.updateStatus();
            setStatus("Status Updated Successfully");
        }
    }

    public String getStatus() {
        return statusText;
    }

    public void setStatus(String statusText) {
        this.statusText = statusText;
    }
}
```



# Step 12: Writing the error.xhtml #

Error page displays in case of abnormal behaviour of application.

```

<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://java.sun.com/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:rich="http://richfaces.org/rich"
	xmlns:s="http://jboss.com/products/seam/taglib"
	xmlns:a="http://richfaces.org/a4j"
	xmlns:c="http://java.sun.com/jstl/core">

	<ui:composition template="layout/template.xhtml">
		<ui:define name="mainForm">
			<h1>Error</h1>
			<p>Something bad happened :-(</p>
			<h:messages styleClass="message"/>
		</ui:define>
	</ui:composition>
</html>

```



# Step 13: Adding the style css #

Please create a folder call stylesheet under webcontent folder and copy the style.css from demo project.



# Step 14: Building of war file #

Following are two approaches to produce war file:

First is through command prompt; it requires ant installation. Also make sure that you have set the ant installed directory path correctly. To launch the ant you need to follow below steps:

  1. Go to project directory
  1. Type ant          (enter) it will read the build instructions from build.xml file.
  1. On completion of ant script, a war (socialauthseam.war) will be generated in**dist**folder under main project folder

Second way is to launch ant through eclipse as shown in below picture. On completion of ant script, a war file called “socialauthseam.war” will be available in **dist**folder under main project folder. This resultant war is ready for deployment on JBoss server.



# Step 15: Deployment #

Deployment is the last but not least step of development journey. Please copy the socialauthseam.war file from dist folder to JBoss’ deployment folder. Usually, we deploy the applications at **C:\jboss-5.1.0.GA\server\default\deploy** location in JBoss. Please start the Jboss server by double clicking bin\run.bat file (run.sh on linux/unix).

After server start you can access the application with following url address in web browser.

http://localhost:8080/socialauthseam/