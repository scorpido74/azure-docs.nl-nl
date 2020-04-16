---
title: Gegevens uit webtabel kopiëren met Azure Data Factory
description: Meer informatie over Web Table Connector van Azure Data Factory waarmee u gegevens uit een webtabel kopiëren naar gegevensopslag die door Data Factory worden ondersteund als putten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0eb4d37342685c13027a69bb6cb85f618fa63f20
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410217"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Gegevens uit de webtabel kopiëren met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-web-table-connector.md)
> * [Huidige versie](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u activiteit kopiëren in Azure Data Factory gebruiken om gegevens uit een database met webtabelen te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

Het verschil tussen deze webtafelconnector, de [REST-connector](connector-rest.md) en de [HTTP-connector](connector-http.md) zijn:

- **In de webtabelconnector** wordt tabelinhoud uit een HTML-webpagina geëxtraheerd.
- **REST-connector** ondersteunt specifiek het kopiëren van gegevens van RESTful API's.
- **HTTP-connector** is algemeen om gegevens op te halen uit elk HTTP-eindpunt, bijvoorbeeld om bestand te downloaden. 

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze webtabelconnector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens uit de database van de webtabel kopiëren naar een ondersteund sinkdataarchief. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Deze webtabelconnector ondersteunt met name **het extraheren van tabelinhoud van een HTML-pagina.**

## <a name="prerequisites"></a>Vereisten

Als u deze webtabelconnector wilt gebruiken, moet u een self-hosted Integration Runtime instellen. Zie [artikel Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) voor meer informatie.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabrieken te definiëren die specifiek zijn voor de connector van de webtabel.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor gekoppelde webtabelservice:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Web** |Ja |
| url | URL naar de webbron |Ja |
| authenticationType | Toegestane waarde is: **Anoniem**. |Ja |
| connectVia | De [integratieruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Een Self-hosted Integration Runtime is vereist zoals vermeld in [Voorwaarden](#prerequisites). |Ja |

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

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset van de webtabel.

Als u gegevens uit de webtabel wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **WebTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **WebTable** | Ja |
| path |Een relatieve URL naar de resource die de tabel bevat. |Nee. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van gekoppelde service. |
| Index |De index van de tabel in de resource. Zie [Index van een tabel in een HTML-paginasectie opvragen](#get-index-of-a-table-in-an-html-page) voor stappen om index van een tabel in een HTML-pagina op te halen. |Ja |

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de bron van de webtabel.

### <a name="web-table-as-source"></a>Webtabel als bron

Als u gegevens uit de webtabel wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **WebSource,** er worden geen extra eigenschappen ondersteund.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Index van een tabel in een HTML-pagina opdoen

Als u de index wilt krijgen van een tabel die u moet configureren in [gegevensseteigenschappen,](#dataset-properties)u bijvoorbeeld Excel 2016 als hulpmiddel als volgt gebruiken:

1. Start **Excel 2016** en ga naar het tabblad **Gegevens.**
2. Klik op **Nieuwe query** op de werkbalk, wijs Uit **andere bronnen** aan en klik op Van **web**.

    ![Power Query-menu](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Voer in het dialoogvenster **Van web** **URL** in die u zou https://en.wikipedia.org/wiki/) gebruiken in de gekoppelde service JSON (bijvoorbeeld: samen met het pad dat u voor de gegevensset opgeeft (bijvoorbeeld: AFI%27s_100_Years... 100_Movies) en klik op **OK**.

    ![Dialoogvenster Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL die in dit voorbeeld wordt gebruikt:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Als u het dialoogvenster **Access Web-inhoud** ziet, selecteert u de juiste **URL**, **verificatie**en klikt u op **Verbinding maken**.

   ![Dialoogvenster Webinhoud openen](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klik op een **tabelitem** in de structuurweergave om de inhoud van de tabel te bekijken en klik vervolgens op **Knop Bewerken** onderin.  

   ![Navigator-venster](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Klik in het venster **Queryeditor** op de knop **Geavanceerde editor** op de werkbalk.

    ![Knop Geavanceerde editor](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. In het dialoogvenster Geavanceerde editor is het getal naast 'Bron' de index.

    ![Geavanceerde editor - Index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Als u Excel 2013 gebruikt, gebruikt u [Microsoft Power Query voor Excel](https://www.microsoft.com/download/details.aspx?id=39379) om de index op te halen. Zie [Verbinding maken met een webpaginaartikel](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) voor meer informatie. De stappen zijn vergelijkbaar als u [Microsoft Power BI voor bureaublad gebruikt.](https://powerbi.microsoft.com/desktop/)


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
