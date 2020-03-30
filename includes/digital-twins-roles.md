---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044937"
---
In de volgende tabel worden de rollen beschreven die beschikbaar zijn in Azure Digital Twins:

| **Rol** | **Beschrijving** | **Id** |
| --- | --- | --- |
| Ruimtebeheerder | *DE*machtiging *MAKEN, LEZEN,* *BIJWERKEN*en *Verwijderen van* de machtiging voor de opgegeven ruimte en alle knooppunten eronder. Wereldwijde toestemming. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Gebruikersbeheerder| Machtigingen voor gebruikers en gebruikersgerelateerde objecten *maken,* *LEZEN,* *BIJWERKEN*en *verwijderen.* *LEES* toestemming voor spaties. | dfaac54c-f583-4dd2-b45d-8d4bbc0a1ac |
| Apparaatbeheerder | Machtigingen voor apparaten en apparaatgerelateerde objecten *maken,* *LEZEN,* *BIJWERKEN*en *VERWIJDEREN.* *LEES* toestemming voor spaties. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Sleutelbeheerder | *De*machtiging voor toegangssleutels *MAKEN, LEZEN,* *BIJWERKEN*en *VERWIJDEREN.* *LEES* toestemming voor spaties. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Tokenbeheerder |  *Lees-* en *update-toestemming* voor toegangssleutels. *LEES* toestemming voor spaties. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Gebruiker |  *LEES* toestemming voor ruimten, sensoren en gebruikers, die de bijbehorende gerelateerde objecten bevat. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Ondersteuningsspecialist |  *LEES* toestemming voor alles behalve toegangssleutels. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Installatieprogramma voor apparaten | *Lees* en *UPDATE* toestemming voor apparaten en sensoren, die de bijbehorende gerelateerde objecten omvat. *LEES* toestemming voor spaties. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Gateway-apparaat | *CREATE-toestemming* voor sensoren. *LEES* toestemming voor apparaten en sensoren, die de bijbehorende gerelateerde objecten omvat. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |