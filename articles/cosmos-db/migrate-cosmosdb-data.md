---
title: Honderden terabytes aan gegevens migreren naar Azure Cosmos DB
description: In dit document wordt beschreven hoe u 100 s terabytes aan gegevens migreren naar Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880208"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Honderden terabytes aan gegevens migreren naar Azure Cosmos DB 

Azure Cosmos DB kan terabytes aan gegevens opslaan. U kunt een grootschalige gegevensmigratie uitvoeren om de werkbelasting van uw productie naar Azure Cosmos DB te verplaatsen. In dit artikel worden de uitdagingen beschreven van het verplaatsen van grootschalige gegevens naar Azure Cosmos DB en wordt u voorgesteld aan het hulpprogramma dat u helpt bij deze uitdagingen en dat gegevens naar Azure Cosmos DB migreert. In deze casestudy heeft de klant de Cosmos DB SQL-API gebruikt.  

Voordat u de volledige werkbelasting migreert naar Azure Cosmos DB, u een subset van gegevens migreren om een aantal aspecten te valideren, zoals de keuze van partitiesleutel, queryprestaties en gegevensmodellering. Nadat u de proof of concept hebt gevalideerd, u de volledige werkbelasting verplaatsen naar Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Hulpmiddelen voor gegevensmigratie 

Azure Cosmos DB-migratiestrategieën verschillen momenteel op basis van de API-keuze en de grootte van de gegevens. Als u kleinere gegevenssets wilt migreren - voor het valideren van gegevensmodellering, queryprestaties, keuze van partitiesleutel enz.) u kiezen voor het [Hulpprogramma voor gegevensmigratie](import-data.md) of de [Azure Cosmos DB-connector van Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Als u bekend bent met Spark, u er ook voor kiezen om de [Azure Cosmos DB Spark-connector](spark-connector.md) te gebruiken om gegevens te migreren.

## <a name="challenges-for-large-scale-migrations"></a>Uitdagingen voor grootschalige migraties 

De bestaande hulpprogramma's voor het migreren van gegevens naar Azure Cosmos DB hebben een aantal beperkingen die vooral op grote schaal duidelijk worden:

 * **Beperkte scale-outmogelijkheden:** Om terabytes aan gegevens zo snel mogelijk te migreren naar Azure Cosmos DB en om de volledige ingerichte doorvoer effectief te verbruiken, moeten de migratieclients de mogelijkheid hebben om voor onbepaalde tijd uit te schalen.  

* **Gebrek aan voortgangstracking en controle-aanwijzen:** Het is belangrijk om de voortgang van de migratie bij te houden en controlete laten wijzen tijdens het migreren van grote gegevenssets. Anders stopt elke fout die optreedt tijdens de migratie de migratie en moet u het proces helemaal opnieuw starten. Het zou niet productief zijn om het hele migratieproces opnieuw op te starten als 99% ervan al is voltooid.  

* **Gebrek aan dode letter wachtrij**: Binnen grote gegevenssets, in sommige gevallen kunnen er problemen met delen van de brongegevens. Bovendien kunnen er tijdelijke problemen zijn met de client of het netwerk. Een van deze gevallen mag er niet toe leiden dat de hele migratie mislukt. Hoewel de meeste migratietools robuuste mogelijkheden voor nieuwe oplossingen hebben die voorkomen tegen intermitterende problemen, is het niet altijd voldoende. Als bijvoorbeeld minder dan 0,01% van de brongegevensdocumenten groter is dan 2 MB, mislukt het schrijven van documenten in Azure Cosmos DB. Idealiter is het handig dat het migratiehulpprogramma deze 'mislukte' documenten blijft voortduren in een andere wachtrij voor dode letters, die na migratie kan worden verwerkt. 

Veel van deze beperkingen worden opgelost voor hulpprogramma's zoals Azure Data factory, Azure Data Migration services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Aangepast gereedschap met bulkexecutorbibliotheek 

De uitdagingen die in de bovenstaande sectie worden beschreven, kunnen worden opgelost met behulp van een aangepast hulpprogramma dat eenvoudig kan worden uitgeschaald over meerdere instanties en bestand is tegen tijdelijke fouten. Bovendien kan het aangepaste gereedschap de migratie bij verschillende controlepunten onderbreken en hervatten. Azure Cosmos DB biedt al de [bulkexecutorbibliotheek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) die een aantal van deze functies bevat. De bulkexecutorbibliotheek heeft bijvoorbeeld al de functionaliteit om tijdelijke fouten te verwerken en kan threads in één knooppunt uitschalen om ongeveer 500 K RU's per knooppunt te verbruiken. De bibliotheek voor bulkuitvoerbestanden partities ook de brongegevensset in microbatches die onafhankelijk worden uitgevoerd als een vorm van controlecontrole.  

Het aangepaste gereedschap maakt gebruik van de bulkexecutorbibliotheek en ondersteunt het uitschalen van meerdere clients en het bijhouden van fouten tijdens het innameproces. Als u dit hulpprogramma wilt gebruiken, moeten de brongegevens worden verdeeld in afzonderlijke bestanden in Azure Data Lake Storage (ADLS), zodat verschillende migratiewerkers elk bestand kunnen oppakken en in Azure Cosmos DB kunnen opnemen. Het aangepaste hulpprogramma maakt gebruik van een aparte verzameling, die metagegevens opslaat over de migratievoortgang voor elk afzonderlijk bronbestand in ADLS en eventuele fouten bijhoudt die eraan zijn gekoppeld.  

In de volgende afbeelding wordt het migratieproces beschreven met behulp van dit aangepaste gereedschap. De tool wordt uitgevoerd op een set virtuele machines en elke virtuele machine vraagt de trackingverzameling in Azure Cosmos DB om een lease op een van de brongegevenspartities te verkrijgen. Zodra dit is gebeurd, wordt de brongegevenspartitie door het hulpprogramma gelezen en inopgenomen in Azure Cosmos DB met behulp van de bibliotheek voor bulkuitvoermiddelen. Vervolgens wordt de trackingverzameling bijgewerkt om de voortgang van de inname van gegevens en eventuele aangetroffen fouten vast te leggen. Nadat een gegevenspartitie is verwerkt, probeert het hulpprogramma de volgende beschikbare bronpartitie op te vragen. Het blijft de volgende bronpartitie verwerken totdat alle gegevens zijn gemigreerd. De broncode voor de tool is [hier](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)beschikbaar.  

 
![Instelling van migratiehulpprogramma's](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

De trackingverzameling bevat documenten zoals weergegeven in het volgende voorbeeld. U ziet dergelijke documenten één voor elke partitie in de brongegevens.  Elk document bevat de metagegevens voor de brongegevenspartitie, zoals de locatie, migratiestatus en eventuele fouten:Each document contains the metadata for the source data partition such as the location, migration status, and errors (if any any):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Voorwaarden voor gegevensmigratie 

Voordat de gegevensmigratie begint, zijn er een paar vereisten om rekening mee te houden:  

#### <a name="estimate-the-data-size"></a>Schat de gegevensgrootte:  

De grootte van de brongegevens wordt mogelijk niet precies toegewezen aan de gegevensgrootte in Azure Cosmos DB. Er kunnen enkele voorbeelddocumenten van de bron worden ingevoegd om hun gegevensgrootte in Azure Cosmos DB te controleren. Afhankelijk van de grootte van het voorbeelddocument kan de totale gegevensgrootte in Azure Cosmos DB na migratie worden geschat. 

Als elk document na migratie in Azure Cosmos DB bijvoorbeeld ongeveer 1 KB is en als er ongeveer 60 miljard documenten in de brongegevensset staan, betekent dit dat de geschatte grootte in Azure Cosmos DB bijna 60 TB zou bedragen. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Maak containers vooraf met voldoende R's: 

Hoewel Azure Cosmos DB de opslag automatisch schaalt, is het niet raadzaam om te beginnen met de kleinste containergrootte. Kleinere containers hebben een lagere beschikbaarheid van de doorvoer, wat betekent dat de migratie veel langer zou duren om te voltooien. In plaats daarvan is het handig om de containers te maken met de uiteindelijke gegevensgrootte (zoals geschat in de vorige stap) en ervoor te zorgen dat de migratieworkload de ingerichte doorvoer volledig verbruikt.  

In de vorige stap. aangezien de gegevensgrootte wordt geschat op ongeveer 60 TB, is een container van ten minste 2,4 M-R's vereist om de volledige gegevensset te kunnen aanpassen.  

 

#### <a name="estimate-the-migration-speed"></a>Schat de migratiesnelheid: 

Ervan uitgaande dat de migratiewerkbelasting de volledige ingerichte doorvoer kan verbruiken, zou de gehele inrichting een schatting geven van de migratiesnelheid. Als u het vorige voorbeeld voortzet, zijn 5 RALLY's vereist voor het schrijven van een document van 1 KB naar Azure Cosmos DB SQL API-account.  2,4 miljoen AMERIKAANSE gegevens zouden een overdracht van 480.000 documenten per seconde (of 480 MB/s) mogelijk maken. Dit betekent dat de volledige migratie van 60 TB 125.000 seconden of ongeveer 34 uur zal duren.  

Als u wilt dat de migratie binnen een dag wordt voltooid, moet u de ingerichte doorvoer verhogen tot 5 miljoen RU's. 

 

#### <a name="turn-off-the-indexing"></a>Schakel de indexering uit:  

Aangezien de migratie zo snel mogelijk moet worden voltooid, is het raadzaam om de tijd te minimaliseren en DE's die worden besteed aan het maken van indexen voor elk van de ingenomen documenten.  Azure Cosmos DB indexeert automatisch alle eigenschappen, het is de moeite waard om indexering te minimaliseren tot een select aantal termen of deze volledig uit te schakelen voor het verloop van migratie. U het indexeringsbeleid van de container uitschakelen door de indexeringsmodus te wijzigen in geen, zoals hieronder wordt weergegeven:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Nadat de migratie is voltooid, u de indexering bijwerken.  

## <a name="migration-process"></a>Migratieproces 

Nadat de vereisten zijn voltooid, u gegevens migreren met de volgende stappen:  

1. Importeer eerst de gegevens uit de bron naar Azure Blob Storage. Om de migratiesnelheid te verhogen, is het handig om te parallellopen over verschillende bronpartities. Voordat de migratie wordt gestart, moet de brongegevensset worden verdeeld in bestanden met een grootte van ongeveer 200 MB.   

2. De bulkexecutorbibliotheek kan worden opgeschaald om 500.000 RU's in één client-VM te verbruiken. Aangezien de beschikbare doorvoer 5 miljoen R's bedraagt, moeten 10 Ubuntu 16.04 VM's (Standard_D32_v3) worden ingericht in dezelfde regio waar uw Azure Cosmos-database zich bevindt. U moet deze VM's voorbereiden met het migratiehulpprogramma en het instellingenbestand.  

3. Voer de wachtrijstap uit op een van de virtuele clientmachines. Met deze stap wordt de trackingverzameling gemaakt, die de ADLS-container scant en een voortgangstrackingdocument maakt voor elk van de partitiebestanden van de brongegevensset.  

4. Voer vervolgens de importstap uit op alle VM's van de client. Elk van de clients kan eigenaar worden van een bronpartitie en zijn gegevens opnemen in Azure Cosmos DB. Zodra het is voltooid en de status ervan is bijgewerkt in de trackingverzameling, kunnen de clients vervolgens de volgende beschikbare bronpartitie in de trackingverzameling opvragen.  

5. Dit proces gaat door totdat de hele set bronpartities is ingenomen. Zodra alle bronpartities zijn verwerkt, moet het gereedschap opnieuw worden uitgevoerd in de foutcorrectiemodus op dezelfde trackingverzameling. Deze stap is vereist om de bronpartities te identificeren die opnieuw moeten worden verwerkt vanwege fouten.  

6. Sommige van deze fouten kunnen het gevolg zijn van onjuiste documenten in de brongegevens. Deze moeten worden geïdentificeerd en vastgesteld. Vervolgens moet u de importstap op de mislukte partities opnieuw uitvoeren om ze opnieuw te plaatsen. 

Zodra de migratie is voltooid, u valideren dat het aantal documenten in Azure Cosmos DB gelijk is aan het aantal documenten in de brondatabase. In dit voorbeeld is de totale grootte in Azure Cosmos DB 65 terabyte geworden. Na migratie kan indexering selectief worden ingeschakeld en kunnen de RU's worden verlaagd tot het niveau dat vereist is voor de bewerkingen van de werkbelasting.

## <a name="contact-the-azure-cosmos-db-team"></a>Contact opnemen met het Azure Cosmos DB-team
Hoewel u deze handleiding volgen om grote gegevenssets succesvol te migreren naar Azure Cosmos DB, wordt voor grootschalige migraties aanbevolen dat u het Azure Cosmos DB-productteam bereikt om de gegevensmodellering en een algemene architectuurbeoordeling te valideren. Op basis van uw gegevensset en werkbelasting kan het productteam ook andere prestatie- en kostenoptimalisaties voorstellen die op u van toepassing kunnen zijn. Als u contact wilt opnemen met het Azure Cosmos DB-team voor hulp bij grootschalige migraties, u een ondersteuningsticket openen onder het probleemtype 'Algemeen advies' en het probleemsubtype 'Grote (TB+) migraties zoals hieronder weergegeven.

![Ondersteuningsonderwerp migratie](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Volgende stappen

* Meer informatie door de voorbeeldtoepassingen uit te proberen die de bibliotheek voor bulkuitvoerbestanden in [.NET](bulk-executor-dot-net.md) en [Java gebruiken](bulk-executor-java.md). 
* De bulkexecutorbibliotheek is geïntegreerd in de Cosmos DB Spark-connector, zie het artikel [azure cosmos DB Spark-connector.](spark-connector.md)  
* Neem contact op met het Azure Cosmos DB-productteam door een ondersteuningsticket te openen onder het probleemtype 'Algemeen advies' en het probleemsubtype 'Grote (TB+) migraties' voor extra hulp bij grootschalige migraties. 
