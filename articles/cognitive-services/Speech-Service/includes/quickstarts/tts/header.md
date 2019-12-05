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
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818198"
---
In deze Quick start gaat u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) gebruiken om tekst te converteren naar gesynthesizerde spraak. Nadat u aan enkele vereisten hebt voldaan, worden in de standaard luidsprekers alleen vier stappen weer gegeven:
> [!div class="checklist"]
> * Een ````SpeechConfig````-object maken op basis van uw abonnements sleutel en-regio.
> * Maak een ````SpeechSynthesizer````-object met behulp van het ````SpeechConfig````-object.
> * Het ````SpeechSynthesizer````-object gebruiken om de tekst te spreken.
> * Controleer het ````SpeechSynthesisResult```` geretourneerd voor fouten.
