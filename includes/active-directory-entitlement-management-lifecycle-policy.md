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
ms.openlocfilehash: 88e11adadcc06875964146ad2046828267258038
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389213"
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

    Als uitbrei dingen zijn toegestaan in het beleid, ontvangt de gebruiker een e-mail bericht van 14 dagen en ook 1 dag voordat de toewijzing van het toegangs pakket wordt ingesteld op verlopen om de toewijzing uit te breiden.

    Als de toegang van een gebruiker wordt uitgebreid, kunnen ze het toegangs pakket niet aanvragen na de opgegeven extensie datum (datum die is ingesteld in de tijd zone van de gebruiker die het beleid heeft gemaakt).

1. Als u goed keuring wilt vereisen voor het verlenen van een uitbrei ding, stelt **u in** **dat goed keuring is vereist voor de extensie**

    Dezelfde goedkeurings instellingen die zijn opgegeven op het tabblad **aanvragen** , worden gebruikt.

1. Klik op **volgende** of **Update**.
