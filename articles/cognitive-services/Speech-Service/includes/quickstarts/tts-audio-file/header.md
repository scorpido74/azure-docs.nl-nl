---
title: 'Quick Start: spraak samen te brengen in audio bestand-Speech-Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467916"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om tekst te converteren naar gesynthesizerde spraak in een audio bestand. Nadat u aan enkele vereisten hebt voldaan, neemt het samen brengen van spraak in een bestand alleen vijf stappen in beslag:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een audio configuratie object dat de bevat. WAV-bestands naam.
> * Maak een ````SpeechSynthesizer````-object met behulp van de bovenstaande configuratie objecten.
> * Gebruik het ````SpeechSynthesizer````-object om uw tekst te converteren naar gesynthesizerde spraak en deze op te slaan in het opgegeven audio bestand.
> * Inspecteer de ````SpeechSynthesizer```` geretourneerd voor fouten.
