---
title: Een Cognitive Services resource maken in de Azure Portal
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met Azure Cognitive Services door een resource te maken en te abonneren in de Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274564"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Een Cognitive Services resource maken met behulp van de Azure Portal

Gebruik deze Quick Start om een Azure Cognitive Services-resource te maken met behulp van de Azure Portal. Nadat u een Cognitive Services resource hebt gemaakt, krijgt u een eind punt en een sleutel die u kunt gebruiken om uw toepassingen te verifiëren.

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Een nieuwe Azure Cognitive Services-resource maken

Voordat u een Cognitive Services resource maakt, moet u een Azure-resource groep hebben om de resource te kunnen bevatten. Wanneer u een nieuwe resource maakt, hebt u de optie om een nieuwe resource groep te maken of een bestaande te gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resource groep maakt.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en klik op **+ een resource maken**.

    ![Cognitive Services-API's selecteren](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Zoals eerder is uitgelegd, kunt u op twee manieren een Cognitive Services resource maken, met behulp van een resource voor meerdere services of een resource met één service.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Resource met meerdere services](#tab/multiservice)

    Als u een resource voor meerdere services wilt maken, voert u **Cognitive Services** in de zoek balk in.

    ![Zoeken naar Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    Selecteer op de pagina Cognitive Services **maken**.

    ![Cognitive Services account maken](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Resource met één service](#tab/singleservice)

    Als u alle beschik bare cognitieve Services wilt weer geven, selecteert u **AI + machine learning**onder **Azure Marketplace**. Als u de service waarin u bent geïnteresseerd, niet ziet, klikt u op **alles weer geven** en bladert u naar **Cognitive Services**. Klik op **meer** weer geven om de volledige catalogus met Cognitive services te bekijken.

    Klik op **maken**wanneer u zich op de gewenste service bevindt.
    
    ![Cognitive Services-API's selecteren](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. Geef op de pagina **maken** de volgende informatie op:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Resource met meerdere services](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | Een beschrijvende naam voor uw cognitieve Services-resource. Bijvoorbeeld *MyCognitiveServicesResource*. |
    | **Abonnement** | Selecteer een van de beschik bare Azure-abonnementen. |
    | **Location** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de runtime-Beschik baarheid van uw resource. Onthoud uw Azure-locatie, omdat u deze mogelijk nodig hebt bij het aanroepen van de Azure Cognitive Services. |
    | **Prijscategorie** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/)voor de API voor meer informatie.
    | **Resourcegroep** | De Azure-resource groep die uw Cognitive Services-resource bevat. U kunt een nieuwe groep maken of toevoegen aan een bestaande groep. |

    ![Scherm voor het maken van resources](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klik op **Create**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Resource met één service](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | Een beschrijvende naam voor uw cognitieve Services-resource. Bijvoorbeeld *TextAnalyticsResource*. |
    | **Abonnement** | Selecteer een van de beschik bare Azure-abonnementen. |
    | **Location** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de runtime-Beschik baarheid van uw resource. Onthoud uw Azure-locatie, omdat u deze mogelijk nodig hebt bij het aanroepen van de Azure Cognitive Services. |
    | **Prijscategorie** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/)voor de API voor meer informatie.
    | **Resourcegroep** | De Azure-resource groep die uw Cognitive Services-resource bevat. U kunt een nieuwe groep maken of toevoegen aan een bestaande groep. |

    ![Scherm voor het maken van resources](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klik op **Create**.

    ***

## <a name="get-the-keys-for-your-resource"></a>De sleutels voor uw resource ophalen

Nadat de resource is gemaakt, krijgt u een pop-upmelding in de rechter bovenhoek van het scherm. Klik in de melding op **Ga naar resource** om de cognitieve service resource te zien die u hebt gemaakt. 

![Ga naar de cognitieve Service Resource](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

In het deel venster Quick start dat wordt geopend, hebt u toegang tot het eind punt en de sleutel.

![Sleutel en eind punt ophalen](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Prijs categorieën en facturering

De prijs categorieën (en de hoeveelheid die u ontvangt) zijn gebaseerd op het aantal trans acties dat u verzendt met behulp van uw verificatie gegevens. Met elke prijs categorie geeft u het volgende op:
* het maximum aantal toegestane trans acties per seconde (TPS).
* Service functies zijn ingeschakeld in de prijs categorie.
* De kosten voor een vooraf gedefinieerd aantal trans acties. Boven deze hoeveelheid worden er extra kosten in rekening gebracht, zoals is opgegeven in de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor uw service.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle resources in de groep verwijderd.

Ga als volgt te werk om een resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. Zoek de resource groep die u wilt verwijderen en klik met de rechter muisknop op de knop meer (...) aan de rechter kant van de vermelding.
3. Selecteer **Resourcegroep verwijderen** en bevestig dit.

## <a name="see-also"></a>Zie ook

* [Aanvragen verifiëren voor Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](Welcome.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor docker-container](cognitive-services-container-support.md)
