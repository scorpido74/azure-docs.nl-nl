---
title: Gegevens verplaatsen met Kopieeractiviteit
description: 'Meer informatie over gegevensverplaatsing in Data Factory-pijplijnen: gegevensmigratie tussen cloudstores en tussen een on-premises winkel en een cloudwinkel. Kopieeractiviteit gebruiken.'
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281884"
---
# <a name="move-data-by-using-copy-activity"></a>Gegevens verplaatsen met Kopieeractiviteit
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-data-movement-activities.md)
> * [Versie 2 (huidige versie)](../copy-activity-overview.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Activiteit kopiëren in V2](../copy-activity-overview.md)als u de huidige versie van de service Gegevensfabriek gebruikt.

## <a name="overview"></a>Overzicht
In Azure Data Factory u Activiteit kopiëren gebruiken om gegevens te kopiëren tussen on-premises en cloudgegevensopslag. Nadat de gegevens zijn gekopieerd, kan deze verder worden getransformeerd en geanalyseerd. U kopieeractiviteit ook gebruiken om transformatie- en analyseresultaten te publiceren voor business intelligence (BI) en toepassingsverbruik.

![Rol van kopieeractiviteit](media/data-factory-data-movement-activities/copy-activity.png)

Copy Activity wordt aangedreven door een veilige, betrouwbare, schaalbare en [wereldwijd beschikbare service.](#global) In dit artikel vindt u informatie over gegevensverplaatsing in Data Factory en Kopieeractiviteit.

Laten we eerst eens kijken hoe gegevensmigratie plaatsvindt tussen twee cloudgegevensopslag en tussen een on-premises gegevensopslag en een cloudgegevensarchief.

> [!NOTE]
> Zie [Pijplijnen en activiteiten begrijpen](data-factory-create-pipelines.md)voor meer informatie over activiteiten in het algemeen.
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Gegevens kopiëren tussen twee cloudgegevensopslag
Wanneer zowel bron- als sinkdatastores zich in de cloud bevinden, gaat Kopieeractiviteit door de volgende fasen om gegevens van de bron naar de gootsteen te kopiëren. De service die kopieeractiviteit aandrijft:

1. Hiermee leest u gegevens uit het brongegevensarchief.
2. Hiermee wordt serialisatie/deserialisatie, compressie/decompressie, kolomtoewijzing en typeconversie uitgevoerd. Deze bewerkingen worden uitgevoerd op basis van de configuraties van de invoergegevensset, uitvoergegevensset en Kopieeractiviteit.
3. Schrijft gegevens naar het doelgegevensarchief.

De service kiest automatisch de optimale regio om de gegevensbeweging uit te voeren. Deze regio is meestal degene die het dichtst bij de gootsteen gegevens op te slaan.

![Cloud-to-cloud-kopie](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Gegevens kopiëren tussen een on-premises gegevensopslag en een cloudgegevensarchief
Als u gegevens veilig wilt verplaatsen tussen een on-premises gegevensopslag en een cloudgegevensarchief, installeert u Data Management Gateway op uw on-premises machine. Data Management Gateway is een agent die hybride gegevensverkeer en -verwerking mogelijk maakt. U het installeren op dezelfde machine als het gegevensarchief zelf, of op een aparte machine die toegang heeft tot het gegevensarchief.

In dit scenario voert Data Management Gateway de serialisatie/deserialisatie, compressie/decompressie, kolomtoewijzing en typeconversie uit. Gegevens worden niet door de Azure Data Factory-service gestroomd. In plaats daarvan schrijft Data Management Gateway de gegevens rechtstreeks naar het doelarchief.

![On-premises-naar-cloud-kopie](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Zie [Gegevens verplaatsen tussen on-premises en cloudgegevenswinkels](data-factory-move-data-between-onprem-and-cloud.md) voor een introductie en walkthrough. Zie [Data Management Gateway](data-factory-data-management-gateway.md) voor gedetailleerde informatie over deze agent.

U gegevens ook verplaatsen van/naar ondersteunde gegevensopslag die worden gehost op virtuele Azure-machines (VM's) met behulp van Data Management Gateway. In dit geval u Data Management Gateway installeren op dezelfde VM als het gegevensarchief zelf, of op een aparte VM die toegang heeft tot het gegevensarchief.

## <a name="supported-data-stores-and-formats"></a>Ondersteunde gegevensarchieven en -indelingen
De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Data Factory ondersteunt de volgende gegevensarchieven. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Klik op een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief.

> [!NOTE] 
> Als u gegevens wilt verplaatsen naar/van een gegevensarchief dat niet wordt ondersteund door de kopieeractiviteit, gebruikt u een **aangepaste activiteit** in Data Factory met uw eigen logica voor het kopiëren/verplaatsen van gegevens. Zie [Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn](data-factory-use-custom-activities.md) voor meer informatie over het maken en gebruiken van een aangepaste activiteit.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevensarchieven met een * kunnen zich on-premises of op Azure IaaS bevinden. Hiervoor moet u [Data Management Gateway](data-factory-data-management-gateway.md) installeren op een on-premises/Azure IaaS-computer.

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen
U Kopieeractiviteit gebruiken om bestanden te **kopiëren tussen** twee bestandsgebaseerde gegevensarchieven, u de [indelingssectie](data-factory-create-datasets.md) overslaan in zowel de definities van de invoer- als de uitvoerset. De gegevens worden efficiënt gekopieerd zonder serialisatie/deserialisatie.

Copy Activity leest ook van en schrijft naar bestanden in opgegeven indelingen: **Tekst, JSON, Avro, ORC en Parquet**en compressiecode C **Zip, Deflate, BZip2 en ZipDeflate** worden ondersteund. Zie [Ondersteunde bestands- en compressie-indelingen](data-factory-supported-file-and-compression-formats.md) met details.

U bijvoorbeeld de volgende kopieeractiviteiten uitvoeren:

* Kopieer gegevens in on-premises SQL Server en schrijf naar Azure Data Lake Store in ORC-indeling.
* Kopieer bestanden in de CSV-indeling (text) van on-premises bestandssysteem en schrijf naar Azure Blob in Avro-indeling.
* Kopieer bestanden met rits en decomprimeer vervolgens naar Azure Data Lake Store.
* Kopieer gegevens in de CSV-indeling (GZip-indeling voor gecomprimeerde tekst) vanuit Azure Blob en schrijf naar Azure SQL Database.

## <a name="globally-available-data-movement"></a><a name="global"></a>Wereldwijd beschikbare gegevensverplaatsing
Azure Data Factory is alleen beschikbaar in de regio's West-VS, Oost-VS en Noord-Europa. De service die kopieeractiviteit mogelijk maakt, is echter wereldwijd beschikbaar in de volgende regio's en regio's. De wereldwijd beschikbare topologie zorgt voor een efficiënte gegevensverplaatsing die hop tussen regio's meestal vermijdt. Zie [Services per regio](https://azure.microsoft.com/regions/#services) voor de beschikbaarheid van gegevensfabriek en gegevensverplaatsing in een regio.

### <a name="copy-data-between-cloud-data-stores"></a>Gegevens kopiëren tussen cloudgegevensarchieven
Wanneer zowel bron- als sinkdataopslag zich in de cloud bevinden, gebruikt Data Factory een service-implementatie in de regio die het dichtst bij de gootsteen in dezelfde geografie ligt om de gegevens te verplaatsen. Raadpleeg de volgende tabel voor toewijzing:

| Geografie van de doelgegevensopslag | Regio van het doelgegevensarchief | Regio die wordt gebruikt voor gegevensverplaatsing |
|:--- |:--- |:--- |
| Verenigde Staten | VS - oost | VS - oost |
| &nbsp; | VS - oost 2 | VS - oost 2 |
| &nbsp; | VS - centraal | VS - centraal |
| &nbsp; | VS - noord-centraal | VS - noord-centraal |
| &nbsp; | VS - zuid-centraal | VS - zuid-centraal |
| &nbsp; | VS - west-centraal | VS - west-centraal |
| &nbsp; | VS - west | VS - west |
| &nbsp; | VS - west 2 | VS - west 2 |
| Canada | Canada - oost | Canada - midden |
| &nbsp; | Canada - midden | Canada - midden |
| Brazilië | Brazilië - zuid | Brazilië - zuid |
| Europa | Europa - noord | Europa - noord |
| &nbsp; | Europa -west | Europa -west |
| Verenigd Koninkrijk | Verenigd Koninkrijk West | Verenigd Koninkrijk Zuid |
| &nbsp; | Verenigd Koninkrijk Zuid | Verenigd Koninkrijk Zuid |
| Azië en Stille Oceaan | Azië - zuidoost | Azië - zuidoost |
| &nbsp; | Azië - oost | Azië - zuidoost |
| Australië | Australië - oost | Australië - oost |
| &nbsp; | Australië - zuidoost | Australië - zuidoost |
| India | India - centraal | India - centraal |
| &nbsp; | India - west | India - centraal |
| &nbsp; | India - zuid | India - centraal |
| Japan | Japan - oost | Japan - oost |
| &nbsp; | Japan - west | Japan - oost |
| Korea | Korea - centraal | Korea - centraal |
| &nbsp; | Korea - zuid | Korea - centraal |

U ook expliciet aangeven in welk gebied gegevensfabriek de kopie moet `executionLocation` worden uitgevoerd `typeProperties`door de eigenschap op te geven onder Activiteit kopiëren. Ondersteunde waarden voor deze eigenschap worden in bovengebied gebruikt voor de kolom **Gegevensverplaatsing.** Let op: uw gegevens gaan door die regio over de draad tijdens het kopiëren. Als u bijvoorbeeld wilt kopiëren tussen Azure-winkels in Korea, u opgeven om door de regio Japan te routeren `"executionLocation": "Japan East"` (zie voorbeeld [JSON](#by-using-json-scripts) als referentie).

> [!NOTE]
> Als het gebied van het doelgegevensarchief niet in de voorgaande lijst staat of niet detecteerbaar is, mislukt kopiëren activiteit standaard in plaats van door een alternatief gebied te gaan, tenzij `executionLocation` is opgegeven. De lijst met ondersteunde regio's wordt in de loop van de tijd uitgebreid.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Gegevens kopiëren tussen een on-premises gegevensopslag en een cloudgegevensarchief
Wanneer gegevens worden gekopieerd tussen on-premises (of Azure virtual machines/IaaS) en cloudstores, voert [Data Management Gateway](data-factory-data-management-gateway.md) gegevensbewegingen uit op een on-premises machine of virtuele machine. De gegevens stromen niet door de service in de cloud, tenzij u de [gefaseerde kopieermogelijkheid](data-factory-copy-activity-performance.md#staged-copy) gebruikt. In dit geval stromen gegevens door de Azure Blob-opslag met tijdelijke bestanden voordat deze in het sink-gegevensarchief wordt geschreven.

## <a name="create-a-pipeline-with-copy-activity"></a>Een pijplijn maken met kopieeractiviteit
U op een aantal manieren een pijplijn maken met Kopieeractiviteit:

### <a name="by-using-the-copy-wizard"></a>Met de wizard Kopiëren
Met de wizard Gegevensfabriekkopiëren u een pijplijn maken met Kopieeractiviteit. Met deze pijplijn u gegevens uit ondersteunde bronnen naar bestemmingen kopiëren *zonder JSON-definities* te schrijven voor gekoppelde services, gegevenssets en pijplijnen. Zie [wizard Gegevensfabriek kopiëren](data-factory-copy-wizard.md) voor meer informatie over de wizard.  

### <a name="by-using-json-scripts"></a>Door JSON-scripts te gebruiken
U De Editor van Gegevensfabriek in Visual Studio of Azure PowerShell gebruiken om een JSON-definitie voor een pijplijn te maken (met Kopieeractiviteit). Vervolgens u deze implementeren om de pijplijn in Data Factory te maken. Zie [Zelfstudie: Kopieeractiviteit gebruiken in een Azure Data Factory-pijplijn](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor een zelfstudie met stapsgewijze instructies.    

Json-eigenschappen (zoals naam, beschrijving, invoer- en uitvoertabellen en beleidsregels) zijn beschikbaar voor alle soorten activiteiten. Eigenschappen die beschikbaar `typeProperties` zijn in het gedeelte van de activiteit, verschillen per activiteitstype.

Voor Kopieeractiviteit `typeProperties` is de sectie afhankelijk van de typen bronnen en putten. Klik op een bron/gootsteen in de sectie [Ondersteunde bronnen en sinks](#supported-data-stores-and-formats) voor meer informatie over teksteigenschappen die Activiteit kopiëren ondersteunt voor dat gegevensarchief.

Hier is een voorbeeld JSON definitie:

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
Het schema dat is gedefinieerd in de uitvoergegevensset bepaalt wanneer de activiteit wordt uitgevoerd (bijvoorbeeld: **dagelijks**, frequentie als **dag**en interval als **1**). De activiteit kopieert gegevens uit een invoergegevensset **(bron)** naar een uitvoergegevensset **(sink).**

U meer dan één invoergegevensset opgeven voor Activiteit kopiëren. Ze worden gebruikt om de afhankelijkheden te verifiëren voordat de activiteit wordt uitgevoerd. Alleen de gegevens van de eerste gegevensset worden echter gekopieerd naar de doelgegevensset. Zie [Plannen en uitvoeren](data-factory-scheduling-and-execution.md)voor meer informatie.  

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Zie de [handleiding voor prestaties en tuning kopiëren,](data-factory-copy-activity-performance.md)waarin de belangrijkste factoren worden beschreven die van invloed zijn op de prestaties van gegevensverplaatsing (Kopieeractiviteit) in Azure Data Factory. Het bevat ook de waargenomen prestaties tijdens interne tests en bespreekt verschillende manieren om de prestaties van kopieeractiviteit te optimaliseren.

## <a name="fault-tolerance"></a>Fouttolerantie
Standaard stopt kopieeractiviteit met het kopiëren van gegevens en retourfouten wanneer er onverenigbare gegevens tussen bron en gootsteen worden aangetroffen; terwijl u expliciet configureren om de incompatibele rijen over te slaan en te loggen en alleen deze compatibele gegevens kopiëren om de kopie te laten slagen. Zie de [fouttolerantie Activiteit kopiëren](data-factory-copy-activity-fault-tolerance.md) voor meer details.

## <a name="security-considerations"></a>Beveiligingsoverwegingen
Zie de [beveiligingsoverwegingen](data-factory-data-movement-security-considerations.md)die de beveiligingsinfrastructuur beschrijven die gegevensverplaatsingsservices in Azure Data Factory gebruiken om uw gegevens te beveiligen.

## <a name="scheduling-and-sequential-copy"></a>Planning en sequentiële kopie
Zie [Planning en uitvoering](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie over hoe planning en uitvoering werkt in Data Factory. Het is mogelijk om meerdere kopieerbewerkingen achter elkaar uit te voeren op een sequentiële /geordende manier. Zie de sectie [Opeenvolgend kopiëren.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)

## <a name="type-conversions"></a>Typeconversies
Verschillende gegevensopslag heeft verschillende native type systemen. Copy Activity voert automatische typeconversies uit van brontypen naar sinktypen met de volgende aanpak in twee stappen:

1. Converteren van native brontypen naar een .NET-type.
2. Converteren van een .NET-type naar een native gootsteentype.

De toewijzing van een native typesysteem naar een .NET-type voor een gegevensarchief bevindt zich in het desbetreffende gegevensarchiefartikel. (Klik op de specifieke koppeling in de tabel Ondersteunde gegevensopslag). U deze toewijzingen gebruiken om de juiste typen te bepalen tijdens het maken van uw tabellen, zodat Kopieeractiviteit de juiste conversies uitvoert.

## <a name="next-steps"></a>Volgende stappen
* Zie [Gegevens kopiëren van Azure Blob-opslag naar Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor meer informatie over de activiteit kopiëren.
* Zie [Gegevens verplaatsen van on-premises naar cloudgegevensarchieven voor](data-factory-move-data-between-onprem-and-cloud.md)meer informatie over het verplaatsen van gegevens van een on-premises gegevensarchief naar een cloudgegevensarchief.
