---
title: Overzicht van Azure Media Services | Microsoft Docs
description: Microsoft Azure Media Services is een uitbreidbaar cloudplatform waarmee ontwikkelaars schaalbare toepassingen voor mediabeheer en -levering kunnen ontwikkelen. Dit artikel geeft een overzicht van Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 1c2d6287a89c7816c30cf26978859c07dba0251d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197501"
---
# <a name="azure-media-services-overview"></a>Overzicht van Azure Media Services 

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 3](../latest/media-services-overview.md)
> * [Versie 2](media-services-overview.md)

> [!NOTE]
> Er worden geen nieuwe functies meer aan Media Services v2 toegevoegd. <br/>Maak kennis met de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services (AMS) is een uitbreidbaar cloudplatform waarmee ontwikkelaars schaalbare toepassingen voor mediabeheer en -levering kunnen ontwikkelen. Media Services is gebaseerd op de REST API's waarmee u veilig video- of audio-inhoud kunt uploaden, opslaan, coderen en verpakken, zowel voor levering on demand als levering via livestreaming aan verschillende clients (bijvoorbeeld tv, pc en mobiele apparaten).

U kunt end-to-end-werkstromen volledig met Media Services bouwen. U kunt er ook voor kiezen om onderdelen van derde partijen voor sommige onderdelen van uw werkstroom te gebruiken. U kunt bijvoorbeeld coderen met een coderingsprogramma van een derde partij. Vervolgens kunt u uploaden, beveiligen, verpakken en leveren met Media Services. U kunt uw inhoud live streamen of on-demand leveren. 


## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Als belang rijke herinnering moet u zich houden aan alle toepasselijke wetgeving bij het gebruik van Azure Media Services en mag u Media Services of een Azure-service niet gebruiken op een manier die de rechten van anderen schendt of die schadelijk voor anderen kunnen zijn.

Voordat u een video/afbeelding naar Media Services uploadt, moet u over alle juiste rechten beschikken voor het gebruik van de video/afbeelding, inclusief, indien vereist door de wet, alle vereiste mede werkers (indien van toepassing) in de video/afbeelding, voor gebruik, verwerking en opslag van hun gegevens in Media Services en Azure. Sommige jurisdicties kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde gegevens categorieën, zoals biometrische gegevens. Voordat u Media Services en Azure gebruikt voor het verwerken en opslaan van gegevens die onder bijzondere wettelijke vereisten vallen, moet u ervoor zorgen dat u voldoet aan de wettelijke vereisten die voor u van toepassing kunnen zijn.

Ga naar het [vertrouwens centrum](https://www.microsoft.com/trust-center/?rtc=1)van micro soft voor meer informatie over naleving, privacy en beveiliging in Media Services. Raadpleeg de privacyverklaring van micro soft voor de privacy van micro soft, het verwerken en bewaren van gegevens, inclusief de manier waarop u uw gegevens kunt verwijderen, de [Privacy verklaring](https://privacy.microsoft.com/PrivacyStatement), de [voor waarden voor Online Services](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") en de [gegevens verwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Door Media Services te gebruiken, gaat u ermee akkoord dat u bent gebonden aan de OST, DPA en de privacyverklaring.
 
## <a name="prerequisites"></a>Vereisten

Als u Azure Media Services wilt gaan gebruiken, moet u over het volgende beschikken:

* Een Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com) voor meer informatie.
* Een Azure Media Services-account. Zie [Een account maken](media-services-portal-create-account.md) voor meer informatie.
* (Optioneel) Instellen van de ontwikkelomgeving. Kies .NET of REST API voor uw ontwikkelomgeving. Zie [De omgeving instellen](media-services-dotnet-how-to-use.md) voor meer informatie.

    Leer ook hoe u via [programmacode verbinding kunt maken met de AMS-API](media-services-use-aad-auth-to-access-ams-api.md).
* Een streaming-eindpunt (Standard of Premium) in de status Gestart.  Zie [Managing streaming endpoints](media-services-portal-manage-streaming-endpoints.md) (Streaming-eindpunten beheren) voor meer informatie.

## <a name="sdks-and-tools"></a>SDK's en hulpprogramma's

Als u Media Services-oplossingen wilt maken, kunt u het volgende gebruiken:

* [Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Een van de beschikbare client-SDK's:
    * Azure Media Services SDK voor .NET
    
        * [NuGet-pakket](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub-bron code](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java)
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services voor Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Dit is een niet-Microsoft-versie van een Node.js SDK. Deze wordt onderhouden door een community en biedt nog geen 100% dekking voor AMS API's).
* Bestaande hulpprogramma's:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) is een Winforms-/C#-toepassing voor Windows)

> [!NOTE]
> Zie [Aan de slag met de SDK voor Java-clients voor Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use) om de nieuwste versie van de Java-SDK op te halen en te ontwikkelen met Java. <br/>
> Als u de nieuwste PHP-SDK voor Media Services wilt downloaden, zoekt u versie 0.5.7 van het Microsoft/WindowAzure-pakket in de [Packagist-opslagplaats](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Codevoorbeelden

Zoeken naar meerdere codevoorbeelden in de galerie **Azure-codevoorbeelden**: [Azure Media Services-codevoorbeelden](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Concepten

Zie [Concepten](media-services-concepts.md) voor Azure Media Services-concepten.

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Ondersteunde scenario's en de beschikbaarheid van Media Services in datacenters

Zie voor gedetailleerde informatie [Scenarios and availability of Media Services features across datacenters](scenarios-and-availability.md) (Scenario's en beschikbaarheid van Media Services-functies via datacenters).

## <a name="service-level-agreement-sla"></a>Service Level Agreement (SLA)

Zie [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/) voor meer informatie.

Zie de sectie [Beschikbaarheid](scenarios-and-availability.md#availability) voor informatie over beschikbaarheid in datacenters.

## <a name="support"></a>Ondersteuning

[Ondersteuning van Azure](https://azure.microsoft.com/support/options/) biedt ondersteuningsopties voor Azure, met inbegrip van Media Services.

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
