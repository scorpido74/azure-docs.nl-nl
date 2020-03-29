---
title: Veelgestelde vragen - Computer Vision
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op veelgestelde vragen over de Computer Vision API in Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68564606"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API veelgestelde vragen

> [!TIP]
> Als u geen antwoorden op uw vragen vinden in deze veelgestelde vragen, u de Computer Vision API-community op [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) vragen of contact opnemen met [Help en ondersteuning op UserVoice](https://cognitive.uservoice.com/)

---

**Vraag:** *Kan ik Computer Vision API trainen om aangepaste tags te gebruiken?  Ik wil bijvoorbeeld foto's van kattenrassen invoeren om de AI te 'trainen', en vervolgens de raswaarde ontvangen op een AI-verzoek.*

**Antwoord**: Deze functie is momenteel niet beschikbaar. Onze engineers werken er echter aan om deze functionaliteit naar Computer Vision te brengen.

---

**Vraag**: *Kan Computer Vision lokaal worden gebruikt zonder internetverbinding?*

**Antwoord**: We bieden momenteel geen on-premises of lokale oplossing.

---

**Vraag**: *Kan Computer Vision worden gebruikt om nummerplaten te lezen?*

**Antwoord**: De Vision API biedt een goede tekstdetectie met OCR, maar is momenteel niet geoptimaliseerd voor nummerplaten. We zijn voortdurend bezig om onze diensten te verbeteren en hebben OCR toegevoegd voor automatische nummerplaatherkenning aan onze lijst met functieverzoeken.

---

**Vraag**: *Welke soorten schrijfoppervlakken worden ondersteund voor handschriftherkenning?*

**Antwoord:** De technologie werkt met verschillende soorten oppervlakken, waaronder whiteboards, wit papier en gele plaknotities.

---

**Vraag**: *Hoe lang duurt de handschriftherkenningsbewerking?*

**Antwoord:** De hoeveelheid tijd die het kost, is afhankelijk van de lengte van de tekst. Voor langere teksten kan het enkele seconden duren. Daarom moet u, nadat de bewerking Handgeschreven tekst herkennen is voltooid, mogelijk wachten voordat u de resultaten ophalen met de bewerking resultaat van de bewerking Met handgeschreven tekst ophalen.

---

**Vraag**: *Hoe gaat de handschriftherkenningstechnologie om met tekst die is ingevoegd met behulp van een caret in het midden van een regel?*

**Antwoord**: Deze tekst wordt geretourneerd als een afzonderlijke regel door de bewerking handschriftherkenning.

---

**Vraag**: *Hoe gaat de handschriftherkenningstechnologie om met doorgestreepte woorden of lijnen?*

**Antwoord:** Als de woorden met meerdere regels worden doorgestreept om ze onherkenbaar te maken, wordt ze niet opgehaald door de bewerking handschriftherkenning. Echter, als de woorden worden doorgestreept met behulp van een enkele regel, die kruising wordt behandeld als lawaai, en de woorden nog steeds opgepikt door het handschrift herkenning operatie.

---

**Vraag**: *Welke tekstoriëntaties worden ondersteund voor de handschriftherkenningstechnologie?*

**Antwoord**: Tekst gericht op hoeken van maximaal ongeveer 30 graden tot 40 graden kan worden opgepikt door de handschriftherkenningsbewerking.

---
