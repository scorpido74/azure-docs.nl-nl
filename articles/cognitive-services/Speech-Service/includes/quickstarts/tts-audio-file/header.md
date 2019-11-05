---
title: 'Quick Start: spraak samen te brengen in audio bestand-Speech-Service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502945"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst te converteren naar gesynthesizerde spraak in een audio bestand. Nadat u aan enkele vereisten hebt voldaan, neemt het samen brengen van spraak in een bestand alleen vijf stappen in beslag:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een audio configuratie object dat de bevat. WAV-bestands naam.
> * Maak een ````SpeechSynthesizer````-object met behulp van de bovenstaande configuratie objecten.
> * Gebruik het ````SpeechSynthesizer````-object om uw tekst te converteren naar gesynthesizerde spraak en deze op te slaan in het opgegeven audio bestand.
> * Inspecteer de ````SpeechSynthesizer```` geretourneerd voor fouten.
