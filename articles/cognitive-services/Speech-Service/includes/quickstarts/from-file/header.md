---
title: 'Quick Start: spraak herkennen vanuit een audio bestand-spraak service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76038138"
---
In deze Quick start gaat u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gebruiken om spraak van een audio bestand te herkennen. Nadat u aan enkele vereisten hebt voldaan, is het herkennen van spraak van een bestand slechts een paar stappen:
> [!div class="checklist"]
> * Een `SpeechConfig`-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een `AudioConfig`-object dat de bevat. WAV-bestands naam.
> * Maak een `SpeechRecognizer`-object met behulp van de bovenstaande `SpeechConfig` en `AudioConfig` objecten.
> * Gebruik het `SpeechRecognizer`-object om het herkennings proces voor één utterance te starten.
> * Controleer de geretourneerde `SpeechRecognitionResult`.
