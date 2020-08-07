---
title: Include-bestand
description: Include-bestand
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76694296"
---
## <a name="lifecycle"></a>Levenscyclus

Op het tabblad **levenscyclus** geeft u op wanneer de toewijzing van een gebruiker aan het toegangs pakket verloopt. U kunt ook opgeven of gebruikers hun toewijzingen kunnen uitbreiden.

1. Stel in de sectie **verloop** de **toegangs pakket toewijzingen** in **op datum**, **aantal dagen**of **nooit**.

    Selecteer voor **op datum**in de toekomst een verval datum.

    Geef voor **aantal dagen**een getal tussen 0 en 3660 dagen op.

    Op basis van uw selectie verloopt de toewijzing van een gebruiker aan het toegangs pakket op een bepaalde datum, een bepaald aantal dagen nadat ze zijn goedgekeurd of nooit.

1. Klik op **Geavanceerde verloop instellingen weer geven** om de extra instellingen weer te geven.

    ![Toegang tot verval instellingen van de levens cyclus van het pakket](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Als u wilt dat gebruikers hun toewijzingen kunnen uitbreiden, stelt u gebruikers toestaan in om de toegang tot **Ja**uit te **breiden** .

    Als uitbrei dingen zijn toegestaan in het beleid, ontvangt de gebruiker een e-mail bericht van 14 dagen en ook 1 dag voordat de toewijzing van het toegangs pakket wordt ingesteld op verlopen om de toewijzing uit te breiden. Als de gebruiker een aanvraag indient om toegang uit te breiden, moet de uitbrei ding datum of de toewijzingen verlopen zoals gedefinieerd in het beleid dat is gebruikt om de gebruiker toegang te verlenen tot het toegangs pakket. Als het beleid bijvoorbeeld aangeeft dat toewijzingen op 30 juni op verlopen zijn ingesteld, kan de maximale extensie die een gebruiker kan aanvragen, 30 juni zijn.

    Als de toegang van een gebruiker wordt uitgebreid, kunnen ze het toegangs pakket niet aanvragen na de opgegeven extensie datum (datum die is ingesteld in de tijd zone van de gebruiker die het beleid heeft gemaakt).

1. Als u goed keuring wilt vereisen voor het verlenen van een uitbrei ding, stelt **u in** **dat goed keuring is vereist voor de extensie**

    Dezelfde goedkeurings instellingen die zijn opgegeven op het tabblad **aanvragen** , worden gebruikt.

1. Klik op **volgende** of **Update**.
