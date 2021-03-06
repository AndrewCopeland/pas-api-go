# pas-api-go
@CyberArk Privileged Access Security (PAS) REST API Client Library

[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=infamousjoeg_pas-api-go&metric=alert_status)](https://sonarcloud.io/dashboard?id=infamousjoeg_pas-api-go)

## Usage

### Command-Line Interface (CLI)

This is a work in progress...

#### Install from Source

```shell
$ git clone https://github.com/infamousjoeg/pas-api-go.git
$ ./install
$ cybr help
cybr is a command-line interface utility created by CyberArk that
wraps the PAS REST API and eases the user experience for automators
and automation to easily interact with CyberArk Privileged Access
Security.

Usage:
  cybr [command]

Available Commands:
  help        Help about any command
  version     Display current version

Flags:
  -h, --help   help for cybr

Use "cybr [command] --help" for more information about a command.
```

### Application

Full example available at [dev/main.go]().

#### Import into project

`github.com/infamousjoeg/pas-api-go/pkg/cybr/api`

```go
package main

import pasapi "github.com/infamousjoeg/pas-api-go/pkg/cybr/api"
```

#### Logon to the PAS REST API Web Service

```go
package main

import (
	"fmt"
	"log"
	"os"

	pasapi "github.com/infamousjoeg/pas-api-go/pkg/cybr/api"
)

var (
	hostname = os.Getenv("PAS_HOSTNAME")
	username = os.Getenv("PAS_USERNAME")
	password = os.Getenv("PAS_PASSWORD")
	authType = os.Getenv("PAS_AUTH_TYPE")
)

func main() {
	// Logon to PAS REST API Web Services
	token, errLogon := pasapi.Logon(hostname, username, password, authType, false)
	if errLogon != nil {
		log.Fatalf("Authentication failed. %s", errLogon)
	}
	fmt.Printf("Session Token:\r\n%s\r\n\r\n", token)
```

#### Call functions by referencing `pasapi` and "dot-referencing"

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"

	pasapi "github.com/infamousjoeg/pas-api-go/pkg/cybr/api"
)

// Declare variables (using Summon so they are env vars)
var (
	hostname = os.Getenv("PAS_HOSTNAME")
	username = os.Getenv("PAS_USERNAME")
	password = os.Getenv("PAS_PASSWORD")
	authType = os.Getenv("PAS_AUTH_TYPE")
)

// Start main function
func main() {
   // Verify PAS REST API Web Services
   // --> pasapi.ServerVerify is a "dot-reference"
	resVerify := pasapi.ServerVerify(hostname)
   // Marshal (convert) struct data into JSON format for pretty print
   // Otherwise, we "dot-reference" e.g. jsonVerify.ApplicationName would equal 'PasswordVault'
	jsonVerify, err := json.Marshal(&resVerify)
	if err != nil {
		log.Fatalf("Unable to marshal struct to JSON for verify. %s", err)
	}
   fmt.Printf("Verify JSON:\r\n%s\r\n\r\n", jsonVerify)
}
```

## Required Environment Variables

| Variable Name | Description |
| --- | --- |
| `PAS_HOSTNAME` | Base URL for PAS REST API Web Service |
| `PAS_USERNAME` | Username to authn to PAS REST API |
| `PAS_PASSWORD` | Password associated with `PAS_USERNAME` |
| `PAS_AUTH_TYPE` | Authentication method to use (cyberark or ldap) |

## Testing

1. Download and install [summon](https://cyberark.github.io/summon).
2. [OPTIONAL] Download and install a [summon provider](https://cyberark.github.io/summon/#providers).
   1. I use the `keyring` provider with [conceal](https://github.com/infamousjoeg/conceal).
3. Modify the values in [secrets.yml]() for your environment.
   1. If you did not complete the optional Step #2, you will use literal strings for `PAS_USERNAME` and `PAS_PASSWORD` similar to the values of `PAS_HOSTNAME` and `PAS_AUTH_TYPE`.
4. Run [main.go]() with the command: `summon go run main.go`.

### Successful Output

```shell
$ summon go run dev/main.go

Verify JSON:
{"ApplicationName":"PasswordVault","AuthenticationMethods":[{"Enabled":false,"Id":"windows"},{"Enabled":false,"Id":"pki"},{"Enabled":true,"Id":"cyberark"},{"Enabled":false,"Id":"oraclesso"},{"Enabled":false,"Id":"rsa"},{"Enabled":true,"Id":"radius"},{"Enabled":true,"Id":"ldap"},{"Enabled":true,"Id":"saml"}],"ServerId":"e00e8q16-b637-11e9-8329-ccd02f0167674","ServerName":"Vault"}

Session Token:
ZDRjNjNjNGItMVPjMS00MzRhLWIyNWMtYzI3MjMzZDFjNDg0OzNCRjk4NDEyMjEyNzgyOUI7MDAwMDAwMDJFN0ZERDcyNzJENUM3MkNDRjdBNUNDQ0UxQjY1QTYyMTkyMTlDQ0I0NTdGMjgxNDkxOTc1RTQxMjc1MkRFRTRFMDAwMDAwMDA7

List Safes JSON:
{"Safes":[{"SafeUrlId":"VaultInternal","SafeName":"VaultInternal","Location":"\\"},{"SafeUrlId":"Notification%20Engine","SafeName":"Notification Engine","Location":"\\"},{"SafeUrlId":"PVWAReports","SafeName":"PVWAReports","Location":"\\"},{"SafeUrlId":"PVWATicketingSystem","SafeName":"PVWATicketingSystem","Location":"\\"},{"SafeUrlId":"PVWAPublicData","SafeName":"PVWAPublicData","Location":"\\"},{"SafeUrlId":"PasswordManager","SafeName":"PasswordManager","Location":"\\"},{"SafeUrlId":"PasswordManager_Pending","SafeName":"PasswordManager_Pending","Location":"\\"},{"SafeUrlId":"AccountsFeedADAccounts","SafeName":"AccountsFeedADAccounts","Location":"\\"},{"SafeUrlId":"AccountsFeedDiscoveryLogs","SafeName":"AccountsFeedDiscoveryLogs","Location":"\\"},{"SafeUrlId":"D-Nix-Root","SafeName":"D-Nix-Root","Location":"\\"},{"SafeUrlId":"D-Win-DomainAdmins","SafeName":"D-Win-DomainAdmins","Location":"\\"},{"SafeUrlId":"D-Win-LocalAdmins","SafeName":"D-Win-LocalAdmins","Location":"\\"},{"SafeUrlId":"D-AWS-AccessKeys","SafeName":"D-AWS-AccessKeys","Location":"\\"},{"SafeUrlId":"D-Nix-AWSEC2-Keypairs","SafeName":"D-Nix-AWSEC2-Keypairs","Location":"\\"},{"SafeUrlId":"D-App-CyberArk-API","SafeName":"D-App-CyberArk-API","Location":"\\"},{"SafeUrlId":"D-MySQL-Users","SafeName":"D-MySQL-Users","Location":"\\"},{"SafeUrlId":"D-Nix-AWS-EC2","SafeName":"D-Nix-AWS-EC2","Location":"\\"},{"SafeUrlId":"PSMUniversalConnectors","SafeName":"PSMUniversalConnectors","Location":"\\"},{"SafeUrlId":"D-App-Docker-Registry","SafeName":"D-App-Docker-Registry","Location":"\\"},{"SafeUrlId":"D-Win-SvcAccts","SafeName":"D-Win-SvcAccts","Location":"\\"},{"SafeUrlId":"D-Nix-Root-2","SafeName":"D-Nix-Root-2","Description":"REST API Summit Toronto","Location":"\\"},{"SafeUrlId":"DemoSafe","SafeName":"DemoSafe","Location":"\\"},{"SafeUrlId":"Version1Safe","SafeName":"Version1Safe","Location":"\\"},{"SafeUrlId":"DemoSafe1","SafeName":"DemoSafe1","Location":"\\"},{"SafeUrlId":"Dummy-Safe","SafeName":"Dummy-Safe","Description":"Dummy safe","Location":"\\"}]}

List Safe Members JSON:
{"members":[{"Permissions":{"Add":true,"AddRenameFolder":true,"BackupSafe":true,"Delete":true,"DeleteFolder":true,"ListContent":true,"ManageSafe":true,"ManageSafeMembers":true,"MoveFilesAndFolders":true,"Rename":true,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":true,"Update":true,"UpdateMetadata":true,"ValidateSafeContent":true,"ViewAudit":true,"ViewMembers":true},"UserName":"jgarcia"},{"Permissions":{"Add":true,"AddRenameFolder":true,"BackupSafe":true,"Delete":true,"DeleteFolder":true,"ListContent":true,"ManageSafe":true,"ManageSafeMembers":true,"MoveFilesAndFolders":true,"Rename":true,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":true,"Update":true,"UpdateMetadata":true,"ValidateSafeContent":true,"ViewAudit":true,"ViewMembers":true},"UserName":"Master"},{"Permissions":{"Add":true,"AddRenameFolder":true,"BackupSafe":true,"Delete":true,"DeleteFolder":true,"ListContent":true,"ManageSafe":true,"ManageSafeMembers":true,"MoveFilesAndFolders":true,"Rename":true,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":true,"Update":true,"UpdateMetadata":true,"ValidateSafeContent":true,"ViewAudit":true,"ViewMembers":true},"UserName":"Batch"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":true,"Delete":false,"DeleteFolder":false,"ListContent":false,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":false,"Retrieve":false,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":false,"ViewMembers":false},"UserName":"Backup Users"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":false,"Retrieve":false,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"Auditors"},{"Permissions":{"Add":false,"AddRenameFolder":true,"BackupSafe":false,"Delete":false,"DeleteFolder":true,"ListContent":false,"ManageSafe":true,"ManageSafeMembers":false,"MoveFilesAndFolders":true,"Rename":false,"RestrictedRetrieve":false,"Retrieve":false,"Unlock":true,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":false,"ViewMembers":false},"UserName":"Operators"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":true,"Delete":false,"DeleteFolder":false,"ListContent":false,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":false,"Retrieve":false,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":false,"ViewMembers":false},"UserName":"DR Users"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":false,"Retrieve":false,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"Notification Engines"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":false,"Retrieve":false,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"PVWAGWAccounts"},{"Permissions":{"Add":true,"AddRenameFolder":true,"BackupSafe":false,"Delete":true,"DeleteFolder":true,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":true,"Rename":true,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":true,"Update":true,"UpdateMetadata":true,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":false},"UserName":"PasswordManager"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":true,"Retrieve":false,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":false},"UserName":"D-Win-DomainAdmin_Users"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":false,"Retrieve":false,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"D-Win-DomainAdmin_Auditors"},{"Permissions":{"Add":true,"AddRenameFolder":false,"BackupSafe":true,"Delete":true,"DeleteFolder":false,"ListContent":true,"ManageSafe":true,"ManageSafeMembers":true,"MoveFilesAndFolders":false,"Rename":true,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":true,"Update":true,"UpdateMetadata":true,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"D-Win-DomainAdmin_Admins"},{"Permissions":{"Add":true,"AddRenameFolder":true,"BackupSafe":true,"Delete":true,"DeleteFolder":true,"ListContent":true,"ManageSafe":true,"ManageSafeMembers":true,"MoveFilesAndFolders":true,"Rename":true,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":true,"Update":true,"UpdateMetadata":true,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"Vault Admins"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"AIMWebService"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"SlackBot"},{"Permissions":{"Add":false,"AddRenameFolder":false,"BackupSafe":false,"Delete":false,"DeleteFolder":false,"ListContent":true,"ManageSafe":false,"ManageSafeMembers":false,"MoveFilesAndFolders":false,"Rename":false,"RestrictedRetrieve":true,"Retrieve":true,"Unlock":false,"Update":false,"UpdateMetadata":false,"ValidateSafeContent":false,"ViewAudit":true,"ViewMembers":true},"UserName":"Prov_PASAAS-PVWA"}]}

List Applications JSON:
{"application":[{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"DemoApp","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"AIMWebService","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"SlackBot","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"Ansible","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"AD Automation","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"DockerRegistry","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"AnsibleCP","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"pyAIM","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"},{"AccessPermittedFrom":0,"AccessPermittedTo":24,"AllowExtendedAuthenticationRestrict":false,"AppID":"IDaptive","BusinessOwnerEmail":"","BusinessOwnerFName":"","BusinessOwnerLName":"","BusinessOwnerPhone":"","Description":"","Disabled":false,"ExpirationDate":"0001-01-01T00:00:00Z","Location":"\\Applications"}]}

List Application Authentication Methods JSON:
{"authentication":[{"AppID":"Ansible","AuthType":"certificateSerialNumber","AuthValue":"5e00000044f3868cc4ce21134c000000000004","authID":0},{"AppID":"Ansible","AuthType":"machineAddress","AuthValue":"12.233.234.444","authID":0},{"AppID":"Ansible","AuthType":"machineAddress","AuthValue":"54.142.78.106","authID":0},{"AppID":"Ansible","AuthType":"machineAddress","AuthValue":"54.235.118.11","authID":0},{"AppID":"Ansible","AuthType":"certificateattr","AuthValue":"","authID":0}]}

Successfully logged off PAS REST API Web Services.
```
