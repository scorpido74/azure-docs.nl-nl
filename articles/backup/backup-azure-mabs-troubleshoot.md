---
title: Problemen met Azure Backup Server oplossen
description: Problemen met de installatie, registratie van Azure Backup Server en back-up en herstel van toepassingsworkloads oplossen.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: deff49a7d00a335c396a6fa36d3846ef353331c5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421299"
---
# <a name="troubleshoot-azure-backup-server"></a>Problemen met Azure Backup Server oplossen

Gebruik de gegevens in de volgende tabellen om fouten op te lossen die u tegenkomt tijdens het gebruik van Azure Backup Server.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

We raden u aan de onderstaande validatie uit te voeren voordat u problemen oplost met Microsoft Azure Backup Server (MABS):

- [Controleren of de MARS-agent (Microsoft Azure Recovery Services) up-to-date is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat er netwerkverbinding is tussen de MARS-agent en Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Controleer of Microsoft Azure Recovery Services wordt uitgevoerd (in Service-console). Indien nodig de bewerking opnieuw opstarten en opnieuw proberen
- [Zorg ervoor dat er 5-10% ruimte vrij is op de locatie van de scratch-map](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Als de registratie mislukt, moet u ervoor zorgen dat de server waarop u Azure Backup Server probeert te installeren, nog niet is geregistreerd bij een andere kluis
- Als een push-installatie mislukt, controleert u of de DPM-agent al aanwezig is. Zo ja, verwijdert u de agent en start u de installatie opnieuw
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Controleren of de SQL Agent-service wordt uitgevoerd en ingesteld op automatisch in de MABS-server<br>

## <a name="invalid-vault-credentials-provided"></a>Er zijn ongeldige kluisreferenties ingevoerd

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Registreren bij een kluis | Er zijn ongeldige kluisreferenties opgegeven. Het bestand is beschadigd of heeft niet de nieuwste referenties die zijn gekoppeld aan de herstelservice. | Aanbevolen actie: <br> <ul><li> Download het nieuwste bestand met referenties uit de kluis en probeer het opnieuw. <br>(OF)</li> <li> Als de vorige actie niet heeft gewerkt, probeert u de referenties te downloaden naar een andere lokale map of een nieuwe kluis te maken. <br>(OF)</li> <li> Probeer de datum- en tijdinstellingen zoals beschreven in [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided)bij te werken. <br>(OF)</li> <li> Controleer of c:\windows\temp meer dan 65000 bestanden heeft. Verplaats verouderde bestanden naar een andere locatie of verwijder de items in de map Temp. <br>(OF)</li> <li> Controleer de status van certificaten. <br> a. Open **Computercertificaten beheren** (in het Configuratiescherm). <br> b. Vouw het **persoonlijke** knooppunt en de **certificaten**voor het onderliggende knooppunt uit.<br> c.  Verwijder het certificaat **Windows Azure Tools**. <br> d. Probeer de registratie opnieuw in de Azure Backup-client. <br> (OF) </li> <li> Controleer of er een groepsbeleid is. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replica is inconsistent

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Replica is inconsistent | Controleer of de optie automatische consistentiecontrole in de wizard Beveiligingsgroep is ingeschakeld. Zie het artikel [Replica is inconsistent](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10))voor meer informatie over de oorzaken van inconsistentie van replica's en relevante suggesties.<br> <ol><li> Controleer in het geval van systeemstatus/BMR-back-up of Windows Server Backup is geïnstalleerd op de beveiligde server.</li><li> Controleer op ruimtegerelateerde problemen in de DPM-opslaggroep op de DPM/Microsoft Azure Backup Server en wijs opslag toe zoals vereist.</li><li> Controleer de status van de Volume Shadow Copy Service op de beveiligde server. Als deze uitgeschakeld is, stelt u deze in om handmatig te starten. Start de service op de server. Ga vervolgens terug naar de DPM/Microsoft Azure Backup Server-console en start de synchronisatie met de taak consistentiecontrole.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Het maken van een onlineherstelpunt is mislukt

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Het maken van een onlineherstelpunt is mislukt | **Foutbericht:** Windows Azure Backup Agent kan geen momentopname maken van het geselecteerde volume. <br> **Tijdelijke oplossing:** probeer de ruimte in het volume van replica' s en herstelpunten te vergroten.<br> <br> **Foutbericht:** de Windows Azure Backup Agent kan geen verbinding maken met de OBEngine-service <br> **Tijdelijke oplossing:** controleer of de OBEngine bestaat in de lijst met lopende services op de computer. Als de OBEngine-service niet wordt uitgevoerd, gebruikt u de opdracht 'net start OBEngine' om de OBEngine-service te starten. <br> <br> **Foutbericht:** de wachtwoordzin voor versleuteling voor deze server is niet ingesteld. Configureer een coderingswachtwoordzin. <br> **Tijdelijke oplossing:** probeer een coderingswachtwoordzin te configureren. Als dit niet lukt, neemt u de volgende stappen: <br> <ol><li>Controleer of de kraslocatie bestaat. Dit is de locatie die wordt vermeld in het register **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, met de naam **ScratchLocation** moet bestaan.</li><li> Als de kraslocatie bestaat, probeert u het opnieuw registreren met behulp van de oude wachtwoordzin. *Wanneer u een versleutelingswachtwoordzin configureert, slaat u deze op een veilige locatie op.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>De originele en externe DPM-servers moeten zijn geregistreerd bij dezelfde kluis

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Herstellen | **Foutcode**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Foutbericht:** De oorspronkelijke en externe DPM-servers moeten zijn geregistreerd bij dezelfde kluis | **Oorzaak:** dit probleem treedt op wanneer u bestanden vanaf de oorspronkelijke server naar de alternatieve server probeert te herstellen met behulp van de optie Extern DPM-herstel en als de server die wordt hersteld en de oorspronkelijke server waarvan de gegevens een back-up maken, niet zijn gekoppeld aan dezelfde herstelservicekluis.<br/> <br/>**Tijdelijke oplossing** Om dit probleem op te lossen, moet u ervoor zorgen dat zowel de oorspronkelijke als de alternatieve server is geregistreerd bij dezelfde kluis.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Online herstelpunt creatie banen voor VMware VM mislukken

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Online herstelpunt creatie banen voor VMware VM mislukken. DPM heeft een fout van VMware ondervonden toen u ChangeTracking-informatie probeerde te krijgen. Foutcode - FileFaultFault (ID 33621) |  <ol><li> Reset de CTK op VMware voor de getroffen VM's.</li> <li>Controleer of er geen onafhankelijke schijf op vmware aanwezig is.</li> <li>Stop de beveiliging voor de getroffen VM's en bescherm opnieuw met de knop **Vernieuwen.** </li><li>Voer een CC uit voor de getroffen VM's.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>De agentbewerking is mislukt vanwege een communicatiefout met de DPM-agentcoördinatorservice op de server

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Agent(s) naar beveiligde servers duwen | De agentbewerking is mislukt vanwege een communicatiefout met \<de DPM-agentcoördinatorservice op ServerName>. | **Als de aanbevolen actie in het product niet werkt, voert u de volgende stappen uit:** <ul><li> Als u een computer loskoppelt van een niet-vertrouwd domein, voert u [de volgende stappen uit.](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019) <br> (OF) </li><li> Als u een computer koppelt vanuit een vertrouwd domein, u problemen oplossen met de stappen die in [deze blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726)worden beschreven. <br>(OF)</li><li> Probeer antivirus uit te schakelen als een probleemoplossingsstap. Als het probleem is opgelost, wijzigt u de antivirusinstellingen zoals voorgesteld in [dit artikel.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>De metagegevens van het register kunnen niet worden bijgewerkt met Setup

| Bewerking | Foutdetails | Tijdelijke oplossing |
|-----------|---------------|------------|
|Installeren | Setup kan de metagegevens van het register niet bijwerken. Deze updatefout kan leiden tot overmatig gebruik van het opslagverbruik. Om te voorkomen dat deze update de registervermelding ReFS Trimming. | Pas de registersleutel **SYSTEEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**aan. Stel de waarde Dword in op 1. |
|Installeren | Setup kan de metagegevens van het register niet bijwerken. Deze updatefout kan leiden tot overmatig gebruik van het opslagverbruik. Werk de registervermelding Volume SnapOptimization bij om dit te voorkomen. | Maak de registersleutel **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** with an empty string value. |

## <a name="registration-and-agent-related-issues"></a>Registratie- en agentgerelateerde problemen

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Agent(s) naar beveiligde servers duwen | De referenties die voor de server zijn opgegeven, zijn ongeldig. | **Als de aanbevolen actie die in het product wordt weergegeven, niet werkt, neemt u de volgende stappen:** <br> Probeer de beveiligingsagent handmatig te installeren op de productieserver zoals aangegeven in [dit artikel.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)|
| Azure Backup Agent kan geen verbinding maken met de Azure Backup-service (ID: 100050) | De Azure Backup Agent kan geen verbinding maken met de Azure Backup-service. | **Als de aanbevolen actie die in het product wordt weergegeven, niet werkt, neemt u de volgende stappen:** <br>1. Voer de volgende opdracht uit vanaf een verhoogde prompt: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Hiermee wordt het venster Internet Explorer geopend. <br/> 2. Ga naar**LAN-instellingen** **voor tools** > **internetopties** > **Connections** > . <br/> 3. Wijzig de instellingen om een proxyserver te gebruiken. Geef vervolgens de details van de proxyserver op.<br/> 4. Als uw machine beperkte toegang tot internet heeft, moet u ervoor zorgen dat firewall-instellingen op de machine of proxy deze [URL's](install-mars-agent.md#verify-internet-access) en [IP-adres](install-mars-agent.md#verify-internet-access)toestaan.|
| Installatie azure backup agent is mislukt | De installatie van Microsoft Azure Recovery Services is mislukt. Alle wijzigingen die door de installatie van Microsoft Azure Recovery Services in het systeem zijn aangebracht, zijn teruggedraaid. (ID: 4024) | Azure Agent handmatig installeren.

## <a name="configuring-protection-group"></a>Beveiligingsgroep configureren

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Beveiligingsgroepen configureren | DPM kan de toepassingscomponent op de beveiligde computer (beveiligde computernaam) niet opsommen. | Selecteer **Vernieuwen** op het gebruikersinterfacescherm van de beveiligingsgroep configureren op het relevante gegevensbron-/componentniveau. |
| Beveiligingsgroepen configureren | Kan de beveiliging niet configureren | Als de beveiligde server een SQL-server is, controleert u of de machtigingen voor sysadmin-rollen zijn verstrekt aan het systeemaccount (NTAuthority\System) op de beveiligde computer zoals beschreven in [dit artikel.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12))
| Beveiligingsgroepen configureren | Er is onvoldoende vrije ruimte in de opslagpool voor deze beschermingsgroep. | De schijven die aan de opslaggroep worden [toegevoegd, mogen geen partitie bevatten.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)) Verwijder bestaande volumes op de schijven. Voeg ze vervolgens toe aan de opslagpool.|
| Beleidswijziging |Het back-upbeleid kan niet worden gewijzigd. Fout: De huidige bewerking is mislukt als gevolg van een interne servicefout [0x29834]. Probeer de bewerking opnieuw nadat er enige tijd is verstreken. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning. | **Oorzaak:**<br/>Deze fout treedt op onder drie voorwaarden: wanneer beveiligingsinstellingen zijn ingeschakeld, wanneer u het bewaarbereik probeert te verlagen tot onder de eerder opgegeven minimumwaarden en wanneer u een niet-ondersteunde versie gebruikt. (Niet-ondersteunde versies zijn versies onder Microsoft Azure Backup Server versie 2.0.9052 en Azure Backup Server update 1.) <br/>**Aanbevolen actie:**<br/> Als u verder wilt gaan met beleidsgerelateerde updates, stelt u de bewaartermijn in boven de opgegeven minimale bewaartermijn. (De minimale bewaartermijn is zeven dagen voor dagelijks, vier weken voor wekelijks, drie weken voor maandelijks of één jaar voor jaarlijks.) <br><br>Optioneel is een andere voorkeursbenadering het bijwerken van de back-upagent en Azure Backup Server om alle beveiligingsupdates te gebruiken. |

## <a name="backup"></a>Backup

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Er is een onverwachte fout opgetreden tijdens het uitvoeren van de taak. Het apparaat is nog niet klaar. | **Als de aanbevolen actie die in het product wordt weergegeven, niet werkt, neemt u de volgende stappen:** <br> <ul><li>Stel de opslagruimte voor schaduwkopie in op onbeperkt voor de items in de beveiligingsgroep en voer de consistentiecontrole uit.<br></li> (OF) <li>Probeer de bestaande beveiligingsgroep te verwijderen en meerdere nieuwe groepen te maken. Elke nieuwe beschermingsgroep moet een individueel item in zich hebben.</li></ul> |
| Backup | Als u alleen een back-up maakt van de systeemstatus, controleert u of er voldoende vrije ruimte op de beveiligde computer is om de back-up van de systeemstatus op te slaan. | <ol><li>Controleer of Windows Server Backup is geïnstalleerd op de beveiligde machine.</li><li>Controleer of er voldoende ruimte is op de beveiligde computer voor de systeemstatus. De eenvoudigste manier om dit te verifiëren is door naar de beveiligde computer te gaan, Windows Server Backup te openen, door de selecties te klikken en vervolgens BMR te selecteren. De gebruikersinterface vertelt u vervolgens hoeveel ruimte nodig is. Open **WSB** > **Local backup** > **backup schedule** > **Select Backup Configuration** > **Full server** (grootte wordt weergegeven). Gebruik deze grootte voor verificatie.</li></ol>
| Backup | Back-up storing voor BMR | Als de BMR-grootte groot is, verplaatst u sommige toepassingsbestanden naar het besturingssysteem en probeert u het opnieuw. |
| Backup | De optie om een VMware VM opnieuw te beschermen op een nieuwe Microsoft Azure Backup Server wordt niet weergegeven als beschikbaar voor toevoegen. | VMware-eigenschappen zijn gericht op een oude, gepensioneerde instantie van Microsoft Azure Backup Server. Los dit probleem als volgt op:<br><ol><li>Ga in VCenter (SC-VMM-equivalent) naar het tabblad **Overzicht** en vervolgens naar **Aangepaste kenmerken**.</li>  <li>Verwijder de oude naam van Microsoft Azure Backup Server uit de **DPMServer-waarde.**</li>  <li>Ga terug naar de nieuwe Microsoft Azure Backup Server en wijzig de PG.  Nadat u de knop **Vernieuwen** hebt inschakelt, wordt de VM weergegeven met een selectievakje dat beschikbaar is om aan de beveiliging toe te voegen.</li></ol> |
| Backup | Fout bij het openen van bestanden/gedeelde mappen | Probeer de antivirusinstellingen zoals voorgesteld in dit artikel [Antivirussoftware op de DPM-server uit](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))te voeren.|

## <a name="change-passphrase"></a>Wachtwoordzin wijzigen

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Wachtwoordzin wijzigen |De ingevoerde beveiligingspincode is onjuist. Geef de juiste beveiligingspincode op om deze bewerking te voltooien. |**Oorzaak:**<br/> Deze fout treedt op wanneer u een ongeldige of verlopen beveiligingspincode invoert terwijl u een kritieke bewerking uitvoert (zoals het wijzigen van een wachtwoordzin). <br/>**Aanbevolen actie:**<br/> Als u de bewerking wilt voltooien, moet u een geldige beveiligingspincode invoeren. Als u de pincode wilt opmaken, meldt u zich aan bij de Azure-portal en gaat u naar de kluis Van Herstelservices. Ga vervolgens naar **Instellingen** > **Eigenschappen** > **Genereren Beveiligingspincode**. Gebruik deze pincode om de wachtwoordzin te wijzigen. |
| Wachtwoordzin wijzigen |De bewerking is mislukt. ID: 120002 |**Oorzaak:**<br/>Deze fout treedt op wanneer beveiligingsinstellingen zijn ingeschakeld of wanneer u de wachtwoordzin probeert te wijzigen wanneer u een niet-ondersteunde versie gebruikt.<br/>**Aanbevolen actie:**<br/> Als u de wachtwoordzin wilt wijzigen, moet u de back-upagent eerst bijwerken naar de minimale versie, namelijk 2.0.9052. U moet Azure Backup Server ook updaten naar het minimum van update 1 en vervolgens een geldige beveiligingspincode invoeren. Als u de pincode wilt krijgen, meldt u zich aan bij de Azure-portal en gaat u naar de kluis Van Herstelservices. Ga vervolgens naar **Instellingen** > **Eigenschappen** > **Genereren Beveiligingspincode**. Gebruik deze pincode om de wachtwoordzin te wijzigen. |

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| E-mailmeldingen instellen met een Office 365-account |Fout-id: 2013| **Oorzaak:**<br> Office 365-account proberen te gebruiken <br>**Aanbevolen actie:**<ol><li> Het eerste ding om ervoor te zorgen is dat "Anonieme relay toestaan op een Receive Connector" voor uw DPM-server is ingesteld op Exchange. Zie Anoniem relay toestaan op [een ontvangstconnector](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019)voor meer informatie over het configureren van dit bericht.</li> <li> Als u geen intern SMTP-relais gebruiken en uw Office 365-server moet instellen, u IIS instellen als een relay. Configureer de DPM-server om [de SMTP door te sturen naar O365 met IIS](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)).<br><br>  Zorg ervoor dat\@u de gebruiker domain.com indeling en *niet* domein\gebruiker te gebruiken.<br><br><li>Wijs DPM om de lokale servernaam te gebruiken als SMTP-server, poort 587. Wijs het vervolgens naar de e-mail van de gebruiker waar de e-mails vandaan moeten komen.<li> De gebruikersnaam en het wachtwoord op de installatiepagina van DPM SMTP moeten zijn voor een domeinaccount in het domein waarop DPM zich bevindt. </li><br> Wanneer u het SMTP-serveradres wijzigt, wijzigt u de wijziging in de nieuwe instellingen, sluit u het vak instellingen en opent u het opnieuw om er zeker van te zijn dat het de nieuwe waarde weergeeft.  Gewoon veranderen en testen kan niet altijd leiden tot de nieuwe instellingen van kracht, dus het testen van het op deze manier is een best practice.<br><br>U deze instellingen op elk moment tijdens dit proces wissen door de DPM-console te sluiten en de volgende registersleutels te bewerken: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName keys**. U ze weer toevoegen aan de gebruikersinterface wanneer u deze opnieuw start.

## <a name="common-issues"></a>Algemene problemen

In deze sectie worden de veelvoorkomende fouten die u tegenkomen tijdens het gebruik van Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Foutbericht | Aanbevolen actie |
-- | --
Back-up is mislukt omdat de back-upreplica van de schijf ongeldig is of ontbreekt. | Als u dit probleem wilt oplossen, controleert u de onderstaande stappen en probeert u de bewerking opnieuw: <br/> 1. Een herstelpunt voor de schijf maken<br/> 2. Voer consistentiecontrole uit op gegevensbron <br/> 3. De beveiliging van gegevensbron stoppen en vervolgens de beveiliging van deze gegevensbron opnieuw configureren

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Foutbericht | Aanbevolen actie |
-- | --
Momentopname bronvolume is mislukt omdat metagegevens op replica ongeldig zijn. | Maak een schijfherstelpunt van deze gegevensbron en probeer opnieuw online back-up

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Foutbericht | Aanbevolen actie |
-- | --
Momentopname bronvolume is mislukt als gevolg van inconsistente gegevensbronreplica. | Voer een consistentiecontrole uit op deze gegevensbron en probeer het opnieuw

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Foutbericht | Aanbevolen actie |
-- | --
De back-up is mislukt omdat de back-upreplica van de schijf niet kan worden gekloond.| Zorg ervoor dat alle vorige back-upreplicabestanden (.vhdx) zijn ontkoppeld en dat er geen back-up van schijf naar schijf wordt uitgevoerd tijdens online back-ups
