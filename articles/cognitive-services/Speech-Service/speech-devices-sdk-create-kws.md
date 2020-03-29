---
title: Aangepaste zoekwoorden maken - Spraakservice
titleSuffix: Azure Cognitive Services
description: Uw apparaat luistert altijd naar een trefwoord (of woordgroep). Wanneer de gebruiker het trefwoord zegt, stuurt het apparaat alle volgende audio naar de cloud, totdat de gebruiker stopt met spreken. Het aanpassen van uw zoekwoord is een effectieve manier om uw apparaat te differentiëren en uw branding te versterken.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717019"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Een aangepast trefwoord maken met Spraakstudio

Uw apparaat luistert altijd naar een trefwoord (of woordgroep). 'Hey Cortana' is bijvoorbeeld een zoekwoord voor de Cortana-assistent. Wanneer de gebruiker het trefwoord zegt, stuurt het apparaat alle volgende audio naar de cloud, totdat de gebruiker stopt met spreken. Het aanpassen van uw zoekwoord is een effectieve manier om uw apparaat te differentiëren en uw branding te versterken.

In dit artikel leert u hoe u een aangepast zoekwoord voor uw apparaat maakt.

## <a name="create-your-keyword"></a>Uw trefwoord maken

Voordat u een aangepast zoekwoord gebruiken, moet u een trefwoord maken met behulp van de pagina [Aangepast trefwoord](https://aka.ms/sdsdk-wakewordportal) op [Spraakstudio.](https://aka.ms/sdsdk-speechportal) Nadat u een trefwoord hebt opgeleverd, produceert het een bestand dat u op uw apparaat implementeert.

1. Ga naar de [Spraakstudio](https://aka.ms/sdsdk-speechportal) en **Meld u aan** of kies [**een abonnement maken**](https://go.microsoft.com/fwlink/?linkid=2086754)als u nog geen spraakabonnement hebt.

1. Maak op de pagina [Aangepast trefwoord](https://aka.ms/sdsdk-wakewordportal) een **nieuw project**. 

1. Voer een **naam**in, een optionele **beschrijving**en selecteer de taal. U hebt één project per taal nodig en de ondersteuning is momenteel beperkt tot de en-Amerikaanse taal.

    ![Uw zoekwoordproject beschrijven](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecteer uw project in de lijst. 

    ![Selecteer uw trefwoordproject](media/custom-keyword/custom-kws-portal-project-list.png)

1. Als u een nieuw trefwoordmodel wilt starten, klikt u op **Treinmodel**.

1. Voer een **naam** in voor het trefwoordmodel en optionele **beschrijving** en typ het **trefwoord** van uw keuze en klik op **Volgende**. We hebben een aantal [richtlijnen](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) om te helpen bij het kiezen van een effectief zoekwoord.

    ![Voer uw trefwoord in](media/custom-keyword/custom-kws-portal-new-model.png)

1. De portal maakt nu kandidaat-uitspraken voor uw zoekwoord. Luister naar elke kandidaat door op de afspeelknoppen te klikken en verwijder de controles naast eventuele onjuistheden. Zodra alleen goede uitspraken zijn gecontroleerd, klikt u op **Trainen** om te beginnen met het genereren van het zoekwoord. 

    ![Uw zoekwoord bekijken](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Het kan tot dertig minuten duren voordat het model wordt gegenereerd. De lijst met zoekwoorden verandert van **verwerking** naar **geslaagd** wanneer het model is voltooid. U het bestand vervolgens downloaden.

    ![Uw zoekwoord bekijken](media/custom-keyword/custom-kws-portal-download-model.png)

1. Sla het .zip-bestand op uw computer op. U hebt dit bestand nodig om uw aangepaste zoekwoord op uw apparaat te implementeren.

## <a name="next-steps"></a>Volgende stappen

Test uw aangepaste zoekwoord met [Spraakapparaten SDK Quickstart](https://aka.ms/sdsdk-quickstart).
