---
title: Een beperkte proef project verplaatsen naar Azure
titleSuffix: Azure Cognitive Services
description: Hebt u een beperkt proef project in Custom Vision? In dit artikel leest u hoe u het kunt verplaatsen naar Azure met een migratie script.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 5c583270428a459f500ae0e220592805fd1bf454
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718904"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Uw beperkte proef project verplaatsen naar Azure

Als Custom Vision Service de overstap naar Azure voltooit, wordt ondersteuning voor projecten met een beperkte proef versie buiten Azure beëindigd. In dit document wordt uitgelegd hoe u de Custom Vision-Api's kunt gebruiken om het project met een beperkte proef versie te kopiëren naar een Azure-resource.

Ondersteuning voor het weer geven van beperkte proef projecten op de [Custom Vision website](https://customvision.ai) is beëindigd op 25 maart 2019. In dit document wordt uitgelegd hoe u de Custom Vision-Api's gebruikt met een [migratie python-script](https://github.com/Azure-Samples/custom-vision-move-project) op github) om uw project te dupliceren naar een Azure-resource.

Raadpleeg de [release opmerkingen](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) of e-mail berichten die worden verzonden naar eigen aren van projecten met een beperkte proef versie voor meer informatie.

Met het [migratie script](https://github.com/Azure-Samples/custom-vision-move-project) kunt u een project opnieuw maken door alle tags, regio's en afbeeldingen in uw huidige iteratie te downloaden en vervolgens te uploaden. U ontvangt een nieuw project in uw nieuwe abonnement dat u vervolgens kunt trainen.

## <a name="prerequisites"></a>Vereisten

- U hebt een geldig Azure-abonnement nodig dat is gekoppeld aan het Microsoft-account-of Azure Active Directory (AAD)-account dat u wilt gebruiken om u aan te melden bij de [Custom Vision-website](https://customvision.ai). 
    - Als u geen Azure-account hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/) .
    - Raadpleeg de [hand leiding voor Azure-ontwikkel aars](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)voor een inleiding tot de concepten van Azure-abonnementen en-resources.
-  [Python](https://www.python.org/downloads/)
- [Gooien](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Custom Vision resources maken in de Azure Portal

Als u Custom Vision Service met Azure wilt gebruiken, moet u Custom Vision trainings-en Voorspellings resources maken in de [Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Meerdere projecten kunnen worden gekoppeld aan één resource. Meer informatie over [prijzen en limieten](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) is beschikbaar. Als u Custom Vision Service gratis wilt blijven gebruiken, kunt u de F0-laag selecteren in de Azure Portal. 

> [!NOTE]
> Wanneer u uw Custom Vision project verplaatst naar een Azure-resource, worden de onderliggende [machtigingen]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) van die Azure-resource overgenomen. Als andere gebruikers in uw organisatie eigenaar zijn van de Azure-resource waarin uw project zich bevindt, kunnen ze toegang krijgen tot uw project op de [Custom Vision-website](https://customvision.ai). Als u uw resources verwijdert, worden uw projecten ook verwijderd.  

## <a name="find-your-limited-trial-project-information"></a>Uw project gegevens met een beperkte proef versie zoeken

Als u uw project wilt verplaatsen, hebt u de _project-id_ en _trainings sleutel_ nodig voor het project dat u wilt migreren. Als u deze informatie niet hebt, gaat u naar [https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects) om de id en de sleutel voor elk van uw projecten te verkrijgen. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>De python-voorbeeld code gebruiken om uw project naar Azure te kopiëren

Volg de [instructies in de voorbeeld code](https://github.com/Azure-Samples/custom-vision-move-project), met behulp van de beperkte sleutel en project-id als bron materiaal en de sleutel van de nieuwe Azure-resource die u hebt gemaakt als de bestemming.

Standaard worden alle projecten met een beperkte proef versie gehost in de Azure-regio Zuid-Centraal vs.

## <a name="next-steps"></a>Volgende stappen

Uw project is nu verplaatst naar een Azure-resource. U moet uw trainings-en Voorspellings sleutels bijwerken in alle toepassingen die u hebt geschreven.

Als u uw project op de [Custom Vision website](https://customvision.ai)wilt bekijken, meldt u zich aan met hetzelfde account dat u hebt gebruikt om u aan te melden bij de Azure Portal. Als uw project niet wordt weer geven, controleert u of u zich in dezelfde [Custom Vision](https://customvision.ai) map bevindt als de map waarin uw resources zich bevinden in de Azure Portal. In zowel de Azure Portal als de CustomVision.ai kunt u uw map selecteren in het menu van de vervolg keuzelijst gebruiker in de rechter bovenhoek van het scherm.