---
title: Gegevens van Presto kopiëren met Azure Data Factory (Voorbeeld)
description: Meer informatie over het kopiëren van gegevens van Presto naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 8364468277123205d967871ab7bf2d048db64a82
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991787"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Gegevens van Presto kopiëren met Azure Data Factory (Voorbeeld)

In dit artikel wordt beschreven hoe u de activiteit kopiëren in Azure Data Factory gebruiken om gegevens van Presto te kopiëren. Het bouwt voort op de [kopie activiteit overzicht](copy-activity-overview.md) artikel dat een algemeen overzicht van kopieeractiviteit presenteert.

> [!IMPORTANT]
> Deze connector is momenteel in preview. Je het uitproberen en ons feedback geven. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) als u een afhankelijkheid van preview-connectors wilt opnemen in uw oplossing.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Presto-connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Presto kopiëren naar een ondersteund sink data store. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslag die wordt ondersteund als bronnen/sinks door de kopieeractiviteit.

Azure Data Factory biedt een ingebouwd stuurprogramma om connectiviteit mogelijk te maken, daarom hoeft u geen stuurprogramma handmatig te installeren met deze connector.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in Gegevensfabriek te definiëren die specifiek zijn voor de Presto-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor presto gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **Presto** | Ja |
| host | Het IP-adres of de hostnaam van de Presto-server. (d.w.z. 192.168.222.160)  | Ja |
| serverVersie | De versie van de Presto-server. (d.w.z. 0.148-t)  | Ja |
| Catalogus | De cataloguscontext voor alle aanvragen tegen de server.  | Ja |
| poort | De TCP-poort die de Presto-server gebruikt om naar clientverbindingen te luisteren. De standaardwaarde is 8080.  | Nee |
| authenticationType | Het verificatiemechanisme dat wordt gebruikt om verbinding te maken met de Presto-server. <br/>Toegestane waarden zijn: **Anoniem,** **LDAP** | Ja |
| gebruikersnaam | De gebruikersnaam die wordt gebruikt om verbinding te maken met de Presto-server.  | Nee |
| wachtwoord | Het wachtwoord dat overeenkomt met de gebruikersnaam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Nee |
| inschakelenSsl | Hiermee geeft u op of de verbindingen met de server zijn versleuteld met TLS. De standaardwaarde is false.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met vertrouwde CA-certificaten voor het verifiëren van de server bij het maken via TLS. Deze eigenschap kan alleen worden ingesteld wanneer tls wordt gebruikt op zelf gehoste IR. De standaardwaarde is het cacerts.pem-bestand dat bij de IR is geïnstalleerd.  | Nee |
| gebruik vanSystemTrustStore | Hiermee geeft u op of u een CA-certificaat wilt gebruiken in het systeemvertrouwensarchief of uit een opgegeven PEM-bestand. De standaardwaarde is false.  | Nee |
| allowHostNameCNMismatch | Hiermee geeft u op of een door CA uitgegeven TLS/SSL-certificaatnaam moet overeenkomen met de hostnaam van de server wanneer u verbinding maakt via TLS. De standaardwaarde is false.  | Nee |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server moeten worden toegestaan. De standaardwaarde is false.  | Nee |
| timeZoneID | De lokale tijdzone die door de verbinding wordt gebruikt. Geldige waarden voor deze optie worden opgegeven in de IANA Time Zone Database. De standaardwaarde is de systeemtijdzone.  | Nee |

**Voorbeeld:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de presto-gegevensset.

Als u gegevens uit Presto wilt kopiëren, stelt u de eigenschap type van de gegevensset in **op PrestoObject**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet zijn ingesteld op: **PrestoObject** | Ja |
| schema | Naam van het schema. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tabel | Naam van de tabel. |Nee (als 'query' in activiteitsbron is opgegeven)  |
| tableName | Naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` `table` en voor nieuwe werkbelasting. | Nee (als 'query' in activiteitsbron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Presto-bron.

### <a name="presto-as-source"></a>Presto als bron

Als u gegevens uit Presto wilt kopiëren, stelt u het brontype in de kopieeractiviteit in **op PrestoSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de kopieeractiviteit moet zijn ingesteld op: **PrestoSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Nee (als 'tabelNaam' in de gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
