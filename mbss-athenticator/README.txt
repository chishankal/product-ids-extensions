================================================================
Patch ID     :  patch_mig00093
Public Jira  :  https://jira.wso2telco.com/jira/browse/INTGW-233
Description  :  MBSS Basic Authenticator for MIG 2.2.0
================================================================

DESCRIPTION
------------
MBSS basic authenticator has following functionalities,
    * Session limiting feature
    * Configurable Login time restrictions (Work time authenticator)
    * Periodic password change feature
    * Detects suspended/locked accounts and prevents those accounts from authorizing.

Please perform below modifications to the current deployment to reflect the changes.

PRE-REQUISITES
--------------

System Requirements

a) Java SE Development Kit 1.8
b) wso2telcoids-2.2.0


DEPENDANT PATCHES
-------------------------
This patch depends on following patches
    * patch_mig00087
    * patch_mig00088
    * patch_dep00096
    

INSTALLATION INSTRUCTIONS
-------------------------
1) Copy following artifacts from resources/ directory into <IS_HOME>/repository/components/dropins (replace existing ones if needed)
    * com.wso2telco.mbss.authenticator-1.0.0.jar
    * org.wso2.carbon.identity.data.publisher.session-1.0.0.jar
    * password_history/password-history-manager-1.0.0.jar

2) Copy following configuration files (replace existing ones if needed),
    * resources/mbss-authenticator-config.xml into <IS_HOME>/repository/conf directory
    * resources/password_history/password-history-identity-mgt.properties into <IS_HOME>/repository/conf/identity

3) Copy resources/pwd-reset.jsp into <IS_HOME>/repository/deployment/server/webapps/authenticationendpoint/ directory.

4) Copy 'patch00093' directory into <IS_HOME>/repository/components/patches directory.

5) To create required database tables, execute following scripts on WSO2IDENTITY_DB
    * resources/db_setup.mysql.sql
    * resources/password_history/mysql.sql


Configuration
-------------------------------------------------------

1) Open identity.xml file located at <IS_HOME>/repository/conf/identity directory and find the <EventListeners> section and insert the following snippet at the end of the section if not already present,

    <EventListener
        type="org.wso2.carbon.identity.core.handler.AbstractIdentityMessageHandler"        
        name="org.wso2.carbon.identity.data.publisher.session.impl.DbSessionDataPublisherImpl"
        orderId="11" enable="true"
    />
        
2) Various configurations options of MBSS Authenticator is defined in <IS_HOME>/repository/conf/mbss-authenticator-config.xml file. Change those configurations as needed.
    
2) Configuration options of Password history manager is defined in <IS_HOME>/repository/conf/identity/password-history-identity-mgt.properties file. Modify as needed.

3) Create following claims in IS using management console, Skip if already exists and leave the other fields in deafult state.

    i)  Dialect:                http://wso2.org/claims
        Display Name:	        UTC Offset
        Description:	        UTC Offset
        Claim Uri:	            http://wso2.org/claims/utcOffset
        Mapped Attribute (s):	utcOffset
        Supported by Default:	true

   ii)  Dialect:                http://wso2.org/claims
        Display Name:	        Day light saving time offset
        Description:        	Day light saving time offset
        Claim Uri:          	http://wso2.org/claims/dstOffset
        Mapped Attribute (s):	dstOffset
        Supported by Default:	true
     
  iii)  Dialect:                http://wso2.org/claims
        Display Name:	        Intitial Password Changed
        Description:        	Intitial Password Changed
        Claim Uri:          	http://wso2.org/claims/identity/initialPasswordChanged
        Mapped Attribute (s):	initialPasswordChanged
        Supported by Default:	false
    
3) Create the roles defined in mbss-authenticator-config.xml (inside workingTime sections) on IS using management console or remove unecessary definitions from configuration file.

4) Assign newly created roles to users as needed.

5) Restart IS

6) Now there will be an authenticator named 'MBSSBasicAuthenticator' in Authenticator configuration section of service providers. Assign MBSSBasicAuthenticator to a service provider. (NOTE: Make sure that the MBSSBasicAuthenticator is the only authenticator assigned to a particular SP)


