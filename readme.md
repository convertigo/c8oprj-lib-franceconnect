


# lib_FranceConnect

This library enables **FranceConnect** Single Sign on on a Convertigo Application. 

# Usage

Just drop the **FranceConnect** Shared component in one of the pages of you application. This will automatically perform the FranceConnect OpendID protocol to connect using all the FranceConnect providers.

# Symbols

This library uses the following symbols :


| Symbol                            		| Usage    		| Default Value|
| --------------------------------------| ------------- |---------------|
| lib_franceconnect.authorize 			| The FranceConnect Authorize Url. Can be https://fcp.integ01.dev-franceconnect.fr/api/v1/authorize for integration environment or https://app.franceconnect.gouv.fr/api/v1/authorize for production		| https://app.franceconnect.gouv.fr/api/v1/authorize  |
| lib_franceconnect.redirecturi   		| The redirection URI after login. Must relect the hosted application in the form : <br /> https://&lt;hosted server DNS name&gt;/convertigo/projects/lib_FranceConnect/getTokenFranceConnect.html	 | https://pedro.convertigo.net/convertigo/projects/lib_FranceConnect/getTokenFranceConnect.html |
| lib_franceconnect.clientid   			| The France Connect client id	 | 211286433e39cce01db448d80181bdfd005554b19cd51b3fe7943f6b3b86ab6e |
| lib_franceconnect.clientsecret.secret 	| The France Connect client secret	|2791a731e6a59f56b6b4dd0d08c9b1f593b5f3658b9fd731cb24248e2669af4b |

# globals

When FranceConnect connection is successful you will find in the **fcConnectedInfo** global object of your application an object with the following fields :

| Field                 | Description                                      |
| ----------------------| -------------------------------------------------|
| family_name 			| The FC family name as described in the FC scope  |
| given_name 			| The FC given name as described in the FC scope   |
| sub 					| The FC sub id (Unique ID for the user)           |

Otherwise this global will be **null**



For more technical informations : [documentation](./project.md)

- [Installation](#installation)
- [Mobile Library](#mobile-library)
    - [Shared Components](#shared-components)
        - [FranceConnect](#franceconnect)


## Installation

1. In your Convertigo Studio click on ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/icons/studio/project_import.gif?raw=true "Import a project in treeview") to import a project in the treeview
2. In the import wizard

   ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/tomcat/webapps/convertigo/templates/ftl/project_import_wzd.png?raw=true "Import Project")
   
   paste the text below into the `Project remote URL` field:
   <table>
     <tr><td>Usage</td><td>Click the copy button at the end of the line</td></tr>
     <tr><td>To contribute</td><td>

     ```
     lib_FranceConnect=https://github.com/convertigo/c8oprj-lib-franceconnect.git:branch=master
     ```
     </td></tr>
     <tr><td>To simply use</td><td>

     ```
     lib_FranceConnect=https://github.com/convertigo/c8oprj-lib-franceconnect/archive/master.zip
     ```
     </td></tr>
    </table>
3. Click the `Finish` button. This will automatically import the __lib_FranceConnect__ project


## Mobile Library

Describes the mobile application global properties

### Shared Components

#### FranceConnect

Drop this component in your login page to enable FranceConnect connections



