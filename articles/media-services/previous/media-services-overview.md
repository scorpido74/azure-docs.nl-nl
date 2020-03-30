---
title: Overzicht van Azure Media Services | Microsoft Docs
description: Microsoft Azure Media Services is een uitbreidbaar cloudplatform waarmee ontwikkelaars schaalbare toepassingen voor mediabeheer en -levering kunnen ontwikkelen. In dit artikel vindt u een overzicht van Azure Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197501"
---
# <a name="azure-media-services-overview"></a>Overzicht van Azure Media Services 

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 3](../latest/media-services-overview.md)
> * [Versie 2](media-services-overview.md)

> [!NOTE]
> Er worden geen nieuwe functies meer aan Media Services v2 toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services (AMS) is een uitbreidbaar cloudplatform waarmee ontwikkelaars schaalbare toepassingen voor mediabeheer en -levering kunnen ontwikkelen. Media Services is gebaseerd op de REST API's waarmee u veilig video- of audio-inhoud kunt uploaden, opslaan, coderen en verpakken, zowel voor levering on demand als levering via livestreaming aan verschillende clients (bijvoorbeeld tv, pc en mobiele apparaten).

U kunt end-to-end-werkstromen volledig met Media Services bouwen. U kunt er ook voor kiezen om onderdelen van derde partijen voor sommige onderdelen van uw werkstroom te gebruiken. U kunt bijvoorbeeld coderen met een coderingsprogramma van een derde partij. Vervolgens kunt u uploaden, beveiligen, verpakken en leveren met Media Services. U kunt uw inhoud live streamen of on-demand leveren. 


## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Als een belangrijke herinnering moet u voldoen aan alle toepasselijke wetten bij uw gebruik van Azure Media Services en mag u Media Services of een Azure-service niet gebruiken op een manier die de rechten van anderen schendt of die schadelijk kan zijn voor anderen.

Voordat u een video/afbeelding uploadt naar Media Services, moet u over alle juiste rechten beschikken om de video/afbeelding te gebruiken, inclusief, indien vereist door de wet, alle benodigde toestemmingen van personen (indien aanwezig) in de video/afbeelding, voor het gebruik, de verwerking en de opslag van hun gegevens in Media Services en Azure. Sommige rechtsgebieden kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde categorieën gegevens, zoals biometrische gegevens. Voordat u Media Services en Azure gebruikt voor de verwerking en opslag van gegevens die aan speciale wettelijke vereisten voldoen, moet u ervoor zorgen dat dergelijke wettelijke vereisten op u worden nageleefd.

Ga naar het Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1)voor meer informatie over compliance, privacy en beveiliging in Media Services. Voor de privacyverplichtingen van Microsoft, gegevensverwerking en bewaarpraktijken, waaronder het verwijderen van uw gegevens, raadpleegt u de [privacyverklaring](https://privacy.microsoft.com/PrivacyStatement)van Microsoft, de [voorwaarden voor onlineservices](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) (OST) en [het addendum voor gegevensverwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA"). Door mediaservices te gebruiken, stemt u ermee in gebonden te zijn door de OST, DPA en de Privacyverklaring.
 
## <a name="prerequisites"></a>Vereisten

Als u Azure Media Services wilt gaan gebruiken, moet u over het volgende beschikken:

* Een Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com) voor meer informatie.
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
        * [GitHub-broncode](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java)
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services voor Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Dit is een niet-Microsoft-versie van een Node.js SDK. Deze wordt onderhouden door een community en biedt nog geen 100% dekking voor AMS API's).
* Bestaande hulpprogramma's:
    * [Azure-portal](https://portal.azure.com/)
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
