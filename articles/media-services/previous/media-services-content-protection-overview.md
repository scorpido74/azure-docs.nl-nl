---
title: Bescherm uw inhoud met Azure Media Services | Microsoft Docs
description: Dit artikel geeft een overzicht van de beveiliging van inhoud met Azure Media Services v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 4ff4025941e9a77148daa91995ecf182231d1f0b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976278"
---
# <a name="content-protection-overview"></a>Overzicht van de beveiliging van inhoud 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Maak kennis met de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

U kunt Azure Media Services gebruiken voor het beveiligen van uw media vanaf het moment dat het verlaten van uw computer via opslag, verwerking en levering. Met Media Services, kunt u uw live en on-demand inhoud dynamisch wordt versleuteld met Advanced Encryption Standard (AES-128) of een van de drie belangrijkste digital rights management (DRM)-systemen leveren: Microsoft PlayReady en Google Widevine Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM (PlayReady, Widevine en FairPlay) licenties voor geautoriseerde clients. 

De volgende afbeelding ziet u de Media Services content protection-werkstroom: 

![Beveiligen met PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

In dit artikel wordt uitgelegd concepten en terminologie die relevant zijn voor informatie over de beveiliging van inhoud met Media Services. Het artikel bevat ook koppelingen naar artikelen waarin wordt uitgelegd hoe u inhoud kunt beveiligen. 

## <a name="dynamic-encryption"></a>Dynamische versleuteling
 U kunt Media Services gebruiken om uw inhoud dynamisch versleuteld met AES clear key- of DRM-versleuteling met behulp van PlayReady, Widevine en FairPlay te leveren. Op dit moment kunt u de HTTP Live Streaming (HLS), MPEG DASH en Smooth Streaming-indelingen versleutelen. Het versleutelen van progressieve down loads wordt niet ondersteund. Elke versleutelings methode ondersteunt de volgende streaming-protocollen:

- AES: MPEG-DASH, Smooth Streaming en HLS
- PlayReady: MPEG-DASH, Smooth Streaming en HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Als u een Asset wilt versleutelen, moet u een versleutelings inhouds sleutel aan uw asset koppelen en ook een autorisatie beleid voor de sleutel configureren. U kunt inhouds sleutels opgeven of automatisch genereren door Media Services.

U moet ook het leverings beleid van het activum configureren. Als u een opslag versleutelde Asset wilt streamen, moet u opgeven hoe u deze wilt leveren door het beleid voor de levering van assets te configureren.

Wanneer een stroom wordt aangevraagd door een speler, Media Services maakt gebruik van de opgegeven sleutel voor het versleutelen van uw inhoud dynamisch met behulp van de lege sleutel AES of DRM-versleuteling. Voor het ontsleutelen van de stroom vraagt de speler de sleutel aan bij Media Services key delivery service. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het autorisatie beleid dat u hebt opgegeven voor de sleutel.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Clear-sleutel versus DRM
Klanten zich vaak afvragen of ze AES-versleuteling of een DRM-systeem gebruiken moeten. Het belangrijkste verschil tussen de twee systemen is dat met AES-codering van de inhoudssleutel wordt verzonden naar de client in een niet-versleutelde indeling ('in de wissen"). De sleutel die wordt gebruikt voor het versleutelen van de inhoud kan als gevolg hiervan worden weergegeven in een netwerktracering op de client in tekst zonder opmaak. AES-128 clear key-versleuteling is geschikt voor gebruik gevallen waarin de viewer voor een vertrouwde partij (bijvoorbeeld coderen bedrijfsvideo's binnen een bedrijf kan worden weergegeven door werknemers gedistribueerd).

PlayReady, Widevine en FairPlay een hogere mate van versleuteling in vergelijking tot AES-128 bieden clear key-versleuteling. De inhoudssleutel wordt verzonden in een versleutelde indeling. Bovendien wordt ontsleuteling verwerkt in een beveiligde omgeving op het niveau van het besturingssysteem, waar is het moeilijker voor een kwaadwillende gebruiker om aan te vallen. DRM wordt aanbevolen voor gebruik gevallen waarbij de viewer mogelijk niet een vertrouwde partij en u het hoogste niveau van beveiliging nodig hebt.

## <a name="storage-encryption"></a>Storage-versleuteling
U kunt opslag versleuteling gebruiken om uw ongecodeerde inhoud lokaal te versleutelen met behulp van AES 256-bits versleuteling. Vervolgens kunt u het uploaden naar Azure Storage, waar het wordt opgeslagen op rest. Assets die zijn beveiligd met opslag versleuteling, worden automatisch niet-versleuteld en in een versleuteld bestands systeem geplaatst vóór de code ring. De activa kunnen eventueel opnieuw worden versleuteld voordat ze worden geüpload als nieuwe uitvoer activa. Het primaire gebruiks voorbeeld voor opslag versleuteling is wanneer u uw invoer bestanden met een hoge kwaliteit wilt beveiligen met een sterke versleuteling van de schijf.

Als u een met opslag versleutelde Asset wilt leveren, moet u het leverings beleid van het activum configureren zodat Media Services weet hoe u uw inhoud wilt leveren. Voordat uw asset kan worden gestreamd, ontsleutelt de streaming-server uw inhoud en streamt deze met behulp van het opgegeven leverings beleid (bijvoorbeeld AES, algemene versleuteling of geen versleuteling).

## <a name="types-of-encryption"></a>Typen versleuteling
PlayReady en Widevine gebruiken algemene versleuteling (AES-plaatsings modus). FairPlay maakt gebruik van AES-versleuteling voor de CBC-modus. AES-128 Clear sleutel versleuteling maakt gebruik van envelop versleuteling.

## <a name="licenses-and-keys-delivery-service"></a>Leveringsservice voor licenties en sleutels
Media Services biedt een leveringsservice voor sleutels voor het leveren van licenties voor DRM (PlayReady, Widevine, FairPlay) en AES-sleutels naar geautoriseerde clients. U kunt [de Azure Portal](media-services-portal-protect-content.md), de rest API of de Media Services SDK voor .net gebruiken om autorisatie-en verificatie beleid te configureren voor uw licenties en sleutels.

## <a name="control-content-access"></a>Toegang tot de inhoud van besturingselement
U kunt bepalen wie toegang heeft tot uw inhoud door het autorisatie beleid voor de inhouds sleutel te configureren. Het autorisatie beleid voor inhouds sleutels ondersteunt open of Token beperking.

### <a name="open-authorization"></a>Autorisatie openen
Met een open autorisatie beleid wordt de inhouds sleutel verzonden naar een wille keurige client (geen beperking).

### <a name="token-authorization"></a>Token autorisatie
Met een autorisatie beleid waarvoor een token beperking geldt, wordt de inhouds sleutel alleen verzonden naar een client die een geldig JSON Web Token (JWT) of een eenvoudig webtoken (SWT) in de sleutel/licentie aanvraag levert. Dit token moet worden uitgegeven door een beveiligingstokenservice (STS). U kunt Azure Active Directory gebruiken als een STS of implementeren van een aangepaste STS. De STS moeten worden geconfigureerd voor het maken van een token dat is ondertekend met de opgegeven sleutel en probleem claims die u hebt opgegeven in de configuratie van de tokenbeperking. De belangrijkste leveringsservice voor Media Services retourneert de aangevraagde sleutels/licenties naar de client als het token geldig is en de claims in het token overeenkomen met die zijn geconfigureerd voor de sleutels/licenties.

Wanneer u beleid met de tokenbeperking configureert, moet u de primaire verificatiesleutel, uitgever en doelgroep parameters opgeven. De primaire verificatiesleutel bevat de sleutel die het token is ondertekend. De uitgever wordt de secure token service die de token uitgeeft. De doelgroep, ook wel genoemd bereik, het doel van het token wordt beschreven of de resource voor het token wordt toegang tot geautoriseerd. De Media Services-sleutelleveringsservice valideert dat deze waarden in het token overeenkomen met de waarden in de sjabloon.

## <a name="streaming-urls"></a>Streaming-Url's
Als uw asset is versleuteld met meer dan één DRM-bestand, gebruikt u een versleutelings code in de streaming-URL: (Format = ' 3u8-AAPL ', Encryption = ' xxx ').

De volgende overwegingen zijn van toepassing:

* Er kan niet meer dan één versleutelings type worden opgegeven.
* Het versleutelings type hoeft niet te worden opgegeven in de URL als er slechts één versleuteling is toegepast op de Asset.
* Het versleutelings type is niet hoofdletter gevoelig.
* De volgende versleutelings typen kunnen worden opgegeven:
  * **Cenc**: voor PlayReady of Widevine (algemene versleuteling)
  * **cbcs-AAPL**: voor FAIRPLAY (AES CBC-versleuteling)
  * **CBC**: voor AES-envelop versleuteling

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de service voorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen worden de volgende stappen beschreven om aan de slag te gaan met inhouds beveiliging:

* [Beveiligen met opslag versleuteling](media-services-rest-storage-encryption.md)
* [Beveiligen met AES-versleuteling](media-services-protect-with-aes128.md)
* [Beveiligen met PlayReady en/of Widevine](media-services-protect-with-playready-widevine.md)
* [Beveiligen met FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Gerelateerde koppelingen

* [Verificatie JWT-token](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Azure Media Services op OWIN MVC gebaseerde app integreren met Azure Active Directory en de levering van de inhouds sleutel beperken op basis van de JWT-claims](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
