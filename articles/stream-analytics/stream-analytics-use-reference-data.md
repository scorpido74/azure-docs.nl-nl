---
title: Referentiegegevens gebruiken voor opzoekingen in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u referentiegegevens gebruikt om gegevens op te zoeken of te correleren in het queryontwerp van een Azure Stream Analytics-taak.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b3808524706b13761dd8eccffa301c602d08f481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267285"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Referentiegegevens gebruiken voor opzoekingen in Stream Analytics

Referentiegegevens (ook wel opzoektabel genoemd) is een eindige gegevensset die statisch is of langzaam van aard verandert, wordt gebruikt om een opzoeking uit te voeren of om uw gegevensstromen uit te breiden. In een IoT-scenario u bijvoorbeeld metagegevens over sensoren (die niet vaak veranderen) opslaan in referentiegegevens en deze aansluiten bij realtime IoT-gegevensstromen. Azure Stream Analytics laadt referentiegegevens in het geheugen om te komen tot verwerking met een lage latentiestroom. Als u referentiegegevens wilt gebruiken in uw Azure Stream Analytics-taak, gebruikt u doorgaans een [verwijzingsgegevensjoin](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) in uw query. 

Stream Analytics ondersteunt Azure Blob-opslag en Azure SQL Database als de opslaglaag voor referentiegegevens. U ook referentiegegevens transformeren en/of kopiëren naar Blob-opslag vanuit Azure Data Factory om [een willekeurig aantal cloudgebaseerde en on-premises gegevensopslag](../data-factory/copy-activity-overview.md)te gebruiken.

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referentiegegevens worden gemodelleerd als een reeks blobs (gedefinieerd in de invoerconfiguratie) in oplopende volgorde van de datum/tijd die is opgegeven in de blobnaam. Het ondersteunt **alleen** het toevoegen aan het einde van de reeks met behulp van een datum / tijd **groter** dan de datum die is opgegeven door de laatste blob in de reeks.

### <a name="configure-blob-reference-data"></a>Blobreferentiegegevens configureren

Als u uw referentiegegevens wilt configureren, moet u eerst een invoer maken die van het type **Referentiegegevens**is. In de onderstaande tabel wordt elke eigenschap uitgelegd die u moet leveren terwijl u de invoer van referentiegegevens maakt met de beschrijving:

|**Naam van eigenschap**  |**Beschrijving**  |
|---------|---------|
|Invoeralias   | Een vriendelijke naam die wordt gebruikt in de taakquery om naar deze invoer te verwijzen.   |
|Opslagaccount   | De naam van het opslagaccount waar uw blobs zich bevinden. Als het in hetzelfde abonnement staat als je Stream Analytics-taak, kun je deze selecteren in de vervolgkeuzelijst.   |
|Opslagaccountsleutel   | De geheime sleutel die is gekoppeld aan het opslagaccount. Dit wordt automatisch ingevuld als het opslagaccount zich in hetzelfde abonnement bevindt als uw Stream Analytics-taak.   |
|Opslagcontainer   | Containers bieden een logische groepering voor blobs die zijn opgeslagen in de Microsoft Azure Blob-service. Wanneer u een blob uploadt naar de Blob-service, moet u een container voor die blob opgeven.   |
|Padpatroon   | Het pad dat wordt gebruikt om uw blobs in de opgegeven container te lokaliseren. Binnen het pad u ervoor kiezen om een of meer exemplaren van de volgende 2 variabelen op te geven:<BR>{date}, {time}<BR>Voorbeeld 1: producten/{date}/{time}/product-list.csv<BR>Voorbeeld 2: producten/{date}/product-list.csv<BR>Voorbeeld 3: productlijst.csv<BR><br> Als de blob niet bestaat in het opgegeven pad, wacht de taak Stream Analytics voor onbepaalde tijd tot de blob beschikbaar is.   |
|Datumnotatie [optioneel]   | Als u {date} hebt gebruikt in het door u opgegeven padpatroon, u de datumnotatie selecteren waarin uw blobs zijn georganiseerd in de vervolgkeuzelijst van ondersteunde indelingen.<BR>Voorbeeld: YYYY/MM/DD, MM/DD/YYYY, etc.   |
|Tijdnotatie [optioneel]   | Als u {time} hebt gebruikt in het opgegeven padpatroon, u de tijdindeling selecteren waarin uw blobs zijn georganiseerd in de vervolgkeuzelijst van ondersteunde indelingen.<BR>Voorbeeld: HH, HH/mm of HH-mm.  |
|Indeling voor gebeurtenisserialisatie   | Om ervoor te zorgen dat uw query's werken zoals u verwacht, moet Stream Analytics weten welke serialisatie-indeling u gebruikt voor binnenkomende gegevensstromen. Voor referentiegegevens zijn de ondersteunde indelingen CSV en JSON.  |
|Encoding   | UTF-8 is op dit moment het enige ondersteunde coderingsformaat.  |

### <a name="static-reference-data"></a>Statische referentiegegevens

Als de referentiegegevens naar verwachting niet worden gewijzigd, wordt ondersteuning voor statische referentiegegevens ingeschakeld door een statisch pad in de invoerconfiguratie op te geven. Azure Stream Analytics pikt de blob op van het opgegeven pad. {date} en {time} substitutietokens zijn niet vereist. Omdat referentiegegevens onveranderlijk zijn in Stream Analytics, wordt het niet aanbevolen om een statische referentiegegevensblob te overschrijven.

### <a name="generate-reference-data-on-a-schedule"></a>Referentiegegevens genereren in een planning

Als uw referentiegegevens een langzaam veranderende gegevensset zijn, wordt ondersteuning voor het vernieuwen van referentiegegevens ingeschakeld door een padpatroon op te geven in de invoerconfiguratie met behulp van de vervangingstokens {date} en {time}. Stream Analytics pikt de bijgewerkte definities van referentiegegevens op op basis van dit padpatroon. `sample/{date}/{time}/products.csv` Een patroon van bijvoorbeeld met een datumformaat van **"YYYY-MM-DD"** en een tijdindeling van **"HH-mm"** instrueert Stream Analytics om de bijgewerkte blob `sample/2015-04-16/17-30/products.csv` op 16 april 2015 UTC-tijdzone om 17:30 uur op te halen.

Azure Stream Analytics scant automatisch op vernieuwde referentiegegevensblobs met een interval van één minuut. Als een blob met tijdstempel 10:30:00 wordt geüpload met een kleine vertraging (bijvoorbeeld 10:30:30), ziet u een kleine vertraging in stream Analytics taak die verwijst naar deze blob. Om dergelijke scenario's te voorkomen, wordt aanbevolen om de blob eerder te uploaden dan de doeleffectieve tijd (10:30:00 in dit voorbeeld) om de Stream Analytics-taak voldoende tijd te geven om deze te ontdekken en te laden in het geheugen en bewerkingen uit te voeren. 

> [!NOTE]
> Momenteel zoeken Stream Analytics-taken alleen naar vernieuwing van de blob wanneer de machinetijd wordt vervijfvoudigd naar de tijd die is gecodeerd in de naam van de blob. De taak zoekt `sample/2015-04-16/17-30/products.csv` bijvoorbeeld zo snel mogelijk, maar niet eerder dan 17:30 uur op de UTC-tijdzone van 16 april 2015. Het zal *nooit* op zoek naar een blob met een gecodeerde tijd eerder dan de laatste die wordt ontdekt.
> 
> Als de taak bijvoorbeeld de `sample/2015-04-16/17-30/products.csv` blob vindt, worden bestanden met een gecodeerde datum eerder dan 16 april 2015 genegeerd, dus als er een te laat aankomende `sample/2015-04-16/17-25/products.csv` blob in dezelfde container wordt gemaakt, wordt de taak niet gebruikt.
> 
> Evenzo `sample/2015-04-16/17-30/products.csv` als alleen wordt geproduceerd om 22:03 16 april 2015, maar geen blob met een eerdere datum aanwezig is in de container, zal de taak dit bestand gebruiken vanaf 16 april 20:03 uur en de vorige referentiegegevens tot die tijd gebruiken.
> 
> Een uitzondering hierop is wanneer de taak gegevens opnieuw in de tijd moet verwerken of wanneer de taak voor het eerst wordt gestart. Bij het begin is de taak op zoek naar de meest recente blob die is geproduceerd vóór de opgegeven begintijd van de taak. Dit wordt gedaan om ervoor te zorgen dat er een **niet-lege** referentiegegevensset is wanneer de taak begint. Als er geen wordt gevonden, wordt `Initializing input without a valid reference data blob for UTC time <start time>`in de taak de volgende diagnose weergegeven: .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kan worden gebruikt om de taak van het maken van de bijgewerkte blobs die stream analytics nodig heeft om referentiegegevensdefinities bij te werken, te orkestreren. Een Data Factory is een cloudgebaseerde gegevensintegratieservice waarmee de verplaatsing en transformatie van gegevens wordt beheerd en geautomatiseerd. Data Factory ondersteunt [het eenvoudig verbinden met een groot aantal cloudgebaseerde en on-premises datastores](../data-factory/copy-activity-overview.md) en het eenvoudig verplaatsen van gegevens volgens een regelmatig schema dat u opgeeft. Voor meer informatie en stapsgewijze richtlijnen voor het instellen van een Data Factory-pijplijn om referentiegegevens te genereren voor Stream Analytics, die wordt vernieuwd volgens een vooraf gedefinieerd schema, raadpleegt u deze [GitHub-steekproef.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs)

### <a name="tips-on-refreshing-blob-reference-data"></a>Tips voor het vernieuwen van blobreferentiegegevens

1. Overschrijf geen verwijzingengegevensblobs omdat ze onveranderlijk zijn.
2. De aanbevolen manier om referentiegegevens te vernieuwen is:
    * {date}/{time} gebruiken in het padpatroon
    * Een nieuwe blob toevoegen met hetzelfde container- en padpatroon dat is gedefinieerd in de taakinvoer
    * Gebruik een datum/tijd **die groter is** dan de datum die is opgegeven door de laatste blob in de reeks.
3. Referentiegegevensblobs worden **niet** geordend op basis van de 'Laatst gewijzigde' tijd van de blob, maar alleen op de tijd en datum die in de blobnaam zijn opgegeven met behulp van de vervangingen {date} en {time}.
3. Om te voorkomen dat u een groot aantal blobs moet vermelden, u overwegen om zeer oude blobs te verwijderen waarvoor de verwerking niet meer wordt uitgevoerd. Houd er rekening mee dat ASA mogelijk een klein bedrag opnieuw moet verwerken in sommige scenario's, zoals een herstart.

## <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database-referentiegegevens worden opgehaald door uw Stream Analytics-taak en worden opgeslagen als momentopname in het geheugen voor verwerking. De momentopname van uw referentiegegevens wordt ook opgeslagen in een container in een opslagaccount dat u opgeeft in de configuratie-instellingen. De container wordt automatisch gemaakt wanneer de taak wordt gestart. Als de taak wordt gestopt of een mislukte status wordt ingegaan, worden de automatisch gemaakte containers verwijderd wanneer de taak opnieuw wordt gestart.  

Als uw referentiegegevens een langzaam veranderende gegevensset zijn, moet u de momentopname die in uw taak wordt gebruikt, periodiek vernieuwen. Met Stream Analytics u een verversingssnelheid instellen wanneer u uw Azure SQL Database-invoerverbinding configureert. De runtime van Stream Analytics zal uw Azure SQL Database opvragen met het interval dat is opgegeven door de verversingsfrequentie. De snelste refresh rate ondersteund is eenmaal per minuut. Voor elke vernieuwing slaat Stream Analytics een nieuwe momentopname op in het opgegeven opslagaccount.

Stream Analytics biedt twee opties voor het opvragen van uw Azure SQL Database. Een momentopnamequery is verplicht en moet in elke taak worden opgenomen. Stream Analytics voert de momentopnamequery periodiek uit op basis van het vernieuwingsinterval en gebruikt het resultaat van de query (de momentopname) als referentiegegevensset. De momentopnamequery moet passen bij de meeste scenario's, maar als u prestatieproblemen ondervindt met grote gegevenssets en snelle verversingssnelheden, u de deltaqueryoptie gebruiken. Query's die meer dan 60 seconden nodig hebben om de referentiegegevensset terug te sturen, resulteren in een time-out.

Met de deltaqueryoptie voert Stream Analytics de momentopnamequery in eerste instantie uit om een basislijnverwijzingsgegevensset te krijgen. Daarna voert Stream Analytics de deltaquery periodiek uit op basis van het vernieuwingsinterval om incrementele wijzigingen op te halen. Deze incrementele wijzigingen worden voortdurend toegepast op de referentiegegevensset om deze bijgewerkt te houden. Het gebruik van deltaquery kan helpen de opslagkosten en netwerk-I/O-bewerkingen te verlagen.

### <a name="configure-sql-database-reference"></a>SQL-databasereferentie configureren

Als u uw SQL-databasereferentiegegevens wilt configureren, moet u eerst **referentiegegevensinvoer** maken. In de onderstaande tabel wordt elke eigenschap uitgelegd die u moet leveren terwijl u de invoer van referentiegegevens maakt met de beschrijving. Zie [Referentiegegevens uit een SQL-database gebruiken voor een Azure Stream Analytics-taak voor](sql-reference-data.md)meer informatie.

U [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) gebruiken als referentiegegevensinvoer. U moet [het openbare eindpunt in Azure SQL Database Managed Instance configureren](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) en vervolgens handmatig de volgende instellingen in Azure Stream Analytics configureren. Azure virtual machine running SQL Server with a database attached is also supported by manually configuring the settings below.

|**Naam van eigenschap**|**Beschrijving**  |
|---------|---------|
|Invoeralias|Een vriendelijke naam die wordt gebruikt in de taakquery om naar deze invoer te verwijzen.|
|Abonnement|Kies uw abonnement|
|Database|De Azure SQL-database die uw referentiegegevens bevat. Voor Azure SQL Database Managed Instance is het vereist om de poort 3342 op te geven. Voorbeeld van *sampleserver.public.database.windows.net,3342*|
|Gebruikersnaam|De gebruikersnaam die is gekoppeld aan uw Azure SQL Database.|
|Wachtwoord|Het wachtwoord dat is gekoppeld aan uw Azure SQL Database.|
|Periodiek vernieuwen|Met deze optie u een verversingsfrequentie kiezen. Als u 'Aan' kiest, u de verversingsfrequentie opgeven in DD:HH:MM.|
|Momentopnamequery|Dit is de standaardqueryoptie waarmee de referentiegegevens uit uw SQL-database worden opgehaald.|
|Deltaquery|Kies voor geavanceerde scenario's met grote gegevenssets en een korte verversingsfrequentie de optie om een deltaquery toe te voegen.|

## <a name="size-limitation"></a>Groottebeperking

Stream Analytics ondersteunt referentiegegevens met **een maximale grootte van 300 MB.** De limiet van 300 MB van de maximale grootte van referentiegegevens is alleen haalbaar met eenvoudige query's. Naarmate de complexiteit van query's toeneemt tot stateful processing, zoals gevensterde aggregaten, temporele joins en tijdelijke analytische functies, wordt verwacht dat de maximale ondersteunde grootte van referentiegegevens afneemt. Als Azure Stream Analytics de referentiegegevens niet kan laden en complexe bewerkingen kan uitvoeren, is de taak zonder geheugen en mislukt deze. In dergelijke gevallen zal de su % gebruiksstatistiek 100% bereiken.    

|**Aantal streaming-eenheden**  |**Ondersteuning voor maximaal formaat (in MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 en verder   |300   |

Het toenemende aantal streamingeenheden van een taak van meer dan 6 verhoogt de maximale ondersteunde grootte van referentiegegevens niet.

Ondersteuning voor compressie is niet beschikbaar voor referentiegegevens. 

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
