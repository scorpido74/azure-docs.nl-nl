---
title: Beveiliging en verificatie Azure Event Grid
description: In dit artikel worden verschillende manieren beschreven om toegang te verifiëren tot uw Event Grid-resources (webhook, abonnementen, aangepaste onderwerpen)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532390"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Toegang tot Event Grid-bronnen verifiëren

Azure Event Grid heeft drie soorten verificatie:

- Overdracht van gebeurtenis van webhook
- Gebeurtenisabonnementen
- Aangepaste onderwerpen publiceren

## <a name="webhook-event-delivery"></a>Overdracht van gebeurtenis van webhook

Webhooks zijn een van de vele manieren om gebeurtenissen van Azure Event Grid te ontvangen. Wanneer een nieuwe gebeurtenis gereed is, boekt Event Grid-Service een HTTP-aanvraag naar het geconfigureerde eind punt met de gebeurtenis in de hoofd tekst van de aanvraag.

Net als bij veel andere services die webhooks ondersteunen, moet Event Grid het eigendom van het webhook-eind punt bewijzen voordat ze gebeurtenissen aan dat eind punt kunnen leveren. Deze vereiste voor komt dat een kwaadwillende gebruiker uw eind punt kan overlopen met gebeurtenissen. Wanneer u een van de drie onderstaande Azure-Services gebruikt, wordt deze validatie automatisch door de Azure-infra structuur afgehandeld:

- Azure Logic Apps met [Event grid-connector](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions met [Event grid trigger](../azure-functions/functions-bindings-event-grid.md)

Als u een ander type eind punt gebruikt, zoals een op een HTTP-trigger gebaseerde Azure-functie, moet uw eindpunt code deel nemen aan een validatie-Handshake met Event Grid. Event Grid ondersteunt twee manieren om het abonnement te valideren.

1. **Synchrone Handshake**: op het moment van het maken van het gebeurtenis abonnement wordt Event grid een validatie gebeurtenis voor het abonnement naar uw eind punt verzonden. Het schema van deze gebeurtenis is vergelijkbaar met een andere Event Grid gebeurtenis. Het gegevens gedeelte van deze gebeurtenis bevat een `validationCode` eigenschap. Uw toepassing controleert of de validatie aanvraag voor een verwacht gebeurtenis abonnement is en retourneert de validatie code in het antwoord synchroon. Dit handshake-mechanisme wordt in alle Event Grid versies ondersteund.

2. **Asynchrone Handshake**: in bepaalde gevallen kunt u de ValidationCode in antwoord niet synchroon retour neren. Als u bijvoorbeeld gebruikmaakt van een service van derden (zoals [`Zapier`](https://zapier.com) of [IFTTT](https://ifttt.com/)), kunt u niet programmatisch met de validatie code reageren.

   Vanaf versie 2018-05-01-preview ondersteunt Event Grid een hand matige validatie-handshake. Als u een gebeurtenis abonnement met een SDK of een hulp programma maakt die gebruikmaakt van API versie 2018-05-01-preview of hoger, wordt `validationUrl` Event grid een eigenschap verzonden in het gedeelte gegevens van de validatie gebeurtenis voor het abonnement. Als u de handshake wilt volt ooien, gaat u naar die URL in de gebeurtenis gegevens en voert u een GET-aanvraag naar deze. U kunt een REST-client of uw webbrowser gebruiken.

   De beschik bare URL is **5 minuten**geldig. Gedurende die tijd is `AwaitingManualAction`de inrichtings status van het gebeurtenis abonnement. Als u de hand matige validatie niet binnen vijf minuten voltooit, wordt de inrichtings `Failed`status ingesteld op. U moet het gebeurtenis abonnement opnieuw maken voordat u de hand matige validatie start.

   Voor dit verificatie mechanisme moet ook het webhook-eind punt een HTTP-status code van 200 retour neren, zodat u weet dat het bericht voor de validatie gebeurtenis is geaccepteerd voordat het in de hand matige validatie modus kan worden geplaatst. Met andere woorden, als het eind punt 200 retourneert, maar niet synchroon een validatie antwoord terugkeert, wordt de modus overgezet naar de hand matige validatie modus. Als er binnen vijf minuten een GET op de validatie-URL is, wordt de validatie-Handshake als geslaagd beschouwd.

> [!NOTE]
> Het gebruik van zelfondertekende certificaten voor validatie wordt niet ondersteund. Gebruik in plaats daarvan een ondertekend certificaat van een certificerings instantie (CA).

### <a name="validation-details"></a>Validatie Details

- Op het moment dat het maken of bijwerken van het gebeurtenis abonnement wordt gemaakt, Event Grid een validatie gebeurtenis voor het abonnement op het eind punt van het doel.
- De gebeurtenis bevat een header waarde "AEG-Event-type: SubscriptionValidation".
- De hoofd tekst van de gebeurtenis heeft hetzelfde schema als andere Event Grid-gebeurtenissen.
- De eigenschap Event type van de gebeurtenis `Microsoft.EventGrid.SubscriptionValidationEvent`is.
- De eigenschap data van de gebeurtenis bevat een `validationCode` eigenschap met een wille keurig gegenereerde teken reeks. Bijvoorbeeld "validationCode: acb13...".
- De gebeurtenis gegevens bevatten ook een `validationUrl` eigenschap met een URL voor het hand matig valideren van het abonnement.
- De matrix bevat alleen de validatie gebeurtenis. Andere gebeurtenissen worden verzonden in een afzonderlijke aanvraag nadat u de validatie code hebt teruggestuurd.
- De EventGrid DataPlane Sdk's hebben klassen die overeenkomen met de validatie van de gebeurtenis gegevens van het abonnement en het antwoord op het abonnement.

In het volgende voor beeld wordt een voor beeld van SubscriptionValidationEvent weer gegeven:

```json
[
  {
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
  }
]
```

Als u het eigendom van het eind punt wilt bewijzen, keert u terug naar de validatie code in de eigenschap validationResponse, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

U moet een HTTP 200 OK-antwoord status code retour neren. HTTP 202-geaccepteerd wordt niet herkend als een geldig Event Grid antwoord op een abonnements validatie. De HTTP-aanvraag moet binnen 30 seconden worden voltooid. Als de bewerking niet binnen 30 seconden wordt voltooid, wordt de bewerking geannuleerd en wordt deze na vijf seconden opnieuw geprobeerd. Als alle pogingen mislukken, wordt deze behandeld als validatie-Handshake-fout.

Of u kunt het abonnement hand matig valideren door een GET-aanvraag naar de validatie-URL te verzenden. Het gebeurtenis abonnement blijft in de status in behandeling totdat het is gevalideerd. De validatie-URL maakt gebruik van poort 553. Als uw firewall regels poort 553 blok keren, moeten de regels mogelijk worden bijgewerkt voor een geslaagde hand matige handshake.

Zie voor een voor beeld van het verwerken van de handshake voor het valideren van een abonnement een C#-voor [beeld](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Problemen met EventSubsciption-validatie oplossen

Bij het maken van het gebeurtenis abonnement wordt er een fout bericht weer gegeven zoals ' de poging om het gegeven eind punt https\/:/Your-endpoint-Here te valideren. Ga naar https:\//aka.MS/esvalidation voor meer informatie. Dit geeft aan dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen door de volgende aspecten te controleren:

- Voer een HTTP POST naar uw webhook-URL uit met een voor beeld van een [SubscriptionValidationEvent](#validation-details) -aanvraag tekst met behulp van Postman of krul of een soortgelijk hulp programma.
- Als uw webhook het handshake-mechanisme voor synchrone validatie implementeert, controleert u of de ValidationCode als onderdeel van het antwoord wordt geretourneerd.
- Als uw webhook een asynchroon validatie-handshake-mechanisme implementeert, controleert u of u het HTTP POST-bericht 200 OK retourneert.
- Als uw webhook 403 (verboden) in het antwoord retourneert, controleert u of uw webhook zich achter een Azure-toepassing gateway of Web Application firewall bevindt. Als dat het geval is, moet u deze firewall regels uitschakelen en opnieuw een HTTP-POST doen:

  920300 (aanvraag mist een Accept-header, we kunnen dit oplossen)

  942430 (beperkte SQL-teken anomalie detectie (argumenten): aantal speciale tekens is overschreden (12))

  920230 (meerdere URL-code ring gedetecteerd)

  942130 (SQL-injectie aanval: SQL-Tautology gedetecteerd.)

  931130 (mogelijke RFI-aanval (Remote File Include) = off-domein verwijzing/koppeling)

### <a name="event-delivery-security"></a>Gebeurtenis leverings beveiliging

#### <a name="azure-ad"></a>Azure AD

U kunt uw webhook-eind punt beveiligen met behulp van Azure Active Directory om Event Grid te verifiëren en te autoriseren voor het publiceren van gebeurtenissen naar uw eind punten. U moet een Azure Active Directory-toepassing maken, een rol-en service beginsel maken in uw toepassing Event Grid en het gebeurtenis abonnement configureren voor het gebruik van de Azure AD-toepassing. [Meer informatie over het configureren van Aad met Event grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Queryparameters

U kunt uw webhook-eind punt beveiligen door query parameters toe te voegen aan de webhook-URL bij het maken van een gebeurtenis abonnement. Stel een van deze query parameters in op een geheim, zoals een [toegangs token](https://en.wikipedia.org/wiki/Access_token). De webhook kan het geheim gebruiken om te herkennen dat de gebeurtenis afkomstig is van Event Grid met geldige machtigingen. Event Grid neemt deze query parameters op in elke gebeurtenis levering aan de webhook.

Bij het bewerken van het gebeurtenis abonnement worden de query parameters niet weer gegeven of geretourneerd, tenzij de para meter [--include-Full-endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) wordt gebruikt in azure [cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Ten slotte is het belang rijk te weten dat Azure Event Grid alleen HTTPS-webhook-eind punten ondersteunt.

## <a name="event-subscription"></a>Gebeurtenis abonnement

Als u zich wilt abonneren op een gebeurtenis, moet u bewijzen dat u toegang hebt tot de gebeurtenis bron en handler. Bewijs dat u een webhook hebt, werd in de voor gaande sectie besproken. Als u een gebeurtenis-handler gebruikt die geen webhook is (zoals een Event Hub of een wachtrij opslag), moet u schrijf toegang hebben voor die bron. Met deze machtigingen kunt u voor komen dat een niet-geautoriseerde gebruiker gebeurtenissen naar uw resource verzendt.

U moet beschikken over de machtiging **micro soft. EventGrid/EventSubscriptions/write** voor de resource die de bron van de gebeurtenis is. U hebt deze machtiging nodig omdat u een nieuw abonnement op het bereik van de resource schrijft. De vereiste resource wijkt af van de vraag of u zich abonneert op een systeem onderwerp of een aangepast onderwerp. Beide typen worden beschreven in deze sectie.

### <a name="system-topics-azure-service-publishers"></a>Systeem onderwerpen (Azure service Publishers)

Voor systeem onderwerpen hebt u toestemming nodig voor het schrijven van een nieuw gebeurtenis abonnement op het bereik van de resource die de gebeurtenis publiceert. De indeling van de resource is:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Als u zich bijvoorbeeld wilt abonneren op een gebeurtenis in een opslag account met de naam **myacct**, hebt u de machtiging micro soft. EventGrid/EventSubscriptions/write nodig voor:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Aangepaste onderwerpen

Voor aangepaste onderwerpen hebt u toestemming nodig voor het schrijven van een nieuw gebeurtenis abonnement in het bereik van het event grid-onderwerp. De indeling van de resource is:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Als u zich bijvoorbeeld wilt abonneren op een aangepast onderwerp met de naam **mytopic**, hebt u de machtiging micro soft. EventGrid/EventSubscriptions/write nodig voor:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Aangepaste onderwerpen publiceren

Aangepaste onderwerpen gebruiken een Shared Access Signature (SAS) of sleutel verificatie. We raden SAS aan, maar sleutel verificatie biedt eenvoudige programmering en is compatibel met veel bestaande webhook-Publishers.

U neemt de verificatie waarde op in de HTTP-header. Voor SAS gebruikt u **AEG-SAS-token** voor de waarde van de header. Gebruik voor sleutel verificatie **AEG-SAS-sleutel** voor de waarde van de header.

### <a name="key-authentication"></a>Sleutel verificatie

Sleutel verificatie is de eenvoudigste vorm van verificatie. Gebruik de volgende indeling:`aeg-sas-key: <your key>`

U geeft bijvoorbeeld een sleutel door:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokens

SAS-tokens voor Event Grid bevatten de resource, een verloop tijd en een hand tekening. De indeling van de SAS-token is `r={resource}&e={expiration}&s={signature}`:.

De resource is het pad naar het event grid-onderwerp waarnaar u gebeurtenissen verzendt. Een geldig bronpad is bijvoorbeeld: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Zie [resource typen micro soft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)voor een overzicht van alle ondersteunde API-versies. 

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

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Alle gebeurtenissen of gegevens die door de Event Grid-Service naar schijf worden geschreven, worden versleuteld met een door micro soft beheerde sleutel, zodat deze op rest versleuteld is. Bovendien is de maximale tijds duur dat gebeurtenissen of gegevens bewaard 24 uur in acht te komen in de [Event grid beleid voor opnieuw proberen](delivery-and-retry.md). Event Grid verwijdert automatisch alle gebeurtenissen of gegevens na 24 uur, of de TTL van de gebeurtenis, afhankelijk van wat kleiner is.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpunt validatie met CloudEvents v 1.0
Als u al bekend bent met Event Grid, is het mogelijk dat u op de hoogte bent van de verificatie-handshake van het Event Grid om misbruik te voor komen. CloudEvents v 1.0 implementeert zijn eigen [beveiligings semantiek](security-authentication.md#webhook-event-delivery) met behulp van de http-opties methode. [Hier](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) vindt u meer informatie. Wanneer u het CloudEvents-schema voor uitvoer gebruikt, wordt Event Grid gebruikt met de CloudEvents v 1.0-beschermings beveiliging in plaats van het Event Grid validatie gebeurtenis mechanisme.

## <a name="next-steps"></a>Volgende stappen

- Zie [About Event grid](overview.md) voor een inleiding tot Event grid.
