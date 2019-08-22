---
title: Gegevens importeren in een zoek index met behulp van Azure Portal-Azure Search
description: Meer informatie over het gebruik van de wizard gegevens importeren in de Azure Portal om Azure-gegevens te verkennen vanuit Cosmos DB, Blob Storage, Table Storage, SQL Database en SQL Server op Azure-Vm's.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e784cbf351bd062712e0fd66332799907a3bcae8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648256"
---
# <a name="import-data-wizard-for-azure-search"></a>De wizard gegevens importeren voor Azure Search

In het Azure Search-dashboard van Azure Portal vindt u de wizard **Gegevens importeren** waarmee u gegevens in een index kunt laden. Achter de schermen worden een *gegevens bron*, *index*en *Indexeer functie* door de wizard geconfigureerd en aangeroepen, waarbij verschillende stappen van het indexerings proces worden geautomatiseerd: 

* Hiermee maakt u verbinding met een externe gegevens bron in hetzelfde Azure-abonnement.
* U kunt ook optische teken herkenning of natuurlijke taal verwerking integreren voor het extra heren van tekst uit ongestructureerde gegevens.
* Genereert een index op basis van gegevens steekproef en meta gegevens van de externe gegevens bron.
* Verkent de gegevens bron voor Doorzoek bare inhoud, serialisatie en laden van JSON-documenten in de index.

De wizard kan geen verbinding maken met een vooraf gedefinieerde index of een bestaande Indexeer functie uitvoeren, maar in de wizard kunt u een nieuwe index of Indexeer functie configureren ter ondersteuning van de structuur en het gedrag dat u nodig hebt.

Bent u niet bekend met Azure Search? Door loop de [Snelstartgids: Importeer, index en query met behulp van](search-get-started-portal.md) Portal-hulpprogram ma's om het importeren en indexeren te testen met behulp van **import gegevens** en de ingebouwde set met voorbeeld gegevens voor onroerend goed.

## <a name="start-importing-data"></a>Beginnen met het importeren van gegevens

In deze sectie wordt uitgelegd hoe u de wizard start en een overzicht op hoog niveau van elke stap geeft.

1. Open in de [Azure Portal](https://portal.azure.com)de pagina zoek service in het dash board of [Zoek uw service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in de lijst met Services.

2. Klik op de pagina overzicht van services bovenaan op **gegevens importeren**.

   De ![opdracht gegevens importeren in de portal](./media/search-import-data-portal/import-data-cmd2.png "De wizard gegevens importeren starten")

   > [!NOTE]
   > U kunt **import gegevens** vanuit andere Azure-Services starten, waaronder Azure Cosmos DB, Azure SQL database en Azure Blob Storage. Zoek **Azure Search toevoegen** in het linkerdeel venster op de pagina service overzicht.

3. De wizard wordt geopend om **verbinding te maken met uw gegevens**, waar u een externe gegevens bron kunt kiezen die u voor deze import wilt gebruiken. Er zijn verschillende dingen die u moet weten over deze stap, lees de sectie [gegevens bron ingangen](#data-source-inputs) voor meer informatie.

   De ![wizard gegevens importeren in de portal] De (./media/search-import-data-portal/import-data-wizard-startup.png "wizard gegevens importeren voor Azure Search")

4. Vervolgens voegt u een **cognitieve zoek opdracht toe**, voor het geval u optische teken herkenning (OCR) van tekst in afbeeldings bestanden wilt opnemen of tekst analyse over ongestructureerde gegevens. AI-algoritmen van Cognitive Services worden voor deze taak opgehaald. Deze stap bestaat uit twee delen:
  
   Koppel eerst [een Cognitive Services-resource](cognitive-search-attach-cognitive-services.md) aan een Azure Search-vakkennisset.
  
   Kies vervolgens welke AI-verrijkingen u wilt toevoegen in de vaardig heden. Zie deze [Snelstartgids](cognitive-search-quickstart-blob.md)voor een demonstratie.

   Als u alleen gegevens wilt importeren, slaat u deze stap over en gaat u rechtstreeks naar de index definitie.

5. Vervolgens past u de **doel index**aan, waar u het index schema dat in de wizard wordt weer gegeven, kunt accepteren of wijzigen. De wizard leidt de velden en gegevens typen af door gegevens te bemonsteren en meta gegevens te lezen van de externe gegevens bron.

   Controleer voor elk veld [de index kenmerken](#index-definition) om specifiek gedrag in te scha kelen. Als u geen kenmerken selecteert, is de index niet bruikbaar. 

6. Vervolgens **maakt u een Indexeer functie**. Dit is een product van deze wizard. Een Indexeer functie is een crawler die Doorzoek bare gegevens en meta gegevens ophaalt uit een externe Azure-gegevens bron. Door de gegevens bron te selecteren en vaardig heden (optioneel) en een index te koppelen, hebt u een Indexeer functie geconfigureerd tijdens elke stap van de wizard.

   Geef de Indexeer functie een naam en klik op **verzenden** om het import proces te starten. 

U kunt de indexering controleren in de portal door te klikken op de Indexeer functie in de lijst **Indexeer functies** . Naarmate meer documenten worden geladen, neemt het aantal documenten toe voor de index die u hebt gedefinieerd. Soms duurt het enkele minuten voordat de portalpagina de meest recente updates heeft opgehaald.

De index kan worden doorzocht zodra het eerste document wordt geladen. U kunt [Search Explorer](search-explorer.md) gebruiken voor deze taak.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Invoer gegevens bron

Met de wizard **gegevens importeren** maakt u een permanent gegevens bron object waarmee verbindings gegevens worden opgegeven voor een externe gegevens bron. Het gegevens bron object wordt uitsluitend gebruikt met [Indexeer functies](search-indexer-overview.md) en kan worden gemaakt voor de volgende gegevens bronnen: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) (niet ondersteund voor [cognitieve Zoek](cognitive-search-concept-intro.md) pijplijnen)

U kunt alleen importeren uit één tabel, database weergave of gelijkwaardige gegevens structuur, maar de structuur kan hiërarchische of geneste substructuren bevatten. Zie voor meer informatie [complex typen model leren](search-howto-complex-data-types.md).

U moet deze gegevens structuur maken voordat u de wizard uitvoert en deze moet inhoud bevatten. Voer de wizard **gegevens importeren** niet uit op een lege gegevens bron.

|  Selectie | Description |
| ---------- | ----------- |
| **Bestaande gegevensbron** |Als u al indexeerfuncties hebt gedefinieerd in uw zoekservice, kunt u een bestaande gegevensbrondefinitie voor een andere import selecteren. In Azure Search worden gegevens bron objecten alleen gebruikt door Indexeer functies. U kunt een gegevens bron object maken via een programma of via de wizard **gegevens importeren** .|
| **Voorbeelden**| Azure Search fungeert als host voor een gratis globale Azure-SQL database die u kunt gebruiken voor het importeren en aanvragen van query's in Azure Search. Zie [Quickstart: Importeer, index en query met behulp van](search-get-started-portal.md) Portal tools voor een overzicht. |
| **Azure SQL Database** |De servicenaam, referenties voor een databasegebruiker met leesmachtiging en de naam van een database kunnen worden opgegeven op de pagina of via een ADO.NET-verbindingsreeks. Kies de verbindingsreeksoptie om eigenschappen te bekijken of aan te passen. <br/><br/>De tabel of weergave die de rijenset bevat, moet worden opgegeven op de pagina. Deze optie wordt weergegeven nadat de verbinding tot stand is gebracht, waarna een vervolgkeuzelijst wordt weergegeven zodat u een selectie kunt maken. |
| **SQL Server op virtuele Azure-machine** |Geef een volledig gekwalificeerde service naam, gebruikers-ID en wacht woord en Data Base als connection string op. Voor het gebruik van deze gegevensbron moet u eerder een certificaat hebben geïnstalleerd in het lokale archief dat de verbinding versleutelt. Zie [SQL VM-verbinding met Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) voor instructies. <br/><br/>De tabel of weergave die de rijenset bevat, moet worden opgegeven op de pagina. Deze optie wordt weergegeven nadat de verbinding tot stand is gebracht, waarna een vervolgkeuzelijst wordt weergegeven zodat u een selectie kunt maken. |
| **Cosmos DB** |Vereisten zijn het account, de database en de verzameling. Alle documenten in de verzameling worden opgenomen in de index. U kunt een query definiëren om de rijenset samen te voegen of te filteren, of de query leeg laten. Er is geen query vereist in deze wizard.|
| **Azure Blob Storage** |Vereisten zijn het opslagaccount en een container. Als blob-namen een virtuele naamconventie voor groeperingsdoeleinden volgen, kunt u desgewenst het gedeelte van de virtuele map van de naam als een map onder de container opgeven. Zie [Blob Storage indexeren](search-howto-indexing-azure-blob-storage.md) voor meer informatie. |
| **Azure Table Storage** |Vereisten zijn het opslagaccount en een tabelnaam. U kunt desgewenst een query opgeven om een subset van de tabellen op te halen. Zie [Table Storage indexeren](search-howto-indexing-azure-tables.md) voor meer informatie. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Index kenmerken

De wizard **gegevens importeren** genereert een index die wordt gevuld met documenten die zijn opgehaald uit de gegevens bron invoer. 

Voor een functionele index moet u de volgende elementen definiëren.

1. Eén veld moet worden gemarkeerd als een **sleutel**, die wordt gebruikt om elk document uniek te identificeren. De **sleutel** moet *EDM. String*zijn. 

   Als veld waarden spaties of streepjes bevatten, moet u de optie **Base-64 Codeer sleutel** instellen in de stap **een Indexeer functie maken** onder **Geavanceerde opties**om de validatie controle voor deze tekens te onderdrukken.

1. Stel de index kenmerken voor elk veld in. Als u geen kenmerken selecteert, is de index in feite leeg, met uitzonde ring van het vereiste sleutel veld. Kies ten minste één of meer van deze kenmerken voor elk veld.
   
   + **Ophalenable** retourneert het veld in de zoek resultaten. Elk veld dat inhoud aan Zoek resultaten levert, moet dit kenmerk hebben. Het instellen van dit veld heeft geen aanzienlijke gevolgen voor de index grootte.
   + Met filterable kan worden verwezen naar het veld in filter expressies. Elk veld dat in een **$filter** expressie wordt gebruikt, moet dit kenmerk hebben. Filter expressies zijn voor exacte overeenkomsten. Omdat tekst teken reeksen intact blijven, is extra opslag ruimte vereist voor de Verbatim-inhoud.
   + Doorzoekbaar maakt zoeken in volledige tekst mogelijk. Elk veld dat wordt gebruikt in vrije-vorm query's of in query-expressies moet dit kenmerk hebben. Er worden omgekeerde indexen gemaakt voor elk veld dat u als doorzoekbaar markeert.

1. Indien nodig kunt u dit kenmerk ook als volgt instellen:

   + Met sorteerbaar kunt u het veld in een sortering gebruiken. Elk veld dat in een **$OrderBy** expressie wordt gebruikt, moet dit kenmerk hebben.
   + **Facetable** schakelt het veld in voor facet navigatie. Alleen velden die ook als **filterbaar** zijn gemarkeerd, kunnenals facetbaar worden gemarkeerd.

1. Stel een **analyse functie** in als u de taal uitgebreid indexeren en query's wilt uitvoeren. De standaard waarde is *standaard lucene* , maar u kunt *micro soft English* kiezen als u de analyse functie van micro soft voor geavanceerde lexicale verwerking wilt gebruiken, zoals het oplossen van onregelmatige zelfstandig-en verbale formulieren.

   + Selecteer doorzoekbaar om de **analyse** lijst in te scha kelen.
   + Kies een analyse functie die in de lijst wordt weer gegeven. 
   
   Op dit moment kunnen alleen taalanalysefuncties worden opgegeven. Voor het gebruik van een aangepaste analysefunctie of een niet-taal-analysefunctie zoals sleutelwoord, patroon, enzovoort is code vereist. Zie [een index voor documenten in meerdere talen maken](search-language-support.md)voor meer informatie over analyseren.

1. Schakel het selectie vakje Voorst **Ellen** in voor het inschakelen van type-ahead query suggesties voor geselecteerde velden.


## <a name="next-steps"></a>Volgende stappen
Bekijk deze koppelingen voor meer informatie over indexeerfuncties:

* [Azure SQL Database indexeren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB indexeren](search-howto-index-cosmosdb.md)
* [Blob Storage indexeren](search-howto-indexing-azure-blob-storage.md)
* [Table Storage indexeren](search-howto-indexing-azure-tables.md)