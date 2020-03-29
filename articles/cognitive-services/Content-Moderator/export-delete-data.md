---
title: Gebruikersgegevens exporteren of verwijderen - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: U hebt volledige controle over uw gegevens. Meer informatie over het bekijken, exporteren of verwijderen van uw gegevens in Inhoudsmoderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744788"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Gebruikersgegevens exporteren of verwijderen in Inhoudsmoderator

Content Moderator verzamelt gebruikersgegevens om de service te bedienen, maar klanten hebben volledige controle om hun gegevens te bekijken, te exporteren en te verwijderen met behulp van de [tool Controleren](https://contentmoderator.cognitive.microsoft.com/) en [moderatie- en beoordelings-API's](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zie de volgende tabel voor meer informatie over het exporteren en verwijderen van gebruikersgegevens in Inhoudsmoderator.

| Gegevens | Exportbewerking | Bewerking verwijderen |
| ---- | ---------------- | ---------------- |
| Accountgegevens (abonnementssleutels) | N.v.t. | Verwijderen met behulp van de Azure-portal (Azure-abonnementen). Of gebruik de knop **Team verwijderen** op de pagina Instellingen [voor ui-team controleren.](https://contentmoderator.cognitive.microsoft.com/) |
| Afbeeldingen voor aangepaste matching | Roep de [API voor afbeeldings-id's op.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676) Afbeeldingen worden opgeslagen in een eigen hash-indeling en er is geen manier om de werkelijke afbeeldingen te extraheren. | Roep de [API Alle afbeeldingen verwijderen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686)aan . Of verwijder de bron Inhoudsmoderator met behulp van de Azure-portal. |
| Voorwaarden voor aangepaste matching | Cal de [API Alle termen krijgen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Roep de [API Alle termen verwijderen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d)aan . Of verwijder de bron Inhoudsmoderator met behulp van de Azure-portal. |
| Tags | N.v.t. | Gebruik het pictogram **Verwijderen** dat beschikbaar is voor elke tag op de pagina Instellingen voor gebruikersinterface-tags controleren. Of gebruik de knop **Team verwijderen** op de pagina Instellingen [voor ui-team controleren.](https://contentmoderator.cognitive.microsoft.com/) |
| Beoordelingen | De [API voor beoordeling opvragen](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Gebruik de knop **Team verwijderen** op de pagina Instellingen van [het UI-team controleren.](https://contentmoderator.cognitive.microsoft.com/)
| Gebruikers | N.v.t. | Gebruik het pictogram **Verwijderen** dat beschikbaar is voor elke gebruiker op de pagina [Instellingen voor ui-team controleren.](https://contentmoderator.cognitive.microsoft.com/) Of gebruik de knop **Team verwijderen** op de pagina Instellingen [voor ui-team controleren.](https://contentmoderator.cognitive.microsoft.com/) |

