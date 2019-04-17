## The following procedures outline the steps to use FreeIPAs kerberos authentication for Windows. This has been tested on Windows 7. I have attempted it on Server 2012 but the FreeIPA was preconfigured and me editing things broke authentication so had to revert and go the pGina route.

1. On FreeIPA, install the ipa-adtrust module. For RedHat, this is done with **ipa-server-trust-ad**.

2. Allow the following ports on the server's firewall:
    - 135/tcp
    - 138/tcp
    - 139/tcp
    - 445/tcp
    - 1024-1300/tcp
    - 138/udp
    - 139/udp
    - 389/udp
    - 445/udp

3. Restart the firewall service.

4. Install the ipa-adtrust with **ipa-adtrust-install**. Answer the prompts.

5. SELinux flips out with epmap and smbd, and I couldnt find a way to allow it. So instead i set those specific protocols to permissive with: **semanage permissive -a smbd**

6. Restart the smb service with **systemctl restart smb**

7. On FreeIPA, add the host. You can do this in the GUI if desired, but here is the command line command:
    - kinit <username>        #to pull your ticket
    - ipa host-add --force --ip-address=<ip> <hostname>.<domain>
        i.e. host-add --force --ip-address=10.10.10.10 testmachine.example.com

8. Run the following command: ipa-getkeytab -s <hostname>.<domain> -p host/<hostname>.<domain> -r arcfour-hmac -k krb5.keytab.windows -P

9. You will be prompted for a password. Remember this password as you'll need it later. I don't recommend using symbols since this will be typed into a command prompt and some symbols mean other things (i.e. \ can mean break)

10. On the windows client, log in with administrator privileges and open command prompt as an admin.

11. Ensure you have DNS able to resolve the FreeIPA FQDN server and if not, configure it to do so. i.e. in most cases this means pointing it to the FreeIPA server, but if FreeIPA doesn't handle the DNS, then point it to where its handled.

11. Run the following commands in order:
    - ksetup /setdomain <domain>                            #i.e. ksetup /setdomain example.com
    - ksetup /addkdc <domain> <FQDN of FreeIPA>             #i.e. ksetup /addkdc example.com testdirsrv.example.com
    - ksetup /addkpasswd <domain> <FQDN of FreeIPA>         #i.e. ksetup /addkpasswd example.com testdirsrv.example.com
    - ksetup /setcomputerpassword <password you set in step 8/9>
    - ksetup /mapuser * *

12. In the windows client, open gpedit.msc by typing it in the search or typing it into the run pane after hitting the Windows key + R.

13. Expand to the following: **Computer Configuration --> Windows Settings --> Security Settings --> Local Policies --> Security Options**.

14. Find **Network Security: Configure encryption types allowed for Kerberos** and double click it to edit it.

15. Check the following options (last few):
      - RC4_HMAC_MD5
      - AES128_HMAC_SHA1
      - AES256_HMAC_SHA1
      - Future encryption types

16. Click Ok, close the windows, and reboot the computer.

17. When the computer comes back up, hit switch user and you'll see the domain listed now. Authenticate with the domain credentials and it shoudl let you in!


Troubleshooting:
- This guide takes snips from here: https://www.rootusers.com/how-to-login-to-windows-with-a-freeipa-account/
- That link has some good areas for troubleshooting. Note, since we used the ipa-adtrust, we dont need to create local accounts prior to login. Which is why this doesn't follow that guide exactly.
