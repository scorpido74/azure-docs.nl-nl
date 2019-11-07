---
title: Uw gegevens Custom Vision Service exporteren of verwijderen
titleSuffix: Azure Cognitive Services
description: U behoudt de volledige controle over uw gegevens. In dit artikel wordt uitgelegd hoe u uw gegevens in de Custom Vision Service kunt weer geven, exporteren of verwijderen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718966"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Gebruikers gegevens exporteren of verwijderen in Custom Vision

Custom Vision verzamelt gebruikers gegevens om de service te kunnen gebruiken, maar klanten hebben volledige controle over het weer geven, exporteren en verwijderen van hun gegevens met behulp van de Custom Vision [training-api's](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zie de volgende tabel voor meer informatie over het exporteren en verwijderen van gebruikers gegevens in Custom Vision.

| Gegevens | Export bewerking | Verwijder bewerking |
| ---- | ---------------- | ---------------- |
| Account gegevens (abonnements sleutels) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Verwijderen met Azure Portal (Azure-abonnementen). Of gebruik de knop uw account verwijderen op de pagina met CustomVision.ai-instellingen (micro soft-account abonnementen) | 
| Details van herhaling | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Details van herhalings prestaties | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lijst met herhalingen | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projecten en project Details | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) en [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Afbeeldings Tags | [Gettag](https://go.microsoft.com/fwlink/?linkid=865446) en [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Installatiekopieën | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (biedt een URI voor het downloaden van afbeeldingen) en [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (biedt URI voor het downloaden van afbeeldingen) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Geëxporteerde modellen | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Verwijderd bij het verwijderen van het account |
