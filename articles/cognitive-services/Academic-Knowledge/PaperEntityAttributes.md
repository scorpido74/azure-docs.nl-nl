---
title: Kenmerken van papier entiteit-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Meer informatie over de kenmerken die u kunt gebruiken met de Paper-entiteit in de Academic Knowledge API.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c300a6477daa5759a68d5d11d40b1a71b46bd808
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793823"
---
# <a name="paper-entity"></a>Paper-entiteit

<sub>* Onderstaande kenmerken zijn specifiek voor de Paper-entiteit. (Ty = 0)</sub>

Naam | Beschrijving | Type | Operations
--- | --- | --- | ---
A. AfId | ID van de auteurs relatie | Int64 | Is gelijk aan
A. AfN | Naam van de auteurs aansluiting | Tekenreeks | Is gelijk aan, StartsWith
A. AuId | Auteur-ID | Int64 | Is gelijk aan
A. AuN | Genormaliseerde auteurs naam | Tekenreeks | Is gelijk aan, StartsWith
A. DAuN | Oorspronkelijke naam van auteur | Tekenreeks | Geen
A. DAfN | Naam van de oorspronkelijke aansluiting | Tekenreeks | Geen
A. Z | Numerieke positie in de lijst met auteurs | Int32 | Is gelijk aan
SEINGEVING | Aantal citaten | Int32 | Geen  
C. CId | Reeks-ID van vergadering | Int64 | Is gelijk aan
C.CN | Naam van de vergaderings reeks | Tekenreeks | Is gelijk aan, StartsWith
D | Publicatie datum in de indeling JJJJ-MM-DD | Datum | Is gelijk aan, IsBetween
& | Uitgebreide meta gegevens (Zie de onderstaande tabel) | Tekenreeks | N/A  
ECC | Aantal geschatte citaten | Int32 | Geen
F. DFN | Oorspronkelijk veld met studie naam | Tekenreeks | Geen
F. FId | Veld met studie-ID | Int64 | Is gelijk aan
F. FN | Genormaliseerd veld van studie naam | Tekenreeks | Is gelijk aan, StartsWith
Id | Papier-ID | Int64 | Is gelijk aan
J. JId | Journaal-ID | Int64 | Is gelijk aan
J. JN | Journaal naam | Tekenreeks | Is gelijk aan, StartsWith
PT | Publicatie type (0: onbekend, 1: journaal artikel, 2: octrooi, 3: Vergader papier, 4: boek hoofd stuk, 5: boek, 6: boek verwijzings vermelding, 7: gegevensset, 8: opslag plaats | Tekenreeks | Is gelijk aan
RId | Lijst met Paper-Id's waarnaar wordt verwezen | Int64 [] | Is gelijk aan
Tij | Genormaliseerde titel | Tekenreeks | Is gelijk aan, StartsWith
W | Unieke woorden in de titel | Teken reeks [] | Is gelijk aan
J | Jaar gepubliceerd | Int32 | Is gelijk aan, IsBetween

## <a name="extended-metadata-attributes"></a>Uitgebreide meta gegevens kenmerken ##

Naam | Beschrijving               
--- | ---
BT | BibTex-document type (' a ': journaal artikel, ' b ': boek, ' c ': boek hoofd stuk, ' p ': conferentie papier)
BW | Naam van BibTex-locaties
SEINGEVING | Citaten: een lijst met Paper-id's waarnaar wordt verwezen, en de bijbehorende context in het papier (bijvoorbeeld [{123: ["bruin Foxes zijn bekend als waarnaar wordt verwezen in papier 123", "de luie honden zijn een historisch misnomer, zoals weer gegeven in Paper 123"]})
REFERENTIES | Oorspronkelijke papier titel
DOI | Digitale object-id
FP | Eerste pagina van het document in de publicatie
I | Publicatie probleem
A | Omgekeerde samen vatting
A. IndexLength | Aantal items in de index (aantal woorden van de abstractie)
A. InvertedIndex | Lijst met abstracte woorden en de bijbehorende positie in de oorspronkelijke samen vatting (bijvoorbeeld [{": [0, 15, 30]}, {" Brown ": [1]}, {" Fox ": [2]}])
SNELHEID | Laatste pagina van het document in publicatie
JAGER | Uitgever
S | Bronnen: lijst met webbronnen van het papier, gesorteerd op statische positie
S. ty | Bron type (1: HTML, 2: tekst, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U | Bron-URL
Verticale | Publicatie volume
VFN | Volledige naam van het logboek of de vergaderings locatie
VSN | De korte naam van het logboek of de conferentie locatie
