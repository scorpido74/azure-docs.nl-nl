---
title: Azure Data Explorer-integratie met Azure Data Factory
description: Integreer in dit onderwerp Azure Data Explorer met Azure Data Factory om de kopieer-, opzoek- en opdrachttakenactiviteiten te gebruiken
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293620"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Azure Data Explorer integreren met Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) is een cloudgebaseerde data-integratieservice waarmee u verschillende gegevensarchieven integreren en activiteiten op de gegevens uitvoeren. Met ADF u datagestuurde workflows maken voor het orkestreren en automatiseren van gegevensverplaatsing en gegevenstransformatie. Azure Data Explorer is een van de [ondersteunde gegevensopslagin](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory-activiteiten voor Azure Data Explorer

Verschillende integraties met Azure Data Factory zijn beschikbaar voor Azure Data Explorer-gebruikers:

### <a name="copy-activity"></a>Kopieeractiviteit
 
Azure Data Factory Copy-activiteit wordt gebruikt om gegevens over te dragen tussen gegevensopslag. Azure Data Explorer wordt ondersteund als een bron, waarbij gegevens worden gekopieerd van Azure Data Explorer naar een ondersteund gegevensarchief en een sink, waar gegevens worden gekopieerd van een ondersteund gegevensarchief naar Azure Data Explorer. Zie [Gegevens kopiëren naar of vanuit Azure Data Explorer met Azure Data Factory](/azure/data-factory/connector-azure-data-explorer)voor meer informatie. en voor een gedetailleerde doorloop zie [laadgegevens van Azure Data Factory naar Azure Data Explorer.](data-factory-load-data.md)
Azure Data Explorer wordt ondersteund door Azure IR (Integration Runtime), wordt gebruikt wanneer gegevens worden gekopieerd binnen Azure en zelfgehoste IR, die wordt gebruikt bij het kopiëren van gegevens van/naar gegevensarchieven die zich on-premises of in een netwerk met toegangscontrole bevinden, zoals een Azure Virtual Network. Zie voor meer informatie [welke IR u moet gebruiken](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Wanneer u de kopieeractiviteit gebruikt en een **gekoppelde service** of een **gegevensset maakt,** selecteert u het gegevensarchief **Azure Data Explorer (Kusto)** en niet het oude gegevensarchief **Kusto**.  

### <a name="lookup-activity"></a>Opzoekactiviteit
 
De opzoekactiviteit wordt gebruikt voor het uitvoeren van query's in Azure Data Explorer. Het resultaat van de query wordt geretourneerd als de uitvoer van de opzoekactiviteit en kan worden gebruikt in de volgende activiteit in de pijplijn, zoals beschreven in de [ADF-opzoekdocumentatie](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Naast de responsgroottelimiet van 5.000 rijen en 2 MB heeft de activiteit ook een querytime-outlimiet van 1 uur.

### <a name="command-activity"></a>Opdrachtactiviteit

Met de opdrachtactiviteit kunnen [de besturingselementopdrachten](/azure/kusto/concepts/#control-commands)van Azure Data Explorer worden uitgevoerd. In tegenstelling tot query's kunnen de besturingselementopdrachten mogelijk gegevens of metagegevens wijzigen. Sommige besturingsopdrachten zijn gericht op het opnemen van gegevens in `.ingest`Azure `.set-or-append`Data Explorer, met opdrachten zoals of ) of `.export`gegevens van Azure Data Explorer kopiëren naar externe gegevensopslag met opdrachten zoals .
Zie [Opdrachtactiviteit Azure Data Factory gebruiken om de besturingselementopdrachten](data-factory-command-activity.md)van Azure Data Explorer uit te voeren voor een gedetailleerde doorloop van de opdrachtactiviteit.  Het gebruik van een besturingselementopdracht om gegevens te kopiëren kan soms een snellere en goedkopere optie zijn dan de activiteit Kopiëren. Zie [Selecteren tussen Kopieer- en Opdrachtactiviteiten bij het kopiëren van gegevens](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)als u wilt bepalen wanneer u de opdrachtactiviteit versus de activiteit Kopiëren wilt gebruiken.

### <a name="copy-in-bulk-from-a-database-template"></a>In bulk kopiëren vanuit een databasesjabloon

De [kopie in bulk van een database naar Azure Data Explorer met behulp van de sjabloon Azure Data Factory](data-factory-template.md) is een vooraf gedefinieerde Azure Data Factory-pijplijn. De sjabloon wordt gebruikt om veel pijplijnen per database of per tabel te maken voor sneller gegevens kopiëren. 

### <a name="mapping-data-flows"></a>Toewijzing gegevensstromen 

[Azure Data Factory mapping data flows](/azure/data-factory/concepts-data-flow-overview) zijn visueel ontworpen gegevenstransformaties waarmee data engineers grafische gegevenstransformatielogica kunnen ontwikkelen zonder code te schrijven. Als u een gegevensstroom wilt maken en gegevens wilt opnemen in Azure Data Explorer, gebruikt u de volgende methode:

1. Maak de [kaartgegevensstroom](/azure/data-factory/data-flow-create).
1. [Exporteer de gegevens naar Azure Blob](/azure/data-factory/data-flow-sink). 
1. Definieer [gebeurtenisraster-](/azure/data-explorer/ingest-data-event-grid) of [ADF-kopieeractiviteit](/azure/data-explorer/data-factory-load-data) om de gegevens in te nemen in Azure Data Explorer.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Kiezen tussen opdrachten kopiëren en Azure Data Explorer wanneer gegevens worden gekopieerd 

In deze sectie u de juiste activiteit selecteren voor uw gegevenskopieerbehoeften.

Bij het kopiëren van gegevens van of naar Azure Data Explorer zijn er twee beschikbare opties in Azure Data Factory:
* Activiteit kopiëren.
* Opdrachtvoor Azure Data Explorer, waarbij een van de besturingselementopdrachten wordt uitgevoerd die gegevens overbrengen in Azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Gegevens kopiëren vanuit Azure Data Explorer
  
U gegevens uit Azure Data Explorer [`.export`](/azure/kusto/management/data-export/) kopiëren met de kopieeractiviteit of de opdracht. De `.export` opdracht voert een query uit en exporteert vervolgens de resultaten van de query. 

Zie de volgende tabel voor een `.export` vergelijking van de activiteit kopiëren en opdracht voor het kopiëren van gegevens uit Azure Data Explorer.

| | Kopieeractiviteit | .export, opdracht |
|---|---|---|
| **Stroombeschrijving** | ADF voert een query uit op Kusto, verwerkt het resultaat en stuurt deze naar het doelgegevensarchief. <br>(**ADX > ADF > sink data store)** | ADF stuurt `.export` een besturingselementopdracht naar Azure Data Explorer, dat de opdracht uitvoert, en stuurt de gegevens rechtstreeks naar het doelgegevensarchief. <br>(**ADX > wastafelgegevensarchief)** |
| **Ondersteunde doelgegevensarchieven** | Een breed scala aan [ondersteunde gegevensarchieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure Blob, SQL-database |
| **Prestaties** | Gecentraliseerde | <ul><li>Gedistribueerd (standaard), gegevens tegelijk exporteren van meerdere knooppunten</li><li>Sneller en COGS efficiënt.</li></ul> |
| **Serverlimieten** | [Querylimieten](/azure/kusto/concepts/querylimits) kunnen worden verlengd/uitgeschakeld. Adf-query's bevatten standaard: <ul><li>Groottelimiet van 500.000 records of 64 MB.</li><li>Tijdslimiet van 10 minuten.</li><li>`noTruncation`ingesteld op false.</li></ul> | De querylimieten standaard uit- of uitschakelen: <ul><li>Groottelimieten zijn uitgeschakeld.</li><li>Servertime-out wordt verlengd tot 1 uur.</li><li>`MaxMemoryConsumptionPerIterator`en `MaxMemoryConsumptionPerQueryPerNode` worden uitgebreid tot max (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Als uw kopieerbestemming een van de gegevensopslag is die door de `.export` opdracht wordt ondersteund en `.export` als geen van de functies Voor de kopieeractiviteit van cruciaal belang is voor uw behoeften, selecteert u de opdracht.

### <a name="copying-data-to-azure-data-explorer"></a>Gegevens kopiëren naar Azure Data Explorer

U gegevens kopiëren naar Azure Data Explorer met behulp van de`.set-or-append` `.set-or-replace`kopieeractiviteit of innameopdrachten, zoals [inname uit query](/azure/kusto/management/data-ingestion/ingest-from-query) (, , `.set`, `.replace)`en [inslikken vanuit opslag](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

Zie de volgende tabel voor een vergelijking van de kopieeractiviteit en innameopdrachten voor het kopiëren van gegevens naar Azure Data Explorer.

| | Kopieeractiviteit | Inname van query<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Inname uit opslag <br> `.ingest` |
|---|---|---|---|
| **Stroombeschrijving** | ADF haalt de gegevens uit het brongegevensarchief, zet deze om in een tabelindeling en brengt de vereiste schematoewijzingswijzigingen uit. ADF uploadt de gegevens vervolgens naar Azure-blobs, splitst deze in brokken en downloadt vervolgens de blobs om ze in de ADX-tabel in te nemen. <br> (**Brongegevensarchief > ADF-> Azure-blobs > ADX)** | Deze opdrachten kunnen een query `.show` of opdracht uitvoeren en de resultaten van de query ineennemen in een tabel **(ADX > ADX).** | Deze opdracht neemt gegevens in een tabel op door de gegevens uit een of meer artefacten voor cloudopslag te "trekken". |
| **Ondersteunde brongegevensarchieven** |  [verschillende opties](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (met behulp van de sql_request-plug-in), Cosmos (met behulp van de cosmosdb_sql_request-plug-in) en elk ander gegevensarchief dat HTTP- of Python-API's biedt. | Bestandssysteem, Azure Blob-opslag, ADLS Gen 1, ADLS Gen 2 |
| **Prestaties** | Inname wordt in de wachtrij geplaatst en beheerd, wat zorgt voor kleine inname en zorgt voor een hoge beschikbaarheid door het bieden van load balancing, retries en foutafhandeling. | <ul><li>Deze opdrachten zijn niet ontworpen voor het importeren van gegevens met een hoog volume.</li><li>Werkt zoals verwacht en goedkoper. Maar voor productiescenario's en wanneer de verkeerssnelheden en gegevensgroottegroot zijn, gebruikt u de activiteit Kopiëren.</li></ul> |
| **Serverlimieten** | <ul><li>Geen groottelimiet.</li><li>Maximale time-out limiet: 1 uur per ingenomen blob. |<ul><li>Er is alleen een limiet voor de grootte van het `noTruncation=true`querygedeelte, die kan worden overgeslagen door op te geven .</li><li>Maximale time-out limiet: 1 uur.</li></ul> | <ul><li>Geen groottelimiet.</li><li>Maximale time-out limiet: 1 uur.</li></ul>|

> [!TIP]
> * Wanneer u gegevens van ADF naar `ingest from query` Azure Data Explorer kopieert, gebruikt u de opdrachten.  
> * Voor grote gegevenssets (>1 GB) gebruikt u de activiteit Kopiëren.  

## <a name="required-permissions"></a>Vereiste machtigingen

In de volgende tabel worden de vereiste machtigingen weergegeven voor verschillende stappen in de integratie met Azure Data Factory.

| Stap | Bewerking | Minimumniveau van machtigingen | Opmerkingen |
|---|---|---|---|
| **Een gekoppelde service maken** | Databasenavigatie | *databaseviewer* <br>De ingelogde gebruiker die ADF gebruikt, moet gemachtigd zijn om databasemetagegevens te lezen. | De gebruiker kan de naam van de database handmatig opgeven. |
| | Verbinding testen | *databasemonitor* of *tabelinname* <br>Serviceprincipal moet worden gemachtigd om `.show` opdrachten op databaseniveau of opname op tabelniveau uit te voeren. | <ul><li>TestConnection controleert de verbinding met het cluster en niet met de database. Het kan slagen, zelfs als de database niet bestaat.</li><li>Machtigingen voor tabelbeheerders zijn niet voldoende.</li></ul>|
| **Een gegevensset maken** | Tabelnavigatie | *databasemonitor* <br>De ingelogde gebruiker die ADF gebruikt, moet `.show` gemachtigd zijn om opdrachten op databaseniveau uit te voeren. | De gebruiker kan de tabelnaam handmatig opgeven.|
| **Een gegevensset maken** of **activiteit kopiëren** | Voorbeeld van gegevens bekijken | *databaseviewer* <br>Serviceprincipal moet gemachtigd zijn om databasemetadata te lezen. | | 
|   | Importschema | *databaseviewer* <br>Serviceprincipal moet gemachtigd zijn om databasemetadata te lezen. | Wanneer ADX de bron is van een tabelvormige-naar-tabelvormige kopie, importeert ADF automatisch schema, zelfs als de gebruiker het schema niet expliciet heeft geïmporteerd. |
| **ADX als Sink** | Een kolomtoewijzing met voornaam maken | *databasemonitor* <br>Serviceprincipal moet gemachtigd zijn om `.show` opdrachten op databaseniveau uit te voeren. | <ul><li>Alle verplichte bewerkingen werken met *tabelinname.*</li><li> Sommige optionele bewerkingen kunnen mislukken.</li></ul> |
|   | <ul><li>Een CSV-toewijzing op de tabel maken</li><li>De toewijzing laten vallen</li></ul>| *tabelinname of* *databasebeheerder* <br>Serviceprincipal moet gemachtigd zijn om wijzigingen aan te brengen in een tabel. | |
|   | Gegevens opnemen | *tabelinname of* *databasebeheerder* <br>Serviceprincipal moet gemachtigd zijn om wijzigingen aan te brengen in een tabel. | | 
| **ADX als bron** | Query uitvoeren | *databaseviewer* <br>Serviceprincipal moet gemachtigd zijn om databasemetadata te lezen. | |
| **Kusto, opdracht** | | Volgens het machtigingsniveau van elke opdracht. |

## <a name="performance"></a>Prestaties 

Als Azure Data Explorer de bron is en u de activiteit Opzoeken, kopiëren of opdracht gebruikt die een query bevat waar u aanbevolen [procedures](/azure/kusto/query/best-practices) voor prestatiegegevens en [ADF-documentatie](/azure/data-factory/copy-activity-performance)voor kopieeractiviteit raadpleegt.
  
In deze sectie wordt het gebruik van kopieeractiviteit waar Azure Data Explorer de gootsteen is, verhelpt. De geschatte doorvoer voor Azure Data Explorer-sink is 11-13 MBps. In de volgende tabel worden de parameters beschreven die van invloed zijn op de prestaties van de azure data explorer-sink.

| Parameter | Opmerkingen |
|---|---|
| **Geografische nabijheid van componenten** | Plaats alle componenten in hetzelfde gebied:<ul><li>bron- en sinkgegevensarchieven.</li><li>Runtime voor ADF-integratie.</li><li>Uw ADX-cluster.</li></ul>Zorg ervoor dat uw inburgeringsruntime zich in ieder geval in dezelfde regio bevindt als uw ADX-cluster. |
| **Aantal DIU's** | 1 VM voor elke 4 DIU's die door ADF worden gebruikt. <br>Het verhogen van de DIU's helpt alleen als uw bron een bestandsgebaseerde winkel is met meerdere bestanden. Elke VM verwerkt dan een ander bestand parallel. Daarom heeft het kopiëren van één groot bestand een hogere latentie dan het kopiëren van meerdere kleinere bestanden.|
|**Bedrag en SKU van uw ADX-cluster** | Een hoog aantal ADX-knooppunten verhoogt de verwerkingstijd van de opname.|
| **Parallellisme** | Als u een zeer grote hoeveelheid gegevens uit een database wilt kopiëren, partitiet u uw gegevens en gebruikt u vervolgens een ForEach-lus die elke partitie parallel kopieert of gebruikt u de [bulkkopie van database naar Azure Data Explorer-sjabloon.](data-factory-template.md) Opmerking: **Instellingen** > **Mate van Parallelisme** in de kopieeractiviteit is niet relevant voor ADX. |
| **Complexiteit van gegevensverwerking** | Latentie is afhankelijk van de bronbestandsindeling, kolomtoewijzing en compressie.|
| **De VM die uw ingebruikmaaktijd voor integratie uitvoert** | <ul><li>Voor Azure-kopie kunnen ADF-VM's en machine-SKU's niet worden gewijzigd.</li><li> Voor on-prem naar Azure-kopie u bepalen dat de VM die uw zelfgehoste IR host, sterk genoeg is.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tips en veelvoorkomende valkuilen

### <a name="monitor-activity-progress"></a>Activiteitsvoortgang controleren

* Bij het bewaken van de voortgang van de activiteit kan de eigenschap *Gegevens geschreven* veel groter zijn dan de eigenschap *Gegevens lezen* omdat het lezen *van gegevens* wordt berekend op basis van de binaire bestandsgrootte, terwijl de *geschreven gegevens* worden berekend op basis van de grootte van het geheugen, nadat gegevens zijn gedeserialiseerd en gedecomprimeerd.

* Wanneer u de voortgang van de activiteit bijhoudt, u zien dat gegevens zijn geschreven naar de azure data explorer-sink. Wanneer u de tabel Azure Data Explorer opvraagt, ziet u dat er geen gegevens zijn aangekomen. Dit komt omdat er twee fasen zijn bij het kopiëren naar Azure Data Explorer. 
    * In de eerste fase worden de brongegevens gelezen, worden deze opgesplitst in brokken van 900 MB en wordt elk segment geüpload naar een Azure Blob. De eerste fase wordt gezien door de adf-activiteitsvoortgangsweergave. 
    * De tweede fase begint zodra alle gegevens zijn geüpload naar Azure Blobs. De Azure Data Explorer-engineknooppunten downloaden de blobs en nemen de gegevens in de gootsteentabel. De gegevens worden vervolgens gezien in de tabel Azure Data Explorer.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Niet inslikken CSV-bestanden als gevolg van onjuiste ontsnapping

Azure Data Explorer verwacht dat CSV-bestanden worden uitgelijnd met [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Zij verwacht:
* Velden die tekens bevatten die moeten ontsnappen (zoals " en nieuwe regels) moeten beginnen en eindigen met een **"** teken, zonder witruimte. Alle **"** personages *in* het veld worden ontsnapt door het gebruik van een dubbele **"** karakter (**""**). _'Hallo, ''Wereld',_ is bijvoorbeeld een geldig CSV-bestand met één record met één kolom of veld met de inhoud _Hello, 'World'._
* Alle records in het bestand moeten hetzelfde aantal kolommen en velden hebben.

Azure Data Factory staat het backslash (escape) teken toe. Als u een CSV-bestand genereert met een backslash-teken met Azure Data Factory, mislukt de opname van het bestand naar Azure Data Explorer.

#### <a name="example"></a>Voorbeeld

De volgende tekstwaarden: Hallo, "Wereld"<br/>
ABC DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

Moet verschijnen in een goede CSV-bestand als volgt: "Hallo, ""World"""<br/>
"ABC DEF"<br/>
"""ABC DEF"<br/>
"""ABC\D""EF"<br/>

Met het standaard escape-teken (backslash) werkt de volgende CSV niet \"met\"Azure Data Explorer: "Hallo, Wereld"<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Geneste JSON-objecten

Wanneer u een JSON-bestand kopieert naar Azure Data Explorer, moet u er rekening mee houden dat:
* Arrays worden niet ondersteund.
* Als uw JSON-structuur objectgegevenstypen bevat, wordt de onderliggende items van het object afgevlakt en wordt geprobeerd elk onderliggend item aan een andere kolom in uw tabel Azure Data Explorer toe te voegen. Als u wilt dat het volledige objectitem wordt toegewezen aan één kolom in Azure Data Explorer:
    * De hele JSON-rij innemen in één dynamische kolom in Azure Data Explorer.
    * Bewerk de pijplijndefinitie handmatig met de JSON-editor van Azure Data Factory. In **toewijzingen**
       * Verwijder de meerdere toewijzingen die voor elk onderliggend item zijn gemaakt en voeg één toewijzing toe die uw objecttype aan uw tabelkolom toebrengt.
       * Voeg na de afsluitende vierkante beugel een komma toe, gevolgd door:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Aanvullende eigenschappen opgeven bij het kopiëren naar Azure Data Explorer

> [!NOTE]
> Deze functie is momenteel beschikbaar door de JSON-payload handmatig te bewerken. 

Voeg als volgt één rij toe onder het gedeelte 'gootsteen' van de kopieeractiviteit:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Ontsnappen aan de waarde kan lastig zijn. Gebruik het volgende codefragment als referentie:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

De afgedrukte waarde:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [kopiëren van gegevens naar Azure Data Explorer met Azure Data Factory](data-factory-load-data.md).
* Meer informatie over het gebruik van [Azure Data Factory-sjabloon voor bulkkopiëren van database naar Azure Data Explorer](data-factory-template.md).
* Meer informatie over het gebruik van [azure data factory-opdrachtactiviteit om besturingselementopdrachten van Azure Data Explorer uit te voeren](data-factory-command-activity.md).
* Meer informatie over [Azure Data Explorer-query's](/azure/data-explorer/web-query-data) voor gegevensquery's.



