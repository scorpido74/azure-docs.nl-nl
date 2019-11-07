---
title: Beveiliging en verificatie Azure Event Grid
description: Beschrijving van Azure Event Grid en de concepten ervan.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: b9e471928940094b29bdffeb73ea42fe852492cb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665580"
---
# <a name="event-grid-security-and-authentication"></a>Beveiliging en verificatie Event Grid 

Azure Event Grid heeft drie soorten verificatie:

* Overdracht van gebeurtenis van webhook
* Gebeurtenisabonnementen
* Aangepaste onderwerpen publiceren

## <a name="webhook-event-delivery"></a>Overdracht van gebeurtenis van webhook

Webhooks zijn een van de vele manieren om gebeurtenissen van Azure Event Grid te ontvangen. Wanneer een nieuwe gebeurtenis gereed is, boekt Event Grid-Service een HTTP-aanvraag naar het geconfigureerde eind punt met de gebeurtenis in de hoofd tekst van de aanvraag.

Net als bij veel andere services die webhooks ondersteunen, moet Event Grid het eigendom van het webhook-eind punt bewijzen voordat ze gebeurtenissen aan dat eind punt kunnen leveren. Deze vereiste voor komt dat een kwaadwillende gebruiker uw eind punt kan overlopen met gebeurtenissen. Wanneer u een van de drie onderstaande Azure-Services gebruikt, wordt deze validatie automatisch door de Azure-infra structuur afgehandeld:

* Azure Logic Apps met [Event grid-connector](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions met [Event grid trigger](../azure-functions/functions-bindings-event-grid.md)

Als u een ander type eind punt gebruikt, zoals een op een HTTP-trigger gebaseerde Azure-functie, moet uw eindpunt code deel nemen aan een validatie-Handshake met Event Grid. Event Grid ondersteunt twee manieren om het abonnement te valideren.

1. **ValidationCode-Handshake (programmatisch)** : als u de bron code voor het eind punt beheert, wordt deze methode aanbevolen. Op het moment dat het gebeurtenis abonnement wordt gemaakt, wordt met Event Grid een validatie gebeurtenis voor abonnementen naar uw eind punt verzonden. Het schema van deze gebeurtenis is vergelijkbaar met een andere Event Grid gebeurtenis. Het gegevens gedeelte van deze gebeurtenis bevat een `validationCode` eigenschap. Uw toepassing controleert of de validatie aanvraag voor een verwacht gebeurtenis abonnement is en ECHO de validatie code op Event Grid. Dit handshake-mechanisme wordt in alle Event Grid versies ondersteund.

2. **ValidationURL Handshake (hand matig)** : in bepaalde gevallen hebt u geen toegang tot de bron code van het eind punt om de ValidationCode-Handshake te implementeren. Als u bijvoorbeeld gebruikmaakt van een service van derden (zoals [Zapier](https://zapier.com) of [IFTTT](https://ifttt.com/)), kunt u niet programmatisch met de validatie code reageren.

   Vanaf versie 2018-05-01-preview ondersteunt Event Grid een hand matige validatie-handshake. Als u een gebeurtenis abonnement maakt met een SDK of een hulp programma dat API-versie 2018-05-01 (preview of hoger) gebruikt, verzendt Event Grid een `validationUrl`-eigenschap in het gedeelte gegevens van de validatie gebeurtenis voor het abonnement. Als u de handshake wilt volt ooien, zoekt u die URL in de gebeurtenis gegevens en verzendt u hand matig een GET-aanvraag naar de. U kunt een REST-client of uw webbrowser gebruiken.

   De beschik bare URL is 5 minuten geldig. Gedurende die tijd wordt de inrichtings status van het gebeurtenis abonnement `AwaitingManualAction`. Als u de hand matige validatie niet binnen vijf minuten voltooit, wordt de inrichtings status ingesteld op `Failed`. U moet het gebeurtenis abonnement opnieuw maken voordat u de hand matige validatie start.

    Voor dit verificatie mechanisme moet ook het webhook-eind punt een HTTP-status code van 200 retour neren, zodat u weet dat het bericht voor de validatie gebeurtenis is geaccepteerd voordat het in de hand matige validatie modus kan worden geplaatst. Met andere woorden, als het eind punt 200 retourneert, maar niet via een programma een validatie reactie terugkeert, wordt de modus overgezet naar de hand matige validatie modus. Als er binnen vijf minuten een GET op de validatie-URL is, wordt de validatie-Handshake als geslaagd beschouwd.

> [!NOTE]
> Het gebruik van zelfondertekende certificaten voor validatie wordt niet ondersteund. Gebruik in plaats daarvan een ondertekend certificaat van een certificerings instantie (CA).

### <a name="validation-details"></a>Validatie Details

* Op het moment dat het maken of bijwerken van het gebeurtenis abonnement wordt gemaakt, Event Grid een validatie gebeurtenis voor het abonnement op het eind punt van het doel. 
* De gebeurtenis bevat een header waarde "AEG-Event-type: SubscriptionValidation".
* De hoofd tekst van de gebeurtenis heeft hetzelfde schema als andere Event Grid-gebeurtenissen.
* De eigenschap Event type van de gebeurtenis is `Microsoft.EventGrid.SubscriptionValidationEvent`.
* De eigenschap data van de gebeurtenis bevat een `validationCode` eigenschap met een wille keurig gegenereerde teken reeks. Bijvoorbeeld "validationCode: acb13...".
* De gebeurtenis gegevens bevatten ook een `validationUrl` eigenschap met een URL voor het hand matig valideren van het abonnement.
* De matrix bevat alleen de validatie gebeurtenis. Andere gebeurtenissen worden verzonden in een afzonderlijke aanvraag nadat u de validatie code hebt teruggestuurd.
* De EventGrid DataPlane Sdk's hebben klassen die overeenkomen met de validatie van de gebeurtenis gegevens van het abonnement en het antwoord op het abonnement.

In het volgende voor beeld wordt een voor beeld van SubscriptionValidationEvent weer gegeven:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Als u het eigendom van het eind punt wilt bewijzen, keert u terug naar de validatie code in de eigenschap validationResponse, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

U moet een HTTP 200 OK-antwoord status code retour neren. HTTP 202-geaccepteerd wordt niet herkend als een geldig Event Grid antwoord op een abonnements validatie. De HTTP-aanvraag moet binnen 30 seconden worden voltooid. Als de bewerking niet binnen 30 seconden wordt voltooid, wordt de bewerking geannuleerd en wordt deze na vijf seconden opnieuw geprobeerd. Als alle pogingen mislukken, wordt deze behandeld als validatie-Handshake-fout.

Of u kunt het abonnement hand matig valideren door een GET-aanvraag naar de validatie-URL te verzenden. Het gebeurtenis abonnement blijft in de status in behandeling totdat het is gevalideerd. De validatie-URL maakt gebruik van poort 553. Als uw firewall regels poort 553 blok keren, moeten de regels mogelijk worden bijgewerkt voor een geslaagde hand matige handshake.

Voor een voor beeld van het verwerken van de handshake voor het [ C# ](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)valideren van het abonnement, raadpleegt u een voor beeld.

### <a name="checklist"></a>Controlelijst

Bij het maken van het gebeurtenis abonnement wordt er een fout bericht weer gegeven zoals ' de poging om het gegeven eind punt HTTPS te valideren:\//your-endpoint-Here is mislukt. Ga naar https:\//aka.ms/esvalidation ' voor meer informatie. Dit geeft aan dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen door de volgende aspecten te controleren:

* Hebt u controle over de toepassings code in het doel eindpunt? Als u bijvoorbeeld een Azure-trigger op basis van HTTP-triggers schrijft, hebt u toegang tot de toepassings code om wijzigingen aan te brengen?
* Als u toegang hebt tot de toepassings code, implementeert u het ValidationCode op basis van het handshake-mechanisme zoals weer gegeven in het bovenstaande voor beeld.

* Als u geen toegang hebt tot de toepassings code (bijvoorbeeld als u een service van derden gebruikt die webhooks ondersteunt), kunt u het hand matige handshake-mechanisme gebruiken. Zorg ervoor dat u de API-preview-versie van 2018-05-01 of hoger gebruikt (Installeer Event Grid Azure CLI-extensie) om de validationUrl in de validatie gebeurtenis te ontvangen. Als u de hand matige validatie-Handshake wilt volt ooien, haalt u de waarde van de eigenschap `validationUrl` op en gaat u naar die URL in uw webbrowser. Als de validatie is geslaagd, ziet u een bericht in de webbrowser dat de validatie is voltooid. U ziet dat de provisioningState van het gebeurtenis abonnement is geslaagd. 

### <a name="event-delivery-security"></a>Gebeurtenis leverings beveiliging

U kunt uw webhook-eind punt beveiligen door query parameters toe te voegen aan de webhook-URL bij het maken van een gebeurtenis abonnement. Stel een van deze query parameters in op een geheim, zoals een [toegangs token](https://en.wikipedia.org/wiki/Access_token). De webhook kan het geheim gebruiken om te herkennen dat de gebeurtenis afkomstig is van Event Grid met geldige machtigingen. Event Grid neemt deze query parameters op in elke gebeurtenis levering aan de webhook.

Bij het bewerken van het gebeurtenis abonnement worden de query parameters niet weer gegeven of geretourneerd, tenzij de para meter [--include-Full-endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) wordt gebruikt in azure [cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Ten slotte is het belang rijk te weten dat Azure Event Grid alleen HTTPS-webhook-eind punten ondersteunt.

## <a name="event-subscription"></a>Gebeurtenis abonnement

Als u zich wilt abonneren op een gebeurtenis, moet u bewijzen dat u toegang hebt tot de gebeurtenis bron en handler. Bewijs dat u een webhook hebt, werd in de voor gaande sectie besproken. Als u een gebeurtenis-handler gebruikt die geen webhook is (zoals een Event Hub of een wachtrij opslag), moet u schrijf toegang hebben voor die bron. Met deze machtigingen kunt u voor komen dat een niet-geautoriseerde gebruiker gebeurtenissen naar uw resource verzendt.

Hebt u de **Microsoft.EventGrid/EventSubscriptions/Write** machtiging voor de desbetreffende bron van de gebeurtenis. U hebt deze machtiging nodig omdat u een nieuw abonnement op het bereik van de resource schrijft. De vereiste resource wijkt af van de vraag of u zich abonneert op een systeem onderwerp of een aangepast onderwerp. Beide typen worden beschreven in deze sectie.

### <a name="system-topics-azure-service-publishers"></a>Systeem onderwerpen (Azure service Publishers)

Voor systeem onderwerpen hebt u toestemming nodig voor het schrijven van een nieuw gebeurtenis abonnement op het bereik van de resource die de gebeurtenis publiceert. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Als u zich bijvoorbeeld wilt abonneren op een gebeurtenis in een opslag account met de naam **myacct**, hebt u de machtiging micro soft. EventGrid/EventSubscriptions/write nodig op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Aangepaste onderwerpen

Voor aangepaste onderwerpen hebt u toestemming nodig voor het schrijven van een nieuw gebeurtenis abonnement in het bereik van het event grid-onderwerp. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Als u zich bijvoorbeeld wilt abonneren op een aangepast onderwerp met de naam **mytopic**, moet u de machtiging micro soft. EventGrid/EventSubscriptions/write hebben op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Aangepaste onderwerpen publiceren

Aangepaste onderwerpen gebruiken een Shared Access Signature (SAS) of sleutel verificatie. We raden SAS aan, maar sleutel verificatie biedt eenvoudige programmering en is compatibel met veel bestaande webhook-Publishers. 

U neemt de verificatie waarde op in de HTTP-header. Voor SAS gebruikt u **AEG-SAS-token** voor de waarde van de header. Gebruik voor sleutel verificatie **AEG-SAS-sleutel** voor de waarde van de header.

### <a name="key-authentication"></a>Sleutel verificatie

Sleutel verificatie is de eenvoudigste vorm van verificatie. Gebruik de indeling: `aeg-sas-key: <your key>`

U geeft bijvoorbeeld een sleutel door:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokens

SAS-tokens voor Event Grid bevatten de resource, een verloop tijd en een hand tekening. De indeling van de SAS-token is: `r={resource}&e={expiration}&s={signature}`.

De resource is het pad naar het event grid-onderwerp waarnaar u gebeurtenissen verzendt. Een geldig bronpad is bijvoorbeeld: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

U kunt de hand tekening genereren op basis van een sleutel.

Een geldige waarde voor **AEG-SAS-token** is bijvoorbeeld:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

In het volgende voor beeld wordt een SAS-token gemaakt voor gebruik met Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Beheer Access Control

Met Azure Event Grid kunt u het toegangs niveau dat aan verschillende gebruikers wordt gegeven, beheren om verschillende beheer bewerkingen uit te voeren, zoals abonnementen op lijst gebeurtenissen, nieuwe maken en sleutels genereren. Event Grid maakt gebruik van het op rollen gebaseerd toegangs beheer (RBAC) van Azure.

### <a name="operation-types"></a>Bewerkings typen

Event Grid ondersteunt de volgende acties:

* Micro soft. EventGrid/*/Read
* Micro soft. EventGrid/*/write
* Micro soft. EventGrid/*/Delete
* Micro soft. EventGrid/eventSubscriptions/getFullUrl/Action
* Micro soft. EventGrid/topics/Listkeys ophalen/Action
* Micro soft. EventGrid/topics/regenerateKey/Action

Met de laatste drie bewerkingen wordt mogelijk geheime informatie geretourneerd, waardoor de normale Lees bewerkingen worden gefilterd. Het is raadzaam de toegang tot deze bewerkingen te beperken. 

### <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid biedt twee ingebouwde rollen voor het beheren van gebeurtenis abonnementen. Ze zijn belang rijk bij het implementeren van [gebeurtenis domeinen](event-domains.md) omdat ze gebruikers de machtigingen geven die ze nodig hebben om zich te abonneren op onderwerpen in uw gebeurtenis domein. Deze rollen zijn gericht op gebeurtenis abonnementen en verlenen geen toegang voor acties zoals het maken van onderwerpen.

U kunt [deze rollen toewijzen aan een gebruiker of groep](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Inzender (preview)** : Event grid-abonnements bewerkingen beheren

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader (preview)** : Event grid-abonnementen lezen

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Aangepaste rollen

Als u machtigingen wilt opgeven die afwijken van de ingebouwde rollen, kunt u aangepaste rollen maken.

Hier volgen enkele voor beelden van Event Grid roldefinities waarmee gebruikers verschillende acties kunnen uitvoeren. Deze aangepaste rollen verschillen van de ingebouwde rollen omdat ze bredere toegang verlenen dan alleen gebeurtenis abonnementen.

**EventGridReadOnlyRole. json**: alleen alleen-lezen bewerkingen toestaan.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole. json**: beperkte post acties toestaan, maar geen verwijderings acties toestaan.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole. json**: alle gebeurtenis raster acties toestaan.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

U kunt aangepaste rollen maken met [Power shell](../role-based-access-control/custom-roles-powershell.md), [Azure cli](../role-based-access-control/custom-roles-cli.md)en [rest](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [About Event grid](overview.md) voor een inleiding tot Event grid.
