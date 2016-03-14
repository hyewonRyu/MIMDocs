---
title: Preparing an identity management server: Windows Server 2012 R2
ms.custom:
  - Identity Management
  - MIM
ms.prod: identity-manager-2015
ms.reviewer: na
ms.suite: na
ms.technology:
  - security
ms.tgt_pltfrm: na
ms.topic: get-started-article
ms.assetid:
author: kgremban
---
# Preparing an identity management server: Windows Server 2012 R2

>[!div class="step-by-step"]
[Previous](https://docsmsftstage.azurewebsites.net/MIM/DeployUse/preparing-domain.html)
**Preparing a domain**

> [!NOTE]
> In all the examples below, **mimservername** represents the name of your domain controller, **contoso** represents your domain name, and **Pass@word1** represents an example password.

## Join Windows Server 2012 R2 to your domain

1. Create a Windows Server 2012 R2 machine, with a minimum of 8GB of RAM. When installing specify "Windows Server 2012 R2 Standard (Server with a GUI) x64" edition.

2. Log into the new computer as its administrator.

3. Using the Control Panel, give the computer a static IP address on the network. Configure that network interface to send DNS queries to the IP address of the domain controller in the previous step, and set the computer name to **CORPIDM**.  This will require a server restart.

4. If the computer is on a virtual network which does not provide Internet connectivity, add an additional network interface to the computer that provides a connection to the Internet.  This is needed for SharePoint installation, and can be disabled after that step is completed.

5. Open the Control Panel and join the computer to the domain that you configured in the last step, *contoso.local*.  This includes providing the username and credentials of a domain administrator such as *Contoso\Administrator*.  After the welcome message appears, close the dialog box and restart this server again.

6. Sign in to the computer *CorpIDM* as a domain administrator such as *Contoso\Administrator*.

7. Launch a PowerShell window as administrator and type the following command to update the computer with the group policy settings.

    ```
    gpupdate /force /target:computer
    ```

    After no more than a minute, it will complete with the message "Computer Policy update has completed successfully."

8. Add the **Web Server (IIS)** and **Application Server** roles, the **.NET Framework** 3.5, 4.0, and 4.5 features, and the **Active Directory module for Windows PowerShell**.

    ![PowerShell features, select Active Directory module for Windows PowerShell](media/MIM-DeployWS2.png)

9. In PowerShell, still as an administrator, type the following commands. Note that it may be necessary to specify a different location for the source files for **.NET Framework** 3.5 features. These features are typically not present when Windows Server installs, but are available in the side-by-side (SxS) folder on the OS install disk sources folder, e.g., “*d:\Sources\SxS\*”.

    ```
    import-module ServerManager
    Install-WindowsFeature Web-WebServer, Net-Framework-Features,rsat-ad-powershell,Web-Mgmt-Tools,Application-Server,Windows-Identity-Foundation,Server-Media-Foundation,Xps-Viewer –includeallsubfeature -restart -source d:\sources\SxS
    ```

## Configure the server security policy

Set up the server security policy to allow the newly-created accounts to run as services.

1. Launch the Local Security Policy program

2. Navigate to **Local Policies > User Rights Assignment**.

3. On the details pane, right click on **Log on as a service**, and select **Properties**.

    ![Local Security Policy, right click for policy properties](media/MIM-DeployWS3.png)

4. Click **Add User or Group**, and in the text box type `contoso\mimsync; contoso\mimma; contoso\MIMService; contoso\SharePoint; contoso\SqlServer; contoso\mimsspr`, click **Check Names**, and click **OK**.

5. Click **OK** to close the **Log on as a service Properties** window.

6.  On the details pane, right click on **Deny access to this computer from the network**, and select **Properties**.

7. Click **Add User or Group**, and in the text box type `contoso\MIMSync; contoso\MIMService` and click **OK**.

8. Click **OK** to close the **Deny access to this computer from the network Properties** window.

9. On the details pane, right click on **Deny log on locally**, and select **Properties**.

10. Click **Add User or Group**, and in the text box type `contoso\MIMSync; contoso\MIMService` and click **OK**.

11. Click **OK** to close the **Deny log on locally Properties** window.

12. Close the Local Security Policy window.


## Change the IIS Windows Authentication mode

1.  Open a PowerShell window.

2.  Stop IIS with the command *iisreset /STOP*

        ```
        iisreset /STOP
        C:\Windows\System32\inetsrv\appcmd.exe unlock config /section:windowsAuthentication -commit:apphost
        iisreset /START
        ```

>[!div class="step-by-step"]  
[Next](https://docsmsftstage.azurewebsites.net/MIM/DeployUse/prepare-server-sql2014.html)
**Preparing an identity management server: SQL Server 2014**