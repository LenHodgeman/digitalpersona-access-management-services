---
layout: default
title: Web Enrollment Service
nav_order: 2
---

## Web Enrollment Service

The DigitalPersona Web Enrollment Service (WES) provides the methods necessary for  
- Creating users in the DigitalPersona LDS database  
- Enrolling both DigitalPersona AD and DigitalPersona LDS users  
- Managing credentials and other data relating to users

Web Enrollment features are accessed through the WES, implemented as a GUI-less web service. It should be located inside the enterprise’s firewall, and will direct enrollment requests to an existing DigitalPersona Server. WES uses a simple RESTful API with HTTP GET/POST/PU/DELETE for stateless communication. It also requires one or more open web endpoints listening on the HTTPS protocol for intranet and/or internet use.  

A sample program is available [here](https://lenhodgeman.github.io/digitalpersona-web-sample/index.html), which provides a simple GUI-based application  illustrating the main features of the service provided through this API.  

### IDPWebEnroll interface  

The IDPWebEnroll interface is a Windows Foundation Class (WCF) interface, and is described below.  

~~~namespace WebServices.DPWebEnroll
{
	[ServiceContract]
	public interface IDPWebEnroll
	{
		/*
		* Return information which credentials are enrolled for specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "GET",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate ="GetUserCredentials?user={userName}&type={userNameType}")]
		Object GetUserCredentials(String userName, UInt16 userNameType);
		/*
		* Return credential specific public enrollment information
		*/
		[OperationContract()]
		[WebInvoke(Method = "GET",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "GetEnrollmentData?user={userName}&type={userNameType}&cred_id={credentialId}")]
		String GetEnrollmentData(String userName, UInt16 userNameType, String credentialId);
		/*
		* Creates specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "PUT",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "CreateUser")]
		void CreateUser(Ticket secOfficer, User user, String password);
		/*
		* Deletes specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "DELETE",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "DeleteUser")]
		void DeleteUser(Ticket secOfficer, User user);
		/*
		* Enroll specific credentials for specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "PUT",
				ResponseFormat = WebMessageFormat.Json,
				BodyStyle = WebMessageBodyStyle.Wrapped,
				UriTemplate = "EnrollUserCredentials")]
		void EnrollUserCredentials(Ticket secOfficer, Ticket owner, Credential credential);
		/*
		* Delete specific credentials for specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "DELETE",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "DeleteUserCredentials")]
		void DeleteUserCredentials(Ticket secOfficer, Ticket owner, Credential credential);

		/*
		* Enroll specific credentials for Non AD user without user authentication
		*/
		[OperationContract()]
		[WebInvoke(Method = "PUT",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "EnrollAltusUserCredentials")]
		void EnrollAltusUserCredentials(Ticket secOfficer, User user, Credential credential);
		/*
		* Delete specific credentials for Non AD user without user authentication
		*/
		[OperationContract()]
		[WebInvoke(Method = "DELETE",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "DeleteAltusUserCredentials")]
		void DeleteAltusUserCredentials(Ticket secOfficer, User user, Credential credential);
		/*
		* Get specific attribute for specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "POST",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "GetUserAttribute")]
		Attribute GetUserAttribute(Ticket ticket, User user, String attributeName);
		/*
		* Put specific attribute to specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "PUT",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "PutUserAttribute")]
	void PutUserAttribute(Ticket ticket, User user, String attributeName,
			AttributeAction action, Attribute attributeData);
		/*
		* Self Unlock user account
		*/
		[OperationContract()]
		[WebInvoke(Method = "POST",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "UnlockUser")]
		void UnlockUser(User user, Credential credential);
		/*
		* Call for credential specific Custom Action.
		*/
		[OperationContract()]
		[WebInvoke(Method = "POST",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "CustomAction")]
		String CustomAction(Ticket ticket, User user, Credential credential, UInt16 actionId);
		/*
		*   Check if enrollment allowed for specific user
		*/
		[OperationContract()]
		[WebInvoke(Method = "POST",
			ResponseFormat = WebMessageFormat.Json,
			BodyStyle = WebMessageBodyStyle.Wrapped,
			UriTemplate = "IsEnrollmentAllowed")]
		void IsEnrollmentAllowed(Ticket secOfficer, User user, String credentialId);
		}
}
~~~  

### Methods
The methods available through the Web Enrollment Servicec API are as follows.  

#### GetUserCredentials method  
The GetUserCredentials method allows the caller to request information about the credentials that have been enrolled by a specified user. GetUserCredentials should be implemented as HTTP GET using JSON as the response format.

##### Syntax

~~~
List<String> GetUserCredentials(String userName, UInt16 userNameType);
~~~

<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:20%" ALIGN="left">Parameter</th>
    <th style="width:35%" ALIGN="left" colspan="2">Description</th>
  </tr>
  <tr>
  <td valign="top">username</td>
  <td valign="top" colspan="2">Name of the user whose credential needs to be verified.</td>
  </tr>
  <tr>
  <td valign="top">userNameType</td>
  <td valign="top" colspan="2">The specific format of the user name is provided in the first parameter. Valid formats are: </td>
  </tr>
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">3</td>
  <td valign="top">Windows NT® 4.0 account name (for example, digital_persona\klozin). The domainonly version includes trailing backslashes (\\).</td>  
  </tr>  
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">4</td>
  <td valign="top">Account name format used in Microsoft® Windows NT® 4.0. For example, "someone".</td>  
  </tr>
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">5</td>
  <td valign="top">GUID string that the IIDFromString function returns (for example, {4fa050f0-f561-11cfbdd9-00aa003a77b6}).</td>  
  </tr>  
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">7</td>
  <td valign="top">User Principal Name (UPN). For example, someone@mycompany.com.</td>  
  </tr>      
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">8</td>
  <td valign="top">User SID string (for example, S-1-5-21-1004).</td>  
  </tr>  
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">9</td>
  <td valign="top">DigitalPersona user name format (user name associated with DigitalPersona identity database).</td>  
  </tr>                
</table>

##### Return values
List of credential IDs of all credentials enrolled by a user. For full details on all supported credential IDs, see [this section](wes-cred-format.md).  

##### Examples  

~~~
https://www.somecompany.com/DPWebEnrollervice.svc/						GetUserCredentials?
user=john.doe@somecompany.com&type=6
~~~  

Here the user name is john.doe@somecompany.com, the user name type is 6 which means UPN.  

The example response would be the following:
~~~
{"GetUserCredentialsResult":
	[
		"D1A1F561-E14A-4699-9138-2EB523E132CC",
		"AC184A13-60AB-40e5-A514-E10F777EC2F9",
		"8A6FCEC3-3C8A-40c2-8AC0-A039EC01BA05"
	]
}
~~~  

This response means that the user john.doe@somecompany.com has password, fingerprint and PIN credentials enrolled.  

#### GetEnrollmentData method  

The GetEnrollmentData method is a utility method which allows the caller to get credential enrollment specific data.  

For example, Live Question authentication may require knowing which particular questions were enrolled, for fingerprint - fingerprint positions enrolled, etc.
GetEnrollmentData should be implemented as HTTP GET using JSON as response format.  

##### Syntax  

~~~  
String GetEnrollmentData(String userName, UInt16 userNameType, String credentialId);
~~~

<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:20%" ALIGN="left">Parameter</th>
    <th style="width:35%" ALIGN="left" colspan="2">Description</th>
  </tr>
  <tr>
  <td valign="top">username</td>
  <td valign="top" colspan="2">Name of the user whose enrollment data needs to be obtained.</td>
  </tr>
  <tr>
  <td valign="top">userNameType</td>
  <td valign="top" colspan="2">The specific format of the user name is provided in the first parameter. Valid formats are: </td>
  </tr>
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">3</td>
  <td valign="top">Windows NT® 4.0 account name (for example, digital_persona\klozin). The domainonly version includes trailing backslashes (\\).</td>  
  </tr>  
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">4</td>
  <td valign="top">Account name format used in Microsoft® Windows NT® 4.0. For example, "someone".</td>  
  </tr>
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">5</td>
  <td valign="top">GUID string that the IIDFromString function returns (for example, {4fa050f0-f561-11cfbdd9-00aa003a77b6}).</td>  
  </tr>  
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">7</td>
  <td valign="top">User Principal Name (UPN). For example, someone@mycompany.com.</td>  
  </tr>      
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">8</td>
  <td valign="top">User SID string (for example, S-1-5-21-1004).</td>  
  </tr>  
  <tr>
  <td valign="top">&nbsp;</td>
  <td valign="top">9</td>
  <td valign="top">DigitalPersona user name format (user name associated with DigitalPersona identity database).</td>  
  </tr>   
  <tr>
    <td valign="top">credentialId</td>
    <td valign="top" colspan="2">Unique ID of credential whose data needs to be returned. For full details on all supported credential IDs, see [this section](wes-cred-format.md).</td>
  </tr>                   
</table>

##### Return values
Base64Url encoded enrollment data. The format of such enrollment data is credential-specific and will be detailed in separate document(s).  

##### Example  

~~~
https://www.somecompany.com/DPWebEnrollService.svc/GetEnrollmentData?
user=john.doe@somecompany.com&type=6&cred_id=AC184A13-60AB-40e5-A514-E10F777EC2F9
~~~

Here the user name is john.doe@somecompany.com, the user name type is 6 which means UPN name and the credential ID is {AC184A13-60AB-40e5-A514-E10F777EC2F9}, which means information about a fingerprint credential was requested.  

##### Note  

The use of braces {} is considered unsafe in URLs (see RFC 1738), which is why "braceless" GUID representation is used in the API.
The example response would be the following:  

~~~
{"GetEnrollmentDataResult":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"}  
~~~

This response shows fingerprint enrollment data for the user john.doe@somecompany.com.

#### CreateUser method

The CreateUser method creates a user account in the DigitalPersona database. This method makes sense only if DigitalPersona is used as the  backend server. In DigitalPersona AD user account is created in Active Directory and Administrator must use standard Active Directory tools to create it.  

CreateUser should be implemented as HTTP PUT using JSON as the response format.

##### Syntax

~~~
void CreateUser(Ticket secOfficer, User user, String password);
~~~
<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:20%" ALIGN="left">Parameter</th>
    <th style="width:35%" ALIGN="left" colspan="2">Description</th>
  </tr>
  <tr>
  <td valign="top">secOfficer</td>
  <td valign="top" colspan="2">JSON Web Token of Security Officer. Security Officer should use the DigitalPersona Web AUTH Service to authenticate himself and acquire this token. Token must be valid to call succeeded. To be valid token must be:<BR><BR>1. Issued no longer than 10 minutes before the operation,<BR>2. One of the Primary credentials must be used to acquire this token and<BR>3. The token owner must have the necessary rights to create the user account in the DigitalPersona AD/LDS  database.</td>
  </tr>
  <tr>
  <td valign="top">user	</td>
  <td valign="top" colspan="2">User account that needs to be created. See the definition of the User class on page 68.</td>
  </tr>  
  <tr>
  <td valign="top">password</td>
  <td valign="top" colspan="2">String which represents the initial password for newly created user account. We cannot create user account without setting initial. Password must satisfy password complexity policy set for AD LDS database otherwise call will fail.</td>
  </tr>    
</table>

##### Example

~~~
https://www.somecompany.com/DPWebEnrollService.svc/CreateUser
~~~

Below is an example of an HTTP BODY of CreateUser request.

~~~
{
	"secOfficer":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
	{
		"name":"john.doe@somecompany.com",
		"type":6
	},
	"password":"aaaAAA123"
}
~~~

The call above creates an account in DigitalPersona (AD LDS) database for Active Directory user with UPN name john.doe@somecompany.com.
DeleteUser method
The DeleteUser method deletes a user account from the DigitalPersona database. This method makes sense only if DigitalPersona is used as the backend server. In DigitalPersona AD, the user account is deleted in Active Directory and an Administrator should use the standard Active Directory tools to do so.
DeleteUser should be implemented as HTTP DELETE using JSON as the response format.
Syntax
void DeleteUser(Ticket secOfficer, User user);

Parameter	Description
secOfficer	JSON Web Token of Security Officer. Security Officer should use the DigitalPersona Web AUTH Service to authenticate himself and acquire this token. Token must be valid to call succeeded. To be valid, a token must be:
Issued no longer than 10 minutes before the operation
One of the Primary credentials must be used to acquire this token and
The token owner must have a rights to create user account in DigitalPersona (AD LDS) database.
user	User account that needs to be deleted. See the definition of the User class on page 68.

Examples
Below is example of a URL that can be used to activate a DeleteUser request:
https://www.somecompany.com/DPWebEnrollService.svc/DeleteUser
Below is example of HTTP BODY of DeleteUser request:
{
	"secOfficer":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
	{
		"name":"john.doe@somecompany.com",
		"type":6
	}
}
The call above deletes an account from DigitalPersona (AD LDS) database for Active Directory user with UPN name john.doe@somecompany.com.
EnrollUserCredentials method
The EnrollUserCredentials method enrolls (or re-enrolls) specific credentials for a named user and stores their credential data in the DigitalPersona AD database. This method will work for both DigitalPersona AD and DigitalPersona LDS backend servers.
EnrollUserCredentials should be implemented as HTTP PUT using JSON as response format.
Syntax
void EnrollUserCredentials(Ticket secOfficer, Ticket owner, Credential credential);

Parameter	Description
secOfficer	JSON Web Token of Security Officer. Security Officer should use the DigitalPersona Web AUTH Service to authenticate himself and acquire this token. Token must be valid to call succeeded. To be valid, a token must be:
Issued no longer than 10 minutes before the operation
One of the Primary credentials must be used to acquire this token and
The token owner must have a rights to create user account in the DigitalPersona LDS or DigitalPersona AD database.
NOTE: This parameter is optional. If user has rights to enroll himself (self-enrollment allowed), caller may provide "null" to this parameter.
owner	JSON Web Token of the owner of credentials. User should use DigitalPersona Web AUTH Service to authenticate itself and acquire this token. Token must be valid to call succeeded. To be valid, a token must be:
Issued no longer than 10 minutes before the operation
One of the Primary credentials (or same credentials) must be used to acquire this token.
credential	Credential to be enrolled. Note that the Data field of this parameter is specific to each credential. See the definition of the Credential class on page 33 and following.

Examples
Below is an example of a URL which can be used to PUT EnrollUserCredentials request:
https://www.somecompany.com/DPWebEnrollService.svc/EnrollUserCredentials
Below is example of HTTP BODY of EnrollUserCredentials request:
{
	"secOfficer":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"owner":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"credential":
	{
		"id":"AC184A13-60AB-40e5-A514-E10F777EC2F9",
		"data":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"
	}
}
The call above enrolls fingerprint credentials for the user specified in the "owner" token.
DeleteUserCredentials method
DeleteUserCredentials method deletes (un-enrolls) specific credentials for a user and remove credential data from DigitalPersona database. This method will work for both DigitalPersona AD and DigitalPersona LDS backend servers.
DeleteUserCredentials should be implemented as HTTP DELETE using JSON as response format.
Syntax
void DeleteUserCredentials(Ticket secOfficer, Ticket owner, Credential credential);

Parameter	Description
secOfficer	JSON Web Token of Security Officer. Security Officer should use the DigitalPersona Web AUTH Service to authenticate himself and acquire this token. Token must be valid to call succeeded. To be valid token must be:
Issued no longer than 10 minutes before the operation
One of the Primary credentials must be used to acquire this token
The token owner must have a rights to create user account in the DigitalPersona LDS or DigitalPersona AD database.
NOTE: This parameter is optional. If user has rights to enroll himself (self-enrollment allowed), caller may provide "null" to this parameter.
owner	JSON Web Token of the owner of credentials. User should use DigitalPersona Web AUTH Service to authenticate itself and acquire this token. Token must be valid to call succeeded. To be valid token must be:
Issued no longer than 10 minutes before the operation
One of the Primary credentials (or same credentials) must be used to acquire this token.
credential	Credential to be deleted. Note that the Data field of this parameter is specific to each credential. See the definition of the Credential class on page 33 and following.

Example
Below is example of a URL which can be used to DELETE DeleteUserCredentials request:
https://www.somecompany.com/DPWebEnrollService.svc/DeleteUserCredentials
Below is example of HTTP BODY of DeleteUserCredentials request:
{
	"secOfficer":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"owner":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"credential":
	{
		"id":"AC184A13-60AB-40e5-A514-E10F777EC2F9",
		"data":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"
	}
}
The call above deletes any fingerprint credentials for the user specified in the "owner" token.
EnrollAltusUserCredentials method
The EnrollAltusUserCredentials method enrolls (or re-enrolls) specific credentials for specific user and store credential data in the DigitalPersona database. This method will work only for Non AD users and the DigitalPersona LDS Server backend. For AD users, the function will return Access Denied.
This method is different from EnrollUserCredentials because it allows enroll user credentials without user being previously authenticated. Only authentication of Security Officer is required. By default the DigitalPersona Server requires the user to be authenticated to enroll credentials so we introduce new GPO setting: AllowSecurityOfficerEnrollment. If this GPO is not configured or set to 0, EnrollAltusUserCredentials function will always return Access Denied error. If this GPO set to 1 and Security Officer has rights to enroll this particular user, enrollment will be performed.
Syntax
void EnrollAltusUserCredentials(Ticket secOfficer, User user, Credential credential);

Parameter	Description
secOfficer	JSON Web Token of Security Officer. Security Officer should use DigitalPersona Web AUTH Service to authenticate itself and acquire this token. Token must be valid to call succeeded. To be valid, a token must be:
Issued no longer than 10 minutes before the operation
One of the Primary credentials must be used to acquire this token
The token owner must have a rights to enroll user in the DigitalPersona LDS database.
user	User which credentials needs to be enrolled. Only Non AD users can be accepted by this function. See the definition of the User class on page 68.
credential	Credential to be deleted. Note that the Data field of this parameter is specific to each credential. See the definition of the Credential class on page 33 and following.

EnrollAltusUserCredentials should be implemented as HTTP PUT using JSON as response format.
Below is example of URL which can be used to PUT EnrollAltusUserCredentials request:
https://www.digitalpersona.com/DPWebEnrollService.svc/EnrollAltusUserCredentials
Below is example of HTTP BODY of EnrollAltusUserCredentials request:
{
	"secOfficer":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
		{
			"name":"someone",
			"type":9
		},
		"credential":
		{
			"id":"AC184A13-60AB-40e5-A514-E10F777EC2F9",
			"data":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"
		}
}
The call above enrolls fingerprint credentials for the Non AD user "someone".
NOTE: This method can be used to Reset a Non AD user’s password without user intervention.
NOTE: This method also can be used to Randomize user Password. To randomize user password caller must provide "null" in data parameter in "credential" class. Below is an example of Password Randomization request:
{
	"secOfficer":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
		{
			"name":"someone ",
			"type":9
		},
		 "credential":
		{
			"id":" D1A1F561-E14A-4699-9138-2EB523E132CC ",
			"data":null
		}
}
DeleteAltusUserCredentials method
DeleteAltusUserCredentials method deletes (un-enrolls) specific credentials for a specific Non AD user and removes credential data from the DigitalPersona database. This method will work only for Non AD users. For AD users it will return Access Denied.
This method is different from DeleteUserCredentials because it allows delete user credentials without user being previously authenticated. Only authentication of Security Officer is required. By default the DigitalPersona Server requires the user to be authenticated to delete credentials so we introduce new GPO setting: AllowSecurityOfficerEnrollment. If this GPO is not configured or set to 0, DeleteAltusUserCredentials function will always return Access Denied error. If this GPO set to 1 and Security Officer has rights to enroll this particular user, credential deletion will be performed.
Syntax
void DeleteAltusUserCredentials(Ticket secOfficer, User user, Credential credential);

Parameter	Description
secOfficer	JSON Web Token of Security Officer. Security Officer should use DigitalPersona Web AUTH Service to authenticate itself and acquire this token. Token must be valid to call succeed. To be valid, a token must be:
Issued no longer than 10 minutes before the operation
One of the Primary credentials must be used to acquire this token
The token owner must have a rights to enroll user in the DigitalPersona database (LDS version) or in Active Directory (AD version).
NOTE: This parameter is optional. If user has rights to enroll himself (self-enrollment allowed), caller may provide "null" to this parameter.
user	User whose credentials needs to be deleted. See the definition of the User class on page 68.
credential	Credential to be deleted. Note that the Data field of this parameter is specific to each credential. See the definition of the Credential class on page 33 and following.

DeleteAltusUserCredentials should be implemented as HTTP DELETE using JSON as response format.
Below is example of URL which can be used to DELETE DeleteAltusUserCredentials request:
https://www.digitalpersona.com/DPWebEnrollService.svc/DeleteAltusUserCredentials
Below is example of HTTP BODY of DeleteAltusUserCredentials request:
{
	"secOfficer":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
	{
		"name":"someone ",
		"type":9
  },
	"credential":
	{
		"id":"AC184A13-60AB-40e5-A514-E10F777EC2F9",
		"data":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"
	}
}
The call above delete fingerprint credentials for Non AD user "someone".
GetUserAttribute method
GetUserAttribute method request some public (biographic) information about user, like user surname, date of birth, e-mail address, etc.
GetUserAttribute should be implemented as HTTP POST using JSON as response format.
Syntax
Attribute GetUserAttribute(Ticket ticket, User user, String attributeName);

Parameter	Description
ticket	JSON Web Token of user who requests this information. This could be an attribute owner, Security Officer, Administrator or any user who has rights to read this information. Token must be valid to call succeed. To be valid token must be: 1) issued no longer than 10 minutes before the operation, 2) one of the Primary credentials must be used to acquire this token and 3) token owner must have a rights to read this attribute user in DigitalPersona (AD LDS) or DigitalPersona AD (Active Directory) database.
user	User which information requested. See the definition of the User class on page 68.
attributeName	Name of the information requested. Both AD and AD LDS are LDAP databases so this name must be Ldap-Display-Name of Attribute Schema in User object in LDAP database.

Return values
JSON representation of object of Attribute class will be returned if the call succeeds. For details on the Attribute class, see the topic Attribute class on page 29.
Examples
Below is an example of a URL which can be used to POST GetUserAttribute request.
https://www.somecompany.com/DPWebEnrollService.svc/GetUserAttribute
Below is example of HTTP BODY of GetUserAttribute request:
{
	"ticket":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
	{
		"name":"john.doe@somecompany.com",
		"type":6
	},
	"attributeName":"sn"
}
The call above requests "Surname" attribute for Active Directory user with UPN name john.doe@somecompany.com.
The example of return value of this call could be:
{"GetUserAttributeResult":
{
"type":3,
"values":["Lozin"]
}
}
PutUserAttribute method
The PutUserAttribute method writes, updates or cleara specific public data (attribute) for the named user. This method makes sense only for DigitalPersona as backend server (AD LDS), for Active Directory Administrator must use standard AD tools to manage attributes (with exception of DP specific attributes).
PutUserAttribute should be implemented as HTTP PUT using JSON as response format.
Syntax
void PutUserAttribute(Ticket ticket, User user, String attributeName,
		 AttributeAction action, Attribute attributeData);

Parameter	Description
ticket	JSON Web Token of user who requests attribute modification. This could be an attribute owner, Security Officer, Administrator or any user who has rights to write this information. Token must be valid to call succeed. To be valid token must be: 1) issued no longer than 10 minutes before the operation, 2) one of the Primary credentials must be used to acquire this token and 3) token owner must have a rights to write this attribute user in DigitalPersona (AD LDS) or DigitalPersona AD (Active Directory) database.
user	User whose attribute needs to be modified. See the definition of the User class on page 68.
attributeName	Name of the information requested. Both AD and AD LDS are LDAP databases so this name must be Ldap-Display-Name of Attribute Schema in User object in LDAP database.
action	Action that needs to be taken. It could be Append, Update, Delete or Clear. For additional information, see page 28.
attributeData	Attribute data that needs to be written. For details on the Attribute class, see the topic Attribute class on page 29.

Examples
Below is an example of URL which can be used to PUT PutUserAttribute request:
https://www.somecompany.com/DPWebEnrollService.svc/PutUserAttribute
Below is example of HTTP BODY of PutUserAttribute request:
{
	"ticket":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
	{
		"name":"john.doe@somecompany.com",
		"type":6
	},
	"attributeName":"sn",
	"action":2,
	"attributeData":
	{
		"type":3,
		"values":["Lozin"]
	}
}
The call above requests update of "Surname" attribute for Active Directory user with UPN name john.doe@somecompany.com. New value should be "Lozin".
Data Contracts
Below are the Data Contracts used in the Web Enrollment API. Only the data specific to Web Enrollment is described below. For a description of additional data contracts used in the ,Web Authentication Service API, see page 68 and following.
AttributeAction enumeration
AttributeAction enumeration specifies action which could be taken with attribute in PutUserAttribute call.
[DataContract]
public enum AttributeAction
{
	[EnumMember]
	Clear = 1,
	[EnumMember]
	Update = 2,
	[EnumMember]
	Append = 3,
	[EnumMember]
	Delete = 4,
}
Parameter	Description
clear	Attribute must be cleared. The attributeData argument of the PutUserAttribute method will be ignored and can be "null".
Update	Attribute will be updated. All previous data in the attribute will be cleared.
Append	The data will be appended to data which already exists in the attribute. Makes sense only for multivalued attributes.
Delete	The data provided in the attributeData argument of the PutUserAttribute method will be deleted from the attribute. Makes sense only for multivalued attributes.

AttributeType enumeration
AttributeType enumeration specifies type of attribute value(s).
DataContract]
public enum AttributeType
{
	[EnumMember]
	Boolean = 1,
	[EnumMember]
	Integer = 2,
	[EnumMember]
	String = 3,
	[EnumMember]
	Blob = 4,
}
Value	Description
Boolean	Attribute has Boolean value(s).
Integer	Attribute has Integer value(s).
String	Attribute has String value(s).
Blob	Attribute has Blob value(s).

Attribute class
Attribute class is Attribute representation in the Web Enrollment API.
[DataContract]
	public class Attribute
	{
		[DataMember]
		public AttributeType type { get; set; }
		[DataMember]
		public List<Object> values { get; set; }
	}

Parameters	Description
type	Type of Attribute value(s).
values	Values of attribute. We assume all attributes are multivalued because singlevalued attributes are a subsystem of multivalued attributes. Below we give details of Json representation of attributes of different types.

Boolean attributes
For Boolean attributes Json representation would be Json Boolean. Below is an example of "isDeleted" attribute in Active Directory:.
{
	"type":1,
	"values":[true]
}
The attribute above claims user is deleted from Active Directory.
Integer attributes
For Integer attributes Json representation would be Json Integer. We will use Json integer for all types of integers like Uin8, Uint16, Uint32 and Uint64. Timestamps will be also represented as long integers (Uint64). Below is an example of "userAccountControl" attribute in Active Directory.
{
	"type":2,
	"values":[65536]
}
The attribute above claims users’ password never expires.
String attributes
For String attributes Json representation would be Json String. Below is an example of "otherMailbox" (users' e-mail addresses) attribute in Active Directory.
{
	"type":3,
	"values":["john.doe@somecompany.com","john.doe@mycompany.com"]
}
The attribute above has all users e-mail addresses.
Blob attributes
For Blob attributes Json representation would be Json String. To convert Blob to a string, we should use Base64UrlEncoding. Below is an example of "thumbnailPhoto" attribute in Active Directory:
{
	"type":4,
	"values":["Z3NhZGhhc2Rma0FTREZLYWZyZGtB"]
}
The attribute above has Base64UrlEncoded users' thumbnail photo.
CustomAction method
CustomAction method performs credential specific operation (custom action) for specific user. CustomAction should be implemented as HTTP POST using JSON as response format. For further details, see CustomAction method on page 64.
Syntax
void CustomAction(Ticket ticket, User user, Credential credential, UInt16 actionId);
Parameters	Description
ticket	JSON Web Token of person who wants to perform CustomAction operation. This parameter is optional because not all CustomAction require Access Control so caller may provide "null" to this parameter.
user	User to whom CustomAction needs to be performed. This parameter is optional because not all CustomAction operations are performed to specific user so caller may provide "null" to this parameter.
credential
Credential to which CustomAction needs to be performed. Id attribute of Credential class should point to valid DigitalPersona Credential. Data attribute of Credential class should point to Base64Url encoded credential specific data and could be set to "null".
credential	Credential to which CustomAction needs to be performed. Id attribute of Credential class should point to valid DigitalPersona Credential. Data attribute of Credential class should point to Base64Url encoded credential specific data and could be set to "null".

Below is example of URL which can be used to POST CustomAction request:
https://www.digitalpersona.com/DPWebEnrollService.svc/CustomAction
Below is example of HTTP BODY of CustomAction request:
{
	"ticket":{"jwt":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"},
	"user":
	{
		"name":"someone",
		"type":9
	},
	"credential":
	{
		"id":"AC184A13-60AB-40e5-A514-E10F777EC2F9",
		"data":"Z3NhZGhhc2Rma0FTREZLYWZyZGtB"
	},
	"actionId":9
}
The call above send CustomAction request to fingerprint credentials with actionId 6 for DigitalPersona user "someone".
This call should return Base64Encoded output data of CustomAction call. The returned information is credential specific and should be provided "Web Authentication Service Credentials Data Format" document.
UnlockUser method
UnlockUser method performs self unlocking currently locked user.
Syntax
void UnlockUser(User user, Credential credential);

Parameters	Description
user	User whose account needs to be unlocked.
credential	A valid user credentials (we consider Live Questions credentials here) to be verified before the account gets unlocked.

UnlockUser should be implemented as HTTP POST using JSON as response format.
Below is example of URL which can be used to POST UnlockUser request:
https://www.digitalpersona.com/DPWebEnrollService.svc/UnlockUser
NOTE: To be able to self-unlock own account the following GPO must be enabled on AD (or LDS) server:
			Allow users to unlock their Windows account using DigitalPersona Recovery Questions.
