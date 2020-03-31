---
title: Uw inhoud beveiligen met Azure Media Services | Microsoft Documenten
description: In dit artikel vindt u een overzicht van inhoudsbescherming met Azure Media Services v2.
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
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460956"
---
# <a name="content-protection-overview"></a>Overzicht van inhoudsbeveiliging 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

U Azure Media Services gebruiken om uw media te beveiligen vanaf het moment dat uw computer wordt verlaten via opslag, verwerking en levering. Met Media Services u uw live en on-demand content dynamisch versleuteld leveren met Advanced Encryption Standard (AES-128) of een van de drie belangrijkste DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde clients. 

De volgende afbeelding illustreert de workflow voor inhoudsbescherming van Media Services: 

![Beveiligen met PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

In dit artikel worden concepten en terminologie uitgelegd die relevant zijn voor het begrijpen van inhoudsbescherming met Media Services. Het artikel bevat ook links naar artikelen die bespreken hoe inhoud te beschermen. 

## <a name="dynamic-encryption"></a>Dynamische versleuteling

U Media Services gebruiken om uw inhoud dynamisch versleuteld te leveren met AES clear key- of DRM-versleuteling met PlayReady, Widevine of FairPlay. Als inhoud is versleuteld met een Duidelijke AES-sleutel en via HTTPS wordt verzonden, is deze pas duidelijk als deze de client bereikt. 

Elke versleutelingsmethode ondersteunt de volgende streamingprotocollen:
 
- AES: MPEG-DASH, Smooth Streaming en HLS
- PlayReady: MPEG-DASH, Smooth Streaming en HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Versleuteling op progressieve downloads wordt niet ondersteund. 

Als u een asset wilt versleutelen, moet u een versleutelingsinhoudssleutel koppelen aan uw asset en ook een autorisatiebeleid voor de sleutel configureren. Inhoudssleutels kunnen worden opgegeven of automatisch worden gegenereerd door Media Services.

U moet ook het leveringsbeleid van het item configureren. Als u een asset met opslagversleuteling wilt streamen, moet u opgeven hoe u het wilt leveren door het beleid voor het leveren van activa te configureren.

Wanneer een stream door een speler wordt aangevraagd, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van AES clear key- of DRM-versleuteling. Om de stream te decoderen, vraagt de speler de sleutel van mediaservices- sleutelleveringsservice. Als u wilt beslissen of de gebruiker gemachtigd is om de sleutel te krijgen, evalueert de service het autorisatiebeleid dat u voor de sleutel hebt opgegeven.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 clear key vs. DRM
Klanten vragen zich vaak af of ze AES-encryptie of een DRM-systeem moeten gebruiken. Het belangrijkste verschil tussen de twee systemen is dat met AES-encryptie de inhoudssleutel wordt verzonden naar de client in een ongecodeerde indeling ("in de duidelijke"). Als gevolg hiervan kan de sleutel die wordt gebruikt om de inhoud te versleutelen in een netwerktracering op de client in platte tekst worden bekeken. AES-128 clear key encryption is geschikt voor use cases waarbij de viewer een vertrouwde partij is (bijvoorbeeld het versleutelen van bedrijfsvideo's die binnen een bedrijf worden gedistribueerd om door werknemers te worden bekeken).

PlayReady, Widevine en FairPlay bieden allemaal een hoger niveau van versleuteling in vergelijking met AES-128 clear key encryptie. De inhoudssleutel wordt versleuteld verzonden. Bovendien, decryptie wordt behandeld in een veilige omgeving op het niveau van het besturingssysteem, waar het moeilijker is voor een kwaadwillende gebruiker aan te vallen. DRM wordt aanbevolen voor use cases waarbij de viewer mogelijk geen vertrouwde partij is en u het hoogste beveiligingsniveau nodig hebt.

## <a name="storage-encryption"></a>Storage-versleuteling
U opslagversleuteling gebruiken om uw duidelijke inhoud lokaal te versleutelen met behulp van AES 256-bits versleuteling. U het vervolgens uploaden naar Azure Storage, waar het in rust versleuteld wordt opgeslagen. Activa die worden beschermd met opslagversleuteling worden automatisch onversleuteld en in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd. De assets worden optioneel opnieuw versleuteld voordat ze worden geüpload als een nieuw uitvoeritem. De primaire use case voor opslagversleuteling is wanneer u uw hoogwaardige invoermediabestanden wilt beveiligen met sterke versleuteling in rust op schijf.

Als u een opslagversleuteld asset wilt leveren, moet u het leveringsbeleid van het activum configureren, zodat Media Services weet hoe u uw inhoud wilt leveren. Voordat uw asset kan worden gestreamd, decodeert en streamt de streamingserver uw inhoud met behulp van het opgegeven leveringsbeleid (bijvoorbeeld AES, algemene versleuteling of geen versleuteling).

## <a name="types-of-encryption"></a>Typen versleuteling
PlayReady en Widevine maken gebruik van algemene versleuteling (AES CTR-modus). FairPlay maakt gebruik van AES CBC-modus encryptie. AES-128 clear key encryptie maakt gebruik van envelop encryptie.

## <a name="licenses-and-keys-delivery-service"></a>Levering van licenties en sleutels
Media Services biedt een belangrijke leveringsservice voor het leveren van DRM-licenties (PlayReady, Widevine, FairPlay) en AES-sleutels aan geautoriseerde clients. U [de Azure-portal,](media-services-portal-protect-content.md)de REST API of de Media Services SDK voor .NET gebruiken om autorisatie- en verificatiebeleid voor uw licenties en sleutels te configureren.

## <a name="control-content-access"></a>Inhoudstoegang beheren
U bepalen wie toegang heeft tot uw inhoud door het autorisatiebeleid voor inhoudssleutel te configureren. Het autorisatiebeleid voor inhoudssleutel ondersteunt open of tokenbeperking.

### <a name="open-authorization"></a>Machtiging openen
Met een open autorisatiebeleid wordt de inhoudssleutel naar elke client verzonden (geen beperking).

### <a name="token-authorization"></a>Tokenautorisatie
Met een autorisatiebeleid met tokenbeperking wordt de inhoudssleutel alleen verzonden naar een client die een geldig JSON-webtoken (JWT) of eenvoudige webtoken (SWT) in de sleutel/licentieaanvraag presenteert. Dit token moet worden uitgegeven door een security token service (STS). U Azure Active Directory gebruiken als een STS of een aangepaste STS implementeren. De STS moet zijn geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel- en uitgifteclaims die u hebt opgegeven in de configuratie van tokenbeperkingen. De mediaservices-sleutelleveringsservice retourneert de gevraagde sleutel/licentie naar de client als het token geldig is en de claims in het token overeenkomen met de aanvragen die zijn geconfigureerd voor de sleutel/licentie.

Wanneer u het beleid met beperkte tokenconfigureert, moet u de primaire verificatiesleutel, de uitgever en de doelgroepparameters opgeven. De primaire verificatiesleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de beveiligde tokenservice die het token uitgeeft. De doelgroep, ook wel scope genoemd, beschrijft de bedoeling van het token of de bron waartoe het token toegang geeft. De service voor het leveren van de sleutel van Media Services valideert dat deze waarden in het token overeenkomen met de waarden in de sjabloon.

### <a name="token-replay-prevention"></a>Token replay preventie

Met de functie *Token Replay Prevention* kunnen mediaservices-klanten een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of een licentie aan te vragen. De klant kan een `urn:microsoft:azure:mediaservices:maxuses` claim van het type toevoegen in het token, waarbij de waarde het aantal keren is dat het token kan worden gebruikt om een licentie of sleutel te verkrijgen. Alle volgende verzoeken met hetzelfde token aan Key Delivery sturen een ongeautoriseerde reactie terug. Zie hoe u de claim toevoegt in het [DRM-voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Overwegingen

* Klanten moeten controle hebben over het genereren van token. De claim moet in het token zelf worden geplaatst.
* Bij het gebruik van deze functie worden aanvragen met tokens waarvan de vervaldatum meer dan een uur verwijderd is van het moment dat het verzoek wordt ontvangen, geweigerd met een ongeautoriseerd antwoord.
* Tokens worden uniek geïdentificeerd door hun handtekening. Elke wijziging in de payload (bijvoorbeeld bijwerken naar de vervaldatum of de claim) verandert de handtekening van het token en het zal tellen als een nieuw token dat Key Delivery nog niet eerder is tegengekomen.
* Afspelen mislukt als het token `maxuses` de door de klant ingestelde waarde heeft overschreden.
* Deze functie kan worden gebruikt voor alle bestaande beveiligde inhoud (alleen het token dat is uitgegeven, moet worden gewijzigd).
* Deze functie werkt met zowel JWT als SWT.

## <a name="streaming-urls"></a>URL's voor streaming
Als uw asset is versleuteld met meer dan één DRM, gebruikt u een versleutelingstag in de streaming-URL: (format='m3u8-aapl', encryption='xxx').

De volgende overwegingen zijn van toepassing:

* Er kan niet meer dan één versleutelingstype worden opgegeven.
* Versleutelingstype hoeft niet in de URL te worden opgegeven als er slechts één versleuteling op het item is toegepast.
* Versleutelingtype is geval ongevoelig.
* De volgende versleutelingstypen kunnen worden opgegeven:

  * **cenc**: Voor PlayReady of Widevine (algemene versleuteling)
  * **cbcs-aapl:** Voor FairPlay (AES CBC encryptie)
  * **cbc**: Voor AES-envelopversleuteling

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen worden de volgende stappen beschreven om u aan de slag te gaan met inhoudsbescherming:

* [Beveiligen met opslagversleuteling](media-services-rest-storage-encryption.md)
* [Beveiligen met AES-versleuteling](media-services-protect-with-aes128.md)
* [Beschermen met PlayReady en/of Widevine](media-services-protect-with-playready-widevine.md)
* [Bescherm met FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Verwante koppelingen

* [JWT-tokenverificatie](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integratie van azure media services owin mvc-gebaseerde app met Azure Active Directory en beperken van de levering van inhoudssleutels op basis van JWT-claims](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
