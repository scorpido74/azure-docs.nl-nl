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
ms.openlocfilehash: 71d47c83586f7e5e31b148714e2804686422326a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588255"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Toegang tot Azure Event Grid-bronnen verifiëren
In dit artikel vindt u informatie over de volgende scenario's:  

- Verifieer clients die gebeurtenissen publiceren naar Azure Event Grid-onderwerpen met behulp van Shared Access Signature (SAS) of sleutel. 
- Beveilig uw webhook-eind punt met Azure Active Directory (Azure AD) om Event Grid te verifiëren voor het **afleveren** van gebeurtenissen aan het eind punt.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Publicerend clients verifiëren met SAS of sleutel
Aangepaste onderwerpen gebruiken een Shared Access Signature (SAS) of sleutel verificatie. We raden SAS aan, maar sleutel verificatie biedt eenvoudige programmering en is compatibel met veel bestaande webhook-Publishers.

U neemt de verificatie waarde op in de HTTP-header. Voor SAS gebruikt u **AEG-SAS-token** voor de waarde van de header. Gebruik voor sleutel verificatie **AEG-SAS-sleutel** voor de waarde van de header.

### <a name="key-authentication"></a>Sleutel verificatie

Sleutel verificatie is de eenvoudigste vorm van verificatie. Gebruik de indeling: `aeg-sas-key: <your key>` in de header van het bericht.

U geeft bijvoorbeeld een sleutel door:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

U kunt ook `aeg-sas-key` een query parameter opgeven. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokens

SAS-tokens voor Event Grid bevatten de resource, een verloop tijd en een hand tekening. De indeling van de SAS-token is: `r={resource}&e={expiration}&s={signature}` .

De resource is het pad naar het event grid-onderwerp waarnaar u gebeurtenissen verzendt. Een geldig bronpad is bijvoorbeeld: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` . Zie [resource typen micro soft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)voor een overzicht van alle ondersteunde API-versies. 

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

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Gebeurtenis levering verifiëren voor webhook-eind punten
In de volgende secties wordt beschreven hoe u de levering van gebeurtenissen verifieert aan webhook-eind punten. U moet een mechanisme voor validatie-Handshake gebruiken, onafhankelijk van de methode die u gebruikt. Zie [webhook Event Delivery](webhook-event-delivery.md) voor meer informatie. 

### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory gebruiken (Azure AD)
U kunt uw webhook-eind punt beveiligen door Azure Active Directory (Azure AD) te gebruiken om Event Grid te verifiëren en te autoriseren om gebeurtenissen te leveren aan uw eind punten. U moet een Azure AD-toepassing maken, een rol-en service beginsel maken in uw toepassing Event Grid en het gebeurtenis abonnement configureren voor het gebruik van de Azure AD-toepassing. [Meer informatie over het configureren van Azure Active Directory met Event grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Client geheim gebruiken als query parameter
U kunt uw webhook-eind punt beveiligen door query parameters toe te voegen aan de webhook-URL bij het maken van een gebeurtenis abonnement. Stel een van deze query parameters in als een client geheim, zoals een [toegangs token](https://en.wikipedia.org/wiki/Access_token) of een gedeeld geheim. De webhook kan het geheim gebruiken om te herkennen dat de gebeurtenis afkomstig is van Event Grid met geldige machtigingen. Event Grid neemt deze query parameters op in elke gebeurtenis levering aan de webhook. Als het client geheim is bijgewerkt, moet het gebeurtenis abonnement ook worden bijgewerkt. Zorg ervoor dat de webhook zowel oude als nieuwe geheimen voor een beperkte duur aanvaardt om afleverings fouten te voor komen tijdens deze geheime draaiing. 

Als query parameters kunnen client geheimen bevatten, worden ze behandeld met extra aandacht. Ze worden opgeslagen als versleuteld en niet toegankelijk voor service operators. Ze worden niet geregistreerd als onderdeel van de service logboeken/traceringen. Bij het bewerken van het gebeurtenis abonnement worden de query parameters niet weer gegeven of geretourneerd, tenzij de para meter [--include-Full-endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) wordt gebruikt in azure [cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Zie [webhook Event Delivery](webhook-event-delivery.md) (Engelstalig) voor meer informatie over het leveren van gebeurtenissen aan webhooks

> [!IMPORTANT]
Azure Event Grid ondersteunt alleen **https** -webhook-eind punten. 

## <a name="next-steps"></a>Volgende stappen

- Zie [About Event grid](overview.md) voor een inleiding tot Event grid.
