---
title: Clients die gebeurtenissen publiceren naar Event Grid aangepaste onderwerpen of domeinen verifiëren
description: In dit artikel worden verschillende manieren beschreven voor het verifiëren van clients die gebeurtenissen publiceren op Event Grid aangepaste onderwerpen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e934ce0d8f5e31dc8dd7592a2e553cd278af2b10
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019110"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Publicerend clients verifiëren (Azure Event Grid)
Dit artikel bevat informatie over het verifiëren van clients die gebeurtenissen publiceren naar Azure Event Grid onderwerpen of domeinen met behulp van het **toegangs sleutel** **-of Shared Access Signature SAS-** token. U kunt het beste SAS-token gebruiken, maar de sleutel verificatie biedt eenvoudige programmering en is compatibel met veel bestaande webhook-Publishers.  

## <a name="authenticate-using-an-access-key"></a>Verifiëren met behulp van een toegangs sleutel
Toegangs sleutel verificatie is de eenvoudigste vorm van verificatie. U kunt de toegangs sleutel door geven als een HTTP-header of een URL-query parameter. 

### <a name="access-key-in-a-http-header"></a>Toegangs sleutel in een HTTP-header
Geef de toegangs sleutel als waarde voor de HTTP-header: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Toegangs sleutel als een query parameter
U kunt ook `aeg-sas-key` een query parameter opgeven. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Zie [toegangs sleutels ophalen](get-access-keys.md)voor instructies over het ophalen van toegangs sleutels voor een onderwerp of domein.

## <a name="authenticate-using-a-sas-token"></a>Verifiëren met een SAS-token
SAS-tokens voor een Event Grid bron zijn de resource, de verval tijd en een hand tekening. De indeling van de SAS-token is: `r={resource}&e={expiration}&s={signature}` .

De resource is het pad naar het event grid-onderwerp waarnaar u gebeurtenissen verzendt. Een geldig bronpad is bijvoorbeeld: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` . Zie [resource typen micro soft. EventGrid](/azure/templates/microsoft.eventgrid/allversions)voor een overzicht van alle ondersteunde API-versies. 

Eerst programmatisch een SAS-token genereren en vervolgens de `aeg-sas-token` header of header gebruiken `Authorization SharedAccessSignature` om te verifiëren met Event grid. 

### <a name="generate-sas-token-programmatically"></a>SAS-token programmatisch genereren
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

### <a name="using-aeg-sas-token-header"></a>AEG-SAS-token header gebruiken
Hier volgt een voor beeld van het door geven van de SAS-token als een waarde voor de `aeg-sas-toke` koptekst. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Autorisatie-header gebruiken
Hier volgt een voor beeld van het door geven van de SAS-token als een waarde voor de `Authorization` koptekst. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Volgende stappen
Zie [verificatie van gebeurtenis bezorging](security-authentication.md) voor meer informatie over verificatie met gebeurtenis-handlers voor het leveren van gebeurtenissen. 