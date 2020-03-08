---
title: Azure Event Grid-beveiliging en verificatie
description: In dit artikel worden verschillende manieren beschreven om toegang te verifiëren tot uw Event Grid-resources (webhook, abonnementen, aangepaste onderwerpen)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924717"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Toegang tot Event Grid-bronnen verifiëren

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

1. **ValidationCode-Handshake (programmatisch)** : als u de bron code voor het eind punt beheert, wordt deze methode aanbevolen. Op het moment van de event-abonnement maken verzendt Event Grid een gebeurtenis van de validatie van abonnement aan uw eindpunt. Het schema van deze gebeurtenis is vergelijkbaar met een andere Event Grid-gebeurtenis. Het gegevens gedeelte van deze gebeurtenis bevat een `validationCode` eigenschap. Uw toepassing gecontroleerd of de aanvraag voor de validatie voor een verwachte gebeurtenisabonnement, en kan de validatiecode naar Event Grid. Dit mechanisme handshake wordt ondersteund in alle versies van Event Grid.

2. **ValidationURL Handshake (hand matig)** : in bepaalde gevallen hebt u geen toegang tot de bron code van het eind punt om de ValidationCode-Handshake te implementeren. Als u bijvoorbeeld gebruikmaakt van een service van derden (zoals [Zapier](https://zapier.com) of [IFTTT](https://ifttt.com/)), kunt u niet programmatisch met de validatie code reageren.

   Beginnen met 2018-05-01-preview-versie, ondersteunt Event Grid een handshake handmatig worden gevalideerd. Als u een gebeurtenis abonnement maakt met een SDK of een hulp programma dat API-versie 2018-05-01 (preview of hoger) gebruikt, verzendt Event Grid een `validationUrl`-eigenschap in het gedeelte gegevens van de validatie gebeurtenis voor het abonnement. Voor het voltooien van de handshake vinden die URL in de gebeurtenisgegevens en handmatig een GET-aanvraag te verzenden. U kunt een REST-client of uw webbrowser.

   De beschik bare URL is 5 minuten geldig. Gedurende die tijd wordt de inrichtings status van het gebeurtenis abonnement `AwaitingManualAction`. Als u de hand matige validatie niet binnen vijf minuten voltooit, wordt de inrichtings status ingesteld op `Failed`. Hebt u het maken van het gebeurtenisabonnement opnieuw voordat u begint met het handmatig worden gevalideerd.

    Voor dit verificatie mechanisme moet ook het webhook-eind punt een HTTP-status code van 200 retour neren, zodat u weet dat het bericht voor de validatie gebeurtenis is geaccepteerd voordat het in de hand matige validatie modus kan worden geplaatst. Met andere woorden, als het eind punt 200 retourneert, maar niet via een programma een validatie reactie terugkeert, wordt de modus overgezet naar de hand matige validatie modus. Als er binnen vijf minuten een GET op de validatie-URL is, wordt de validatie-Handshake als geslaagd beschouwd.

> [!NOTE]
> Het gebruik van zelfondertekende certificaten voor validatie wordt niet ondersteund. Gebruik in plaats daarvan een ondertekend certificaat van een certificerings instantie (CA).

### <a name="validation-details"></a>Validatiedetails

* Event Grid berichten op het moment van gebeurtenis-abonnement maken/bijwerken, een abonnement validatiegebeurtenis naar het doel-eindpunt. 
* De gebeurtenis bevat de waarde van een header ' aeg gebeurtenistype: SubscriptionValidation '.
* De hoofdtekst van de gebeurtenis heeft hetzelfde schema als andere Event Grid-gebeurtenissen.
* De eigenschap Event type van de gebeurtenis is `Microsoft.EventGrid.SubscriptionValidationEvent`.
* De eigenschap data van de gebeurtenis bevat een `validationCode` eigenschap met een wille keurig gegenereerde teken reeks. Bijvoorbeeld, "validationCode: acb13... '.
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

Of u kunt handmatig het abonnement valideren door een GET-aanvraag verzenden naar de URL van de validatie. Het gebeurtenisabonnement blijft in behandeling totdat gevalideerd. De validatie-URL maakt gebruik van poort 553. Als uw firewall regels poort 553 blok keren, moeten de regels mogelijk worden bijgewerkt voor een geslaagde hand matige handshake.

Voor een voor beeld van het verwerken van de handshake voor het [ C# ](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)valideren van het abonnement, raadpleegt u een voor beeld.

### <a name="checklist"></a>Controlelijst

Bij het maken van het gebeurtenis abonnement wordt er een fout bericht weer gegeven zoals ' de poging om het gegeven eind punt HTTPS te valideren:\//your-endpoint-Here is mislukt. Ga naar https:\//aka.ms/esvalidation ' voor meer informatie. Dit geeft aan dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen, controleert u of de volgende aspecten:

* Beheert u de toepassings code die wordt uitgevoerd in het doel eindpunt? Bijvoorbeeld, als u een HTTP-trigger op basis van Azure-functie schrijft, hebt u toegang tot de toepassingscode wijzigingen aanbrengen?
* Hebt u toegang tot de toepassingscode, de ValidationCode op basis van handshake-mechanisme implementeren zoals wordt weergegeven in het bovenstaande voorbeeld.

* Als u geen toegang tot de toepassingscode (bijvoorbeeld als u een service van derden die webhooks ondersteunen), kunt u de handmatige handshake-mechanisme. Zorg ervoor dat u de API-versie 2018-05-01-preview of hoger (installatie Event Grid-Azure CLI-extensie) voor het ontvangen van de validationUrl in de validatiegebeurtenis. Als u de hand matige validatie-Handshake wilt volt ooien, haalt u de waarde van de eigenschap `validationUrl` op en gaat u naar die URL in uw webbrowser. Als de validatie is gelukt, ziet u een bericht in uw webbrowser validatie is gelukt. U ziet dat het gebeurtenisabonnement provisioningState is 'geslaagd'. 

### <a name="event-delivery-security"></a>Gebeurtenis levering beveiliging

#### <a name="azure-ad"></a>Azure AD

U kunt uw webhook-eind punt beveiligen met behulp van Azure Active Directory om Event Grid te verifiëren en te autoriseren voor het publiceren van gebeurtenissen naar uw eind punten. U moet een Azure Active Directory-toepassing maken, een rol-en service beginsel maken in uw toepassing Event Grid en het gebeurtenis abonnement configureren voor het gebruik van de Azure AD-toepassing. [Meer informatie over het configureren van Aad met Event grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Queryparameters
U kunt de webhook-eindpunt kunt beveiligen door queryparameters toevoegen aan de webhook-URL bij het maken van een gebeurtenisabonnement. Stel een van deze query parameters in op een geheim, zoals een [toegangs token](https://en.wikipedia.org/wiki/Access_token). De webhook kunt u het geheim gebruiken voor het herkennen van dat de gebeurtenis is afkomstig van Event Grid met geldige machtigingen. Event Grid bevat deze queryparameters in elke bezorging van gebeurtenissen naar de webhook.

Bij het bewerken van het gebeurtenis abonnement worden de query parameters niet weer gegeven of geretourneerd, tenzij de para meter [--include-Full-endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) wordt gebruikt in azure [cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Ten slotte is het belangrijk te weten dat Azure Event Grid biedt alleen ondersteuning voor HTTPS-webhook-eindpunten.

## <a name="event-subscription"></a>Gebeurtenisabonnement

U moet bewijzen dat u toegang tot de gebeurtenisbron en de handler hebt om u te abonneren op een gebeurtenis. Aan te tonen dat u eigenaar van een WebHook is in de voorgaande sectie behandeld. Als u een gebeurtenis-handler die niet van een WebHook (zoals een event hub of queue-opslag), moet u toegang voor schrijven naar die resource. Deze machtigingscontrole voorkomt dat een onbevoegde gebruiker verzenden van gebeurtenissen naar uw resource.

U moet beschikken over de machtiging **micro soft. EventGrid/EventSubscriptions/write** voor de resource die de bron van de gebeurtenis is. U moet deze machtiging namelijk u een nieuw abonnement in het bereik van de resource schrijft. De vereiste resource verschilt afhankelijk van of u bent u zich op een systeemonderwerp of een aangepast onderwerp abonneert. Beide typen worden beschreven in deze sectie.

### <a name="system-topics-azure-service-publishers"></a>Systeemonderwerpen (uitgevers voor Azure-service)

Voor onderwerpen over het systeem moet u machtigingen voor schrijven van een nieuw gebeurtenisabonnement in het bereik van de resource publiceren van de gebeurtenis. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Als u zich bijvoorbeeld wilt abonneren op een gebeurtenis in een opslag account met de naam **myacct**, hebt u de machtiging micro soft. EventGrid/EventSubscriptions/write nodig op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Aangepaste onderwerpen

Voor aangepaste onderwerpen moet u machtigingen voor schrijven van een nieuw gebeurtenisabonnement in het bereik van de event grid-onderwerp. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Als u zich bijvoorbeeld wilt abonneren op een aangepast onderwerp met de naam **mytopic**, moet u de machtiging micro soft. EventGrid/EventSubscriptions/write hebben op: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Aangepast onderwerp publiceren

Aangepaste onderwerpen Shared Access Signature (SAS) of sleutelverificatie gebruiken. We raden SAS, maar sleutelverificatie biedt eenvoudige programmeren en is compatibel met veel bestaande webhook uitgevers. 

De verificatie-waarde in de HTTP-header te nemen. Voor SAS gebruikt u **AEG-SAS-token** voor de waarde van de header. Gebruik voor sleutel verificatie **AEG-SAS-sleutel** voor de waarde van de header.

### <a name="key-authentication"></a>Verificatie met een sleutel

Verificatie met een sleutel is de eenvoudigste vorm van verificatie. Gebruik de indeling: `aeg-sas-key: <your key>`

Bijvoorbeeld, doorgeven u een sleutel met:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokens

SAS-tokens voor Event Grid zijn de bron, een verlooptijd en een handtekening. De indeling van de SAS-token is: `r={resource}&e={expiration}&s={signature}`.

De resource is het pad voor de event grid-onderwerp waarmee u gebeurtenissen verzendt. Een geldig bronpad is bijvoorbeeld: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

U de handtekening van een sleutel genereren.

Een geldige waarde voor **AEG-SAS-token** is bijvoorbeeld:

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

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Alle gebeurtenissen of gegevens die door de Event Grid-Service naar schijf worden geschreven, worden versleuteld met een door micro soft beheerde sleutel, zodat deze op rest versleuteld is. Bovendien is de maximale tijds duur dat gebeurtenissen of gegevens bewaard 24 uur in acht te komen in de [Event grid beleid voor opnieuw proberen](delivery-and-retry.md). Event Grid verwijdert automatisch alle gebeurtenissen of gegevens na 24 uur, of de TTL van de gebeurtenis, afhankelijk van wat kleiner is.

## <a name="next-steps"></a>Volgende stappen

* Zie [About Event grid](overview.md) voor een inleiding tot Event grid.
