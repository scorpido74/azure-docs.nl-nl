---
title: Problemen met Windows Virtual Desktop Session Host oplossen-Azure
description: Problemen oplossen bij het configureren van virtuele machines voor virtuele bureau blad-sessies van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
ms.openlocfilehash: f8400cbefc514fa01dedb1434a60989b1df0528d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980220"
---
# <a name="session-host-virtual-machine-configuration"></a>Configuratie van sessiehost-VM's

Gebruik dit artikel voor het oplossen van problemen die zich voordoen bij het configureren van de virtuele machines (Vm's) voor virtuele bureau blad-sessies van Windows.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="vms-are-not-joined-to-the-domain"></a>Vm's zijn niet toegevoegd aan het domein

Volg deze instructies als u problemen ondervindt bij het samen voegen van Vm's aan het domein.

- Voeg de VM hand matig toe met behulp van het proces in [een virtuele Windows Server-machine toevoegen aan een beheerd domein](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) of het gebruik van de [sjabloon voor domein deelname](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Probeer de domein naam te pingen vanaf de opdracht regel op de virtuele machine.
- Bekijk de lijst met fout berichten voor domein deelname in het [oplossen van fout berichten voor domein deelname](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Fout: onjuiste referenties

**Oorzaak:** Er is een type fout opgetreden bij het invoeren van de referenties in de Azure Resource Manager sjabloon-interface oplossingen.

**Oplossen:** Voer een van de volgende acties uit om het probleem op te lossen.

- Voeg de Vm's hand matig toe aan een domein.
- De sjabloon opnieuw implementeren wanneer de referenties zijn bevestigd. Zie [een hostgroep maken met Power shell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
- Virtuele machines toevoegen aan een domein met behulp van een sjabloon met een [bestaande Windows-VM koppelen aan een AD-domein](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Fout: time-out bij het wachten op invoer van de gebruiker

**Oorzaak:** Het account dat wordt gebruikt voor het volt ooien van het lidmaatschap van een domein, heeft mogelijk multi-factor Authentication (MFA).

**Oplossen:** Voer een van de volgende acties uit om het probleem op te lossen.

- MFA voor het account tijdelijk verwijderen.
- Gebruik een service account.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Fout: het account dat wordt gebruikt tijdens het inrichten, heeft geen machtigingen om de bewerking te volt ooien

**Oorzaak:** Het account dat wordt gebruikt, heeft geen machtigingen om Vm's toe te voegen aan het domein vanwege naleving en voor Schriften.

**Oplossen:** Voer een van de volgende acties uit om het probleem op te lossen.

- Gebruik een account dat lid is van de groep Administrators.
- Verleen de benodigde machtigingen voor het account dat wordt gebruikt.

### <a name="error-domain-name-doesnt-resolve"></a>Fout: de domein naam kan niet worden omgezet

**Oorzaak 1:** Vm's bevinden zich in een virtueel netwerk dat niet is gekoppeld aan het virtuele netwerk (VNET) waarin het domein zich bevindt.

**Oplossing 1:** Maak VNET-peering tussen het VNET waar Vm's zijn ingericht en het VNET waar de domein controller (DC) wordt uitgevoerd. Zie [een peering voor een virtueel netwerk maken-Resource Manager, verschillende abonnementen](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Oorzaak 2:** Wanneer u Azure Active Directory Domain Services (Azure AD DS) gebruikt, zijn de DNS-server instellingen van het virtuele netwerk niet bijgewerkt zodat ze verwijzen naar de beheerde domein controllers.

**Oplossing 2:** Zie [DNS-instellingen bijwerken voor het virtuele Azure-netwerk](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance#update-dns-settings-for-the-azure-virtual-network)om de DNS-instellingen voor het virtuele netwerk met Azure AD DS bij te werken.

**Oorzaak 3:** De DNS-server instellingen van de netwerk interface verwijzen niet naar de juiste DNS-server in het virtuele netwerk.

**Oplossing 3:** Voer een van de volgende acties uit om het probleem op te lossen, volgens de stappen in [Change DNS servers].
- Wijzig de DNS-server instellingen van de netwerk interface in **aangepast** met de stappen van [DNS-servers wijzigen](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers) en geef de privé-IP-adressen op van de DNS-servers in het virtuele netwerk.
- Wijzig de instellingen van de DNS-server van de netwerk interface in **overnemen van het virtuele netwerk** met de stappen van [DNS-servers wijzigen](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers)en wijzig vervolgens de DNS-server instellingen van het virtuele netwerk met de stappen van [DNS-servers wijzigen](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>De Windows Virtual Desktop agent en het Windows-opstart laad programma voor virtueel bureau blad zijn niet geïnstalleerd

De aanbevolen manier om Vm's in te richten, is met behulp van de Azure Resource Manager sjabloon voor het **maken en inrichten van Windows virtueel-bureaublad groep** . De sjabloon installeert automatisch de Windows Virtual Desktop agent en de opstart lader van de Windows Virtual Desktop agent.

Volg deze instructies om te bevestigen dat de onderdelen zijn geïnstalleerd en om te controleren op fout berichten.

1. Controleer of de twee onderdelen zijn geïnstalleerd door in **het configuratie scherm** te controleren > **Program Ma's** > **Program ma's en onderdelen**. Als **Windows Virtual Desktop agent** en de **opstart lader van de Windows Virtual Desktop agent** niet zichtbaar zijn, zijn ze niet geïnstalleerd op de virtuele machine.
2. Open **bestanden Verkenner** en ga naar **C:\Windows\Temp\ScriptLog.log**. Als het bestand ontbreekt, geeft dit aan dat de Power shell DSC waarmee de twee onderdelen zijn geïnstalleerd, niet kan worden uitgevoerd in de beschik bare beveiligings context.
3. Als het bestand **C:\Windows\Temp\ScriptLog.log** aanwezig is, opent u het en controleert u op fout berichten.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Error: Windows Virtual Desktop Agent and Windows Virtual Desktop Agent Boot Loader are missing. C:\Windows\Temp\ScriptLog.log is also missing

**Cause 1:** Credentials provided during input for the Azure Resource Manager template were incorrect or permissions were insufficient.

**Fix 1:** Manually add the missing components to the VMs using [Create a host pool with PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Cause 2:** PowerShell DSC was able to start and execute but failed to complete as it can't sign in to Windows Virtual Desktop and obtain needed information.

**Fix 2:** Confirm the items in the following list.

- Make sure the account doesn't have MFA.
- Confirm that the tenant name is accurate and the tenant exists in Windows Virtual Desktop.
- Confirm the account has at least RDS Contributor permissions.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Error: Authentication failed, error in C:\Windows\Temp\ScriptLog.log

**Cause:** PowerShell DSC was able to execute but couldn't connect to Windows Virtual Desktop.

**Fix:** Confirm the items in the following list.

- Manually register the VMs with the Windows Virtual Desktop service.
- Confirm account used for connecting to Windows Virtual Desktop has permissions on the tenant to create host pools.
- Confirm account doesn't have MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop Agent is not registering with the Windows Virtual Desktop service

When the Windows Virtual Desktop Agent is first installed on session host VMs (either manually or through the Azure Resource Manager template and PowerShell DSC), it provides a registration token. The following section covers troubleshooting issues applicable to the Windows Virtual Desktop Agent and the token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Error: The status filed in Get-RdsSessionHost cmdlet shows status as Unavailable

![Get-RdsSessionHost cmdlet shows status as Unavailable.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Cause:** The agent isn't able to update itself to a new version.

**Fix:** Follow these instructions to manually update the agent.

1. Download a new version of the agent on the session host VM.
2. Launch Task Manager and, in the Service Tab, stop the RDAgentBootLoader service.
3. Run the installer for the new version of the Windows Virtual Desktop Agent.
4. When prompted for the registration token, remove the entry INVALID_TOKEN and press next (a new token isn't required).
5. Complete the installation Wizard.
6. Open Task Manager and start the RDAgentBootLoader service.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Error:  Windows Virtual Desktop Agent registry entry IsRegistered shows a value of 0

**Cause:** Registration token has expired or has been generated with expiration value of 999999.

**Fix:** Follow these instructions to fix the agent registry error.

1. If there's already a registration token, remove it with Remove-RDSRegistrationInfo.
2. Generate new token with Rds-NewRegistrationInfo.
3. Confirm that the -ExpriationHours parameter is set to 72 (max value is 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Error: Windows Virtual Desktop agent isn't reporting a heartbeat when running Get-RdsSessionHost

**Cause 1:** RDAgentBootLoader service has been stopped.

**Fix 1:** Launch Task Manager and, if the Service Tab reports a stopped status for RDAgentBootLoader service, start the service.

**Cause 2:** Port 443 may be closed.

**Fix 2:** Follow these instructions to open port 443.

1. Confirm port 443 is open by downloading the PSPing tool from [Sysinternal tools](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Install PSPing on the session host VM where the agent is running.
3. Open the command prompt as an administrator and issue the command below:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirm that PSPing received information back from the RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Troubleshooting issues with the Windows Virtual Desktop side-by-side stack

The Windows Virtual Desktop side-by-side stack is automatically installed with Windows Server 2019. Use Microsoft Installer (MSI) to install the side-by-side stack on Microsoft Windows Server 2016 or Windows Server 2012 R2. For Microsoft Windows 10, the Windows Virtual Desktop side-by-side stack is enabled with **enablesxstackrs.ps1**.

There are three main ways the side-by-side stack gets installed or enabled on session host pool VMs:

- With the Azure Resource Manager **Create and provision new Windows Virtual Desktop host pool** template
- By being included and enabled on the master image
- Installed or enabled manually on each VM (or with extensions/PowerShell)

If you're having issues with the Windows Virtual Desktop side-by-side stack, type the **qwinsta** command from the command prompt to confirm that the side-by-side stack is installed or enabled.

The output of **qwinsta** will list **rdp-sxs** in the output if the side-by-side stack is installed and enabled.

![Side-by-side stack installed or enabled with qwinsta listed as rdp-sxs in the output.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Controleer de hieronder vermelde Register vermeldingen en controleer of de waarden overeenkomen. Als er register sleutels ontbreken of als de waarden niet overeenkomen, volgt u de instructies in [een hostgroep maken met Power shell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) voor informatie over het opnieuw installeren van de side-by-side-stack.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Fout: O_REVERSE_CONNECT_STACK_FAILURE

![Fout code O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Oorzaak:** De side-by-side-stack is niet geïnstalleerd op de host-VM van de sessie.

**Oplossen:** Volg deze instructies voor het installeren van de side-by-side-stack op de host-VM van de sessie.

1. Gebruik Remote Desktop Protocol (RDP) om rechtstreeks naar de host-VM te gaan als lokale beheerder.
2. Down load en importeer [de Windows Virtual Desktop Power shell-module](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) voor gebruik in uw Power shell-sessie als u dit nog niet hebt gedaan, voert u deze cmdlet uit om u aan te melden bij uw account:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. De side-by-side stack installeren met behulp van [een hostgroep maken met Power shell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Een Windows-stack met virtuele Bureau bladen die niet goed werkt herstellen

Er zijn bekende omstandigheden waardoor de side-by-side-stack defect kan raken:

- Niet de juiste volg orde van de stappen voor het inschakelen van de side-by-side-stack
- Automatisch bijwerken naar Windows 10 Enhanced veelzijdige schijf (EVD)
- De rol van de Extern bureaublad sessiehost (RDSH) ontbreekt
- Meerdere keren uitvoeren van enablesxsstackrc. ps1
- Enablesxsstackrc. ps1 wordt uitgevoerd in een account zonder lokale beheerders bevoegdheden

De instructies in deze sectie kunnen u helpen bij het verwijderen van de Windows-stack met virtuele Bureau bladen. Nadat u de side-by-stack hebt verwijderd, gaat u naar ' de virtuele machine registreren bij de Windows Virtual Desktop-hostgroep ' in [een hostgroep met Power shell maken](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) om de side-by-side-stack opnieuw te installeren.

De virtuele machine die wordt gebruikt voor het uitvoeren van herstel, moet zich op hetzelfde subnet en domein bevinden als de virtuele machine met de stack-by-side-stapel.

Volg deze instructies voor het uitvoeren van herstel vanuit hetzelfde subnet en domein:

1. Maak verbinding met Standard Remote Desktop Protocol (RDP) naar de VM vanaf waar de oplossing wordt toegepast.
2. Down load PsExec van https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Pak het gedownloade bestand uit.
4. Start de opdracht prompt als lokale beheerder.
5. Ga naar map waar PsExec is uitgepakt.
6. Gebruik de volgende opdracht vanaf de opdracht prompt:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname is de computer naam van de virtuele machine met de niet-gelijktijdige stack.

7. Accepteer de gebruiksrecht overeenkomst voor PsExec door op akkoord te klikken.

    ![Scherm opname van de software licentie overeenkomst.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Dit dialoog venster wordt alleen weer gegeven voor de eerste keer dat PsExec wordt uitgevoerd.

8. Wanneer de sessie met de opdracht prompt op de virtuele machine wordt geopend met de niet-gelijktijdige stack, voert u qwinsta uit en controleert u of er een vermelding met de naam RDP-SxS beschikbaar is. Als dat niet het geval is, is er geen side-by-side stack aanwezig op de VM, zodat het probleem niet is gekoppeld aan de side-by-side-stack.

    ![Opdracht prompt van beheerder](media/AdministratorCommandPrompt.png)

9. Voer de volgende opdracht uit, waarmee micro soft-onderdelen die op de virtuele machine zijn geïnstalleerd, worden weer geven met de stack-by-side-stapel.

    ```cmd
        wmic product get name
    ```

10. Voer de onderstaande opdracht uit met product namen uit de bovenstaande stap.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Verwijder alle producten die beginnen met "Extern bureaublad".

12. Nadat alle onderdelen van het virtuele bureau blad van Windows zijn verwijderd, volgt u de instructies voor uw besturings systeem:

13. Als uw besturings systeem Windows Server is, start u de VM die de defecte side-by-side stack had, opnieuw op (met Azure Portal of van het hulp programma PsExec).

Als uw besturings systeem micro soft Windows 10 is, gaat u verder met de onderstaande instructies:

14. Open in de virtuele machine met PsExec de bestanden Verkenner en kopieer disablesxsstackrc. ps1 naar het systeem station van de virtuele machine met de niet-werkende side-by-side-stack.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname is de computer naam van de virtuele machine met de niet-gelijktijdige stack.

15. Het aanbevolen proces: vanuit het PsExec-hulp programma start u Power shell en navigeert u naar de map uit de vorige stap en voert u disablesxsstackrc. ps1 uit. U kunt ook de volgende cmdlets uitvoeren:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Wanneer de cmdlets worden uitgevoerd, start u de VM opnieuw op met de stack-aan-zij die niet goed werkt.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>De licentie modus Extern bureaublad is niet geconfigureerd

Als u zich met een Administrator-account aanmeldt bij Windows 10 Enter prise multi-session, ontvangt u mogelijk een melding met de tekst ' Extern bureaublad licentie modus is niet geconfigureerd Extern bureaublad-services werkt niet meer over X dagen. Gebruik op de Connection Broker-server Serverbeheer om de Extern bureaublad licentie modus op te geven. "

Als de tijds limiet verloopt, wordt een fout bericht weer gegeven met de tekst ' de externe sessie is beëindigd omdat er geen Extern bureaublad licenties voor client toegang beschikbaar zijn voor deze computer. '

Als u een van deze berichten ziet, betekent dit dat de installatie kopie niet de meest recente Windows-updates heeft geïnstalleerd of dat u de Extern bureaublad licentie modus instelt via groeps beleid. Volg de stappen in de volgende secties om de groeps beleids instelling te controleren, de versie van Windows 10 Enter prise multi-session te identificeren en de bijbehorende update te installeren.  

>[!NOTE]
>Voor het virtuele bureau blad van Windows is alleen een RDS-Client Access License (CAL) vereist wanneer uw hostgroep Windows Server Session hosts bevat. Zie [licentie voor uw RDS-implementatie met licenties voor client toegang](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-client-access-license)voor meer informatie over het configureren van een RDS CAL.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>De groeps beleids instelling Extern bureaublad licentie modus uitschakelen

Controleer de groeps beleids instelling door de groepsbeleid editor te openen in de virtuele machine en te navigeren naar **Beheersjablonen** > **Windows-onderdelen** > **Extern bureaublad-services** > extern bureaublad **sessie host** > **licentie** > **de licentie modus voor extern bureaublad instellen**. Als de groeps beleids instelling is **ingeschakeld**, wijzigt u deze in **uitgeschakeld**. Als de functie al is uitgeschakeld, kunt u deze vervolgens ongewijzigd laten.

>[!NOTE]
>Als u groeps beleid via uw domein instelt, schakelt u deze instelling uit voor beleids regels die zijn gericht op deze Windows 10 Enter prise-Vm's met meerdere sessies.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Bepalen welke versie van Windows 10 Enter prise multi-sessie u gebruikt

Controleren welke versie van Windows 10 Enter prise multi-session u hebt:

1. Meld u aan met uw beheerders account.
2. Geef "about" op in de zoek balk naast het menu Start.
3. Selecteer **over uw PC**.
4. Controleer het nummer naast versie. Het getal moet ' 1809 ' of ' 1903 ' zijn, zoals wordt weer gegeven in de volgende afbeelding.

    ![Een scherm opname van het Windows-specificaties venster. Het versie nummer is blauw gemarkeerd.](media/windows-specifications.png)

Nu u het versie nummer weet, gaat u verder met de relevante sectie.

### <a name="version-1809"></a>Versie 1809

Als uw versie nummer 1809 is, installeert u [de update KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Versie 1903

Implementeer het hostbesturingssysteem opnieuw met de nieuwste versie van de installatie kopie van Windows 10, versie 1903 van de Azure Gallery.

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.
