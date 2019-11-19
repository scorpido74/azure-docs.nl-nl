---
title: Azure Event Grid-beveiliging en verificatie
description: Beschrijving van Azure Event Grid en de concepten ervan.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: dfa53acaf392e225873a40b05b8517de2f9780dc
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169564"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid-beveiliging en verificatie 

Azure Event Grid heeft drie typen verificatie:

* Levering van de WebHook-gebeurtenissen
* Gebeurtenisabonnementen
* Aangepast onderwerp publiceren

## <a name="webhook-event-delivery"></a>Levering van gebeurtenissen voor WebHook

Webhooks vormen een van de vele manieren voor het ontvangen van gebeurtenissen uit Azure Event Grid. Wanneer een nieuwe gebeurtenis klaar is, plaatst Event Grid-service een HTTP-aanvraag naar de geconfigureerde eindpunt met de gebeurtenis in de aanvraagtekst.

Net als vele andere services die webhooks ondersteunen, moet u eigenaar van de Webhook-eindpunt bewijzen voordat er begonnen wordt met het leveren van gebeurtenissen naar dit eindpunt Event Grid. Deze vereiste voorkomt dat een kwaadwillende gebruiker overbelasting van uw eindpunt met gebeurtenissen. Wanneer u een van de drie Azure-services die hieronder worden vermeld, wordt deze validatie automatisch verwerkt door de Azure-infrastructuur:

* Azure Logic Apps met [Event grid-connector](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions met [Event grid trigger](../azure-functions/functions-bindings-event-grid.md)

Als u een ander type eindpunt, zoals Azure-functie op basis van een HTTP-trigger, moet de code van uw eindpunt om deel te nemen in een validatie-handshake met Event Grid. Event Grid ondersteunt twee manieren voor het valideren van het abonnement.

1. **ValidationCode handshake (programmatisch)** : als u de broncode voor het eindpunt voor beheren, deze methode wordt aanbevolen. Op het moment van de event-abonnement maken verzendt Event Grid een gebeurtenis van de validatie van abonnement aan uw eindpunt. Het schema van deze gebeurtenis is vergelijkbaar met een andere Event Grid-gebeurtenis. Het gegevensgedeelte van deze gebeurtenis bevat een `validationCode` eigenschap. Uw toepassing gecontroleerd of de aanvraag voor de validatie voor een verwachte gebeurtenisabonnement, en kan de validatiecode naar Event Grid. Dit mechanisme handshake wordt ondersteund in alle versies van Event Grid.

2. **ValidationURL handshake (handmatig)** : In bepaalde gevallen, u geen toegang tot de broncode van het eindpunt voor het implementeren van de handshake ValidationCode. Bijvoorbeeld, als u een service van derden gebruiken (zoals [Zapier](https://zapier.com) of [IFTTT](https://ifttt.com/)), u via een programma kan niet reageren met de code voor validatie.

   Beginnen met 2018-05-01-preview-versie, ondersteunt Event Grid een handshake handmatig worden gevalideerd. Als u een gebeurtenisabonnement maakt met een SDK of een hulpprogramma dat gebruikmaakt van API-versie 2018-05-01-preview of hoger, Event Grid verzendt een `validationUrl` eigenschap in het gegevensgedeelte van het abonnement validatie-gebeurtenis. Voor het voltooien van de handshake vinden die URL in de gebeurtenisgegevens en handmatig een GET-aanvraag te verzenden. U kunt een REST-client of uw webbrowser.

   De beschik bare URL is 5 minuten geldig. Gedurende deze periode kan de Inrichtingsstatus van het gebeurtenisabonnement is `AwaitingManualAction`. Als u de hand matige validatie niet binnen vijf minuten voltooit, wordt de inrichtings status ingesteld op `Failed`. Hebt u het maken van het gebeurtenisabonnement opnieuw voordat u begint met het handmatig worden gevalideerd.

    Voor dit verificatie mechanisme moet ook het webhook-eind punt een HTTP-status code van 200 retour neren, zodat u weet dat het bericht voor de validatie gebeurtenis is geaccepteerd voordat het in de hand matige validatie modus kan worden geplaatst. Met andere woorden, als het eind punt 200 retourneert, maar niet via een programma een validatie reactie terugkeert, wordt de modus overgezet naar de hand matige validatie modus. Als er binnen vijf minuten een GET op de validatie-URL is, wordt de validatie-Handshake als geslaagd beschouwd.

> [!NOTE]
> Het gebruik van zelfondertekende certificaten voor validatie wordt niet ondersteund. Gebruik in plaats daarvan een ondertekend certificaat van een certificerings instantie (CA).

### <a name="validation-details"></a>Validatiedetails

* Event Grid berichten op het moment van gebeurtenis-abonnement maken/bijwerken, een abonnement validatiegebeurtenis naar het doel-eindpunt. 
* De gebeurtenis bevat de waarde van een header ' aeg gebeurtenistype: SubscriptionValidation '.
* De hoofdtekst van de gebeurtenis heeft hetzelfde schema als andere Event Grid-gebeurtenissen.
* De eigenschap type gebeurtenis van de gebeurtenis is `Microsoft.EventGrid.SubscriptionValidationEvent`.
* De eigenschap gegevens van de gebeurtenis bevat een `validationCode` eigenschap met een willekeurige tekenreeks. Bijvoorbeeld, "validationCode: acb13... '.
* De gebeurtenis gegevens bevatten ook een `validationUrl` eigenschap met een URL voor het hand matig valideren van het abonnement.
* De matrix bevat alleen de validatiegebeurtenis. Andere gebeurtenissen worden verzonden in een afzonderlijke aanvraag nadat u echo terug van de code voor validatie.
* De EventGrid DataPlane-SDK's zijn klassen die overeenkomt met de abonnement-validatie-gebeurtenisgegevens en abonnement validatie-antwoord.

Een voorbeeld SubscriptionValidationEvent wordt weergegeven in het volgende voorbeeld:

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

Om te bewijzen dat eindpunt eigendom, echo wordt teruggestuurd de validatiecode in de eigenschap validationResponse, zoals wordt weergegeven in het volgende voorbeeld:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

U moet een HTTP 200 OK-antwoord status code retour neren. HTTP 202-geaccepteerd wordt niet herkend als een geldig Event Grid antwoord op een abonnements validatie. De HTTP-aanvraag moet binnen 30 seconden worden voltooid. Als de bewerking niet binnen 30 seconden wordt voltooid, wordt de bewerking geannuleerd en wordt deze na vijf seconden opnieuw geprobeerd. Als alle pogingen mislukken, wordt deze behandeld als validatie-Handshake-fout.

Of u kunt handmatig het abonnement valideren door een GET-aanvraag verzenden naar de URL van de validatie. Het gebeurtenis abonnement blijft in de status in behandeling totdat het is gevalideerd. De validatie-URL maakt gebruik van poort 553. Als uw firewall regels poort 553 blok keren, moeten de regels mogelijk worden bijgewerkt voor een geslaagde hand matige handshake.

Zie voor een voorbeeld van het verwerken van de abonnement-handshake-validatie: een [ C# voorbeeld](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Controlelijst

Bij het maken van het gebeurtenis abonnement wordt er een fout bericht weer gegeven zoals ' de poging om het gegeven eind punt HTTPS te valideren:\//your-endpoint-Here is mislukt. Ga naar https:\//aka.ms/esvalidation ' voor meer informatie. Dit geeft aan dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen, controleert u of de volgende aspecten:

* Hebt u controle over de toepassingscode in de doel-eindpunt? Bijvoorbeeld, als u een HTTP-trigger op basis van Azure-functie schrijft, hebt u toegang tot de toepassingscode wijzigingen aanbrengen?
* Hebt u toegang tot de toepassingscode, de ValidationCode op basis van handshake-mechanisme implementeren zoals wordt weergegeven in het bovenstaande voorbeeld.

* Als u geen toegang tot de toepassingscode (bijvoorbeeld als u een service van derden die webhooks ondersteunen), kunt u de handmatige handshake-mechanisme. Zorg ervoor dat u de API-versie 2018-05-01-preview of hoger (installatie Event Grid-Azure CLI-extensie) voor het ontvangen van de validationUrl in de validatiegebeurtenis. Voor het voltooien van de handshake handmatig worden gevalideerd, haal de waarde van de `validationUrl` eigenschap en gaat u naar deze URL in uw webbrowser. Als de validatie is gelukt, ziet u een bericht in uw webbrowser validatie is gelukt. U ziet dat het gebeurtenisabonnement provisioningState is 'geslaagd'. 

### <a name="event-delivery-security"></a>Gebeurtenis levering beveiliging

#### <a name="azure-ad"></a>Azure AD

U kunt uw webhook-eind punt beveiligen met behulp van Azure Active Directory om Event Grid te verifiëren en te autoriseren voor het publiceren van gebeurtenissen naar uw eind punten. U moet een Azure Active Directory-toepassing maken, een rol-en service beginsel maken in uw toepassing Event Grid en het gebeurtenis abonnement configureren voor het gebruik van de Azure AD-toepassing. [Meer informatie over het configureren van Aad met Event grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Queryparameters
U kunt de webhook-eindpunt kunt beveiligen door queryparameters toevoegen aan de webhook-URL bij het maken van een gebeurtenisabonnement. Stel een van deze queryparameters moet een geheim, zoals een [toegangstoken](https://en.wikipedia.org/wiki/Access_token). De webhook kunt u het geheim gebruiken voor het herkennen van dat de gebeurtenis is afkomstig van Event Grid met geldige machtigingen. Event Grid bevat deze queryparameters in elke bezorging van gebeurtenissen naar de webhook.

Tijdens het bewerken van het gebeurtenisabonnement, de queryparameters worden niet weergegeven of geretourneerd, tenzij de [--opnemen-full-eindpunt-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parameter wordt gebruikt in Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Ten slotte is het belangrijk te weten dat Azure Event Grid biedt alleen ondersteuning voor HTTPS-webhook-eindpunten.

## <a name="event-subscription"></a>Gebeurtenisabonnement

U moet bewijzen dat u toegang tot de gebeurtenisbron en de handler hebt om u te abonneren op een gebeurtenis. Aan te tonen dat u eigenaar van een WebHook is in de voorgaande sectie behandeld. Als u een gebeurtenis-handler die niet van een WebHook (zoals een event hub of queue-opslag), moet u toegang voor schrijven naar die resource. Deze machtigingscontrole voorkomt dat een onbevoegde gebruiker verzenden van gebeurtenissen naar uw resource.

Hebt u de **Microsoft.EventGrid/EventSubscriptions/Write** machtiging voor de desbetreffende bron van de gebeurtenis. U moet deze machtiging namelijk u een nieuw abonnement in het bereik van de resource schrijft. De vereiste resource verschilt afhankelijk van of u bent u zich op een systeemonderwerp of een aangepast onderwerp abonneert. Beide typen worden beschreven in deze sectie.

### <a name="system-topics-azure-service-publishers"></a>Systeemonderwerpen (uitgevers voor Azure-service)

Voor onderwerpen over het systeem moet u machtigingen voor schrijven van een nieuw gebeurtenisabonnement in het bereik van de resource publiceren van de gebeurtenis. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Bijvoorbeeld, om u te abonneren op een gebeurtenis op een storage-account met de naam **MIJNACCT**, moet u de machtiging Microsoft.EventGrid/EventSubscriptions/Write op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Aangepaste onderwerpen

Voor aangepaste onderwerpen moet u machtigingen voor schrijven van een nieuw gebeurtenisabonnement in het bereik van de event grid-onderwerp. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Bijvoorbeeld, om u te abonneren op een aangepast onderwerp met de naam **mytopic**, moet u de machtiging Microsoft.EventGrid/EventSubscriptions/Write op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Aangepast onderwerp publiceren

Aangepaste onderwerpen Shared Access Signature (SAS) of sleutelverificatie gebruiken. We raden SAS, maar sleutelverificatie biedt eenvoudige programmeren en is compatibel met veel bestaande webhook uitgevers. 

De verificatie-waarde in de HTTP-header te nemen. Gebruik voor SAS, **aeg-sas-token** voor de headerwaarde. Gebruik voor verificatie met een sleutel, **aeg-sas-sleutel** voor de headerwaarde.

### <a name="key-authentication"></a>Verificatie met een sleutel

Verificatie met een sleutel is de eenvoudigste vorm van verificatie. Gebruik de volgende indeling: `aeg-sas-key: <your key>`

Bijvoorbeeld, doorgeven u een sleutel met:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokens

SAS-tokens voor Event Grid zijn de bron, een verlooptijd en een handtekening. De indeling van de SAS-token is: `r={resource}&e={expiration}&s={signature}`.

De resource is het pad voor de event grid-onderwerp waarmee u gebeurtenissen verzendt. Bijvoorbeeld, is een geldige resource-pad: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

U de handtekening van een sleutel genereren.

Bijvoorbeeld, een geldige **aeg-sas-token** waarde is:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Het volgende voorbeeld wordt een SAS-token voor gebruik met Event Grid:

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

## <a name="management-access-control"></a>Beheer van Access Control

Azure Event Grid kunt u bepalen het niveau van toegang krijgen tot verschillende gebruikers verschillende beheerbewerkingen zoals gebeurtenisabonnementen lijst, een nieuwe maken en genereren van sleutels. Event Grid maakt gebruik van toegangsbeheer van Azure op basis van rollen (RBAC).

### <a name="operation-types"></a>Bewerkingstypen

Event Grid ondersteunt de volgende acties:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

De laatste drie bewerkingen retourneren mogelijk geheime gegevens die buiten het normale leesbewerkingen wordt gefilterd. Het raadzaam dat u toegang tot deze bewerkingen beperken. 

### <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid biedt twee ingebouwde rollen voor het beheren van abonnementen. Ze zijn belangrijk bij het implementeren van [gebeurtenis domeinen](event-domains.md) omdat ze gebruikers geven de machtigingen die ze nodig hebben om u te abonneren op onderwerpen in het domein van de gebeurtenis. Deze rollen zijn gericht op gebeurtenisabonnementen en geen toegangsmachtigingen voor acties zoals het maken van onderwerpen.

U kunt [deze rollen toewijzen aan een gebruiker of groep](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Inzender**: Event grid-abonnements bewerkingen beheren

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
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

**EventGrid EventSubscription-lezer**: Event grid-abonnementen lezen

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
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

Als u nodig hebt om op te geven van de machtigingen die verschillen van de ingebouwde rollen, kunt u aangepaste rollen maken.

Hieronder vindt u voorbeeld Event Grid roldefinities die toestaat dat gebruikers verschillende acties uitvoeren. Deze aangepaste rollen wijken af van de ingebouwde rollen, omdat ze uitgebreidere toegang dan alleen gebeurtenisabonnementen verlenen.

**EventGridReadOnlyRole.json**: alleen kunnen alleen-lezen bewerkingen.

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

**EventGridNoDeleteListKeysRole.json**: toestaan, maar weigeren verwijderingsacties beperkte post-acties.

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

**EventGridContributorRole.json**: kunnen alle event grid-acties.

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

U kunt maken van aangepaste rollen met [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md), en [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Volgende stappen

* Zie voor een inleiding tot Event Grid, [over Event Grid](overview.md)
