---
title: Gegevens uit een webtabel kopiëren met behulp van Azure Data Factory | Microsoft Docs
description: Meer informatie over web Table connector van Azure Data Factory waarmee u gegevens kunt kopiëren van een webtabel naar gegevens archieven die door Data Factory worden ondersteund als Sinks.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0ee573f26f717c0ed8ad36f1a06fe557d423e3c1
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274715"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Gegevens uit een webtabel kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-web-table-connector.md)
> * [Huidige versie](connector-web-table.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een Data Base van een webtabel te kopiëren. Dit is gebaseerd op de [overzicht kopieeractiviteit](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

Het verschil tussen deze web Table-connector, de [rest-connector](connector-rest.md) en de [http-connector](connector-http.md) zijn:

- Met **Web Table connector** wordt tabel inhoud geëxtraheerd van een HTML-webpagina.
- **Rest connector** biedt specifiek ondersteuning voor het kopiëren van gegevens uit rest-api's.
- **Http-connector** is algemeen om gegevens op te halen uit een http-eind punt, bijvoorbeeld om het bestand te downloaden. 

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit de Web Table-Data Base kopiëren naar een ondersteunde Sink-gegevens opslag. Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen/put door de kopieeractiviteit, de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Met name deze web Table-connector ondersteunt het **extra heren van tabel inhoud van een HTML-pagina**.

## <a name="prerequisites"></a>Vereisten

Als u deze web Table-connector wilt gebruiken, moet u een zelf-hostende Integration Runtime instellen. Zie [zelfgehoste Cloudintegratieruntime](create-self-hosted-integration-runtime.md) artikel voor meer informatie.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor web Table connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor gekoppelde webtabelgegevens:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **Web** |Ja |
| url | URL naar de webbron |Ja |
| authenticationType | Toegestane waarde is: **Anonymous**. |Ja |
| connectVia | De [Integration Runtime](concepts-integration-runtime.md) moet worden gebruikt verbinding maken met het gegevensarchief. Een zelf-hostende Integration Runtime is vereist zoals vermeld in de [vereisten](#prerequisites). |Ja |

**Voorbeeld:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de gegevensset van de webtabel.

Als u gegevens wilt kopiëren uit een webtabel, stelt u de eigenschap type van de gegevensset in op **Webtabel**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van de gegevensset moet worden ingesteld op: **WebTable** | Ja |
| path |Een relatieve URL naar de resource die de tabel bevat. |Nee. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van de gekoppelde service. |
| index |De index van de tabel in de resource. Zie [index ophalen van een tabel in een sectie met een HTML-pagina](#get-index-of-a-table-in-an-html-page) voor de stappen voor het ophalen van index van een tabel in een HTML-pagina. |Ja |

**Voorbeeld:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst van de secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de bron van de webtabel.

### <a name="web-table-as-source"></a>Webtabel als bron

Als u gegevens wilt kopiëren uit een webtabel, stelt u het bron type in de Kopieer activiteit in op **webbron**. er worden geen aanvullende eigenschappen ondersteund.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Index van een tabel in een HTML-pagina ophalen

Als u de index wilt ophalen van een tabel die u in de [Eigenschappen](#dataset-properties)van de gegevensset wilt configureren, kunt u bijvoorbeeld Excel 2016 als volgt gebruiken als het hulp programma:

1. Start **Excel 2016** en schakel over naar het tabblad **gegevens** .
2. Klik op **nieuwe query** op de werk balk, wijs **vanuit andere bronnen** naar en klik op **van web**.

    ![Menu Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Voer in het dialoog venster **van web** de **URL** in die u in de gekoppelde service-JSON zou gebruiken ( https://en.wikipedia.org/wiki/) bijvoorbeeld: in combi natie met het pad dat u opgeeft voor de gegevensset (bijvoorbeeld: AFI%27s_100_Years... 100_Movies) en klik op **OK**.

    ![Vanuit het dialoog venster Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL die in dit voor beeld wordt gebruikt: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Als u het dialoog venster **webinhoud openen** ziet, selecteert u de juiste **URL**, **verificatie**en klikt u op **verbinding maken**.

   ![Het dialoog venster toegang tot webinhoud](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klik in de structuur weergave op een **tabel** item om de inhoud van de tabel te bekijken en klik vervolgens onderaan op de knop **bewerken** .  

   ![Dialoog venster navigator](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Klik in het venster **query-editor** op **Geavanceerde editor** knop op de werk balk.

    ![Knop Geavanceerde editor](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. In het dialoog venster Geavanceerde editor is het nummer naast ' Bron ' de index.

    ![Geavanceerde editor-index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Als u Excel 2013 gebruikt, gebruikt u [Microsoft Power query voor Excel](https://www.microsoft.com/download/details.aspx?id=39379) om de index op te halen. Zie [verbinding maken met een webpagina](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) -artikel voor meer informatie. De stappen zijn vergelijkbaar als u [micro soft power BI voor het bureau blad](https://powerbi.microsoft.com/desktop/)gebruikt.


## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
