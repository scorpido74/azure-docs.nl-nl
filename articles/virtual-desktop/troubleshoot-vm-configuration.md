---
title: Problemen met Windows Virtual Desktop-sessiehost oplossen - Azure
description: Problemen oplossen wanneer u virtuele machines voor Windows Virtual Desktop-sessiehost configureert.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127583"
---
# <a name="session-host-virtual-machine-configuration"></a>Configuratie van sessiehost-VM's

Gebruik dit artikel om problemen op te lossen die u ondervindt bij het configureren van virtuele machines (Virtual Desktop-sessiehost) van Windows.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) om de Windows Virtual Desktop-service te bespreken met het productteam en actieve communityleden.

## <a name="vms-are-not-joined-to-the-domain"></a>VM's zijn niet verbonden met het domein

Volg deze instructies als u problemen ondervindt bij het samenvoegen van VM's in het domein.

- Word handmatig lid van de virtuele machine van de virtuele machine van Windows Server met de virtuele [machine van Windows Server naar een beheerd domein](../active-directory-domain-services/join-windows-vm.md) of gebruik de sjabloon voor het deelnemen aan [domeinen](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Probeer de domeinnaam te pingen vanaf de opdrachtregel op VM.
- Bekijk de lijst met foutberichten voor domeinjoinen in [Foutberichten voor het oplossen van problemen oplossen.](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)

### <a name="error-incorrect-credentials"></a>Fout: Onjuiste referenties

**Oorzaak:** Er is een typefout gemaakt toen de referenties werden ingevoerd in de azure resource manager-sjablooninterfacefixes.

**Oplossing:** Neem een van de volgende acties op te lossen.

- Voeg de VM's handmatig toe aan een domein.
- Implementeer de sjabloon opnieuw zodra referenties zijn bevestigd. Zie [Een hostgroep maken met PowerShell](create-host-pools-powershell.md).
- Word lid van VM's naar een domein met behulp van een sjabloon met [Joins een bestaande Windows VM naar AD-domein](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Fout: Time-out wachten op invoer van de gebruiker

**Oorzaak:** Het account dat wordt gebruikt om de domeinjoin te voltooien, kan multi-factor authenticatie (MFA) hebben.

**Oplossing:** Neem een van de volgende acties op te lossen.

- MFA tijdelijk verwijderen voor het account.
- Gebruik een serviceaccount.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Fout: het account dat wordt gebruikt tijdens het inrichten, heeft geen machtigingen om de bewerking te voltooien

**Oorzaak:** Het account dat wordt gebruikt, heeft geen machtigingen om VM's aan te sluiten bij het domein vanwege naleving en regelgeving.

**Oplossing:** Neem een van de volgende acties op te lossen.

- Gebruik een account dat lid is van de groep Administrator.
- De benodigde machtigingen verlenen aan het account dat wordt gebruikt.

### <a name="error-domain-name-doesnt-resolve"></a>Fout: domeinnaam wordt niet opgelost

**Oorzaak 1:** VM's bevinden zich op een virtueel netwerk dat niet is gekoppeld aan het virtuele netwerk (VNET) waar het domein zich bevindt.

**Fix 1:** VNET-peering maken tussen de VNET waar VM's zijn ingericht en vnet waar de domeincontroller (DC) wordt uitgevoerd. Zie [Een virtueel netwerk peering maken - Resourcemanager, verschillende abonnementen](../virtual-network/create-peering-different-subscriptions.md).

**Oorzaak 2:** Wanneer u Azure Active Directory Domain Services (Azure AD DS) gebruikt, worden de DNS-serverinstellingen niet bijgewerkt om de beheerde domeincontrollers aan te wijzen.

**Fix 2:** Zie [DNS-instellingen voor het virtuele Azure-netwerk](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)bijwerken als u de DNS-instellingen voor het virtuele netwerk van Azure wilt bijwerken.

**Oorzaak 3:** De DNS-serverinstellingen van de netwerkinterface wijzen niet naar de juiste DNS-server in het virtuele netwerk.

**Fix 3:** Neem een van de volgende acties op te lossen, volgens de stappen in [DNS-servers wijzigen].
- Wijzig de DNS-serverinstellingen van de netwerkinterface in **Aangepast** met de stappen van [DNS-servers wijzigen](../virtual-network/virtual-network-network-interface.md#change-dns-servers) en geef de privé-IP-adressen op van de DNS-servers in het virtuele netwerk.
- Wijzig de DNS-serverinstellingen van de netwerkinterface in **Erven van virtueel netwerk** met de stappen van [DNS-servers wijzigen](../virtual-network/virtual-network-network-interface.md#change-dns-servers)en wijzig vervolgens de DNS-serverinstellingen van het virtuele netwerk met de stappen van [DNS-servers wijzigen.](../virtual-network/manage-virtual-network.md#change-dns-servers)

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows Virtual Desktop Agent en Windows Virtual Desktop Boot Loader zijn niet geïnstalleerd

De aanbevolen manier om VM's in te richten, is het gebruik van de sjabloon **voor het maken en inrichten van Windows Virtual Desktop-hostgroep.** De sjabloon installeert automatisch de Windows Virtual Desktop Agent en Windows Virtual Desktop Agent Boot Loader.

Volg deze instructies om te controleren of de onderdelen zijn geïnstalleerd en om te controleren op foutmeldingen.

1. Controleer of de twee componenten zijn geïnstalleerd door**programma's** > en -functies van **het Configuratiescherm** > **programma's en -functies in te**vinken. Als **Windows Virtual Desktop Agent** en Windows Virtual Desktop Agent Boot **Loader** niet zichtbaar zijn, worden ze niet op de vm geïnstalleerd.
2. Open **Verkenner** en navigeer naar **C:\Windows\Temp\ScriptLog.log**. Als het bestand ontbreekt, geeft dit aan dat de PowerShell DSC die de twee componenten heeft geïnstalleerd, niet kan worden uitgevoerd in de opgegeven beveiligingscontext.
3. Als het bestand **C:\Windows\Temp\ScriptLog.log** aanwezig is, opent u het bestand en controleert u op foutmeldingen.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Fout: Windows Virtual Desktop Agent en Windows Virtual Desktop Agent Boot Loader ontbreken. C:\Windows\Temp\ScriptLog.log ontbreekt ook

**Oorzaak 1:** Referenties die tijdens de invoer voor de Azure Resource Manager-sjabloon waren opgegeven, waren onjuist of de machtigingen waren onvoldoende.

**Fix 1:** Voeg de ontbrekende onderdelen handmatig toe aan de VM's met [PowerShell maken.](create-host-pools-powershell.md)

**Oorzaak 2:** PowerShell DSC kon starten en uitvoeren, maar kon niet worden voltooid omdat het zich niet kan aanmelden bij Windows Virtual Desktop en de benodigde informatie kan verkrijgen.

**Fix 2:** Bevestig de items in de volgende lijst.

- Zorg ervoor dat het account geen MFA heeft.
- Controleer of de tenantnaam juist is en dat de tenant bestaat in Windows Virtual Desktop.
- Bevestig dat het account ten minste RDS-inzendermachtigingen heeft.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Fout: Verificatie is mislukt, fout in C:\Windows\Temp\ScriptLog.log

**Oorzaak:** PowerShell DSC kon worden uitgevoerd, maar kon geen verbinding maken met Windows Virtual Desktop.

**Oplossing:** Bevestig de items in de volgende lijst.

- Registreer de VM's handmatig met de Windows Virtual Desktop-service.
- Bevestig dat account dat wordt gebruikt voor het maken van verbinding met Windows Virtual Desktop, machtigingen heeft voor de tenant om hostpools te maken.
- Bevestig dat het account geen MFA heeft.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop Agent registreert zich niet bij de Windows Virtual Desktop-service

Wanneer de Windows Virtual Desktop Agent voor het eerst is geïnstalleerd op vm's voor sessiehost (handmatig of via de Azure Resource Manager-sjabloon en PowerShell DSC), biedt dit een registratietoken. In de volgende sectie worden probleemproblemen voor problemen met de Windows Virtual Desktop Agent en het token opgelost.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Fout: de status die is opgeslagen in de cmdlet Get-RdsSessionHost geeft status weer als Niet beschikbaar

![Get-RdsSessionHost-cmdlet geeft de status weer als Niet beschikbaar.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Oorzaak:** De agent is niet in staat om zichzelf te updaten naar een nieuwe versie.

**Oplossing:** Volg deze instructies om de agent handmatig bij te werken.

1. Download een nieuwe versie van de agent op de vm van sessiehost.
2. Taakbeheer starten en op het tabblad Service de rdagentbootloader-service stoppen.
3. Voer het installatieprogramma uit voor de nieuwe versie van de Windows Virtual Desktop Agent.
4. Wanneer u om het registratietoken wordt gevraagd, verwijdert u de vermelding INVALID_TOKEN en drukt u op volgende (een nieuw token is niet vereist).
5. Voltooi de wizard installatie.
6. Open Taakbeheer en start de rdagentbootloaderservice.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Fout: Windows Virtual Desktop Agent registerentry IsRegistered toont een waarde van 0

**Oorzaak:** Registratietoken is verlopen of is gegenereerd met een vervaldatum van 999999.

**Oplossing:** Volg deze instructies om de fout in het agentregister op te lossen.

1. Als er al een registratietoken is, verwijdert u het met Remove-RDSRegistrationInfo.
2. Genereer nieuwe token met Rds-NewRegistrationInfo.
3. Controleer of de parameter -Onteigeninguren is ingesteld op 72 (maximale waarde is 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Fout: Windows Virtual Desktop-agent rapporteert geen heartbeat bij het uitvoeren van Get-RdsSessionHost

**Oorzaak 1:** RdAgentBootLoader-service is gestopt.

**Fix 1:** Start Taakbeheer en als het servicetabblad een gestopte status voor de RDAgentBootLoader-service rapporteert, start u de service.

**Oorzaak 2:** Poort 443 kan worden gesloten.

**Fix 2:** Volg deze instructies om poort 443 te openen.

1. Bevestig dat poort 443 is geopend door de PSPing-tool te downloaden van [Sysinternal-tools.](/sysinternals/downloads/psping/)
2. Installeer PSPing op de vm van sessiehost waar de agent wordt uitgevoerd.
3. Open de opdrachtprompt als beheerder en geef de opdracht hieronder uit:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Controleer of PSPing informatie heeft ontvangen van de RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Problemen met de Windows Virtual Desktop naast elkaar oplossen

De Windows Virtual Desktop side-by-side stack wordt automatisch geïnstalleerd met Windows Server 2019. Gebruik Microsoft Installer (MSI) om de stack naast elkaar te installeren op Microsoft Windows Server 2016 of Windows Server 2012 R2. Voor Microsoft Windows 10 is de Windows Virtual Desktop side-by-side stack ingeschakeld met **enablesxstackrs.ps1**.

Er zijn drie belangrijke manieren waarop de side-by-side stack wordt geïnstalleerd of ingeschakeld op vm's van sessiehostgroep:

- Met de sjabloon Azure Resource Manager **Maken en inrichten van nieuwe Windows Virtual Desktop-hostgroepsjabloon**
- Door opgenomen en ingeschakeld te zijn op de hoofdafbeelding
- Handmatig geïnstalleerd of ingeschakeld op elke VM (of met extensies/PowerShell)

Als u problemen ondervindt met de Windows Virtual Desktop side-by-side stack, typt u de **opdracht qwinsta** in de opdrachtprompt om te bevestigen dat de stack naast elkaar is geïnstalleerd of ingeschakeld.

De output van **qwinsta** vermeldt **rdp-sxs** in de uitvoer als de side-by-side stack is geïnstalleerd en ingeschakeld.

![Side-by-side stack geïnstalleerd of ingeschakeld met qwinsta vermeld als rdp-sxs in de uitgang.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Bekijk de onderstaande registervermeldingen en bevestig dat hun waarden overeenkomen. Als registersleutels ontbreken of waarden niet overeenkomen, volgt u de instructies in [Een hostgroep maken met PowerShell](create-host-pools-powershell.md) over het opnieuw installeren van de stack naast elkaar.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Fout: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE foutcode.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Oorzaak:** De stack naast elkaar is niet geïnstalleerd op de vm van sessiehost.

**Oplossing:** Volg deze instructies om de side-by-side stack op de vm van sessiehost te installeren.

1. Gebruik Extern bureaublad-protocol (RDP) om rechtstreeks in de vm van sessiehost te komen als lokale beheerder.
2. Download en importeer [de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) die u in uw PowerShell-sessie wilt gebruiken als u dit nog niet hebt gedaan, voer deze cmdlet uit om u aan te melden bij uw account:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Installeer de stack naast elkaar met [Een hostpool maken met PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Een Windows Virtual Desktop naast elkaar oplossen die niet oplost

Er zijn bekende omstandigheden die kunnen leiden tot de side-by-side stack defect:

- Niet de juiste volgorde van de stappen volgen om de side-by-side stack in te schakelen
- Automatische update naar Windows 10 Enhanced Versatile Disc (EVD)
- De rdsh-rol (Extern bureaublad-sessiehost) missen
- Running enablesxsstackrc.ps1 meerdere keren
- Uitvoeren van enablesxsstackrc.ps1 in een account dat geen lokale beheerdersrechten heeft

Met de instructies in deze sectie u de windows-virtuele bureaubladstack naast elkaar verwijderen. Zodra u de stack naast elkaar verwijdert, gaat u naar 'De VM registreren met de Windows Virtual Desktop-hostpool' in [Een hostgroep maken met PowerShell](create-host-pools-powershell.md) om de stack naast elkaar opnieuw te installeren.

De VM die wordt gebruikt om herstel uit te voeren, moet zich op hetzelfde subnet en domein bevinden als de VM met de defecte side-by-side stack.

Volg deze instructies om herstel uit hetzelfde subnet en domein uit te voeren:

1. Maak verbinding met het standaard Extern bureaublad-protocol (RDP) met de VM van waaruit de oplossing wordt toegepast.
2. Download PsExec https://docs.microsoft.com/sysinternals/downloads/psexecvan .
3. Rits het gedownloade bestand uit.
4. Opdrachtprompt starten als lokale beheerder.
5. Navigeer naar een map waar PsExec is uitgepakt.
6. Gebruik in opdrachtprompt de volgende opdracht:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname is de machinenaam van de VM met de defecte side-by-side stack.

7. Accepteer de PsExec-licentieovereenkomst door op Akkoord te klikken.

    ![Software licentie overeenkomst screenshot.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Dit dialoogvenster wordt alleen weergegeven wanneer PsExec voor het eerst wordt uitgevoerd.

8. Nadat de opdrachtpromptsessie wordt geopend op de VM met de defecte side-by-side stack, voert u qwinsta uit en bevestigt u dat er een item met de naam rdp-sxs beschikbaar is. Zo niet, dan is een side-by-side stack niet aanwezig op de VM, zodat het probleem niet is gekoppeld aan de side-by-side stack.

    ![Opdrachtprompt administrator](media/AdministratorCommandPrompt.png)

9. Voer de volgende opdracht uit, waarin Microsoft-componenten die op de VM zijn geïnstalleerd, worden weergegeven met de defecte stack naast elkaar.

    ```cmd
        wmic product get name
    ```

10. Voer de opdracht hieronder uit met productnamen van stap boven.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Verwijder alle producten die beginnen met 'Extern bureaublad'.

12. Nadat alle Windows Virtual Desktop-onderdelen zijn verwijderd, volgt u de instructies voor uw besturingssysteem:

13. Als uw besturingssysteem Windows Server is, start u de VM opnieuw waarop de defecte stack naast elkaar is geïnstalleerd (met Azure-portal of met het PsExec-hulpprogramma).

Als uw besturingssysteem Microsoft Windows 10 is, gaat u verder met de onderstaande instructies:

14. Open Van de VM met PsExec File Explorer en kopieer disablesxsstackrc.ps1 tot de systeemaandrijving van de VM met de defecte side-by-side stack.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname is de machinenaam van de VM met de defecte side-by-side stack.

15. Het aanbevolen proces: start vanuit het PsExec-gereedschap PowerShell en navigeer vanuit de vorige stap naar de map en voer disablesxsstackrc.ps1 uit. U ook de volgende cmdlets uitvoeren:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Wanneer de cmdlets klaar zijn, start u de VM opnieuw met de defecte stack naast elkaar.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>De licentiemodus voor Extern bureaublad is niet geconfigureerd

Als u zich aanmeldt bij Windows 10 Enterprise met meerdere sessies met een beheerdersaccount, ontvangt u mogelijk een melding met de tekst: "De licentiemodus voor Extern bureaublad is niet geconfigureerd, Extern bureaublad-services werkt binnen X-dagen niet meer. Gebruik op de Connection Broker-server Server Server om de licentiemodus voor Extern bureaublad op te geven."

Als de tijdslimiet verloopt, verschijnt er een foutbericht met de tekst:'De externe sessie is verbroken omdat er geen clienttoegangslicenties voor Extern bureaublad beschikbaar zijn voor deze computer.'

Als u een van deze berichten ziet, betekent dit dat de afbeelding niet de nieuwste Windows-updates heeft geïnstalleerd of dat u de licentiemodus Extern bureaublad instelt via groepsbeleid. Volg de stappen in de volgende secties om de groepsbeleidsinstelling te controleren, de versie van Windows 10 Enterprise-multisessie te identificeren en de bijbehorende update te installeren.  

>[!NOTE]
>Windows Virtual Desktop vereist alleen een CAL-licentie voor toegang voor Extern bureaublad-clienttoegang wanneer uw hostgroep Windows Server-sessiehosts bevat. Zie [Uw RDS-implementatie met clienttoegangslicenties](/windows-server/remote/remote-desktop-services/rds-client-access-license/)in licentie geven voor meer informatie over het configureren van een RDS CAL.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Groepsbeleidsinstelling voor extern bureaublad-licentiemodus uitschakelen

Controleer de groepsbeleidsinstelling door de groepsbeleidseditor in de virtuele machine te openen en te navigeren naar **beheersjablonen** > **Windows Components** > **Remote Desktop Services** > **Extern bureaublad-sessiehostlicentielicenties** > **Licensing** > **Stel de licentiemodus extern bureaublad in**. Als de groepsbeleidsinstelling is **ingeschakeld,** wijzigt u deze in **Uitgeschakeld**. Als het al is uitgeschakeld, laat het dan zoals-is.

>[!NOTE]
>Als u groepsbeleid instelt via uw domein, schakelt u deze instelling uit voor beleidsregels die zijn gericht op deze VM's met meerdere sessies van Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Bepalen welke versie van Windows 10 Enterprise multisessie gebruikt

Ga als het gaat om de versie van Windows 10 Enterprise-multisessie:

1. Meld u aan met uw beheerdersaccount.
2. Typ 'Over' in de zoekbalk naast het menu Start.
3. Selecteer **Over uw pc**.
4. Controleer het nummer naast 'Versie'. Het getal moet "1809" of "1903" zijn, zoals in de volgende afbeelding wordt weergegeven.

    ![Een screenshot van het Windows-specificatiesvenster. Het versienummer is blauw gemarkeerd.](media/windows-specifications.png)

Nu u uw versienummer kent, gaat u verder naar de desbetreffende sectie.

### <a name="version-1809"></a>Versie 1809

Als op uw versienummer '1809' staat, installeert u [de KB4516077-update](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Versie 1903

Implementeer het hostbesturingssysteem opnieuw met de nieuwste versie van de Windows 10-afbeelding, versie 1903, uit de Azure Gallery.

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van probleemoplossing voor het oplossen van problemen met Windows Virtual Desktop en de escalatietracks.
- Zie [Tenant en hostpool maken](troubleshoot-set-up-issues.md)als u problemen wilt oplossen tijdens het maken van een tenant en hostpool in een Windows Virtual Desktop-omgeving.
- Zie Configuratie van de [virtuele machine sessiehost](troubleshoot-vm-configuration.md)voor het oplossen van problemen tijdens het configureren van een virtuele machine (VM) in Windows Virtual Desktop.
- Zie [Windows Virtual Desktop-serviceverbindingen](troubleshoot-service-connection.md)voor het oplossen van problemen met Windows Virtual Desktop-clientverbindingen.
- Zie Problemen met [de Extern bureaublad-client oplossen](troubleshoot-client.md) als u problemen met Extern bureaublad-clients wilt oplossen
- Zie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)voor het oplossen van problemen bij het gebruik van PowerShell met Windows Virtual Desktop.
- Zie De Windows Virtual [Desktop-omgeving](environment-setup.md)voor meer informatie over de service.
- Zie [Zelfstudie: Problemen met resourcebeheersjabloonimplementaties](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)oplossen.
- Zie [Controlebewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controleacties.
- Zie [Implementatiebewerkingen weergeven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten tijdens de implementatie te bepalen.
