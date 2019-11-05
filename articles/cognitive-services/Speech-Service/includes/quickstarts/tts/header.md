---
title: 'Snelstartgids: de service spraak herkenning voor de spraak'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bekunsten van spraak met de Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503078"
---
In deze Quick start gaat u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gebruiken om tekst te converteren naar gesynthesizerde spraak. Nadat u aan enkele vereisten hebt voldaan, worden in de standaard luidsprekers alleen vier stappen weer gegeven:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````SpeechSynthesizer````-object met behulp van het ````SpeechConfig````-object.
> * Het ````SpeechSynthesizer````-object gebruiken om de tekst te spreken.
> * Controleer het ````SpeechSynthesisResult```` geretourneerd voor fouten.
