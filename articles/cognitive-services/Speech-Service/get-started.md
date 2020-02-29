---
title: Speech Service gratis uitproberen
titleSuffix: Azure Cognitive Services
description: U kunt eenvoudig en betaalbaar aan de slag met de speech-service. Er zijn twee opties beschikbaar die gratis zijn, zodat u kunt ontdekken wat de service kan doen en bepalen of deze geschikt is voor uw behoeften.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913664"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service gratis uitproberen

In dit artikel kiest u een optie om de spraak service eenvoudig gratis te testen, zodat u kunt ontdekken wat de service kan doen en bepalen of het geschikt is voor uw behoeften. Kies een van de volgende twee opties, afhankelijk van uw situatie en gebruiks voorbeeld:

- [Optie 1](#no-card): Ontvang onmiddellijk **gratis proef versie** van API-sleutels zonder creditcard gegevens (u moet een bestaand Azure-account hebben). De **gratis proef versie** duurt 30 dagen en gegevens worden aan het einde verwijderd. Met deze optie kunt u het beste snel experimenteren met de service.
- [Optie 2](#new-resource): Maak gratis een nieuwe spraak bron in azure, zonder dat hiervoor een **gratis abonnement** (creditcard gegevens vereist) wordt gebruikt. Een **gratis abonnement** heeft voornamelijk een beperkt aantal limieten dan een betaald abonnement. Deze optie is het beste als u de service wilt testen, maar ook wilt upgraden naar een betaald abonnement in de toekomst en geen gegevens meer wilt verliezen.

## <a id="no-card"></a>Probeer de spraak service zonder creditcard gegevens

Voer de volgende stappen uit om een gratis proef versie van 30 dagen te activeren en de API-sleutels op te halen. De proef periode wordt onmiddellijk gestart zodra de volgende stappen zijn voltooid.

1. Ga naar de [Probeer cognitieve service](https://azure.microsoft.com/try/cognitive-services/).
1. Selecteer het tabblad **spraak-api's** .
1. Kies **API-sleutel ophalen**.

U krijgt de facturerings opties te zien. Kies de optie gratis en lees en goed keuring van de gebruikers overeenkomst. U krijgt de toetsen die u kunt gebruiken om de speech-service 30 dagen gratis te proberen.

## <a id="new-resource"></a>Probeer de speech-service door een Azure-resource te maken

Voor de volgende stappen hebt u zowel een Microsoft-account als een Azure-account nodig. Als u geen Microsoft-account hebt, kunt u zich gratis aanmelden bij de [Microsoft-account-Portal](https://account.microsoft.com/account). Selecteer **Aanmelden met Microsoft** en selecteer vervolgens **een Microsoft-account maken**wanneer u wordt gevraagd om u aan te melden. Volg de stappen voor het maken en te controleren of uw nieuwe Microsoft-account.

Zodra u een Microsoft-account hebt, gaat u naar de [pagina aanmelden bij Azure](https://azure.microsoft.com/free/ai/), selecteert u **gratis starten**en maakt u een nieuw Azure-account met behulp van een Microsoft-account.

> [!NOTE]
> De speech-service heeft twee service lagen: gratis en abonnement, die verschillende beperkingen en voor delen hebben. Wanneer u zich aanmeldt voor een gratis Azure-account, wordt het geleverd met $200 in service tegoed dat u kunt Toep assen op een betaald spraak service-abonnement dat Maxi maal 30 dagen geldig is.
>
> Als u de gratis laag Speech-Service laag gebruikt, kunt u dit gratis abonnement blijven zelfs nadat uw gratis proef-of service tegoed verloopt.
>
> Zie [Cognitive Servicesing prijs-Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)(Engelstalig) voor meer informatie.

### <a name="create-the-resource"></a>De resource maken

Een bron van de spraak-service (gratis of betaalde laag) toevoegen aan uw Azure-account:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw Microsoft-account.

1. Selecteer **een resource maken** in de linkerbovenhoek van de portal. Als u geen **resource maken**ziet, kunt u deze altijd vinden door het samengevouwen menu linksboven te selecteren:

   ![samengevouwen navigatie knop](media/index/collapsed-nav.png)

1. Typ in het **nieuwe** venster ' Speech ' in het zoekvak en druk op ENTER.

1. Selecteer in de zoek resultaten **spraak**.

   ![Zoek resultaten voor spraak](media/index/speech-search.png)

1. Selecteer **maken**en vervolgens:

   - Geef een unieke naam op voor de nieuwe resource. De naam helpt u om onderscheid te maken tussen meerdere abonnementen die zijn gekoppeld aan dezelfde service.
   - Kies het Azure-abonnement waaraan de nieuwe resource is gekoppeld om te bepalen hoe de kosten worden in rekening gebracht.
   - Kies de [regio](regions.md) waar de resource wordt gebruikt.
   - Kies een gratis (F0) of betaalde (S0) prijs categorie. Selecteer **volledige prijs informatie weer geven**voor meer informatie over prijs-en gebruiks quota's voor elke laag.
   - Maak een nieuwe resourcegroep voor dit abonnement spraak of het abonnement toewijzen aan een bestaande resourcegroep. Resourcegroepen houdt u uw verschillende Azure-abonnementen ingedeeld.
   - Selecteer **Maken**. Hiermee gaat u naar het implementatie overzicht en worden de voortgangs berichten van de implementatie weer gegeven.

> [!NOTE]
> U kunt een onbeperkt aantal standard-laag-abonnementen maken in een of meerdere regio's. U kunt echter slechts één gratis-laag-abonnement maken. Model implementaties in de gratis laag die gedurende 7 dagen niet worden gebruikt, worden automatisch buiten gebruik gesteld.

Het kan even duren voordat u uw nieuwe spraak resource implementeert. Zodra de implementatie is voltooid, selecteert **u naar resource gaan** en selecteert u in het navigatie deel venster links de optie **sleutels** om uw abonnements sleutels voor uw spraak service weer te geven. Elk abonnement heeft twee sleutels; u kunt beide sleutels gebruiken in uw toepassing. Als u snel een sleutel naar de code-editor of een andere locatie wilt kopiëren/plakken, selecteert u de knop kopiëren naast elke sleutel en schakelt u Windows om de inhoud van het klem bord te plakken naar de gewenste locatie.

> [!IMPORTANT]
> Deze abonnements sleutels worden gebruikt om toegang te krijgen tot uw API voor de cognitieve service. Deel uw sleutels niet. Sla ze veilig op, bijvoorbeeld met behulp van Azure Key Vault. We raden u ook aan deze sleutels regel matig opnieuw te genereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Wanneer u de eerste sleutel opnieuw genereert, kunt u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="switch-to-a-new-subscription"></a>Overschakelen naar een nieuw abonnement

Als u wilt overschakelen van één abonnement naar een andere, vervangen bijvoorbeeld wanneer de gratis proefversie verloopt of bij het publiceren van uw toepassing, door de regio en abonnement sleutel in uw code de regio en abonnement sleutel van de nieuwe Azure-resource.

## <a name="about-regions"></a>Over regio's

- Als uw toepassing gebruikmaakt van een [Speech SDK](speech-sdk.md), geeft u de regio code, zoals `westus`, op bij het maken van een spraak configuratie.
- Als uw toepassing gebruikmaakt van een van de rest- [api's](rest-apis.md)van de speech-service, maakt de regio deel uit van de EINDPUNT-URI die u gebruikt bij het maken van aanvragen.
- Sleutels die zijn gemaakt voor een regio zijn alleen geldig in deze regio. Er wordt geprobeerd om ze te gebruiken met andere regio's leidt tot verificatiefouten.

## <a name="next-steps"></a>Volgende stappen

Voer een van onze quickstarts van 10 minuten of Bekijk onze SDK-voorbeelden:

> [!div class="nextstepaction"]
> [Snelstartgids: spraak herkennen in C# ](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Speech SDK](speech-sdk.md#get-the-samples) -voor beelden
