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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123075"
---
# <a name="paper-entity"></a>Paper-entiteit

> [!NOTE]
> De onderstaande kenmerken zijn specifiek voor de Paper-entiteit. (Ty = 0)

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
A. AfId | ID van de auteurs relatie | Int64 | Is gelijk aan
A. AfN | Naam van de auteurs aansluiting | Tekenreeks | Is gelijk aan, StartsWith
AA.AuId | Auteur-ID | Int64 | Is gelijk aan
AA.AuN | Genormaliseerde auteurs naam | Tekenreeks | Is gelijk aan, StartsWith
A. DAuN | Oorspronkelijke naam van auteur | Tekenreeks | None
A. DAfN | Naam van de oorspronkelijke aansluiting | Tekenreeks | None
A. Z | Numerieke positie in de lijst met auteurs | Int32 | Is gelijk aan
BT | BibTex-document type (' a ': journaal artikel, ' b ': boek, ' c ': boek hoofd stuk, ' p ': conferentie papier) | Tekenreeks | None
BV | Naam van BibTex-locaties | Tekenreeks | None
C.CId | Reeks-ID van vergadering | Int64 | Is gelijk aan
C.CN | Naam van de vergaderings reeks | Tekenreeks | Is gelijk aan, StartsWith
CC | Aantal citaten | Int32 | None  
CitCon | Contexten bron vermelding</br></br>Lijst met de Paper-ID waarnaar wordt verwezen en de bijbehorende context in het document (bijvoorbeeld [{123: ["bruin Foxes zijn bekend als waarnaar wordt verwezen in papier 123", "de luie honden zijn een historisch misnomer, zoals weer gegeven in Paper 123"]}) | Aangepast | None
D | Publicatie datum in de indeling JJJJ-MM-DD | Date | Is gelijk aan, IsBetween
DN-NAAM | Oorspronkelijke papier titel | Tekenreeks | None
DOI | Digitale object-id | Tekenreeks | Is gelijk aan, StartsWith
E | Uitgebreide meta gegevens</br></br>**Belang rijk**: dit kenmerk is afgeschaft en wordt alleen ondersteund voor oudere toepassingen. Als u dit kenmerk afzonderlijk aanvraagt (dat wil zeggen kenmerken = id, Ti, E), worden alle uitgebreide meta gegevens kenmerken geretourneerd in een *GESERIALISEERDE JSON-teken reeks*</br></br>Alle kenmerken in de uitgebreide meta gegevens zijn nu beschikbaar als kenmerk op het hoogste niveau en kunnen als dusdanig worden aangevraagd (bijvoorbeeld kenmerken = id, Ti, DOI, IA) | [Diakritische](#extended) | None
ECC | Aantal geschatte citaten | Int32 | None
F. DFN | Oorspronkelijk veld met studie naam | Tekenreeks | None
F.FId | Veld met studie-ID | Int64 | Is gelijk aan
F.FN | Genormaliseerd veld van studie naam | Tekenreeks | Is gelijk aan, StartsWith
FP | Eerste pagina van het document in de publicatie | Tekenreeks | Is gelijk aan
I | Publicatie probleem | Tekenreeks | Is gelijk aan
A | Omgekeerde samen vatting | [InvertedAbstract](#invertedabstract) | None
Id | Papier-ID | Int64 | Is gelijk aan
J.JId | Journaal-ID | Int64 | Is gelijk aan
J.JN | Journaal naam | Tekenreeks | Is gelijk aan, StartsWith
SNELHEID | Laatste pagina van het document in publicatie | Tekenreeks | Is gelijk aan
JAGER | Uitgever | Tekenreeks | None
PT | Publicatie type (0: onbekend, 1: journaal artikel, 2: octrooi, 3: Vergader papier, 4: boek hoofd stuk, 5: boek, 6: boek verwijzings vermelding, 7: gegevensset, 8: opslag plaats | Tekenreeks | Is gelijk aan
RId | Lijst met Paper-Id's waarnaar wordt verwezen | Int64 [] | Is gelijk aan
S | Lijst met bron-Url's van het papier, gesorteerd op relevantie | [Bron](#source)[] | None
Tij | Genormaliseerde titel | Tekenreeks | Is gelijk aan, StartsWith
V | Publicatie volume | Tekenreeks | Is gelijk aan
VFN | Volledige naam van het logboek of de vergaderings locatie | Tekenreeks | None
VSN | De korte naam van het logboek of de conferentie locatie | Tekenreeks | None
W | Unieke woorden in de titel | String[] | Is gelijk aan
J | Jaar gepubliceerd | Int32 | Is gelijk aan, IsBetween

## <a name="extended"></a>Diakritische
> [!IMPORTANT]
> Dit kenmerk is afgeschaft en wordt alleen ondersteund voor oudere toepassingen. Als u dit kenmerk afzonderlijk aanvraagt (dat wil zeggen kenmerken = id, Ti, E), worden alle uitgebreide meta gegevens kenmerken geretourneerd in een *GESERIALISEERDE JSON-teken reeks*</br></br>Alle kenmerken in de uitgebreide meta gegevens zijn nu beschikbaar als kenmerk op het hoogste niveau en kunnen als dusdanig worden aangevraagd (bijvoorbeeld kenmerken = id, Ti, DOI, IA)

> [!IMPORTANT]
> Ondersteuning voor het aanvragen van afzonderlijke uitgebreide kenmerken met behulp van de ' E '. het bereik, d.w.z. ' E. DN ', wordt afgeschaft. Hoewel dit nog steeds technisch wordt ondersteund, worden afzonderlijke uitgebreide kenmerken aangevraagd met behulp van de ' E '. het bereik leidt ertoe dat de kenmerk waarde wordt geretourneerd op twee plaatsen in het JSON-antwoord als onderdeel van het object ' E ' en als kenmerk van het hoogste niveau.

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
BT | BibTex-document type (' a ': journaal artikel, ' b ': boek, ' c ': boek hoofd stuk, ' p ': conferentie papier) | Tekenreeks | None
BV | Naam van BibTex-locaties | Tekenreeks | None
CC | Contexten bron vermelding</br></br>Lijst met de Paper-ID waarnaar wordt verwezen en de bijbehorende context in het document (bijvoorbeeld [{123: ["bruin Foxes zijn bekend als waarnaar wordt verwezen in papier 123", "de luie honden zijn een historisch misnomer, zoals weer gegeven in Paper 123"]}) | Aangepast | None
DN-NAAM | Oorspronkelijke papier titel | Tekenreeks | None
DOI | Digitale object-id | Tekenreeks | None
FP | Eerste pagina van het document in de publicatie | Tekenreeks | None
I | Publicatie probleem | Tekenreeks | None
A | Omgekeerde samen vatting | [InvertedAbstract](#invertedabstract) | None
SNELHEID | Laatste pagina van het document in publicatie | Tekenreeks | None
JAGER | Uitgever | Tekenreeks | None
S | Lijst met bron-Url's van het papier, gesorteerd op relevantie | [Bron](#source)[] | None
V | Publicatie volume | Tekenreeks | None
VFN | Volledige naam van het logboek of de vergaderings locatie | Tekenreeks | None
VSN | De korte naam van het logboek of de conferentie locatie | Tekenreeks | None

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> InvertedAbstract-kenmerken kunnen niet rechtstreeks worden aangevraagd als een retour kenmerk. Als u een afzonderlijk kenmerk nodig hebt, moet u het kenmerk ' IA ' van het hoogste niveau aanvragen, d.w.z. om IA te krijgen. IndexLength-aanvraag kenmerken = IA

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
IndexLength | Aantal items in de index (aantal woorden van de abstractie) | Int32 | None
InvertedIndex | Lijst met abstracte woorden en de bijbehorende positie in de oorspronkelijke samen vatting (bijvoorbeeld [{": [0, 15, 30]}, {" Brown ": [1]}, {" Fox ": [2]}]) | Aangepast | None

## <a name="source"></a>Bron

> [!IMPORTANT]
> Bron kenmerken kunnen niet rechtstreeks worden aangevraagd als een retour kenmerk. Als u een afzonderlijk kenmerk nodig hebt, moet u het kenmerk S van het hoogste niveau aanvragen, d.w.z. om S. U-aanvraag kenmerken te verkrijgen = S

Naam | Beschrijving | Type | Bewerkingen
--- | --- | --- | ---
Ty | Type bron-URL (1: HTML, 2: tekst, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | Tekenreeks | None
U | Bron-URL | Tekenreeks | None
