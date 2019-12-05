---
title: Integratie van Azure Data Explorer met Azure Data Factory
description: In dit onderwerp integreert u Azure Data Explorer met Azure Data Factory om de activiteiten Copy, lookup en Command te gebruiken
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 51683e529f832e06efbe8eb71466f3b27d95fcb1
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819136"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Azure Data Explorer integreren met Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) is een service voor gegevens integratie in de Cloud waarmee u verschillende gegevens archieven kunt integreren en activiteiten op de gegevens moet uitvoeren. Met ADF kunt u gegevensgestuurde werk stromen maken voor het organiseren en automatiseren van gegevens verplaatsing en gegevens transformatie. Azure Data Explorer is een van de [ondersteunde gegevens archieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory activiteiten voor Azure Data Explorer

Er zijn verschillende integraties met Azure Data Factory beschikbaar voor Azure Data Explorer-gebruikers:

### <a name="copy-activity"></a>Kopieeractiviteit
 
Azure Data Factory Kopieer activiteit wordt gebruikt om gegevens over te dragen tussen gegevens archieven. Azure Data Explorer wordt ondersteund als een bron, waarbij gegevens worden gekopieerd van Azure Data Explorer naar elk ondersteund gegevens archief en een sink, waarbij gegevens worden gekopieerd uit een ondersteund gegevens archief naar Azure Data Explorer. Zie [gegevens kopiëren naar of van Azure Data Explorer met behulp van Azure Data Factory](/azure/data-factory/connector-azure-data-explorer)voor meer informatie. en voor een gedetailleerde stapsgewijze Zie [laad gegevens van Azure Data Factory in Azure Data Explorer](data-factory-load-data.md).
Azure Data Explorer wordt ondersteund door Azure IR (Integration Runtime), gebruikt wanneer gegevens worden gekopieerd binnen Azure en zelf-hostende IR, die wordt gebruikt bij het kopiëren van gegevens van/naar gegevens archieven op locatie of in een netwerk met toegangs beheer, zoals een Azure-Virtual Network. Zie voor meer informatie [welke IR u moet gebruiken](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Wanneer u de Kopieer activiteit gebruikt en een **gekoppelde service** of een **gegevensset**maakt, selecteert u de gegevens opslag **Azure Data Explorer (Kusto)** en niet de oude **Kusto**van het gegevens archief.  

### <a name="lookup-activity"></a>Opzoek activiteit
 
De opzoek activiteit wordt gebruikt voor het uitvoeren van query's op Azure Data Explorer. Het resultaat van de query wordt geretourneerd als de uitvoer van de lookup-activiteit en kan worden gebruikt in de volgende activiteit in de pijp lijn zoals beschreven in de [documentatie bij ADF-Zoek opdrachten](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Naast de limiet voor de antwoord grootte van 5.000 rijen en 2 MB, heeft de activiteit ook een limiet voor de querytime-out van 1 uur.

### <a name="command-activity"></a>Opdracht activiteit

Met de opdracht activiteit kan de Azure Data Explorer- [besturings opdrachten](/azure/kusto/concepts/#control-commands)worden uitgevoerd. In tegens telling tot query's kunnen de besturings opdrachten gegevens of meta gegevens wijzigen. Sommige besturings opdrachten zijn bedoeld om gegevens op te nemen in azure Data Explorer, met behulp van opdrachten als `.ingest`of `.set-or-append`) of door gegevens te kopiëren van Azure-Data Explorer naar externe gegevens archieven met behulp van opdrachten als `.export`.
Zie [Azure Data Factory opdracht activiteit gebruiken om Azure Data Explorer-besturings opdrachten uit te voeren](data-factory-command-activity.md)voor een gedetailleerde uitleg van de opdracht activiteit.  Het gebruik van een besturings opdracht om gegevens te kopiëren kan op elk moment een snellere en goedkopere optie zijn dan de Kopieer activiteit. Zie [selecteren tussen kopiëren en opdracht activiteiten bij het kopiëren van gegevens](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)om te bepalen wanneer u de opdracht activiteit wilt gebruiken in plaats van de Kopieer activiteit.

### <a name="copy-in-bulk-from-a-database-template"></a>Bulksgewijs kopiëren uit een database sjabloon

De [kopie in bulk van een Data Base naar Azure Data Explorer met behulp van de Azure Data Factory sjabloon](data-factory-template.md) is een vooraf gedefinieerde Azure Data Factory pijp lijn. De sjabloon wordt gebruikt om een groot aantal pijp lijnen per data base of per tabel te maken voor het sneller kopiëren van gegevens. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Selecteer tussen Kopieer-en Azure Data Explorer-opdracht activiteiten wanneer u gegevens kopieert 

Deze sectie helpt u bij het selecteren van de juiste activiteit voor het kopiëren van uw gegevens.

Bij het kopiëren van gegevens van of naar Azure Data Explorer zijn er twee beschik bare opties in Azure Data Factory:
* Kopieer activiteit.
* Azure Data Explorer-opdracht activiteit, waarmee een van de besturings opdrachten wordt uitgevoerd waarmee gegevens worden overgebracht in azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Gegevens kopiëren van Azure Data Explorer
  
U kunt gegevens uit Azure Data Explorer kopiëren met behulp van de Kopieer activiteit of de [`.export`](/azure/kusto/management/data-export/) opdracht. De `.export` opdracht voert een query uit en exporteert vervolgens de resultaten van de query. 

Raadpleeg de volgende tabel voor een vergelijking van de Kopieer activiteit en `.export` opdracht voor het kopiëren van gegevens uit Azure Data Explorer.

| | Kopieeractiviteit | . de opdracht exporteren |
|---|---|---|
| **Stroom beschrijving** | ADF voert een query uit op Kusto, verwerkt het resultaat en verzendt het naar het doel gegevens archief. <br>(**ADX > ADF > Sink-gegevens archief**) | ADF verzendt een `.export` controle opdracht naar Azure Data Explorer, waarmee de opdracht wordt uitgevoerd en de gegevens rechtstreeks naar de doel gegevens opslag worden verzonden. <br>(**ADX > Sink-gegevens opslag**) |
| **Ondersteunde doel gegevens archieven** | Een groot aantal [ondersteunde gegevens archieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure-Blob, SQL Database |
| **Prestaties** | Gecentraliseerde | <ul><li>Gedistribueerd (standaard), gegevens van meerdere knoop punten gelijktijdig exporteren</li><li>Sneller en COGS efficiënt.</li></ul> |
| **Server limieten** | [Query limieten](/azure/kusto/concepts/querylimits) kunnen worden uitgebreid/uitgeschakeld. ADF-query's bevatten standaard: <ul><li>De maximale grootte van 500.000 records of 64 MB.</li><li>Tijds limiet van 10 minuten.</li><li>`noTruncation` ingesteld op false.</li></ul> | De query limieten worden standaard uitgebreid of uitgeschakeld: <ul><li>Grootte limieten zijn uitgeschakeld.</li><li>De time-out van de server is verlengd tot 1 uur.</li><li>`MaxMemoryConsumptionPerIterator` en `MaxMemoryConsumptionPerQueryPerNode` zijn uitgebreid tot Maxi maal (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Als uw Kopieer doel een van de gegevens archieven is die worden ondersteund door de `.export`-opdracht en als geen van de functies van de Kopieer activiteit cruciaal is voor uw behoeften, selecteert u de `.export` opdracht.

### <a name="copying-data-to-azure-data-explorer"></a>Gegevens kopiëren naar Azure Data Explorer

U kunt gegevens naar Azure Data Explorer kopiëren met behulp van de kopieer-of opname opdrachten, zoals [opname van query's](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`en opnemen [vanuit Storage](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

Raadpleeg de volgende tabel voor een vergelijking van de Kopieer activiteit en opname opdrachten voor het kopiëren van gegevens naar Azure Data Explorer.

| | Kopieeractiviteit | Opnemen uit query<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Opnemen uit opslag <br> `.ingest` |
|---|---|---|---|
| **Stroom beschrijving** | ADF haalt de gegevens op uit de brongegevens opslag, converteert deze naar een tabellaire indeling en voert de vereiste schema toewijzing wijzigingen door. ADF laadt vervolgens de gegevens naar Azure-blobs, splitst deze op in segmenten en downloadt vervolgens de blobs om deze op te nemen in de tabel ADX. <br> (**Brongegevens opslag > ADF > Azure-blobs > ADX**) | Met deze opdrachten kan een query of een `.show` opdracht worden uitgevoerd en worden de resultaten van de query opgenomen in een tabel (**ADX > ADX**). | Met deze opdracht worden gegevens opgenomen in een tabel door de gegevens van een of meer opslag artefacten in de Cloud op te halen. |
| **Ondersteunde gegevens archieven voor bronnen** |  [verschillende opties](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS gen 2, Azure Blob, SQL (met behulp van de sql_request-invoeg toepassing), Cosmos (met behulp van de cosmosdb_sql_request-invoeg toepassing) en een ander gegevens archief dat HTTP-of python-Api's levert. | File System, Azure Blob Storage, ADLS gen 1, ADLS gen 2 |
| **Prestaties** | Opname is in de wachtrij geplaatst en beheerd, waardoor kleine opname mogelijkheden worden gegarandeerd en de maximale Beschik baarheid wordt gegarandeerd door taak verdeling, nieuwe pogingen en fout afhandeling te bieden. | <ul><li>Deze opdrachten zijn niet bedoeld voor het importeren van grote hoeveel heden gegevens.</li><li>Werkt zoals verwacht en goed koper. Maar voor productie scenario's en wanneer de verkeers frequentie en de grootte van de gegevens groot zijn, gebruikt u de Kopieer activiteit.</li></ul> |
| **Server limieten** | <ul><li>Geen maximale grootte.</li><li>Maximale time-outlimiet: 1 uur per opgenomen blob. |<ul><li>Er is slechts een maximale grootte voor het query onderdeel, dat kan worden overgeslagen door `noTruncation=true`op te geven.</li><li>Maximale time-outlimiet: 1 uur.</li></ul> | <ul><li>Geen maximale grootte.</li><li>Maximale time-outlimiet: 1 uur.</li></ul>|

> [!TIP]
> * Bij het kopiëren van gegevens van ADF naar Azure Data Explorer de `ingest from query`-opdrachten gebruiken.  
> * Gebruik voor grote gegevens sets (> 1 GB) de Kopieer activiteit.  

## <a name="required-permissions"></a>Vereiste machtigingen

De volgende tabel geeft een lijst van de vereiste machtigingen voor verschillende stappen in de integratie met Azure Data Factory.

| Stap | Bewerking | Mini maal machtigings niveau | Opmerkingen |
|---|---|---|---|
| **Een gekoppelde service maken** | Database navigatie | *Database Viewer* <br>De aangemelde gebruiker die gebruikmaakt van ADF moet gemachtigd zijn om meta gegevens van de data base te lezen. | De gebruiker kan de database naam hand matig opgeven. |
| | Verbinding testen | *database monitor* of *tabel opname* <br>De Service-Principal moet worden gemachtigd om database niveau `.show` opdrachten of opname op tabel niveau uit te voeren. | <ul><li>TestConnection controleert de verbinding met het cluster en niet naar de-data base. Dit kan zelfs lukken als de data base niet bestaat.</li><li>De machtigingen voor de tabel beheerder zijn niet voldoende.</li></ul>|
| **Een gegevensset maken** | Tabel navigatie | *database monitor* <br>De aangemelde gebruiker die gebruikmaakt van ADF, moet gemachtigd zijn om `.show`-opdrachten op database niveau uit te voeren. | De gebruiker kan de tabel naam hand matig opgeven.|
| **Een gegevensset** of **Kopieer activiteit** maken | Preview-gegevens | *Database Viewer* <br>De Service-Principal moet worden gemachtigd om meta gegevens van de data base te lezen. | | 
|   | Schema importeren | *Database Viewer* <br>De Service-Principal moet worden gemachtigd om meta gegevens van de data base te lezen. | Wanneer ADX de bron is van een kopie in tabel vorm-naar-tabel, wordt het schema automatisch geïmporteerd door ADF, zelfs als de gebruiker het schema niet expliciet heeft geïmporteerd. |
| **ADX als Sink** | Een kolom toewijzing met een naam maken | *database monitor* <br>De Service-Principal moet worden gemachtigd om `.show` opdrachten op database niveau uit te voeren. | <ul><li>Alle verplichte bewerkingen werken met een *tabel opname*.</li><li> Sommige optionele bewerkingen kunnen mislukken.</li></ul> |
|   | <ul><li>Een CSV-toewijzing maken voor de tabel</li><li>Toewijzing verwijderen</li></ul>| *tabel opname* of *database beheerder* <br>De Service-Principal moet worden gemachtigd om wijzigingen aan te brengen in een tabel. | |
|   | Gegevens opnemen | *tabel opname* of *database beheerder* <br>De Service-Principal moet worden gemachtigd om wijzigingen aan te brengen in een tabel. | | 
| **ADX als bron** | Query uitvoeren | *Database Viewer* <br>De Service-Principal moet worden gemachtigd om meta gegevens van de data base te lezen. | |
| **Opdracht Kusto** | | Op basis van het machtigings niveau van elke opdracht. |

## <a name="performance"></a>Prestaties 

Als Azure Data Explorer de bron is en u de opzoek-, kopieer-of opdracht activiteit gebruikt die een query bevat, raadpleegt u de [Aanbevolen procedures](/azure/kusto/query/best-practices) voor het uitvoeren van query's en de [ADF-documentatie voor kopieer activiteiten](/azure/data-factory/copy-activity-performance).
  
In deze sectie wordt het gebruik van de Kopieer activiteit opgelost, waarbij Azure Data Explorer de sink is. De geschatte door Voer voor Azure Data Explorer sink is 11-13 MBps. De volgende tabel bevat informatie over de para meters die de prestaties van de Azure Data Explorer Sink beïnvloeden.

| Parameter | Opmerkingen |
|---|---|
| **Onderdelen geografische nabijheid** | Plaats alle onderdelen in dezelfde regio:<ul><li>Bron-en Sink-gegevens opslag.</li><li>ADF Integration runtime.</li><li>Uw ADX-cluster.</li></ul>Zorg ervoor dat ten minste uw Integration runtime zich in dezelfde regio bevindt als uw ADX-cluster. |
| **Aantal DIUs** | 1 VM voor elke 4 DIUs die wordt gebruikt door ADF. <br>Het verhogen van de DIUs is alleen nuttig als uw bron een archief op basis van bestanden met meerdere bestanden is. Elke virtuele machine verwerkt vervolgens een ander bestand parallel. Het kopiëren van één groot bestand heeft daarom een hogere latentie dan het kopiëren van meerdere kleinere bestanden.|
|**Hoeveelheid en SKU van uw ADX-cluster** | Een groot aantal ADX-knoop punten verhoogt de verwerkings tijd voor opname.|
| **Parallelle uitvoering** | Als u een zeer grote hoeveelheid gegevens uit een Data Base wilt kopiëren, moet u uw gegevens partitioneren en vervolgens een ForEach-lus gebruiken waarmee elke partitie parallel wordt gekopieerd of de [bulk kopie van de Data Base naar Azure Data Explorer sjabloon](data-factory-template.md)gebruiken. Opmerking: **instellingen** > **mate van parallellisme** in de Kopieer activiteit zijn niet relevant voor ADX. |
| **Complexiteit van gegevens verwerking** | De latentie varieert afhankelijk van de bron bestands indeling, de kolom toewijzing en de compressie.|
| **De virtuele machine waarop uw Integration runtime wordt uitgevoerd** | <ul><li>Voor Azure copy kunnen ADF-Vm's en machine-Sku's niet worden gewijzigd.</li><li> Bepaal voor on-premises naar Azure kopiëren of de virtuele machine die als host fungeert voor uw zelf-hostende IR krachtig genoeg is.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tips en algemene Valk uilen

### <a name="monitor-activity-progress"></a>Voortgang van activiteit bewaken

* Bij het bewaken van de voortgang van de activiteit kan de eigenschap voor het *schrijven van gegevens* veel groter zijn dan de eigenschap voor het *lezen* van gegevens omdat de gegevens die worden *gelezen* , worden berekend volgens de binaire bestands grootte, terwijl gegevens die zijn *geschreven* , worden berekend op basis van de grootte in het geheugen, nadat de gegevens zijn gedeserialiseerd en gedecomprimeerd.

* Wanneer u de voortgang van de activiteit bewaken, kunt u zien dat de gegevens naar de Azure Data Explorer-Sink zijn geschreven. Wanneer u een query uitvoert op de Azure Data Explorer-tabel, ziet u dat er geen gegevens zijn aangekomen. Dit komt doordat er twee fasen zijn bij het kopiëren naar Azure Data Explorer. 
    * In eerste instantie worden de bron gegevens gelezen, gesplitst naar segmenten van 900 MB en wordt elk segment geüpload naar een Azure-Blob. De eerste fase wordt gezien door de voortgangs weergave van de ADF-activiteit. 
    * De tweede fase begint zodra alle gegevens zijn geüpload naar Azure-blobs. De knoop punten van de Azure Data Explorer-engine downloaden de blobs en nemen de gegevens op in de Sink-tabel. De gegevens worden vervolgens weer gegeven in de Azure Data Explorer-tabel.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Fout bij het opnemen van CSV-bestanden vanwege onjuiste aanhalings tekens

Azure Data Explorer verwacht dat CSV-bestanden worden uitgelijnd met [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Verwacht:
* Velden die tekens bevatten die moeten worden opgenomen in een escape-teken (zoals ' en nieuwe regels), moeten beginnen en eindigen met een **' character '** zonder spaties. Alle **"** tekens *in* het veld worden voorafgegaan door **een dubbel teken** ( **" "** ). Bijvoorbeeld _' Hallo ', ' wereld_ ' ' is een geldig CSV-bestand met één record met één kolom of veld met de inhoud _Hello, World_.
* Alle records in het bestand moeten hetzelfde aantal kolommen en velden bevatten.

Azure Data Factory staat het back slash-teken (Escape) toe. Als u een CSV-bestand met een back slash-teken genereert met behulp van Azure Data Factory, mislukt het opnemen van het bestand naar Azure Data Explorer.

#### <a name="example"></a>Voorbeeld

De volgende tekst waarden: Hallo, wereld<br/>
ABC-DEF<br/>
"ABC\D" EF<br/>
"ABC-DEF<br/>

Moet worden weer gegeven in een correct CSV-bestand als volgt: "Hallo," "wereld" ""<br/>
"ABC-DEF"<br/>
"" "ABC-DEF"<br/>
"" "ABC\D" "EF"<br/>

Met behulp van het standaard escape teken (back slash) werkt de volgende CSV niet met Azure Data Explorer: "Hallo, \"World\""<br/>
"ABC-DEF"<br/>
"\"ABC-DEF."<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Geneste JSON-objecten

Houd rekening met het volgende wanneer u een JSON-bestand naar Azure Data Explorer kopieert:
* Matrices worden niet ondersteund.
* Als uw JSON-structuur object gegevens typen bevat, worden de onderliggende items van het object door Azure Data Factory afgevlakt en wordt geprobeerd om elk onderliggend item toe te wijzen aan een andere kolom in uw Azure Data Explorer-tabel. Als u wilt dat het volledige object item wordt toegewezen aan één kolom in azure Data Explorer:
    * De volledige JSON-rij opnemen in een enkele dynamische kolom in azure Data Explorer.
    * Bewerk de pijplijn definitie hand matig met behulp van de JSON-editor van Azure Data Factory. In **toewijzingen**
       * Verwijder de meerdere toewijzingen die zijn gemaakt voor elk onderliggend item en voeg één toewijzing toe waarmee u uw object type kunt toewijzen aan de tabel kolom.
       * Voeg na het sluiten vier kant haakje een komma toe, gevolgd door:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>AdditionalProperties opgeven bij het kopiëren naar Azure Data Explorer

> [!NOTE]
> Deze functie is momenteel beschikbaar door de JSON-Payload hand matig te bewerken. 

Voeg als volgt één rij toe onder het gedeelte ' sink ' van de Kopieer activiteit:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Het maken van een Escape van de waarde kan lastig zijn. Gebruik het volgende code fragment als referentie:

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

* Meer informatie over het [kopiëren van gegevens naar Azure Data Explorer met behulp van Azure Data Factory](data-factory-load-data.md).
* Meer informatie over [het gebruik van Azure Data Factory sjabloon voor bulksgewijs kopiëren van de Data Base naar Azure Data Explorer](data-factory-template.md).
* Meer informatie over [het gebruik van Azure Data Factory opdracht activiteit voor het uitvoeren van Azure Data Explorer-besturings opdrachten](data-factory-command-activity.md).
* Meer informatie over [Azure Data Explorer query's](/azure/data-explorer/web-query-data) voor het uitvoeren van gegevens query's.



