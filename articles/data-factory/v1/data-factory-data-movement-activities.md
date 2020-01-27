---
title: Gegevens verplaatsen met behulp van Kopieer activiteit
description: 'Meer informatie over het verplaatsen van gegevens in Data Factory pijp lijnen: gegevens migratie tussen Cloud archieven en tussen een on-premises Store en een Cloud archief. Kopieer activiteit gebruiken.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fbaa8c3544b35978786404619879f59ab91a6979
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931922"
---
# <a name="move-data-by-using-copy-activity"></a>Gegevens verplaatsen met behulp van Kopieer activiteit
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](data-factory-data-movement-activities.md)
> * [Versie 2 (huidige versie)](../copy-activity-overview.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [Kopieer activiteit in v2](../copy-activity-overview.md).

## <a name="overview"></a>Overzicht
In Azure Data Factory kunt u de Kopieer activiteit gebruiken om gegevens te kopiëren tussen on-premises en gegevens archieven in de Cloud. Nadat de gegevens worden gekopieerd, kan worden verder verwerkt en geanalyseerd. U kunt ook een Kopieeractiviteit gebruiken om transformatie en analyseresultaten voor business intelligence (BI) en het verbruik van de toepassing te publiceren.

![Rol van Kopieeractiviteit](media/data-factory-data-movement-activities/copy-activity.png)

De Kopieer activiteit wordt aangedreven door een beveiligde, betrouw bare, schaal bare en [wereld wijd beschik bare service](#global). Dit artikel bevat informatie over het verplaatsen van gegevens in Data Factory en de Kopieer activiteit.

Eerst laten we zien hoe gegevens migratie plaatsvindt tussen twee Cloud gegevensopslag en tussen een on-premises gegevens archief en een gegevens archief in de Cloud.

> [!NOTE]
> Zie informatie over [pijp lijnen en activiteiten](data-factory-create-pipelines.md)voor meer informatie over activiteiten in het algemeen.
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Gegevens tussen twee gegevens archieven in de Cloud kopiëren
Wanneer zowel de bron-als de Sink-gegevens opslag zich in de cloud bevinden, gaat de Kopieer activiteit door de volgende fasen om gegevens van de bron naar de sink te kopiëren. De service die wordt gebruikt door de Kopieeractiviteit:

1. Hiermee worden gegevens uit de brongegevens opslag gelezen.
2. Voert serialisatie/deserialisatie, compressie/decompressie, kolom toewijzing en type conversie uit. Dit gebeurt deze bewerkingen op basis van de configuraties van de invoergegevensset, de uitvoergegevensset en de Kopieeractiviteit.
3. Schrijft gegevens naar het doel gegevens archief.

De service kiest automatisch de optimale regio om de gegevens verplaatsing uit te voeren. Deze regio is doorgaans het dichtst bij de Sink-gegevens opslag.

![Kopie van Cloud naar Cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Gegevens kopiëren tussen een on-premises gegevens opslag en een gegevens archief in de Cloud
Als u gegevens veilig wilt verplaatsen tussen een on-premises gegevens opslag en een gegevens archief in de Cloud, installeert u Data Management Gateway op uw on-premises machine. Data Management Gateway is een agent die het verplaatsen en verwerken van hybride gegevens mogelijk maakt. U kunt deze op dezelfde computer installeren als het gegevens archief zelf of op een afzonderlijke computer die toegang heeft tot het gegevens archief.

In dit scenario voert Data Management Gateway de serialisatie/deserialisatie, compressie/decompressie, kolom toewijzing en type conversie uit. De gegevens worden niet door de Azure Data Factory-Service getransporteerd. In plaats daarvan schrijft Data Management Gateway de gegevens rechtstreeks naar het doel archief.

![Kopie van on-premises naar de Cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Zie [gegevens verplaatsen tussen on-premises en gegevens archieven in de Cloud](data-factory-move-data-between-onprem-and-cloud.md) voor een inleiding en een overzicht. Zie [Data Management Gateway](data-factory-data-management-gateway.md) voor gedetailleerde informatie over deze agent.

U kunt ook gegevens verplaatsen van/naar ondersteunde gegevens archieven die worden gehost op Azure IaaS virtual machines (Vm's) met behulp van Data Management Gateway. In dit geval kunt u Data Management Gateway op dezelfde VM installeren als het gegevens archief zelf of op een afzonderlijke virtuele machine die toegang heeft tot het gegevens archief.

## <a name="supported-data-stores-and-formats"></a>Ondersteunde gegevensarchieven en indelingen
De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Data Factory ondersteunt de volgende gegevensarchieven. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Klik op een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief.

> [!NOTE] 
> Als u gegevens wilt verplaatsen naar/van een gegevensarchief dat niet wordt ondersteund door de kopieeractiviteit, gebruikt u een **aangepaste activiteit** in Data Factory met uw eigen logica voor het kopiëren/verplaatsen van gegevens. Zie [Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn](data-factory-use-custom-activities.md) voor meer informatie over het maken en gebruiken van een aangepaste activiteit.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevensarchieven met een * kunnen zich on-premises of op Azure IaaS bevinden. Hiervoor moet u [Data Management Gateway](data-factory-data-management-gateway.md) installeren op een on-premises/Azure IaaS-computer.

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen
U kunt Kopieer activiteit gebruiken om **bestanden te kopiëren** tussen twee bestanden op basis van een bestand, u kunt de [sectie opmaak](data-factory-create-datasets.md) in zowel de definitie van de invoer-als uitvoer gegevensset overs Laan. De gegevens worden efficiënt gekopieerd zonder serialisatie/deserialisatie.

Kopieer activiteit leest ook van en schrijft naar bestanden in de opgegeven indelingen: **Text, JSON, AVRO, Orc en Parquet**, en de compressie **-codec gzip, Deflate, bzip2 en ZipDeflate** worden ondersteund. Zie [ondersteunde indelingen voor bestanden en compressie](data-factory-supported-file-and-compression-formats.md) met details.

U kunt bijvoorbeeld de volgende kopieeractiviteiten doen:

* Kopiëren van gegevens in on-premises SQL Server en schrijven naar Azure Data Lake Store in ORC-indeling.
* Bestanden kopiëren in tekstindeling (CSV) van on-premises bestandssysteem en naar Azure-Blob in Avro-indeling wilt schrijven.
* ZIP-bestanden kopiëren van on-premises bestandssysteem en decomprimeren vervolgens land Azure Data Lake Store.
* Kopiëren van gegevens in de indeling van GZip gecomprimeerde tekstbestand (CSV) van Azure Blob en schrijven naar Azure SQL Database.

## <a name="global"></a>Wereld wijd beschik bare gegevens verplaatsing
Azure Data Factory is alleen beschikbaar in de regio's VS-Oost, VS-West en Europa-noord. De service die de Kopieer activiteit toestuurt, is echter algemeen beschikbaar in de volgende regio's en in de geografische gebieden. De algemeen beschikbare topologie zorgt u ervoor efficiënt gegevensverplaatsing die meestal regio-overschrijdende hops voorkomt. Zie [-Services per regio](https://azure.microsoft.com/regions/#services) voor beschikbaarheid van Data Factory en de verplaatsing van gegevens in een regio.

### <a name="copy-data-between-cloud-data-stores"></a>Gegevens kopiëren tussen gegevens archieven in de Cloud
Wanneer zowel de bron-als de Sink-gegevens opslag zich in de cloud bevinden, gebruikt Data Factory een service-implementatie in de regio die het dichtst bij de Sink ligt die zich in dezelfde geografie bevindt om de gegevens te verplaatsen. Raadpleeg de volgende tabel voor toewijzing:

| Geografie van de doel gegevens archieven | Regio van het doel gegevens archief | Regio die wordt gebruikt voor gegevens verplaatsing |
|:--- |:--- |:--- |
| Verenigde Staten | VS - oost | VS - oost |
| &nbsp; | VS - oost 2 | VS - oost 2 |
| &nbsp; | US - centraal | US - centraal |
| &nbsp; | US - noord-centraal | US - noord-centraal |
| &nbsp; | US - zuid-centraal | US - zuid-centraal |
| &nbsp; | US - west-centraal | US - west-centraal |
| &nbsp; | US - west | US - west |
| &nbsp; | US - west 2 | US - west 2 |
| Canada | Canada-Oost | Canada-Midden |
| &nbsp; | Canada-Midden | Canada-Midden |
| Brazilië | Brazilië - Zuid | Brazilië - Zuid |
| Europa | Europa - noord | Europa - noord |
| &nbsp; | Europa - west | Europa - west |
| Verenigd Koninkrijk | UK - west | UK - zuid |
| &nbsp; | UK - zuid | UK - zuid |
| Azië en Stille Oceaan | Azië - zuidoost | Azië - zuidoost |
| &nbsp; | Azië - oost | Azië - zuidoost |
| Australië | Australië - oost | Australië - oost |
| &nbsp; | Australië - zuidoost | Australië - zuidoost |
| India | India - centraal | India - centraal |
| &nbsp; | India - west | India - centraal |
| &nbsp; | India - zuid | India - centraal |
| Japan | Japan - Oost | Japan - Oost |
| &nbsp; | Japan - West | Japan - Oost |
| Korea | Korea - centraal | Korea - centraal |
| &nbsp; | Korea - zuid | Korea - centraal |

U kunt de regio van Data Factory service die moet worden gebruikt voor het uitvoeren van de kopie, ook expliciet opgeven door `executionLocation` eigenschap onder Copy activity `typeProperties`op te geven. Ondersteunde waarden voor deze eigenschap worden weer gegeven in de bovenstaande **regio die wordt gebruikt voor de kolom gegevens verplaatsing** . Houd er rekening mee dat uw gegevens tijdens het kopiëren over de kabel heen gaan. Als u bijvoorbeeld wilt kopiëren tussen Azure-winkels in Korea, kunt u `"executionLocation": "Japan East"` opgeven om te routeren via een Japanse regio (Zie voor [beeld van JSON](#by-using-json-scripts) als referentie).

> [!NOTE]
> Als de regio van het doel gegevens archief zich niet in de voor gaande lijst bevindt of niet detecteerbaar is, mislukt de standaard Kopieer activiteit in plaats van een alternatieve regio, tenzij `executionLocation` is opgegeven. De lijst met ondersteunde regio's wordt na verloop van tijd uitgebreid.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Gegevens kopiëren tussen een on-premises gegevens opslag en een gegevens archief in de Cloud
Wanneer gegevens worden gekopieerd tussen on-premises (of virtuele Azure-machines/IaaS) en Cloud archieven, voert [Data Management Gateway](data-factory-data-management-gateway.md) gegevens verplaatsing uit op een on-premises machine of virtuele machine. De gegevens stroomt niet door de service in de Cloud, tenzij u de mogelijkheid tot [gefaseerd kopiëren](data-factory-copy-activity-performance.md#staged-copy) gebruikt. In dit geval stroomt gegevens via de staging Azure Blob-opslag voordat deze wordt geschreven in de Sink-gegevens opslag.

## <a name="create-a-pipeline-with-copy-activity"></a>Een pijp lijn maken met de Kopieer activiteit
U kunt op verschillende manieren een pijp lijn maken met de Kopieer activiteit:

### <a name="by-using-the-copy-wizard"></a>Met de wizard kopiëren
De wizard Data Factory kopiëren helpt u bij het maken van een pijp lijn met Kopieer activiteit. Met deze pijp lijn kunt u gegevens van ondersteunde bronnen naar bestemmingen kopiëren *zonder* dat u JSON-definities voor gekoppelde services, gegevens sets en pijp lijnen hoeft te schrijven. Zie [Data Factory wizard kopiëren](data-factory-copy-wizard.md) voor meer informatie over de wizard.  

### <a name="by-using-json-scripts"></a>Met behulp van JSON-scripts
U kunt Data Factory editor gebruiken in Visual Studio of Azure PowerShell om een JSON-definitie voor een pijp lijn te maken (met behulp van Kopieer activiteit). Vervolgens kunt u de app implementeren om de pijp lijn te maken in Data Factory. Zie [zelf studie: Kopieer activiteit gebruiken in een Azure Data Factory pijp lijn](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor een zelf studie met stapsgewijze instructies.    

JSON-eigenschappen (zoals naam, beschrijving, invoer-en uitvoer tabellen en beleids regels) zijn beschikbaar voor alle typen activiteiten. Eigenschappen die beschikbaar zijn in het gedeelte `typeProperties` van de activiteit verschillen per type activiteit.

Voor kopieer activiteit is de sectie `typeProperties` afhankelijk van de typen bronnen en Sinks. Klik op een bron/sink in de sectie [ondersteunde bronnen en sinks](#supported-data-stores-and-formats) voor meer informatie over type-eigenschappen die door de Kopieer activiteit worden ondersteund voor die gegevens opslag.

Hier volgt een voor beeld van een JSON-definitie:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
De planning die in de uitvoer gegevensset is gedefinieerd, bepaalt wanneer de activiteit wordt uitgevoerd (bijvoorbeeld **dagelijks**, frequentie als **dag**en interval als **1**). Met de activiteit worden gegevens gekopieerd van een invoer-gegevensset (**bron**) naar een uitvoer gegevensset (**sink**).

U kunt meer dan één invoer gegevensset opgeven om de activiteit te kopiëren. Ze worden gebruikt om de afhankelijkheden te controleren voordat de activiteit wordt uitgevoerd. Alleen de gegevens van de eerste gegevensset worden echter gekopieerd naar de doel-DataSet. Zie [planning en uitvoering](data-factory-scheduling-and-execution.md)voor meer informatie.  

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie de [Kopieeractiviteit prestatie- en afstemmingshandleiding](data-factory-copy-activity-performance.md), waarin belangrijke factoren die invloed hebben op de prestaties van de verplaatsing van gegevens (Kopieeractiviteit) in Azure Data Factory wordt beschreven. Ook geeft een lijst van de waargenomen prestaties tijdens interne tests van en worden verschillende manieren om te optimaliseren de prestaties van Kopieeractiviteit besproken.

## <a name="fault-tolerance"></a>Fouttolerantie
De Kopieer activiteit stopt standaard met het kopiëren van gegevens en retourneert een fout wanneer er incompatibele gegevens tussen de bron en de Sink optreden. u kunt expliciet configureren om de niet-compatibele rijen over te slaan en te registreren en alleen die compatibele gegevens kopiëren om het kopiëren te laten verlopen. Zie de [Kopieeractiviteit fouttolerantie](data-factory-copy-activity-fault-tolerance.md) op meer informatie.

## <a name="security-considerations"></a>Beveiligingsoverwegingen
Zie de [beveiligings overwegingen](data-factory-data-movement-security-considerations.md)die de beveiligings infrastructuur beschrijft die services voor gegevens verplaatsing in azure Data Factory gebruiken om uw gegevens te beveiligen.

## <a name="scheduling-and-sequential-copy"></a>Planning en sequentiële kopie
Zie [planning en uitvoering](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie over de werking van planning en uitvoering in Data Factory. Het is mogelijk om meerdere Kopieer bewerkingen achter elkaar op een sequentieve of bestelde manier uit te voeren. Zie de sectie [opeenvolgend kopiëren](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) .

## <a name="type-conversions"></a>Typeconversies
Verschillende gegevens archieven hebben verschillende systeem eigen type systemen. Met de Kopieer activiteit worden automatische type conversies van bron typen naar Sink-typen uitgevoerd met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar een .NET-type.
2. Converteren van een .NET-type naar een systeem eigen Sink-type.

De toewijzing van een systeem systeem eigen type aan een .NET-type voor een gegevens archief bevindt zich in het desbetreffende artikel van het gegevens archief. (Klik op de specifieke koppeling in de tabel ondersteunde gegevens archieven). U kunt deze toewijzingen gebruiken om de juiste typen te bepalen tijdens het maken van uw tabellen, zodat de Kopieer activiteit de juiste conversies uitvoert.

## <a name="next-steps"></a>Volgende stappen
* Zie [gegevens kopiëren van Azure Blob-opslag naar Azure SQL database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor meer informatie over de Kopieer activiteit.
* Zie voor meer informatie over het verplaatsen van gegevens van een on-premises gegevens opslag naar een gegevens archief in de Cloud [gegevens verplaatsen van on-premises naar gegevens](data-factory-move-data-between-onprem-and-cloud.md)opslag in de Cloud.
