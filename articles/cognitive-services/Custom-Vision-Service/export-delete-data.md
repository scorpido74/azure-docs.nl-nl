---
title: Uw gegevens exporteren of verwijderen - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: U behoudt de volledige controle over uw gegevens. In dit artikel wordt uitgelegd hoe u uw gegevens bekijken, exporteren of verwijderen in de Aangepaste Vision-service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718966"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Gebruikersgegevens exporteren of verwijderen in Aangepast zicht

Custom Vision verzamelt gebruikersgegevens om de service te bedienen, maar klanten hebben volledige controle over het bekijken, exporteren en verwijderen van hun gegevens met behulp van de Custom Vision [Training API's.](https://go.microsoft.com/fwlink/?linkid=865446)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Zie de volgende tabel voor meer informatie over het exporteren en verwijderen van gebruikersgegevens in Aangepast zicht.

| Gegevens | Exportbewerking | Bewerking verwijderen |
| ---- | ---------------- | ---------------- |
| Accountgegevens (abonnementssleutels) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Verwijderen met Azure-portal (Azure-abonnementen). Of gebruik de knop Uw account verwijderen op CustomVision.ai instellingenpagina (Microsoft-accountabonnementen) | 
| Iteratiedetails | [GetIteratie](https://go.microsoft.com/fwlink/?linkid=865446) | [Deleteiteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Details iteratieprestaties | [GetiterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [Deleteiteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lijst met iteraties | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [Deleteiteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projecten en projectdetails | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) en [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [Project verwijderen](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Afbeeldingstags | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) en [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Installatiekopieën | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (biedt uri voor het downloaden van afbeeldingen) en [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (biedt uri voor het downloaden van afbeeldingen) | [Afbeeldingen verwijderen](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Geëxporteerde modellen | [GetExport](https://go.microsoft.com/fwlink/?linkid=865446) | Verwijderd bij het verwijderen van het account |
