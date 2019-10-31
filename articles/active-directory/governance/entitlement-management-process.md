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
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199924"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Aanvraag proces en e-mail meldingen in het beheer van rechten van Azure AD

Wanneer een gebruiker een aanvraag indient bij een toegangs pakket, begint een proces met het leveren van die toegangs aanvraag. Het Azure AD-rechts beheer verzendt e-mail meldingen naar goed keurders en aanvragers wanneer er belang rijke gebeurtenissen optreden tijdens het proces. In dit artikel worden het aanvraag proces en de e-mail meldingen beschreven die worden verzonden.

## <a name="request-process"></a>Aanvraag proces

Een gebruiker die toegang tot een toegangs pakket nodig heeft, kan een toegangs aanvraag indienen. Afhankelijk van de configuratie van het beleid, kan de aanvraag een goed keuring vereisen. Wanneer een aanvraag wordt goedgekeurd, begint een proces met het toewijzen van de gebruikers toegang tot elke resource in het toegangs pakket. In het volgende diagram ziet u een overzicht van het proces en de verschillende statussen:

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

Als u een goed keurder bent, ontvangt u e-mail meldingen wanneer u een toegangs aanvraag moet goed keuren en wanneer een toegangs aanvraag is voltooid. Als u een aanvrager bent, worden er e-mail meldingen verzonden die de status van uw aanvraag aangeven.

In de volgende diagrammen ziet u wanneer deze e-mail meldingen worden verzonden naar de goed keurders of de aanvrager. Raadpleeg de [tabel e-mail meldingen](entitlement-management-process.md#email-notifications-table) om het overeenkomende nummer te vinden voor de e-mail meldingen die in de diagrammen worden weer gegeven.

### <a name="primary-approvers-and-alternate-approvers"></a>Primaire goed keurders en alternatieve goed keurders
In het volgende diagram ziet u de ervaring van primaire goed keurders en alternatieve goed keurders en de e-mail meldingen die ze ontvangen tijdens het aanvraag proces:

![Proces stroom voor primaire en alternatieve goed keurders](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Aanvragers
In het volgende diagram ziet u de ervaring van aanvragers en de e-mail meldingen die worden ontvangen tijdens het aanvraag proces:

![Proces stroom van de aanvrager](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>Tabel met e-mail meldingen
De volgende tabel bevat meer details over elk van deze e-mail meldingen. Als u deze e-mail berichten wilt beheren, kunt u regels gebruiken. In Outlook kunt u bijvoorbeeld regels maken om de e-mail berichten naar een map te verplaatsen als het onderwerp woorden uit deze tabel bevat:

| # | E-mail onderwerp | Na verzen ding | Verzonden naar |
| --- | --- | --- | --- |
| 1 | Actie vereist: doorgestuurde aanvraag goed keuren of weigeren voor *[date]* | Deze e-mail wordt verzonden naar fase-1 alternatieve goed keurders (nadat de aanvraag is geëscaleerd) om actie te ondernemen. | Fase-1 alternatieve fiatteur |
| 2 | Actie vereist: aanvraag goed keuren of weigeren voor *[date]* | Deze e-mail wordt verzonden naar fase 1 primaire goed keurders, als escalatie is uitgeschakeld, om actie te ondernemen. | Fase-1 primaire goed keurder |
| 3 | Herinnering: de aanvraag goed keuren of weigeren voor *[date]* voor *[aanvrager]* | Deze herinnerings-e-mail wordt verzonden naar fase 1 primaire goed keurders, als escalatie is uitgeschakeld, om actie te ondernemen, alleen wanneer er nog geen actie is ondernomen. | Fase-1 primaire goed keurder |
| 4 | De aanvraag wordt goedgekeurd of geweigerd door *[time]* op *[date]* | Deze e-mail wordt verzonden naar fase 1 primaire goed keurders (als escalatie is ingeschakeld) om actie te ondernemen. | Fase-1 primaire goed keurder |
| 5 | Herinnering voor actie vereist: Hiermee wordt de aanvraag goedgekeurd of geweigerd voor [ *date]* voor *[aanvrager]* | Deze herinnerings-e-mail wordt verzonden naar fase 1 primaire goed keurders, als escalatie is ingeschakeld, om actie te ondernemen, alleen wanneer ze nog geen actie hebben ondernomen. | Fase-1 primaire goed keurder |
| 6 | De aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt verzonden naar fase 1 primaire goed keurders en/of fase 1 alternatieve goed keurders, van een aanvraag voor één of meerdere fase, nadat de aanvraag is verlopen. | Fase-1 primaire goed keurder, fase-1 alternatieve fiatteur |
| 7 | Aanvraag goedgekeurd voor *[aanvrager]* in *[access_package]* | Deze e-mail wordt verzonden naar de fase-1 primaire goed keurders en/of fase-1 alternatieve goed keurders, nadat een aanvraag is voltooid. | Fase-1 primaire goed keurder, fase-1 alternatieve fiatteur |
| 8 | Aanvraag goedgekeurd voor *[aanvrager]* in *[access_package]* | Deze e-mail wordt verzonden naar de fase-1 primaire goed keurders en/of fase 1 alternatieve goed keurders, van een aanvraag met twee fasen, alleen als fase-1 wordt goedgekeurd. | Fase-1 primaire goed keurder, fase-1 alternatieve fiatteur |
| 9 | De aanvraag is geweigerd voor *[access_package]* | Deze e-mail wordt alleen naar de aanvrager verzonden wanneer zijn aanvraag is geweigerd | Aanvrager |
| 10 | Uw aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt verzonden naar de aanvrager aan het einde van fase 1, van een aanvraag voor één of meerdere fase, nadat de aanvraag is verlopen. | Aanvrager |
| 18 | U hebt nu toegang tot *[access_package]* | Deze e-mail wordt verzonden naar de eind gebruikers om te beginnen met het gebruik van hun toegang. | Aanvrager |
| 19 | Toegang voor *[access_package]* uitbreiden op *[date]* | Deze e-mail wordt verzonden naar de eind gebruiker voordat de toegang verloopt. | Aanvrager |
| 20 | De toegang is beëindigd voor *[access_package]* | Deze e-mail wordt verzonden naar de eind gebruikers nadat de toegang is verlopen. | Aanvrager |

### <a name="access-request-emails"></a>E-mail berichten voor toegang

Wanneer een aanvrager een toegangs aanvraag indient voor een toegangs pakket dat is geconfigureerd om goed keuring te vereisen, ontvangt alle goed keurders die zijn toegevoegd aan het beleid een e-mail melding met de details van de aanvraag. Details bevatten de naam van de aanvrager, de organisatie, de start-en eind datum van de toegang (indien opgegeven), zakelijke rechtvaardiging, wanneer de aanvraag is ingediend en wanneer de aanvraag verloopt.

Het e-mail bericht bevat een koppeling goed keurders kunnen klikken om naar Myaccess te gaan om de toegangs aanvraag goed te keuren of te weigeren. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een goed keurder wanneer een aanvrager een toegangs aanvraag indient:

![Aanvraag voor toegang tot pakket e-mail goed keuren](./media/entitlement-management-shared/approver-request-email.png)

De primaire goed keurders worden ook een e-mail melding verzonden met een herinnering om actie te ondernemen en een beslissing te nemen voor de aanvraag. Hier volgt een voor beeld van een e-mail bericht van de primaire fiatteurs van de melding om hen te herinneren actie te ondernemen:

![E-mail voor herinnerings toegang](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>E-mail berichten alternatieve fiatteur aanvraag

Als het door sturen naar alternatieve goed keurders is ingeschakeld, per doorstuur beleid, wordt de aanvraag doorgestuurd als de aanvraag nog in behandeling is. De alternatieve goed keurder ontvangt een e-mail melding voor het goed keuren of weigeren van de aanvraag. Hier volgt een voor beeld van een e-mail bericht van de melding dat de alternatieve goed keurders worden ontvangen:

![Alternatieve aanvraag e-mail voor goed keurder](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Beide, de primaire goed keurders en de alternatieve goed keurders kunnen de aanvraag goed keuren of weigeren.

### <a name="approved-or-denied-emails"></a>Goedgekeurde of geweigerde e-mail berichten

Aanvragers worden gewaarschuwd wanneer hun toegangs aanvraag wordt goedgekeurd en beschikbaar voor toegang, of wanneer hun toegangs aanvraag wordt geweigerd. Wanneer een goed keurder een toegangs aanvraag ontvangt die door een aanvrager is ingediend, kan hij of zij de toegangs aanvraag goed keuren of weigeren. De fiatteur moet een zakelijke rechtvaardiging toevoegen voor hun beslissing. Hier volgt een voor beeld van een e-mail bericht dat wordt verzonden naar primaire of alternatieve goed keurders nadat een aanvraag is goedgekeurd:

![E-mail van toegangs aanvraag controleren](./media/entitlement-management-process/approver-request-email-approved.png)

Wanneer een toegangs aanvraag wordt goedgekeurd en de toegang is ingericht, wordt er een e-mail melding verzonden naar de aanvrager dat ze nu toegang tot het toegangs pakket hebben. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer ze toegang krijgen tot een toegangs pakket:

![Verlopen e-mail met toegangs aanvraag](./media/entitlement-management-process/requestor-email-approved.png)

Wanneer een toegangs aanvraag wordt geweigerd, wordt een e-mail melding verzonden naar de aanvrager. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangs aanvraag wordt geweigerd:

![E-mail afgewezen aanvraag geweigerd](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>Verlopen e-mail berichten voor toegang

Toegangs aanvragen kunnen verlopen als geen fiatteur de aanvraag heeft goedgekeurd of geweigerd. 

Wanneer de aanvraag de geconfigureerde verloop datum bereikt en verloopt, kan deze niet meer worden goedgekeurd of geweigerd door de goed keurders. Hier volgt een voor beeld van een e-mail bericht van de melding die wordt verzonden naar alle primaire en alternatieve goed keurders:

 ![E-mail met verlopen toegang aanvragen voor goed keurders](./media/entitlement-management-process/approver-request-email-expired.png)

 Er wordt ook een e-mail melding verzonden naar de aanvrager, waarin wordt gemeld dat hun toegangs aanvraag is verlopen en dat de toegangs aanvraag opnieuw moet worden ingediend. Hier volgt een voor beeld van een e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangs aanvraag is verlopen:

![E-mail met verlopen verzoeken voor aanvrager](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Volgende stappen

- [Toegang aanvragen tot een toegangs pakket](entitlement-management-request-access.md)
- [Toegangs aanvragen goed keuren of weigeren](entitlement-management-request-approve.md)
