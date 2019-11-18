---
title: Entiteits kenmerken van conferentie-exemplaar-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Meer informatie over de kenmerken die u kunt gebruiken met de entiteit conferentie-exemplaar in het Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146562"
---
# <a name="conference-instance-entity"></a>Entiteit van het conferentie-exemplaar

> [!NOTE]
> De volgende kenmerken zijn specifiek voor de entiteit van het conferentie-exemplaar. (Ty = ' 4 ')

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
CC      |Totaal aantal vermeldingen van conferentie-exemplaar           |Int32      |None  
CD.D    |Datum van een conferentie-exemplaar gebeurtenis    |Date       |Is gelijk aan, IsBetween
CD.T    |Titel van een conferentie-exemplaar gebeurtenis   |Date       |Is gelijk aan, IsBetween
CIARD   |De verval datum van de abstracte registratie van het conferentie-exemplaar  |Date       |Is gelijk aan, IsBetween
CIED    |Eind datum van het Vergader exemplaar    |Date       |Is gelijk aan, IsBetween
CIFVD   |Eind datum van de laatste versie van het conferentie-exemplaar  |Date       |Is gelijk aan, IsBetween
CIL     |Locatie van het Conference-exemplaar    |Tekenreeks     |Is gelijk aan, StartsWith
CIN     |Genormaliseerde naam van het Conference-exemplaar |Tekenreeks        |Is gelijk aan
CINDD   |Meldings datum van het conferentie-exemplaar   |Date       |Is gelijk aan, IsBetween
CISD    |Begin datum van het conferentie-exemplaar  |Date       |Is gelijk aan, IsBetween
CISDD   |Verval datum van de inzending van het conferentie-exemplaar     |Date       |Is gelijk aan, IsBetween
DCN     |Weergave naam van Vergader instantie  |Tekenreeks      |None
E | Uitgebreide meta gegevens</br></br>**Belang rijk**: dit kenmerk is afgeschaft en wordt alleen ondersteund voor oudere toepassingen. Als u dit kenmerk afzonderlijk aanvraagt (dat wil zeggen kenmerken = id, Ti, E), worden alle uitgebreide meta gegevens kenmerken geretourneerd in een *GESERIALISEERDE JSON-teken reeks*</br></br>Alle kenmerken in de uitgebreide meta gegevens zijn nu beschikbaar als kenmerk op het hoogste niveau en kunnen als dusdanig worden aangevraagd (bijvoorbeeld kenmerken = id, Ti, DOI, IA) | [Diakritische](#extended) | None
ECC     |Totaal aantal verwachte bron vermeldingen van conferentie-exemplaar |Int32      |None
FN | Volledige naam van het Conference-exemplaar | Tekenreeks | None
Id      |Entiteit-ID                              |Int64      |Is gelijk aan
Pc | Totaal aantal publicaties van conferentie-exemplaar | Int32 | None
Tablets. GENOEMD  |De naam van de bovenliggende conferentie reeks van het exemplaar |Tekenreeks  |Is gelijk aan
Tablets. CId |ID van de bovenliggende conferentie reeks van het exemplaar |Int64     |Is gelijk aan

## <a name="extended"></a>Diakritische

> [!IMPORTANT]
> Dit kenmerk is afgeschaft en wordt alleen ondersteund voor oudere toepassingen. Als u dit kenmerk afzonderlijk aanvraagt (dat wil zeggen kenmerken = id, Ti, E), worden alle uitgebreide meta gegevens kenmerken geretourneerd in een *GESERIALISEERDE JSON-teken reeks*</br></br>Alle kenmerken in de uitgebreide meta gegevens zijn nu beschikbaar als kenmerk op het hoogste niveau en kunnen als dusdanig worden aangevraagd (bijvoorbeeld kenmerken = id, Ti, DOI, IA)

> [!IMPORTANT]
> Ondersteuning voor het aanvragen van afzonderlijke uitgebreide kenmerken met behulp van de ' E '. het bereik, d.w.z. ' E. DN ', wordt afgeschaft. Hoewel dit nog steeds technisch wordt ondersteund, worden afzonderlijke uitgebreide kenmerken aangevraagd met behulp van de ' E '. het bereik leidt ertoe dat de kenmerk waarde wordt geretourneerd op twee plaatsen in het JSON-antwoord als onderdeel van het object ' E ' en als kenmerk van het hoogste niveau.

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
FN | Volledige naam van het Conference-exemplaar | Tekenreeks | None
Pc | Totaal aantal publicaties van conferentie-exemplaar | Int32 | None
