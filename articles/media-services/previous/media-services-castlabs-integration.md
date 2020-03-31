---
title: CastLabs gebruiken om Widevine-licenties te leveren aan Azure Media Services | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Media Services (AMS) gebruiken om een stream te leveren die dynamisch is versleuteld door AMS met zowel PlayReady- als Widevine-DRM's.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969136"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>castLabs gebruiken om Widevine-licenties te leveren aan Azure Media Services 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u Azure Media Services (AMS) gebruiken om een stream te leveren die dynamisch is versleuteld door AMS met zowel PlayReady- als Widevine-DRM's. De PlayReady-licentie is afkomstig van Media Services PlayReady-licentieserver en Widevine-licentie wordt geleverd door **de castLabs-licentieserver.**

Als u streaming-inhoud wilt afspelen die is beschermd door CENC (PlayReady en/of Widevine), u [Azure Media Player gebruiken.](https://aka.ms/azuremediaplayer) Zie [AMP-document](https://amp.azure.net/libs/amp/latest/docs/) voor meer informatie.

In het volgende diagram wordt een azure mediaservices op hoog niveau en de castLabs-integratiearchitectuur getoond.

![Integratie](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typisch systeem instellen

* Media-inhoud wordt opgeslagen in AMS.
* Belangrijke id's van inhoudssleutels worden opgeslagen in zowel castLabs als AMS.
* castLabs en AMS hebben beide tokenauthenticatie ingebouwd. In de volgende secties worden verificatietokens besproken. 
* Wanneer een client vraagt om de video te streamen, wordt de inhoud dynamisch versleuteld met **Common Encryption** (CENC) en dynamisch verpakt door AMS naar Smooth Streaming en DASH. We leveren ook PlayReady M2TS elementaire stream encryptie voor HLS streaming protocol.
* PlayReady-licentie wordt opgehaald van AMS-licentieserver en Widevine-licentie wordt opgehaald van castLabs-licentieserver. 
* Media Player bepaalt automatisch welke licentie u wilt ophalen op basis van de mogelijkheden van het clientplatform. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Verificatietokengeneratie voor het verkrijgen van een licentie

Zowel castLabs als AMS ondersteunen JWT -token-indeling (JSON Web Token) die wordt gebruikt om een licentie te autoriseren. 

### <a name="jwt-token-in-ams"></a>JWT-token in AMS

In de volgende tabel wordt JWT-token in AMS beschreven. 

| Verlener | Tekenreeks van de gekozen Secure Token Service (STS) |
| --- | --- |
| Doelgroep |Publiekstekenreeks van de gebruikte STS |
| Claims |Een reeks claims |
| NotBefore |Startgeldigheid van het token |
| Verloopt |Einde geldigheid van het token |
| Logopen voor ondertekening |De sleutel die wordt gedeeld tussen PlayReady License Server, castLabs License Server en STS, het kan ofwel symmetrische of asymmetrische sleutel. |

### <a name="jwt-token-in-castlabs"></a>JWT-token in castLabs

In de volgende tabel wordt JWT-token in castLabs beschreven. 

| Name | Beschrijving |
| --- | --- |
| optData |Een JSON-tekenreeks met informatie over u. |
| Crt |Een JSON-tekenreeks met informatie over het item, de licentiegegevens en afspeelrechten. |
| Iat |De huidige datumtijd in het tijdperk. |
| Gti |Een unieke id over dit token (elk token kan slechts één keer worden gebruikt in het castLabs-systeem). |

## <a name="sample-solution-setup"></a>Voorbeeldoplossing instellen

De [voorbeeldoplossing](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) bestaat uit twee projecten:

* Een console-app die kan worden gebruikt om DRM-beperkingen in te stellen voor een reeds ingenomen asset, voor zowel PlayReady als Widevine.
* Een webapplicatie die tokens uitdeelt, die kunnen worden gezien als een zeer vereenvoudigde versie van een STS.

Ga als nodig de consoletoepassing gebruiken:

1. Wijzig de app.config in het instellen van AMS-referenties, castLabs-referenties, STS-configuratie en gedeelde sleutel.
2. Upload een asset naar AMS.
3. Haal de UUID uit het geüploade item en wijzig lijn 32 in het Program.cs bestand:
   
      var objIAsset = _context. Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Gebruik een AssetId voor het benoemen van de asset in het castLabs-systeem (lijn 44 in het Program.cs bestand).
   
   U moet AssetId instellen voor **castLabs;** het moet een unieke alfanumerieke tekenreeks zijn.
5. Voer het programma uit.

Ga als lid van de webtoepassing (STS):

1. Wijzig de web.config in het instellen van castlabs merchant ID, de STS-configuratie en de gedeelde sleutel.
2. Implementeren op Azure-websites.
3. Navigeer naar de website.

## <a name="playing-back-a-video"></a>Een video afspelen

Als u een video wilt afspelen die is versleuteld met algemene versleuteling (PlayReady en/of Widevine), u de [Azure Media Player gebruiken.](https://aka.ms/azuremediaplayer) Bij het uitvoeren van de console-app worden de Inhoudssleutel-ID en de manifest-URL herhaald.

1. Open een nieuw tabblad en start uw STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Ga naar [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Plak in de streaming-URL.
4. Klik op het selectievakje **Geavanceerde opties.**
5. Selecteer **playready** en/of Widevine in de vervolgkeuzelijst Beveiliging.
6. Plak het token dat je van je STS hebt gekregen in het tekstvak Token. 
   
   De castLab-licentieserver heeft het voorvoegsel "Bearer=" niet nodig voor het token. Dus verwijder dat voordat u het token indient.
7. Werk de speler bij.
8. De video moet worden afgespeeld.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

