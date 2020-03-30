---
title: Problemen met de configuratieserver oplossen tijdens noodherstel van Vm's en fysieke servers naar Azure met Azure Site Recovery | Microsoft Documenten
description: In dit artikel vindt u informatie over probleemoplossing voor het implementeren van de configuratieserver voor noodherstel van Vm's en fysieke servers naar Azure met behulp van Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 25e2b488d3b6e7e5cabd1a71d1489efaf01231b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748551"
---
# <a name="troubleshoot-configuration-server-issues"></a>Problemen met de configuratieserver oplossen

Met dit artikel u problemen oplossen wanneer u de [configuratieserver azure site recovery](site-recovery-overview.md) implementeert en beheert. De configuratieserver fungeert als beheerserver. Gebruik de configuratieserver om noodherstel van on-premises Vm's en fysieke servers voor Noodgevallen in te stellen op Azure met behulp van Site Recovery. In de volgende secties worden de meest voorkomende fouten besproken die u ondervinden wanneer u een nieuwe configuratieserver toevoegt en wanneer u een configuratieserver beheert.

## <a name="registration-failures"></a>Registratiefouten

De bronmachine registreert zich bij de configuratieserver wanneer u de mobiliteitsagent installeert. U eventuele fouten tijdens deze stap debuggen door de volgende richtlijnen te volgen:

1. Open het c:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log file. (De map ProgramData kan een verborgen map zijn. Als u de map ProgramData niet ziet in Verkenner, schakelt u op het tabblad **Weergave** in de sectie **Weergeven/verbergen** het selectievakje **Verborgen items** in.) Fouten kunnen worden veroorzaakt door meerdere problemen.

2. Zoek naar de tekenreeks **Geen geldig IP-adres gevonden**. Als de tekenreeks wordt gevonden:
   1. Controleer of de gevraagde host-id dezelfde is als de host-id van de bronmachine.
   2. Controleer of de bronmachine ten minste één IP-adres heeft toegewezen aan de fysieke NIC. Om agentregistratie met de configuratieserver te laten slagen, moet de bronmachine ten minste één geldig IP V4-adres hebben dat is toegewezen aan de fysieke NIC.
   3. Voer een van de volgende opdrachten uit op de bronmachine om alle IP-adressen van de bronmachine op te halen:
      - Voor Windows:`> ipconfig /all`
      - Voor Linux:`# ifconfig -a`

3. Als de tekenreeks **Geen geldig IP-adres** is gevonden, zoekt u naar de tekenreeks **Reden=>NULL**. Deze fout treedt op als de bronmachine een lege host gebruikt om zich te registreren bij de configuratieserver. Als de tekenreeks wordt gevonden:
    - Nadat u de problemen hebt opgelost, volgt u richtlijnen in [De bronmachine registreren met de configuratieserver](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) om de registratie handmatig opnieuw te proberen.

4. Als de tekenreeks **Reason=>NULL** niet wordt gevonden, opent u op de bronmachine het bestand C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (De map ProgramData kan een verborgen map zijn. Als u de map ProgramData niet ziet in Verkenner, schakelt u op het tabblad **Weergave** in de sectie **Weergeven/verbergen** het selectievakje **Verborgen items** in.) Fouten kunnen worden veroorzaakt door meerdere problemen. 

5. Zoeken naar de aanknopingspunt **bericht aanvraag: (7) - Kon geen verbinding maken met server**. Als de tekenreeks wordt gevonden:
    1. Los de netwerkproblemen op tussen de bronmachine en de configuratieserver. Controleer of de configuratieserver bereikbaar is vanaf de bronmachine met behulp van netwerkhulpprogramma's zoals ping, traceroute of een webbrowser. Zorg ervoor dat de bronmachine de configuratieserver kan bereiken via poort 443.
    2. Controleer of firewallregels op de bronmachine de verbinding tussen de bronmachine en de configuratieserver blokkeren. Werk samen met uw netwerkbeheerders om verbindingsproblemen te deblokkeren.
    3. Controleer of de mappen die worden vermeld in [uitsluitingvan de siteherstelmap van antivirusprogramma's,](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) zijn uitgesloten van de antivirussoftware.
    4. Wanneer netwerkproblemen zijn opgelost, probeert u de registratie opnieuw door de richtlijnen in [De bronmachine registreren met de configuratieserver](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)te volgen.

6. Als de aanmaning van de **tekenreekspost: (7) - Geen verbinding met de server** is gevonden, zoekt u in hetzelfde logboekbestand naar de **tekenreeksaanvraag: (60) - Peer-certificaat kan niet worden geverifieerd met bepaalde CA-certificaten**. Deze fout kan optreden omdat het configuratieservercertificaat is verlopen of de bronmachine geen TLS 1.0- of hoger SSL-protocollen ondersteunt. Het kan ook optreden als een firewall SSL-communicatie tussen de bronmachine en de configuratieserver blokkeert. Als de tekenreeks wordt gevonden: 
    1. Als u dit wilt oplossen, maakt u verbinding met het IP-adres van de configuratieserver met behulp van een webbrowser op de bronmachine. Gebruik het URI-https:\/ \/<-configuratieserver IP-adres\>:443/. Zorg ervoor dat de bronmachine de configuratieserver kan bereiken via poort 443.
    2. Controleer of er firewallregels op de bronmachine moeten worden toegevoegd of verwijderd om de bronmachine met de configuratieserver te laten praten. Vanwege de verscheidenheid aan firewallsoftware die mogelijk in gebruik is, kunnen we niet alle vereiste firewallconfiguraties vermelden. Werk samen met uw netwerkbeheerders om verbindingsproblemen te deblokkeren.
    3. Controleer of de mappen die worden vermeld in [uitsluitingvan de siteherstelmap van antivirusprogramma's,](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) zijn uitgesloten van de antivirussoftware.  
    4. Nadat u de problemen hebt opgelost, probeert u de registratie opnieuw door richtlijnen te volgen in [De bronmachine registreren bij de configuratieserver.](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)

7. Op Linux, als de waarde van\>het platform in <INSTALLATION_DIR /etc/drscout.conf beschadigd is, mislukt de registratie. Open het bestand /var/log/ua_install.log om dit probleem te identificeren. Zoek naar de tekenreeks **Afbreken configuratie als VM_PLATFORM waarde is ofwel null of het is niet VmWare / Azure**. Het platform moet worden ingesteld op **VmWare** of **Azure.** Als het drscout.conf-bestand beschadigd is, raden we u aan [de mobiliteitsagent te verwijderen](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) en vervolgens de mobiliteitsagent opnieuw te installeren. Als de installatie niet wordt verwijderd, voert u de volgende stappen uit: a. Open het bestand Installation_Directory/verwijderen.sh en geef commentaar op de aanroep naar de functie **StopServices.**
    b. Open het bestand Installation_Directory/Vx/bin/uninstall.sh en geef **stop_services** commentaar op de oproep naar de stop_services-functie.
    c. Open het bestand Installation_Directory/Fx/uninstall.sh en geef commentaar op het hele gedeelte dat de Fx-service probeert te stoppen.
    d. [Verwijder de](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) mobiliteitsagent. Nadat u de installatie hebt uitgevoerd, start u het systeem opnieuw op en probeert u de mobiliteitsagent opnieuw te installeren.

## <a name="installation-failure-failed-to-load-accounts"></a>Installatiefout: kan accounts niet laden

Deze fout treedt op wanneer de service geen gegevens van de transportverbinding kan lezen wanneer deze de mobiliteitsagent installeert en zich registreert bij de configuratieserver. Als u het probleem wilt oplossen, moet u ervoor zorgen dat TLS 1.0 is ingeschakeld op uw bronmachine.

## <a name="vcenter-discovery-failures"></a>vCenterdetectiefouten

Als u vCenter-detectiefouten wilt oplossen, voegt u de vCenter-server toe aan de proxy-instellingen van de byPass-lijst. 

- Download de PsExec-tool [vanaf hier](https://aka.ms/PsExec) om toegang te krijgen tot de gebruikersinhoud van het systeem.
- Open Internet Explorer in de inhoud van de systeemgebruiker door de volgende opdrachtregel psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" uit te voeren
- Voeg proxy-instellingen toe in IE en start de tmanssvc-service opnieuw.
- Voer cd C:\Program Files\Microsoft Azure Site Recovery Provider uit om DRA-proxy-instellingen te configureren.
- Voer vervolgens DRCONFIGURATOR uit. EXE /configure /AddBypassUrls [IP-adres/FQDN van vCenter Server toevoegen tijdens **configuratieserverimplementatie configureren vCenter Server/vSphere ESXi-server]** [Configuration Server deployment](vmware-azure-deploy-configuration-server.md#configure-settings)

## <a name="change-the-ip-address-of-the-configuration-server"></a>Het IP-adres van de configuratieserver wijzigen

We raden u ten zeerste aan het IP-adres van een configuratieserver niet te wijzigen. Controleer of alle IP-adressen die aan de configuratieserver zijn toegewezen, statische IP-adressen zijn. Gebruik geen DHCP IP-adressen.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-token is ongeldig

Om deze fout te voorkomen, moet u ervoor zorgen dat de tijd op de klok van uw systeem niet meer dan 15 minuten verschilt van de lokale tijd. Voer het installatieprogramma opnieuw uit om de registratie te voltooien.

## <a name="failed-to-create-a-certificate"></a>Kan geen certificaat maken

Er kan geen certificaat worden gemaakt dat vereist is om siteherstel te verifiëren. De installatie opnieuw uitvoeren nadat u ervoor hebt gezorgd dat u de installatie uitvoert als lokale beheerder.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Het niet activeren van Windows-licentie van Server Standard EVALUATION naar Server Standard

1. Als onderdeel van de implementatie van de configuratieserver via OVF wordt een evaluatielicentie gebruikt, die 180 dagen geldig is. U moet deze licentie activeren voordat deze verloopt. Anders kan dit leiden tot frequente uitschakeling van de configuratieserver en dus leiden tot hinder voor replicatieactiviteiten.
2. Als u de Windows-licentie niet activeren, neem dan contact op met [het Windows-ondersteuningsteam](https://aka.ms/Windows_Support) om het probleem op te lossen.

## <a name="register-source-machine-with-configuration-server"></a>Bronmachine registreren met configuratieserver

### <a name="if-the-source-machine-runs-windows"></a>Als de bronmachine Windows uitvoert

Voer de volgende opdracht uit op de bronmachine:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Instelling | Details
--- | ---
Gebruik | UnifiedAgentConfigurator.exe /CSEndPoint <-configuratieserver IP-adres\> /PassphraseFilePath <wachtwoordzinbestandspad\>
Agentconfiguratielogboeken | Gelegen onder %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Verplichte parameter. Hiermee geeft u het IP-adres van de configuratieserver op. Gebruik een geldig IP-adres.
/PassphraseFilePath |  Verplicht. De locatie van de wachtwoordzin. Gebruik een geldig UNC- of lokaal bestandspad.

### <a name="if-the-source-machine-runs-linux"></a>Als de bronmachine Linux draait

Voer de volgende opdracht uit op de bronmachine:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Instelling | Details
--- | ---
Gebruik | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <-i-configuratieserver IP-adres\> -P <wachtwoordzinbestandspad\>
-i | Verplichte parameter. Hiermee geeft u het IP-adres van de configuratieserver op. Gebruik een geldig IP-adres.
-P |  Verplicht. Het volledige bestandspad van het bestand waarin de wachtwoordzin wordt opgeslagen. Gebruik een geldige map.

## <a name="unable-to-configure-the-configuration-server"></a>Kan de configuratieserver niet configureren

Als u andere toepassingen dan de configuratieserver op de virtuele machine installeert, u het hoofddoel mogelijk niet configureren. 

De configuratieserver moet een server voor één doel zijn en deze als gedeelde server wordt niet ondersteund. 

Zie de veelgestelde vragen over de configuratie in [Een configuratieserver implementeren](vmware-azure-deploy-configuration-server.md#faqs)voor meer informatie. 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>De verouderde items voor beveiligde items verwijderen uit de configuratieserverdatabase 

Als u verouderde beveiligde machine op de configuratieserver wilt verwijderen, gebruikt u de volgende stappen. 
 
1. Ga als volgt te werk om de bronmachine en het IP-adres van de verouderde vermelding te bepalen: 

    1. Open de MYSQL-cmdline in de beheerdersmodus. 
    2. Voer de volgende opdrachten uit. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Dit retourneert de lijst van geregistreerde machines, samen met hun IP-adressen en laatste hartslag. Zoek de host met verouderde replicatieparen.

2. Open een opdrachtprompt met verhoogde bevoegdheid en navigeer naar C:\ProgramData\ASR\home\svsystems\bin. 
4. Als u de geregistreerde hosts-gegevens en de verouderde invoergegevens van de configuratieserver wilt verwijderen, voert u de volgende opdracht uit met de bronmachine en het IP-adres van de verouderde vermelding. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Als u een bronserververmelding hebt van "OnPrem-VM01" met een ip-adres van 10.0.0.4, gebruikt u in plaats daarvan de volgende opdracht.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Start de volgende services opnieuw op de bronmachine om opnieuw te registreren bij de configuratieserver. 
 
    - InMage Scout-toepassingsservice
    - InMage Scout VX Agent - Sentinel/Buitenpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Upgrade mislukt wanneer de services niet stoppen

De upgrade van de configuratieserver mislukt wanneer bepaalde services niet stoppen. 

Als u het probleem wilt identificeren, navigeert u naar C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile op de configuratieserver. Als u volgende fouten vindt, gebruikt u de onderstaande stappen om het probleem op te lossen: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Ga als ander op zoek naar het probleem:

De volgende services handmatig stoppen:

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Buitenpost, 
- Microsoft Azure Recovery Services-agent, 
- Microsoft Azure-siteherstelservice, 
- tmansvc tmansvc
  
Als u de configuratieserver wilt bijwerken, voert u de [uniforme installatie](service-updates-how-to.md#links-to-currently-supported-update-rollups) opnieuw uit.

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory-fout voor het maken van toepassingen

U hebt onvoldoende machtigingen om een toepassing te maken in Azure Active Directory (AAD) met de [ova-sjabloon (Open Virtualisatie Application)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) .You have inadequate permissions to create an application in Azure Active Directory (AAD) using the Open Virtualization Application (OVA) template.

Als u het probleem wilt oplossen, meldt u zich aan bij de Azure-portal en gaat u op een van de volgende handelingen:

- Vraag de rol Application Developer aan in AAD. Zie [Beheerdersrolmachtigingen in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de rol Toepassingsontwikkelaar.
- Controleren of de **gebruiker toepassingsvlag kan maken,** is ingesteld op *true* in AAD. Zie [De portal gebruiken om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)voor meer informatie.

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Processerver/Master Target kan niet communiceren met de configuratieserver 

De MT-modules (Process server) en Master Target (MT) kunnen niet communiceren met de configuratieserver (CS) en hun status wordt weergegeven als niet verbonden op azure-portal.

Dit is meestal te wijten aan een fout met poort 443. Gebruik de volgende stappen om de poort te deblokkeren en de communicatie met de CS opnieuw in te schakelen.

**Controleer of de MARS-agent wordt aangeroepen door de Master Target-agent**

Ga naar een traceert dat vergelijkbaar is met het volgende in de logboeken van de hoofddoelagent om te controleren of de hoofddoelagent een TCP-sessie voor het IP-adres van de configuratieserver kan maken:

TCP \<Vervang IP met CS IP hier \<>:52739 Vervang IP met CS IP hier>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT - IP vervangen door CS-IP

Als u sporen vindt die vergelijkbaar zijn met de volgende in de MT-agentlogboeken, rapporteert de MT-agent fouten op poort 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Deze fout kan worden aangetroffen wanneer andere toepassingen ook poort 443 gebruiken of als gevolg van een firewall-instelling die de poort blokkeert.

Ga als ander op zoek naar het probleem:

- Controleer of poort 443 niet wordt geblokkeerd door uw firewall.
- Als de poort onbereikbaar is vanwege een andere toepassing die die poort gebruikt, stopt u en verwijdert u de app.
  - Als het stoppen van de app niet haalbaar is, stelt u een nieuwe schone CS in.
- Start de configuratieserver opnieuw.
- Start de IIS-service opnieuw.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Configuratieserver is niet verbonden vanwege onjuiste UUID-vermeldingen

Deze fout kan optreden wanneer er meerdere configuratieserverinstantie UUID-vermeldingen in de database zijn. Het probleem treedt vaak op wanneer u de vm van de configuratieserver kloont.

Ga als ander op zoek naar het probleem:

1. Verwijder verouderde/oude CS-VM uit vCenter. Zie [Servers verwijderen en beveiliging uitschakelen](site-recovery-manage-registration-and-protection.md)voor meer informatie.
2. Meld u aan bij de vm van de configuratieserver en maak verbinding met de MySQL svsdb1-database. 
3. Voer de volgende query uit:

    > [!IMPORTANT]
    >
    > Controleer of u de UUID-gegevens van de gekloonde configuratieserver of de verouderde invoer van de configuratieserver invoert die niet langer wordt gebruikt om virtuele machines te beschermen. Als u een onjuiste UUID invoert, verliest u de informatie voor alle bestaande beveiligde items.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Vernieuw de portalpagina.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Een oneindige aanmeldingslus treedt op bij het invoeren van uw referenties

Na het invoeren van de juiste gebruikersnaam en wachtwoord op de configuratieserver OVF, blijft Azure aanmelden om de juiste referenties.

Dit probleem kan optreden wanneer de systeemtijd onjuist is.

Ga als ander op zoek naar het probleem:

Stel de juiste tijd in op de computer en probeer het aanmeldingsformulier opnieuw in. 
 