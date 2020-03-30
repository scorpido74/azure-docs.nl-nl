---
title: HANA-back-up en herstel op SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: HANA-back-up en herstel uitvoeren op SAP HANA op Azure (Grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430088"
---
# <a name="backup-and-restore"></a>Back-ups en herstellen

>[!IMPORTANT]
>Dit artikel is geen vervanging voor de SAP HANA-beheerdocumentatie of SAP Notes. Wij verwachten dat u een goed begrip en expertise hebt in SAP HANA-beheer en -activiteiten, met name voor back-up, herstel, hoge beschikbaarheid en disaster recovery. In dit artikel worden screenshots van SAP HANA Studio getoond. Inhoud, structuur en de aard van de schermen van SAP-beheertools en de tools zelf kunnen veranderen van SAP HANA-release naar release.

Het is belangrijk dat u stappen en processen uitoefent die in uw omgeving en met uw HANA-versies en -releases worden genomen. Sommige processen beschreven in dit artikel zijn vereenvoudigd voor een beter algemeen begrip. Ze zijn niet bedoeld om te worden gebruikt als gedetailleerde stappen voor eventuele operatie handboeken. Als u bewerkingshandboeken voor uw configuraties wilt maken, test en oefent u uw processen uit en documenteert u de processen die verband houden met uw specifieke configuraties. 

Een van de belangrijkste aspecten van het bedienen van databases is om ze te beschermen tegen catastrofale gebeurtenissen. De oorzaak van deze gebeurtenissen kan van alles zijn, van natuurrampen tot eenvoudige gebruikersfouten.

Als u een back-up van een database maakt, met de mogelijkheid om deze te herstellen naar elk moment, bijvoorbeeld voordat iemand kritieke gegevens verwijderde, kan worden hersteld naar een status die zo dicht mogelijk bij de manier waarop deze vóór de onderbreking was.

Er moeten twee soorten back-ups worden uitgevoerd om de mogelijkheid te bereiken om te herstellen:

- Databaseback-ups: volledige, incrementele of differentiële back-ups
- Back-ups van transactielogboeken

Naast back-ups met volledige database die op toepassingsniveau worden uitgevoerd, u back-ups uitvoeren met opslagmomentopnamen. Opslagmomentopnamen vervangen geen back-ups van transactielogboeken. Transactielogboekback-ups blijven belangrijk om de database te herstellen naar een bepaald tijdstip of om de logboeken van reeds vastgelegde transacties te legen. Opslagmomentopnamen kunnen het herstel versnellen door snel een roll-forward-afbeelding van de database te geven. 

SAP HANA op Azure (Large Instances) biedt twee back-up- en herstelopties:

- **Doe het zelf (DIY).** Nadat u ervoor hebt gezorgd dat er voldoende schijfruimte is, voert u volledige database- en logboekback-ups uit met behulp van een van de volgende schijfback-upmethoden. U een back-up maken van de volumes die zijn gekoppeld aan de HANA Large Instance-eenheden of aan NFS-shares die zijn ingesteld in een virtuele Azure-machine (VM). In het laatste geval stellen klanten een Linux-vm in Azure in, koppelen ze Azure Storage aan de VM en delen ze de opslag via een geconfigureerde NFS-server in die VM. Als u de back-up uitvoert tegen volumes die rechtstreeks aan HANA Large Instance-eenheden worden gekoppeld, kopieert u de back-ups naar een Azure-opslagaccount. Doe dit nadat u een Azure VM hebt ingesteld waarmee NFS-shares worden geëxporteerd die zijn gebaseerd op Azure Storage. U ook een Azure Backup-kluis of azure-koudeopslag gebruiken. 

   Een andere optie is om een hulpprogramma voor gegevensbescherming van derden te gebruiken om de back-ups op te slaan nadat ze zijn gekopieerd naar een Azure-opslagaccount. De doe-het-back-upoptie kan ook nodig zijn voor gegevens die u voor langere tijd moet opslaan voor nalevings- en controledoeleinden. In alle gevallen worden de back-ups gekopieerd naar NFS-shares die worden weergegeven via een VM- en Azure-opslag.

- **Infrastructuur back-up en herstel functionaliteit.** U ook de back-up- en herstelfunctionaliteit gebruiken die de onderliggende infrastructuur van SAP HANA op Azure (Large Instances) biedt. Deze optie voldoet aan de behoefte aan back-ups en snelle herstelt. De rest van deze sectie richt zich op de back-up- en herstelfunctionaliteit die wordt aangeboden met HANA Large Instances. Deze sectie behandelt ook de relatie die back-up en herstel hebben met de disaster recovery-functionaliteit die wordt aangeboden door HANA Large Instances.

> [!NOTE]
>   De momentopnametechnologie die wordt gebruikt door de onderliggende infrastructuur van HANA Large Instances, is afhankelijk van SAP HANA-snapshots. Op dit moment werken SAP HANA-snapshots niet samen met meerdere tenants van SAP HANA multitenant databasecontainers. Als er slechts één tenant wordt geïmplementeerd, werken SAP HANA-snapshots wel en u deze methode gebruiken.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Opslagmomentopnamen van SAP HANA gebruiken op Azure (grote exemplaren)

De opslaginfrastructuur die ten grondslag ligt aan SAP HANA op Azure (Large Instances) ondersteunt opslagmomentopnamen van volumes. Zowel back-up als herstel van volumes wordt ondersteund, met de volgende overwegingen:

- In plaats van volledige databaseback-ups worden momentopnamen voor opslagvolume regelmatig gemaakt.
- Wanneer een momentopname wordt geactiveerd via /hana/data en /hana/shared, waaronder /usr/sap, volumes, initieert de snapshot-technologie een SAP HANA-momentopname voordat de opslagmomentopname wordt uitgevoerd. Deze SAP HANA-momentopname is het installatiepunt voor eventuele logboekherstelen na herstel van de opslagmomentopname. Om een HANA-momentopname succesvol te laten zijn, heb je een actief HANA-exemplaar nodig. In een HSR-scenario wordt een opslagmomentopname niet ondersteund op een huidig secundair knooppunt waar geen HANA-momentopname kan worden uitgevoerd.
- Nadat de opslagmomentopname is uitgevoerd, wordt de SAP HANA-momentopname verwijderd.
- Transactielogboekback-ups worden regelmatig genomen en opgeslagen in het /hana/logbackups-volume of in Azure. U het volume /hana/logbackups activeren dat de back-ups van het transactielogboek bevat om een momentopname afzonderlijk te maken. In dat geval hoeft u geen HANA-momentopname uit te voeren.
- Als u een database moet herstellen naar een bepaald tijdstip, moet u voor een productiestoring microsoft Azure Support of SAP HANA op Azure laten herstellen naar een bepaalde opslagmomentopname. Een voorbeeld is een geplande restauratie van een sandbox-systeem in de oorspronkelijke staat.
- De SAP HANA-momentopname die is opgenomen in de opslagmomentopname is een verschuivingspunt voor het toepassen van transactielogboekback-ups die zijn uitgevoerd en zijn opgeslagen nadat de opslagmomentopname is gemaakt.
- Deze back-ups van het transactielogboek worden genomen om de database terug te herstellen naar een bepaald tijdstip.

U opslagmomentopnamen uitvoeren die zich richten op drie klassen van volumes:

- Een gecombineerde momentopname over /hana/data en /hana/shared, inclusief /usr/sap. Deze momentopname vereist het maken van een SAP HANA-momentopname als voorbereiding op de opslagmomentopname. De SAP HANA-momentopname zorgt ervoor dat de database consistent is vanuit het oogpunt van opslag. Voor het herstelproces is dat een punt om op in te stellen.
- Een aparte momentopname over /hana/logbackups.
- Een partitie van het besturingssysteem.

Zie [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de nieuwste momentopnamescripts en -documentatie. Wanneer u het snapshot script pakket van [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)downloadt, krijgt u drie bestanden. Een van de bestanden is gedocumenteerd in een PDF voor de functionaliteit die wordt verstrekt. Nadat u de gereedschapsset hebt gedownload, volgt u de instructies in 'Download de momentopnamegereedschappen'.

## <a name="storage-snapshot-considerations"></a>Overwegingen voor opslagmomentopnamen

>[!NOTE]
>Opslagmomentopnamen verbruiken opslagruimte die is toegewezen aan de HANA Large Instance-eenheden. Houd rekening met de volgende aspecten van het plannen van opslagmomentopnamen en het aantal opslagmomentopnamen dat moet worden opgeslagen. 

De specifieke mechanica van storage snapshots voor SAP HANA op Azure (Large Instances) omvatten:

- Een specifieke opslagmomentopname op het moment dat deze wordt genomen, verbruikt weinig opslagruimte.
- Naarmate de gegevensinhoud verandert en de inhoud in SAP HANA-gegevensbestanden verandert op het opslagvolume, moet de momentopname de oorspronkelijke blokinhoud en de gegevenswijzigingen opslaan.
- Als gevolg hiervan wordt de momentopname van de opslag groter. Hoe langer de momentopname bestaat, hoe groter de opslagmomentopname wordt.
- Hoe meer wijzigingen er worden aangebracht in het SAP HANA-databasevolume gedurende de levensduur van een opslagmomentopname, hoe groter het ruimteverbruik van de opslagmomentopname.

SAP HANA op Azure (Large Instances) wordt geleverd met vaste volumegroottes voor de SAP HANA-gegevens en logboekvolumes. Het uitvoeren van snapshots van deze volumes eet in uw volumeruimte. Je moet:

- Bepaal wanneer u momentopnamen voor opslag moet plannen.
- Houd het ruimteverbruik van de opslagvolumes in de gaten. 
- Beheer het aantal momentopnamen dat u opslaat. 

U de opslagmomentopnamen uitschakelen wanneer u massa's gegevens importeert of andere belangrijke wijzigingen in de HANA-database uitvoert. 


In de volgende secties vindt u informatie voor het uitvoeren van deze momentopnamen en worden algemene aanbevelingen opgenomen:

- Hoewel de hardware 255 snapshots per volume kan ondersteunen, wilt u ver onder dit aantal blijven. De aanbeveling is 250 of minder.
- Voordat u opslagmomentopnamen uitvoert, moet u de vrije ruimte bewaken en bijhouden.
- Verlaag het aantal opslagmomentopnamen op basis van vrije ruimte. U het aantal momentopnamen dat u bewaart verlagen of u de volumes uitbreiden. U extra opslagruimte bestellen in eenheden van 1 terabyte.
- Schakel tijdens activiteiten zoals het verplaatsen van gegevens naar SAP HANA met SAP-platformmigratietools (R3load) of het herstellen van SAP HANA-databases uit back-ups opslagmomentopnamen uit op het /hana/datavolume. 
- Vermijd tijdens grotere reorganisaties van SAP HANA-tabellen opslagmomentopnamen indien mogelijk.
- Opslagmomentopnamen zijn een vereiste om te profiteren van de mogelijkheden voor noodherstel van SAP HANA op Azure (Large Instances).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Vereisten voor het gebruik van selfservice-opslagmomentopnamen

Als u ervoor wilt zorgen dat het momentopnamescript succesvol wordt uitgevoerd, controleert u of Perl is geïnstalleerd op het Linux-besturingssysteem op de HANA Large Instances-server. Perl wordt vooraf geïnstalleerd op uw HANA Large Instance unit. Als u de Perl-versie wilt controleren, gebruikt u de volgende opdracht:

`perl -v`

![De openbare sleutel wordt gekopieerd door deze opdracht uit te voeren](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Opslagmomentopnamen instellen

Voer deze stappen uit om opslagmomentopnamen in te stellen met HANA Large Instances.
1. Zorg ervoor dat Perl is geïnstalleerd op het Linux-besturingssysteem op de HANA Large Instances-server.
1. Wijzig de /etc/ssh/ssh\_config om de lijn _MACs hmac-sha1_toe te voegen.
1. Maak een SAP HANA-back-upgebruikersaccount aan op het hoofdknooppunt voor elk SAP HANA-exemplaar dat u uitvoert, indien van toepassing.
1. Installeer de SAP HANA HDB-client op alle SAP HANA Large Instances-servers.
1. Maak op de eerste SAP HANA Large Instances-server van elke regio een openbare sleutel om toegang te krijgen tot de onderliggende opslaginfrastructuur die het maken van momentopnamen regelt.
1. Kopieer het scripts- en configuratiebestand van [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) naar de locatie van **hdbsql** in de SAP HANA-installatie.
1. Wijzig het *HANABackupDetails.txt-bestand* indien nodig voor de juiste klantspecificaties.

Ontvang de nieuwste snapshotscripts en documentatie van [GitHub.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) Zie [Microsoft-momentopnamehulpprogramma's voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)de eerder genoemde stappen.

### <a name="consideration-for-mcod-scenarios"></a>Aandacht voor MCOD-scenario's
Als u een [MCOD-scenario](https://launchpad.support.sap.com/#/notes/1681092) uitvoert met meerdere SAP HANA-exemplaren op één HANA-eenheid voor grote instanties, worden afzonderlijke opslagvolumes ingericht voor elk van de SAP HANA-exemplaren. Zie 'Belangrijke dingen om te onthouden' in [Microsoft-snapshottools voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)meer informatie over MDC en andere overwegingen.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Stap 1: Installeer de SAP HANA HDB-client

Het Linux-besturingssysteem dat is geïnstalleerd op SAP HANA op Azure (Large Instances) bevat de mappen en scripts die nodig zijn om SAP HANA-opslagmomentopnamen uit te voeren voor back-up- en disaster recovery-doeleinden. Controleer op meer recente releases in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Het is uw verantwoordelijkheid om de SAP HANA HDB-client te installeren op de HANA Large Instance-eenheden terwijl u SAP HANA installeert.

### <a name="step-2-change-the-etcsshssh_config"></a>Stap 2: Verander de /etc/ssh/ssh\_config

Deze stap wordt beschreven in 'Communicatie met opslag inschakelen' in [Microsoft-snapshottools voor SAP HANA op Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)


### <a name="step-3-create-a-public-key"></a>Stap 3: Een openbare sleutel maken

Als u toegang wilt tot de opslagmomentopnameinterfaces van uw HANA Large Instance-tenant, stelt u een aanmeldingsprocedure in via een openbare sleutel. 

Maak op de eerste SAP HANA op Azure (Large Instances) server in uw tenant een openbare sleutel om toegang te krijgen tot de opslaginfrastructuur. Met een openbare sleutel is een wachtwoord niet vereist om u aan te melden bij de opslagmomentopnameinterfaces. U hoeft ook geen wachtwoordreferenties te onderhouden met een openbare sleutel. 

Zie 'Communicatie met opslag inschakelen' in [Microsoft-snapshottools voor SAP HANA op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)om een openbare sleutel te genereren.


### <a name="step-4-create-an-sap-hana-user-account"></a>Stap 4: Een SAP HANA-gebruikersaccount maken

Als u wilt beginnen met het maken van SAP HANA-momentopnamen, maakt u een gebruikersaccount in SAP HANA dat de opslagmomentopnamescripts kunnen gebruiken. Maak hiervoor een SAP HANA-gebruikersaccount aan binnen SAP HANA Studio. De gebruiker moet worden gemaakt onder de SYSTEMDB en *niet* onder de SID-database voor MDC. In de omgeving met één container wordt de gebruiker gemaakt in de tenantdatabase. Dit account moet **back-upbeheerders** en **catalogusleesrechten** hebben. 

Zie 'Communicatie met SAP HANA inschakelen met SAP HANA' in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor het instellen en gebruiken van een gebruikersaccount.


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Stap 5: Het SAP HANA-gebruikersaccount autoriseren

In deze stap autoriseert u het SAP HANA-gebruikersaccount dat u hebt gemaakt, zodat de scripts geen wachtwoorden hoeven in te dienen tijdens runtime. De SAP HANA-opdracht `hdbuserstore` maakt het mogelijk om een SAP HANA-gebruikerssleutel te maken. De sleutel wordt opgeslagen op een of meer SAP HANA-knooppunten. Met de gebruikerssleutel heeft de gebruiker toegang tot SAP HANA zonder dat hij wachtwoorden hoeft te beheren vanuit het scriptproces. Het scriptingsproces wordt later in dit artikel besproken.

>[!IMPORTANT]
>Voer deze configuratieopdrachten uit met dezelfde gebruikerscontext waarin de momentopnameopdrachten worden uitgevoerd. Anders werken de momentopnameopdrachten niet goed.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Stap 6: Download de momentopnamescripts, configureer de momentopnamen en test de configuratie en connectiviteit

Download de meest recente versie van de scripts van [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). De manier waarop de scripts worden geïnstalleerd veranderd met release 4.1 van de scripts. Zie 'Communicatie met SAP HANA inschakelen met SAP HANA' in [Microsoft-snapshottools voor SAP HANA op Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

Zie 'Eenvoudige installatie van snapshottools (standaard)' in [Microsoft-snapshottools voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)de exacte volgorde van opdrachten. Wij raden het gebruik van de standaardinstallatie aan. 

Als u wilt upgraden van versie 3.x naar 4.1, raadpleegt u 'Een bestaande installatie bijwerken' in [Microsoft-snapshottools voor SAP HANA op Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md) Zie 'De installatie van de momentopnamehulpprogramma's ongedaan maken' in [Microsoft-snapshottools voor SAP HANA op Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

Vergeet niet de stappen uit te voeren die zijn beschreven in 'Complete setup of snapshot tools' in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Het doel van de verschillende scripts en bestanden als ze werden geïnstalleerd wordt beschreven in "Wat zijn deze snapshot tools?" in [Microsoft-snapshottools voor SAP HANA op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Voordat u de momentopnametools configureert, moet u ervoor zorgen dat u ook HANA-back-uplocaties en -instellingen correct hebt geconfigureerd. Zie 'SAP HANA-configuratie' in [Microsoft-snapshottools voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)meer informatie.

De configuratie van de snapshot tool set wordt beschreven in "Config bestand - HANABackupCustomerDetails.txt" in [Microsoft snapshot tools voor SAP HANA op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Connectiviteit testen met SAP HANA

Nadat u alle configuratiegegevens in het *HANABackupCustomerDetails.txt-bestand* hebt geplaatst, controleert u of de configuraties correct zijn voor de HANA-instantiegegevens. Gebruik het `testHANAConnection`script , dat onafhankelijk is van een SAP HANA scale-up of scale-out configuratie.

Zie 'De connectiviteit met SAP HANA - testHANAConnection' in [Microsoft-snapshottools voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)meer informatie.

#### <a name="test-storage-connectivity"></a>Opslagconnectiviteit testen

De volgende teststap is het controleren van de connectiviteit met de opslag op basis van de gegevens die u in het *HANABackupCustomerDetails.txt-configuratiebestand* plaatst. Voer vervolgens een testmomentopname uit. Voordat u `azure_hana_backup` de opdracht uitvoert, moet u deze test uitvoeren. Zie 'Connectiviteit met opslag - testStorageSnapshotConnection' controleren in [Microsoft-snapshottools voor SAP HANA op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de reeks opdrachten voor deze test.

Na een succesvolle aanmelding bij de opslag virtuele machine interfaces, het script gaat verder met fase 2 en creëert een test momentopname. De output wordt hier getoond voor een scale-outconfiguratie met drie nodes van SAP HANA.

Als de testmomentopname met succes wordt uitgevoerd met het script, u de werkelijke opslagmomentopnamen plannen. Als het niet lukt, onderzoekt u de problemen voordat u verder gaat. De testmomentopname moet blijven totdat de eerste echte snapshots zijn gemaakt.


### <a name="step-7-perform-snapshots"></a>Stap 7: Momentopnamen uitvoeren

Wanneer de voorbereidingsstappen zijn voltooid, u beginnen met het configureren en plannen van de werkelijke opslagmomentopnamen. Het te laten geplande script werkt met SAP HANA scale-up en scale-out configuraties. Voor periodieke en regelmatige uitvoering van het back-upscript, plant u het script met behulp van het cron-hulpprogramma. 

Zie 'Maak van momentopnameback-up - azure_hana_backup' in [Microsoft-momentopnametools voor SAP HANA op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de exacte syntaxis en functionaliteit van de opdracht. 

Wanneer het `azure_hana_backup` script wordt uitgevoerd, wordt de momentopname van de opslag in de volgende drie fasen gemaakt:

1. Het draait een SAP HANA momentopname.
1. Het voert een opslagmomentopname uit.
1. Het verwijdert de SAP HANA-momentopname die is gemaakt voordat de opslagmomentopname is uitgevoerd.

Als u het script wilt uitvoeren, roept u het aan vanuit de map HDB uitvoerbare map waarnaar het is gekopieerd. 

De bewaartermijn wordt beheerd met het aantal momentopnamen dat als parameter wordt ingediend wanneer u het script uitvoert. De hoeveelheid tijd die wordt gedekt door de opslagmomentopnamen is een functie van de uitvoeringsperiode en van het aantal momentopnamen dat als parameter wordt ingediend wanneer het script wordt uitgevoerd. 

Als het aantal momentopnamen dat wordt bewaard groter is dan het aantal dat als parameter in de aanroep van het script wordt genoemd, wordt de oudste opslagmomentopname van hetzelfde label verwijderd voordat een nieuwe momentopname wordt uitgevoerd. Het nummer dat u als laatste parameter van de aanroep geeft, is het nummer dat u gebruiken om het aantal momentopnamen te bepalen dat wordt bewaard. Met dit nummer u indirect ook de schijfruimte beheren die wordt gebruikt voor momentopnamen. 


## <a name="snapshot-strategies"></a>Momentopnamestrategieën
De frequentie van momentopnamen voor de verschillende typen is afhankelijk van de vraag of u de functionaliteit voor disaster recovery van HANA Large Instance gebruikt. Deze functionaliteit is afhankelijk van opslagmomentopnamen, waarvoor mogelijk speciale aanbevelingen nodig zijn voor de frequentie- en uitvoeringsperioden van de opslagmomentopnamen. 

In de overwegingen en aanbevelingen die volgen, is de veronderstelling dat u de disaster recovery-functionaliteit die HANA Large Instances *biedt, niet* gebruikt. In plaats daarvan gebruikt u de opslagmomentopnamen om back-ups te hebben en u de afgelopen 30 dagen point-in-time-herstel bieden. Houd, gezien de beperkingen van het aantal momentopnamen en de ruimte, rekening met de volgende vereisten:

- De hersteltijd voor point-in-time herstel.
- De gebruikte ruimte.
- Het herstelpunt en hersteltijddoelstellingen voor een mogelijk herstel van een ramp.
- De uiteindelijke uitvoering van HANA full-database back-ups tegen schijven. Wanneer een back-up met volledige database tegen schijven of de **backint-interface** wordt uitgevoerd, mislukt de uitvoering van de opslagmomentopnamen. Als u back-ups met volledige database wilt uitvoeren bovenop opslagmomentopnamen, moet u ervoor zorgen dat de uitvoering van de opslagmomentopnamen gedurende deze periode is uitgeschakeld.
- Het aantal momentopnamen per volume, dat beperkt is tot 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Als u de functionaliteit voor noodherstel van HANA Large Instances niet gebruikt, komt de momentopnameperiode minder vaak voor. Voer in dergelijke gevallen de gecombineerde momentopnamen uit op /hana/data en /hana/shared, waaronder /usr/sap, in perioden van 12 uur of 24 uur. Bewaar de momentopnamen een maand. Hetzelfde geldt voor de momentopnamen van het logboekback-upvolume. De uitvoering van SAP HANA-transactielogboekback-ups tegen het logboekback-upvolume vindt plaats in perioden van 5 minuten tot 15 minuten.

Geplande opslagmomentopnamen kunnen het beste worden uitgevoerd met cron. Gebruik hetzelfde script voor alle back-ups en noodherstelbehoeften. Wijzig de scriptingangen om overeen te komen met de verschillende gevraagde back-uptijden. Deze momentopnamen zijn allemaal anders gepland in cron, afhankelijk van hun uitvoeringstijd. Het kan elk uur, elke 12 uur, dagelijks of wekelijks. 

In het volgende voorbeeld ziet u een cronschema in /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
In het vorige voorbeeld heeft een gecombineerde momentopname per uur betrekking op de volumes die de /hana/gegevens en /hana/shared/SID bevatten, waaronder /usr/sap, locaties. Gebruik dit type momentopname voor een sneller point-in-time herstel in de afgelopen twee dagen. Er is ook een dagelijkse momentopname op die volumes. Dus, je hebt twee dagen van de dekking per uur snapshots plus vier weken dekking door dagelijkse snapshots. Er wordt ook dagelijks een back-up gemaakt van het back-upvolume van het transactielogboek. Deze back-ups worden vier weken bewaard. 

Zoals u ziet in de derde regel van crontab, de back-up van de HANA transactie log is gepland om elke 5 minuten uit te voeren. De begintijden van de verschillende cron-taken die opslagmomentopnamen uitvoeren, zijn gespreid. Op deze manier worden de momentopnamen niet in één keer tegelijk uitgevoerd op een bepaald moment. 

In het volgende voorbeeld voert u een gecombineerde momentopname uit die betrekking heeft op de volumes die de /hana/gegevens en /hana/shared/SID bevatten, waaronder /usr/sap, locaties op uurbasis. Je bewaart deze foto's twee dagen. De momentopnamen van de back-upvolumes van het transactielogboek worden uitgevoerd op basis van 5 minuten en worden vier uur bewaard. Net als voorheen wordt de back-up van het HANA-transactielogboekbestand elke 5 minuten uitgevoerd. 

De momentopname van het back-upvolume van het transactielogboek wordt uitgevoerd met een vertraging van 2 minuten nadat de back-up van het transactielogboek is gestart. Onder normale omstandigheden, de SAP HANA transactie log back-up eindigt binnen die 2 minuten. Net als voorheen wordt het volume dat de boot LUN bevat één keer per dag ondersteund door een opslagmomentopname en wordt het gedurende vier weken bewaard.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

De volgende afbeelding illustreert de sequenties van het vorige voorbeeld. De boot LUN is uitgesloten.

![Relatie tussen back-ups en momentopnamen](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA voert regelmatig schrijft uit tegen het /hana/log volume om de vastgelegde wijzigingen in de database te documenteren. Sap HANA schrijft regelmatig een savepoint op het /hana/datavolume. Zoals aangegeven in crontab, wordt elke 5 minuten een SAP HANA-transactielogboekback-up uitgevoerd. 

U ziet ook dat een SAP HANA-momentopname elk uur wordt uitgevoerd als gevolg van het activeren van een gecombineerde opslagmomentopname over de /hana/data en /hana/shared/SID-volumes. Nadat de HANA-momentopname is geslaagd, wordt de gecombineerde opslagmomentopname uitgevoerd. Zoals geïnstrueerd in crontab, de opslag momentopname op de / hana / logbackup volume loopt elke 5 minuten, ongeveer 2 minuten na de HANA transactie log back-up.

> 

>[!IMPORTANT]
> Het gebruik van storage snapshots voor SAP HANA back-ups is alleen waardevol wanneer de snapshots worden uitgevoerd in combinatie met SAP HANA transactielogboekbacks. Deze back-ups van het transactielogboek moeten betrekking hebben op de perioden tussen de opslagmomentopnamen. 

Als u gebruikers een point-in-time herstel van 30 dagen hebt ingesteld, moet u:

- Toegang tot een gecombineerde opslag momentopname meer dan / hana / gegevens en / hana / shared / SID dat is 30 dagen oud, in extreme gevallen. 
- Hebben aaneengesloten transactielogboekback-ups die de tijd tussen een van de gecombineerde opslagmomentopnamen bestrijken. Dus, de oudste momentopname van de transactie log back-up volume moet 30 dagen oud. Dit is niet het geval als u de back-ups van het transactielogboek kopieert naar een ander NFS-share dat zich op Azure Storage bevindt. In dat geval u oude transactielogboekback-ups uit dat NFS-aandeel halen.

Als u wilt profiteren van opslagmomentopnamen en de uiteindelijke opslagreplicatie van transactielogboekback-ups, wijzigt u de locatie waar NAAR SAP HANA de back-ups van het transactielogboek schrijft. U deze wijziging aanbrengen in HANA Studio. 

Hoewel SAP HANA automatisch een back-up maakt van volledige logboeksegmenten, geeft u een logboekback-upinterval op dat deterministisch moet zijn. Dit geldt vooral wanneer u de optie voor noodherstel gebruikt, omdat u meestal logboekback-ups wilt uitvoeren met een deterministische periode. In het volgende geval wordt 15 minuten ingesteld als het interval voor logboekback-up.

![Plan SAP HANA back-up logs in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

U ook back-ups kiezen die vaker voorkomen dan elke 15 minuten. Een frequentere instelling wordt vaak gebruikt in combinatie met disaster recovery-functionaliteit van HANA Large Instances. Sommige klanten voeren elke 5 minuten back-ups van transactielogboeken uit.

Als er nooit een back-up van de database is gemaakt, is de laatste stap het uitvoeren van een databaseback-up op basis van bestanden om één back-upitem te maken dat in de back-upcatalogus moet bestaan. Anders kan SAP HANA uw opgegeven logboekback-ups niet starten.

![Maak een back-up op basis van bestanden om één back-upitem te maken](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Nadat uw eerste succesvolle opslagmomentopnamen zijn uitgevoerd, verwijdert u de testmomentopname die in stap 6 is uitgevoerd. Zie 'Testmomentopnamen verwijderen - verwijderenTestStorageSnapshot' in [Microsoft-snapshottools voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)meer informatie. 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Het aantal en de grootte van momentopnamen op het schijfvolume controleren

Op een specifiek opslagvolume u het aantal momentopnamen en het opslagverbruik van die momentopnamen controleren. De `ls` opdracht toont de momentopnamemap of -bestanden niet. De opdracht `du` Linux OS toont details over die opslagmomentopnamen omdat ze op dezelfde volumes zijn opgeslagen. Gebruik de opdracht met de volgende opties:

- `du –sh .snapshot`: Deze optie biedt een totaal van alle momentopnamen in de momentopnamemap.
- `du –sh --max-depth=1`: Met deze optie worden alle momentopnamen weergegeven die zijn opgeslagen in de **.snapshot-map** en de grootte van elke momentopname.
- `du –hc`: Deze optie biedt de totale grootte die door alle momentopnamen wordt gebruikt.

Gebruik deze opdrachten om ervoor te zorgen dat de momentopnamen die zijn gemaakt en opgeslagen niet alle opslag op de volumes verbruiken.

>[!NOTE]
>De momentopnamen van de boot LUN zijn niet zichtbaar met de vorige opdrachten.

### <a name="get-details-of-snapshots"></a>Details van momentopnamen opvragen
Gebruik het script `azure_hana_snapshot_details`om meer details over momentopnamen te krijgen. U dit script op beide locaties uitvoeren als er een actieve server is op de locatie voor herstel bij noodgevallen. Het script biedt de volgende uitvoer, uitgesplitst naar elk volume dat momentopnamen bevat: 
   * De grootte van het totale aantal momentopnamen in een volume
   * De volgende details in elke momentopname in dat volume: 
      - Momentopnamenaam 
      - Tijd maken 
      - Grootte van de momentopname
      - Frequentie van de momentopname
      - HANA Backup ID gekoppeld aan die momentopname, indien relevant

Zie 'Momentopnamen van de lijst - azure_hana_snapshot_details' in [Microsoft-momentopnamen voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)syntaxis van de opdracht en uitvoer. 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Het aantal momentopnamen op een server verminderen

Zoals eerder uitgelegd, u het aantal bepaalde labels van momentopnamen dat u opslaat, verminderen. De laatste twee parameters van de opdracht om een momentopname te starten zijn het label en het aantal momentopnamen dat u wilt behouden.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

In het vorige voorbeeld is het momentopnamelabel **dailyhana**. Het aantal momentopnamen met dit label dat moet worden bewaard is **28**. Als u reageert op het verbruik van schijfruimte, wilt u mogelijk het aantal opgeslagen momentopnamen verminderen. Een eenvoudige manier om het aantal momentopnamen te verminderen tot 15, bijvoorbeeld, is het uitvoeren van het script met de laatste parameter ingesteld op **15:**

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Als u het script met deze instelling uitvoert, is het aantal momentopnamen, waaronder de nieuwe opslagmomentopname, 15. De 15 meest recente momentopnamen worden bewaard en de 15 oudere momentopnamen worden verwijderd.

 >[!NOTE]
 > Dit script vermindert het aantal momentopnamen alleen als er momentopnamen zijn van meer dan een uur oud. Het script verwijdert geen momentopnamen die minder dan een uur oud zijn. Deze beperkingen zijn gerelateerd aan de optionele functie voor noodherstel.

Als u niet langer een set momentopnamen wilt onderhouden met het back-upvoorvoegsel **dailyhana** in de syntaxisvoorbeelden, voert u het script uit met **0** als bewaarnummer. Alle momentopnamen die overeenkomen met dat label worden vervolgens verwijderd. Het verwijderen van alle momentopnamen kan van invloed zijn op de mogelijkheden van hana Large Instances disaster recovery-functionaliteit.

Een tweede optie om specifieke momentopnamen `azure_hana_snapshot_delete`te verwijderen is het script te gebruiken. Dit script is ontworpen om een momentopname of set momentopnamen te verwijderen met behulp van de HANA-back-up-id zoals gevonden in HANA Studio of via de momentopnamenaam zelf. Momenteel is de back-up-id alleen gekoppeld aan de momentopnamen die zijn gemaakt voor het **type hana-momentopname.** Snapshot back-ups van het type **logs** en **boot** niet het uitvoeren van een SAP HANA snapshot, dus er is geen back-id te vinden voor die snapshots. Als de momentopnamenaam wordt ingevoerd, wordt gezocht naar alle momentopnamen op de verschillende volumes die overeenkomen met de ingevoerde momentopnamenaam. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Zie 'Een momentopname verwijderen - azure_hana_snapshot_delete' in [Microsoft-snapshottools voor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)meer informatie over het script.

Voer het script uit als **gebruikershoofd**.

>[!IMPORTANT]
>Als er alleen gegevens aanwezig zijn op de momentopname die u wilt verwijderen, gaat die gegevens voor altijd verloren nadat de momentopname is verwijderd.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Herstel op bestandsniveau vanuit een opslagmomentopname

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Voor de momentopnametypen **hana** en **logboeken**hebt u rechtstreeks toegang tot de momentopnamen op de volumes in de **.snapshot-map.** Er is een subdirectory voor elk van de snapshots. Kopieer elk bestand in de status waarin het zich bevond op het punt van de momentopname van die submap naar de werkelijke mapstructuur. 

In de huidige versie van het script is er *geen* herstelscript beschikbaar voor het herstellen van de momentopname als selfservice. Het herstellen van momentopnamen kan worden uitgevoerd als onderdeel van de selfservice-disaster recovery-scripts op de rampherstelsite tijdens failover. Als u een gewenste momentopname wilt herstellen van de bestaande beschikbare momentopnamen, moet u contact opnemen met het Microsoft-operations team door een serviceaanvraag te openen.

>[!NOTE]
>Het herstellen van één bestand werkt niet voor momentopnamen van de boot LUN, onafhankelijk van het type HANA Large Instance-eenheden. De **.snapshot** directory wordt niet weergegeven in de boot LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Herstellen naar de meest recente HANA-momentopname

In een productie-downscenario kan het proces van het herstellen van een opslagmomentopname worden gestart als een klantincident met Microsoft Azure Support. Het is een dringende kwestie als gegevens zijn verwijderd in een productiesysteem en de enige manier om deze op te halen is het herstellen van de productiedatabase.

In een andere situatie kan een point-in-time herstel een lage urgentie en geplande dagen van tevoren zijn. U dit herstel plannen met SAP HANA op Azure in plaats van een vlag met hoge prioriteit te hijsen. U bijvoorbeeld van plan zijn om de SAP-software te upgraden door een nieuw uitbreidingspakket toe te passen. U moet dan terugkeren naar een momentopname die de status vertegenwoordigt voordat het uitbreidingspakket wordt bijgewerkt.

Voordat u het verzoek verzendt, moet u zich voorbereiden. Het SAP HANA-team in Azure kan vervolgens de aanvraag afhandelen en de herstelde volumes leveren. Daarna herstelt u de HANA-database op basis van de momentopnamen.

Zie 'Een momentopname herstellen' in [Handmatigherstelvoor SAP HANA op Azure voor](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)de mogelijkheden om een momentopname te herstellen met de nieuwe gereedschapsset.

Volg deze stappen om u op de aanvraag voor te bereiden.

1. Bepaal welke momentopname u wilt herstellen. Alleen het hana/datavolume wordt hersteld, tenzij u anders instrueert. 

1. Sluit de HANA-instantie af.

   ![Het HANA-exemplaar afsluiten](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. De gegevensvolumes van elk HANA-databaseknooppunt ongedaan maken. Als de gegevensvolumes nog steeds op het besturingssysteem zijn gemonteerd, mislukt het herstel van de momentopname.

   ![De gegevensvolumes van elk HANA-databaseknooppunt demonteren](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Open een Azure-ondersteuningsaanvraag en voeg instructies toe over het herstellen van een specifieke momentopname:

   - Tijdens de restauratie: SAP HANA on Azure Service kan u vragen om een telefonische vergadering bij te wonen om te coördineren, te verifiëren en te bevestigen dat de juiste opslagmomentopname is hersteld. 

   - Na de restauratie: SAP HANA on Azure Service waarschuwt u wanneer de opslagmomentopname is hersteld.

1. Nadat het restauratieproces is voltooid, montert u alle gegevensvolumes opnieuw.

   ![Alle gegevensvolumes opnieuw monteren](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Een andere mogelijkheid voor het verkrijgen van, bijvoorbeeld, SAP HANA-gegevensbestanden hersteld van een opslag momentopname, is gedocumenteerd in stap 7 in [Handmatige herstelhandleiding voor SAP HANA op Azure van een opslag momentopname](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Zie [Handmatige herstelhandleiding voor SAP HANA op Azure vanuit een momentopname](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor het herstellen van een momentopname. 

>[!Note]
>Als uw momentopname is hersteld door Microsoft-bewerkingen, hoeft u stap 7 niet te doen.


### <a name="recover-to-another-point-in-time"></a>Herstellen naar een ander punt in de tijd
Zie 'De database herstellen naar het volgende moment' in [Handmatige herstelhandleiding voor SAP HANA op Azure vanuit een momentopname van de opslag.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md) 


## <a name="next-steps"></a>Volgende stappen
- Zie [Principes voor noodherstel en voorbereiding](hana-concept-preparation.md).
