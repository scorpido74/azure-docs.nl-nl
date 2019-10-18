---
title: Probeer de speech-service gratis uit
titleSuffix: Azure Cognitive Services
description: Het is eenvoudig en betaalbaar om aan de slag te gaan met de speech-service. Met een gratis proef versie van 30 dagen kunt u ontdekken wat de service kan doen en bepalen of deze geschikt is voor de behoeften van uw toepassing.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: eb4478a435fbfc899055a60e13b318be771652f7
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527539"
---
# <a name="try-speech-services-for-free"></a>Probeer spraak services gratis uit

Aan de slag met de spraak Services is eenvoudig en betaalbaar. Met een gratis proef versie van 30 dagen kunt u ontdekken wat de service kan doen en bepalen of deze geschikt is voor de behoeften van uw toepassing.

Als u meer tijd nodig hebt, meldt u zich aan voor een Microsoft Azure-account. het wordt geleverd met $200 in de service credit die u Maxi maal 30 dagen kunt gebruiken in een betaald abonnement voor spraak Services.

Ten slotte biedt de spraak Services een gratis laag die geschikt is voor het ontwikkelen van toepassingen. U kunt dit gratis abonnement blijven, zelfs nadat het tegoed van uw service verloopt.

## <a name="free-trial"></a>Gratis versie

De gratis proef versie van 30 dagen geeft u voor een beperkte periode toegang tot de prijs categorie Standard.

Meld u aan voor een gratis proef versie van 30 dagen:

1. Ga naar [proberen Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Selecteer het tabblad **spraak-api's** .

   ![Speech API-tabblad spraak Services](media/index/cognitive-services-speech-api-tab.png)

1. Onder **spraak Services**selecteert u **API-sleutel ophalen**.

   ![Speech API-API-sleutel ophalen](media/index/speech-api-get-api-key.png)

1. Ga akkoord met de voor waarden en selecteer uw land instellingen in de vervolg keuzelijst.

   ![Speech API-overeenkomst met voor waarden](media/index/speech-api-agree-to-terms.png)

1. Meld u aan met uw micro soft-, Facebook-, LinkedIn-of GitHub-account.

    U kunt zich registreren voor een gratis Microsoft-account op de [Microsoft-account Portal](https://account.microsoft.com/account). Als u aan de slag wilt gaan, selecteert u **Aanmelden met Microsoft** en selecteert u vervolgens **een account maken** wanneer u wordt gevraagd om u aan te melden. Volg de stappen om uw nieuwe Microsoft-account te maken en te controleren.

Nadat u zich hebt aangemeld om Cognitive Services te proberen, begint de gratis proef versie. De weer gegeven webpagina bevat een lijst met alle Azure Cognitive Services-services waarvoor u momenteel proef abonnementen hebt. Naast **spraak Services**worden twee abonnements sleutels weer gegeven. U kunt een van beide sleutels gebruiken in uw toepassingen.

> [!NOTE]
> Alle gratis proef abonnementen bevinden zich in de regio vs West. Wanneer u aanvragen maakt, moet u ervoor zorgen dat u het `westus`-eind punt gebruikt.

## <a name="new-azure-account"></a>Nieuw Azure-account

Nieuwe Azure-accounts ontvangen een tegoed van $200 service dat Maxi maal 30 dagen beschikbaar is. U kunt dit tegoed gebruiken om meer te weten te komen over de spraak Services of om te beginnen met het ontwikkelen van toepassingen.

Als u zich wilt aanmelden voor een nieuw Azure-account, gaat u naar de [pagina aanmelden bij Azure](https://azure.microsoft.com/free/ai/), selecteert u **gratis starten** en maakt u een nieuw Azure-account met behulp van uw Microsoft-account.

U kunt zich registreren voor een gratis Microsoft-account op de [Microsoft-account Portal](https://account.microsoft.com/account). Als u aan de slag wilt gaan, selecteert u **Aanmelden met Microsoft** en selecteert u vervolgens **een account maken** wanneer u wordt gevraagd om u aan te melden. Volg de stappen om uw nieuwe Microsoft-account te maken en te controleren.

Nadat u uw Azure-account hebt gemaakt, volgt u de stappen in de volgende sectie om een abonnement op de spraak services te starten.

## <a name="create-a-speech-resource-in-azure"></a>Een spraak bron maken in azure

Een speech Services-resource (gratis of betaalde laag) toevoegen aan uw Azure-account:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw Microsoft-account.

1. Selecteer **een resource maken** in de linkerbovenhoek van de portal.

    ![Speech API: een resource maken](media/index/speech-api-create-resource.png)

1. Zoek in het **nieuwe** venster naar **spraak**.

1. Selecteer in de zoek resultaten **spraak**.

    ![Speech API: spraak selecteren](media/index/speech-api-select-speech.png)

1. Klik onder **spraak**op de knop **maken** .

    ![Speech API-knop maken](media/index/speech-api-create-button.png)

1. Voer onder **maken**het volgende in:

   * Een naam voor de nieuwe resource. De naam helpt u bij het onderscheid tussen meerdere abonnementen op dezelfde service.
   * Kies het Azure-abonnement waaraan de nieuwe resource is gekoppeld om te bepalen hoe de kosten worden gefactureerd.
   * Kies de [regio](regions.md) waar de resource wordt gebruikt.
   * Kies een prijs categorie voor gratis of betaald. Selecteer **volledige prijs informatie weer geven** voor volledige informatie over prijzen en gebruiks quota's voor elke laag.
   * Maak een nieuwe resource groep voor dit spraak abonnement of wijs het abonnement toe aan een bestaande resource groep. Met resource groepen kunt u de verschillende Azure-abonnementen ordenen.
   * Schakel het selectie vakje **vastmaken aan dash board in als** u in de toekomst eenvoudig toegang wilt hebben tot uw abonnement.
   * Selecteer **maken.**

     ![Speech API: Create selecteren](media/index/speech-api-select-create.png)

     Het duurt even om uw nieuwe spraak bron te maken en te implementeren. Selecteer **Quick** start om informatie over uw nieuwe resource weer te geven.

     ![Speech API: de bron implementeren](media/index/speech-api-deploy-resource.png)

1. Onder **Quick**start selecteert u de koppeling **sleutels** onder stap 1 om uw abonnements sleutels weer te geven. Elk abonnement heeft twee sleutels. u kunt een van beide sleutels gebruiken in uw toepassing. Selecteer de knop naast elke sleutel om deze naar het klem bord te kopiëren om deze naar uw code te plakken.

> [!NOTE]
> U kunt een onbeperkt aantal abonnementen op de Standard-laag maken in een of meer regio's. U kunt echter slechts één gratis-laag abonnement maken. Model implementaties in de gratis laag die gedurende 7 dagen niet worden gebruikt, worden automatisch genomen.

## <a name="switch-to-a-new-subscription"></a>Overschakelen naar een nieuw abonnement

Als u wilt overschakelen van het ene naar het andere abonnement, bijvoorbeeld wanneer uw gratis proef versie verloopt of wanneer u uw toepassing publiceert, vervangt u de regio en de abonnements sleutel in uw code door de regio en de abonnements sleutel van de nieuwe Azure-resource.

> [!NOTE]
> Sleutels voor gratis proef versies worden gemaakt in de regio vs-West (`westus`). Een abonnement dat is gemaakt via het Azure-dash board, bevinden zich mogelijk in een andere regio als u dat doet.

* Als uw toepassing gebruikmaakt van een [Speech SDK](speech-sdk.md), geeft u de regio code, zoals `westus`, op bij het maken van een spraak configuratie.
* Als uw toepassing gebruikmaakt van een van de rest- [api's](rest-apis.md)van spraak Services, maakt de regio deel uit van de EINDPUNT-URI die u gebruikt bij het maken van aanvragen.

Sleutels die zijn gemaakt voor een regio, zijn alleen geldig in die regio. Als u deze probeert te gebruiken met andere regio's, worden verificatie fouten veroorzaakt.

## <a name="next-steps"></a>Volgende stappen

Voltooi een van onze Quick starts voor 10 minuten of Bekijk onze SDK-voor beelden:

> [!div class="nextstepaction"]
> [Snelstartgids: spraak herkennen in C# ](quickstart-csharp-dotnet-windows.md) 
> [Speech SDK](speech-sdk.md#get-the-samples) -voor beelden
