---
title: Aangepaste tref woorden maken-spraak service
titleSuffix: Azure Cognitive Services
description: Het apparaat luistert altijd naar een tref woord (of woord groep). Wanneer de gebruiker het tref woord heeft gestaan, stuurt het apparaat alle volgende audio naar de Cloud totdat de gebruiker stopt met spreken. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 5bd7352230d4d9daaed219f654be51dc528bea8e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380181"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Een aangepast sleutel woord maken met behulp van speech Studio

Het apparaat luistert altijd naar een tref woord (of woord groep). "Hey Cortana" is bijvoorbeeld een tref woord voor de Cortana-assistent. Wanneer de gebruiker het tref woord heeft gestaan, stuurt het apparaat alle volgende audio naar de Cloud totdat de gebruiker stopt met spreken. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken.

In dit artikel leert u hoe u een aangepast tref woord maakt voor uw apparaat.

## <a name="create-your-keyword"></a>Uw tref woord maken

Voordat u een aangepast tref woord kunt gebruiken, moet u een tref woord maken met behulp van de [aangepaste trefwoord](https://aka.ms/sdsdk-wakewordportal) pagina in [Speech Studio](https://aka.ms/sdsdk-speechportal). Nadat u een tref woord hebt verstrekt, produceert het een bestand dat u op het apparaat implementeert.

1. Ga naar de [Speech Studio](https://aka.ms/sdsdk-speechportal) en **Meld** u aan of als u nog geen abonnement op spraak hebt, kiest u [**een abonnement maken**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Maak een **Nieuw project**op de pagina [aangepast tref woord](https://aka.ms/sdsdk-wakewordportal) . 

1. Voer een **naam**, een optionele **Beschrijving**en selecteer de taal. U hebt één project per taal nodig en de ondersteuning is momenteel beperkt tot de taal en-US.

    ![Uw trefwoord project beschrijven](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecteer uw project in de lijst. 

    ![Selecteer uw trefwoord project](media/custom-keyword/custom-kws-portal-project-list.png)

1. Als u een nieuw trefwoord model wilt starten, klikt u op **model trainen**.

1. Voer een **naam** in voor het trefwoord model en geef een optionele **Beschrijving** op en typ het **tref woord** van uw keuze en klik op **volgende**. Er zijn enkele [richt lijnen](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) die u helpen bij het kiezen van een effectief tref woord.

    ![Voer uw tref woord in](media/custom-keyword/custom-kws-portal-new-model.png)

1. De portal maakt nu kandidaat-uitspraak voor uw tref woord. Luister naar elke kandidaat door te klikken op de afspeel knoppen en de controles te verwijderen naast eventuele uitstaande uitspraaken. Als er alleen goede uitspraaken zijn ingeschakeld, klikt u op **trainen** om het tref woord te genereren. 

    ![Je tref woord controleren](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Het kan Maxi maal tien minuten duren voordat het model is gegenereerd. De lijst met tref woorden wordt gewijzigd van de **verwerking** naar **geslaagd** wanneer het model is voltooid. Vervolgens kunt u het bestand downloaden.

    ![Je tref woord controleren](media/custom-keyword/custom-kws-portal-download-model.png)

1. Sla het ZIP-bestand op uw computer. U hebt dit bestand nodig om uw aangepaste tref woord te implementeren op het apparaat.

## <a name="next-steps"></a>Volgende stappen

Test uw aangepaste tref woord met [Speech apparaten SDK Quick](https://aka.ms/sdsdk-quickstart)start.
