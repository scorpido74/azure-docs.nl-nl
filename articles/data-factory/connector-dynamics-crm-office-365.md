---
title: Gegevens kopiëren in Dynamics (Common Data Service)
description: Meer informatie over het kopiëren van gegevens uit Microsoft Dynamics CRM of Microsoft Dynamics 365 (Common Data Service) naar ondersteunde sinkdatastores of van ondersteunde brongegevensopslag naar Dynamics CRM of Dynamics 365, met behulp van een kopieeractiviteit in een pijplijn voor gegevensfabrieken.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9bbd2e3376f1da3fdf5b10d654a331ce258be5cf
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422090"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Gegevens van en naar Dynamics 365 (Common Data Service) of Dynamics CRM kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruiken om gegevens van en naar Microsoft Dynamics 365 of Microsoft Dynamics CRM te kopiëren. Het bouwt voort op het [overzichtsartikel Activiteit kopiëren](copy-activity-overview.md) dat een algemeen overzicht van Kopieeractiviteit weergeeft.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze connector wordt ondersteund voor de volgende activiteiten:

- [Activiteit kopiëren](copy-activity-overview.md) met [ondersteunde bron/sinkmatrix](copy-activity-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)

U gegevens van Dynamics 365 (Common Data Service) of Dynamics CRM kopiëren naar een ondersteund sink datastore. U ook gegevens uit elk ondersteund brongegevensarchief kopiëren naar Dynamics 365 (Common Data Service) of Dynamics CRM. Zie de tabel [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bronnen of sinks door de kopieeractiviteit.

Deze Dynamics-connector ondersteunt Dynamics-versie 7.x tot 9.x voor zowel online als on-premises. Meer in het bijzonder,

- Versie 7.x maps to Dynamics CRM 2015
- Versie 8.x brengt kaarten naar Dynamics CRM 2016 en de vroege versie van Dynamics 365
- Versie 9.x kaarten naar de latere versie van Dynamics 365

Raadpleeg de volgende tabel over de ondersteunde verificatietypen en -configuraties voor de respectievelijke Dynamics-versies/-producten. (IFD is een afkorting voor internet-gerichte implementatie.)

| Dynamics-versies | Verificatietypen | Gekoppelde servicevoorbeelden |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | AAD-serviceprincipal <br> Office365 | [Dynamics online + AAD-serviceprincipal of Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 on-premises met IFD <br> Dynamics CRM 2016 on-premises met IFD <br> Dynamics CRM 2015 on-premises met IFD | IFD | [Dynamics on-premises met IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Specifiek voor Dynamics 365 worden de volgende toepassingstypen ondersteund:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Andere toepassingstypen zoals Finance and Operations, Talent, etc. worden niet ondersteund door deze connector.

Deze Dynamics-connector is gebouwd bovenop [Dynamics XRM-gereedschappen.](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)

>[!TIP]
>Als u gegevens uit **Dynamics 365 Finance and Operations**wilt kopiëren, u de Dynamics [AX-connector](connector-dynamics-ax.md)gebruiken.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die worden gebruikt om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor Dynamica.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de gekoppelde service Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 en Dynamics CRM Online

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **Dynamics,** **DynamicsCrm**of **CommonDataServiceForApps**. | Ja |
| deploymentType | Het implementatietype van de instantie Dynamica. Het moet **"Online"** voor Dynamics online. | Ja |
| serviceUri | De service-URL van uw instantie `https://adfdynamics.crm.dynamics.com`Dynamics, bijvoorbeeld . | Ja |
| authenticationType | Het verificatietype dat verbinding maakt met een Dynamics-server. Toegestane waarden zijn: **AADServicePrincipal** of **"Office365".** | Ja |
| servicePrincipalId | Geef de client-id van de Azure Active Directory-toepassing op. | Ja bij `AADServicePrincipal` het gebruik van verificatie |
| servicePrincipalCredentialType | Geef het referentietype op dat moet worden gebruikt voor serviceprincipal-verificatie. Toegestane waarden zijn: **ServicePrincipalKey** of **ServicePrincipalCert**. | Ja bij `AADServicePrincipal` het gebruik van verificatie |
| servicePrincipalCredential | Geef de hoofdreferentie van de service op. <br>Bij `ServicePrincipalKey` gebruik als `servicePrincipalCredential` referentietype kan een tekenreeks zijn (ADF versleutelt deze bij de implementatie van gekoppelde services) of een verwijzing naar een geheim in AKV. <br>Wanneer `ServicePrincipalCert` u als `servicePrincipalCredential` referentie wordt gebruikt, moet dit een verwijzing zijn naar een certificaat in AKV. | Ja bij `AADServicePrincipal` het gebruik van verificatie | 
| gebruikersnaam | Geef de gebruikersnaam op om verbinding te maken met Dynamics. | Ja bij `Office365` het gebruik van verificatie |
| wachtwoord | Geef het wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor gebruikersnaam. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja bij `Office365` het gebruik van verificatie |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee voor bron, Ja voor gootsteen als de brongekoppelde service geen inlooptijd voor integratie heeft |

>[!NOTE]
>De Dynamics-connector die wordt gebruikt om de optionele eigenschap 'organizationName' te gebruiken om uw instantie Dynamics CRM/365 Online te identificeren. Terwijl het blijft werken, wordt u voorgesteld om de nieuwe eigenschap "serviceUri" op te geven in plaats daarvan om betere prestaties te bereiken, bijvoorbeeld detectie.

**Voorbeeld: Dynamics online met AAD-serviceprincipal + sleutelverificatie**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Voorbeeld: Dynamics online met AAD-serviceprincipal + certificaatverificatie**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Voorbeeld: Dynamica online met Office365-verificatie**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
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

*Aanvullende eigenschappen die zich vergelijken met Dynamics online zijn 'hostName' en 'port'.*

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op **Dynamics,** **DynamicsCrm**of **CommonDataServiceForApps**. | Ja |
| deploymentType | Het implementatietype van de instantie Dynamica. Het moet **"OnPremisesWithIfd"** zijn voor Dynamics on-premises met IFD.| Ja |
| Hostname | De hostnaam van de on-premises Dynamics-server. | Ja |
| poort | De poort van de on-premises Dynamics-server. | Nee, standaard is 443 |
| Naamorganisatie | De organisatienaam van de instantie Dynamica. | Ja |
| authenticationType | Het verificatietype dat verbinding maakt met de Dynamics-server. Geef **'Ifd'** op voor Dynamics on-premises met IFD. | Ja |
| gebruikersnaam | Geef de gebruikersnaam op om verbinding te maken met Dynamics. | Ja |
| wachtwoord | Geef het wachtwoord op voor het gebruikersaccount dat u hebt opgegeven voor gebruikersnaam. U ervoor kiezen om dit veld te markeren als een SecureString om het veilig op te slaan in ADF, of wachtwoord op te slaan in Azure Key Vault en de kopieeractiviteit van daaruit te laten trekken bij het uitvoeren van gegevenskopie - meer informatie over [storereferenties in Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | De [inburgeringsruntijd](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevensarchief. Als dit niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. | Nee voor bron, Ja voor gootsteen |

**Voorbeeld: Dynamics on-premises met IFD met IFD-verificatie**

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

Zie het artikel [Gegevenssets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de gegevensset Dynamics.

Als u gegevens van en naar Dynamica wilt kopiëren, worden de volgende eigenschappen ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op **DynamicsEntity**, **DynamicsCrmEntity**of **CommonDataServiceForAppsEntity**. |Ja |
| entiteitNaam | De logische naam van de entiteit die moet worden opgehaald. | Nee voor bron (als 'query' in de activiteitsbron is opgegeven), Ja voor gootsteen |

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

Zie het artikel [Pijplijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door bron- en sinktypen van Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamiek als brontype

Als u gegevens uit Dynamica wilt kopiëren, worden de volgende eigenschappen ondersteund in de sectie **kopieeractiviteitsbron.**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopieeractiviteit moet zijn ingesteld op **DynamicsSource**, **DynamicsCrmSource**of **CommonDataServiceForAppsSource**. | Ja |
| query | FetchXML is een eigen querytaal die wordt gebruikt in Dynamics (online en on-premises). Zie het volgende voorbeeld Zie [Query's maken met FetchXML](https://msdn.microsoft.com/library/gg328332.aspx)voor meer informatie. | Nee (als 'entityName' in de gegevensset is opgegeven) |

>[!NOTE]
>De PK-kolom wordt altijd gekopieerd, zelfs als de kolomprojectie die u configureert in de fetchXML-query deze niet bevat.

> [!IMPORTANT]
>- Wanneer u gegevens uit Dynamics kopieert, is expliciete kolomtoewijzing van Dynamica naar gootsteen optioneel, maar sterk aanbevolen om een deterministisch kopieerresultaat te garanderen.
>- Wanneer adf schema importeert bij het ontwerpen van ui, leidt ADF het schema af door de bovenste rijen uit het queryresultaat Dynamica te samplen om de lijst met bronkolomte te initialiseren, in welk geval kolommen zonder waarden in bovenste rijen worden weggelaten. Hetzelfde gedrag is van toepassing op het kopiëren van uitvoeringen als er geen expliciete toewijzing is. U meer kolommen bekijken en toevoegen aan de toewijzing, die tijdens de runtime van de kopie wordt gehonoreerd.

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

### <a name="sample-fetchxml-query"></a>Voorbeeld van XML-query

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

### <a name="dynamics-as-a-sink-type"></a>Dynamica als gootsteentype

Als u gegevens wilt kopiëren naar Dynamica, worden de volgende eigenschappen ondersteund in de sectie **logboeken voor kopieeractiviteit.**

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de kopieeractiviteit moet worden ingesteld op **DynamicsSink**, **DynamicsCrmSink**of **CommonDataServiceForAppsSink**. | Ja |
| writeBehavior | Het schrijfgedrag van de operatie.<br/>Toegestane waarde is **"Upsert".** | Ja |
| alternateKeyName | Geef de alternatieve sleutelnaam op die op uw entiteit is gedefinieerd om 'Upsert' uit te voeren. | Nee |
| writeBatchSize | Het aantal gegevens dat in elke batch naar Dynamics is geschreven. | Nee (standaard is 10) |
| negerenNullValues | Hiermee geeft u aan of null-waarden uit invoergegevens (behalve sleutelvelden) moeten worden genegeerd tijdens een schrijfbewerking.<br/>Toegestane waarden zijn **waar** en **onwaar.**<br>- **True:** Laat de gegevens in het doelobject ongewijzigd wanneer u een upsert/update-bewerking uitvoert. Voeg een gedefinieerde standaardwaarde in wanneer u een invoegbewerking doet.<br/>- **False:** Werk de gegevens in het doelobject bij aan NULL wanneer u een upsert/update-bewerking uitvoert. Voeg een NULL-waarde in wanneer u een invoegbewerking doet. | Nee (standaard is onwaar) |

>[!NOTE]
>De standaardwaarde van de gootsteen "**writeBatchSize**" en de kopieeractiviteit "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" voor de Dynamics sink zijn beide 10. Daarom worden 100 records gelijktijdig bij Dynamics ingediend.

Voor Dynamics 365 online is er een limiet van [2 gelijktijdige batchcalls per organisatie.](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations) Als die limiet wordt overschreden, wordt een fout 'Server bezet' gegenereerd voordat de eerste aanvraag ooit wordt uitgevoerd. Het houden van "writeBatchSize" minder of gelijk aan 10 zou voorkomen dat een dergelijke beperking van gelijktijdige oproepen.

De optimale combinatie van "**writeBatchSize**" en "**parallelCopies**" is afhankelijk van het schema van uw entiteit, bijvoorbeeld het aantal kolommen, rijgrootte, het aantal plug-ins/workflows/workflowactiviteiten die aan die oproepen zijn aangesloten, enz. De standaardinstelling van 10 writeBatchSize * 10 parallelKopieën is de aanbeveling volgens de Dynamica-service, die voor de meeste Dynamics-entiteiten zou werken, maar misschien niet de beste prestaties zijn. U de prestaties afstemmen door de combinatie aan te passen in de instellingen voor kopieeractiviteit.

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

## <a name="data-type-mapping-for-dynamics"></a>Toewijzing van gegevenstype voor Dynamica

Wanneer u gegevens uit Dynamics kopieert, worden de volgende toewijzingen gebruikt, van Dynamics-gegevenstypen naar tussentijdse gegevenstypen van Data Factory. Zie [Schema en gegevenstypetoewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de kopieeractiviteit het bronschema en het gegevenstype aan de gootsteen toebrengt.

Configureer het bijbehorende gegevenstype Gegevensfabriek in een gegevenssetstructuur op basis van uw gegevenstype Source Dynamics met behulp van de volgende toewijzingstabel.

| Gegevenstype Dynamics | Tussentijds gegevenstype Data Factory | Ondersteund als bron | Ondersteund als gootsteen |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Lange | ✓ | ✓ |
| AttributeTypeCode.Boolean | Booleaans | ✓ | ✓ |
| AttributeType.Klant | GUID | ✓ | |
| Kenmerktype.datetime | Datum/tijd | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| Kenmerktype.EntityName | Tekenreeks | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Opzoeking | GUID | ✓ | ✓ (met enkel doel in verband) |
| AttributeType.ManagedEigenschap | Booleaans | ✓ | |
| AttributeType.Memo | Tekenreeks | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| KenmerkType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | Tekenreeks | ✓ | ✓ |
| AttributeType.state | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> De dynamics-gegevenstypen AttributeType.CalendarRules, AttributeType.MultiSelectPicklist en AttributeType.PartyList worden niet ondersteund.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoekactiviteit

Ga voor meer informatie over de eigenschappen naar [opzoekactiviteit](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Volgende stappen
Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Data Factory.
