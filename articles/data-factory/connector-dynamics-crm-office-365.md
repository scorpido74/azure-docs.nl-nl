---
title: Gegevens kopiëren van en naar Dynamics CRM of Dynamics 365 (Common Data Service) met behulp van Azure Data Factory | Microsoft Docs
description: Informatie over het kopiëren van gegevens van micro soft Dynamics CRM of micro soft Dynamics 365 (Common Data Service) naar ondersteunde Sink-gegevens archieven of van ondersteunde bron gegevens archieven naar Dynamics CRM of Dynamics 365 door gebruik te maken van een Kopieer activiteit in een data factory pijp lijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 3ad9ac3f0a3106b4562217a9c444b58423374318
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931111"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Dynamics 365 (Common Data Service) of Dynamics CRM door gebruik te maken van Azure Data Factory

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar micro soft Dynamics 365 of micro soft Dynamics CRM. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit Dynamics 365 (Common Data Service) of Dynamics CRM kopiëren naar een ondersteunde Sink-gegevens opslag. U kunt ook gegevens uit een ondersteund brongegevens archief kopiëren naar Dynamics 365 (Common Data Service) of Dynamics CRM. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Deze Dynamics-connector ondersteunt Dynamics versie 7. x tot 9. x voor online of on-premises. Meer specifiek,

- Versie 7. x wordt toegewezen aan Dynamics CRM 2015
- Versie 8. x wordt toegewezen aan Dynamics CRM 2016 en de vroege versie van Dynamics 365
- Versie 9. x wordt toegewezen aan de nieuwere versie van Dynamics 365

Raadpleeg de volgende tabel over de ondersteunde verificatie typen en configuraties voor de respectieve Dynamics-versies/-producten. (IFD is kort voor Internet gerichte implementatie.)

| Dynamics-versies | Verificatie typen | Voor beelden van gekoppelde services |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 on-premises met IFD <br> Dynamics CRM 2016 on-premises met IFD <br> Dynamics CRM 2015 on-premises met IFD | IFD | [On-premises Dynamics met IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Voor Dynamics 365 worden de volgende toepassings typen ondersteund:

- Dynamics 365 voor verkoop
- Dynamics 365 voor klanten service
- Dynamics 365 for Field Service
- Dynamics 365 voor project Service Automation
- Dynamics 365 voor marketing

Andere toepassings typen, zoals Finance en Operations, talen enz., worden niet ondersteund door deze connector.

Deze Dynamics-connector is gebaseerd op [Dynamics xRM-hulp middelen](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>U kunt de [Dynamics AX-connector](connector-dynamics-ax.md)gebruiken om gegevens uit **Dynamics 365-Financiën en-bewerkingen**te kopiëren.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Dynamics.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de Dynamics gekoppelde service.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 en Dynamics CRM Online

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Dynamics**, **DynamicsCrm**of **CommonDataServiceForApps**. | Ja |
| deploymentType | Het implementatie type van het Dynamics-exemplaar. Deze moet **online** zijn voor Dynamics online. | Ja |
| serviceUri | De service-URL van uw Dynamics-exemplaar, bijvoorbeeld `https://adfdynamics.crm.dynamics.com`. | Ja |
| authenticationType | Het verificatie type om verbinding te maken met een Dynamics-Server. Geef **' Office365 '** op voor Dynamics online. | Ja |
| gebruikersnaam | Geef de gebruikers naam op om een verbinding met Dynamics te maken. | Ja |
| wachtwoord | Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee voor bron, ja voor Sink als de gekoppelde bron service geen Integration runtime heeft |

>[!NOTE]
>De Dynamics-connector die wordt gebruikt om de optionele eigenschap Organization naam te gebruiken om uw Dynamics CRM/365 online-exemplaar te identificeren. Terwijl het werkt, wordt u geadviseerd om in plaats daarvan de nieuwe ' serviceUri-eigenschap op te geven om betere prestaties te verkrijgen voor exemplaar detectie.

**Voor beeld: Dynamics online met behulp van Office365-verificatie**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 en Dynamics CRM on-premises met IFD

*Aanvullende eigenschappen die worden vergeleken met Dynamics online zijn "hostName" en "Port".*

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Dynamics**, **DynamicsCrm**of **CommonDataServiceForApps**. | Ja |
| deploymentType | Het implementatie type van het Dynamics-exemplaar. Dit moet **' OnPremisesWithIfd '** zijn voor on-premises Dynamics op locatie met IFD.| Ja |
| Hostnaam | De hostnaam van de on-premises Dynamics-Server. | Ja |
| poort | De poort van de on-premises Dynamics-Server. | Nee, standaard waarde is 443 |
| organisatie naam | De naam van de organisatie van het Dynamics-exemplaar. | Ja |
| authenticationType | Het verificatie type om verbinding te maken met de Dynamics-Server. Geef **' IFD '** op voor on-premises Dynamics met IFD. | Ja |
| gebruikersnaam | Geef de gebruikers naam op om een verbinding met Dynamics te maken. | Ja |
| wachtwoord | Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. U kunt dit veld markeren als SecureString om het veilig op te slaan in ADF, of het wacht woord op te slaan in Azure Key Vault en de Kopieer activiteit te laten optrekken tijdens het uitvoeren van de gegevens kopie: meer informatie over [referenties voor opslaan in Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee voor bron, ja voor Sink |

**Voor beeld: on-premises Dynamics met IFD met IFD-verificatie**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die door Dynamics DataSet worden ondersteund.

Als u gegevens wilt kopiëren van en naar Dynamics, worden de volgende eigenschappen ondersteund.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **DynamicsEntity**, **DynamicsCrmEntity**of **CommonDataServiceForAppsEntity**. |Ja |
| entityName | De logische naam van de entiteit die moet worden opgehaald. | Nee voor bron (als ' query ' in de activiteit bron is opgegeven), ja voor Sink |

**Voorbeeld:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de typen Dynamics-bron en-Sink.

### <a name="dynamics-as-a-source-type"></a>Dynamics als bron type

Als u gegevens wilt kopiëren uit Dynamics, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op **DynamicsSource**, **DynamicsCrmSource**of **CommonDataServiceForAppsSource**. | Ja |
| query | FetchXML is een eigen query taal die wordt gebruikt in Dynamics (online en on-premises). Zie het volgende voorbeeld Zie [Query's bouwen met FetchXML](https://msdn.microsoft.com/library/gg328332.aspx)voor meer informatie. | Nee (als ' EntityName ' in de gegevensset is opgegeven) |

>[!NOTE]
>De PK-kolom wordt altijd gekopieerd, zelfs als de kolom projectie die u configureert in de FetchXML-query deze niet bevat.

> [!IMPORTANT]
>- Wanneer u gegevens uit Dynamics kopieert, is expliciete kolom toewijzing van Dynamics naar Sink optioneel, maar is deze zeer opnieuw ingesteld om te zorgen voor een deterministisch Kopieer resultaat.
>- Bij het importeren van het schema in de gebruikers interface, wordt het schema door ADF afgeleid door de bovenste rijen uit het query resultaat van de Dynamics te bemonsteren om de bron kolom lijst te initialiseren. in dat geval worden kolommen zonder waarden in de bovenste rijen wegge laten. Dit geldt ook voor het kopiëren van uitvoeringen als er geen expliciete toewijzing is. U kunt meer kolommen bekijken en toevoegen aan de toewijzing, die tijdens de Kopieer runtime worden nageleefd.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Voor beeld van FetchXML-query

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics als Sink-type

Als u gegevens wilt kopiëren naar Dynamics, worden de volgende eigenschappen ondersteund in het gedeelte **sink** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **DynamicsSink**, **DynamicsCrmSink**of **CommonDataServiceForAppsSink**. | Ja |
| WriteBehavior | Het schrijf gedrag van de bewerking.<br/>De toegestane waarde is **' Upsert '** . | Ja |
| alternateKeyName | Geef de alternatieve sleutel naam op die voor uw entiteit is gedefinieerd om "Upsert" uit te voeren. | Nee |
| writeBatchSize | Het aantal rijen van gegevens dat in elke batch naar Dynamics is geschreven. | Nee (de standaard waarde is 10) |
| ignoreNullValues | Hiermee wordt aangegeven of Null-waarden moeten worden genegeerd voor invoer gegevens (met uitzonde ring van sleutel velden) tijdens een schrijf bewerking.<br/>Toegestane waarden zijn **waar** en **Onwaar**.<br>- **waar**: laat de gegevens in het doel object ongewijzigd wanneer u een upsert/update-bewerking uitgevoerd. Voeg een gedefinieerde standaard waarde in wanneer u een INSERT-bewerking wilt uitvoeren.<br/>- **False**: werk de gegevens in het doel object bij naar NULL wanneer u een upsert-of update-bewerking uitgevoerd. Voeg een NULL-waarde toe wanneer u een INSERT-bewerking uitgevoerd. | Nee (standaard waarde is False) |

>[!NOTE]
>De standaard waarde van de sink "**writeBatchSize**" en de Kopieer activiteit " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " voor de Dynamics-Sink zijn beide 10. Daarom worden 100-records gelijktijdig aan Dynamics verzonden.

Voor Dynamics 365 online geldt een limiet van [2 gelijktijdige batch-aanroepen per organisatie](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Als deze limiet wordt overschreden, wordt de fout server bezet gegenereerd voordat de eerste aanvraag wordt uitgevoerd. Als u ' writeBatchSize ' beperkt of gelijk houdt aan 10, vermijdt u het beperken van gelijktijdige aanroepen.

De optimale combi natie van '**writeBatchSize**' en '**parallelCopies**' is afhankelijk van het schema van uw entiteit, bijvoorbeeld het aantal kolommen, de Rijgrootte, het aantal invoeg toepassingen/werk stromen/werk stroom activiteiten dat is aangesloten op deze aanroepen, enzovoort. De standaard instelling van 10 writeBatchSize * 10 parallelCopies is de aanbeveling volgens de Dynamics-service. dit werkt voor de meeste Dynamics-entiteiten, maar mogelijk niet de beste prestaties. U kunt de prestaties afstemmen door de combi natie te wijzigen in de instellingen voor de Kopieer activiteit.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Toewijzing van gegevens type voor Dynamics

Wanneer u gegevens uit Dynamics kopieert, worden de volgende toewijzingen gebruikt vanuit Dynamics-gegevens typen om tussenliggende gegevens typen te Data Factory. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

Configureer het bijbehorende Data Factory gegevens type in een gegevensset structuur op basis van uw bron Dynamics-gegevens type met behulp van de volgende toewijzings tabel.

| Dynamics-gegevens type | Data Factory tussentijds gegevens type | Ondersteund als bron | Ondersteund als Sink |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Lang | ✓ | ✓ |
| AttributeTypeCode. Boolean | Booleaans | ✓ | ✓ |
| AttributeType. klant | GUID | ✓ | |
| AttributeType. DateTime | Datetime | ✓ | ✓ |
| AttributeType. decimaal | Komma | ✓ | ✓ |
| AttributeType. Double | Dubbelklik | ✓ | ✓ |
| AttributeType. EntityName | Tekenreeks | ✓ | ✓ |
| AttributeType. integer | Int32 | ✓ | ✓ |
| AttributeType. lookup | GUID | ✓ | ✓ (met één doel gekoppeld) |
| AttributeType. ManagedProperty | Booleaans | ✓ | |
| AttributeType. Memo | Tekenreeks | ✓ | ✓ |
| AttributeType. geld | Komma | ✓ | ✓ |
| AttributeType. owner | GUID | ✓ | |
| AttributeType. selectie lijst | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType. String | Tekenreeks | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> De Dynamics-gegevens typen AttributeType. CalendarRules, AttributeType. MultiSelectPicklist en AttributeType. PartyList worden niet ondersteund.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
