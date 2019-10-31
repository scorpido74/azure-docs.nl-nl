---
title: Aanvraag proces en e-mail meldingen in het beheer van rechten van Azure AD-Azure Active Directory
description: Meer informatie over het aanvraag proces voor een toegangs pakket en wanneer e-mail meldingen worden verzonden in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ce3b2cb72a7b57e556e09264cb5bd421eda0f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173754"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Aanvraag proces en e-mail meldingen in het beheer van rechten van Azure AD

Wanneer een gebruiker een aanvraag indient bij een toegangs pakket, wordt een proces gestart om die aanvraag te verzenden. Het beheer van rechten van Azure AD verzendt ook e-mail meldingen naar goed keurders en aanvragers wanneer er belang rijke gebeurtenissen optreden tijdens het proces.

In dit artikel worden het aanvraag proces beschreven en de e-mail meldingen die worden verzonden.

## <a name="request-process"></a>Aanvraag proces

Een gebruiker die toegang tot een toegangs pakket nodig heeft, kan een toegangs aanvraag indienen. Afhankelijk van de configuratie van het beleid, kan de aanvraag een goed keuring vereisen. Wanneer een aanvraag wordt goedgekeurd, begint een proces met het toewijzen van de gebruikers toegang tot elke resource in het toegangs pakket. In het volgende diagram ziet u een overzicht van het proces en de verschillende statussen.

![Diagram goedkeurings proces](./media/entitlement-management-process/request-process.png)

| Staat | Beschrijving |
| --- | --- |
| Gestelde | Gebruiker een aanvraag indient. |
| Goed keuring in behandeling | Als voor het beleid voor een toegangs pakket goed keuring is vereist, wordt een aanvraag verplaatst naar wachtende goed keuring. |
| Verlopen | Als geen goed keurders een aanvraag binnen de time-out van de goedkeurings aanvraag goed keuren, verloopt de aanvraag. Als u het opnieuw wilt proberen, moet de gebruiker de aanvraag opnieuw verzenden. |
| Verboden | Goed keurder weigert een aanvraag. |
| Goedgekeurd | Goed keurder keurt een aanvraag goed. |
| Leveren | Aan de gebruiker is **geen** toegang toegewezen tot alle resources in het toegangs pakket. Als dit een externe gebruiker is, heeft de gebruiker mogelijk nog geen toegang tot de resource directory en heeft hij de toestemming prompt geaccepteerd. |
| Afgeleverd | Aan de gebruiker is toegang toegewezen tot alle resources in het toegangs pakket. |
| Toegang uitgebreid | Als de uitbrei dingen in het beleid zijn toegestaan, wordt de toewijzing door de gebruiker uitgebreid. |
| Toegang is verlopen | De toegang van de gebruiker tot het toegangs pakket is verlopen. Als u opnieuw toegang wilt krijgen, moet de gebruiker een aanvraag indienen. |

## <a name="email-notifications"></a>E-mailmeldingen

Als u een goed keurder bent, ontvangt u e-mail meldingen wanneer u een toegangs aanvraag moet goed keuren en wanneer een toegangs aanvraag is voltooid. Als u een aanvrager bent, worden er e-mail meldingen verzonden die de status van uw aanvraag aangeven. In het volgende diagram ziet u wanneer deze e-mail meldingen worden verzonden.

![E-mail proces van recht beheer](./media/entitlement-management-process/email-notifications.png)

De volgende tabel bevat meer details over elk van deze e-mail meldingen.

| # | E-mail onderwerp | Na verzen ding | Verzonden naar |
| --- | --- | --- | --- |
| 1 | Actie vereist: Controleer de toegangs aanvraag van *[aanvrager]* naar *[toegangs pakket]* op *[datum]* | Wanneer een aanvrager een aanvraag indient voor een toegangs pakket | Alle goed keurders |
| 2 | Actie vereist: Controleer de toegangs aanvraag van *[aanvrager]* naar *[toegangs pakket]* op *[datum]* | X dagen vóór de time-out van de goedkeurings aanvraag | Alle goed keurders |
| 3 | Status melding: de toegangs aanvraag van *[aanvrager]* voor *[Access package]* is verlopen | Wanneer de goed keurders een toegangs aanvraag niet goed keuren of weigeren binnen de aanvraag duur | Aanvrager |
| 4 | Status melding: de toegangs aanvraag voor [ *aanvrager]* voor *[Access package]* is voltooid | Wanneer de eerste goed keurder een toegangs aanvraag goedkeurt of weigert | Alle goed keurders |
| 5 | U hebt geen toegang tot *[toegangs pakket]* | Wanneer een aanvrager geen toegang meer heeft tot het toegangs pakket | Aanvrager |
| 6 | U hebt nu toegang tot *[toegangs pakket]*  | Wanneer een aanvrager toegang heeft gekregen tot elke bron in het toegangs pakket | Aanvrager |
| 7 | Uw toegang tot *[toegangs pakket]* verloopt over X dag (en) | X dagen voordat de aanvrager toegang tot het toegangs pakket verloopt | Aanvrager |
| 8 | Uw toegang tot *[toegangs pakket]* is verlopen | Wanneer de toegang van de aanvrager tot een toegangs pakket verloopt | Aanvrager |

### <a name="access-request-emails"></a>E-mail berichten voor toegang

Wanneer een aanvrager een toegangs aanvraag indient voor een toegangs pakket dat is geconfigureerd om goed keuring te vereisen, ontvangen alle goed keurders die in het beleid zijn geconfigureerd, een e-mail melding met de details van de aanvraag. Details bevatten de naam van de aanvrager, de organisatie, de start-en eind datum van de toegang, indien van toepassing, zakelijke rechtvaardiging, wanneer de aanvraag is ingediend en wanneer de aanvraag verloopt. Het e-mail bericht bevat een koppeling waarbij goed keurders de toegangs aanvraag kunnen goed keuren of weigeren. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een goed keurder wanneer een aanvrager een toegangs aanvraag indient.

![E-mail van toegangs aanvraag controleren](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Goedgekeurde of geweigerde e-mail berichten

Aanvragers worden gewaarschuwd wanneer hun toegangs aanvraag wordt goedgekeurd en beschikbaar voor toegang, of wanneer hun toegangs aanvraag wordt geweigerd. Wanneer een goed keurder een toegangs aanvraag ontvangt die door een aanvrager is ingediend, kan hij of zij de toegangs aanvraag goed keuren of weigeren. De fiatteur moet een zakelijke rechtvaardiging toevoegen voor hun beslissing.

Wanneer een toegangs aanvraag wordt goedgekeurd, start het rechten beheer het proces van het verlenen van toegang tot alle resources in het toegangs pakket aan de aanvrager. Nadat de aanvrager toegang tot elke resource in het toegangs pakket heeft gekregen, wordt een e-mail melding verzonden naar de aanvrager dat hun toegangs aanvraag is goedgekeurd en dat ze nu toegang tot het toegangs pakket hebben. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer ze toegang krijgen tot een toegangs pakket.

Wanneer een toegangs aanvraag wordt geweigerd, wordt een e-mail melding verzonden naar de aanvrager. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangs aanvraag wordt geweigerd.

### <a name="expired-access-request-emails"></a>Verlopen e-mail berichten voor toegang

Aanvragers worden gewaarschuwd wanneer hun toegangs aanvraag is verlopen. Wanneer een aanvrager een toegangs aanvraag indient, heeft de aanvraag een aanvraag duur, waarna deze verloopt. Als er geen goed keurders zijn die een beslissing over goed keuren/weigeren indienen, blijft de aanvraag in de goedkeurings status in behandeling. Wanneer de aanvraag de geconfigureerde verloop tijd bereikt, verloopt de aanvraag en kan deze niet meer worden goedgekeurd of geweigerd door de goed keurders. In dit geval wordt de aanvraag verlopen. Een verlopen aanvraag kan niet meer worden goedgekeurd of geweigerd. Er wordt een e-mail melding verzonden naar de aanvrager of de toegangs aanvraag is verlopen en dat de toegangs aanvraag opnieuw moet worden ingediend. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangs aanvraag is verlopen.

![Verlopen e-mail met toegangs aanvraag](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Volgende stappen

- [Toegang aanvragen tot een toegangs pakket](entitlement-management-request-access.md)
- [Toegangs aanvragen goed keuren of weigeren](entitlement-management-request-approve.md)
