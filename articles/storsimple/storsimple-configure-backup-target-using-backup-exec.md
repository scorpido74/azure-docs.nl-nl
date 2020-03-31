---
title: StorSimple 8000-serie als back-updoel met Backup Exec | Microsoft Documenten
description: Beschrijft de StorSimple back-up doelconfiguratie met Veritas Backup Exec.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: matd
ms.openlocfilehash: 4dcda65384190050e11f1bf9b15c706b0e38c6b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561640"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple als back-updoel met Backup Exec

## <a name="overview"></a>Overzicht

Azure StorSimple is een hybride cloudopslagoplossing van Microsoft. StorSimple pakt de complexiteit van exponentiële gegevensgroei aan door een Azure-opslagaccount te gebruiken als uitbreiding van de on-premises oplossing en gegevens automatisch te gelaagderen voor on-premises opslag- en cloudopslag.

In dit artikel bespreken we de integratie van StorSimple met Veritas Backup Exec en best practices voor de integratie van beide oplossingen. We doen ook aanbevelingen over het opzetten van Backup Exec om het beste te integreren met StorSimple. We stellen de best practices, back-uparchitecten en beheerders van Veritas uit voor de beste manier om Backup Exec in te stellen om te voldoen aan individuele back-upvereisten en service-level agreements (SLA's).

Hoewel we configuratiestappen en sleutelconcepten illustreren, is dit artikel geenszins een stapsgewijze configuratie- of installatiehandleiding. We gaan ervan uit dat de basiscomponenten en infrastructuur in goede staat zijn en klaar zijn om de concepten die we beschrijven te ondersteunen.

### <a name="who-should-read-this"></a>Wie moet dit lezen?

De informatie in dit artikel is zeer nuttig voor back-upbeheerders, opslagbeheerders en opslagarchitecten die kennis hebben van opslag, Windows Server 2012 R2, Ethernet, cloudservices en Backup Exec.

## <a name="supported-versions"></a>Ondersteunde versies

-   [Back-up van Exec 16 en latere versies](https://www.veritas.com/content/support/en_US/article.100040087)
-   [StorSimple Update 3 en latere versies](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Waarom StorSimple als back-up doelwit?

StorSimple is een goede keuze voor een back-up doel, omdat:

-   Het biedt standaard, lokale opslag voor back-uptoepassingen om te gebruiken als een snelle back-upbestemming, zonder wijzigingen. U StorSimple ook gebruiken voor een snelle herstel van recente back-ups.
-   De cloud-gelaagding is naadloos geïntegreerd met een Azure-cloudopslagaccount om kosteneffectieve Azure Storage te gebruiken.
-   Het biedt automatisch offsite opslag voor noodherstel.

## <a name="key-concepts"></a>Belangrijkste concepten

Zoals bij elke opslagoplossing is een zorgvuldige beoordeling van de opslagprestaties, SLA's, veranderingssnelheid en capaciteitsgroeibehoeften van cruciaal belang voor succes. Het belangrijkste idee is dat door de invoering van een cloud laag, uw toegangstijden en doorvoernaar de cloud een fundamentele rol spelen in het vermogen van StorSimple om zijn werk te doen.

StorSimple is ontworpen om opslag te bieden aan toepassingen die werken op een goed gedefinieerde werkset van gegevens (hot data). In dit model wordt de werkset gegevens opgeslagen op de lokale lagen en wordt de resterende niet-werkende/koud/gearchiveerde set gegevens naar de cloud gegelaagdt. Dit model wordt weergegeven in het volgende cijfer. De bijna vlakke groene lijn vertegenwoordigt de gegevens die zijn opgeslagen op de lokale lagen van het StorSimple-apparaat. De rode lijn vertegenwoordigt de totale hoeveelheid gegevens die is opgeslagen op de StorSimple-oplossing in alle lagen. De ruimte tussen de vlakke groene lijn en de exponentiële rode curve vertegenwoordigt de totale hoeveelheid gegevens die in de cloud is opgeslagen.

**StorSimple tiering**
![StorSimple tiering diagram StorSimple tiering diagram StorSimple tiering diagram StorSimple tiering diagram StorSimple tiering Diagram](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

Met deze architectuur in het achterhoofd, zult u merken dat StorSimple is bij uitstek geschikt om te werken als een back-up doel. U StorSimple gebruiken om:
-   Voer uw meest voorkomende herstelbewerkingen uit van de lokale werkset met gegevens.
-   Gebruik de cloud voor offsite disaster recovery en oudere gegevens, waarbij herstel minder vaak voorkomt.

## <a name="storsimple-benefits"></a>StorSimple voordelen

StorSimple biedt een on-premises oplossing die naadloos is geïntegreerd met Microsoft Azure, door gebruik te maken van naadloze toegang tot on-premises en cloudopslag.

StorSimple maakt gebruik van automatische lagen tussen het on-premises apparaat, dat ssd-opslag (solid-state device) en seriële scsi-opslag (SAS) en Azure Storage heeft. Automatische tiering houdt vaak lokale gegevens op de SSD- en SAS-lagen. Het verplaatst zelden geopende gegevens naar Azure Storage.

StorSimple biedt deze voordelen:

-   Unieke ontdubbelings- en compressiealgoritmen die de cloud gebruiken om ongekende deduplicatieniveaus te bereiken
-   Hoge beschikbaarheid
-   Geo-replicatie met Azure geo-replicatie
-   Azure-integratie
-   Gegevensversleuteling in de cloud
-   Verbeterde herstel en naleving van rampen

Hoewel StorSimple twee belangrijke implementatiescenario's presenteert (primair back-updoel en secundair back-updoel), is het in principe een gewoon, blokopslagapparaat. StorSimple doet alle compressie en ontdubbeling. Het verzendt en haalt naadloos gegevens op tussen de cloud en het applicatie- en bestandssysteem.

Zie [StorSimple 8000-serie: Hybride cloudopslagoplossing voor](storsimple-overview.md)meer informatie over StorSimple. Ook u de [technische StorSimple 8000-serie specificaties](storsimple-technical-specifications-and-compliance.md)bekijken.

> [!IMPORTANT]
> Het gebruik van een StorSimple-apparaat als back-updoel wordt alleen ondersteund voor StorSimple 8000 Update 3 en latere versies.

## <a name="architecture-overview"></a>Overzicht van de architectuur

In de volgende tabellen worden de eerste richtlijnen van het apparaat model-naar-architectuur weergegeven.

**StorSimple-capaciteiten voor lokale en cloudopslag**

| Opslagcapaciteit       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Lokale opslagcapaciteit | &lt;10 TiB\*  | &lt;20 TiB\*  |
| Cloudopslagcapaciteit | &gt;200 TiB\* | &gt;500 TiB\* |

\*Opslaggrootte gaat uit van geen ontdubbeling of compressie.

**StorSimple-capaciteiten voor primaire en secundaire back-ups**

| Back-upscenario  | Lokale opslagcapaciteit  | Cloudopslagcapaciteit  |
|---|---|---|
| Primaire back-up  | Recente back-ups die zijn opgeslagen op lokale opslag voor snel herstel om te voldoen aan de doelstelling van het herstelpunt (RPO) | Back-upgeschiedenis (RPO) past in cloudcapaciteit |
| Secundaire back-up | Secundaire kopie van back-upgegevens kan worden opgeslagen in cloudcapaciteit  | N.v.t.  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple als primair back-updoel

In dit scenario worden StorSimple-volumes gepresenteerd aan de back-uptoepassing als de enige opslagplaats voor back-ups. De volgende afbeelding toont een oplossingsarchitectuur waarin alle back-ups storSimple-gelaagde volumes gebruiken voor back-ups en herstel.

![StorSimple als een primair logisch back-updoeldiagram](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logische stappen primaire doelback-up

1.  De back-upserver neemt contact op met de doelback-upagent en de back-upagent verzendt gegevens naar de back-upserver.
2.  De back-upserver schrijft gegevens naar de gelaagde volumes van StorSimple.
3.  De back-upserver werkt de catalogusdatabase bij en voltooit vervolgens de back-uptaak.
4.  Een momentopnamescript activeert de StorSimple-cloudmomentopnamemanager (starten of verwijderen).
5.  De back-upserver verwijdert verlopen back-ups op basis van een bewaarbeleid.


### <a name="primary-target-restore-logical-steps"></a>Logische stappen voor primaire doelstelling herstellen

1.  De back-upserver begint met het herstellen van de juiste gegevens uit de opslagopslagplaats.
2.  De back-upagent ontvangt de gegevens van de back-upserver.
3.  De back-upserver voltooit de hersteltaak.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple als secundair back-updoel

In dit scenario worden StorSimple-volumes voornamelijk gebruikt voor langetermijnretentie of archivering.

De volgende afbeelding toont een architectuur waarin initiële back-ups en herstelde een high-performance volume targeten. Deze back-ups worden gekopieerd en gearchiveerd naar een StorSimple-gelaagd volume volgens een vast schema.

Het is belangrijk om uw high-performance volume te vergroten, zodat het uw retentiebeleidscapaciteit en prestatievereisten aankan.

![StorSimple als een logisch secundair back-updoeldiagram](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logische stappen voor secundaire doelback-ups

1.  De back-upserver neemt contact op met de doelback-upagent en de back-upagent verzendt gegevens naar de back-upserver.
2.  De back-upserver schrijft gegevens naar krachtige opslag.
3.  De back-upserver werkt de catalogusdatabase bij en voltooit vervolgens de back-uptaak.
4.  De back-upserver kopieert back-ups naar StorSimple op basis van een bewaarbeleid.
5.  Een momentopnamescript activeert de StorSimple-cloudmomentopnamemanager (starten of verwijderen).
6.  De back-upserver verwijdert verlopen back-ups op basis van een bewaarbeleid.

### <a name="secondary-target-restore-logical-steps"></a>Logische stappen voor secundair doelherstellen

1.  De back-upserver begint met het herstellen van de juiste gegevens uit de opslagopslagplaats.
2.  De back-upagent ontvangt de gegevens van de back-upserver.
3.  De back-upserver voltooit de hersteltaak.

## <a name="deploy-the-solution"></a>De oplossing implementeren

Het implementeren van de oplossing vereist drie stappen:
1. Bereid de netwerkinfrastructuur voor.
2. Implementeer uw StorSimple-apparaat als back-updoel.
3. Back-up Exec implementeren.

Elke stap wordt in detail besproken in de volgende secties.

### <a name="set-up-the-network"></a>Netwerk instellen

Omdat StorSimple een oplossing is die is geïntegreerd met de Azure-cloud, vereist StorSimple een actieve en werkende verbinding met de Azure-cloud. Deze verbinding wordt gebruikt voor bewerkingen zoals cloudmomentopnamen, beheer en overdracht van metagegevens, en voor oudere, minder geopende gegevens naar Azure-cloudopslag.

Om de oplossing optimaal te laten presteren, raden we u aan deze best practices voor netwerken te volgen:

-   De koppeling die uw StorSimple-lagen verbindt met Azure, moet voldoen aan uw bandbreedtevereisten. Om dit te bereiken, past u het benodigde Quality of Service (QoS)-niveau toe op uw infrastructuurswitches om aan uw RPO- en hersteltijddoelstelling (RTO) SLA's te voldoen.
-   Maximale azure blob-opslagtoegangslatenmoeten ongeveer 80 ms zijn.

### <a name="deploy-storsimple"></a>StorSimple implementeren

Zie [Uw on-premises StorSimple-apparaat implementeren](storsimple-deployment-walkthrough-u2.md)voor een stapsgewijze implementatiehandleiding voor een stapsgewijze storsimple-implementatiehandleiding.

### <a name="deploy-backup-exec"></a>Back-upexec implementeren

Zie [Aanbevolen procedures voor back-upexec-installatie](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464)voor aanbevolen procedures voor de installatie van Backup Exec .

## <a name="set-up-the-solution"></a>De oplossing instellen

In deze sectie tonen we enkele configuratievoorbeelden. De volgende voorbeelden en aanbevelingen illustreren de meest fundamentele en fundamentele uitvoering. Deze implementatie is mogelijk niet rechtstreeks van toepassing op uw specifieke back-upvereisten.

### <a name="set-up-storsimple"></a>StorSimple instellen

| StorSimple-implementatietaken  | Aanvullende opmerkingen |
|---|---|
| Implementeer uw on-premises StorSimple-apparaat. | Ondersteunde versies: Update 3 en latere versies. |
| Schakel het back-updoel in. | Gebruik deze opdrachten om de back-updoelmodus in of uit te schakelen en status te krijgen. Zie [Op afstand verbinding maken met een StorSimple-apparaat](storsimple-remote-connect.md)voor meer informatie.</br> De back-upmodus `Set-HCSBackupApplianceMode -enable`inschakelen: . </br> De back-upmodus `Set-HCSBackupApplianceMode -disable`uitschakelen: . </br> Ga als u de huidige `Get-HCSBackupApplianceMode`instellingen voor de back-upmodus instellen: . |
| Maak een algemene volumecontainer voor uw volume waarmee de back-upgegevens worden opgeslagen. Alle gegevens in een volumecontainer worden gededuplicate. | StorSimple-volumecontainers definiëren ontdubbelingsdomeinen.  |
| Maak StorSimple-volumes. | Maak volumes met zo dicht mogelijk bij het verwachte gebruik, omdat de volumegrootte van invloed is op de duur van de periode van de momentopname in de cloud. Lees meer over [bewaarbeleid](#retention-policies)voor informatie over het vergroten van een volume.</br> </br> Gebruik Gelaagde volumes van StorSimple en schakel het selectievakje **Dit volume gebruiken voor minder vaak geopende archiefgegevens** in. </br> Het gebruik van alleen lokaal vastgemaakte volumes wordt niet ondersteund. |
| Maak een uniek StorSimple-back-upbeleid voor alle back-updoelvolumes. | Een StorSimple-back-upbeleid definieert de groep voor volumeconsistentie. |
| Schakel de planning uit als de momentopnamen verlopen. | Momentopnamen worden geactiveerd als een nabewerking. |

### <a name="set-up-the-host-backup-server-storage"></a>De opslag van de hostback-upserver instellen

Stel de opslag van de hostback-upserver in volgens deze richtlijnen:  

- Gebruik geen overspannen volumes (gemaakt door Windows Disk Management). Overspannen schijven worden niet ondersteund.
- Maak uw volumes op met NTFS met een toewijzingsgrootte van 64 KB.
- Breng de StorSimple-volumes rechtstreeks naar de Backup Exec-server.
    - Gebruik iSCSI voor fysieke servers.
    - Gebruik doorgeefschijven voor virtuele servers.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Aanbevolen procedures voor StorSimple en Backup Exec

Stel uw oplossing in volgens de richtlijnen in de volgende secties.

### <a name="operating-system-best-practices"></a>Aanbevolen procedures voor besturingssysteem

- Windows Server-versleuteling en ontdubbeling voor het NTFS-bestandssysteem uitschakelen.
- Windows Server-defragmentatie uitschakelen op de StorSimple-volumes.
- Windows Server-indexering op de StorSimple-volumes uitschakelen.
- Voer een antivirusscan uit bij de bronhost (niet tegen de StorSimple-volumes).
- Schakel het [standaardonderhoud van Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) uit in Taakbeheer. Doe dit op een van de volgende manieren:
  - Schakel de onderhoudsconfigurator uit in Windows Taakplanner.
  - Download [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) van Windows Sysinternals. Nadat u PsExec hebt gedownload, voert u Azure PowerShell uit als beheerder en typt u:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple best practices

  -   Zorg ervoor dat het StorSimple-apparaat wordt bijgewerkt naar [Update 3 of hoger.](storsimple-install-update-3.md)
  -   Isoleer iSCSI- en cloudverkeer. Gebruik speciale iSCSI-verbindingen voor verkeer tussen StorSimple en de back-upserver.
  -   Zorg ervoor dat uw StorSimple-apparaat een speciaal back-updoelwit is. Gemengde workloads worden niet ondersteund omdat ze van invloed zijn op uw RTO en RPO.

### <a name="backup-exec-best-practices"></a>Back-up van Exec best practices

-   Back-up Exec moet worden geïnstalleerd op een lokale schijf van de server, en niet op een StorSimple-volume.
-   Stel de gelijktijdige **schrijfbewerkingen** van Backup Exec-opslag in op het maximaal toegestane.
    -   Stel het opslagblok Backup Exec **en de buffergrootte** in op 512 KB.
    -   Schakel back-up exec-opslag **bufferd lezen en schrijven**in.
-   StorSimple ondersteunt back-ups van Backup Exec volledige en incrementele back-ups. Wij raden u aan geen synthetische en differentiële back-ups te gebruiken.
-   Back-upgegevensbestanden mogen alleen gegevens bevatten voor een specifieke taak. Er zijn bijvoorbeeld geen media-toevoegen aan verschillende taken toegestaan.
-   Taakverificatie uitschakelen. Indien nodig moet de verificatie worden gepland na de laatste back-uptaak. Het is belangrijk om te begrijpen dat deze taak van invloed is op uw back-upvenster.
-   Selecteer **Eigenschappen van uw** > **schijfgegevens** > **Details** > opslaan **.** Schijfruimte **vooraf toewijzen**uitschakelen .

Voor de nieuwste Backup Exec-instellingen en best practices voor het implementeren van deze vereisten, zie [de website van Veritas.](https://www.veritas.com)

## <a name="retention-policies"></a>Bewaarbeleid

Een van de meest voorkomende back-up retentie beleid types is een grootvader, vader en zoon (GFS) beleid. In een GFS-beleid wordt dagelijks een incrementele back-up uitgevoerd en worden de volledige back-ups wekelijks en maandelijks uitgevoerd. Dit beleid resulteert in zes StorSimple gelaagde volumes. Eén volume bevat de wekelijkse, maandelijkse en jaarlijkse volledige back-ups. De andere vijf volumes slaan dagelijkse incrementele back-ups op.

In het volgende voorbeeld gebruiken we een GFS-rotatie. In het voorbeeld wordt als volgt uitgegaan van:

-   Niet-ontdoste of gecomprimeerde gegevens worden gebruikt.
-   Volledige back-ups zijn 1 TiB per stuk.
-   Dagelijkse incrementele back-ups zijn 500 GiB per stuk.
-   Vier wekelijkse back-ups worden bewaard voor een maand.
-   Twaalf maandelijkse back-ups worden bewaard voor een jaar.
-   Een jaarlijkse back-up wordt bewaard voor 10 jaar.

Maak op basis van de voorgaande aannames een 26-TiB StorSimple-gelaagd volume voor de maandelijkse en jaarlijkse volledige back-ups. Maak een 5-TiB StorSimple-gelaagd volume voor elk van de incrementele dagelijkse back-ups.

| Behoud van back-uptype | Grootte (TiB) | GFS-vermenigvuldiger\* | Totale capaciteit (TiB)  |
|---|---|---|---|
| Wekelijks vol | 1 | 4  | 4 |
| Dagelijkse incrementele | 0.5 | 20 (cycli gelijk aan aantal weken per maand) | 12 (2 voor extra contingent) |
| Maandelijks vol | 1 | 12 | 12 |
| Jaarlijks vol | 1  | 10 | 10 |
| GFS-eis |   | 38 |   |
| Extra contingent  | 4  |   | 42 totale GFS-eis  |

\*De GFS multiplier is het aantal exemplaren dat u moet beschermen en behouden om te voldoen aan uw vereisten voor back-upbeleid.

## <a name="set-up-backup-exec-storage"></a>Backup Exec-opslag instellen

### <a name="to-set-up-backup-exec-storage"></a>Backup Exec-opslag instellen

1.  Selecteer in de beheerconsole Back-up exec de optie > **Opslagopslag** > **op schijf configureren** > **volgende**. **Storage**

    ![Back-up van Exec-beheerconsole, opslagpagina configureren](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Selecteer **Schijfopslag**en selecteer **Volgende**.

    ![Back-up van Exec-beheerconsole, opslagpagina selecteren](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Voer bijvoorbeeld een representatieve naam in, bijvoorbeeld **Zaterdag Vol**en een beschrijving. Selecteer **Volgende**.

    ![Pagina Back-up van Exec-beheerconsole, naam en beschrijving](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Selecteer de schijf waar u het schijfopslagapparaat wilt maken en selecteer **Volgende**.

    ![Back-up Van Exec-beheerconsole, pagina voor opslagschijfselectie](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Het aantal schrijfbewerkingen verhogen tot **16**en selecteer **Volgende**.

    ![Back-up van Exec-beheerconsole, pagina met gelijktijdige schrijfbewerkingen](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Controleer de instellingen en selecteer **Voltooien**.

    ![Back-up Van Exec-beheerconsole, overzichtspagina voor opslagconfiguratie](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Wijzig aan het einde van elke volumetoewijzing de instellingen van het opslagapparaat die overeenkomen met de instellingen die aanbevolen worden bij [Aanbevolen procedures voor StorSimple en Backup Exec.](#best-practices-for-storsimple-and-backup-exec)

    ![Back-up Van Exec-beheerconsole, pagina instellingen voor opslagapparaten](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Herhaal stap 1-7 totdat u klaar bent met het toewijzen van uw StorSimple-volumes aan Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>StorSimple instellen als primair back-updoel

> [!NOTE]
> Het herstellen van gegevens van een back-up die is gelaagd naar de cloud, vindt plaats bij cloudsnelheden.

In de volgende afbeelding wordt de toewijzing van een normaal volume aan een back-uptaak weergegeven. In dit geval worden alle wekelijkse back-ups toegewezen aan de volledige schijf van de zaterdag en worden de incrementele back-ups toegewezen aan incrementele schijven van maandag tot en met vrijdag. Alle back-ups en herstelt zijn van een StorSimple gelaagd volume.

![Logisch diagram voor primaire back-updoelconfiguratie](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple als een primaire back-up doel GFS schema voorbeeld

Hier is een voorbeeld van een GFS-rotatieschema voor vier weken, maandelijks en jaarlijks:

| Type Frequentie/back-up | Volledig | Incrementeel (dagen 1-5)  |   
|---|---|---|
| Weekblad (weken 1-4) | Saturday | Maandag t/m vrijdag |
| Maandelijks  | Saturday  |   |
| Jaar | Saturday  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-volumes toewijzen aan een back-uptaak back-up van Back-up exec

In de volgende reeks wordt ervan uitgegaan dat Backup Exec en de doelhost zijn geconfigureerd in overeenstemming met de richtlijnen voor back-upexec-agent.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-volumes toewijzen aan een back-uptaak back-up van Back-up exec

1.  Selecteer**hostback-up** > **back-up naar schijf**in de beheerconsole Back-up van **Exec.** > 

    ![Back-up van Exec-beheerconsole, host, back-up en back-up naar schijf selecteren](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Selecteer in het dialoogvenster **Eigenschappen van back-updefinitie** onder **Back-up**de optie **Bewerken**.

    ![Back-up van Exec-beheerconsole, dialoogvenster Eigenschappen van back-updefinitie](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Stel uw volledige en incrementele back-ups in, zodat ze voldoen aan uw RPO- en RTO-vereisten en voldoen aan de beste praktijken van Veritas.

4.  Selecteer **Opslag**in het dialoogvenster **Back-upopties** .

    ![Back-up van Exec-beheerconsole, dialoogvenster Opslag van back-upopties](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Wijs overeenkomstige StorSimple-volumes toe aan uw back-upschema.

    > [!NOTE]
    > **Compressie-** en **versleutelingstype** zijn ingesteld op **Geen**.

6.  Schakel **onder Verifiëren**het selectievakje Gegevens voor deze taak niet **verifiëren** in. Het gebruik van deze optie kan van invloed zijn op storSimple-laaglaag.

    > [!NOTE]
    > Defragmentatie, indexering en achtergrondverificatie hebben een negatieve invloed op de StorSimple-laaglaag.

    ![Back-up Exec-beheerconsole, Back-upopties controleren instellingen](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Wanneer u de rest van uw back-upopties hebt ingesteld om aan uw vereisten te voldoen, selecteert u **OK** om te voltooien.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>StorSimple instellen als secundair back-updoel

> [!NOTE]
>Gegevens herstelt van een back-up die is gelaagd naar de cloud optreden bij cloudsnelheden.

In dit model moet u een opslagmedium (andere dan StorSimple) hebben om als tijdelijke cache te kunnen dienen. U bijvoorbeeld een redundante array van onafhankelijke schijven (RAID)-volume gebruiken om ruimte, invoer/uitvoer (I/O) en bandbreedte te kunnen aanpassen. We raden u aan RAID 5, 50 en 10 te gebruiken.

De volgende figuur toont typische korte termijn retentie lokale (aan de server) volumes en lange termijn retentie archieven volumes. In dit scenario worden alle back-ups uitgevoerd op het lokale (naar de server) RAID-volume. Deze back-ups worden periodiek gedupliceerd en gearchiveerd naar een archiefvolume. Het is belangrijk om het volume van uw lokale (naar de server) RAID-volume te vergroten, zodat het uw retentiecapaciteit en prestatievereisten op korte termijn aankan.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple als secundaire back-up doel GFS voorbeeld

![StorSimple als logisch diagram voor secundair back-updoel](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

In de volgende tabel ziet u hoe u back-ups instelt die op de lokale en StorSimple-schijven worden uitgevoerd. Het omvat individuele en totale capaciteitsvereisten.

### <a name="backup-configuration-and-capacity-requirements"></a>Vereisten voor back-upconfiguratie en capaciteit

| Back-uptype en retentie | Geconfigureerde opslag | Grootte (TiB) | GFS-vermenigvuldiger | Totale\* capaciteit (TiB) |
|---|---|---|---|---|
| Week 1 (volledig en incrementeel) |Lokale schijf (korte termijn)| 1 | 1 | 1 |
| StorSimple weken 2-4 |StorSimple schijf (lange termijn) | 1 | 4 | 4 |
| Maandelijks vol |StorSimple schijf (lange termijn) | 1 | 12 | 12 |
| Jaarlijks vol |StorSimple schijf (lange termijn) | 1 | 1 | 1 |
|GFS volumes grootte eis |  |  |  | 18*|

\*De totale capaciteit omvat 17 TiB StorSimple-schijven en 1 TiB met lokaal RAID-volume.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS voorbeeldschema: GFS-rotatie wekelijks, maandelijks en jaarlijks schema

| Wekelijks | Volledig | Incrementele dag 1 | Incrementele dag 2 | Incrementele dag 3 | Incrementele dag 4 | Incrementele dag 5 |
|---|---|---|---|---|---|---|
| Week 1 | Lokaal RAID-volume  | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume |
| Week 2 | StorSimple weken 2-4 |   |   |   |   |   |
| Week 3 | StorSimple weken 2-4 |   |   |   |   |   |
| Week 4 | StorSimple weken 2-4 |   |   |   |   |   |
| Maandelijks | StorSimple maandelijks |   |   |   |   |   |
| Jaar | StorSimple jaarlijks  |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>StorSimple-volumes toewijzen aan een Backup Exec-archief en deduplicatietaak

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>StorSimple-volumes toewijzen aan een Backup Exec-archief en duplicatietaak

1.  Klik in de beheerconsole Back-up exec met de rechtermuisknop op de taak die u wilt archiveren op een StorSimple-volume en selecteer vervolgens >  **Eigenschappen van back-updefinitie****bewerken**.

    ![Back-up van Exec-beheerconsole, tabblad Eigenschappen van back-updefinitie](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Selecteer **Werkgebied dupliceren** > toevoegen**aan Schijfbewerken** > **Edit**.

    ![Back-up van Exec-beheerconsole, fase toevoegen](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Selecteer **in** het dialoogvenster Opties dupliceren de waarden die u wilt gebruiken voor **Bron** en **Planning**.

    ![Back-up Exec-beheerconsole, eigenschappen van back-updefinities en dubbele opties](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Selecteer **in** de vervolgkeuzelijst Opslag het storSimple-volume waar u de archieftaak wilt opslaan om de gegevens op te slaan.

    ![Back-up Exec-beheerconsole, eigenschappen van back-updefinities en dubbele opties](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Schakel **Verifiëren**in en schakel het selectievakje **Gegevens voor deze taak niet verifiëren** in.

    ![Back-up Exec-beheerconsole, eigenschappen van back-updefinities en dubbele opties](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Selecteer **OK**.

    ![Back-up Exec-beheerconsole, eigenschappen van back-updefinities en dubbele opties](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Voeg in de kolom **Back-up** een nieuw fase toe. Gebruik voor de bron **incrementele**. Kies voor het doel het StorSimple-volume waarbij de incrementele back-uptaak wordt gearchiveerd. Herhaal stap 1-6.

## <a name="storsimple-cloud-snapshots"></a>StorSimple cloudsnapshots

StorSimple cloudsnapshots beschermen de gegevens die zich in uw StorSimple-apparaat bevinden. Het maken van een cloudmomentopname is gelijk aan het verzenden van lokale back-uptapes naar een offsite-faciliteit. Als u azure georedundant-opslag gebruikt, is het maken van een cloudmomentopname gelijk aan het verzenden van back-uptapes naar meerdere sites. Als u een apparaat na een ramp moet herstellen, u een ander StorSimple-apparaat online brengen en een failover uitvoeren. Na de failover hebt u toegang tot de gegevens (met cloudsnelheden) vanaf de meest recente cloudmomentopname.

In de volgende sectie wordt beschreven hoe u een kort script maakt om StorSimple-cloudmomentopnamen te starten en te verwijderen tijdens back-upnabewerking.

> [!NOTE]
> Momentopnamen die handmatig of programmatisch zijn gemaakt, volgen niet het storSimple-momentopnameverloopbeleid. Deze momentopnamen moeten handmatig of programmatisch worden verwijderd.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Cloudmomentopnamen starten en verwijderen met behulp van een script

> [!NOTE]
> Beoordeel zorgvuldig de gevolgen voor naleving en gegevensbewaring voordat u een StorSimple-momentopname verwijdert. Zie de [Exec-documentatie voor back-ups](https://www.veritas.com/support/en_US/article.100032497.html)voor meer informatie over het uitvoeren van een script na de back-up.

### <a name="backup-lifecycle"></a>Levenscyclus van back-ups

![Diagram back-uplevenscyclus](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Vereisten

-   De server waarop het script wordt uitgevoerd, moet toegang hebben tot Azure-cloudbronnen.
-   Het gebruikersaccount moet over de nodige machtigingen beschikken.
-   Een StorSimple-back-upbeleid met de bijbehorende StorSimple-volumes moet zijn ingesteld, maar niet ingeschakeld.
-   U hebt de naam van de StorSimple-bron, registratiesleutel, apparaatnaam en back-upbeleids-ID nodig.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Een cloudmomentopname starten of verwijderen

1. [Azure PowerShell installeren](/powershell/azure/overview).
2. Download en setup [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell script.
3. Voer PowerShell uit op de server waarop het script wordt uitgevoerd als beheerder. Zorg ervoor dat u `-WhatIf $true` het script uitvoert om te zien met welke wijzigingen het script zal aanbrengen. Zodra de validatie is `-WhatIf $false`voltooid, geeft u door . Voer de onderstaande opdracht uit:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Voeg het script toe aan uw back-uptaak in Backup Exec door de voorbewerkings- en nabewerkingsopdrachten van de back-upexec-taakopties te bewerken.

   ![Back-up Exec-console, back-upopties, tabblad voor- en nabewerking](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> We raden u aan uw StorSimple-back-upbeleid voor cloudmomentopnamen uit te voeren als een script voor nabewerking aan het einde van uw dagelijkse back-uptaak. Voor meer informatie over het maken van een back-up en het herstellen van uw back-uptoepassingsomgeving om u te helpen uw RPO en RTO te ontmoeten, u contact opnemen met uw back-uparchitect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple als herstelbron

Herstelt van een StorSimple-apparaat werk, zoals herstelt van elk blok opslagapparaat. Herstel van gegevens die naar de cloud zijn gelaagd, vindt plaats bij cloudsnelheden. Voor lokale gegevens vinden herstelberichten plaats op de lokale schijfsnelheid van het apparaat. Zie de exec-documentatie back-up voor informatie over het uitvoeren van een herstel. We raden u aan om zich te conformeren aan back-upexec-herstelaanbevolen procedures.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple failover en disaster recovery

> [!NOTE]
> Voor back-updoelscenario's wordt StorSimple Cloud Appliance niet ondersteund als hersteldoel.

Een ramp kan worden veroorzaakt door een verscheidenheid van factoren. In de volgende tabel worden veelvoorkomende scenario's voor noodherstel weergegeven.

| Scenario | Impact | Hoe te herstellen | Opmerkingen |
|---|---|---|---|
| StorSimple-apparaatstoring | Back-up- en herstelbewerkingen worden onderbroken. | Vervang het defecte apparaat en voer [StorSimple-failover en disaster recovery](storsimple-device-failover-disaster-recovery.md)uit. | Als u een herstel na herstel van het apparaat moet uitvoeren, worden de volledige gegevenswerksets opgehaald van de cloud naar het nieuwe apparaat. Alle bewerkingen zijn op cloudsnelheden. Het indexerings- en catalogiseren van het opnieuw scannen kan ertoe leiden dat alle back-upsets worden gescand en van de cloudlaag naar de lokale apparaatlaag worden getrokken, wat een tijdrovend proces kan zijn. |
| Back-up Exec-serverfout | Back-up- en herstelbewerkingen worden onderbroken. | Herbouw de back-upserver en voer databaseherstel uit zoals beschreven in [Het handmatig e-mailen en herstellen van back-upexec-database (BEDB).](http://www.veritas.com/docs/000041083) | U moet de Backup Exec-server opnieuw opbouwen of herstellen op de site voor noodherstel. Herstel de database naar het meest recente punt. Als de herstelde Exec-database met back-ups niet synchroon loopt met uw nieuwste back-uptaken, is indexering en catalogisering vereist. Dit indexerings- en catalogusherscanproces kan ertoe leiden dat alle back-upsets worden gescand en van de cloudlaag naar de lokale apparaatlaag worden getrokken. Dit maakt het verder tijdsintensief. |
| Sitefout die resulteert in het verlies van zowel de back-upserver als StorSimple | Back-up- en herstelbewerkingen worden onderbroken. | Herstel StorSimple eerst en herstel vervolgens Backup Exec. | Herstel StorSimple eerst en herstel vervolgens Backup Exec. Als u een herstel na herstel van het apparaat moet uitvoeren, worden de volledige gegevenswerksets opgehaald van de cloud naar het nieuwe apparaat. Alle bewerkingen zijn op cloudsnelheden. |

## <a name="references"></a>Verwijzingen

Naar de volgende documenten voor dit artikel is verwezen:

- [StorSimple multipath I/O setup](storsimple-configure-mpio-windows-server.md)
- [Opslagscenario's: thin provisioning](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT-stations gebruiken](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Schaduwkopieën instellen voor gedeelde mappen](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen vanuit een back-upset](storsimple-restore-from-backup-set-u2.md).
- Meer informatie over het uitvoeren van [apparaatfailover en disaster recovery](storsimple-device-failover-disaster-recovery.md).
