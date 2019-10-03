Self Service Password
=====================

This role installs the [LTB Self Service Password](https://ltb-project.org/documentation/self-service-password) web application.

Requirements
------------

Setting up and configuring a web server with support for PHP is beyond the scope of this role.
This needs to be present and be configured to server the `ssp_web_root` directory.

Furthermore, this role does not handle client certificates.
If the LDAP service requires this, a proxy service should be used to locally provide an unencrypted LDAP service.

Depending on the configuration, sending email may be required.
In this case, the local `sendmail` command is used, which needs to be set up properly.

Role Variables
--------------

* `ssp_web_root`  
  The directory where LTB Self Service Password is installed.
  Mandatory.
* `ssp_web_server_group`  
  Group the web server is running as (or is a member of).
  Since the config file of Self Service Password may contain sensitive information, read access is restricted to this group.
  Mandatory.
* `ssp_version`  
  The version of LTB Self Service Password that should be installed.
  If not set, the latest released version is installed.
* `ssp_ldap_url`  
  A list of URLs of LDAP servers to work with.
  Later URLs are only used as a fallback if those listed earlier are down.
  Defaults to `ldap://localhost`.
* `ssp_ldap_starttls`  
  Whether to use STARTTLS when connecting to the LDAP servers.
  Defaults to `true`.
* `ssp_ldap_bind_dn`  
  Connect to the directory as this DN.
  This is used to retrieve information about user and for password resets.
  Ensure that the account has the appropriate privileges.
  If not set, an anonymous bind is attempted.
* `ssp_ldap_bind_password`  
  The password for `ssp_ldap_bind_dn`.
* `ssp_ldap_base`  
  The search base for user account.
  Optional.
* `ssp_ldap_filter`  
  A search filter that returns the user's account.
  The string `{login}` is replaced by the user's login name.
  The default is `(&(objectClass=account)(uid={login}))`.
* `ssp_display_name_attribute`  
  The name of the attribute containing the user's display name.
  Defaults to `cn`.
* `ssp_mail_attribute`  
  The name of the attribute containing the user's email address.
  Defaults to `mail`.
* `ssp_mobile_attribute`  
  The name of the attribute containing the user's mobile phone number.
  Defaults to `mobile`.
* `ssp_sshkey_attribute`  
  The name of the attribute containing the user's public SSH key.
  Defaults to `sshPublicKey`.
* `ssp_ad_mode`  
  Set to `true` for compatibility with Microsoft Active Directory.
  Defaults to `false`.
* `ssp_samba_mode`  
  Set to `true` to update the `sambaNTpassword` and `sambaPwdLastSet` attributes on password changes.
  Defaults to `false`.
* `ssp_change_sshkey`  
  Enable support for changing SSH keys via Self Service Password.
  Defaults to `false`.
* `ssp_hash_passwords`  
  Set to `true` to pass hashed passwords to the LDAP directory.
  Set to `false` to pass clear text passwords and let the directory handle hashing.
  The latter is required for a directory-side password policy to work.
  Defaults to `false`.
* `ssp_password_length`  
  Password minimal length requirement.
  Optional.
* `ssp_password_complexity`  
  The number of character classes required for new passwords.
  Sensible values are `2`, `3` or `4`.
  Optional.
* `ssp_check_pwned`  
  Check passwords against publicly known password leaks using the https://haveibeenpwned.com/ API.
  For comparison, a part of the password's hash is sent to the API.
  Defaults to `true`.
* `ssp_notify_user`  
  If set to `true`, an email notification is sent to the users when their password or SSH key is changed.
  Defaults to `false`.
* `ssp_reset_email`  
  Whether to enable resetting passwords via email confirmation.
  Defaults to `false`.
* `ssp_reset_sms`  
  Whether to enable resetting passwords via SMS confirmation.
  Defaults to `false`.
* `ssp_reset_email_lifetime`  
  How long a reset link is valid for (in seconds).
  Defaults to one hour.
* `ssp_email_from`  
  The sender address for emails.
  Mandatory if `ssp_reset_email` or `ssp_notify_user` is `true` or if `ssp_send_sms_method` is `mail`, ignored otherwise.
* `ssp_email_name`  
  The sender display name for emails.
  Optional, Self Service Password's default is used if not set.
  Only used if `ssp_reset_email` or `ssp_notify_user` is `true` or if `ssp_send_sms_method` is `mail`.
* `ssp_send_sms_method`  
  The method of contacting the SMS sending provider.
  Either `mail` or `api`.
  Mandatory if `ssp_reset_sms` is `true`, ignored otherwise.
* `ssp_sms_mail_provider`  
  To send out an SMS, send an email to this address.
  The string `{sms_attribute}` will be replaced by the mobile number.
  Mandatory if `ssp_send_sms_method` is `mail`, ignored otherwise.
* `ssp_sms_mail_subject`  
  The subject of the email sent to the SMS provider.
  Only used if `ssp_send_sms_method` is `mail`.
* `ssp_sms_api`  
  Name of the SMS API provider as used in the file name in `lib/sms-api-*.inc.php`.
  Mandatory if `ssp_send_sms_method` is `api`, ignored otherwise.
* `ssp_sms_api_config`  
  A dictionary containing further configuration for the SMS API.
  Note that string values require additional quotes (for YAML and PHP).
  Usually mandatory if `ssp_send_sms_method` is `api`, ignored otherwise.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
- hosts: servers
  roles:
    - role: ssp
      become: true
      ssp_web_root: '/var/www'
      ssp_web_server_group: 'www-data'
```

License
-------

MIT
