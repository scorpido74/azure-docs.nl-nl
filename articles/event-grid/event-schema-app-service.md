---
title: Azure App Service als Event Grid bron
description: In dit artikel wordt beschreven hoe u Azure App Service als een Event Grid gebeurtenis bron gebruikt. Het bevat het schema en de koppelingen naar de zelf studie en de artikelen met procedures.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jafreebe
ms.openlocfilehash: 2465b2f260ed6c174b762fcf64a71100a148254d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106708"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor Azure App Service gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's. Het biedt ook een lijst met snel starten en zelf studies om Azure App Service als gebeurtenis bron te gebruiken.

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

### <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Azure App Service worden de volgende gebeurtenis typen verzonden

|    Gebeurtenistype                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Micro soft. web/sites. BackupOperationStarted             |    Geactiveerd wanneer een back-up is gestart                             |
|    Micro soft. web/sites. BackupOperationCompleted           |    Geactiveerd wanneer een back-up is voltooid                           |
|    Micro soft. web/sites. BackupOperationFailed              |    Geactiveerd wanneer een back-up is mislukt                              |
|    Micro soft. web/sites. RestoreOperationStarted            |    Geactiveerd wanneer een herstel bewerking van een back-up is gestart        |
|    Micro soft. web/sites. RestoreOperationCompleted          |    Geactiveerd wanneer een herstel bewerking van een back-up is voltooid      |
|    Micro soft. web/sites. RestoreOperationFailed             |    Geactiveerd wanneer een herstel bewerking van een back-up is mislukt         |
|    Micro soft. web/sites. SlotSwapStarted                    |    Geactiveerd wanneer een sleuf wisseling is gestart                          |
|    Micro soft. web/sites. SlotSwapCompleted                  |    Geactiveerd wanneer een sleuf wisseling is voltooid                      |
|    Micro soft. web/sites. SlotSwapFailed                     |    Geactiveerd wanneer het wisselen van een sleuf is mislukt                           |
|    Micro soft. web/sites. SlotSwapWithPreviewStarted         |    Wordt geactiveerd wanneer een sleuf wisseling met preview is gestart           |
|    Micro soft. web/sites. SlotSwapWithPreviewCancelled       |    Wordt geactiveerd wanneer een sleuf wisseling met preview is geannuleerd    |
|    Micro soft. web/sites. AppUpdated. opnieuw gestart               |    Geactiveerd wanneer een site opnieuw is opgestart                      |
|    Micro soft. web/sites. AppUpdated. gestopt                 |    Geactiveerd wanneer een site is gestopt                          |
|    Micro soft. web/sites. AppUpdated.ChangedAppSettings      |    Geactiveerd wanneer de app-instellingen van een site zijn gewijzigd             |
|    Micro soft. web/server farms. AppServicePlanUpdated        |    Geactiveerd wanneer een App Service plan wordt bijgewerkt                 |

### <a name="the-contents-of-an-event-response"></a>De inhoud van een gebeurtenis reactie

Wanneer een gebeurtenis wordt geactiveerd, verzendt de Event Grid-Service gegevens over die gebeurtenis om het eind punt te abonneren.
Deze sectie bevat een voor beeld van hoe de gegevens voor elke gebeurtenis eruitzien. Elke gebeurtenis heeft de volgende gegevens op het hoogste niveau:

|     Eigenschap          |     Type     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    onderwerp              |    tekenreeks    |    Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde.                                      |
|    Onderwerp            |    tekenreeks    |    Het door de uitgever gedefinieerde pad naar het gebeurtenis onderwerp.                                                                                              |
|    Type          |    tekenreeks    |    Een van de geregistreerde gebeurtenis typen voor deze gebeurtenis bron.                                                                                  |
|    eventTime          |    tekenreeks    |    Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider.                                                                         |
|    id                 |    tekenreeks    |    De unieke id voor de gebeurtenis.                                                                                                            |
|    gegevens               |    object    |    Gebeurtenis gegevens van Blob-opslag.                                                                                                                    |
|    dataVersion        |    tekenreeks    |    De schema versie van het gegevens object. De uitgever definieert de schemaversie.                                                          |
|    metadataVersion    |    tekenreeks    |    De schema versie van de meta gegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid levert deze waarde.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

Het gegevens object bevat de volgende eigenschappen:

|    Eigenschap                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Details van actie op de app                                                                                       |
|    action                  |    tekenreeks    |    Type actie van de bewerking                                                                                   |
|    naam                    |    tekenreeks    |    naam van de website waarvoor deze gebeurtenis is opgetreden                                                                          |
|    clientRequestId         |    tekenreeks    |    De client aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd         |
|    correlationRequestId    |    tekenreeks    |    De correlatie aanvraag-id die door de app service is gegenereerd voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd    |
|    requestId               |    tekenreeks    |    De aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd                |
|    adres                 |    tekenreeks    |    URL van de HTTP-aanvraag van deze bewerking                                                                                |
|    term                    |    tekenreeks    |    HTTP-woord van deze bewerking                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Het gegevens object bevat de volgende eigenschappen:

|    Eigenschap                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Details van actie op de app                                                                                       |
|    action                  |    tekenreeks    |    Type actie van de bewerking                                                                                   |
|    naam                    |    tekenreeks    |    naam van de website waarvoor deze gebeurtenis is opgetreden                                                                          |
|    clientRequestId         |    tekenreeks    |    De client aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd         |
|    correlationRequestId    |    tekenreeks    |    De correlatie aanvraag-id die door de app service is gegenereerd voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd    |
|    requestId               |    tekenreeks    |    De aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd                |
|    adres                 |    tekenreeks    |    URL van de HTTP-aanvraag van deze bewerking                                                                                |
|    term                    |    tekenreeks    |    HTTP-woord van deze bewerking                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Het gegevens object bevat de volgende eigenschappen:

|    Eigenschap                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Details van actie op de app                                                                                       |
|    action                  |    tekenreeks    |    Type actie van de bewerking                                                                                   |
|    naam                    |    tekenreeks    |    naam van de website waarvoor deze gebeurtenis is opgetreden                                                                          |
|    clientRequestId         |    tekenreeks    |    De client aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd         |
|    correlationRequestId    |    tekenreeks    |    De correlatie aanvraag-id die door de app service is gegenereerd voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd    |
|    requestId               |    tekenreeks    |    De aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd                |
|    adres                 |    tekenreeks    |    URL van de HTTP-aanvraag van deze bewerking                                                                                |
|    term                    |    tekenreeks    |    HTTP-woord van deze bewerking                                                                                       |
|    sourceSlot              |    tekenreeks    |    De bron sleuf van de wisseling                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Het gegevens object bevat de volgende eigenschappen:

|    Eigenschap                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Details van actie op de app                                                                                       |
|    action                  |    tekenreeks    |    Type actie van de bewerking                                                                                   |
|    naam                    |    tekenreeks    |    naam van de website waarvoor deze gebeurtenis is opgetreden                                                                          |
|    clientRequestId         |    tekenreeks    |    De client aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd         |
|    correlationRequestId    |    tekenreeks    |    De correlatie aanvraag-id die door de app service is gegenereerd voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd    |
|    requestId               |    tekenreeks    |    De aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd                |
|    adres                 |    tekenreeks    |    URL van de HTTP-aanvraag van deze bewerking                                                                                |
|    term                    |    tekenreeks    |    HTTP-woord van deze bewerking                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. started, AppUpdated. stopped, AppUpdated. ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Het gegevens object heeft de volgende eigenschappen:

|    Eigenschap                |    Type      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Details van actie op de app                                                                                       |
|    action                  |    tekenreeks    |    Type actie van de bewerking                                                                                   |
|    naam                    |    tekenreeks    |    naam van de website waarvoor deze gebeurtenis is opgetreden                                                                          |
|    clientRequestId         |    tekenreeks    |    De client aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd         |
|    correlationRequestId    |    tekenreeks    |    De correlatie aanvraag-id die door de app service is gegenereerd voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd    |
|    requestId               |    tekenreeks    |    De aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd                |
|    adres                 |    tekenreeks    |    URL van de HTTP-aanvraag van deze bewerking                                                                                |
|    term                    |    tekenreeks    |    HTTP-woord van deze bewerking                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Server farms. AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

Het gegevens object heeft de volgende eigenschappen:

|    Eigenschap                         |    Type      |    Description                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    object    |    Details van actie op het app service-plan                                                                          |
|    stampKind                        |    tekenreeks    |    Type omgeving waarin het app service-plan wordt                                                                     |
|    action                           |    tekenreeks    |    Type actie voor het app service-plan                                                                            |
|    status                           |    tekenreeks    |    Status van de bewerking op het app service-plan                                                                   |
|    sku                              |    object    |    SKU van het app service-plan                                                                                       |
|    naam                             |    tekenreeks    |    de naam van het app service-plan                                                                                      |
|    Laag                             |    tekenreeks    |    de laag van het app service-plan                                                                                      |
|    Grootte                             |    tekenreeks    |    grootte van het app service-plan                                                                                      |
|    Familie                           |    tekenreeks    |    app service-plan                                                                                        |
|    Capaciteit                         |    tekenreeks    |    capaciteit van het app service-plan                                                                                      |
|    action                           |    tekenreeks    |    Type actie van de bewerking                                                                                   |
|    naam                             |    tekenreeks    |    naam van de website waarvoor deze gebeurtenis is opgetreden                                                                          |
|    clientRequestId                  |    tekenreeks    |    De client aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd         |
|    correlationRequestId             |    tekenreeks    |    De correlatie aanvraag-id die door de app service is gegenereerd voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd    |
|    requestId                        |    tekenreeks    |    De aanvraag-id die is gegenereerd door de app service voor de site-API-bewerking die deze gebeurtenis heeft geactiveerd                |
|    adres                          |    tekenreeks    |    URL van de HTTP-aanvraag van deze bewerking                                                                                |
|    term                             |    tekenreeks    |    HTTP-woord van deze bewerking                                                                                       |
