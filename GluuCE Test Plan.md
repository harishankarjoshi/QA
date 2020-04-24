|Test Case ID| Feature                                                                  | Test Case | Assigned | Status |
|:-----------|:-------------------------------------------------------------------------|:----------|----------|--------|
|Gluu1.1|Login & Registration                                | case 1:- setup smtp details<br> case 2:- without captcha enabled <br>case 3:- with captcha<br>case 4:- registration confirmation mail<br>case 5:- user can edit/add his self information like password ,country etc<br>case 6:- self passport reset Functionality<br>case 7:- forgot password Functionality without captcha<br>case 8:- forgot password Functionality with captcha|||
|Gluu1.2|User Management |case 1:- add/update user from oxtrust<br>case 2:- add/update user to group from oxtust<br>case 3:- import user from excel|||
|Gluu1.3|Custom Script |case 1:- testing duo Script<br>case 2:- testing twilio<br>case 3:- testing otp<br>case 4:- testing passport social<br>case 5:- testing passport saml<br>case 6:- testing super gluu script<br>case 7:- testing u2f script|||
|Gluu1.4|Attribute Management |case 1:- create/delete custom Attribute<br>case 2:- update/delete existing Attribute<br>case 3:- import/export Attribute|||
|Gluu1.5|Saml|case 1:- create trust relationship<br>a) with different release Attribute<br>b) with different metadata like file , uri and federation<br>case 2:- Configured nameid Attribute<br>case 3:- Test SSO with above online service available.<br>case 4:- Test passport saml demo<br>case 5:- Test SAML Logout<br>case 6:- Test vaious options for saml:AuthnContextClassRef|||
|Gluu1.6|OpenId Connect|case 1:- add/delete scope<br>case 2:- add/delete client manually<br>a) Provide  details like redirect login/logout uri, scopes ,  response type etc.<br>case 3:- Perform SSO and check various response type.<br>case 4:- Dynamic client registration.<br>case 5:- add sector identifier.|||
|Gluu1.7|Cache Refresh|case 1:- Add mulitple Source backend server<br>case 2:- mapping with different Attribute<br>case 3:- once cache refresh run check the user from user section<br>case 3:- test multi user config script (https://github.com/GluuFederation/oxAuth/tree/master/Server/integrations/basic.multi_auth_conf)|||
|Gluu1.8|Passport| case 1:- Add provider like github ,google,  fb.<br>case 2:- Testing user login via passport service|||
|Gluu1.9|Casa|case 1:- enable casa<br>case 2:- enroll devices to check user registration.|||
|Gluu1.10|Services|case 1:- start/stop/restart all the services and container|||
|Gluu1.11|Upgrade|case 1:- Test upgrade from previous minor version<br>case 2:- Test upgrade from previous major version|||
|Gluu1.12|Misc|case 1:- Test with remote LDAP<br> case 2:- Test adding Passport and Shibboleth components post installation|||
