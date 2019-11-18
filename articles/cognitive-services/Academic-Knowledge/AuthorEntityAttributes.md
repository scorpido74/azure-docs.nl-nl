---
title: Entiteits kenmerken van auteur-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Meer informatie over de kenmerken die u kunt gebruiken met de entiteit auteur in de Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146488"
---
# <a name="author-entity"></a>Entiteit Auteur

> [!NOTE]
> De volgende kenmerken zijn specifiek voor de auteur-entiteit. (Ty = ' 1 ')

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
Id      | Entiteit-ID                             |Int64      |Is gelijk aan
AuN     | Genormaliseerde naam schrijven                    |Tekenreeks     |Is gelijk aan
CC      | Totaal aantal bron vermeldingen van auteur           |Int32      |None  
DAuN    | Weergave naam van auteur                   |Tekenreeks     |None
E | Uitgebreide meta gegevens</br></br>**Belang rijk**: dit kenmerk is afgeschaft en wordt alleen ondersteund voor oudere toepassingen. Als u dit kenmerk afzonderlijk aanvraagt (dat wil zeggen kenmerken = id, Ti, E), worden alle uitgebreide meta gegevens kenmerken geretourneerd in een *GESERIALISEERDE JSON-teken reeks*</br></br>Alle kenmerken in de uitgebreide meta gegevens zijn nu beschikbaar als kenmerk op het hoogste niveau en kunnen als dusdanig worden aangevraagd (bijvoorbeeld kenmerken = id, Ti, DOI, IA) | [Diakritische](#extended) | None
ECC     | Totaal aantal geschatte bron vermeldingen van auteur |Int32      |None
LKA. AfId | Entiteit-ID van de laatste bekende relatie die is gevonden voor de auteur | Int64 | None
LKA. AfN | De genormaliseerde naam van de laatste bekende relatie die is gevonden voor de auteur | Tekenreeks | None
Pc | Totaal aantal publicaties van auteur | Int32 | None

## <a name="extended"></a>Diakritische

> [!IMPORTANT]
> Dit kenmerk is afgeschaft en wordt alleen ondersteund voor oudere toepassingen. Als u dit kenmerk afzonderlijk aanvraagt (dat wil zeggen kenmerken = id, Ti, E), worden alle uitgebreide meta gegevens kenmerken geretourneerd in een *GESERIALISEERDE JSON-teken reeks*</br></br>Alle kenmerken in de uitgebreide meta gegevens zijn nu beschikbaar als kenmerk op het hoogste niveau en kunnen als dusdanig worden aangevraagd (bijvoorbeeld kenmerken = id, Ti, DOI, IA)

> [!IMPORTANT]
> Ondersteuning voor het aanvragen van afzonderlijke uitgebreide kenmerken met behulp van de ' E '. het bereik, d.w.z. ' E. DN ', wordt afgeschaft. Hoewel dit nog steeds technisch wordt ondersteund, worden afzonderlijke uitgebreide kenmerken aangevraagd met behulp van de ' E '. het bereik leidt ertoe dat de kenmerk waarde wordt geretourneerd op twee plaatsen in het JSON-antwoord als onderdeel van het object ' E ' en als kenmerk van het hoogste niveau.

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
LKA. AfId | Entiteit-ID van de laatste bekende relatie die is gevonden voor de auteur | Int64 | None
LKA. AfN | De genormaliseerde naam van de laatste bekende relatie die is gevonden voor de auteur | Tekenreeks | None
Pc | Totaal aantal publicaties van auteur | Int32 | None
