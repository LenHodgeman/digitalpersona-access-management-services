---
layout: default
title: WES Credential Format
---

## WES Credential Format

In the IDPWebEnroll interface (see IDPWebEnroll interface
<mark style="color:Red;"> on page 15)</mark> we define a number of methods for credential enrollment which have as an input parameter an object of the Credential class. This topic describes the format of the data member for each Credential supported by DigitalPersona.

- Fingerprint Credential
- Password Credential
- PIN Credential
- Recovery Questions Credential
- Proximity Card Credential
- Smart Card Credential
- Face Credential
- Contactless Card Credential
- U2F Device Credential

### Credential class  

The Credential class is defined as follows.

~~~
[DataContract]
public class Credential
{
	[DataMember]
	public String id { get; set; } // unique id (Guid) of credential
	[DataMember]
	public String data { get; set; } //  credential data
}
~~~  

The data member of the Credential class is different for each credential. For example, the data member for the Fingerprint Credential is different from the same member for the Password Credential.

#### Fingerprint Credential  

The following ID is defined for Fingerprint Credentials.

{AC184A13-60AB-40e5-A514-E10F777EC2F9}


<mark style="color:Red;">We described BioSample class will be used in description below in the chapter WAS Credentials Data Format on page 99.</mark>

##### GetEnrollmentDataResult  

To ask information about enrolled fingerprints, the client should send an IDPWebEnroll-> GetEnrollmentData request to the server. Below is an example of such a request.

~~~
https://www.mycompany.com/DPWebEnrollService.svc/GetEnrollmentData?
user=john.doe@yourdomain.com&type=6&cred_id=AC184A13-60AB-40e5-A514-E10F777EC2F9
~~~

The result of a successful GetEnrollmentData request should be a Base64url encoded UTF-8 representation of a list (array) of fingerprints (fingerprint positions) enrolled by the user. Below is the ANSI 381 list of valid fingerprint positions.


<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:27%" ALIGN="left">Fingerprint position		</th>
    <th style="width:5%" ALIGN="left">Value</th>
    <th style="width:2%" ALIGN="left"></th>
    <th style="width:27%" ALIGN="left">Fingerprint position</th>
    <th style="width:5%" ALIGN="left">Value</th>            
  </tr>
  <tr>
    <td valign="top">Unknown</td>
    <td valign="top">0  </td>
    <td valign="top"></td>
    <td valign="top"></td>
    <td valign="top"></td>
  </tr>
  <tr>
  <td valign="top">Right thumb</td>
  <td valign="top">		1</td>
  <td valign="top"></td>
  <td valign="top">Left thumb</td>
  <td valign="top">6</td>
  </tr>
  <tr>
  <td valign="top">Right index finger	</td>
  <td valign="top">	2
  	</td>
  <td valign="top"></td>
  <td valign="top">Left index finger</td>
  <td valign="top">		7		</td>
  </tr>
  <tr>
  <td valign="top">Right middle finger</td>
  <td valign="top">3</td>
  <td valign="top"></td>
  <td valign="top">Left middle finger</td>
  <td valign="top">8</td>
  </tr>
  <tr>
  <td valign="top">Right ring finger</td>
  <td valign="top">4</td>
  <td valign="top"></td>
  <td valign="top">Left ring finger</td>
  <td valign="top">9</td>
  </tr>
  <tr>
  <td valign="top">Right little finger</td>
  <td valign="top">5</td>
  <td valign="top"></td>
  <td valign="top">Left little finger</td>
  <td valign="top">10</td>
  </tr>    
</table>

The following GetEnrollmentDataResult indicates that the user has their right thumb, right index finger and left middle fingers enrolled.

~~~
[{"position":1},{"position":2},{"position":8}]
EnrollUserCredentials
~~~

The following class will represent enrollment sample for fingerprint credentials.

~~~
[DataContract]
public class BioEnrollment
{
	[DataMember]
	Byte position { get; set; } 																				// Bio sample (fingerprint) position
	[DataMember]
	List<BioSample> samples { get; set; } // Bio samples to enroll
}
~~~

<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:20%" ALIGN="left">Parameter</th>
    <th style="width:35%" ALIGN="left">Description</th>
  </tr>
  <tr>
  <td valign="top">position</td>
  <td valign="top">	 Fingerprint position to enroll. For a list of valid fingerprint positions, see the table on the previous page.
</td>
  </tr>
  <tr>
  <td valign="top">  samples</td>
  <td valign="top">	List BioSample with fingerprint data to enroll for such position. List could include from one BioSample to any BioSamples (see BioSample class <mark style="color:Red;">on page 99.</mark></td>
  </tr>
</table>

The following data members for BioSample could be provided for enrollment.

<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:20%" ALIGN="left">BioSampleData members	</th>
    <th style="width:35%" ALIGN="left">Description</th>
  </tr>
  <tr>
  <td valign="top">BioFactor</td>
  <td valign="top">	 Must be set to 0x0008 (Fingerprints).</td>
  </tr>
  <tr>
  <td valign="top">BioSampleFormat->FormatOwner</td>
  <td valign="top">Could be 51 (DigitalPersona) or 49 Neurotec. This parameter will be ignored if Fingerprint image provided as BioSample.</td>
  </tr>
  <tr>
  <td valign="top">BioSampleType</td>
  <td valign="top">Could be 0x01 (fingerprint image) or 0x04 (Fingerprint enrollment template).</td>
  </tr>
  <tr>
  <td valign="top">BioSamplePurpose</td>
  <td valign="top">Could be 0 (Any purpose) or 3 (purpose Enrollment).</td>
  </tr>
  <tr>
  <td valign="top">BioSampleEncryption</td>
  <td valign="top">Could be 0 (not encrypted) or 1 (XTEA encryption).</td>
  </tr>    
</table>

Below is an example of a JSON representation of BioEnrollment.

~~~
{"position":7, 														// Left index finger
	"samples":
	[{
		"Version":1,
		"Header":
		{
			"Factor":8, 											// Fingerprint
			"Format":
			{
				"FormatOwner":51, 										// DigitalPersona engine
				"FormatID":0
			},
			"Type":4,											// Fingerprint template
			"Purpose":3, 											// Enrollment purpose
			"Quality":-1,
			"Encryption":0											// Unencrypted
		},
		"Data":"eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9" // Base64url encoded 			Fingerprint Template
	}]
}
~~~

The sample below representS AN enrollment request to enroll A left index finger and provides a processed Fingerprint Template as enrollment data.  

The following steps will be needed to create Fingerprint Enrollment packet:

1. Create JSON representation of every BioSample.  
2. Combine those JSON representations in a JSON array ([]).  
3. Add fingerprint position to create JSON representation of BioEnrollment class as shown above.  
4. Base64Url encode the string you created in step #3.  
5. Set Fingerprint Credential ID as the id member and the string created in step #4 as the data member.
**Note**: If the user already has fingerprint data enrolled at this position, the old data will be replaced with new data. If the user does not have fingerprint data enrolled at this position, the new data will be added.

##### DeleteUserCredentials
To delete user fingerprint credentials you will need send a Base64Url encoded array of fingerprint positions you would like to delete. For example if you want to delete  "Right thumb", "Right index finger" and "Left middle finger, you would construct  the following JSON string.

~~~
[{"position":1},{"position":2},{"position":8}]
~~~
Then Base64Url encode it and present it as a data member of the credential argument of a DeleteUserCredentials call.  

If you would like to clear all of the user's fingerprint credentials, you should  provide null as a member of the credential argument of a DeleteUserCredentials call.  

For example:  

~~~
{"id":"AC184A13-60AB-40e5-A514-E10F777EC2F9",
"data":null}  
~~~

##### CustomAction  

CustomAction information is detailed in the  
<mark style="color:Red;">"Web Authentication Service Credentials Data Format" document</mark>.  

#### Password Credential  

The following ID is defined for the Password Credential.

~~~
{D1A1F561-E14A-4699-9138-2EB523E132CC}  
~~~

##### GetEnrollmentDataResult  

This call is not supported for the Password credential.  

##### EnrollUserCredentials
The following class is used to represent Password Enrollment credentials.

~~~
[DataContract]
	public class PasswordEnrollment
	{
		[DataMember]
		String oldPassword { get; set; } // Old password
		[DataMember]
		String newPassword { get; set; } // New password
	}
~~~

<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:20%" ALIGN="left">Parameter</th>
    <th style="width:35%" ALIGN="left">Description</th>
  </tr>
  <tr>
  <td valign="top">oldPassword </td>
  <td valign="top">	User's old password. This parameter is optional and can be null, in this case Password Reset operation would be used. If old password is presented, Password Change operation will be used and owner argument of EnrollUserCredentials could be null.</td>
  </tr>
  <tr>
  <td valign="top">newPassword</td>
  <td valign="top">	Users new password. Password must satisfy password policy otherwise this call will fail.
  NOTE: Password Reset operation is not supported for Active Directory users, so caller must provide old password to call succeed.</td>
  </tr>
</table>

The following is an example of a JSON representation of a Password Enrollment request.

~~~
{"oldPassword":"aaaAAA111","newPassword":"aaaAAA123"}
~~~

To send this enrollment request you need to Base64Url encode the JSON representation of Password Enrollment credentials as shown abovee and provide the obtained string as the data member of the credential argument of an EnrollUserCredentials call.

##### DeleteUserCredentials

This call is not supported for password credential.  

##### CustomAction  

CustomAction information is detailed in
<mark style="color:Red;">"Web Authentication Service Credentials Data Format" document</mark>.  

#### PIN Credential  

The following ID is defined for the PIN Credential.  

~~~
{8A6FCEC3-3C8A-40c2-8AC0-A039EC01BA05}
~~~

##### GetEnrollmentDataResult  

This call is not supported for the PIN credential.  

##### EnrollUserCredentials  

The following steps need to be done to create a PIN Enrollment Credential.

1. Base64url encode UTF-8 representation of PIN. NOTE: It is not necessary to include null terminating character to UTF-8 representation.  

2. Create a JSON representation to PIN credential setting PIN Credential ID as id member and string created in step #1 as data member.

  For example we need to create a JSON representation of the following PIN: 1234. The Base64url encoded UTF-8 representation of such PIN is: *MTIzNA*

3. Finally we can create a JSON representation of the Credential class which we can send for PIN authentication to the DigitalPersona Server.

~~~
{"id":"8A6FCEC3-3C8A-40c2-8AC0-A039EC01BA05",
"data":"MTIzNA"}
~~~

**Note**: If the PIN is already enrolled, it will be replaced with new data.

##### DeleteUserCredentials  

The data member of the credential argument of DeleteUserCredential should be null and will be ignored.  

##### CustomAction  

CustomAction information is detailed in
<mark style="color:Red;">"Web Authentication Service Credentials Data Format" </mark>document.  

#### Recovery Questions Credential

The following ID is defined for the Recovery Questions Credential.  

~~~
{B49E99C6-6C94-42DE-ACD7-FD6B415DF503}
~~~

##### GetEnrollmentData  

The result of a successful GetEnrollmentData call is detailed in the
<mark style="color:Red;">"Web Authentication Service Credentials Data Format" document</mark>.

##### EnrollUserCredentials  

The following class is used to represent Recovery Questions Enrollment data.


<mark style="color:Red;">Can we replace LiveQuestion with RecoveryQuestion?</mark>
~~~
[DataContract]
	public class LiveQuestionEnrollment
	{
		[DataMember]
		LiveQuestion question { get; set; } // Question information
		[DataMember]
		LiveAnswer answer { get; set; } // Answer information
	}
Below is an example of JSON representation of Live Questions Enrollment data:
{
	"question":
	{
		"version":1,
		"number":6,
		"type":0,
		"lang_id":9,
		"sublang_id":1,
		"keyboard_layout":1033,
		"text":"Who was your first employer?"
	},
	"answer":
	{
		"version":1,
		"number":6,
		"text":"DigitalPersona"
	}
}
~~~
The following steps will be needed to create Live Questions Enrollment packet.

1. Create JSON representation of every Question/Answer as shown above.  
2. Combine question/answer pairs JSON representation in a JSON array ([]).  
3. Base64Url encode the string you created in step #2.  

4. Set Live Question Credential ID as id member and string created in step #3 as data member.  

**Note**: We always replace Live Question data enrolled previously on the enrollment request. Merging of the data is not supported.

##### DeleteUserCredentials
The data member of the credential argument of DeleteUserCredential should be null and will be ignored. All Recovery Questions will be deleted by this request.

##### CustomAction  

CustomAction information is detailed in
<mark style="color:Red;">"Web Authentication Service Credentials Data Format" document</mark>.

#### Proximity Card Credential  

The following ID is defined for the Proximity Card Credential.

~~~
{1F31360C-81C0-4EE0-9ACD-5A4400F66CC2}
~~~

##### GetEnrollmentData  

We treat the Proximity Card Data (Prox Card ID) as an opaque blob.   

Follow these steps to create the Prox Card Credential.  

1. Base64url encode the Prox Card ID. The Card ID data must be a 64 byte long byte array, padded with zeroes if the Card ID is shorter than 64 bytes (as in the  iClass and MiFare cards). This 64 byte array must be Base64url encoded.
2. Create the JSON representation of the Credential class, setting the Proximity Card Credential ID as the id member and the string created in step #1 as the data member.

  For example, the client gets the Prox Card ID and it is represented by the following byte array:

  [123,34,116,121,112,34,58,34,74,87,84,34,44,10,32,34,97,108,103,34,58,
34,32,82,83,50,53,54,34,125]

  The Base64url encoded value of Prox Card ID is:  

  eyJ0eXAiOiJKV1QiLAogImFsZyI6IiBSUzI1NiJ9  

3. Then we create a JSON representation of the Credential class which we can send for Prox Card enrollment to the DigitalPersona Server.

~~~
{"id":"1F31360C-81C0-4EE0-9ACD-5A4400F66CC2",
"data":"eyJ0eXAiOiJKV1QiLAogImFsZyI6IiBSUzI1NiJ9"}
~~~
**Note**: If the Proximity Card is already enrolled, its data will be replaced with the new data.

##### DeleteUserCredentials  

The data member of the credential argument of the DeleteUserCredential should be null and will be ignored.  

##### CustomAction  

CustomAction information is specific to each credential. See CustomAction under each credential in the WAS Credentials Data Format topic
<mark style="color:Red;">beginning on page 96.</mark>  

#### Time-Based OTP (TOTP) Credential  

The following ID is defined for the TOTP Credential.

~~~
{324C38BD-0B51-4E4D-BD75-200DA0C8177F}
~~~

##### GetEnrollmentData  

The result of a GetEnrollmentData call is specific to each credential. See GetEnrollmentData under each credential in the WAS Credentials Data Format topic
<mark style="color:Red;">beginning on page 96.</mark>  

##### EnrollUserCredentials  

There are two types of OTP tokens supported.
- Software
- Hardware

Software tokens are found on smart phones and other mobile devices. Hardware tokens are found on spe4cificaly manufactured hardware. Enrollment data is different for software and hardware tokens.

###### Software OTP token enrollment  

The following class will represent an enrollment sample for TOTP credentials.

~~~
[DataContract]
public class OTPEnrollData
{
	[DataMember]
	public String otp{ get; set; }      // String with OTP verification code
	[DataMember]
	public String key { get; set; }     // String with Base64Encoded OTO key.
	[DataMember]
	public String phoneNumber { get; set; }     // User phone number..
}
~~~

<table style="width:95%;margin-left:auto;margin-right:auto;">
  <tr>
    <th style="width:20%" ALIGN="left">Parameter</th>
    <th style="width:35%" ALIGN="left">Description</th>
  </tr>
  <tr>
  <td valign="top">otp</td>
  <td valign="top">	 String with OTP verification code. If OTP code cannot be verified, the enrollment operation will fail with the following error: "The operation being requested was not performed because the user has not been authenticated.". <BR><BR>Note that  verification can fail for one of two reasons. Either the user mistyped the OTP code or the clocks on the phone and the DigitalPersona Server are not synchronized.</td>
  </tr>
  <tr>
  <td valign="top">key</td>
  <td valign="top">	Bease64Url Encoded TOTP key.
  </td>
  </tr>
  <tr>
  <td valign="top">phoneNumber</td>
  <td valign="top">	User's phone number. This parameter is required to support SMS OTP logon option and if SMS OTP support is not required can be omitted or set to "null".</td>
  </tr>
</table>

The following steps are needed to create a  TOTP Enrollment Credential.

1. Base64Url encode the TOTP Key.  We support a TOTP Key of any length, but at least a 160 bit TOTP Key is suggested for security reasons.  
2. Create a JSON representation of the  OTPEnrollData class where the otp assign OTP verification code typed by user and  key is string created in step #1.
Base64Url encode string created in step #2.
Create JSON representation to Credential class setting TOTP Credential ID as id member and string we created in step #3 as data member.
For example client gets TOTP Key and it could be represented by following byte array:
[123,34,116,121,112,34,58,34,74,87,84,34,44,10,32,34,97,108,103,34,58,
34,32,82,83,50,53,54,34,125]
The Base64url encoded value of TOTP Key is:
eyJ0eXAiOiJKV1QiLAogImFsZyI6IiBSUzI1NiJ9
User types the following verification code:
123456
JSON representation of TOTP enrolment data would be the following:
{
"otp":":"123456",
"key":":"eyJ0eXAiOiJKV1QiLAogImFsZyI6IiBSUzI1NiJ9"
}
Finally by Base64Url encoding string above we can create a JSON representation of Credential class which we can send for TOTP enrollment to the DigitalPersona Server:
{"id":"324C38BD-0B51-4E4D-BD75-200DA0C8177F",
"data":"eyahsadHKJjahdsdjlaJHKHLhalkdhsj1298475JHLHGLglagd"}
NOTE: If TOTP is already enrolled, it will be replaced with new data.
Hardware OTP token enrollment
The following class will represent enrollment sample for TOTP credentials:
[DataContract]
public class OTPHDEnrollData
{
	[DataMember]
	public String otp{ get; set; }      // String with OTP verification code
	[DataMember]
	public String serialNumber{ get; set; } // String with hardware token
																							 manufacturing serial number.
}

Parameter	Description
otp	 String with OTP verification code. If OTP code cannot be verified, enrollment operation will fail with the following error: "The operation being requested was not performed because the user has not been authenticated.". NOTE: verification can fail only for two reasons:
The user mistyped OTP code
The clocks on the phone and the DigitalPersona Server are not synchronized.
serialNumber	Hardware token serial number. This serial number was assign to the token during manufacturing and usually written on the token itself.

The following steps are needed to create TOTP Enrollment Credential:
Create JSON representation of OTPHDEnrollData class where to otp assign OTP verification code typed by user and  serialNumber is token serial number also typed by user.
Base64Url encode string created in step #1.
Create JSON representation to Credential class setting TOTP Credential ID as id member and string we created in step #2 as data member.
For example user types the following verification code:
123456
And the following serial number:
2608513503936
JSON representation of TOTP enrolment data would be the following:
{
"otp":":"123456",
"serialNumber":":"2608513503936"
}
Finally by Base64Url encoding string above we can create a JSON representation of Credential class which we can send for TOTP enrollment to the DigitalPersona Server:
{"id":"324C38BD-0B51-4E4D-BD75-200DA0C8177F",
"data":"eyahsadHKJjahdsdjlaJHKHLhalkdhsj1298475JHLHGLglagd"}
NOTE: If TOTP is already enrolled, it will be replaced with new data.
DeleteUserCredentials
The data member of credential argument of DeleteUserCredential should be null and will be ignored.
CustomAction
CustomAction informationis specific to each credential. See CustomAction under each credential in the WAS Credentials Data Format chapter beginning on page 96.
Smart Card Credential
The following ID is defined for Smart Card Credential:
{D66CC98D-4153-4987-8EBE-FB46E848EA98}
GetEnrollmentData
This method is not supported for the Smart Card credential.
EnrollUserCredntials
The data for smart card credential is a Base64url encoded UTF-8 representation of the JSON representation of the CDPJsonSCEnrollData class. CDPJsonSCEnrollData class is defined as following:
[DataContract]
public class CDPJsonSCEnrollData
{
	[DataMember]
	public Byte version { get; set; }    // version
	[DataMember]
	public String key { get; set; }      // public key
	[DataMember]
	public String nickname { get; set; } // token's nickname
}
where:
Parameter	Description
Byte version	Version of CDPJsonSCEnrollData object, must be set to 1 for current implementation
String key	Public key, Base64url UTF-8 encoded string. The public key from the Smart Card must be imported in the PUBLICKEYBLOB format. After that, it must be Base64url encoded to the string as shown below.
Key = Base64urlEncode( (PUBLICKEYBLOB (PuK) )
String nickname	The nickname of the smart card token. It's suggested to use the card name, like "SmartCafe Expert 72K DI v3.2", as a part of the nickname. The server limits the length of the nickname to 255 symbols, the exceeding symbols will be cut off.

To create the Smart Card Credential for enrollment, following steps must be performed on the client:
Enumerate the asymmetric key pairs on the Smart Card and select the exact key pair to use. WASSC supports RSA keys of any length but at least 1024 bit length key is suggested for security reason.
Create a JSON representation of the CDPJsonSCEnrollData class for the public key selected in step #1 above.
Base64Url encode string created in step #2 above.
Finally, create a JSON representation of the Credential class using Smart Card Credential ID as id member and string created in step #3 as a data member.
DeleteUserCredentials
To delete the particular Smart Card credential, the input data for this call must be a string presenting the Smart Card Public key's hash, calculated or received from the server as described in the chapter Web Smart Card support on page 153.
Parameter	Description
String keyHash	Public key's hash, Base64url UTF-8 encoded string. The public key from the Smart Card must be imported in the PUBLICKEYBLOB  format. After that, the RSA256 hash  of the key must be calculated. The resulting 32 bytes must be Base64url encoded to the string:
KeyHash	Base64urlEncode(RSA256 Hash (PUBLICKEYBLOB (PuK))

To delete all the Smart Card credentials enrolled for particular user, the input data for this call must be an empty string (not a NULL pointer).
CustomAction
CustomAction information is specific to each credential. See CustomAction under each credential in the WAS Credentials Data Format chapter beginning on page 96.
Face Credential
The following ID is defined for Face Credentials.
{85AEAA44-413B-4DC1-AF09-ADE15892730A}
One of the following third-party SDKs can be used to support the Face Credential in your application.
Cognitec FVSDK ver. 9.1.0
Innovatrics IFace SDK ver. 3.1.0
All the DigitalPersona Servers and Clients in the environment must use the same SDK. Enrollment data is not compatible between the SDKs!
The BioSample class used in description below is declared in "Altus 1 1 WAS Credentials Format.docx"
EnrollUserCredentials
The data for Face credential enrollment is a Base64url encoded UTF-8 representation of the JSON array, containing BioSample objects(s).
The following data members for BioSample should be provided for authentication.
Data member	Description
BioFactor	Must be set to 2 (DP_BIO_FACTOR::FACIAL_FEATURES)
BioSamplePurpose	Must be set to 0 (Any purpose) or 3 (purpose Enroll)
BioSampleEncryption	Must be 0 (not encrypted) or 1 (XTEA encryption)
BioSampleType	Must be one of the following:
	DP_BIO_SAMPLE_TYPE::PROCESSED
	DP_BIO_SAMPLE_TYPE::RAW

The following types of BioSampleType are supported for Face credential enrollment.
DP_BIO_SAMPLE_TYPE::PROCESSED image
Indicates a face template in one of the following internal SDK formats.
	Cognitec FIR format;
	Innovatrics Template format.
When the input credential data is already a face template (type 4), then only one BioSample object is expected in the array.
Data member	Description
BioSampleType	Must be 4.
BioSampleFormat->FormatOwner	"Organization Identifier" number from the International Biometrics Identity Association.
0x63 (99) for Cognitec
0x35 (53) for Innovatrics
BioSampleEncryption	Must be 0 (not encrypted) or 1 (XTEA encryption)
Data	Base64url encoded JSON representation of the CDPJsonFIR class described below:

[DataContract]
public class CDPJsonFIR
{
	[DataMember]
	public Byte Version { get; set; }    // version
	[DataMember]
	public long SDKVersion { get; set; } // SDK version
	[DataMember]
	public String Data { get; set; }     // FIR object
}
where:
Data member	Description
Byte Version	Specifies the version of the CDPJsonFIR object. It must be set to 1.   
ULONG SDKVersion	Specifies the version of the SDK:
Cognitec FVSDK, must be not less than 0x90100 (ver. 9.1.0).
Innovatrics IFace SDK, must be not less than 0x30100 (ver. 3.1.0).
String Data 	Contains a Base64url encoded BYTE array.
Cognitec SDK: this BYTE array is a serialized Cognitec FIR object, created using FIR::writeTo() method.
Innovatrics SDK: this BYTE array is a Template object, created using IFACE_CreateTemplate function.


If BioSampleEncryption is set to 1 (XTEA encryption), then this data is encrypted.
Below is an example of JSON representation of the CDPJsonFIR object containing the Cognitec FIR object.
{
	"Version":1,
	"SDKVersion":?590080??, //? ?0x90100?, ver. 9.1.0           				
	"Data":"2lvbiI6MSwibnVtYmVyIjoyLCJ0ZXh0IjoiTmV3IFlvcmsifSx7InZlcnNpb24iOj
	EsIm51bWJlciI6NiwidGV4dCI6IkNhbnlvbiBNaWRkbGUifSx7InZlcnNpb24iOjEsIm51bW
	JlciI6MTAyLCJ0ZXh0IjoiMDQvMjQvMjAwOSJ9XQ" // Base64url encoded 	serialized FIR object
       }
Example of JSON representation of the enrollment array containing the face FIR template:
{
	[{
	"Version":1,
	"Header":
	{
		"Factor":2, 											// Facial features
		"Format":
		{
			"FormatOwner":99,										// Cognitec
			"FormatID":0
		},
		"Type":4,	 										// Face template
		"Purpose":3, 	 										// Enrollment
		"Quality":-1,
		"Encryption":0 											// Unencrypted
	},
		"Data":"eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9" // Base64url encoded
		CDPJsonFIR object
	}]
}
DP_BIO_SAMPLE_TYPE::RAW image
Indicates a raw face image. It's recommended to have a minimum of ten BioSample objects containing raw images in the authentication array for successful verification.
The only type of raw image supported by the current version is a jpeg file.
Data member	Description
BioSampleType	Must be 1.

BioSampleFormat->FormatOwner 	Not used, must be 0.
Data	Base64url encoded JSON representation of the CDPJsonFaceImage class described below.

[DataContract]
public class CDPJsonFaceImage
{
	[DataMember]
	public Byte Version   { get; set; }  // version
	[DataMember]
	public DP_FACE_IMAGE_TYPE ImageType  { get; set; } // type of image
	[DataMember]
	public String ImageData { get; set; } // face image
}
where:
Data member	Description
Byte Version 	version of CDPJsonFaceImage object, must be set to 1.
DP_FACE_IMAGE_TYPE ImageType	type of image. Must be set to 1, JPEG_FILE:

typedef enum DP_FACE_IMAGE_TYPE
{
   JPEG_FILE = 1, // Base64Url encoded JPEG file
}
DP_FACE_IMAGE_TYPE;
String ImageData - contains Base64Url encoded raw image data, according to the ImageType. In the current version, it's a jpeg file. If BioSampleEncryption is set to 1 (XTEA encryption), this data is encrypted.
Below is an example of JSON representation of the CDPJsonFaceImage object containing the raw face image (jpeg file):
{
	"Version":1,
	"ImageType":1,											// JPEG_FILE
	"ImageData":"W3sidmVyc2lvbiI6MSwibnVtYmVyIjoyLCJ0ZXh0IjoiTmV3IFlvcmsifSx7
	InZlcnNpb24iOjEsIm51bWJlciI6NiwidGV4dCI6IkNhbnlvbiBNaWRkbGUifSx7InZlcnNp
	b24iOjEsIm51bWJlciI6MTAyLCJ0ZXh0IjoiMDQvMjQvMjAwOSJ9XQ" // Base64url
	encoded jpeg file
       }
Example of JSON representation of the authentication array containing the raw face images (jpeg files).
{
	[{
		"Version":1,
		"Header":
		{
			"Factor":2, 	// Facial features
			"Format":
			{
			"FormatOwner":0, // Not used
			"FormatID":0
			},
			"Type":1,	// Raw image
			"Purpose":1, 	// Enrollment
			"Quality":-1,
			"Encryption":0 // Unencrypted
		},
		"Data":"WF0T3duZXIiOjUxLA0KIkZvcm1hdElEIjowDQp9LA0KIlR5cGUiOjIsDQoiUHVycG9z
…
…
		ZSI6MCwNCiJRdWFsaX" // Base64url encoded CDPJsonFaceImage object
		},
…
…
		{
		"Version":1,
		"Header":
		{
			"Factor":2, 	// Facial features
			"Format":
			{
			"FormatOwner":0, // Not used
			"FormatID":0
			},
			"Type":1,	// Raw image
			"Purpose":1, 	// Enrollment
			"Quality":-1,
			"Encryption":0 // Unencrypted
		},
	"Data":"SGVhZGVyIjp7IkRldmljZUlkIjowLCJEZXZpY2VUeXBlIjo0OTI2NDQxNzM0NzI3Mjc
  …
  …
	wNCwiaURhdGFBY3F1a" // Base64url encoded CDPJsonFaceImage object
}]
}
The following steps will be needed to create the Face credential enrollment packet.
Create JSON representation of BioSample(s).
Combine those JSON representations in a JSON array ([]).
Base64Url encode the string created in step #2.
Create a JSON representation of the Credential class using Face Credential ID as id member and a string created in step #3 as a data member.
NOTE: If user already has the face credentials enrolled at this position, then the old credential data will be replaced with new one. If user does not have the face credentials enrolled at this position, then the face credentials will be added to the user record.
DeleteUserCredentials
The data member of credential argument of DeleteUserCredential should be null and will be ignored. For example:
{"id":"85AEAA44-413B-4DC1-AF09-ADE15892730A","data":null}
GetEnrollmentDataResult
This method is not supported.
CustomAction
CustomAction information is specific to each credential. See CustomAction under each credential in the WAS Credentials Data Format chapter beginning on page 96.
Contactless Card Credential
The following ID is defined for Contactless Card Credential:
{F674862D-AC70-48ca-B73E-64A22F3BAC44}
GetEnrollmentData
This method is not supported by the Contactless Card credential.
EnrollUserCredentials
The data for the Contactless Card credential is a Base64url encoded UTF-8 representation of the JSON CDPJsonCLCEnrollData class, which is defined as the following:
[DataContract]
	public class CDPJsonCLCEnrollData
	{
		[DataMember]
		public Byte version { get; set; } 																			// version
		[DataMember]
		public String key { get; set; }   																			// symmetric key
		[DataMember]
		public String nickname { get; set; } // token’s nickname
		[DataMember]
		public String UID { get; set; }   																			// card UID
		[DataMember]
		public int32 address { get; set; }   // address of card record
	}
where:
Data member	Description
Byte Version 	version of CDPJsonCLCEnrollData object, must be set to 1.
String key 	Card's symmetric key, presented as a Base64Url UTF-8 encoded string. The key must be imported in the PLAINTEXTKEYBLOB format. After that, it must be Base64url encoded to the string:
Key = Base64urlEncode( (PLAINTEXTKEYBLOB (Ps) )
String nickname	The nickname of the smart card token. It's suggested to use the card name, like "iClass ISO 14443 A", as a part of the nickname. The server limits the length of the nickname to 255 symbols, the exceeding symbols will be cut off.
String UID	Card's unique ID, array of 64 bytes, presented as Base64url UTF-8 encoded string.
int32 address	A DWORD containing the information on the physical address of the DP CA data on the card. This information will be used to speed up the card access.

To create the Contactless Card Credential for enrollment, following steps must be performed on the client:
Read the card UID and the symmetric key stored on the Contactless card, create the SHA 256 hash of this key.
Use the key hash as a TOTP seed, generate the OTP.
Create a JSON representation of the CDPJsonCLCAuthToken class.
Use the OTP string created in step #2;
Base64url UTF-8 encode the card UID;
Base64Url encode the JSON representation of the CDPJsonCLCAuthToken class;
Create a JSON representation of the Credential class using Contactless Card Credential ID as id member and string created in step #4 as a data member.
DeleteUserCredentials
The data member of credential argument of DeleteUserCredential should be null and will be ignored.
CustomAction
CustomAction is not currently supported for the Contactless Card credential.
U2F Device Credential
The following ID is defined for the U2F Credential.
{5D5F73AF-BCE5-4161-9584-42A61AED0E48}
GetEnrollmentData
This method is not supported.
EnrollUserCredentials
The data for U2F Device credential is a Base64url encoded UTF-8 representation of the JSON representation of the CDPJsonU2FEnrollData class. CDPJsonU2FEnrollData class is defined as following:
[DataContract]
	public class CDPJsonU2FEnrollData
	{
		[DataMember]
		public String version { get; set; }
		[DataMember]
		public String appId { get; set; }
		[DataMember]
		public String serialNum { get; set; }
		[DataMember]
		public String registrationData { get; set; }
		[DataMember]
		public String clientData { get; set; }
	}
where:
Data member	Description
String version	 - version of supported FIDO standard, must be set to "U2F_V2" for current implementation;   
String appId	 - facet ID of the caller.    
String serialNum	 - U2F Device serial number.
String registrationData	 - registration response from U2F device, Base64url UTF-8 encoded string.
String clientData 	- Base64Url encoded client data (see https://fidoalliance.org/specs/fido-u2f-v1.0-nfc-bt-amendment-20150514/fido-u2f-raw-message-formats.html for details). Client data contains challenged.


DeleteUserCredentials
The data member of credential argument of DeleteUserCredential should be null and will be ignored.
CustomAction
None? No entry in document for this cred.
