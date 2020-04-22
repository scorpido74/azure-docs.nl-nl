---
title: Beveiligde inhoud van Azure Media Player
description: Azure Media Player ondersteunt momenteel versleutelde inhoud met AES-128-bits enveloppen en veelgebruikte versleutelde inhoud.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 64414d3ec31e8763b7c576af93374bf514141fd4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726492"
---
# <a name="protected-content"></a>Beveiligde inhoud #

Azure Media Player ondersteunt momenteel aes-128-bits envelopversleutelde inhoud en veelgebruikte versleutelde inhoud (via PlayReady en Widevine) of versleutelde inhoud via FairPlay. Als u beveiligde inhoud correct wilt afspelen, moet `protectionInfo`u Azure Media Player de . Deze informatie bestaat per bron en `<source>` kan direct `data-setup`op de tag worden toegevoegd via de .  U de `protectionInfo` parameter ook rechtstreeks als parameter toevoegen als u de bron dynamisch instelt.

`protectionInfo`accepteert een JSON-object en omvat:

- `type`of `AES` `PlayReady` of `Widevine``FairPlay`
- `certificateUrl`: dit moet een directe link zijn naar je gehoste FairPlay-cert

- `authenticationToken`: dit is een optieveld om een ongecodeerd verificatietoken toe te voegen

> [!IMPORTANT]
> Het **object certificateUrl** is alleen nodig voor FairPlay DRM.***
>[!NOTE]
> De standaard techOrder is gewijzigd om `html5FairPlayHLS` de nieuwe technologie tegemoet te komen, met name om FairPlay-content native af te spelen op browsers die het ondersteunen (Safari op OSX 8+). Als je FairPlay-content hebt om af te spelen **en** je hebt de standaard techOrder gewijzigd in een aangepaste in je applicatie, moet je deze nieuwe technologie toevoegen aan je techOrder-object. We raden je aan het op te nemen voordat silverlightSS, zodat je inhoud niet wordt afgespeeld via PlayReady.

## <a name="code-sample"></a>Codevoorbeeld ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

of, met meerdere DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Niet alle browsers/platforms zijn in staat om beveiligde inhoud af te spelen. Zie de sectie [Afspeeltechnologie](azure-media-player-playback-technology.md) voor meer informatie over wat wordt ondersteund.
> [!IMPORTANT]
> Het token dat in de speler wordt doorgegeven, is bedoeld voor beveiligde inhoud en wordt alleen gebruikt voor geverifieerde gebruikers. Er wordt aangenomen dat de toepassing SSL of een andere vorm van beveiligingsmaatregel gebruikt. Ook wordt de eindgebruiker assummed te vertrouwen om het token niet te misbruiken; als dat niet het geval is, neem dan contact op met uw security experts.

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)