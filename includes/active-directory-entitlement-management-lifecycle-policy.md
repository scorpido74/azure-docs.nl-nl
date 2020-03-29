---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694296"
---
## <a name="lifecycle"></a>Levenscyclus

Op het tabblad **Levenscyclus** geeft u op wanneer de toewijzing van een gebruiker aan het toegangspakket verloopt. U ook opgeven of gebruikers hun toewijzingen kunnen uitbreiden.

1. Stel in de sectie **Verloop** de **toewijzingen van toegangspakketten in die verloopt** tot **Op datum**, **Aantal dagen**of **Nooit**.

    Selecteer **voor Op datum**een vervaldatum in de toekomst.

    Geef **voor Aantal dagen**een getal op tussen 0 en 3660 dagen.

    Op basis van uw selectie verloopt de toewijzing van een gebruiker aan het toegangspakket op een bepaalde datum, een bepaald aantal dagen nadat deze zijn goedgekeurd of nooit.

1. Klik **op Geavanceerde vervaldatums weergeven** om extra instellingen weer te geven.

    ![Toegangspakket - Instellingen voor het verlopen van de levenscyclus](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Als u de gebruiker in staat wilt stellen hun toewijzingen uit te breiden, stelt u **Gebruikers toestaan de toegang uit te breiden** tot **Ja.**

    Als extensies zijn toegestaan in het beleid, ontvangt de gebruiker 14 dagen een e-mail en ook 1 dag voordat de toewijzing van het toegangspakket verloopt met de vraag of hij de toewijzing wilt verlengen. Als de gebruiker een verzoek indient om de toegang te verlengen, moet de verlengingsdatum op of vóór het verlopen van toewijzingen zijn zoals gedefinieerd in het beleid dat is gebruikt om de gebruiker toegang te verlenen tot het toegangspakket. Als het beleid bijvoorbeeld aangeeft dat toewijzingen op 30 juni verlopen, is de maximale extensie die een gebruiker kan aanvragen 30 juni.

    Als de toegang van een gebruiker wordt verlengd, kan hij het toegangspakket niet aanvragen na de opgegeven verlengingsdatum (datum die is ingesteld in de tijdzone van de gebruiker die het beleid heeft gemaakt).

1. Als u goedkeuring wilt vereisen om een verlenging toe te kennen, stelt **u goedkeuring vereisen om verlenging van** Ja toe te **kennen.**

    Op het tabblad **Aanvragen** worden dezelfde goedkeuringsinstellingen gebruikt.

1. Klik **op Volgende** of **Bijwerken**.
