---
title: Gegevens uit een REST-bron kopiëren met Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit een cloud of on-premises REST-bron naar ondersteunde sinkdatastores met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 3e0dd6e0bb81aef340dc83288e6e5c0af0bf11c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867373"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Gegevens van een REST-eindpunt kopiëren met Azure Data Factory

In dit artikel wordt beschreven hoe u Activiteit kopiëren in Azure Data Factory gebruikt om gegevens van een REST-eindpunt te kopiëren. Het artikel bouwt voort op [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md), dat een algemeen overzicht van Kopieeractiviteit weergeeft.

Het verschil tussen deze REST-connector, [HTTP-connector](connector-http.md) en de [webtafelconnector](connector-web-table.md) zijn:

- **REST-connector** ondersteunt specifiek het kopiëren van gegevens van RESTful API's; 
- **HTTP-connector** is algemeen om gegevens op te halen uit elk HTTP-eindpunt, bijvoorbeeld om bestand te downloaden. Voordat deze REST-connector beschikbaar komt, u toevallig HTTP-connector gebruiken om gegevens te kopiëren van RESTful API, die wordt ondersteund, maar minder functioneel in vergelijking met REST-connector.
- **In de webtabelconnector** wordt tabelinhoud uit een HTML-webpagina geëxtraheerd.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U gegevens uit een REST-bron kopiëren naar een ondersteund sinkdataarchief. Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die Activiteit kopiëren als bronnen en sinks ondersteunt.

Met name deze generieke REST-connector ondersteunt:

- Gegevens ophalen van een REST-eindpunt met behulp van de methoden **GET** of **POST.**
- Gegevens ophalen met behulp van een van de volgende verificaties: **Anoniem,** **Basic,** **AAD-serviceprincipal**en **beheerde identiteiten voor Azure-resources**.
- **[Pagination](#pagination-support)** in de REST API's.
- Het kopiëren van de REST JSON-respons [as-is](#export-json-response-as-is) of ontleden met behulp van [schematoewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping). Alleen responspayload in **JSON** wordt ondersteund.

> [!TIP]
> Als u een aanvraag voor het ophalen van gegevens wilt testen voordat u de REST-connector configureert in Gegevensfabriek, leest u meer over de API-specificatie voor header- en hoofdvereisten. U tools zoals Postman of een webbrowser gebruiken om te valideren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In de volgende secties vindt u informatie over eigenschappen die u gebruiken om entiteiten in gegevensfabriek te definiëren die specifiek zijn voor de REST-connector.

## <a name="linked-service-properties"></a>Gekoppelde service-eigenschappen

De volgende eigenschappen worden ondersteund voor de rest-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** moet zijn ingesteld op **RestService**. | Ja |
| url | De basis-URL van de REST-service. | Ja |
| enableServerCertificateValidation | Of u ssl-certificaat aan de serverzijde moet valideren wanneer u verbinding maakt met het eindpunt. | Nee<br /> (de standaardinstelling is **waar)** |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met de REST-service. Toegestane waarden zijn **Anoniem,** **Basic,** **AadServicePrincipal** en **ManagedServiceIdentity**. Raadpleeg de bijbehorende secties hieronder over respectievelijk meer eigenschappen en voorbeelden. | Ja |
| connectVia | De [runtime integratie](concepts-integration-runtime.md) om verbinding te maken met het gegevensarchief. Meer informatie van de sectie [Voorwaarden.](#prerequisites) Als deze eigenschap niet is opgegeven, wordt de standaardruntijd voor Azure-integratie gebruikt. |Nee |

### <a name="use-basic-authentication"></a>Basisverificatie gebruiken

De eigenschap **authenticationType** instellen op **Basic**. Geef naast de algemene eigenschappen die in de vorige sectie worden beschreven, de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| userName | De gebruikersnaam die u wilt gebruiken om toegang te krijgen tot het REST-eindpunt. | Ja |
| wachtwoord | Het wachtwoord voor de gebruiker (de **waarde userName).** Markeer dit veld als een **SecureString-type** om het veilig op te slaan in Gegevensfabriek. U ook [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |

**Voorbeeld**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
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

### <a name="use-aad-service-principal-authentication"></a>Aad-serviceprincipalverificatie gebruiken

Stel de eigenschap **authenticationType** in op **AadServicePrincipal**. Geef naast de algemene eigenschappen die in de vorige sectie worden beschreven, de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| servicePrincipalId | Geef de client-id van de Azure Active Directory-toepassing op. | Ja |
| servicePrincipalKey | Geef de sleutel van de Azure Active Directory-toepassing op. Markeer dit veld als een **SecureString** om het veilig op te slaan in Data Factory of [verwijs naar een geheim dat is opgeslagen in Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| tenant | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. Haal deze op door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja |
| aadResourceId | Geef de AAD-bron op die u om `https://management.core.windows.net`autorisatie vraagt, bijvoorbeeld .| Ja |

**Voorbeeld**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Beheerde identiteiten gebruiken voor Azure-bronverificatie

Stel de eigenschap **authenticationType** in op **ManagedServiceIdentity**. Geef naast de algemene eigenschappen die in de vorige sectie worden beschreven, de volgende eigenschappen op:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| aadResourceId | Geef de AAD-bron op die u om `https://management.core.windows.net`autorisatie vraagt, bijvoorbeeld .| Ja |

**Voorbeeld**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

In deze sectie vindt u een lijst met eigenschappen die de REST-gegevensset ondersteunt. 

Zie [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets. 

Als u gegevens van REST wilt kopiëren, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de gegevensset moet worden ingesteld op **RestResource**. | Ja |
| relativenurl | Een relatieve URL naar de bron die de gegevens bevat. Wanneer deze eigenschap niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de gekoppelde servicedefinitie. De HTTP-connector kopieert gegevens `[URL specified in linked service]/[relative URL specified in dataset]`uit de gecombineerde URL: . | Nee |

Als u `requestMethod`instelling `additionalHeaders` `requestBody` , `paginationRules` , en in de gegevensset, het wordt nog steeds ondersteund as-is, terwijl u wordt voorgesteld om het nieuwe model te gebruiken in activiteit bron gaan vooruit.

**Voorbeeld:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Activiteitseigenschappen kopiëren

In deze sectie vindt u een lijst met eigenschappen die de REST-bron ondersteunt.

Zie [Pijplijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="rest-as-source"></a>REST als bron

De volgende eigenschappen worden ondersteund in de sectie **bron** van kopieeractiviteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De **eigenschap type** van de bron van de kopieeractiviteit moet worden ingesteld op **RestSource**. | Ja |
| requestMethode | De HTTP-methode. Toegestane waarden zijn **Get** (default) en **Post**. | Nee |
| extra Kopteksten | Aanvullende HTTP-aanvraagkoppen. | Nee |
| requestBody | De instantie voor het HTTP-verzoek. | Nee |
| paginationRegels | De paginatieregels voor het samenstellen van volgende paginaaanvragen. Raadpleeg [de sectie ondersteuning](#pagination-support) van pagination over details. | Nee |
| httpRequestTime-out | De time-out (de **TimeSpan-waarde)** voor het HTTP-verzoek om een antwoord te krijgen. Deze waarde is de time-out om een antwoord te krijgen, niet de time-out om antwoordgegevens te lezen. De standaardwaarde is **00:01:40**.  | Nee |
| requestInterval | De tijd om te wachten voordat u het verzoek voor de volgende pagina verzendt. De standaardwaarde is **00:00:01** |  Nee |

>[!NOTE]
>Rest-connector negeert elke koptekst `additionalHeaders`accepteren die is opgegeven in . Als REST-connector alleen ondersteuning reactie in JSON, `Accept: application/json`zal het automatisch genereren van een header van .

**Voorbeeld 1: De methode Oppakken gebruiken met pagination**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Voorbeeld 2: De methode Post gebruiken**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Ondersteuning voor Pagination

Normaal gesproken beperkt REST API de grootte van het antwoordlaadvermogen van één enkele aanvraag onder een redelijk getal; terwijl om grote hoeveelheid gegevens terug te keren, het splitst het resultaat in meerdere pagina's en vereist bellers om opeenvolgende verzoeken te sturen naar de volgende pagina van het resultaat te krijgen. Meestal is het verzoek voor één pagina dynamisch en samengesteld door de informatie die is geretourneerd uit het antwoord van de vorige pagina.

Deze generieke REST-connector ondersteunt de volgende paginatiepatronen: 

* De absolute of relatieve URL van volgende aanvraag = eigenschapswaarde in de huidige reactietekst
* De absolute of relatieve URL van volgende aanvraag = kopwaarde in huidige antwoordkoppen
* Queryparameter van volgende aanvraag = eigenschapswaarde in huidige antwoordbody
* Queryparameter van volgende aanvraag = kopwaarde in huidige antwoordkoppen
* Koptekst van volgende aanvraag = eigenschapswaarde in huidige antwoordtekst
* Koptekst van volgende aanvraag = kopwaarde in huidige antwoordkoppen

**Pagination-regels** worden gedefinieerd als een woordenboek in de gegevensset die een of meer hoofdlettergevoelige sleutelwaardeparen bevat. De configuratie wordt gebruikt om de aanvraag te genereren vanaf de tweede pagina. De connector stopt met herhalen wanneer http-statuscode 204 (Geen inhoud) wordt weergegeven, of als een van de JSONPath-expressies in 'paginationRules' null retourneert.

**Ondersteunde sleutels** in pagination-regels:

| Sleutel | Beschrijving |
|:--- |:--- |
| AbsoluteUrl AbsoluteUrl | Geeft de URL aan om het volgende verzoek uit te geven. Het kan **absolute URL of relatieve URL**zijn. |
| Queryparameters. *request_query_parameter* OF QueryParameters['request_query_parameter'] | "request_query_parameter" is door de gebruiker gedefinieerd die verwijst naar één queryparameternaam in de volgende HTTP-aanvraag-URL. |
| Headers. *request_header* OR-headers['request_header'] | "request_header" is door de gebruiker gedefinieerd die verwijst naar één kopnaam in de volgende HTTP-aanvraag. |

**Ondersteunde waarden** in paginationregels:

| Waarde | Beschrijving |
|:--- |:--- |
| Headers. *response_header* OR-headers['response_header'] | "response_header" is door de gebruiker gedefinieerd die verwijst naar één koptekstnaam in het huidige HTTP-antwoord, waarvan de waarde wordt gebruikt om de volgende aanvraag uit te geven. |
| Een JSONPath-expressie die begint met '$' (die de wortel van de reactiebody vertegenwoordigt) | De responsbody mag slechts één JSON-object bevatten. De JSONPath-expressie moet één primitieve waarde retourneren, die wordt gebruikt om de volgende aanvraag uit te geven. |

**Voorbeeld:**

Facebook Graph API retourneert reactie in de volgende structuur, in welk geval de URL van de volgende pagina wordt weergegeven in ***paging.next:***

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

De overeenkomstige REST kopie `paginationRules` activiteit bron configuratie met name de is als volgt:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>OAuth gebruiken
In deze sectie wordt beschreven hoe u een oplossingssjabloon gebruiken om gegevens van REST-connector te kopiëren naar Azure Data Lake Storage in JSON-indeling met OAuth. 

### <a name="about-the-solution-template"></a>Informatie over de oplossingssjabloon

De sjabloon bevat twee activiteiten:
- **Webactiviteit** haalt het token aan toonder op en geeft het door aan de volgende kopieeractiviteit als autorisatie.
- **Kopieer** activiteit kopieergegevens van REST naar Azure Data Lake Storage.

De sjabloon definieert twee parameters:
- **SinkContainer** is het hoofdmappad waarnaar de gegevens worden gekopieerd in uw Azure Data Lake Storage. 
- **SinkDirectory** is het mappad onder de hoofdmap waarnaar de gegevens worden gekopieerd in uw Azure Data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Deze oplossingssjabloon gebruiken

1. Ga naar de **sjabloon Kopiëren van REST of HTTP met OAuth.** Maak een nieuwe verbinding voor Bronverbinding. 
    ![Nieuwe verbindingen maken](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Hieronder vindt u de belangrijkste stappen voor nieuwe instellingen voor gekoppelde service (REST):Below are key steps for new linked service (REST) settings:
    
     1. Geef **onder Basis-URL**de url-parameter op voor uw eigen bronREST-service. 
     2. Kies *Anoniem* **voor verificatietype**.
        ![Nieuwe REST-verbinding](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Maak een nieuwe verbinding voor Doelverbinding.  
    ![Nieuwe Gen2-verbinding](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Selecteer **Deze sjabloon gebruiken**.
    ![Deze sjabloon gebruiken](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. U ziet de pijplijn die is gemaakt ![zoals weergegeven in het volgende voorbeeld: Pijplijn](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Selecteer **Webactiviteit.** Geef in **Instellingen**de bijbehorende **URL**, **Methode**, **Kopteksten**en **Instantie** op om OAuth-token aan toonder op te halen uit de inlog-API van de service waarvan u gegevens wilt kopiëren. De tijdelijke aanduiding in de sjabloon toont een voorbeeld van Azure Active Directory (AAD) OAuth. Opmerking AAD-authenticatie wordt native ondersteund door REST-connector, hier is slechts een voorbeeld voor OAuth flow. 

    | Eigenschap | Beschrijving |
    |:--- |:--- |:--- |
    | URL |Geef de url op om OAuth-token aan toonder op te halen. bijvoorbeeld in het monster hier is hethttps://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Methode | De HTTP-methode. Toegestane waarden zijn **Post** en **Get**. | 
    | Headers | Koptekst is door de gebruiker gedefinieerd, die verwijst naar één kopnaam in de HTTP-aanvraag. | 
    | Hoofdtekst | De instantie voor het HTTP-verzoek. | 

    ![Pijplijn](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Selecteer in **Gegevensactiviteit kopiëren,** selecteer Tabblad *Bron,* u zien dat het token aan de drager (access_token) dat is opgehaald uit de vorige stap, wordt doorgegeven aan Gegevensactiviteit kopiëren als **Autorisatie** onder Aanvullende kopteksten. Bevestig de instellingen voor het volgen van eigenschappen voordat u een pijplijnrun start.

    | Eigenschap | Beschrijving |
    |:--- |:--- |:--- | 
    | Aanvraagmethode | De HTTP-methode. Toegestane waarden zijn **Get** (default) en **Post**. | 
    | Extra kopteksten | Aanvullende HTTP-aanvraagkoppen.| 

   ![Bronverificatie kopiëren](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.
   ![Pijplijnrun](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Wanneer de pijplijnrun is voltooid, ziet u het resultaat ![vergelijkbaar met het volgende voorbeeld: resultaat voor pijplijnuitvoering](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Klik op het pictogram 'Uitvoeren' van WebActiviteit in de kolom **Acties,** u ziet de access_token die door de service zijn geretourneerd.

   ![Tokenuitvoer](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Klik op het pictogram 'Invoer' van CopyActivity in de kolom **Handelingen,** u ziet dat de access_token die door WebActiviteit zijn opgehaald, wordt doorgegeven aan CopyActivity voor verificatie. 

    ![Tokeninvoer](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Om te voorkomen dat token wordt aangemeld in platte tekst, schakelt u 'Secure output' in Webactiviteit en 'Veilige invoer' in Kopieeractiviteit in.


## <a name="export-json-response-as-is"></a>Json-respons exporteren as-is

U deze REST-connector gebruiken om REST API JSON-respons as-is naar verschillende bestandenop te slaan te exporteren. Als u een dergelijke schema-agnostische kopie wilt bereiken, slaat u de sectie "structuur" (ook wel *schema*genoemd) in gegevensset en schematoewijzing in kopieeractiviteit over.

## <a name="schema-mapping"></a>Schematoewijzing

Als u gegevens wilt kopiëren van REST-eindpunt naar tabelgoot, raadpleegt u [schematoewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Volgende stappen

Zie [Ondersteunde gegevensopslag en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die activiteit kopiëren als bronnen en sinks in Azure Data Factory ondersteunt.
