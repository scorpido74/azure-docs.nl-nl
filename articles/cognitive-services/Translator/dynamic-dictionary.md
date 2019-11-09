---
title: Dynamische woorden lijst-Translator Text-API
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de functie voor dynamische woorden lijsten van de Azure Cognitive Services Translator Text-API gebruikt.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5f007660b98a22ff9ffad072991a39cf59518b6a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837343"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Een dynamische woorden lijst gebruiken

Als u al weet wat de vertaling is die u wilt Toep assen op een woord of een woord groep, kunt u deze als aantekeningen opgeven in de aanvraag. De dynamische woorden lijst is alleen veilig voor samengestelde samen stellingen, zoals eigen namen en product namen.

**Syntaxis**

< msTrans: Dictionary Translator = "vertaling van woord groep" > frase </msTrans: Dictionary >

**Vereiste**

* De `From` en `To` talen moeten verschillend zijn. 
* U moet de para meter `From` in uw API-omzettings aanvraag toevoegen in plaats van de functie voor automatische detectie te gebruiken. 

**Voor beeld: nl-de:**

Bron invoer: het woord < msTrans: woorden lijst vertaling =\"wordomatic\"> woord of zin </msTrans: Dictionary > een vermelding van een woorden lijst is.

Doel uitvoer: das Wort "wordomatic" ist ein Wörterbucheintrag.

Deze functie werkt op dezelfde manier met en zonder HTML-modus.

De functie moet spaarzaam worden gebruikt. De juiste en veel betere manier om de vertaling aan te passen, is door aangepaste Translator te gebruiken. Aangepaste vertaler maakt volledig gebruik van context-en statistische kansen. Als u trainings gegevens hebt of kunt maken waarin uw werk of zin in de context wordt weer gegeven, krijgt u veel betere resultaten. Meer informatie over Custom Translator vindt u op [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
