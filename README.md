# HelloID-Conn-SA-Full-EntraID-Remove-Intune-Device
| This repository contains the connector and configuration code only. The implementer is responsible for acquiring the connection details such as username, password, certificate, etc. You might even need to sign a contract or agreement with the supplier before implementing this connector. Please contact the client's application manager to coordinate the connector requirements. |

## Description
_HelloID-Conn-SA-Full-EntraID-Remove-Intune-Device_ is a delegated form template designed for use with HelloID Service Automation (SA) Delegated Forms. It can be imported into HelloID and customized according to your requirements.

By using this delegated form, you can remove Intune managed devices from Entra ID user accounts. The following options are available:
 1. Search and select an Entra ID user (wildcard search by username or email address)
 2. View user's basic attributes
 3. View user's Intune managed devices
 4. Select devices to remove from the user's account
 5. Device ownership links are removed in Microsoft Entra ID

## Getting started

### Requirements

#### App Registration & Certificate Setup

Before implementing this connector, make sure to configure a Microsoft Entra ID, an App Registration. During the setup process, you’ll create a new App Registration in the Entra portal, assign the necessary API permissions (such as user and group read/write), and generate and assign a certificate.

Follow the official Microsoft documentation for creating an App Registration and setting up certificate-based authentication:
- [App-only authentication with certificate (Exchange Online)](https://learn.microsoft.com/en-us/powershell/exchange/app-only-auth-powershell-v2?view=exchange-ps#set-up-app-only-authentication)

#### HelloID-specific configuration

Once you have completed the Microsoft setup and followed their best practices, configure the following HelloID-specific requirements.

- **API Permissions** (Application permissions):
  - `User.Read.All` - To read user information
  - `DeviceManagementManagedDevices.ReadWrite.All` - To read and manage Intune devices
  - `Device.ReadWrite.All` - To remove device ownership links
- **Certificate:**
  - Upload the public key file (.cer) in Entra ID
  - Provide the certificate as a Base64 string in HelloID. For instructions on creating the certificate and obtaining the base64 string, refer to our forum post: [Setting up a certificate for Microsoft Graph API in HelloID connectors](https://forum.helloid.com/forum/helloid-provisioning/5338-instruction-setting-up-a-certificate-for-microsoft-graph-api-in-helloid-connectors#post5338)


#### Connection settings

The following user-defined variables are used by the connector.

| Setting                        | Description                                                              | Mandatory |
| ------------------------------ | ------------------------------------------------------------------------ | --------- |
| EntraIdTenantId                | The unique identifier (ID) of the tenant in Microsoft Entra ID           | Yes       |
| EntraIdAppId                   | The unique identifier (ID) of the App Registration in Microsoft Entra ID | Yes       |
| EntraIdCertificateBase64String | The Base64-encoded string representation of the app certificate          | Yes       |
| EntraIdCertificatePassword     | The password associated with the app certificate                         | Yes       |

## Remarks

### User Search
- **Wildcard Search**: Users can search for Entra ID users using a wildcard (`*`) to return all users, or by entering partial text to search by username or email address. The search returns key user attributes including Display Name, User Principal Name, Department, Title, and Account Enabled status.

### Intune Managed Devices
- **Device Listing**: The form displays all Intune managed devices associated with the selected user, retrieved via the Microsoft Graph API `managedDevices` endpoint.
- **Device Selection**: Multiple devices can be selected for removal using a dual-list selector interface.

### Device Removal Process
- **Ownership Link Removal**: The connector removes the device ownership link by deleting the reference in the user's `ownedDevices` navigation property. This operation uses the Microsoft Graph API DELETE method on the `$ref` endpoint.
- **Error Handling**: If a device cannot be removed (e.g., device no longer exists or user no longer owns it), the operation logs the failure and continues with remaining devices.

### Certificate-Based Authentication
- **JWT Token Generation**: The connector uses certificate-based authentication to generate JSON Web Tokens (JWT) for secure communication with Microsoft Graph API. The certificate is converted from a base64 string and used to sign the JWT assertion for OAuth2 authentication.

## Development resources

### API endpoints

The following Microsoft Graph API endpoints are used by the connector:

| Endpoint                                    | Description                        |
| ------------------------------------------- | ---------------------------------- |
| /v1.0/users                                 | Search and list users              |
| /v1.0/users/{id}                            | Get specific user details          |
| /v1.0/users/{upn}/managedDevices            | List user's Intune managed devices |
| /v1.0/users/{id}/ownedDevices/{deviceId}/$ref | Remove device ownership link    |

### API documentation

- [List users](https://learn.microsoft.com/en-us/graph/api/user-list)
- [Get user](https://learn.microsoft.com/en-us/graph/api/user-get)
- [List user's managed devices](https://learn.microsoft.com/en-us/graph/api/user-list-manageddevices)
- [Remove device ownership reference](https://learn.microsoft.com/en-us/graph/api/device-delete-registeredowners)


## Getting help

> :bulb: **Tip:**  
> For more information on Delegated Forms, please refer to our documentation pages: https://docs.helloid.com/en/service-automation/delegated-forms.html


## HelloID docs
The official HelloID documentation can be found at: https://docs.helloid.com/