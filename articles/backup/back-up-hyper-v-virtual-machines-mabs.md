---
title: Back-up maken van virtuele Hyper-V-machines met Azure Backup Server
description: Dit artikel bevat de procedures voor het maken van back-ups en het herstellen van virtuele machines met behulp van Microsoft Azure Backup-Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: dacurwin
ms.openlocfilehash: f15606c83c221e4591a2a1f6a71fc7141bdf3daf
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074966"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Back-up maken van virtuele Hyper-V-machines met Azure Backup Server

In dit artikel wordt uitgelegd hoe u een back-up van virtuele Hyper-V-machines maakt met behulp van Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Ondersteunde scenario's

MABS kan een back-up maken van virtuele machines die worden uitgevoerd op Hyper-V-hostservers in de volgende scenario's:

- **Virtuele machines met lokale of directe opslag** : een back-up maken van virtuele machines die worden gehost op zelfstandige Hyper-V-host servers die lokale of rechtstreeks gekoppelde opslag hebben. Bijvoorbeeld: een harde schijf, een Storage Area Network apparaat (SAN) of een NAS-apparaat (Network Attached Storage). De MABS-beveiligings agent moet op alle hosts zijn geïnstalleerd.

- **Virtuele machines in een cluster met CSV-opslag** : Maak een back-up van virtuele machines die worden gehost op een Hyper-V-cluster met cluster Shared volume (CSV)-opslag. De MABS-beveiligings agent wordt op elk cluster knooppunt geïnstalleerd.

## <a name="host-versus-guest-backup"></a>Host versus gast back-up

MABS kan een back-up van Hyper-V-Vm's op een host of gast niveau maken. Op hostniveau wordt de MABS-beveiligings agent geïnstalleerd op de Hyper-V-hostserver of het cluster en worden de volledige Vm's en gegevens bestanden op die host beveiligd.   Op gast niveau wordt de agent geïnstalleerd op elke virtuele machine en wordt de werk belasting die op die computer aanwezig is, beveiligd.

Beide methoden hebben voor-en nadelen:

- Back-ups op hostniveau zijn flexibel omdat ze werken, ongeacht het type besturings systeem dat wordt uitgevoerd op de gast computers en de installatie van de MABS-beveiligings agent op elke virtuele machine niet hoeft te worden geïnstalleerd. Als u back-up op hostniveau implementeert, kunt u een volledige virtuele machine, of bestanden en mappen (herstel op item niveau) herstellen.

- Back-ups op gast niveau zijn handig als u specifieke werk belastingen wilt beveiligen die op een virtuele machine worden uitgevoerd. Op hostniveau kunt u een volledige virtuele machine of specifieke bestanden herstellen, maar dit biedt geen herstel in de context van een bepaalde toepassing. Als u bijvoorbeeld specifieke share point-items van een back-up wilt herstellen, moet u een back-up op gast niveau van die virtuele machine uitvoeren. Gebruik back-up op gast niveau als u gegevens wilt beveiligen die op passthrough-schijven zijn opgeslagen. Met passthrough kan de virtuele machine rechtstreeks toegang krijgen tot het opslag apparaat en worden virtuele volume gegevens niet in een VHD-bestand opgeslagen.

## <a name="how-the-backup-process-works"></a>Hoe het back-upproces werkt

MABS voert een back-up met VSS als volgt uit. De stappen in deze beschrijving zijn genummerd om duidelijkheid te bieden.

1. De MABS-synchronisatie-engine op basis van blokken maakt een eerste kopie van de beveiligde virtuele machine en zorgt ervoor dat de kopie van de virtuele machine volledig en consistent is.

2. Nadat de eerste kopie is gemaakt en geverifieerd, gebruikt MABS het Hyper-V-VSS Writer voor het vastleggen van back-ups. De VSS Writer biedt een gegevens-consistente set schijf blokken die zijn gesynchroniseerd met de MABS-server. Deze benadering biedt het voor deel van een ' volledige back-up ' met de MABS-server, terwijl de back-upgegevens die over het netwerk moeten worden verzonden, worden geminimaliseerd.

3. De MABS-beveiligings agent op een server waarop Hyper-V wordt uitgevoerd, maakt gebruik van de bestaande Hyper-V-Api's om te bepalen of een beveiligde virtuele machine ook VSS ondersteunt.

   - Als een virtuele machine voldoet aan de vereisten voor online back-up en het onderdeel Hyper-V Integration Services is geïnstalleerd, stuurt de Hyper-V VSS Writer de VSS-aanvraag recursief door naar alle VSS-processen op de virtuele machine. Deze bewerking wordt uitgevoerd zonder dat de MABS-beveiligings agent op de virtuele machine wordt geïnstalleerd. Met de recursieve VSS-aanvraag kan de Hyper-V-VSS Writer ervoor zorgen dat schijf schrijf bewerkingen worden gesynchroniseerd zodat een VSS-moment opname wordt vastgelegd zonder gegevens verlies.

     Het onderdeel Hyper-V Integration Services roept de Hyper-V-VSS Writer in Volume Shadow Copy Services (VSS) op virtuele machines aan om ervoor te zorgen dat de toepassings gegevens in een consistente staat zijn.

   - Als de virtuele machine niet voldoet aan de vereisten voor online back-ups, gebruikt MABS automatisch de Hyper-V-Api's om de virtuele machine te onderbreken voordat de gegevens bestanden worden vastgelegd.

4. Nadat de oorspronkelijke basislijn kopie van de virtuele machine is gesynchroniseerd met de MABS-server, worden alle wijzigingen die worden aangebracht aan de resources van de virtuele machine vastgelegd in een nieuw herstel punt. Het herstel punt vertegenwoordigt de consistente status van de virtuele machine op een specifiek tijdstip. Vastleggen van herstel punten kan ten minste één keer per dag plaatsvinden. Wanneer een nieuw herstel punt wordt gemaakt, gebruikt MABS replicatie op blok niveau in combi natie met de Hyper-V-VSS Writer om te bepalen welke blokken zijn gewijzigd op de server waarop Hyper-V wordt uitgevoerd nadat het laatste herstel punt is gemaakt. Deze gegevens blokken worden vervolgens overgedragen naar de MABS-server en worden toegepast op de replica van de beveiligde gegevens.

5. De MABS-server gebruikt VSS op de volumes die herstel gegevens hosten, zodat er meerdere schaduw kopieën beschikbaar zijn. Elk van deze schaduw kopieën voorziet in een afzonderlijk herstel. VSS-herstel punten worden opgeslagen op de MABS-server. De tijdelijke kopie die wordt gemaakt op de server waarop Hyper-V wordt uitgevoerd, wordt alleen opgeslagen voor de duur van de MABS-synchronisatie.

>[!NOTE]
>
>Vanaf Windows Server 2016 zijn Hyper-V virtuele harde schijven ingebouwd voor het bijhouden van wijzigingen (RCT). MABS maakt gebruik van RCT (het systeem eigen bijhouden van wijzigingen in Hyper-V), waardoor er minder tijdrovende consistentie controles nodig zijn in scenario's zoals VM-crashes. RCT biedt betere tolerantie dan het bijhouden van wijzigingen door VSS-back-ups op basis van moment opnamen. MABS v3 optimaliseert het netwerk-en opslag verbruik verder door alleen de gewijzigde gegevens tijdens de consistentie controles over te dragen.

## <a name="backup-prerequisites"></a>Back-upvereisten

Dit zijn de vereisten voor het maken van back-ups van virtuele Hyper-V-machines met MABS:

|Vereiste|Details|
|------------|-------|
|Vereisten voor MABS|-Als u herstel op item niveau voor virtuele machines wilt uitvoeren (bestanden, mappen, volumes), moet u de Hyper-V-functie installeren op de MABS-server.  Als u alleen de virtuele machine wilt herstellen en niet op item niveau, dan is de functie niet vereist.<br />-U kunt Maxi maal 800 virtuele machines van 100 GB elk op één MABS-server beveiligen en meerdere MABS-servers toestaan die grotere clusters ondersteunen.<br />-MABS sluit het wissel bestand van incrementele back-ups uit om de back-upprestaties van virtuele machines te verbeteren.<br />-MABS kan een back-up maken van een Hyper-V-Server of-cluster in hetzelfde domein als de MABS-server, of in een onderliggend of vertrouwd domein. Als u een back-up wilt maken van Hyper-V in een werk groep of een niet-vertrouwd domein, moet u verificatie instellen. U kunt voor één Hyper-V-server NTLM of certificaat verificatie gebruiken. Voor een cluster kunt u alleen certificaat verificatie gebruiken.<br />-Het gebruik van back-up op hostniveau om back-ups te maken van gegevens van virtuele machines op passthrough-schijven wordt niet ondersteund. In dit scenario raden we u aan om back-up op hostniveau te gebruiken om back-ups te maken van VHD-bestanden en back-ups op gast niveau om back-ups te maken van de andere gegevens die niet zichtbaar zijn op de host.<br />   -U kunt back-ups maken van virtuele machines die zijn opgeslagen op ontdubbelde volumes.|
|Vereisten voor Hyper-V-VM'S|-De versie van integratie onderdelen die op de virtuele machine wordt uitgevoerd, moet hetzelfde zijn als de versie van de Hyper-V-host. <br />-Voor elke back-up van de virtuele machine hebt u vrije ruimte nodig op het volume dat als host fungeert voor de bestanden van de virtuele harde schijf, zodat Hyper-V voldoende ruimte heeft voor differentiërende schijven (AVHD) tijdens het maken van een back-up. De ruimte moet mini maal gelijk zijn aan de **eerste schijf grootte van de berekening\*het verloop snelheid\*** de tijd van het back-upvenster. Als u meerdere back-ups op een cluster uitvoert, hebt u voldoende opslag capaciteit nodig voor de Avhd's samengevoegd voor elk van de virtuele machines met behulp van deze berekening.<br />-Als u een back-up wilt maken van virtuele machines op Hyper-V-hostservers met Windows Server 2012 R2, moet op de virtuele machine een SCSI-controller zijn opgegeven, zelfs als deze geen verbinding heeft met iets. (In Windows Server 2012 R2 online backup koppelt de Hyper-V-host een nieuwe VHD in de virtuele machine en wordt deze vervolgens later ontkoppeld. Alleen de SCSI-controller kan dit ondersteunen en is daarom vereist voor online back-up van de virtuele machine.  Zonder deze instelling wordt gebeurtenis-ID 10103 uitgegeven wanneer u een back-up van de virtuele machine probeert te maken.)|
|Vereisten voor Linux|-U kunt back-ups maken van virtuele Linux-machines met behulp van MABS 2012 R2. Alleen bestands consistente moment opnamen worden ondersteund.|
|Back-ups maken van Vm's met CSV-opslag|-Voor CSV-opslag installeert u de VSS-hardwareprovider (Volume Shadow Copy Services) op de Hyper-V-Server. Neem contact op met de leverancier van uw Storage Area Network (SAN) voor de VSS-hardwareprovider.<br />-Als één knoop punt onverwacht wordt uitgeschakeld in een CSV-cluster, wordt door MABS een consistentie controle uitgevoerd voor de virtuele machines die op het knoop punt worden uitgevoerd.<br />-Als u een Hyper-V-Server moet opnieuw opstarten waarvoor BitLocker-stationsversleuteling op het CSV-cluster is ingeschakeld, moet u een consistentie controle uitvoeren voor virtuele Hyper-V-machines.|
|Back-ups maken van Vm's met SMB-opslag|-Schakel automatisch koppelen in op de server waarop Hyper-V wordt uitgevoerd om de beveiliging van virtuele machines in te scha kelen.<br />   -Schakel TCP-chimney-offload uit.<br />-Zorg ervoor dat alle Hyper-V-computer accounts volledige machtigingen hebben voor de specifieke externe SMB-bestands shares.<br />-Zorg ervoor dat het bestandspad voor alle onderdelen van virtuele machines tijdens het herstel naar een alternatieve locatie minder dan 260 tekens is. Als dat niet het geval is, kan het herstel slagen, maar kan de virtuele machine niet worden gekoppeld met Hyper-V.<br />-De volgende scenario's worden niet ondersteund:<br />     Implementaties waarbij sommige onderdelen van de virtuele machine zich op lokale volumes bevinden en sommige onderdelen zich op externe volumes bevinden. een IPv4-of IPv6-adres voor de opslaglocatie Bestands server en het herstellen van een virtuele machine naar een computer die gebruikmaakt van externe SMB-shares.<br />-U moet de File Server VSS Agent-service op elke SMB-server inschakelen: Voeg deze toe in **functies en onderdelen toevoegen** > **selecteer Server functies** > **bestands-en opslag Services** > **Bestands Services** > **File-Service** > **File Server VSS Agent service**.|

## <a name="back-up-virtual-machines"></a>Back-ups maken van virtuele machines

1. Stel uw [MABS-server](backup-azure-microsoft-azure-backup.md) en [uw opslag](backup-mabs-add-storage.md)in. Gebruik de richt lijnen voor opslag capaciteit bij het instellen van uw opslag.
   - Gemiddelde grootte van virtuele machine-100 GB
   - Aantal virtuele machines per MABS-server-800
   - Totale grootte van 800 Vm's-80 TB
   - Vereiste ruimte voor back-upopslag-80 TB

2. Stel de MABS-beveiligings agent in op de Hyper-V-Server of Hyper-V-cluster knooppunten. Als u een back-up op gast niveau uitvoert, installeert u de agent op de virtuele machines waarvan u een back-up wilt maken op gast niveau.

3. Klik in de MABS Administrator-console op **beveiliging** > **beveiligings groep maken** om de wizard **nieuwe beveiligings groep maken** te openen.

4. Selecteer op de pagina **groeps leden selecteren** de virtuele machines die u wilt beveiligen vanaf de Hyper-V-hostservers waarop ze zich bevinden. We raden u aan alle Vm's die hetzelfde beveiligings beleid hebben, in één beveiligings groep te plaatsen. Als u een efficiënt gebruik van ruimte wilt maken, schakelt u co-locatie in. Met de co-locatie kunt u gegevens uit verschillende beveiligings groepen op dezelfde schijf of tape opslag plaatsen, zodat meerdere gegevens bronnen één replica-en herstel punt volume hebben.

5. Geef op de pagina **methode voor gegevens beveiliging selecteren** een naam op voor de beveiligings groep. Selecteer **Ik wil kortetermijnbeveiliging met schijf** en selecteer **Ik wil online beveiliging** als u back-ups wilt maken van gegevens naar Azure met behulp van de Azure backup-service.

6. Geef in **doelen voor korte termijn opgeven** > **Bewaar termijn**op hoelang u schijf gegevens wilt bewaren. Geef bij **synchronisatie frequentie**op hoe vaak incrementele back-ups van de gegevens moeten worden uitgevoerd. In plaats van een interval voor incrementele back-ups kunt u ook **net vóór een herstel punt**inschakelen. Als deze instelling is ingeschakeld, voert MABS een snelle volledige back-up uit net vóór elk gepland herstel punt.

    > [!NOTE]
    >
    >Als u toepassings werkbelastingen beveiligt, worden herstel punten gemaakt op basis van de synchronisatie frequentie, mits de toepassing incrementele back-ups ondersteunt. Als dat niet het geval is, voert MABS een snelle volledige back-up uit in plaats van een incrementele back-up en worden er herstel punten gemaakt volgens de planning voor snelle back-ups.

7. Controleer op de pagina **schijf toewijzing controleren** de schijf ruimte van de opslag groep die is toegewezen aan de beveiligings groep.

   **Totale gegevens grootte** is de grootte van de gegevens waarvan u een back-up wilt maken en **schijf ruimte die moet worden ingericht op MABS** is de ruimte die MABS raadt aan de beveiligings groep. MABS kiest het ideale back-upvolume, op basis van de instellingen. U kunt echter de keuzes van het back-upvolume bewerken in de **schijf toewijzings Details**. Selecteer voor de werk belastingen de voorkeurs opslag in het vervolg keuzemenu. Met uw wijzigingen wijzigt u de waarden voor de **totale opslag** en **vrije opslag ruimte** in het deel venster **beschik bare Disk Storage** . Onderingerichte ruimte is de hoeveelheid opslag MABS die u toevoegt aan het volume, zodat u in de toekomst probleemloos kunt door gaan met het maken van back-ups.

8. Geef op de pagina **methode voor maken van replica selecteren** op hoe de initiële replicatie van gegevens in de beveiligings groep wordt uitgevoerd. Als u ervoor kiest om **automatisch via het netwerk te repliceren**, raden we u aan een rustige tijd te kiezen. Voor grote hoeveel heden gegevens of minder dan optimale netwerk omstandigheden, kunt u overwegen om **hand matig**te selecteren. hiervoor moet u de gegevens offline repliceren met behulp van Verwissel bare media.

9. Selecteer op de pagina **Opties voor consistentie controle** hoe u consistentie controles wilt automatiseren. U kunt inschakelen dat er alleen een controle wordt uitgevoerd als de replica gegevens inconsistent worden, of volgens een planning. Als u geen automatische consistentie controle wilt configureren, kunt u op elk gewenst moment een hand matige controle uitvoeren door met de rechter muisknop op de beveiligings groep te klikken en **consistentie controle uitvoeren**te selecteren.

    Nadat u de beveiligings groep hebt gemaakt, vindt de initiële replicatie van de gegevens plaats in overeenstemming met de methode die u hebt geselecteerd. Na de initiële replicatie vindt elke back-up plaats in overeenstemming met de instellingen van de beveiligings groep. Als u een back-up van gegevens moet herstellen, let dan op het volgende:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Back-ups maken van virtuele machines die voor Livemigratie zijn geconfigureerd

Wanneer virtuele machines zijn betrokken bij Livemigratie, blijft MABS de virtuele machines beveiligen, zolang de MABS-beveiligings agent op de Hyper-V-host is geïnstalleerd. De manier waarop MABS de virtuele machines beveiligt, is afhankelijk van het type Livemigratie.

**Livemigratie binnen een cluster** : wanneer een virtuele machine binnen een cluster wordt gemigreerd, wordt de migratie door MABS gedetecteerd en wordt er een back-up van de virtuele machine gemaakt vanaf het nieuwe cluster knooppunt zonder dat hiervoor gebruikers interventie nodig is. MABS gaat verder met snelle volledige back-ups omdat de opslag locatie niet is gewijzigd.

**Livemigratie buiten het cluster** : wanneer een virtuele machine wordt gemigreerd tussen zelfstandige servers, verschillende clusters of tussen een zelfstandige server en een cluster, detecteert MABS de migratie en kan er een back-up worden gemaakt van de virtuele machine zonder tussen komst van de gebruiker.

### <a name="requirements-for-maintaining-protection"></a>Vereisten voor het onderhouden van de beveiliging

Hieronder vindt u de vereisten voor het onderhouden van de beveiliging tijdens Live migratie:

- De Hyper-V-hosts voor de virtuele machines moeten zich bevinden in een System Center VMM-Cloud op een VMM-server waarop ten minste System Center 2012 met SP1 wordt uitgevoerd.

- De MABS-beveiligings agent moet zijn geïnstalleerd op alle Hyper-V-hosts.

- MABS-servers moeten zijn verbonden met de VMM-server. Alle Hyper-V-hostservers in de VMM-Cloud moeten ook zijn verbonden met de MABS-servers. Hierdoor kan MABS communiceren met de VMM-server, zodat MABS kan nagaan op welke Hyper-V-hostserver de virtuele machine wordt uitgevoerd en een nieuwe back-up maakt van die Hyper-V-Server. Als er geen verbinding kan worden gemaakt met de Hyper-V-Server, mislukt de back-up met een bericht dat de MABS-beveiligings agent niet bereikbaar is.

- Alle MABS-servers, VMM-servers en Hyper-V-hostservers moeten zich in hetzelfde domein bestaan.

### <a name="details-about-live-migration"></a>Details over Livemigratie

Let op het volgende voor back-up tijdens Livemigratie:

- Als een Livemigratie opslag ruimte draagt, voert MABS een volledige consistentie controle van de virtuele machine uit en gaat vervolgens door met snelle volledige back-ups. Als er sprake is van een Livemigratie van opslag, wordt de virtuele harde schijf (VHD) of VHDX door Hyper-V opnieuw ingedeeld, waardoor een eenmalige piek in de grootte van de MABS-back-upgegevens wordt veroorzaakt.

- Schakel op de host van de virtuele machine automatisch koppelen in om virtuele beveiliging in te scha kelen en schakel TCP-chimney-offload uit.

- MABS gebruikt poort 6070 als de standaard poort voor het hosten van de DPM-VMM Helper-service. Het REGI ster wijzigen:

    1. Navigeer naar **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Maak een 32-bits DWORD-waarde: DpmVmmHelperServicePort en schrijf het bijgewerkte poort nummer als onderdeel van de register sleutel.
    3. Open ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```en wijzig het poort nummer van 6070 in de nieuwe poort. Bijvoorbeeld: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Start de DPM-VMM Helper-service opnieuw en start de DPM-service opnieuw.

### <a name="set-up-protection-for-live-migration"></a>Beveiliging voor Livemigratie instellen

Beveiliging voor Livemigratie instellen:

1. Stel de MABS-server en de opslag in en installeer de MABS-beveiligings agent op elke Hyper-V-hostserver of cluster knooppunt in de VMM-Cloud. Als u SMB-opslag gebruikt in een cluster, installeert u de MABS-beveiligings agent op alle cluster knooppunten.

2. Installeer de VMM-console als een client onderdeel op de MABS-server, zodat MABS kan communiceren met de VMM-server. De console moet dezelfde versie hebben als die op de VMM-server wordt uitgevoerd.

3. Wijs het MABSMachineName $-account toe als alleen-lezen beheerders account op de VMM-beheer server.

4. Verbind alle Hyper-V-hostservers met de `Set-DPMGlobalProperty` Power shell-cmdlet voor alle MABS-servers. De cmdlet accepteert meerdere MABS-server namen. Gebruik de indeling: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Zie [set-DPMGlobalProperty](https://technet.microsoft.com/library/hh881752.aspx)voor meer informatie.

5. Nadat alle virtuele machines die op de Hyper-V-hosts in de VMM-Clouds worden uitgevoerd, zijn gedetecteerd in VMM, stelt u een beveiligings groep in en voegt u de virtuele machines toe die u wilt beveiligen. Automatische consistentie controles moeten worden ingeschakeld op het niveau van de beveiligings groep voor beveiliging onder mobiliteits scenario's voor virtuele machines.

6. Wanneer de instellingen zijn geconfigureerd en een virtuele machine van het ene naar het andere cluster wordt gemigreerd, worden alle back-upbewerkingen op de verwachte wijze uitgevoerd. U kunt als volgt controleren of Livemigratie is ingeschakeld zoals verwacht:

   1. Controleer of de DPM-VMM Helper-service wordt uitgevoerd. Als dat niet het geval is, start u deze.

   2. Open Microsoft SQL Server Management Studio en maak verbinding met het exemplaar dat als host fungeert voor de MABS-data base (DPMDB). Voer de volgende query uit op DPMDB: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Deze query bevat een eigenschap met de naam `KnownVMMServer`. Deze waarde moet overeenkomen met de waarde die u bij de cmdlet `Set-DPMGlobalProperty` hebt gekregen.

   3. Voer de volgende query uit om de *VMMIdentifier* -para meter in de `PhysicalPathXML` voor een bepaalde virtuele machine te valideren. Vervang `VMName` door de naam van de virtuele machine.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Open het XML-bestand dat met deze query wordt geretourneerd en controleer of het veld *VMMIdentifier* een waarde heeft.

### <a name="run-manual-migration"></a>Hand matige migratie uitvoeren

Nadat u de stappen in de vorige secties hebt voltooid en de taak MABS Summary Manager is voltooid, wordt de migratie ingeschakeld. Standaard begint deze taak om middernacht en wordt elke ochtend uitgevoerd. Als u een hand matige migratie wilt uitvoeren om te controleren of alles werkt zoals verwacht, gaat u als volgt te werk:

1. Open SQL Server Management Studio en maak verbinding met het exemplaar dat als host fungeert voor de MABS-data base.

2. Voer de volgende query uit: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. Deze query retourneert de **ScheduleID**. Noteer deze ID zoals u deze in de volgende stap gaat gebruiken.

3. Vouw in het SQL Server Management Studio **SQL Server Agent**uit en vouw vervolgens **taken**uit. Klik met de rechter muisknop op **ScheduleID** die u hebt genoteerd en selecteer **taak starten bij stap**.

De back-upprestaties worden beïnvloed wanneer de taak wordt uitgevoerd. De grootte en schaal van uw implementatie bepalen hoe lang het duurt voordat de taak is voltooid.

## <a name="back-up-replica-virtual-machines"></a>Back-ups maken van virtuele replica machines

Als MABS wordt uitgevoerd op Windows Server 2012 R2 of hoger, kunt u een back-up maken van virtuele replica machines. Dit is om verschillende redenen handig:

**Vermindert de invloed van back-ups op de actieve werk belasting** , waarbij een back-up van een virtuele machine wordt gemaakt, wordt een zekere overhead voor het maken van een moment opname. Door het back-upproces te offloaden naar een secundaire externe site, wordt de back-upbewerking niet meer beïnvloed door de actieve werk belasting. Dit is alleen van toepassing op implementaties waarbij de back-up op een externe site wordt opgeslagen. U kunt bijvoorbeeld dagelijks back-ups maken en gegevens lokaal opslaan om snel herstel tijden te garanderen, maar maandelijks of driemaandelijkse back-ups maken van virtuele replica machines die extern zijn opgeslagen voor lange termijn retentie.

**Hiermee wordt de band breedte bespaard** : in een typische installatie van een extern filiaal/hoofd kantoor hebt u een geschikte hoeveelheid ingerichte band breedte nodig voor het overdragen van back-upgegevens tussen sites. Als u een replicatie-en failover-strategie maakt naast uw strategie voor gegevens back-ups, kunt u de hoeveelheid redundante gegevens die via het netwerk worden verzonden, beperken. Als u een back-up maakt van de gegevens van de replica virtuele machine in plaats van de primaire, bespaart u de overhead van het verzenden van de gegevens waarvan een back-up is gemaakt via het netwerk.

**Maakt een back-up van de Hoster** . u kunt een gehoste Data Center gebruiken als replica-site, zonder dat er een secundair Data Center nodig is. In dit geval vereist de SLA voor de Hoster een consistente back-up van virtuele replica machines.

Een virtuele replica machine wordt uitgeschakeld totdat een failover wordt gestart en VSS geen toepassings consistente back-up voor een virtuele replica machine kan garanderen. De back-up van een virtuele replica machine wordt dus alleen crash-consistent. Als er geen crash-consistentie kan worden gegarandeerd, mislukt de back-up en kan dit in een aantal omstandigheden optreden:

- De virtuele replica machine is niet in orde en heeft een kritieke status.

- De replica van de virtuele machine wordt opnieuw gesynchroniseerd (bij opnieuw synchroniseren of de status voor opnieuw synchroniseren is vereist).

- Initiële replicatie tussen de primaire en secundaire site wordt uitgevoerd of is in behandeling voor de virtuele machine.

- . HRL-logboeken worden toegepast op de replica virtuele machine, of een eerdere actie om de. HRL-logboeken op de virtuele schijf toe te passen, is mislukt of is geannuleerd of onderbroken.

- Migratie of failover van de virtuele replica machine wordt uitgevoerd

## <a name="recover-backed-up-virtual-machines"></a>Back-ups van virtuele machines herstellen

Wanneer u een back-up van de virtuele machine kunt herstellen, gebruikt u de wizard herstellen om de virtuele machine en het specifieke herstel punt te selecteren. Ga als volgt te werk om de wizard herstellen te openen en een virtuele machine te herstellen:

1. Typ in de MABS Administrator-console de naam van de virtuele machine of vouw de lijst met beveiligde items uit en selecteer de virtuele machine die u wilt herstellen.

2. Klik in het deel venster **herstel punten voor** in de agenda op een datum om de beschik bare herstel punten weer te geven. Selecteer vervolgens in het deel venster **pad** het herstel punt dat u wilt gebruiken in de wizard herstellen.

3. Klik in het menu **acties** op **herstellen** om de wizard herstellen te openen.

    De virtuele machine en het herstel punt die u hebt geselecteerd, worden weer gegeven in het scherm **selectie voor herstel controleren** . Klik op **Volgende**.

4. Selecteer op het scherm **herstel type selecteren** waar u de gegevens wilt herstellen en klik vervolgens op **volgende**.

    - **Herstellen naar oorspronkelijk exemplaar**: wanneer u het oorspronkelijke exemplaar herstelt, wordt de oorspronkelijke VHD verwijderd. MABS herstelt de VHD en andere configuratie bestanden op de oorspronkelijke locatie met behulp van Hyper-V-VSS Writer. Aan het einde van het herstel proces zijn virtuele machines nog steeds Maxi maal beschikbaar.
        De resource groep moet aanwezig zijn voor herstel. Als deze niet beschikbaar is, herstelt u naar een alternatieve locatie en maakt u de virtuele machine Maxi maal beschikbaar.

    - **Herstellen als virtuele machine op een wille keurige host**: MABS ondersteunt een probleemloze herstel bewerking van een beveiligde hyper-v-virtuele machine naar een andere hyper-v-host, onafhankelijk van de processor architectuur. Virtuele Hyper-V-machines die worden hersteld naar een cluster knooppunt zijn niet Maxi maal beschikbaar. Als u deze optie kiest, geeft de herstel wizard een extra scherm voor het identificeren van het doel en doelpad.

    - **Kopiëren naar een netwerkmap**: MABS ondersteunt herstel op item niveau (ILR), waarmee u op item niveau bestanden, mappen, volumes en virtuele harde schijven (vhd's) kunt herstellen vanaf een back-up op hostniveau van virtuele Hyper-V-machines naar een netwerk share of een volume op een MABS beveiligde server. De MABS-beveiligings agent hoeft niet in de gast te worden geïnstalleerd om herstel op item niveau uit te voeren. Als u deze optie kiest, geeft de herstel wizard een extra scherm voor het identificeren van het doel en doelpad.

5. Configureer in **herstel opties opgeven** de herstel opties en klik op **volgende**:

    - Als u een VM met een lage band breedte herstelt, klikt u op **wijzigen** om **beperking van netwerk bandbreedte gebruik**in te scha kelen. Nadat u de beperkings optie hebt ingeschakeld, kunt u de hoeveelheid band breedte opgeven die u beschikbaar wilt maken en de tijd waarop de band breedte beschikbaar is.
    - Selecteer **op San gebaseerd herstel inschakelen met behulp van een moment opname van de hardware** als u uw netwerk hebt geconfigureerd.
    - Selecteer **een e-mail bericht verzenden wanneer het herstel is voltooid** en geef vervolgens de e-mail adressen op als u wilt dat e-mail meldingen worden verzonden nadat het herstel proces is voltooid.

6. Zorg ervoor dat alle gegevens in het scherm samen vatting juist zijn. Als de details niet juist zijn of als u een wijziging wilt aanbrengen, klikt u op **vorige**. Als u tevreden bent met de instellingen, klikt u op **herstellen** om het herstel proces te starten.

7. Het scherm **herstel status** bevat informatie over de herstel taak.

## <a name="next-steps"></a>Volgende stappen

[Gegevens herstellen vanaf Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
