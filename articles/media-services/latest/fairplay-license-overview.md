---
title: Ondersteuning voor Media Services en Apple FairPlay-licentie - Azure | Microsoft Documenten
description: In dit onderwerp vindt u een overzicht van de vereisten en configuratie van een Apple FairPlay-licentie.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733569"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Vereisten voor en configuratie van Apple FairPlay-licenties 

Met Azure Media Services u uw HLS-inhoud versleutelen met **Apple FairPlay** (AES-128 CBC). Media Services biedt ook een service voor het leveren van FairPlay-licenties. Wanneer een speler probeert uw fairplay-beveiligde inhoud af te spelen, wordt een verzoek naar de licentieleveringsservice verzonden om een licentie te verkrijgen. Als de licentieservice de aanvraag goedkeurt, geeft deze de licentie uit die naar de client wordt verzonden en wordt deze gebruikt om de opgegeven inhoud te decoderen en af te spelen.

Media Services biedt ook API's die u gebruiken om uw FairPlay-licenties te configureren. In dit onderwerp worden fairplay-licentievereisten besproken en wordt uitgelegd hoe u een **FairPlay-licentie** configureren met behulp van MEDIA Services API's. 

## <a name="requirements"></a>Vereisten

Het volgende is vereist wanneer u Media Services gebruikt om uw HLS-inhoud te versleutelen met **Apple FairPlay** en Media Services te gebruiken om FairPlay-licenties te leveren:

* Meld u aan bij [het Apple Development Program](https://developer.apple.com/).
* Apple vereist dat de eigenaar van de inhoud het [implementatiepakket verkrijgt.](https://developer.apple.com/contact/fps/) Geef aan dat u Key Security Module (KSM) al hebt geïmplementeerd met Media Services en dat u het definitieve FPS-pakket aanvraagt. Er zijn instructies in het uiteindelijke FPS-pakket om certificering te genereren en de Application Secret Key (ASK) te verkrijgen. Je gebruikt ASK om FairPlay te configureren.
* De volgende dingen moeten worden ingesteld op de sleutel/licentieleveringskant van Media Services:

    * **App Cert (AC)**: Dit is een .pfx-bestand dat de privésleutel bevat. U maakt dit bestand en versleutelt het met een wachtwoord. Het .pfx-bestand moet in base64-indeling zijn.

        In de volgende stappen wordt beschreven hoe u een .pfx-certificaatbestand voor FairPlay genereren:

        1. Installeer OpenSSL https://slproweb.com/products/Win32OpenSSL.htmlvanaf .

            Ga naar de map waar het FairPlay-certificaat en andere bestanden die door Apple zijn geleverd.
        2. Voer de volgende opdracht uit via de opdrachtregel. Hiermee wordt het .cer-bestand omgezet in een .pem-bestand.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der-in FairPlay.cer -out FairPlay-out.pem
        3. Voer de volgende opdracht uit via de opdrachtregel. Hiermee wordt het .pem-bestand omgezet in een .pfx-bestand met de privésleutel. Het wachtwoord voor het .pfx-bestand wordt vervolgens gevraagd door OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem-passin file:privatekey-pem-pass.txt
            
    * **App Cert-wachtwoord**: het wachtwoord voor het maken van het .pfx-bestand.
    * **VRAGEN:** Deze sleutel wordt ontvangen wanneer u de certificering genereert met behulp van de Apple Developer-portal. Elk ontwikkelteam ontvangt een unieke ASK. Sla een kopie van de ASK op en bewaar deze op een veilige plaats. Je moet ASK configureren als FairPlayAsk met Media Services.
    
* De volgende dingen moeten worden ingesteld door de FPS-clientzijde:

  * **App Cert (AC):** Dit is een .cer/.der-bestand dat de openbare sleutel bevat, die het besturingssysteem gebruikt om een payload te versleutelen. Media Services moet hiervan op de hoogte zijn omdat dit door de speler vereist is. De belangrijke leveringsservice decodeert deze met behulp van de bijbehorende privésleutel.

* Als u een FairPlay-versleutelde stream wilt afspelen, krijgt u eerst een echte ASK en genereert u vervolgens een echt certificaat. Dat proces creëert alle drie de delen:

  * .der-bestand
  * .pfx-bestand
  * wachtwoord voor de .pfx

## <a name="fairplay-and-player-apps"></a>FairPlay en spelersapps

Wanneer uw inhoud is versleuteld met **Apple FairPlay,** worden de afzonderlijke video- en audiosamples versleuteld met behulp van de **AES-128 CBC-modus.** **FairPlay Streaming** (FPS) is geïntegreerd in de besturingssystemen van het apparaat, met native ondersteuning op iOS en Apple TV. Safari on OS X maakt FPS mogelijk door gebruik te maken van de EME-interfaceondersteuning (Encrypted Media Extensions).

Azure Media Player ondersteunt ook FairPlay-weergave. Zie [Azure Media Player-documentatie](https://amp.azure.net/libs/amp/latest/docs/index.html)voor meer informatie.

Je je eigen player apps ontwikkelen met behulp van de iOS SDK. Om FairPlay-content te kunnen spelen, moet je het licentie-uitwisselingsprotocol implementeren. Dit protocol is niet opgegeven door Apple. Het is aan elke app om belangrijke leveringsverzoeken te verzenden. De Media Services FairPlay key delivery service verwacht dat de SPC komt als een www-form-url gecodeerd bericht, in de volgende vorm:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay-configuratie .NET voorbeeld

U de Media Services API gebruiken om FairPlay-licenties te configureren. Wanneer de speler probeert om uw FairPlay-beschermde inhoud af te spelen, wordt een verzoek verzonden naar de licentie leveringsdienst om de licentie te verkrijgen. Als de licentieservice de aanvraag goedkeurt, geeft de service de licentie uit. Het wordt verzonden naar de client en wordt gebruikt om de opgegeven inhoud te decoderen en af te spelen.

> [!NOTE]
> Meestal wilt u fairplay-beleidsopties slechts één keer configureren, omdat u slechts één set certificering en een ASK hebt.

In het volgende voorbeeld wordt [Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) gebruikt om de licentie te configureren.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Volgende stappen

Bekijk hoe u [kunt beschermen met DRM](protect-with-drm.md)
