

# Introduction #

This is the step by step guide to show how to develop a CDI application using socialauth library. If you want demo application of CDI, you can get it from SDK.

# Prerequisites #

Prior to dig into the actual code, following are few prerequisites which are required on a developer machine

  1. Install Java-IDE (preferred eclipse 3.3 or latter)([download](http://www.eclipse.org/downloads/)) <br />[Installation instructions](http://ist.berkeley.edu/as-ag/tools/howto/install-eclipse-win.html)
  1. Install JBossTools-2.0  eclipse plug-in  or above <br />([Update site and installation instructions](http://www.jboss.org/tools/download/))
  1. apache-ant-1.8.0 or latter ([download](http://ant.apache.org/bindownload.cgi))

# Step 1: Setup a CDI Project using JBoss Tool #

Please make sure that JBoss Tool plug-in is installed in eclipse. This tool helps user to create a JBoss  6.0 runtime environment and allow to add CDI capabilities. Please follow below steps to setup a demo project:
  * Create a dynamic web project using eclipse New Project wizard.
> Please follow corresponding numbered options in following figures 1 &2.

Figure 1<br />
![http://socialauth.googlecode.com/svn/wiki/images/cdi_demo1.jpg](http://socialauth.googlecode.com/svn/wiki/images/cdi_demo1.jpg)

Figure 2<br />
![http://socialauth.googlecode.com/svn/wiki/images/cdi_demo2.jpg](http://socialauth.googlecode.com/svn/wiki/images/cdi_demo2.jpg)

**Note:-**
  1. Please select JBOSS 6.0 runtime environment. If it’s not available then please create it using Window -> Preferences -> Server tab -> Runtime Environments -> Add option.
  1. Choose dynamic module version 3.0 as shown in Figure-1.
  1. Please click on Modify (point 3 marked in Figure-1) to choose required facets.
  1. Select CDI facet (point 4 marked in Figure-2).
  1. Select Java Server Faces facet (point 5 marked in Figure-2).

  * Click on Finish button (shown in Figure-1) to create the project.

Above wizard will create an empty CDI web project with following structure as shown in following screenshot.<br /><br />
![http://socialauth.googlecode.com/svn/wiki/images/cdi_demo3.jpg](http://socialauth.googlecode.com/svn/wiki/images/cdi_demo3.jpg)

# Step 2: Setup CDI configuration file #

  * beans.xml: This file automatically created by wizard. Although, in our example it would not contain any specific CDI information but it needs to be in the WAR to enable CDI services.
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://java.sun.com/xml/ns/javaee"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="
      http://java.sun.com/xml/ns/javaee   
      http://java.sun.com/xml/ns/javaee/beans_1_0.xsd">

</beans>

```

  * faces-config.xml: It’s a JSF configuration file. Since this web project uses JSF MVC so this file is required to be in WEB-INF folder. This file comprises the settings for message resource bundles as well.
```
<?xml version="1.0" encoding="UTF-8"?>
<faces-config xmlns="http://java.sun.com/xml/ns/javaee"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
http://java.sun.com/xml/ns/javaee/web-facesconfig_2_0.xsd"
   version="2.0">
   <application>
	  <resource-bundle>
		<base-name>org.brickred.socialauthcdi.bundle.MessageResources</base-name>
		<var>msg</var>
	   </resource-bundle>
     </application>
</faces-config>
```

  * web.xml: This is a standard web configuration file. It doesn’t having any special entry except standard JSF configuration and a context parameter for interfacing with socialauth library.
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" 
xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" version="3.0">
  <display-name>CDI Sample</display-name>
  <context-param>
    <param-name>javax.faces.PROJECT_STAGE</param-name>
    <param-value>Development</param-value>
  </context-param>
  <context-param>
    <param-name>successUrl</param-name>
    <param-value>/success.xhtml</param-value>
  </context-param>
  <servlet>
    <servlet-name>Faces Servlet</servlet-name>
    <servlet-class>javax.faces.webapp.FacesServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>Faces Servlet</servlet-name>
    <url-pattern>*.jsf</url-pattern>
  </servlet-mapping>
  <session-config>
    <session-timeout>10</session-timeout>
  </session-config>
  <security-constraint>
    <display-name>Restrict access to XHTML documents</display-name>
    <web-resource-collection>
      <web-resource-name>XHTML</web-resource-name>
      <url-pattern>*.xhtml</url-pattern>
    </web-resource-collection>
    <auth-constraint/>
  </security-constraint>
  <mime-mapping>
    <extension>gif</extension>
    <mime-type>image/gif</mime-type>
  </mime-mapping>
  <mime-mapping>
    <extension>jpeg</extension>
    <mime-type>image/jpeg</mime-type>
  </mime-mapping>
  <mime-mapping>
    <extension>jpg</extension>
    <mime-type>image/jpg</mime-type>
  </mime-mapping>
  <mime-mapping>
    <extension>png</extension>
    <mime-type>image/png</mime-type>
  </mime-mapping>
</web-app>

```

The **"successUrl"** context parameter in above configuration is a custom parameter which corresponds to the success view id where the application control is redirected by open id provider on successful authentication.


# Step 3: Setup application configuration files #

  * oauth\_consumer.properties : Properties file with exclusive settings
for different open id providers. E.g.
```
#google
www.google.com.consumer_key = opensource.brickred.com
www.google.com.consumer_secret = YC06FqhmCLWvtBg/O4W/aJfj
```
For simplicity please take this file from demo application and keep it in class path. Don’t forget to generate security keys for different vendors.

# Step 4: Add Libraries #
Please make sure that below jar files to be copied correctly from the demo project into WEB-INF\lib folder of your application. In case you are using JBossTools-3.0 then please replace the WEB-INF\library with demo project library to avoid the jars duplication.

![http://socialauth.googlecode.com/svn/wiki/images/cdi_demo4.jpg](http://socialauth.googlecode.com/svn/wiki/images/cdi_demo4.jpg)

Please note that **"socialauth4.0.jar"** & **"socialauth-cdi-2.0-beta1.jar"** should be copied in WEB-INF\lib path. These JARs are built from "socialauth-core" & "socialauth-cdi" core projects.


# Step 5: Creating the Authenticator component #
Create a CDI component i.e. Authenticator. It is wrapped the socialauth-cdi library component (SocialAuth) and processes the user’s UI operations.
```
package org.brickred.socialauthcdi.session;

import java.io.Serializable;

import javax.enterprise.context.SessionScoped;
import javax.faces.context.ExternalContext;
import javax.faces.event.ActionEvent;
import javax.faces.event.ComponentSystemEvent;
import javax.inject.Inject;
import javax.inject.Named;

import org.apache.log4j.Logger;
import org.brickred.socialauth.cdi.SocialAuth;


/**
 * This is the main component class, it is referred in application pages and
 * provides the navigation functionality to the user.
 * 
 * @author lakhdeeps@brickred.com
 * 
 */
@SessionScoped
@Named("socialauthenticator")

public class Authenticator implements Serializable{

	private static final Logger log = Logger.getLogger(Authenticator.class);

	@Inject
	SocialAuth socialauth;

	/**
	 * Variable for storing open id from main form
	 */
	private String openID;

	/**
	 * Track the user interaction with main page and set the state of components
	 * accordingly.
	 * 
	 * @param ActionEvent
	 */

	public void updateId(ActionEvent ae) {
		String btnClicked = ae.getComponent().getId();
		log.info("*************login method called ************"
				+ socialauth.getId());

		ExternalContext context = javax.faces.context.FacesContext
				.getCurrentInstance().getExternalContext();

		String viewUrl = context.getInitParameter("successUrl");
		socialauth.setViewUrl(viewUrl);

		if (btnClicked.indexOf("facebook") != -1) {
			socialauth.setId("facebook");
			log.info("***facebook*********" + socialauth.getId());
		} else if (btnClicked.indexOf("twitter") != -1) {
			socialauth.setId("twitter");
			log.info("***twitter*********" + socialauth.getId());
		} else if (btnClicked.indexOf("yahoo") != -1) {
			socialauth.setId("yahoo");
			log.info("***yahoo*********" + socialauth.getId());
		} else if (btnClicked.indexOf("hotmail") != -1) {
			socialauth.setId("hotmail");
			log.info("***hotmail*********" + socialauth.getId());
		} else if (btnClicked.indexOf("google") != -1) {
			socialauth.setId("google");
			log.info("***google*********" + socialauth.getId());
		} else if (btnClicked.indexOf("linkedin") != -1) {
			socialauth.setId("linkedin");
			log.info("***linkedin*********" + socialauth.getId());
		} else if (btnClicked.indexOf("foursquare") != -1) {
			socialauth.setId("foursquare");
			log.info("***foursquare*********" + socialauth.getId());
		} else {
			socialauth.setId(openID);
			log.info("***openID*********" + socialauth.getId());
		}
	}

	/**
	 * Redirect the user back to the main page from success view.
	 * 
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

	public void verify(ComponentSystemEvent cse) {

		boolean ajaxRequest = javax.faces.context.FacesContext
				.getCurrentInstance().getPartialViewContext().isAjaxRequest();
		if (!ajaxRequest) {
						try {
				socialauth.connect();
			} catch (Exception e) {
				log.warn(e);
			}
		}
	}
}

```

# Step 6: Creating the UpdateStatus component #

This component is used to update the status text on various integrated social sites.
```
@RequestScoped
@Named("socialAuthUpdateStatus")
public class UpdateStatus implements Serializable {
	private static final Logger log = Logger.getLogger(Authenticator.class);

	@Inject
	SocialAuth socialauth;

	String statusText;

	/**
	 * Method which updates the status on profile.
	 * 
	 * @param ActionEvent
	 * @throws Exception
	 */

	public void updateStatus() throws Exception {
		final HttpServletRequest request = (HttpServletRequest) FacesContext
				.getCurrentInstance().getExternalContext().getRequest();
		String statusText = request.getParameter("statusMessage");
		if (statusText != null && !statusText.equals("")) {
			socialauth.setStatus(statusText);
			socialauth.updateStatus();
			setStatus("Status Updated Successfully");
			System.out.println("status text:" + statusText);
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

# Step 7: Adding resource bundles #

Resource bundle is available in package org.brickred.socialauthcdi.bundle under src folder. Please copy the resource bundles from there and configure it in faces-config.xml file. Please make sure that resource bundle file names are same as configured in 

&lt;message-bundle&gt;

 tag in faces-config.xml file.

# Step 8: Writing the template.xhtml #
Main template layout helps in rendering of other view pages.
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" 
xmlns:ui="http://java.sun.com/jsf/facelets"
xmlns:h="http://java.sun.com/jsf/html" 
xmlns:f="http://java.sun.com/jsf/core" 
xmlns:rich="http://richfaces.org/rich"
xmlns:a="http://richfaces.org/a4j"
xmlns:c="http://java.sun.com/jstl/core">
    <f:view>
  	 <f:loadBundle basename="org.brickred.socialauthcdi.bundle.MessageResources" var="msg"/>
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
	        <div id="slogan">
	            &nbsp;</div>
	    </div>
  	  	<ui:insert name="mainForm">Main Form Goes Here</ui:insert>
		 <div id="footer">
		        <div id="left_footer">
		            <b>&#169; 2010 BrickRed All Rights Reserved&nbsp;.</b></div>
		        <div id="right_footer">
		            <b>BrickRed Technologies Pvt. Ltd</b></div>
		 </div>
     
     
	</body>
     
	 </f:view> 
</html>

```

Note: Please add css and images files from demo application into current project.


# Step 9: Writing the index.xhtml #

Please redirect to the “mainpage .jsf” once user hits index page of the application.
```
<html>
<head>
  <meta http-equiv="Refresh" content="0; URL=home.jsf">
</head>
</html>
```

# Step 10: Writing the home.xhtml #
Application home page comprises login links for different providers

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
                <h1><a href="http://www.brickred.com/">
<img alt="" src="images/logo.png" style="border: 0px" /></a></h1>
            </div>
            
            <div id="text" >
            <h:form>
                <table cellpadding="10" cellspacing="10" align="center">
                    <tr><td colspan="6"><h3 align="center">${msg['label.welcome']}</h3></td></tr>
                    <tr><td colspan="6"><p align="center">${msg['label.pleaseClick']}</p></td></tr>
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
                    </td><td>
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

                
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
                
            <p class="additional">
                ${msg['msg.footerMsg1']}
            </p>
            
            </div>
        </div>
    </ui:define>
    </ui:composition>
</html>

```

# Step 11: Writing the success.xhtml #
Success view page, it renders on successful login and display various information about logged in user.
```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
xmlns:ui="http://java.sun.com/jsf/facelets"
xmlns:h="http://java.sun.com/jsf/html"
xmlns:f="http://java.sun.com/jsf/core"
xmlns:c="http://java.sun.com/jsp/jstl/core">
<h:head>
    <f:metadata>  
               <f:event type="preRenderView" listener="#{socialauthenticator.verify}" />  
    </f:metadata>  
</h:head>

<ui:composition template="layout/template.xhtml">

    <ui:define name="mainForm">
       
    <div id="main">
        <div id="sidebar">
           <h1><a href="http://www.brickred.com/">
                <img alt="" src="images/logo.png" style="border: 0px" />
            </a></h1>
        </div>
         <div id="text" >
               <h2 align="center">${msg['msg.authenticationSuccessfull']}</h2>
            <br/>
            <div align="center">
                <h:form>
                    <h:commandLink action="#{socialauthenticator.mainPage}" 
                    value="Back" actionListener="socialauth.logout" />
                </h:form>
            </div>
            <br />
            <h3 align="center">${msg['label.profileInfo']}</h3>
            <ui:fragment rendered="${not empty socialauth.profile}" >
                <table cellspacing="1" border="0" bgcolor="e5e5e5" width="60%" align="center">
                    <tr class="sectiontableheader">
                        <th>${msg['label.field']}</th>
                        <th>${msg['label.value']}Value</th>
                    </tr>
                    <tr class="sectiontableentry1">
                        <td>${msg['label.email']}:</td>
                        <td>${socialauth.profile.email}</td>
                    </tr>
                    <tr class="sectiontableentry2">
                        <td>${msg['label.firstName']}:</td>
                        <td>${socialauth.profile.firstName}</td>
                    </tr>
                    <tr class="sectiontableentry1">
                        <td>${msg['label.lastName']}:</td>
                        <td>${socialauth.profile.lastName}</td>
                    </tr>
                    <tr class="sectiontableentry2">
                        <td>${msg['label.country']}:</td>
                        <td>${socialauth.profile.country}</td>
                    </tr>
                    <tr class="sectiontableentry1">
                        <td>${msg['label.language']}:</td>
                        <td>${socialauth.profile.language}</td>
                    </tr>
                    <tr class="sectiontableentry2">
                        <td>${msg['label.fullName']}:</td>
                        <td>${socialauth.profile.fullName}</td>
                    </tr>
                    <tr class="sectiontableentry1">
                        <td>${msg['label.displayName']}:</td>
                        <td>${socialauth.profile.displayName}</td>
                    </tr>
                    <tr class="sectiontableentry2">
                        <td>${msg['label.dob']}:</td>
                        <td>${socialauth.profile.dob}</td>
                    </tr>
                    <tr class="sectiontableentry1">
                        <td>${msg['label.gender']}:</td>
                        <td>${socialauth.profile.gender}</td>
                    </tr>
                    <tr class="sectiontableentry2">
                        <td>${msg['label.location']}:</td>
                        <td>${socialauth.profile.location}</td>
                    </tr>
                    <tr class="sectiontableentry1">
                    <td>${msg['label.profileImage']}:</td>
                    <td>
                        <ui:fragment rendered="${socialauth.profile.profileImageURL != null}">
                            <img src="${socialauth.profile.profileImageURL}"/>
                        </ui:fragment>
                    </td>
                    </tr>
                    <tr class="sectiontableentry2">
                        <td>${msg['label.updateStatus']}:</td>
                        <td>
                            <h:panelGrid>
                                <h:form prependId="false"> 
                                    <h:inputHidden id="statusMessage"/>
                                    <h:commandButton value="Click to Update Status" id="btnUpdateStatus"
                                        actionListener="#{socialAuthUpdateStatus.updateStatus}" 
                                        onclick="return preUpdateStatus();">
                                            <f:ajax execute="statusMessage" render="statusPanel" 
                                            onevent="processEvent" onerror="processError"/>
                                    </h:commandButton> 
                                    
                                    <h:panelGroup id="statusPanel" >
                                        <h:outputText value="#{socialAuthUpdateStatus.status}" style="color:red"/>
                                    </h:panelGroup>
                                </h:form>
                            </h:panelGrid>
                        </td>
                    </tr>
                    
            </table>
            <h3 align="center">${msg['label.contactDet']}</h3>
            
            <table cellspacing="4" border="0" bgcolor="e5e5e5" align="center" width="60%">
                <tr class="sectiontableheader">
                    <th width="15%">${msg['label.fullName']}</th>
                    <th>${msg['label.email']}</th>
                    <th>${msg['label.profileURL']}</th>
                </tr>
                    <ui:fragment rendered="${not empty socialauth.contactList}">
                        <ui:repeat value="#{socialauth.contactList}" var="contact">
                            <tr class="sectiontableentry1">
                                <td>${contact.firstName}</td>
                                <td>${contact.email}</td>
                                <td><a href="${contact.profileUrl}" target="_new">${contact.profileUrl}</a></td>
                            </tr>
                        </ui:repeat>
                    </ui:fragment>    
                    
                    <ui:fragment rendered="${empty socialauth.contactList}">
                        <tr>
                            <td colspan="4">
    
                                ${msg['msg.noContactAvailable']}
    
                            </td>
                        </tr>
                    </ui:fragment>
            </table>
            
        </ui:fragment>
        
        
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
            <br />
             <p class="additional">
                ${msg['msg.footerMsg1']}
            </p>
        </div>
    </div>
    
          <script>
                function disableUpdateStatusButton(){
                    var btn = document.getElementById('btnUpdateStatus');
                    btn.disabled=true;
                }
                function enableUpdateStatusButton(){
                    var btn = document.getElementById('btnUpdateStatus');
                    btn.disabled=false;
                }

                function preUpdateStatus(){
                    disableUpdateStatusButton();
                    var msg = prompt("Enter your status here:");
                    if(msg == null || msg.length == 0){
                        enableUpdateStatusButton();
                        return false;
                    }
                    document.getElementById('statusMessage').value=msg;
                }

                function processEvent(e){
                        var status= e.status
                        if(status=='complete'){
                            enableUpdateStatusButton();
                        }
                    }

                function processError(e){
                     alert("A error has been occured.")
                     enableUpdateStatusButton();
                    }
                
        </script>
    </ui:define>
</ui:composition>
</html>

```

# Step 12: Writing the error.xhtml #
Error page displays in case of abnormal behaviour of application.
```
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://java.sun.com/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:rich="http://richfaces.org/rich"
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

# Step 14: Adding the build files #
Please copy the following build files from demo project and put in the root folder of your project.
  * env.properties: this file contains jboss.home property which is used in ant build script.
  * build.xml: ant script to generate and deploy war file to JBOSS server.

# Step 15: Building & deploying the war file #

Please run the build.xml to generate and deploy the war file under server/default/deploy folder in JBOSS server.

Please start the Jboss server using bin\run.bat file (run.sh on linux/unix). After successful server start-up, you can access the application using following address in web browser.

http://[localhost or opensource.brickred.com]:8080/socialauth-cdi-demo/home.jsf

Landing page: <br />
![http://socialauth.googlecode.com/svn/wiki/images/cdi_demo5.jpg](http://socialauth.googlecode.com/svn/wiki/images/cdi_demo5.jpg)