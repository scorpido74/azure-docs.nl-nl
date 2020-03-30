---
title: Gegevens uit Xero kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Xero naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 1f6404da163e075b63a99a1d8474cdba4e064b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930887"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Gegevens uit Xero kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Xero te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Xero-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Xero kopiëren naar elk ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Met name deze Xero-connector ondersteunt:

- Xero [private applicatie,](https://developer.xero.com/documentation/getting-started/api-application-types) maar geen openbare toepassing.
- Alle Xero-tabellen (API-eindpunten) behalve "Rapporten". 

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Xero-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor xero gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Xero** | Ja |
| host | Het eindpunt van de Xero-server (`api.xero.com`).  | Ja |
| consumerKey | De consumentensleutel die bij de Xero-toepassing is gekoppeld. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| privateKey privateKey | De privésleutel van het .pem-bestand dat is gegenereerd voor uw Xero-privétoepassing, [raadpleegt Een openbaar/privé sleutelpaar maken.](https://developer.xero.com/documentation/api-guides/create-publicprivate-key) Opmerking om **de privatekey.pem te genereren met numbits van 512** met behulp van `openssl genrsa -out privatekey.pem 512`; 1024 wordt niet ondersteund. Neem alle tekst uit het .pem-bestand op, inclusief de Unix-regeleinde(\n), zie voorbeeld hieronder.<br/><br/>Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| gebruikVersleutelde eindpunten | Hiermee geeft u op of de eindpunten van de gegevensbron zijn versleuteld met HTTPS. De standaardwaarde is waar.  | Nee |
| useHostVerification | Hiermee geeft u op of de hostnaam vereist is in het certificaat van de server die overeenkomt met de hostnaam van de server wanneer deze verbinding maakt via SSL. De standaardwaarde is waar.  | Nee |
| usePeerVerification | Hiermee geeft u op of u de identiteit van de server moet verifiëren wanneer u verbinding maakt via SSL. De standaardwaarde is waar.  | Nee |

**Voorbeeld:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Voorbeeld van private key value:**

Neem alle tekst uit het .pem-bestand op, inclusief de Unix-regeleinde(\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Xero-gegevensset.

Als u gegevens uit Xero wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **XeroObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet zijn ingesteld op: **XeroObject** | Ja |
| tableName | Naam van de tabel. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Xero-bron.

### <a name="xero-as-source"></a>Xero als bron

Als u gegevens van Xero wilt kopiëren, stelt u het brontype in de kopieeractiviteit in op **XeroSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van kopieeractiviteit moet zijn ingesteld op: **XeroSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM Contacts"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Let op het volgende wanneer u de Xero-query opgeeft:

- Tabellen met complexe items worden opgesplitst in meerdere tabellen. Banktransacties hebben bijvoorbeeld een complexe gegevensstructuur "LineItems", zodat gegevens van `Bank_Transaction` `Bank_Transaction_Line_Items`banktransacties `Bank_Transaction_ID` worden toegewezen aan tabel en , met als buitenlandse sleutel om ze aan elkaar te koppelen.

- Xero-gegevens zijn beschikbaar via `Minimal` twee schema's: (standaard) en `Complete`. Het schema Voltooien bevat vereiste aanroeptabellen waarvoor aanvullende gegevens nodig zijn (bijvoorbeeld id-kolom) voordat u de gewenste query maakt.

De volgende tabellen bevatten dezelfde informatie in het schema Minimaal en Volledig. Als u het aantal API-aanroepen wilt verminderen, gebruikt u Minimaal schema (standaard).

- Bank_Transactions
- Contact_Groups 
- Contactpersonen 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Facturen 
- Invoices_Credit_Notes
- Invoices_ Vooruitbetalingen 
- Invoices_Overpayments 
- Manual_Journals 
- Te veel betaalde bedragen 
- Overpayments_Allocations 
- Vooruitbetalingen 
- Prepayments_Allocations 
- Ontvangsten 
- Receipt_Validation_Errors 
- Tracking_Categories

De volgende tabellen kunnen alleen worden opgevraagd met een volledig schema:

- Compleet.Bank_Transaction_Line_Items 
- Compleet.Bank_Transaction_Line_Item_Tracking 
- Compleet.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Personen 
- Compleet.Credit_Note_Line_Items 
- Compleet.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Betalingen 
- Compleet.Expense_Claim_Receipts 
- Compleet.Invoice_Line_Items 
- Compleet.Invoices_Line_Items_Tracking
- Compleet.Manual_Journal_Lines 
- Compleet.Manual_Journal_Line_Tracking 
- Compleet.Overpayment_Line_Items 
- Compleet.Overpayment_Line_Items_Tracking 
- Compleet.Prepayment_Line_Items 
- Compleet.Prepayment_Line_Item_Tracking 
- Compleet.Receipt_Line_Items 
- Compleet.Receipt_Line_Item_Tracking 
- Compleet.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met ondersteunde gegevensopslag door de kopieeractiviteit.
