---
title: Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen2 | Microsoft Documenten
description: Meer informatie over de aanbevolen procedures voor het innemen van gegevens, datumbeveiliging en prestaties met betrekking tot het gebruik van Azure Data Lake Storage Gen2 (voorheen bekend als Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299676"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen2

In dit artikel vindt u meer informatie over aanbevolen procedures en overwegingen voor het werken met Azure Data Lake Storage Gen2. Dit artikel bevat informatie over beveiliging, prestaties, tolerantie en bewaking voor Data Lake Storage Gen2. Vóór Data Lake Storage Gen2 was het werken met echt big data in services zoals Azure HDInsight complex. U moest gegevens over meerdere Blob-opslagaccounts sharden, zodat petabyteopslag en optimale prestaties op die schaal konden worden bereikt. Data Lake Storage Gen2 ondersteunt afzonderlijke bestandsformaten tot 5 TB en de meeste harde limieten voor prestaties zijn verwijderd. Er zijn echter nog enkele overwegingen die dit artikel behandelt, zodat u de beste prestaties krijgen met Data Lake Storage Gen2.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Azure Data Lake Storage Gen2 biedt POSIX-toegangsbesturingselementen voor Azure Active Directory-gebruikers, -groepen en serviceprincipals (Azure Active Directory). Deze toegangsbesturingselementen kunnen worden ingesteld op bestaande bestanden en mappen. De toegangsbesturingselementen kunnen ook worden gebruikt om standaardmachtigingen te maken die automatisch kunnen worden toegepast op nieuwe bestanden of mappen. Meer informatie over Data Lake Storage Gen2 ACL's zijn beschikbaar bij [Access control in Azure Data Lake Storage Gen2.](storage-data-lake-storage-access-control.md)

### <a name="use-security-groups-versus-individual-users"></a>Beveiligingsgroepen gebruiken versus individuele gebruikers

Wanneer u met big data werkt in Data Lake Storage Gen2, is het waarschijnlijk dat een serviceprincipal wordt gebruikt om services zoals Azure HDInsight toe te staan met de gegevens te werken. Er kunnen echter ook gevallen zijn waarin individuele gebruikers toegang tot de gegevens nodig hebben. In alle gevallen u sterk overwegen azure Active [Directory-beveiligingsgroepen](../common/storage-auth-aad.md) te gebruiken in plaats van individuele gebruikers toe te wijsen aan mappen en bestanden.

Zodra een beveiligingsgroep machtigingen heeft toegewezen, vereist het toevoegen of verwijderen van gebruikers uit de groep geen updates voor Data Lake Storage Gen2. Dit helpt er ook voor te zorgen dat u het maximum aantal toegangscontrole-items per toegangscontrolelijst (ACL) niet overschrijdt. Momenteel is dat aantal 32 (inclusief de vier POSIX-stijl ACL's die altijd zijn gekoppeld aan elk bestand en directory): de eigenaar gebruiker, de eigenaar groep, het masker, en andere. Elke map kan twee soorten ACL hebben, de toegangsACL en de standaard ACL, voor een totaal van 64 toegangscontrole-items. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over deze ACL's.

### <a name="security-for-groups"></a>Beveiliging voor groepen

Wanneer u of uw gebruikers toegang nodig hebben tot gegevens in een opslagaccount met hiërarchische naamruimte ingeschakeld, u het beste Azure Active Directory-beveiligingsgroepen gebruiken. Sommige aanbevolen groepen om mee te beginnen zijn **readonlyusers,** **WriteAccessUsers**en **FullAccessUsers** voor de hoofdtekst van de container, en zelfs afzonderlijke groepen voor belangrijke submappen. Als er andere verwachte groepen gebruikers zijn die later kunnen worden toegevoegd, maar nog niet zijn geïdentificeerd, u overwegen om dummy-beveiligingsgroepen te maken die toegang hebben tot bepaalde mappen. Het gebruik van de beveiligingsgroep zorgt ervoor dat u lange verwerkingstijd vermijden bij het toewijzen van nieuwe machtigingen aan duizenden bestanden.

### <a name="security-for-service-principals"></a>Beveiliging voor serviceprincipals

Azure Active Directory-serviceprincipals worden doorgaans gebruikt door services zoals Azure Databricks om toegang te krijgen tot gegevens in Data Lake Storage Gen2. Voor veel klanten kan één Azure Active Directory-serviceprincipal voldoende zijn en kan het volledige machtigingen hebben bij de hoofdmap van de Data Lake Storage Gen2-container. Andere klanten hebben mogelijk meerdere clusters nodig met verschillende serviceprincipals waarbij het ene cluster volledige toegang heeft tot de gegevens en een ander cluster met alleen leestoegang. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>De Data Lake Storage Gen2-firewall inschakelen met Azure-servicetoegang

Data Lake Storage Gen2 ondersteunt de optie om een firewall in te schakelen en de toegang alleen tot Azure-services te beperken, wat wordt aanbevolen om de vector van externe aanvallen te beperken. Firewall kan worden ingeschakeld op een opslagaccount in de Azure-portal via de **Firewall** > **Enable Firewall (ON)** > Toegang verlenen tot**Azure-services.**

Als u toegang wilt krijgen tot uw opslagaccount vanuit Azure Databricks, implementeert u Azure Databricks in uw virtuele netwerk en voegt u dat virtuele netwerk toe aan uw firewall. Zie [Azure Storage-firewalls en virtuele netwerken configureren.](https://docs.microsoft.com/azure/storage/common/storage-network-security)

## <a name="resiliency-considerations"></a>Overwegingen voor tolerantie

Bij het ontwerpen van een systeem met Data Lake Storage Gen2 of een cloudservice moet u rekening houden met uw beschikbaarheidsvereisten en hoe u reageren op mogelijke onderbrekingen in de service. Een probleem kan worden gelokaliseerd op de specifieke instantie of zelfs regio-breed, dus het hebben van een plan voor beide is belangrijk. Afhankelijk van de doelstelling hersteltijd en de doelstelling SLA's voor het herstelpunt voor uw werkbelasting, u kiezen voor een min of meer agressieve strategie voor hoge beschikbaarheid en herstel na noodgevallen.

### <a name="high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen

Hoge beschikbaarheid (HA) en disaster recovery (DR) kunnen soms samen worden gecombineerd, hoewel elk van deze opties een iets andere strategie heeft, vooral als het gaat om gegevens. Data Lake Storage Gen2 verwerkt al 3x replicatie onder de motorkap om te beschermen tegen gelokaliseerde hardwarefouten. Daarnaast verbeteren andere replicatieopties, zoals ZRS of GZRS (preview), HA, terwijl GRS-& RA-GRS DR verbeteren. Bij het bouwen van een plan voor HA heeft de werkbelasting bij het bouwen van een plan voor HA zo snel mogelijk toegang nodig tot de meest recente gegevens door over te schakelen naar een afzonderlijk gerepliceerdexemplaar lokaal of in een nieuwe regio.

In een DR-strategie is het ook belangrijk om gegevens te laten repliceren naar een andere regio met GRS- of RA-GRS-replicatie om zich voor te bereiden op het onwaarschijnlijke geval van een catastrofale mislukking van een regio. U moet ook rekening houden met uw vereisten voor randgevallen, zoals gegevensbeschadiging, waarbij u mogelijk periodieke momentopnamen wilt maken om op terug te vallen. Afhankelijk van het belang en de grootte van de gegevens, u overwegen deltamomentopnamen van 1-, 6- en 24-uursperioden te rollen, afhankelijk van risicotoleranties.

Voor gegevenstolerantie met Data Lake Storage Gen2 is het raadzaam om uw gegevens te georepliceren via GRS of RA-GRS die voldoet aan uw HA/DR-vereisten. Daarnaast moet u overwegen manieren voor de toepassing met behulp van Data Lake Storage Gen2 om automatisch niet over naar de secundaire regio door het toezicht triggers of de lengte van mislukte pogingen, of op zijn minst een melding te sturen naar beheerders voor handmatige interventie. Houd in gedachten dat er afweging van falen over versus wachten op een dienst om terug te komen online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp gebruiken voor gegevensverplaatsing tussen twee locaties

Kort voor gedistribueerde kopie, DistCp is een Linux command-line tool die wordt geleverd met Hadoop en biedt gedistribueerde gegevensverkeer tussen twee locaties. De twee locaties kunnen Data Lake Storage Gen2, HDFS of S3 zijn. Deze tool maakt gebruik van MapReduce taken op een Hadoop cluster (bijvoorbeeld HDInsight) om uit te schalen op alle knooppunten. Distcp wordt beschouwd als de snelste manier om big data te verplaatsen zonder speciale netwerkcompressie-apparaten. Distcp biedt ook een optie om delta's alleen tussen twee locaties bij te werken, automatische pogingen te verwerken en dynamische schaling van compute. Deze aanpak is ongelooflijk efficiënt als het gaat om het repliceren van dingen zoals Hive / Spark tabellen die veel grote bestanden in een enkele directory kunnen hebben en u alleen wilt kopiëren over de gewijzigde gegevens. Om deze redenen is Distcp het meest aanbevolen hulpmiddel voor het kopiëren van gegevens tussen big data-winkels.

Copy jobs kunnen worden geactiveerd door Apache Oozie workflows met behulp van frequentie of data triggers, evenals Linux cron jobs. Voor intensieve replicatietaken wordt aanbevolen om een afzonderlijk HDInsight Hadoop-cluster op te draaien dat specifiek kan worden afgestemd en geschaald voor de kopieertaken. Dit zorgt ervoor dat kopieertaken kritieke taken niet verstoren. Als replicatie op een frequentie met breed genoeg wordt uitgevoerd, kan het cluster zelfs tussen elke taak worden verwijderd. Als het niet overnaar de secundaire regio gaat, moet u ervoor zorgen dat een ander cluster ook in de secundaire regio wordt gesponnen om nieuwe gegevens terug te repliceren naar het primaire Data Lake Storage Gen2-account zodra het weer wordt afgespeeld. Zie [Distcp gebruiken om gegevens te kopiëren tussen Azure Storage Blobs en Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md)voor voorbeelden van het gebruik van Distcp.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory gebruiken om kopieertaken te plannen

Azure Data Factory kan ook worden gebruikt om kopieertaken te plannen met een kopieeractiviteit en kan zelfs op een frequentie worden ingesteld via de wizard Kopiëren. Houd er rekening mee dat Azure Data Factory een limiet heeft van dm's voor cloudgegevensen en uiteindelijk de doorvoer/compute voor grote gegevensworkloads beperkt. Bovendien biedt Azure Data Factory momenteel geen delta-updates tussen Data Lake Storage Gen2-accounts, dus mappen zoals Hive-tabellen vereisen een volledige kopie om te repliceren. Raadpleeg het artikel van de [gegevensfabriek](../../data-factory/load-azure-data-lake-storage-gen2.md) voor meer informatie over kopiëren met Data Factory.

## <a name="monitoring-considerations"></a>Monitoringoverwegingen

Data Lake Storage Gen2 biedt statistieken in de Azure-portal onder het Data Lake Storage Gen2-account en in Azure Monitor. Beschikbaarheid van Data Lake Storage Gen2 wordt weergegeven in de Azure-portal. Om de meest up-to-date beschikbaarheid van een Data Lake Storage Gen2-account te krijgen, moet u uw eigen synthetische tests uitvoeren om de beschikbaarheid te valideren. Andere statistieken zoals het totale opslaggebruik, lees-/schrijfverzoeken en ingress/uitgangen zijn beschikbaar om te worden gebruikt door toepassingen te controleren en kunnen ook waarschuwingen activeren wanneer drempelwaarden (bijvoorbeeld gemiddelde latentie of foutpercentage per minuut) worden overschreden.

## <a name="directory-layout-considerations"></a>Overwegingen voor directory-indeling

Bij het landen van gegevens in een gegevensmeer is het belangrijk om de structuur van de gegevens vooraf te plannen, zodat beveiliging, partitionering en verwerking effectief kunnen worden gebruikt. Veel van de volgende aanbevelingen zijn van toepassing op alle big data workloads. Elke werkbelasting heeft andere vereisten voor hoe de gegevens worden verbruikt, maar hieronder staan enkele algemene lay-outs waarmee u rekening moet houden bij het werken met IoT- en batchscenario's.

### <a name="iot-structure"></a>IoT-structuur

In IoT-workloads kunnen er veel gegevens worden aangevoerd in het gegevensarchief dat zich uitstrekt over tal van producten, apparaten, organisaties en klanten. Het is belangrijk om de directory-indeling vooraf te plannen voor organisatie, beveiliging en efficiënte verwerking van de gegevens voor downstream-consumenten. Een algemene sjabloon om te overwegen kan de volgende lay-out zijn:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Als u bijvoorbeeld telemetrie voor een vliegtuigmotor binnen het Verenigd Koninkrijk landt, ziet het er mogelijk uit als de volgende structuur:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Er is een belangrijke reden om de datum aan het einde van de directory structuur. Als u bepaalde regio's wilt vergrendelen of zaken wilt onderwerpen aan gebruikers/groepen, u dit eenvoudig doen met de POSIX-machtigingen. Anders, als er een noodzaak om een bepaalde beveiligingsgroep te beperken tot het bekijken van alleen de Britse gegevens of bepaalde vliegtuigen, met de datum structuur vooraan een aparte toestemming nodig zou zijn voor tal van mappen onder elk uur directory. Bovendien zou het hebben van de datumstructuur vooraan het aantal mappen exponentieel verhogen naarmate de tijd verstreek.

### <a name="batch-jobs-structure"></a>Structuur batchtaken

Van een hoog niveau, een veelgebruikte aanpak in batch verwerking is om gegevens te landen in een "in" directory. Vervolgens, zodra de gegevens zijn verwerkt, zet de nieuwe gegevens in een "out" directory voor downstream processen te consumeren. Deze directorystructuur wordt soms gezien voor taken waarvoor verwerking op afzonderlijke bestanden vereist is en die mogelijk geen massaal parallelle verwerking nodig heeft ten opzichte van grote gegevenssets. Net als de hierboven aanbevolen IoT-structuur heeft een goede directorystructuur de mappen op bovenliggend niveau voor zaken als regio- en onderwerpzaken (bijvoorbeeld organisatie, product/producent). Deze structuur helpt bij het beveiligen van de gegevens in uw organisatie en een beter beheer van de gegevens in uw workloads. Houd bovendien rekening met datum en tijd in de structuur om een betere organisatie, gefilterde zoekopdrachten, beveiliging en automatisering in de verwerking mogelijk te maken. Het niveau van granulariteit voor de datumstructuur wordt bepaald door het interval waarop de gegevens worden geüpload of verwerkt, zoals per uur, per dag of zelfs per maand.

Soms is bestandsverwerking mislukt als gevolg van gegevensbeschadiging of onverwachte indelingen. In dergelijke gevallen kan de directorystructuur profiteren van een **/slechte** map om de bestanden te verplaatsen voor verdere inspectie. De batchtaak kan ook de rapportage of melding van deze *slechte* bestanden verwerken voor handmatige interventie. Denk aan de volgende sjabloonstructuur:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Een marketingbedrijf ontvangt bijvoorbeeld dagelijkse gegevensfragmenten van klantupdates van hun klanten in Noord-Amerika. Het lijkt misschien op het volgende fragment voor en na verwerking:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

In het algemene geval dat batchgegevens rechtstreeks worden verwerkt in databases zoals Hive of traditionele SQL-databases, is er geen behoefte aan een **/in** **of /out-map,** omdat de uitvoer al in een aparte map voor de Hive-tabel of externe database gaat. Dagelijkse uittreksels van klanten zouden bijvoorbeeld in hun respectievelijke mappen terecht komen en orkestratie door iets als Azure Data Factory, Apache Oozie of Apache Airflow zou een dagelijkse Hive- of Spark-taak activeren om de gegevens in een Hive-tabel te verwerken en te schrijven.
