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
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281013"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Toegang tot bronnen voor gebeurtenisrasters verifiëren

Azure Event Grid heeft drie soorten verificatie:

* WebHook-gebeurtenislevering
* Gebeurtenisabonnementen
* Aangepaste onderwerppublicatie

## <a name="webhook-event-delivery"></a>WebHook-gebeurtenislevering

Webhooks zijn een van de vele manieren om gebeurtenissen van Azure Event Grid te ontvangen. Wanneer een nieuwe gebeurtenis klaar is, wordt er een HTTP-aanvraag voor het geconfigureerde eindpunt gekoppeld aan de gebeurtenis in de aanvraaginstantie.

Net als veel andere services die webhooks ondersteunen, vereist Event Grid dat u het eigendom van uw Webhook-eindpunt bewijst voordat het gebeurtenissen naar dat eindpunt begint te leveren. Deze vereiste voorkomt dat een kwaadwillende gebruiker uw eindpunt overspoelt met gebeurtenissen. Wanneer u een van de drie hieronder vermelde Azure-services gebruikt, wordt deze validatie automatisch uitgevoerd door de Azure-infrastructuur:

* Azure Logic Apps met [gebeurtenisrasterconnector](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure-functies met [gebeurtenisrastertrigger](../azure-functions/functions-bindings-event-grid.md)

Als u een ander type eindpunt gebruikt, zoals een op HTTP-trigger gebaseerde Azure-functie, moet uw eindpuntcode deelnemen aan een validatiehandshake met gebeurtenisraster. Event Grid ondersteunt twee manieren om het abonnement te valideren.

1. **ValidationCode handshake (programmatisch)**: Als u de broncode voor uw eindpunt beheert, wordt deze methode aanbevolen. Op het moment van het maken van een evenementabonnement stuurt Gebeurtenisgrid een gebeurtenis voor abonnementsvalidatie naar uw eindpunt. Het schema van deze gebeurtenis is vergelijkbaar met elke andere gebeurtenis Event Grid. Het gegevensgedeelte van deze `validationCode` gebeurtenis bevat een eigenschap. Uw toepassing controleert of de validatieaanvraag voor een verwacht gebeurtenisabonnement is en weerspiegelt de validatiecode naar Gebeurtenisraster. Dit handshakemechanisme wordt ondersteund in alle Event Grid-versies.

2. **ValidationURL handshake (handleiding)**: In bepaalde gevallen hebt u geen toegang tot de broncode van het eindpunt om de Handshake van validationcode te implementeren. Als u bijvoorbeeld een service van derden gebruikt (zoals [Zapier](https://zapier.com) of [IFTTT),](https://ifttt.com/)u niet programmatisch reageren met de validatiecode.

   Vanaf versie 2018-05-01-preview ondersteunt Event Grid een handmatige validatiehanddruk. Als u een gebeurtenisabonnement maakt met een SDK of hulpprogramma dat API-versie 2018-05-01-preview of hoger gebruikt, verzendt Gebeurtenisraster een `validationUrl` eigenschap in het gegevensgedeelte van de gebeurtenis voor abonnementsvalidatie. Als u de handdruk wilt voltooien, zoekt u die URL in de gebeurtenisgegevens en stuurt u er handmatig een GET-verzoek naar. U een REST-client of uw webbrowser gebruiken.

   De opgegeven URL is 5 minuten geldig. Gedurende die tijd is `AwaitingManualAction`de inleveringvan het evenementabonnement . Als u de handmatige validatie niet binnen 5 minuten voltooit, wordt de instelstatus ingesteld op `Failed`. U moet het gebeurtenisabonnement opnieuw maken voordat u met de handmatige validatie begint.

    Dit verificatiemechanisme vereist ook dat het webhook-eindpunt een HTTP-statuscode van 200 retourneert, zodat het weet dat de POST voor de validatiegebeurtenis is geaccepteerd voordat deze in de handmatige validatiemodus kan worden geplaatst. Met andere woorden, als het eindpunt 200 retourneert maar een validatierespons niet programmatisch teruggeeft, wordt de modus overgezet naar de handmatige validatiemodus. Als er binnen 5 minuten een GET op de validatie-URL staat, wordt de validatiehandshake als geslaagd beschouwd.

> [!NOTE]
> Het gebruik van zelfondertekende certificaten voor validatie wordt niet ondersteund. Gebruik in plaats daarvan een ondertekend certificaat van een certificeringsinstantie (CA).

### <a name="validation-details"></a>Validatiedetails

* Op het moment van het maken/bijwerken van een gebeurtenisabonnement plaatst Event Grid een gebeurtenis voor abonnementsvalidatie op het doeleindpunt. 
* De gebeurtenis bevat een kopwaarde "aeg-event-type: SubscriptionValidation".
* De gebeurtenisbody heeft hetzelfde schema als andere gebeurtenisrastergebeurtenissen.
* De eigenschap eventType van `Microsoft.EventGrid.SubscriptionValidationEvent`de gebeurtenis is .
* De eigenschap gegevens van `validationCode` de gebeurtenis bevat een eigenschap met een willekeurig gegenereerde tekenreeks. Bijvoorbeeld "validatieCode: acb13...".
* De gebeurtenisgegevens bevatten `validationUrl` ook een eigenschap met een URL voor het handmatig valideren van het abonnement.
* De array bevat alleen de validatiegebeurtenis. Andere gebeurtenissen worden in een apart verzoek verzonden nadat u de validatiecode hebt teruggekaatst.
* De EventGrid DataPlane SDKs hebben klassen die overeenkomen met de gegevens van het abonnementsvalidatiegebeurtenis en de reactie van abonnementsvalidatie.

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

Als u het eigendom van eindpunt wilt aantonen, weerkaatst u de validatiecode in de eigenschap validationResponse, zoals in het volgende voorbeeld wordt weergegeven:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

U moet een HTTP 200 OK-antwoordstatuscode retourneren. HTTP 202 Geaccepteerd wordt niet erkend als een geldige gebeurtenisgrid-abonnementvalidatiereactie. De http-aanvraag moet binnen 30 seconden zijn voltooid. Als de bewerking niet binnen 30 seconden is voltooid, wordt de bewerking geannuleerd en kan deze na 5 seconden opnieuw worden geprobeerd. Als alle pogingen mislukken, wordt deze behandeld als fout in de validatiehandshake.

U het abonnement ook handmatig valideren door een GET-verzoek naar de validatie-URL te sturen. Het gebeurtenisabonnement blijft in behandeling totdat het is gevalideerd. De validatie-url maakt gebruik van poort 553. Als uw firewallregels poort 553 blokkeren, moeten de regels mogelijk worden bijgewerkt voor een succesvolle handdruk.

Zie een [C#-voorbeeld](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)voor een voorbeeld van de verwerking van de handdruk van de abonnementsvalidatie.

### <a name="checklist"></a>Controlelijst

Als u tijdens het maken van een gebeurtenisabonnement een foutbericht ziet, zoals\/'De poging om het opgegeven eindpunt https te valideren: /your-endpoint-here is mislukt. Ga voor meer informatie\/naar https: /aka.ms/esvalidation", het geeft aan dat er een fout is in de validatiehandshake. Controleer de volgende aspecten om deze fout op te lossen:

* Heeft u de controle over de toepassingscode die wordt uitgevoerd in het doeleindpunt? Als u bijvoorbeeld een op HTTP-trigger gebaseerde Azure-functie schrijft, hebt u dan toegang tot de toepassingscode om deze code aan te brengen?
* Als u toegang hebt tot de toepassingscode, implementeert u het op ValidatieCode gebaseerde handshakemechanisme zoals in het bovenstaande voorbeeld wordt weergegeven.

* Als u geen toegang hebt tot de toepassingscode (bijvoorbeeld als u een service van derden gebruikt die webhooks ondersteunt), u het handmatige handshakemechanisme gebruiken. Zorg ervoor dat u de API-versie voor 2018-05-01-preview of hoger gebruikt (installeer de Azure CLI-extensie gebeurtenis) om de validatieUrl in de validatiegebeurtenis te ontvangen. Als u de handmatige validatiehanddruk `validationUrl` wilt voltooien, krijgt u de waarde van de eigenschap en bezoekt u die URL in uw webbrowser. Als de validatie succesvol is, ziet u in uw webbrowser een bericht dat de validatie is geslaagd. U ziet dat de provisioningState van dat gebeurtenisabonnement 'Geslaagd' is. 

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

De resource is het pad voor het gebeurtenisrasteronderwerp waarnaar u gebeurtenissen verzendt. Een geldig resourcepad is bijvoorbeeld:`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

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

## <a name="next-steps"></a>Volgende stappen

* Zie [Over gebeurtenisraster](overview.md) voor een inleiding tot gebeurtenisraster
