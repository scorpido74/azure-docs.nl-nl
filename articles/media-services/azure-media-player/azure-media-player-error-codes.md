---
title: Foutcodes van Azure Media Player
description: Een foutcodeverwijzing voor Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727253"
---
# <a name="error-codes"></a>Foutcodes #

Wanneer het afspelen niet kan worden gestart of is `error()` gestopt, wordt een foutgebeurtenis geactiveerd en wordt een code en een optioneel bericht teruggegeven om de app-ontwikkelaar te helpen meer details te krijgen. `error().message`wordt niet het bericht weergegeven aan de gebruiker.  Het bericht dat aan de `error().code` gebruiker wordt weergegeven, is gebaseerd op bits 27-20, zie onderstaande tabel.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Foutcodes, bits [31-28] (4 bits) ##

Beschrijf het gebied van de fout.

- 0 - Onbekend
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - Knippert
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Foutcodes, bits [27-0] (28 bits) ##

Beschrijf details van de fout, bits 27-20 bieden een hoog niveau, bits 19-0 bieden meer details indien beschikbaar.


| amp.errorCode. [naam] | Codes, Bits [27-0] (28 bits) | Beschrijving |
|---|---:|---|
| **MEDIA_ERR_ABORTED foutenbereik (0x0100000 - 0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Fout bij algemene afbreken |
| abortedErrNotImplemented | 0x0100001 | Fout afbreken, niet geïmplementeerd |
| abortedErHttpMixedContentBlocked | 0x0100002 | Fout afbreken, gemengde inhoud geblokkeerd - treedt `http://` meestal `https://` op bij het laden van een stream vanaf een pagina |
| **MEDIA_ERR_NETWORK beginwaarde van fouten (0x0200000 - 0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Algemene netwerkfout |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400-foutreactie |
| networkErrHttpUserAuthVereist | 0x0200191 | Reactie van http 401-fout |
| networkErrHttpUserForbidden | 0x0200193 | Reactie van http 403-fout |
| networkErrHttpUrlNotFound | 0x0200194 | Reactie van http 404-fout |
| networkErrHttpNotA toegestaan | 0x0200195 | Reactie van http 405-fout |
| networkErrHttpGone | 0x020019A | Reactie van http 410-fout |
| networkErrHttpPreconditionFailed | 0x020019C | Reactie van http 412-fout |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500-foutreactie
| networkErrHttpBadGateway | 0x02001F6 | Reactie van http 502-fout |
| networkErrHttpServiceNiet beschikbaar | 0x02001F7 | Reactie van http 503-fout |
| networkErrHttpGatewayTimeout | 0x02001F8 | Reactie van http 504-fout |
| networkErrTimeout | 0x0200258 | Fout bij time-out van het netwerk
| netwerkErrErr | 0x0200259 | Reactie van de foutvan de netwerkverbinding |
| **MEDIA_ERR_DECODE fouten (0x0300000 - 0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Fout bij generieke decoderen |
| **MEDIA_ERR_SRC_NOT_SUPPORTED fouten (0x04000000 - 0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Fout met generieke bron wordt niet ondersteund |
| srcErrParsePresentatie | 0x0400001 | Parse-fout van presentatie |
| srcErrParseSegment | 0x0400002 | Segmentparse-fout |
| srcErrUnsupportedPresentatie | 0x0400003 | Presentatie niet ondersteund |
| srcErrInvalidSegment | 0x0400004 | Ongeldig segment |
| **beginwaarde MEDIA_ERR_ENCRYPTED fouten (0x0500000 - 0x05FFFFF)** | | |
| encryptErrUnknown encryptErrUnknown | 0x0500000 | Algemene versleutelde fout | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter niet gevonden |
| encryptErrDecrypterInit | 0x0500002 | Initialisatiefout voor decrypter |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter wordt niet ondersteund |
| encryptErrKeyAcquire encryptErrKeyAcquire | 0x0500004 | Sleutelverwerven mislukt |
| encryptErrDecryption encryptErrDecryption encryptErrDecryption encryptEr | 0x0500005 | Decryptie van segment mislukt |
| encryptErrLicenseAcquire encryptErrLicenseAcquire encryptErrLicenseAcquire encryptEr | 0x0500006 | Licentie verkregen mislukt |
| **beginwaarde SRC_PLAYER_MISMATCH(0x06000000 - 0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Generieke geen overeenkomende tech-speler om de bron te spelen |
| srcPlayerMismatchFlashNotGeïnstalleerd | 0x0600001 |Flash-plug-in is niet geïnstalleerd, indien geïnstalleerd kan de bron worden afgespeeld. *OF* Flash 30 is geïnstalleerd en afgespeeld AES-inhoud.  Als dit het geval is, probeer dan een andere browser. Flash 30 wordt vanaf 7 juni niet meer ondersteund. Zie [bekende problemen](azure-media-player-known-issues.md) voor meer details. Opmerking: Als 0x00600003, zowel Flash en Silverlight zijn niet geïnstalleerd, indien opgegeven in de techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Silverlight plugin is niet geïnstalleerd, indien geïnstalleerd de bron kan spelen. Opmerking: Als 0x00600003, zowel Flash en Silverlight zijn niet geïnstalleerd, indien opgegeven in de techOrder. |
| | 0x00600003 | Zowel Flash als Silverlight zijn niet geïnstalleerd, indien gespecificeerd in de techOrder. |
| **Onbekende fouten (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Onbekende fouten |

## <a name="user-error-messages"></a>Foutberichten van gebruikers ##

Het weergegeven bericht van de gebruiker is gebaseerd op de bits 27-20 van de foutcode.

- MEDIA_ERR_ABORTED (1) -"U hebt het afspelen van video afgebroken"
- MEDIA_ERR_NETWORK (2) - "Een netwerkfout zorgde ervoor dat de videodownload halverwege mislukte."
- MEDIA_ERR_DECODE (3) - "Het afspelen van video is afgebroken vanwege een corruptieprobleem of omdat de gebruikte videofuncties niet ondersteunde."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"De video kan niet worden geladen, omdat de server of het netwerk is mislukt of omdat de indeling niet wordt ondersteund."
- MEDIA_ERR_ENCRYPTED (5) - "De video is versleuteld en we hebben niet de sleutels om het te decoderen."
- SRC_PLAYER_MISMATCH (6) - "Er is geen compatibele bron gevonden voor deze video."
- MEDIA_ERR_UNKNOWN (0xFF) - "Er is een onbekende fout opgetreden."

## <a name="examples"></a>Voorbeelden ##

### <a name="0x10600001"></a>0x10600001 ##

"Er is geen compatibele bron gevonden voor deze video." wordt weergegeven aan de eindgebruiker.

Er is geen technische speler die de gevraagde bronnen kan spelen, maar als Flash-plug-in is geïnstalleerd, is het waarschijnlijk dat een bron kan worden afgespeeld.  

### <a name="0x20200194"></a>0x20200194 ###

"Een netwerkfout zorgde ervoor dat de videodownload halverwege mislukte." wordt weergegeven aan de eindgebruiker.

AzureHtml5JS kan niet worden afgespeeld vanaf een http 404-antwoord.

### <a name="categorizing-errors"></a>Fouten categoriseren ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Een specifieke fout opvangen ###

De volgende code vangt slechts 404 fouten:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)