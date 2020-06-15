
Here is how to test Casa. Any findings should be reported with relevant logs attached ... used TRACE level wherever possible 

installation

Choose to install casa with accompanying oxd in Gluu Server installation menu (or add Casa post-install)
systemctl status casa shows a successful running service
systemctl status oxd-server shows a successful running service
casa.log shows no error statements
Database attribute oxConfApplication is populated (dn ou=casa, ou=configuration, o=gluu)
Attribute oxConfApplication has properties authz_redirect_uri/post_logout_uri/frontchannel_logout_uri 
Attribute oxConfApplication has a client section
A script named casa appears enabled in person authentication tab (oxtrust)
A script named casa_client_registration appears enabled in client registration tab (other scripts)
An OIDC client named gluu-casa_* was registered and 
	- has scopes oxd, openid, profile, user_name, clientinfo
	- Pre-Authorization is checked 
The client has no expiration or expires at least in 10 years
Directory `/opt/gluu/python/libs` has 6 .py scripts starting with "casa"
jetty user is able to copy/replace files in /opt/gluu/python/libs
Directory /opt/gluu/jetty/oxauth/custom/lib contains twilio and jsmpp jar files


uninstallation

Run `casa_cleanup.py` found in `/install/community-edition`
Successful messages are shown
Directory `/opt/gluu/jetty/casa` does not exist
systemctl status casa shows the service does not exist
ou=casa, ou=configuration, o=gluu was removed from database
The 2 casa scripts (person authentication & client registration) were removed
The gluu-casa client was removed
The casa scripts in `/opt/gluu/python/libs` were removed

Service survival
Run systemctl stop casa
In a browser hit /casa. It outputs 503
Run systemctl stop oxd-server
netstat shows there are no apps running at ports 8443, 8444, and 8099
Run systemctl start oxd-server
Run systemctl start casa
Wait a couple of minutes
In a browser hit /casa/health-check. An OK message is displayed
Restart VM and wait several minutes
In a browser hit /casa/health-check. An OK message is displayed

Auth flows with 2nd app

(I)
In a browser hit /casa and login
In a new tab hit /oxtrust. No prompt for username is shown
Logout from oxtrust, then from casa
Sucessful pages for logout are shown in both cases

(II)
In a browser hit /casa and login
In a new tab hit /oxtrust. No prompt for username is shown
Logout from casa, then from oxtrust
Sucessful pages for logout are shown in both cases

(III)
In a browser hit /oxtrust and login
In a new tab hit /casa. The prompt for username is shown
Logout from oxtrust, then from casa
Sucessful pages for logout are shown in both cases

(IV)
In a browser hit /oxtrust and login
In a new tab hit /casa. The prompt for username is shown
Logout from casa, then from oxtrust
Sucessful pages for logout are shown in both cases


Admin features

run touch /opt/gluu/jetty/casa/.administrable

Log level

In a browser hit /casa and login with admin user
Go to Administration Console > Log Level
Click on TRACE. A success message is shown

CORS domains

Go to Administration Console > CORS domains
Enter a dummy domain preceded by protocol (http/https) and click add
The domain entered appears in the list
Enter a new different domain and click add
The domain entered appears in the list
Enter one of the already enter domains and click add
Nothing happens
Delete all domains one by one
Successful messages are shown

OXD

(I)
Go to Administration Console > OXD
Change the post logout uri (e.g google.com) and click Save
A success message is shown
Login to oxTrust and navigate to the casa client
Redirect Logout panel shows the URI entered previously 
Logout from Casa (you are taken to google)

(II)
In a browser hit /casa and login with admin user
Go to Administration Console > OXD
Paste the original post logout uri (eg. https://acme.com/casa/bye.zul)
Click on Add scopes, select some and click Add
Scopes selected are added to the list
Click Save
A success message is shown
Login to oxTrust and navigate to the casa client
Redirect Logout panel shows the original URI and the new scopes 


Password reset availability

Go to Administration Console > Password reset availability
The switch is ON
Click the switch to turn it OFF
A success message is shown
Click on back to your credentials
The left menu does not show an item "Password reset (lock icon)"
Go to Administration Console > Password reset availability
Click the switch to turn it ON


Enabled authentication methods

(I)
Login to oxTrust and enable the following scripts: twilio_sms, otp, u2f, fido2, super_gluu
Notes:
 - fido2 requires the fido2 service installed and running (systemctl status fido2)
 - supergluu usage requires a Gluu installation in a public domain host
Create a testing user
Ensure the scripts are running correctly and fix any issues before proceeding. Use the testing user for this
Go to Users > Manage people and search the testing user, click on it
Flush all credentials for the testing user (at the bottom of the form remove all entries, if any, in authentication methods panel)
Remove supergluu and google authenticator entries on mobile for this user (if any)

(II)
In a browser hit /casa and login with admin user
A hint is shown about using the admin dashboard to add authentication methods
Wait a minute
Go to Administration Console > Enabled authentication methods
All enabled scripts must appear listed
Tick them all and Save
A success message is shown
Click on back to your credentials
The left menu shows all credential types enabled under 2FA credentials
Logout

(III)
Login to oxTrust and disable one of enabled scripts
Wait 2 minutes
In casa, Go to Administration Console > Enabled authentication methods
the method disabled is not listed
Re-enable the custom script and wait 2 minutes
the method enabled is listed again
Check it and Save
A success message is shown

enrollment

twilio_sms

This subsection requires the VM to have internet connection and 2 working mobile phones (or 1 with dual SIM).

(I)
In a browser hit /casa and login with the testing user
Click on Mobile phone numbers on the left
Fill the details of the number in "Add mobile phone"
Click on Send SMS
Wait for the message to arrive 
Enter a wrong code (it won't allow to proceed)
Enter the code sent (it will allow to assign a nickname)
Set a nickname and Save
A success message is shown
The phone added appears in the upper panel

(II)
Repeat the previous steps with another phone number
A success message is shown
Remove the most recently added phone
A success message is shown
Only 1 phone appears in the upper panel


otp

This subsection requires an smartphone with Google Authenticator installed or another otp app

(I)
In a browser hit /casa and login with the testing user
Click on OTP tokens numbers on the left
Click on Soft token
A hint appears warning you have to have an otp app installed
Open the application on the smartphone
Click on ready. A QR code appears
Scan the code with the app
A new entry appears listed in the app with a code
Enter a wrong code and press validate
A failure message appears
Enter the right code and press validate
New form fields appear to set a name or cancel the enrollment
Click on Cancel
The UI restores as when the page was initially visited
Remove the entry from the mobile app

(II)
Click on Soft token
A hint appears warning you have to have an otp app installed
Open the application on the smartphone
Click on ready. A QR code appears
Scan the code with the app
A new entry appears listed in the app with a code
Enter the code and press validate
New form fields appear to set a name or cancel the enrollment
Enter a nickname and press Add
A success message is shown
The new enrollment appears in the upper panel

u2f
This subsection requires a security key or an smartphone with the krypton app (as well as the krypton extension installed in the browser)

(I)
In a browser hit /casa and login with the testing user
Click on U2F Security Keys on the left
Plug the security key
Click on Ready
A message appears instructing to touch the key
The key is blinking
Touch the key
New form fields appear to set a name or cancel the enrollment
Click on Cancel
The UI restores as when the page was initially visited

(II)
Click on Ready
A message appears instructing to touch the key
The key is blinking
Touch the key
New form fields appear to set a name or cancel the enrollment
Enter a nickname and press Add
A success message is shown
The new enrollment appears in the upper panel


fido2
The same prerequisites and steps of u2f apply here
Additionally ensure the fido2 service is running

password reset

(I)
In a browser hit /casa and login with the testing user
Click on password reset
Type a random password, then enter 2 identical strings in the 2nd and 3rd text fields
Click on Change password
An error message is shown
Type the current password, then enter 2 different strings in the 2nd and 3rd text fields
Click on Change password
An error message is shown
Type the current password, then enter 2 identical strings in the 2nd and 3rd text fields
Click on Change password
A success message is shown
Logout

(II)
In a browser hit /casa and login with the testing user (use the new password)
Authentication succeeds
Restore the original password


2fa authentication

(I)
In a browser hit /casa and login with the testing user
Ensure there are at least 2 credentials enrolled in total and that they are not all of the same type. The following combinations are convenient examples: 1 security key and 1 otp, 1 supergluu and 1 phone number, etc.
In the main page Click on the switch to turn 2FA on
A success message is shown
Logout and re-login
You are prompted to perform an action. Proceed until completion
You must be logged in now
Logout and re-login
Click on "Try another way to sign in"
The options presented are consistent wrt the type of credentials already enrolled for the user
Select any of the options presented
You are prompted to perform an action. Proceed until completion
You must be logged in now

(II)
In the main page Click on the switch to turn 2FA off
Logout and re-login
You are prompted for user name and password (only)
You must be logged in now

(III)
In the main page Click on the switch to turn 2FA on
Logout
Login with admin user
Click on Admin Console > Reset to password authentication
Search the testing user 
Depending on the searching pattern entered a result set is presented
Tick the row corresponding to the testing user
Click on Reset to password
A success message is shown
Logout
Login with the testing user
You are prompted for user name an password (only)
You must be logged in now

(IV)
In the main page Click on the switch to turn 2FA on
Delete as many credentials as necessary so that only 2 remain
Delete one additional cred from the remaining ones
A prompt warns that 2FA will be turned off
Proceed with the operation
A success message is shown
In the main page the 2FA switch is turned off and disabled
Logout

(V)

Login to Casa as admin
Go to Administration Console > Enabled authentication methods
Uncheck the row corresponding to the credential type that still remains for the testing user
Click on Save
A success message is shown
Logout and re-login as the testing user
The 2FA credentials menu on the left is absent
A hint is shown to contact the administrator to be able to use 2fa
Go to Administration Console > Enabled authentication methods
Check all methods and Cick on Save
A success message is shown
Logout

(VI)
Login to casa with the testing user
Remove the only credential left
Logout
Login to oxTrust as administrator
Choose one of the methods supported (twilio_sms, otp, u2f, fido2, super_gluu) and go to the corresponding script
Add a custom property named 2fa_requisite and set its value to true
Hit Update at the bottom
Login to casa with the testing user
Under 2FA credentials only one item appears (corresponding to the selection made)
In the center of the page, a big card is shown inviting to enroll a credential of that specific type
Logout
Login to oxTrust as administrator
Delete the property previously added to the custom script 

custom branding plugin

Install the plugin. Instructions here: https://gluu.org/docs/casa/4.2/plugins/custom-branding/
Wait 2 mins

(I)
Login with admin user
Click on Admin Console > Custom branding
Tick the option "Upload images and pick colors"
Make modifications at will and save
A warning appears
Logout and login with the testing user
Except for logo & favicon, login pages must reflect changes applied
Once in casa ensure changes took effect accurately
Logout

(II)
Login with admin user
Click on Admin Console > Custom branding
Tick the option "Use default (Gluu Inc.) theme"
A warning appears
Logout and login with the testing user
login pages look as usual
Casa looks as usual
Logout

Consent Management

Install the plugin. Instructions here?
Wait 2 mins
Logout from Casa if there is session

(I)
Login to oxTrust with admin user
Locate the oxTrust OIDC client
Uncheck the Pre-Authorization field
Click on Update
Logout

(II)
Login to oxTrust with the testing user
The consent prompt appears, accept to continue
Logout and relogin
No consent prompt appear
Logout
Login to Casa with the testing user
Go to ?
The oxTrust client should appear listed with the summary of permissions granted
Click on revoke
A prompt appears, proceed to continue
A success message is shown
Logout
Login to oxTrust with the testing user
The consent prompt appears, accept to continue
Logout

(III)
Login to oxTrust with admin user
A prompt appears, proceed to continue
Locate the oxTrust OIDC client
Check the Pre-Authorization field
Click on Update
Logout

Account linking

These 2 sections require passport component (in the same VM of Casa) 

SAML
- Have somewhere else a SAML IDP available (e.g. gluu with shibboleth)
- Config passport for usage with such IDP
- Test passport_saml is working fine

Detailed instructions are here: https://gluu.org/docs/casa/4.2/plugins/account-linking/

Scenarios to test:

User creation

- Hit /casa and click on IDP icon (should appear below the username+password fields)
- After authentication at IDP, you are taken back to Casa
- A new user has been created in gluu database
- Click on "accounts linking" on the left. 
- An entry is listed showing the association with the IDP

Setting a password

- Remove the association (unlink). It should not allow you
- Set a password for the account. There is a menu item on the left for this
- Remove the association. Should work
- Logout, and then login with the username + password recently set (not via IDP)

Existing user

- With a different existing user, login with username + pass
- On the left-hand menu, click on "accounts linking". Click on "Link" button at the corresponding row for the IDP
- You will be taken to IDP for authentication in a separate window
- After authentication completes, a message like "you can close..." should appear. Close the window
- In casa the associated account should appear listed automatically
- Logout, then login clicking on the IDP icon this time
- You will be taken to IDP for authentication
- After completion, you will be back in Casa
- Ensure the user session corresponds to user chosen for the first step of this scenario

SOCIAL

Use the same steps as in SAML section, however, integrate a social site or an external OP (such a Gluu Server) instead of a SAML provider (IDP)


U2F to Fido2 migration

This section requires inspecting database entries (GUI/web client)

Create 2-3 testing users
enroll 1-2 u2f credentials each in addition to another credential (eg OTP)
Mirate the u2f entries to fido 2. Steps here: https://gluu.org/docs/casa/4.2/administration/fido2-migration/
Check all entries were migrated:
	- Inspect existence of corresponding fido_register entries
	- Entries are listed as fido2 security keys in Casa
	- u2f credentials are not listed in Casa
	
	
APIs testing

TBD. This requires augmenting 