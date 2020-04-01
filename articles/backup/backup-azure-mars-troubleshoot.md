---
title: Problemen met de Azure Backup-agent oplossen
description: In dit artikel leest u hoe u problemen oplossen met de installatie en registratie van de Azure Backup-agent.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: a15f8a4531bc31dab5b99e125454b0d9c4fd4521
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421278"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Problemen oplossen met de MARS-agent (Microsoft Azure Recovery Services)

In dit artikel wordt beschreven hoe u fouten oplossen die u zien tijdens de configuratie, registratie, back-up en herstel.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

We raden u aan het volgende te controleren voordat u de Azure Recovery Services -agent (Azure Recovery Services) op de problemen brengen:

- [Zorg ervoor dat de MARS-agent up-to-date is.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat u netwerkconnectiviteit hebt tussen de MARS-agent en Azure.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Zorg ervoor dat MARS wordt uitgevoerd (in de serviceconsole). Als dat nodig is, start u de bewerking opnieuw en opnieuw.
- [Zorg ervoor dat 5% tot 10% vrije volumeruimte beschikbaar is op de locatie van de krasmap.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Controleer of een ander proces of antivirussoftware Azure Backup verstoort.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- Als geplande back-up mislukt, maar handmatige back-upwerkt, raadpleegt u [Back-ups die niet volgens planning worden uitgevoerd.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Zorg ervoor dat uw besturingssysteem de nieuwste updates heeft.
- [Zorg ervoor dat niet-ondersteunde stations en bestanden met niet-ondersteunde kenmerken geen back-up kunnen maken.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Zorg ervoor dat de klok op het beveiligde systeem is geconfigureerd in de juiste tijdzone.
- [Zorg ervoor dat .NET Framework 4.5.2 of hoger op de server is geïnstalleerd.](https://www.microsoft.com/download/details.aspx?id=30653)
- Als u uw server opnieuw wilt registreren in een kluis:
  - Zorg ervoor dat de agent is verwijderd op de server en dat deze is verwijderd uit de portal.
  - Gebruik dezelfde wachtwoordzin die in eerste instantie werd gebruikt om de server te registreren.
- Voor offline back-ups moet u ervoor zorgen dat Azure PowerShell 3.7.0 is geïnstalleerd op zowel de bron als de kopieercomputer voordat u de back-up start.
- Zie [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)als de back-upagent op een virtuele azure-machine wordt uitgevoerd.

## <a name="invalid-vault-credentials-provided"></a>Er zijn ongeldige kluisreferenties ingevoerd

**Foutbericht:** Ongeldige vault credentials verstrekt. Het bestand is beschadigd of de nieuwste referenties zijn niet gekoppeld aan de Recovery Service. (ID: 34513)

| Oorzaak | Aanbevolen acties |
| ---     | ---    |
| **Vault-referenties zijn niet geldig** <br/> <br/> Vault-referentiebestanden kunnen beschadigd zijn of zijn verlopen. (Ze zijn bijvoorbeeld meer dan 48 uur voor de registratie gedownload.)| Download nieuwe referenties uit de vault van Recovery Services op de Azure-portal. (Zie stap 6 in de sectie [De MARS-agent downloaden.)](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) Neem vervolgens de volgende stappen, indien van toepassing: <ul><li> Als u MARS al hebt geïnstalleerd en geregistreerd, opent u de MMC-console van Microsoft Azure Backup Agent en selecteert u **Server registreren** in het deelvenster **Handelingen** om de registratie met de nieuwe referenties te voltooien. <br/> <li> Als de nieuwe installatie mislukt, probeert u opnieuw te installeren met de nieuwe referenties.</ul> **Opmerking:** Als er meerdere vault credential files zijn gedownload, is alleen het laatste bestand geldig voor de komende 48 uur. We raden u aan een nieuw kluisreferentiebestand te downloaden.
| **Proxyserver/firewall blokkeert registratie** <br/>of <br/>**Geen internetverbinding** <br/><br/> Als uw machine of proxyserver een beperkte internetverbinding heeft en u geen toegang hebt voor de benodigde URL's, mislukt de registratie.| Neem de volgende stappen:<br/> <ul><li> Werk samen met uw IT-team om ervoor te zorgen dat het systeem een internetverbinding heeft.<li> Als u geen proxyserver hebt, moet u ervoor zorgen dat de proxyoptie niet is geselecteerd wanneer u de agent registreert. [Controleer uw proxy-instellingen](#verifying-proxy-settings-for-windows).<li> Als u wel een firewall/proxyserver hebt, werkt u samen met uw netwerkteam om ervoor te zorgen dat deze URL's en IP-adressen toegang hebben:<br/> <br> **Urls**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-adressen**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Probeer je opnieuw te registreren nadat je de vorige stappen voor het oplossen van problemen hebt voltooid.<br></br> Als de verbinding met U via Azure ExpressRoute is, controleert u of de instellingen zijn geconfigureerd zoals beschreven in [azure ExpressRoute-ondersteuning.](backup-support-matrix-mars-agent.md#azure-expressroute-support)
| **Antivirussoftware blokkeert registratie** | Als u antivirussoftware op de server hebt geïnstalleerd, voegt u de nodige uitsluitingsregels toe aan de antivirusscan voor deze bestanden en mappen: <br/><ul> <li> CBengine.exe <li> CSC.exe (CSC.exe)<li> De krasmap. De standaardlocatie is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> De map met opslaglocatie bij C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Extra aanbevelingen

- Ga naar C:/Windows/Temp en controleer of er meer dan 60.000 of 65.000 bestanden zijn met de .tmp-extensie. Als die er zijn, verwijder deze bestanden.
- Zorg ervoor dat de datum en tijd van de machine overeenkomen met de lokale tijdzone.
- Zorg ervoor dat [deze sites](install-mars-agent.md#verify-internet-access) worden toegevoegd aan uw vertrouwde sites in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Proxy-instellingen voor Windows verifiëren

1. Download PsExec van de [Sysinternals-pagina.](https://docs.microsoft.com/sysinternals/downloads/psexec)
1. Uitvoeren `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` vanaf een opdrachtprompt met verhoogde bevoegdheid.

   Met deze opdracht wordt Internet Explorer geopend.
1. Ga naar**Connections** > **LAN-instellingen** **voor extra** > **internetopties** > .
1. Controleer de proxy-instellingen voor het systeemaccount.
1. Als er geen proxy is geconfigureerd en proxydetails worden verstrekt, verwijdert u de details.
1. Als een proxy is geconfigureerd en de proxygegevens onjuist zijn, moet u ervoor zorgen dat de **IP-** en **poortgegevens** van de proxy correct zijn.
1. Sluit Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Kan het kluisreferentiebestand niet downloaden

| Fout   | Aanbevolen acties |
| ---     | ---    |
|Kan het kluisreferentiebestand niet downloaden. (ID: 403) | <ul><li> Probeer de vault credentials te downloaden met een andere browser of neem de volgende stappen: <ul><li> Start Internet Explorer. Selecteer F12. </li><li> Ga naar het tabblad **Netwerk** en schakel de cache en cookies uit. </li> <li> Vernieuw de pagina.<br></li></ul> <li> Controleer of het abonnement is uitgeschakeld/verlopen.<br></li> <li> Controleer of een firewallregel de download blokkeert. <br></li> <li> Zorg ervoor dat u de limiet op de kluis niet hebt uitgeput (50 machines per kluis).<br></li>  <li> Controleer of de gebruiker over de Azure Backup-machtigingen beschikt die nodig zijn om vault-referenties te downloaden en een server met de kluis te registreren. Zie [Toegangsbeheer op basis van rollen gebruiken om herstelpunten voor Azure Backup te beheren.](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>De Microsoft Azure Recovery Services-agent kan geen verbinding maken met Microsoft Azure Backup

| Fout  | Mogelijke oorzaak | Aanbevolen acties |
| ---     | ---     | ---    |
| <br /><ul><li>De Microsoft Azure Recovery Service Agent kan geen verbinding maken met Microsoft Azure Backup. (ID: 100050) Controleer uw netwerkinstellingen en zorg ervoor dat u verbinding maken met internet.<li>(407) Proxy verificatie vereist. |Een proxy blokkeert de verbinding. |  <ul><li>Ga in Internet Explorer naar**Internet-opties** > **Beveiliging** > **internet** **.** >  Selecteer **Aangepast niveau** en blader omlaag naar de sectie Bestand **downloaden.** Selecteer **Inschakelen**.<p>Mogelijk moet u ook [URL's en IP-adressen](install-mars-agent.md#verify-internet-access) toevoegen aan uw vertrouwde sites in Internet Explorer.<li>Wijzig de instellingen om een proxyserver te gebruiken. Geef vervolgens de details van de proxyserver op.<li> Als uw machine beperkte internettoegang heeft, moet u ervoor zorgen dat firewall-instellingen op de machine of proxy deze [URL's en IP-adressen](install-mars-agent.md#verify-internet-access)toestaan. <li>Als u antivirussoftware op de server hebt geïnstalleerd, sluit u deze bestanden uit van de antivirusscan: <ul><li>CBEngine.exe (in plaats van dpmra.exe).<li>CSC.exe (gerelateerd aan .NET Framework). Er is een CSC.exe voor elke .NET Framework-versie die op de server is geïnstalleerd. Sluit CSC.exe-bestanden uit voor alle versies van .NET Framework op de betreffende server. <li>De krasmap of cachelocatie. <br>De standaardlocatie voor de krasmap of het cachepad is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>De map met opslaglocatie bij C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Instellen van de versleutelingssleutel voor veilige back-ups is mislukt

| Fout | Mogelijke oorzaken | Aanbevolen acties |
| ---     | ---     | ---    |
| <br />Kan de versleutelingssleutel niet instellen voor beveiligde back-ups. Activering is niet volledig gelukt, maar de wachtwoordzin voor versleuteling is opgeslagen in het volgende bestand. |<li>De server is al geregistreerd bij een andere kluis.<li>Tijdens de configuratie is de wachtwoordzin beschadigd.| De server uitdep- en registereer de server uit de kluis en registreer deze opnieuw met een nieuwe wachtwoordzin.

## <a name="the-activation-did-not-complete-successfully"></a>De activering is niet voltooid

| Fout  | Mogelijke oorzaken | Aanbevolen acties |
|---------|---------|---------|
|<br />De activering is niet voltooid. De huidige bewerking is mislukt als gevolg van een interne servicefout [0x1FC07]. Probeer de bewerking na enige tijd opnieuw. Neem contact op met Microsoft Ondersteuning als het probleem zich blijft voordoen.     | <li> De krasmap bevindt zich op een volume dat niet genoeg ruimte heeft. <li> De krasmap is verkeerd verplaatst. <li> Het Bestand OnlineBackup.KEK ontbreekt.         | <li>Upgrade naar de [nieuwste versie](https://aka.ms/azurebackup_agent) van de MARS-agent.<li>Verplaats de krasmap of cachelocatie naar een volume met vrije ruimte die tussen 5% en 10% van de totale grootte van de back-upgegevens bedraagt. Als u de cachelocatie correct wilt verplaatsen, raadpleegt u de stappen in [algemene vragen over het maken van back-ups van bestanden en mappen.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)<li> Zorg ervoor dat het Bestand OnlineBackup.KEK aanwezig is. <br>*De standaardlocatie voor de krasmap of het cachepad is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Wachtwoordzin voor versleuteling niet correct geconfigureerd

| Fout  | Mogelijke oorzaken | Aanbevolen acties |
|---------|---------|---------|
| <br />Fout 34506. De versleutelingswachtwoordzin die op deze computer is opgeslagen, is niet correct geconfigureerd.    | <li> De krasmap bevindt zich op een volume dat niet genoeg ruimte heeft. <li> De krasmap is verkeerd verplaatst. <li> Het Bestand OnlineBackup.KEK ontbreekt.        | <li>Upgrade naar de [nieuwste versie](https://aka.ms/azurebackup_agent) van de MARS-agent.<li>Verplaats de krasmap of cachelocatie naar een volume met vrije ruimte die tussen 5% en 10% van de totale grootte van de back-upgegevens bedraagt. Als u de cachelocatie correct wilt verplaatsen, raadpleegt u de stappen in [algemene vragen over het maken van back-ups van bestanden en mappen.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)<li> Zorg ervoor dat het Bestand OnlineBackup.KEK aanwezig is. <br>*De standaardlocatie voor de krasmap of het cachepad is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Back-ups worden niet volgens planning uitgevoerd

Als geplande back-ups niet automatisch worden geactiveerd, maar handmatige back-ups correct werken, probeert u de volgende acties:

- Zorg ervoor dat het back-upschema van Windows Server niet in strijd is met het back-upschema voor Azure-bestanden en mappen.

- Zorg ervoor dat de online back-upstatus is ingesteld **op Inschakelen**. Ga als volgt te werk om de status te verifiëren:

  1. Vouw in Taakplanner **Microsoft** uit en selecteer **Online back-up**.
  1. Dubbelklik op **Microsoft-OnlineBackup** en ga naar het tabblad **Triggers.**
  1. Controleer of de status is ingesteld op **Ingeschakeld**. Als dit niet het is, selecteert u **Bewerken,** selecteert u **Ingeschakeld**en selecteert u **OK**.

- Controleer of het gebruikersaccount dat is geselecteerd voor het uitvoeren van de taak de groep **SYSTEEM** of **Lokale beheerders** op de server is. Als u het gebruikersaccount wilt verifiëren, gaat u naar het tabblad **Algemeen** en controleert u de **beveiligingsopties.**

- Zorg ervoor dat PowerShell 3.0 of hoger op de server is geïnstalleerd. Als u de PowerShell-versie wilt controleren, voert u deze opdracht uit en controleert u of het `Major` versienummer 3 of hoger is:

  `$PSVersionTable.PSVersion`

- Controleer of dit pad `PSMODULEPATH` deel uitmaakt van de omgevingsvariabele:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Als het PowerShell-uitvoeringsbeleid `restricted`is ingesteld op `LocalMachine` , kan de PowerShell-cmdlet die de back-uptaak activeert, mislukken. Voer deze opdrachten in de verhoogde modus uit om `Unrestricted` `RemoteSigned`het uitvoeringsbeleid te controleren en in te stellen op:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Zorg ervoor dat er geen ontbrekende of beschadigde PowerShell-module MSOnlineBackup-bestanden zijn. Als er ontbrekende of corrupte bestanden zijn, neemt u de volgende stappen:

  1. Kopieer de MAP MSOnlineBackup van elke machine met een MARS-agent die naar behoren werkt, vanuit C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Plak de gekopieerde bestanden op dezelfde maplocatie (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules) op de problematische machine.

     Als er al een MSOnlineBackup-map op de machine staat, plakt u de bestanden erin of vervangt u bestaande bestanden.

> [!TIP]
> Als u ervoor wilt zorgen dat wijzigingen consistent worden toegepast, start u de server opnieuw nadat u de voorgaande stappen hebt uitgevoerd.

## <a name="resource-not-provisioned-in-service-stamp"></a>Resource die niet in de servicestempel is ingericht

Fout | Mogelijke oorzaken | Aanbevolen acties
--- | --- | ---
De huidige bewerking is mislukt als gevolg van een interne servicefout 'Resource niet in servicestempel is ingericht'. Probeer de bewerking na verloop van tijd opnieuw. (ID: 230006) | De beveiligde server is hernoemd. | <li> Wijzig de naam van de server terug naar de oorspronkelijke naam zoals geregistreerd bij de kluis. <br> <li> Registreer de server opnieuw in de kluis met de nieuwe naam.


## <a name="troubleshoot-restore-problems"></a>Herstelproblemen oplossen

Azure Backup kan het herstelvolume mogelijk niet met succes monteren, zelfs niet na enkele minuten. En u tijdens het proces foutmeldingen ontvangen. Als u normaal wilt beginnen met herstellen, neemt u de volgende stappen:

1. Annuleer het montageproces als het enkele minuten actief is.

2. Controleer of u de nieuwste versie van de back-upagent hebt. Als u de versie wilt controleren, selecteert u in het deelvenster **Handelingen** van de MARS-console de optie **Over Microsoft Azure Recovery Services Agent**. Controleer of het **versienummer** gelijk is aan of hoger is dan de versie die in [dit artikel](https://go.microsoft.com/fwlink/?linkid=229525)wordt genoemd. Selecteer deze link om de nieuwste versie te [downloaden.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. Ga naar **Device Manager** > **Storage-controllers** en zoek **Microsoft iSCSI Initiator**. Als u het vindt, gaat u direct naar stap 7.

4. Als u de Microsoft iSCSI Initiator-service niet vinden, probeert **Unknown Device** u een vermelding te vinden onder Device Manager **Device Manager** > **Storage-controllers** met de naam Onbekend apparaat met hardware-id **ROOT\ISCSIPRT**.

5. Klik met de rechtermuisknop op **Onbekend apparaat** en selecteer **Stuurprogrammasoftware bijwerken**.

6. Werk het stuurprogramma bij door de optie te selecteren om automatisch te **zoeken naar bijgewerkte stuurprogrammasoftware.** Deze update moet **onbekend apparaat** wijzigen in **Microsoft iSCSI Initiator:**

    ![Schermafbeelding van Azure Backup Device Manager, met opslagcontrollers gemarkeerd](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Ga naar De Microsoft**iSCSI Initiator-service** **(Task Manager** > **Services)** > (Lokaal):

    ![Schermafbeelding van Azure Backup Task Manager, met Lokaal beheer van Services](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Start de Microsoft iSCSI Initiator-service opnieuw. Klik hiervoor met de rechtermuisknop op de service en selecteer **Stoppen**. Klik er vervolgens met de rechtermuisknop op en selecteer **Start**.

9. Probeer herstel opnieuw uit met [Instant Restore](backup-instant-restore-capability.md).

Als het herstel nog steeds mislukt, start u de server of client opnieuw. Als u niet opnieuw wilt starten of als het herstel nog steeds mislukt, zelfs nadat u de server opnieuw hebt opgestart, probeert u [te herstellen van een andere machine.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="troubleshoot-cache-problems"></a>Cacheproblemen oplossen

Back-upbewerking kan mislukken als de cachemap (ook wel krasmap genoemd) onjuist is geconfigureerd, vereisten ontbreekt of beperkte toegang heeft.

### <a name="prerequisites"></a>Vereisten

Als de cachemap van MARS-agents succesvol is, moet aan de onderstaande vereisten worden voldaan:

- [Zorg ervoor dat 5% tot 10% vrije volumeruimte beschikbaar is op de locatie van de krasmap](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Ervoor zorgen dat de locatie van de krasmap geldig en toegankelijk is](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Ervoor zorgen dat bestandskenmerken in de cachemap worden ondersteund](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Ervoor zorgen dat de toegewezen opslagruimte voor schaduwkopieën voldoende is voor een back-upproces](#increase-shadow-copy-storage)
- [Zorg ervoor dat er geen andere processen (bijvoorbeeld antivirussoftware) zijn die de toegang tot de cachemap beperken](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Schaduwkopieopslag vergroten

Back-upbewerkingen kunnen mislukken als er onvoldoende schaduwkopieopslagruimte is die nodig is om de gegevensbron te beschermen. Als u dit probleem wilt oplossen, verhoogt u de opslagruimte voor schaduwkopieën op het beveiligde volume met behulp van vssadmin zoals hieronder wordt weergegeven:

- Controleer de huidige schaduwopslagruimte vanuit de opdrachtprompt met verhoogde bevoegdheid:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Verhoog de schaduwopslagruimte met de opdracht Hieronder:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Een ander proces of antivirussoftware die de toegang tot de cachemap blokkeert

Als u antivirussoftware op de server hebt geïnstalleerd, voegt u de nodige uitsluitingsregels toe aan de antivirusscan voor deze bestanden en mappen:  

- De krasmap. De standaardlocatie is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- De map met opslaglocatie bij C:\Program Files\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- CSC.exe (CSC.exe)

## <a name="common-issues"></a>Algemene problemen

In deze sectie worden de veelvoorkomende fouten die u tegenkomt tijdens het gebruik van mars-agent.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Foutbericht | Aanbevolen actie |
-- | --
De Microsoft Azure Recovery Services-agent heeft geen toegang gekregen tot de controlesom van de back-up die is opgeslagen op de scratchlocatie | Als u dit probleem wilt oplossen, voert u het onderstaande uit en start u de server opnieuw op <br/> - [Controleer of er een antivirus of andere processen vergrendelen van de scratch locatie bestanden](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controleer of de kraslocatie geldig en toegankelijk is voor de Mars-agent.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Foutbericht | Aanbevolen actie |
-- | --
De Microsoft Azure Recovery Services-agent heeft geen toegang gekregen tot de scratchlocatie om de VHD te initialiseren | Als u dit probleem wilt oplossen, voert u het onderstaande uit en start u de server opnieuw op <br/> - [Controleer of er een antivirus of andere processen vergrendelen van de scratch locatie bestanden](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controleer of de kraslocatie geldig en toegankelijk is voor de Mars-agent.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace SalLowDiskSpace

Foutbericht | Aanbevolen actie |
-- | --
Back-up mislukt als gevolg van onvoldoende opslag in volume waar de krasmap zich bevindt | Als u dit probleem wilt oplossen, controleert u de onderstaande stappen en probeert u de bewerking opnieuw:<br/>- [Zorg ervoor dat MARS-agent op de hoogte is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Opslagproblemen verifiëren en oplossen die van invloed zijn op de krasruimte van de back-up](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Foutbericht | Aanbevolen actie |
-- | --
Kan wijzigingen niet vinden in een bestand. Dit kan verschillende redenen hebben. Voer de bewerking opnieuw uit | Als u dit probleem wilt oplossen, controleert u de onderstaande stappen en probeert u de bewerking opnieuw:<br/> - [Zorg ervoor dat MARS-agent op de hoogte is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Opslagproblemen verifiëren en oplossen die van invloed zijn op de krasruimte van de back-up](#prerequisites)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van [een back-up van Windows Server met de Azure Backup-agent.](tutorial-backup-windows-server-to-azure.md)
- Als u een back-up wilt herstellen, raadpleegt u [bestanden herstellen naar een Windows-machine.](backup-azure-restore-windows-server.md)
