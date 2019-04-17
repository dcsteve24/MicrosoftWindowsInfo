#These instructions tie FreeIPA into LDAP authentication for windows machines. This has been tested on Windows 10 and Server 2012.

1. Download the latest pGina: http://pgina.org/download.html

2. Move to destination client/server and install it by running the .exe. No special prompts or selections are required.

3. When finished, open the pGina application.

4. Select **Plugin Selection** and check the **Authentication, Authorization, and Gateway** checkboxes for LDAP and Local Machine.

5. Double click LDAP to bring up the LDAP Plugin Settings window. Enter the following:
    - **LDAP Host(s)** - the IP or hostname of the LDAP Server
    - **LDAP Port** - 389 for unencrypted or TLS encrypted. 636 for SSL encrypted.
    - **Timeout** - Change if desired. Seconds itll attempt before it qualifies as a fail.
    - **Search DN** - the LDAP syntax of the account to use to check LDAP. Needs permissions to read the directory.
        - FreeIPA default syntax is "uid=UserName, cn=users, cn=accounts, dc=example, dc=com". Change username, example, and com to match your case and add more cn as needed if you compartmentalize more.
    - **Search Password** - Password of the Search DN account
    - **Group DN Pattern** - The LDAP syntax of the compartment where groups are located.
        - FreeIPA default syntax is "cn=%g, cn=groups, cn=accounts, dc=example, dc=com". Change example and com to match your environments and add more cn as needed if you compartmentalize more.
    - **Member Attribute** - change default to member
    - Under the **Authentication** tab, define the location of accounts to apply to
        - Check the **Search for DN** check box
        - **Search Filter** - Enter uid=%u to pull all users
        - **Search Context** - The LDAP syntax compartment you want to pull from. if everything, just leave as dc=example, dc=com (with your info).
            - I typically break it down to the default users folder like so: "cn=users,cn=accounts,dc=example,dc=com"
    - Click the **Authorization** tab. Define in what conditions you allow or deny into the client.
        - I.e. to only allow people from the admins group in FreeIPA:
            - Change the **Default** radio button to **Deny**
            - In the dropbox at bottom left, select **If member of LDAP group**, next to it type in the group name (admins in this example), and the next dropbox select **allow**.
            - Click **Add Rule**.
    - Click the **Gateway** tab. Here you define what local groups to add users to based on their LDAP groups.
        - I.e. to add the admins FreeIPA group members to the local Administrators, do the following:
            - In the dropbox at the bottom left, select **If member of LDAP group**, next to it type in the group name (admins in this example), and the textbox type the local group (Administrators in this example).
            - Click **Add Rule**.
    - Click **Save** to close the LDAP window.

6. Back on the pGina Configuration window, click the **Plugin Order** tab.

7. Under **Authentication, Authorization, and Gateway**, move LDAP to the top for all three.

8. Click the **Simulation** tab. Here you can test the FreeIPA authentication to ensure your settings are correct.
    - type in a FreeIPA account user name and password and hit the play button.
    - If the results show all green, you will be able to log in!
    - If you were using the gateway to add to permissions, the **Local Groups** should be populated with the expected group. If not, go back and edit your configs and try again.
        - **Group DN Pattern** and **Member Attribute** and the **Gateway** settings affect the groups membership.

9. If everything simmulates correctly, click **Save & Close** and you're done!
