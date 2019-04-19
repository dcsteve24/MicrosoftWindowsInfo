#Tested on Windows 10 and Server Installs

1. Download LGPO from https://www.microsoft.com/en-us/download/details.aspx?id=55319
    - The following technet post details how to use for other options: https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/

2. Download the relevant DISA STIG from the DISA website: https://iase.disa.mil/stigs/gpo/Pages/index.aspx

3. Move and unzip all files into the destination machine.

4. Open command prompt as an admin and change directories into the LGPO folder.

5. Run the LGPO.exe.
    - LGPO.exe /g <lowest directory path of GPO>
        - i.e. .\U_STIG_GPO_Package_July_2018\July 2018 DISA STIG GPO Package\DoD Windows 10 vlr14\GPOs\(Lots of Chars)\DomainSysvol\GPO\machine

    - Reboot and you will have the STIG applied!
