---
title: Beveiligde inhoud Azure Media Player
description: Azure Media Player ondersteunt momenteel versleutelde inhoud van AES-128 bits-enveloppen en gemeen schappelijke versleutelde inhoud.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 64414d3ec31e8763b7c576af93374bf514141fd4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726492"
---
# <a name="protected-content"></a>Beveiligde inhoud #

Azure Media Player ondersteunt momenteel versleutelde inhoud met AES-128 bits-enveloppen en gemeen schappelijke versleutelde inhoud (via PlayReady en Widevine) of versleutelde inhoud via FairPlay. Als u beveiligde inhoud op de juiste manier wilt afspelen, moet u `protectionInfo`Azure Media Player de. Deze informatie bestaat per bron en kan rechtstreeks op de `<source>` tag worden toegevoegd via de `data-setup`.  U kunt ook `protectionInfo` rechtstreeks als een para meter toevoegen als u de bron dynamisch instelt.

`protectionInfo`Hiermee wordt een JSON-object geaccepteerd en opgenomen:

- `type`: `AES` of `PlayReady` `Widevine` of`FairPlay`
- `certificateUrl`: dit moet een directe koppeling naar het gehoste FairPlay-certificaat zijn

- `authenticationToken`: dit is een optie veld om een niet-gecodeerd verificatie token toe te voegen

> [!IMPORTANT]
> Het **certificateUrl** -object is alleen nodig voor Fairplay DRM. * * *
>[!NOTE]
> De standaard techOrder is gewijzigd om de nieuwe technicus te voorzien van een `html5FairPlayHLS` eigen Fairplay-inhoud op browsers die deze ondersteunen (Safari op OSX 8 +). Als u FairPlay inhoud wilt afspelen **en** u de standaard techOrder hebt gewijzigd in een aangepaste versie van uw toepassing, moet u deze nieuwe tech toevoegen aan uw techOrder-object. We raden u aan deze op te nemen vóór Silverlight, zodat uw inhoud niet wordt afgespeeld via PlayReady.

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
> Niet alle browsers/platformen kunnen beveiligde inhoud afspelen. Zie de sectie [afspeel technologie](azure-media-player-playback-technology.md) voor meer informatie over wat er wordt ondersteund.
> [!IMPORTANT]
> Het token dat is door gegeven aan de speler is bedoeld voor beveiligde inhoud en wordt alleen gebruikt voor geverifieerde gebruikers. Er wordt van uitgegaan dat de toepassing gebruikmaakt van SSL of een andere vorm van beveiligings maatregel. De eind gebruiker is ook assummed om vertrouwd te zijn om het token niet te misbruiken. Als dat niet het geval is, moet u uw beveiligings experts betreffen.

## <a name="next-steps"></a>Volgende stappen ##

- [Snelstartgids Azure Media Player](azure-media-player-quickstart.md)