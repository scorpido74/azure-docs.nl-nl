---
title: Beveiliging en verificatie van Azure Event Grid
description: In dit artikel worden verschillende manieren beschreven om de toegang tot uw eventgridbronnen te verifiëren (WebHook, abonnementen, aangepaste onderwerpen)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532390"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Toegang tot bronnen voor gebeurtenisrasters verifiëren

Azure Event Grid heeft drie soorten verificatie:

- WebHook-gebeurtenislevering
- Gebeurtenisabonnementen
- Aangepaste onderwerppublicatie

## <a name="webhook-event-delivery"></a>WebHook-gebeurtenislevering

Webhooks zijn een van de vele manieren om gebeurtenissen van Azure Event Grid te ontvangen. Wanneer een nieuwe gebeurtenis klaar is, wordt er een HTTP-aanvraag voor het geconfigureerde eindpunt gekoppeld aan de gebeurtenis in de aanvraaginstantie.

Net als veel andere services die webhooks ondersteunen, vereist Event Grid dat u het eigendom van uw Webhook-eindpunt bewijst voordat het gebeurtenissen naar dat eindpunt begint te leveren. Deze vereiste voorkomt dat een kwaadwillende gebruiker uw eindpunt overspoelt met gebeurtenissen. Wanneer u een van de drie hieronder vermelde Azure-services gebruikt, wordt deze validatie automatisch uitgevoerd door de Azure-infrastructuur:

- Azure Logic Apps met [gebeurtenisrasterconnector](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure-functies met [gebeurtenisrastertrigger](../azure-functions/functions-bindings-event-grid.md)

Als u een ander type eindpunt gebruikt, zoals een op HTTP-trigger gebaseerde Azure-functie, moet uw eindpuntcode deelnemen aan een validatiehandshake met gebeurtenisraster. Event Grid ondersteunt twee manieren om het abonnement te valideren.

1. **Synchrone handdruk**: Op het moment van het maken van een gebeurtenisabonnement stuurt gebeurtenisraster een gebeurtenis voor abonnementsvalidatie naar uw eindpunt. Het schema van deze gebeurtenis is vergelijkbaar met elke andere gebeurtenis Event Grid. Het gegevensgedeelte van deze `validationCode` gebeurtenis bevat een eigenschap. Uw toepassing controleert of de validatieaanvraag voor een verwacht gebeurtenisabonnement is en retourneert de validatiecode in het antwoord synchroon. Dit handshakemechanisme wordt ondersteund in alle Event Grid-versies.

2. **Asynchrone handdruk:** In bepaalde gevallen u de Validatiecode niet synchroon retourneren. Als u bijvoorbeeld een service van derden [`Zapier`](https://zapier.com) gebruikt (zoals of [IFTTT),](https://ifttt.com/)u niet programmatisch reageren met de validatiecode.

   Vanaf versie 2018-05-01-preview ondersteunt Event Grid een handmatige validatiehanddruk. Als u een gebeurtenisabonnement maakt met een SDK of hulpprogramma dat API-versie 2018-05-01-preview of hoger gebruikt, verzendt Gebeurtenisraster een `validationUrl` eigenschap in het gegevensgedeelte van de gebeurtenis voor abonnementsvalidatie. Als u de handdruk wilt voltooien, zoekt u die URL in de gebeurtenisgegevens en voert u een GET-verzoek aan. U een REST-client of uw webbrowser gebruiken.

   De opgegeven URL is **5 minuten**geldig. Gedurende die tijd is `AwaitingManualAction`de inleveringvan het evenementabonnement . Als u de handmatige validatie niet binnen 5 minuten voltooit, wordt de instelstatus ingesteld op `Failed`. U moet het gebeurtenisabonnement opnieuw maken voordat u met de handmatige validatie begint.

   Dit verificatiemechanisme vereist ook dat het webhook-eindpunt een HTTP-statuscode van 200 retourneert, zodat het weet dat de POST voor de validatiegebeurtenis is geaccepteerd voordat deze in de handmatige validatiemodus kan worden geplaatst. Met andere woorden, als het eindpunt 200 retourneert maar een validatierespons niet synchroon teruggeeft, wordt de modus overgezet naar de handmatige validatiemodus. Als er binnen 5 minuten een GET op de validatie-URL staat, wordt de validatiehandshake als geslaagd beschouwd.

> [!NOTE]
> Het gebruik van zelfondertekende certificaten voor validatie wordt niet ondersteund. Gebruik in plaats daarvan een ondertekend certificaat van een certificeringsinstantie (CA).

### <a name="validation-details"></a>Validatiedetails

- Op het moment van het maken/bijwerken van een gebeurtenisabonnement plaatst Event Grid een gebeurtenis voor abonnementsvalidatie op het doeleindpunt.
- De gebeurtenis bevat een kopwaarde "aeg-event-type: SubscriptionValidation".
- De gebeurtenisbody heeft hetzelfde schema als andere gebeurtenisrastergebeurtenissen.
- De eigenschap eventType van `Microsoft.EventGrid.SubscriptionValidationEvent`de gebeurtenis is .
- De eigenschap gegevens van `validationCode` de gebeurtenis bevat een eigenschap met een willekeurig gegenereerde tekenreeks. Bijvoorbeeld "validatieCode: acb13...".
- De gebeurtenisgegevens bevatten `validationUrl` ook een eigenschap met een URL voor het handmatig valideren van het abonnement.
- De array bevat alleen de validatiegebeurtenis. Andere gebeurtenissen worden in een apart verzoek verzonden nadat u de validatiecode hebt teruggekaatst.
- De EventGrid DataPlane SDKs hebben klassen die overeenkomen met de gegevens van het abonnementsvalidatiegebeurtenis en de reactie van abonnementsvalidatie.

Een voorbeeld SubscriptionValidationEvent wordt weergegeven in het volgende voorbeeld:

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

Als u het eigendom van eindpunt wilt aantonen, weerkaatst u de validatiecode in de eigenschap validationResponse, zoals in het volgende voorbeeld wordt weergegeven:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

U moet een HTTP 200 OK-antwoordstatuscode retourneren. HTTP 202 Geaccepteerd wordt niet erkend als een geldige gebeurtenisgrid-abonnementvalidatiereactie. De http-aanvraag moet binnen 30 seconden zijn voltooid. Als de bewerking niet binnen 30 seconden is voltooid, wordt de bewerking geannuleerd en kan deze na 5 seconden opnieuw worden geprobeerd. Als alle pogingen mislukken, wordt deze behandeld als fout in de validatiehandshake.

U het abonnement ook handmatig valideren door een GET-verzoek naar de validatie-URL te sturen. Het gebeurtenisabonnement blijft in behandeling totdat het is gevalideerd. De validatie-url maakt gebruik van poort 553. Als uw firewallregels poort 553 blokkeren, moeten de regels mogelijk worden bijgewerkt voor een succesvolle handdruk.

Zie een [C#-voorbeeld](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)voor een voorbeeld van de verwerking van de handdruk van de abonnementsvalidatie.

## <a name="troubleshooting-eventsubsciption-validation"></a>Probleemoplossing gebeurtenissubsciption validatie

Als u tijdens het maken van een gebeurtenisabonnement een foutbericht ziet, zoals\/'De poging om het opgegeven eindpunt https te valideren: /your-endpoint-here is mislukt. Ga voor meer informatie\/naar https: /aka.ms/esvalidation", het geeft aan dat er een fout is in de validatiehandshake. Controleer de volgende aspecten om deze fout op te lossen:

- Doe een HTTP-bericht naar uw webhook url met een [voorbeeld SubscriptionValidationEvent](#validation-details) aanvraag lichaam met behulp van Postbode of krul of soortgelijke tool.
- Als uw webhook het synchrone validatiehandshakemechanisme implementeert, controleert u of de validatiecode wordt geretourneerd als onderdeel van het antwoord.
- Als uw webhook een asynchrone validatiehandshakemechanisme implementeert, controleert u of u de HTTP POST 200 OK retoureert.
- Als uw webhook 403 (Verboden) retourkeert in het antwoord, controleert u of uw webhook zich achter een Azure Application Gateway of Web Application Firewall bevindt. Als dat het is, dan is het nodig om deze firewall regels uit te schakelen en een HTTP POST opnieuw te doen:

  920300 (Verzoek ontbreekt een header accepteren, kunnen we dit oplossen)

  942430 (Restricted SQL Character Anomaly Detection (args): # van de speciale tekens overschreden (12))

  920230 (Meerdere URL-codering gedetecteerd)

  942130 (SQL Injection Attack: SQL Tautology Gedetecteerd.)

  931130 (Mogelijke RFI-aanval (Remote File Inclusion) = Referentie/koppeling buiten het domein)

### <a name="event-delivery-security"></a>Beveiliging van de levering van gebeurtenissen

#### <a name="azure-ad"></a>Azure AD

U uw webhook-eindpunt beveiligen door Azure Active Directory te gebruiken om gebeurtenisraster te verifiëren en te autoriseren om gebeurtenissen naar uw eindpunten te publiceren. U moet een Azure Active Directory-toepassing maken, een rol- en serviceprincipe maken in uw toepassings die gebeurtenisraster autoriseert en het gebeurtenisabonnement configureren om de AD-toepassing van Azure te gebruiken. [Meer informatie over het configureren van AAD met gebeurtenisraster](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Queryparameters

U uw webhook-eindpunt beveiligen door queryparameters toe te voegen aan de webhook-URL bij het maken van een gebeurtenisabonnement. Stel een van deze queryparameters in als een geheim, zoals een [toegangstoken.](https://en.wikipedia.org/wiki/Access_token) De webhook kan het geheim gebruiken om te herkennen dat de gebeurtenis afkomstig is van Event Grid met geldige machtigingen. Event Grid bevat deze queryparameters in elke gebeurtenislevering aan de webhook.

Bij het bewerken van het gebeurtenisabonnement worden de queryparameters niet weergegeven of geretourneerd, tenzij de parameter [-include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) wordt gebruikt in Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Ten slotte is het belangrijk op te merken dat Azure Event Grid alleen HTTPS webhook-eindpunten ondersteunt.

## <a name="event-subscription"></a>Abonnement op evenement

Als u zich wilt abonneren op een gebeurtenis, moet u bewijzen dat u toegang hebt tot de gebeurtenisbron en handler. Bewijzen dat u eigenaar bent van een WebHook werd behandeld in de vorige sectie. Als u een gebeurtenishandler gebruikt die geen WebHook is (zoals een gebeurtenishub of wachtrijopslag), hebt u schrijftoegang tot die bron nodig. Met deze controle met machtigingen voorkomt u dat een onbevoegde gebruiker gebeurtenissen naar uw bron verzendt.

U moet de machtiging **Microsoft.EventGrid/EventSubscriptions/Write** hebben op de bron die de gebeurtenisbron is. U hebt deze toestemming nodig omdat u een nieuw abonnement schrijft in het kader van de resource. De vereiste resource verschilt op basis van of u zich abonneert op een systeemonderwerp of aangepast onderwerp. Beide typen worden in deze sectie beschreven.

### <a name="system-topics-azure-service-publishers"></a>Systeemonderwerpen (Azure-service-uitgevers)

Voor systeemonderwerpen hebt u toestemming nodig om een nieuw gebeurtenisabonnement te schrijven in het kader van de bron die de gebeurtenis publiceert. De indeling van de resource is:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Als u zich bijvoorbeeld wilt abonneren op een evenement op een opslagaccount met de naam **myacct,** hebt u de machtiging Microsoft.EventGrid/EventSubscriptions/Write nodig op:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Aangepaste onderwerpen

Voor aangepaste onderwerpen hebt u toestemming nodig om een nieuw gebeurtenisabonnement te schrijven in het kader van het onderwerp van het gebeurtenisraster. De indeling van de resource is:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Als u zich bijvoorbeeld wilt abonneren op een aangepast onderwerp met de naam **mytopic,** hebt u de machtiging Microsoft.EventGrid/EventSubscriptions/Write nodig op:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Aangepaste onderwerppublicatie

Aangepaste onderwerpen gebruiken SAS (Shared Access Signature) of sleutelverificatie. We raden SAS aan, maar sleutelverificatie biedt eenvoudige programmering en is compatibel met veel bestaande webhook-uitgevers.

U neemt de verificatiewaarde op in de HTTP-header. Gebruik voor SAS **aeg-sas-token** voor de kopwaarde. Voor sleutelverificatie gebruikt u **aeg-sas-key** voor de kopwaarde.

### <a name="key-authentication"></a>Sleutelverificatie

Sleutelverificatie is de eenvoudigste vorm van verificatie. Gebruik de indeling:`aeg-sas-key: <your key>`

U geeft bijvoorbeeld een sleutel door met:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokens

SAS-tokens voor Gebeurtenisraster bevatten de bron, een vervaldatum en een handtekening. Het formaat van het SAS-token is: `r={resource}&e={expiration}&s={signature}`.

De resource is het pad voor het gebeurtenisrasteronderwerp waarnaar u gebeurtenissen verzendt. Een geldig resourcepad is `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`bijvoorbeeld: . Zie [Microsoft.EventGrid-brontypen](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)voor alle ondersteunde API-versies. 

U genereert de handtekening van een sleutel.

Een geldige **aeg-sas-tokenwaarde** is bijvoorbeeld:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

In het volgende voorbeeld wordt een SAS-token gemaakt voor gebruik met gebeurtenisraster:

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

Alle gebeurtenissen of gegevens die door de Event Grid-service naar de schijf zijn geschreven, worden versleuteld door een door Microsoft beheerde sleutel die ervoor zorgt dat deze in rust wordt versleuteld. Bovendien is de maximale periode die gebeurtenissen of gegevens bewaren 24 uur in overeenstemming met het [beleid voor opnieuw proberen van het gebeurtenisraster](delivery-and-retry.md). Event Grid verwijdert automatisch alle gebeurtenissen of gegevens na 24 uur, of de gebeurtenis time-to-live, wat minder is.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpuntvalidatie met CloudEvents v1.0
Als u al bekend bent met Gebeurtenisraster, bent u mogelijk op de hoogte van de handdruk van event grid voor het voorkomen van misbruik. CloudEvents v1.0 implementeert zijn eigen [misbruikbescherming semantiek](security-authentication.md#webhook-event-delivery) met behulp van de HTTP OPTIONS-methode. [Hier](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) vindt u meer informatie. Wanneer u het CloudEvents-schema gebruikt voor uitvoer, gebruikt Gebeurtenisraster met de cloudevents v1.0-misbruikbeveiliging in plaats van het gebeurtenismechanisme voor gebeurtenisrastergebeurtenissen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Over gebeurtenisraster](overview.md) voor een inleiding tot gebeurtenisraster
