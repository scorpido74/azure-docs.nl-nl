---
title: Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Meer informatie over de aanbevolen procedures voor het innemen van gegevens, datumbeveiliging en prestaties met betrekking tot het gebruik van Azure Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: a8ca67d1ff3100aee02ed473c9cc2180de3973b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638932"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

In dit artikel vindt u meer informatie over aanbevolen procedures en overwegingen voor het werken met Azure Data Lake Storage Gen1. Dit artikel bevat informatie over beveiliging, prestaties, tolerantie en bewaking voor Data Lake Storage Gen1. Vóór Data Lake Storage Gen1 was het werken met echt big data in services zoals Azure HDInsight complex. U moest gegevens over meerdere Blob-opslagaccounts sharden, zodat petabyteopslag en optimale prestaties op die schaal konden worden bereikt. Met Data Lake Storage Gen1 worden de meeste harde limieten voor grootte en prestaties verwijderd. Er zijn echter nog enkele overwegingen die dit artikel behandelt, zodat u de beste prestaties krijgen met Data Lake Storage Gen1.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Azure Data Lake Storage Gen1 biedt POSIX-toegangscontroles en gedetailleerde controle voor Azure Active Directory-gebruikers, -groepen en serviceprincipals van Azure Active Directory (Azure AD). Deze toegangsbesturingselementen kunnen worden ingesteld op bestaande bestanden en mappen. De toegangsbesturingselementen kunnen ook worden gebruikt om standaardinstellingen te maken die kunnen worden toegepast op nieuwe bestanden of mappen. Wanneer machtigingen zijn ingesteld op bestaande mappen en onderliggende objecten, moeten de machtigingen opnieuw worden gepropageerd voor elk object. Als er een groot aantal bestanden is, kan het verspreiden van de machtigingen lang duren. De tijd die nodig is kan variëren tussen 30-50 objecten verwerkt per seconde. Plan daarom de mapstructuur en gebruikersgroepen op de juiste manier. Anders kan dit leiden tot onverwachte vertragingen en problemen wanneer u met uw gegevens werkt.

Stel dat u een map hebt met 100.000 onderliggende objecten. Als u de ondergrens van 30 objecten per seconde neemt, kan het een uur duren voordat de machtiging voor de hele map wordt bijgewerkt. Meer informatie over Data Lake Storage Gen1-ACL's zijn beschikbaar bij [Access-beheer in Azure Data Lake Storage Gen1.](data-lake-store-access-control.md) Voor betere prestaties bij het opnieuw toewijzen van ACL's u het Azure Data Lake Command-Line Tool gebruiken. De tool maakt meerdere threads en recursieve navigatielogica om snel ACL's toe te passen op miljoenen bestanden. De tool is beschikbaar voor Linux en Windows, en de [documentatie](https://github.com/Azure/data-lake-adlstool) en [downloads](https://aka.ms/adlstool-download) voor deze tool is te vinden op GitHub. Dezelfde prestatieverbeteringen kunnen worden ingeschakeld door uw eigen tools geschreven met de Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) en [Java](data-lake-store-get-started-java-sdk.md) SDKs.

### <a name="use-security-groups-versus-individual-users"></a>Beveiligingsgroepen gebruiken versus individuele gebruikers

Wanneer u met big data werkt in Data Lake Storage Gen1, wordt hoogstwaarschijnlijk een serviceprincipal gebruikt om services zoals Azure HDInsight in staat te stellen met de gegevens te werken. Er kunnen echter ook gevallen zijn waarin individuele gebruikers toegang tot de gegevens nodig hebben. In dergelijke gevallen moet u Azure Active [Directory-beveiligingsgroepen](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) gebruiken in plaats van individuele gebruikers toe te wijzen aan mappen en bestanden.

Zodra een beveiligingsgroep machtigingen heeft toegewezen, vereist het toevoegen of verwijderen van gebruikers uit de groep geen updates voor Data Lake Storage Gen1. Dit helpt ook ervoor te zorgen dat u de limiet van [32 Access- en StandaardALS](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) niet overschrijdt (dit omvat de vier POSIX-stijl ACL's die altijd zijn gekoppeld aan elk bestand en elke map: de eigenaar van de [gebruiker,](data-lake-store-access-control.md#the-owning-user) [de eigenaargroep,](data-lake-store-access-control.md#the-owning-group) [het masker](data-lake-store-access-control.md#the-mask)en andere).

### <a name="security-for-groups"></a>Beveiliging voor groepen

Zoals besproken, wanneer gebruikers toegang nodig hebben tot Data Lake Storage Gen1, is het het beste om Azure Active Directory-beveiligingsgroepen te gebruiken. Sommige aanbevolen groepen om mee te beginnen zijn **readonlyusers,** **WriteAccessUsers**en **FullAccessUsers** voor de hoofdtekst van het account en zelfs afzonderlijke groepen voor belangrijke submappen. Als er andere verwachte groepen gebruikers zijn die later kunnen worden toegevoegd, maar nog niet zijn geïdentificeerd, u overwegen om dummy-beveiligingsgroepen te maken die toegang hebben tot bepaalde mappen. Het gebruik van de beveiligingsgroep zorgt ervoor dat u later geen lange verwerkingstijd nodig hebt om nieuwe machtigingen toe te voegen aan duizenden bestanden.

### <a name="security-for-service-principals"></a>Beveiliging voor serviceprincipals

Azure Active Directory-serviceprincipals worden doorgaans gebruikt door services zoals Azure HDInsight om toegang te krijgen tot gegevens in Data Lake Storage Gen1. Afhankelijk van de toegangsvereisten voor meerdere workloads, kunnen er enkele overwegingen zijn om de beveiliging binnen en buiten de organisatie te garanderen. Voor veel klanten kan één Azure Active Directory-serviceprincipal voldoende zijn en kan het volledige machtigingen hebben bij de hoofdmap van het Data Lake Storage Gen1-account. Andere klanten hebben mogelijk meerdere clusters nodig met verschillende serviceprincipals waarbij het ene cluster volledige toegang heeft tot de gegevens en een ander cluster met alleen leestoegang. Net als bij de beveiligingsgroepen u overwegen een serviceprincipal te maken voor elk verwacht scenario (lezen, schrijven, vol) zodra een Data Lake Storage Gen1-account is gemaakt.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>De Data Lake Storage Gen1-firewall inschakelen met Azure-servicetoegang

Data Lake Storage Gen1 ondersteunt de optie om een firewall in te schakelen en de toegang alleen tot Azure-services te beperken, wat wordt aanbevolen voor een kleinere aanvalsvector van externe indringers. Firewall kan worden ingeschakeld op het Data Lake Storage Gen1-account in de Azure-portal via de **Firewall** > **Enable Firewall (ON)** > Toegang tot**Azure-services** toestaan.

![Firewall-instellingen in Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Firewall-instellingen in Data Lake Storage Gen1")

Zodra firewall is ingeschakeld, hebben alleen Azure-services zoals HDInsight, Data Factory, SQL Data Warehouse, enz. Vanwege de interne netwerkadresvertaling die door Azure wordt gebruikt, ondersteunt de Data Lake Storage Gen1-firewall geen beperking van specifieke services per IP en is deze alleen bedoeld voor beperkingen van eindpunten buiten Azure, zoals on-premises.

## <a name="performance-and-scale-considerations"></a>Prestatie- en schaaloverwegingen

Een van de krachtigste functies van Data Lake Storage Gen1 is dat het de harde limieten voor gegevensdoorvoer verwijdert. Het verwijderen van de limieten stelt klanten in staat om hun gegevensgrootte en bijbehorende prestatievereisten te vergroten zonder de gegevens te hoeven verharden. Een van de belangrijkste overwegingen voor het optimaliseren van data lake storage Gen1 prestaties is dat het de beste presteert wanneer gegeven parallellisme.

### <a name="improve-throughput-with-parallelism"></a>Doorvoer verbeteren met parallellisme

Overweeg om 8-12 threads per core te geven voor de meest optimale lees-/schrijfdoorvoer. Dit is te wijten aan het blokkeren van leest / schrijft op een enkele thread, en meer threads kan hogere gelijktijdigheid op de VM mogelijk te maken. Om ervoor te zorgen dat de niveaus gezond zijn en parallellisme kan worden verhoogd, moet u het CPU-gebruik van de VM controleren.

### <a name="avoid-small-file-sizes"></a>Kleine bestandsgroottes vermijden

POSIX-machtigingen en auditing in Data Lake Storage Gen1 wordt geleverd met een overhead die duidelijk wordt bij het werken met tal van kleine bestanden. Als een best practice, moet u batch uw gegevens in grotere bestanden in plaats van het schrijven van duizenden of miljoenen kleine bestanden naar Data Lake Storage Gen1. Het vermijden van kleine bestandsgroottes kan meerdere voordelen hebben, zoals:

* De verificatiecontroles in meerdere bestanden verlagen
* Minder geopende bestandsverbindingen
* Sneller kopiëren/replicatie
* Minder bestanden om te verwerken bij het bijwerken van Data Lake Storage Gen1 POSIX-machtigingen

Afhankelijk van welke services en workloads de gegevens gebruiken, is een goede grootte om rekening te houden voor bestanden 256 MB of meer. Als de bestandsgrootte niet kan worden gebatched wanneer u in Data Lake Storage Gen1 landt, u een afzonderlijke verdichtingstaak hebben die deze bestanden combineert tot grotere bestanden. Zie [Uw gegevensset structureren](data-lake-store-performance-tuning-guidance.md#structure-your-data-set)voor meer informatie en aanbevelingover bestandsgroottes en het ordenen van de gegevens in Data Lake Storage Gen1.

### <a name="large-file-sizes-and-potential-performance-impact"></a>Grote bestandsgrootte en potentiële impact op de prestaties

Hoewel Data Lake Storage Gen1 grote bestanden tot petabytes in grootte ondersteunt, is het voor optimale prestaties en afhankelijk van het proces het lezen van de gegevens misschien niet ideaal om gemiddeld boven de 2 GB te gaan. Wanneer bestanden bijvoorbeeld **Distcp** gebruiken om gegevens tussen locaties of verschillende opslagaccounts te kopiëren, zijn bestanden het fijnste niveau van granulariteit dat wordt gebruikt om kaarttaken te bepalen. Dus, als u het kopiëren van 10 bestanden die 1 TB per stuk, ten hoogste 10 mappers worden toegewezen. Ook als u veel bestanden met mappers toegewezen, in eerste instantie de mappers werken parallel aan grote bestanden te verplaatsen. Echter, als de taak begint te wind down slechts een paar mappers blijven toegewezen en u worden geplakt met een enkele mapper toegewezen aan een groot bestand. Microsoft heeft verbeteringen ingediend bij Distcp om dit probleem in toekomstige Hadoop-versies aan te pakken.

Een ander voorbeeld om rekening mee te houden is bij het gebruik van Azure Data Lake Analytics met Data Lake Storage Gen1. Afhankelijk van de verwerking die door de afzuiger wordt uitgevoerd, kunnen sommige bestanden die niet kunnen worden gesplitst (bijvoorbeeld XML, JSON) last hebben van prestaties wanneer deze groter zijn dan 2 GB. In gevallen waarin bestanden kunnen worden gesplitst door een afzuiger (bijvoorbeeld CSV), hebben grote bestanden de voorkeur.

### <a name="capacity-plan-for-your-workload"></a>Capaciteitsplan voor uw werklast

Azure Data Lake Storage Gen1 verwijdert de harde IO-beperkingslimieten die op Blob-opslagaccounts worden geplaatst. Er zijn echter nog steeds zachte grenzen die moeten worden overwogen. De standaard limieten voor ingress/egress throttling voldoen aan de behoeften van de meeste scenario's. Als de limieten moeten worden verhoogd, werkt u met Microsoft-ondersteuning. Kijk ook naar de limieten tijdens de proof-of-concept fase, zodat IO throttling limieten niet worden geraakt tijdens de productie. Als dat gebeurt, moet mogelijk worden gewacht op een handmatige verhoging van het Microsoft-engineeringteam. Als IO-beperking optreedt, retourneert Azure Data Lake Storage Gen1 een foutcode van 429 en moet het idealiter opnieuw worden geprobeerd met een geschikt exponentieel back-offbeleid.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optimaliseren "schrijft" met de Data Lake Storage Gen1-stuurprogrammabuffer

Voer schrijfbewerkingen uit die zo dicht mogelijk bij de data lake storage Gen1 zijn. Probeer de buffergrootte niet te overschrijden voordat u doorspoelt, bijvoorbeeld bij het streamen met Apache Storm- of Spark-streamingworkloads. Bij het schrijven naar Data Lake Storage Gen1 van HDInsight/Hadoop is het belangrijk om te weten dat Data Lake Storage Gen1 een driver heeft met een buffer van 4 MB. Net als veel stuurprogramma's van het bestandssysteem kan deze buffer handmatig worden gespoeld voordat u de grootte van 4 MB bereikt. Zo niet, dan wordt het onmiddellijk doorgespoeld naar opslag als de volgende schrijf de maximale grootte van de buffer overschrijdt. Waar mogelijk moet u een overschrijding of een significante onderloop van de buffer vermijden bij het synchroniseren/doorspoelen van beleid per telling of tijdvenster.

## <a name="resiliency-considerations"></a>Overwegingen voor tolerantie

Bij het ontwerpen van een systeem met Data Lake Storage Gen1 of een cloudservice moet u rekening houden met uw beschikbaarheidsvereisten en hoe u reageren op mogelijke onderbrekingen in de service. Een probleem kan worden gelokaliseerd op de specifieke instantie of zelfs regio-breed, dus het hebben van een plan voor beide is belangrijk. Afhankelijk van de **doelstelling hersteltijd** en de doelstelling SLA's voor het **herstelpunt** voor uw werkbelasting, u kiezen voor een min of meer agressieve strategie voor hoge beschikbaarheid en herstel na noodgevallen.

### <a name="high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen

Hoge beschikbaarheid (HA) en disaster recovery (DR) kunnen soms samen worden gecombineerd, hoewel elk van deze opties een iets andere strategie heeft, vooral als het gaat om gegevens. Data Lake Storage Gen1 verwerkt al 3x replicatie onder de motorkap om te beschermen tegen gelokaliseerde hardwarefouten. Aangezien replicatie in verschillende regio's echter niet is ingebouwd, moet u dit zelf beheren. Bij het bouwen van een plan voor HA heeft de werkbelasting bij het bouwen van een plan voor HA zo snel mogelijk toegang nodig tot de meest recente gegevens door over te schakelen naar een afzonderlijk gerepliceerdexemplaar lokaal of in een nieuwe regio.

In een DR-strategie, ter voorbereiding op het onwaarschijnlijke geval van een catastrofale mislukking van een regio, is het ook belangrijk om gegevens te laten repliceren naar een andere regio. Deze gegevens kunnen in eerste instantie hetzelfde zijn als de gerepliceerde HA-gegevens. U moet echter ook rekening houden met uw vereisten voor randgevallen, zoals gegevensbeschadiging, waarbij u mogelijk periodieke momentopnamen wilt maken om op terug te vallen. Afhankelijk van het belang en de grootte van de gegevens, u deltamomentopnamen van 1-, 6- en 24-uursperioden op de lokale en/of secundaire winkel gebruiken, afhankelijk van risicotoleranties.

Voor gegevenstolerantie met Data Lake Storage Gen1 is het raadzaam om uw gegevens te geo-repliceren naar een aparte regio met een frequentie die voldoet aan uw HA/DR-vereisten, idealiter elk uur. Deze replicatiefrequentie minimaliseert enorme gegevensbewegingen die concurrerende doorvoerbehoeften met het hoofdsysteem en een betere doelstelling voor herstelpunten (RPO) kunnen hebben. Daarnaast moet u overwegen manieren voor de toepassing met behulp van Data Lake Storage Gen1 om automatisch niet over naar de secundaire account door middel van monitoring triggers of lengte van mislukte pogingen, of op zijn minst een melding te sturen naar beheerders voor handmatige interventie. Houd in gedachten dat er afweging van falen over versus wachten op een dienst om terug te komen online. Als de gegevens nog niet zijn gerepliceerd, kan een failover mogelijk gegevensverlies, inconsistentie of complexe samenvoeging van de gegevens veroorzaken.

Hieronder vindt u de drie belangrijkste aanbevolen opties voor het orkestreren van replicatie tussen Data Lake Storage Gen1-accounts en belangrijke verschillen tussen elk van deze accounts.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Schaallimieten**     | Begrensd door werkknooppunten        | Beperkt door Max Cloud Data Movement-eenheden        | Gebonden aan Analytics-eenheden        |
|**Ondersteunt het kopiëren van delta's**     |   Ja      | Nee         | Nee         |
|**Ingebouwde orkestratie**     |  Nee (gebruik Oozie Airflow of cron jobs)       | Ja        | Nee (Azure Automation of Windows Task Scheduler gebruiken)         |
|**Ondersteunde bestandssystemen**     | ADL, HDFS, WASB, S3, GS, CFS        |Talrijk, zie [Connectors](../data-factory/connector-azure-blob-storage.md).         | ADL naar ADL, WASB naar ADL (alleen in dezelfde regio)        |
|**Ondersteuning voor besturingssysteem**     |Elk BE met Hadoop         | N.v.t.          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp gebruiken voor gegevensverplaatsing tussen twee locaties

Kort voor gedistribueerde kopie, Distcp is een Linux command-line tool die wordt geleverd met Hadoop en biedt gedistribueerde gegevensverkeer tussen twee locaties. De twee locaties kunnen Data Lake Storage Gen1, HDFS, WASB of S3 zijn. Deze tool maakt gebruik van MapReduce taken op een Hadoop cluster (bijvoorbeeld HDInsight) om uit te schalen op alle knooppunten. Distcp wordt beschouwd als de snelste manier om big data te verplaatsen zonder speciale netwerkcompressie-apparaten. Distcp biedt ook een optie om delta's alleen tussen twee locaties bij te werken, automatische pogingen te verwerken en dynamische schaling van compute. Deze aanpak is ongelooflijk efficiënt als het gaat om het repliceren van dingen zoals Hive / Spark tabellen die veel grote bestanden in een enkele directory kunnen hebben en u alleen wilt kopiëren over de gewijzigde gegevens. Om deze redenen is Distcp het meest aanbevolen hulpmiddel voor het kopiëren van gegevens tussen big data-winkels.

Copy jobs kunnen worden geactiveerd door Apache Oozie workflows met behulp van frequentie of data triggers, evenals Linux cron jobs. Voor intensieve replicatietaken wordt aanbevolen om een afzonderlijk HDInsight Hadoop-cluster op te draaien dat specifiek kan worden afgestemd en geschaald voor de kopieertaken. Dit zorgt ervoor dat kopieertaken kritieke taken niet verstoren. Als replicatie op een frequentie met breed genoeg wordt uitgevoerd, kan het cluster zelfs tussen elke taak worden verwijderd. Als het niet overgaat naar de secundaire regio, moet u ervoor zorgen dat een ander cluster ook in de secundaire regio wordt gesponnen om nieuwe gegevens terug te repliceren naar het primaire Data Lake Storage Gen1-account zodra het weer wordt afgespeeld. Zie [Distcp gebruiken om gegevens te kopiëren tussen Azure Storage Blobs en Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)voor voorbeelden van het gebruik van Distcp.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory gebruiken om kopieertaken te plannen

Azure Data Factory kan ook worden gebruikt om kopieertaken te plannen met behulp van een **kopieeractiviteit**en kan zelfs op een frequentie worden ingesteld via de **wizard Kopiëren.** Houd er rekening mee dat Azure Data Factory een limiet heeft van dm's voor cloudgegevensen en uiteindelijk de doorvoer/compute voor grote gegevensworkloads beperkt. Bovendien biedt Azure Data Factory momenteel geen delta-updates tussen Data Lake Storage Gen1-accounts, dus mappen zoals Hive-tabellen vereisen een volledige kopie om te repliceren. Raadpleeg de [tuninggids Voor het kopiëren van activiteitstuning](../data-factory/copy-activity-performance.md) voor meer informatie over kopiëren met Data Factory.

### <a name="adlcopy"></a>AdlCopy

AdlCopy is een Windows-opdrachtregeltool waarmee u gegevens tussen twee Data Lake Storage Gen1-accounts alleen binnen dezelfde regio kopiëren. Het AdlCopy-hulpprogramma biedt een zelfstandige optie of de optie om een Azure Data Lake Analytics-account te gebruiken om uw kopieertaak uit te voeren. Hoewel het oorspronkelijk werd gebouwd voor on-demand kopieën in tegenstelling tot een robuuste replicatie, biedt het een andere optie om gedistribueerd kopiëren te doen over Data Lake Storage Gen1-accounts binnen dezelfde regio. Voor betrouwbaarheid is het raadzaam om de premium Data Lake Analytics-optie te gebruiken voor elke productieworkload. De zelfstandige versie kan drukke reacties retourneren en heeft beperkte schaal en monitoring.

Net als Distcp moet de AdlCopy worden georkestreerd door iets als Azure Automation of Windows Task Scheduler. Net als bij Data Factory ondersteunt AdlCopy geen ondersteuning voor het kopiëren van alleen bijgewerkte bestanden, maar voor het opnieuw kopiëren en overschrijven van bestaande bestanden. Zie [Gegevens kopiëren van Azure Storage Blobs naar Data Lake Storage Gen1 voor](data-lake-store-copy-data-azure-storage-blob.md)meer informatie en voorbeelden van het gebruik van AdlCopy.

## <a name="monitoring-considerations"></a>Monitoringoverwegingen

Data Lake Storage Gen1 biedt gedetailleerde diagnostische logboeken en auditing. Data Lake Storage Gen1 biedt een aantal basisstatistieken in de Azure-portal onder het Data Lake Storage Gen1-account en in Azure Monitor. Beschikbaarheid van Data Lake Storage Gen1 wordt weergegeven in de Azure-portal. Deze statistiek wordt echter elke zeven minuten vernieuwd en kan niet worden opgevraagd via een openbaar belichte API. Om de meest up-to-date beschikbaarheid van een Data Lake Storage Gen1-account te krijgen, moet u uw eigen synthetische tests uitvoeren om de beschikbaarheid te valideren. Andere statistieken, zoals het totale opslaggebruik, lees-/schrijfverzoeken en invallen/uitgangen, kunnen tot 24 uur duren voordat deze worden vernieuwd. Er moeten dus meer up-to-date statistieken handmatig worden berekend via hadoop-opdrachtregelgereedschappen of het aggregeren van logboekgegevens. De snelste manier om het meest recente opslaggebruik te krijgen, is het uitvoeren van deze HDFS-opdracht vanaf een Hadoop-clusterknooppunt (bijvoorbeeld hoofdknooppunt):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Gegevens Meer opslag Gen1-diagnose exporteren

Een van de snelste manieren om toegang te krijgen tot doorzoekbare logboeken van Data Lake Storage Gen1 is door logboekverzending naar **Log Analytics** mogelijk te maken onder het **Diagnostics-blade** voor het Data Lake Storage Gen1-account. Dit biedt directe toegang tot binnenkomende logboeken met tijd- en inhoudsfilters, samen met waarschuwingsopties (e-mail/webhook) die binnen intervallen van 15 minuten worden geactiveerd. Zie Diagnostische [logboeken voor Azure Data Lake Storage Gen1 voor](data-lake-store-diagnostic-logs.md)instructies.

Voor meer realtime waarschuwingen en meer controle over waar de logboeken moeten worden geplaatst, u overwegen logboeken te exporteren naar Azure EventHub, waar inhoud afzonderlijk of in een tijdvenster kan worden geanalyseerd om realtime meldingen naar een wachtrij te verzenden. Een afzonderlijke toepassing, zoals een [Logic App,](../connectors/connectors-create-api-azure-event-hubs.md) kan de waarschuwingen vervolgens gebruiken en communiceren naar het juiste kanaal, en statistieken indienen bij monitoringtools zoals NewRelic, Datadog of AppDynamics. Als u een tool van derden gebruikt, zoals ElasticSearch, u de logboeken exporteren naar Blob Storage en de [Azure Logstash-plug-in](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) gebruiken om de gegevens te gebruiken in uw Elasticsearch-, Kibana- en Logstash-stack (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Logboekregistratie op foutopsporingsniveau inschakelen in HDInsight

Als De logboekverzending van Data Lake Storage Gen1 niet is ingeschakeld, biedt Azure HDInsight ook een manier om [logboekregistratie aan de clientzijde in](data-lake-store-performance-tuning-mapreduce.md) te schakelen voor Data Lake Storage Gen1 via log4j. U moet de volgende eigenschap instellen in **Ambari** > **YARN** > **Config** > Advanced**garen-log4j configuraties:**

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Zodra de eigenschap is ingesteld en de knooppunten opnieuw zijn gestart, wordt Gegevens Lake Storage Gen1-diagnose geschreven naar de YARN-logboeken op de knooppunten (/tmp/\<gebruiker\>/yarn.log), en belangrijke details zoals fouten of beperking (HTTP 429-foutcode) kunnen worden gecontroleerd. Deze zelfde informatie kan ook worden gecontroleerd in Azure Monitor-logboeken of waar logboeken worden verzonden in het [Diagnostics-blad](data-lake-store-diagnostic-logs.md) van het Data Lake Storage Gen1-account. Het wordt aanbevolen om op zijn minst logboekregistratie aan de clientzijde in te hebben of de optie voor het verzenden van logboeken met Data Lake Storage Gen1 te gebruiken voor operationele zichtbaarheid en eenvoudiger foutopsporing.

### <a name="run-synthetic-transactions"></a>Synthetische transacties uitvoeren

Momenteel heeft de statistiek voor de beschikbaarheid van services voor Data Lake Storage Gen1 in de Azure-portal een vernieuwingsvenster van 7 minuten. Ook kan het niet worden opgevraagd met behulp van een openbaar belichte API. Daarom wordt aanbevolen om een basistoepassing te bouwen die synthetische transacties doet naar Data Lake Storage Gen1 die tot op de minuut beschikbaarheid kan bieden. Een voorbeeld hiervan is het maken van een WebJob, Logic App of Azure Function App om een lees- of maak- en updateuit te voeren ten opzichte van Data Lake Storage Gen1 en de resultaten naar uw bewakingsoplossing te verzenden. De bewerkingen kunnen worden uitgevoerd in een tijdelijke map en vervolgens verwijderd na de test, die kan worden uitgevoerd om de 30-60 seconden, afhankelijk van de eisen.

## <a name="directory-layout-considerations"></a>Overwegingen voor directory-indeling

Bij het landen van gegevens in een gegevensmeer is het belangrijk om de structuur van de gegevens vooraf te plannen, zodat beveiliging, partitionering en verwerking effectief kunnen worden gebruikt. Veel van de volgende aanbevelingen kunnen worden gebruikt, of het nu gaat om Azure Data Lake Storage Gen1, Blob Storage of HDFS. Elke werkbelasting heeft andere vereisten voor hoe de gegevens worden verbruikt, maar hieronder staan enkele algemene lay-outs waarmee u rekening moet houden bij het werken met IoT- en batchscenario's.

### <a name="iot-structure"></a>IoT-structuur

In IoT-workloads kunnen er veel gegevens worden aangevoerd in het gegevensarchief dat zich uitstrekt over tal van producten, apparaten, organisaties en klanten. Het is belangrijk om de directory-indeling vooraf te plannen voor organisatie, beveiliging en efficiënte verwerking van de gegevens voor downstream-consumenten. Een algemene sjabloon om te overwegen kan de volgende lay-out zijn:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Als u bijvoorbeeld telemetrie voor een vliegtuigmotor binnen het Verenigd Koninkrijk landt, ziet het er mogelijk uit als de volgende structuur:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Er is een belangrijke reden om de datum aan het einde van de mapstructuur te zetten. Als u bepaalde regio's wilt vergrendelen of zaken wilt onderwerpen aan gebruikers/groepen, u dit eenvoudig doen met de POSIX-machtigingen. Anders, als er een noodzaak om een bepaalde beveiligingsgroep te beperken tot het bekijken van alleen de Britse gegevens of bepaalde vliegtuigen, met de datum structuur aan de voorkant een aparte toestemming nodig zou zijn voor tal van mappen onder elk uur map. Bovendien zou het hebben van de datumstructuur aan de voorkant het aantal mappen exponentieel verhogen naarmate de tijd verstreek.

### <a name="batch-jobs-structure"></a>Structuur batchtaken

Van een hoog niveau, een veelgebruikte aanpak in batch verwerking is om gegevens te landen in een "in" map. Vervolgens, zodra de gegevens zijn verwerkt, zet de nieuwe gegevens in een "out" map voor downstream processen te consumeren. Deze directorystructuur wordt soms gezien voor taken waarvoor verwerking op afzonderlijke bestanden vereist is en die mogelijk geen massaal parallelle verwerking nodig heeft ten opzichte van grote gegevenssets. Net als de hierboven aanbevolen IoT-structuur heeft een goede directorystructuur de mappen op bovenliggend niveau voor zaken als regio- en onderwerpzaken (bijvoorbeeld organisatie, product/producent). Deze structuur helpt bij het beveiligen van de gegevens in uw organisatie en een beter beheer van de gegevens in uw workloads. Houd bovendien rekening met datum en tijd in de structuur om een betere organisatie, gefilterde zoekopdrachten, beveiliging en automatisering in de verwerking mogelijk te maken. Het niveau van granulariteit voor de datumstructuur wordt bepaald door het interval waarop de gegevens worden geüpload of verwerkt, zoals per uur, per dag of zelfs per maand.

Soms is bestandsverwerking mislukt als gevolg van gegevensbeschadiging of onverwachte indelingen. In dergelijke gevallen kan de directorystructuur profiteren van een **/slechte** map om de bestanden te verplaatsen voor verdere inspectie. De batchtaak kan ook de rapportage of melding van deze *slechte* bestanden verwerken voor handmatige interventie. Denk aan de volgende sjabloonstructuur:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Een marketingbedrijf ontvangt bijvoorbeeld dagelijkse gegevensfragmenten van klantupdates van hun klanten in Noord-Amerika. Het lijkt misschien op het volgende fragment voor en na verwerking:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

In het algemene geval dat batchgegevens rechtstreeks worden verwerkt in databases zoals Hive of traditionele SQL-databases, is er geen behoefte aan een **/in** **of /out-map,** omdat de uitvoer al in een aparte map voor de Hive-tabel of externe database gaat. Dagelijkse uittreksels van klanten zouden bijvoorbeeld in hun respectievelijke mappen terecht komen en orkestratie door iets als Azure Data Factory, Apache Oozie of Apache Airflow zou een dagelijkse Hive- of Spark-taak activeren om de gegevens in een Hive-tabel te verwerken en te schrijven.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Toegangsbeheer in Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Beveiliging in Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Azure Data Lake Storage Gen1 afstemmen op prestaties](data-lake-store-performance-tuning-guidance.md)
* [Richtlijnen voor prestatieafstemming voor het gebruik van HDInsight Spark met Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Richtlijnen voor prestatieafstemming voor het gebruik van HDInsight Hive met Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [HDInsight-clusters maken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
