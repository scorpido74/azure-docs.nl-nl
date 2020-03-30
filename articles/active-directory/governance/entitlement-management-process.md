---
title: Aanvraagproces & meldingen - Azure AD-rechtenbeheer
description: Meer informatie over het aanvraagproces voor een toegangspakket en wanneer e-mailmeldingen worden verzonden in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128526"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Proces- en e-mailmeldingen aanvragen in Azure AD-rechtenbeheer

Wanneer een gebruiker een verzoek indient bij een toegangspakket, begint een proces met het leveren van dat toegangsverzoek. Azure AD-rechtenbeheer stuurt e-mailmeldingen naar fiatteurs en aanvragers wanneer belangrijke gebeurtenissen tijdens het proces plaatsvinden. In dit artikel worden de aanvraagprocedure en de verzonden e-mailmeldingen beschreven.

## <a name="request-process"></a>Aanvraagprocedure

Een gebruiker die toegang nodig heeft tot een toegangspakket kan een toegangsverzoek indienen. Afhankelijk van de configuratie van het beleid is de aanvraag mogelijk een goedkeuring nodig. Wanneer een aanvraag wordt goedgekeurd, begint een proces om de gebruiker toegang te geven tot elke bron in het toegangspakket. Het volgende diagram toont een overzicht van het proces en de verschillende toestanden:

![Diagram voor goedkeuringsproces](./media/entitlement-management-process/request-process.png)

| Status | Beschrijving |
| --- | --- |
| Ingediend | De gebruiker dient een verzoek in. |
| In afwachting van goedkeuring | Als het beleid voor een toegangspakket goedkeuring vereist, wordt een aanvraag verplaatst naar goedkeuring in behandeling. |
| Verlopen | Als geen fiatteurs een aanvraag goedkeuren binnen de time-out van de goedkeuringsaanvraag, verloopt de aanvraag. Om het opnieuw te proberen, moet de gebruiker zijn verzoek opnieuw indienen. |
| Geweigerd | De fiatter ontkent een verzoek. |
| Goedgekeurd | De fiattiser keurt een verzoek goed. |
| Leveren | De gebruiker heeft **geen** toegang gekregen tot alle bronnen in het toegangspakket. Als dit een externe gebruiker is, heeft de gebruiker mogelijk nog geen toegang tot de bronmap. Zij kunnen ook niet hebben aanvaard de toestemming prompt. |
| Afgeleverd | De gebruiker heeft toegang gekregen tot alle bronnen in het toegangspakket. |
| Toegang verlengd | Als extensies zijn toegestaan in het beleid, heeft de gebruiker de toewijzing verlengd. |
| Toegang verlopen | De toegang van de gebruiker tot het toegangspakket is verlopen. Om weer toegang te krijgen, moet de gebruiker een verzoek indienen. |

## <a name="email-notifications"></a>E-mailmeldingen

Als je een goedkeurder bent, krijg je e-mailmeldingen wanneer je een toegangsverzoek moet goedkeuren. U ontvangt ook meldingen wanneer een toegangsverzoek is voltooid. Je ontvangt ook e-mailmeldingen die de status van je verzoek aangeven als je een aanvrager bent.

De volgende diagrammen geven aan wanneer deze e-mailmeldingen worden verzonden naar de fiatteurs of de aanvrager. Verwijs naar de [tabel met e-mailmeldingen](entitlement-management-process.md#email-notifications-table) om het bijbehorende nummer te vinden voor de e-mailmeldingen die in de diagrammen worden weergegeven.

### <a name="first-approvers-and-alternate-approvers"></a>Eerste fiatteurs en alternatieve fiatteurs
In het volgende diagram ziet u de ervaring van de eerste fiatteurs en alternatieve fiatteurs en de e-mailmeldingen die ze tijdens het aanvraagproces ontvangen:

![Eerste en alternatieve goedkeurders processtroom](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Aanvragers
Het volgende diagram toont de ervaring van aanvragers en de e-mailmeldingen die ze ontvangen tijdens het aanvraagproces:

![Processtroom van aanvrager](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Goedkeuring in 2 fasen
Het volgende diagram toont de ervaring van stap-1- en fase-2-fiatteurs en de e-mailmeldingen die ze tijdens het aanvraagproces ontvangen:

![Goedkeuringsprocesstroom in 2 fasen](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabel E-mailmeldingen
In de volgende tabel vindt u meer informatie over elk van deze e-mailmeldingen. Om deze e-mails te beheren, u regels gebruiken. In Outlook u bijvoorbeeld regels maken om de e-mails naar een map te verplaatsen als het onderwerp woorden uit deze tabel bevat:

| # | Onderwerp van e-mail | Wanneer verzonden | Verzonden naar |
| --- | --- | --- | --- |
| 1 | Vereiste actie: doorgestuurde aanvraag goedkeuren of weigeren op *[datum]* | Deze e-mail wordt verzonden naar alternatieve fiatteurs in fase 1 (nadat het verzoek is geëscaleerd) om actie te ondernemen. | Alternatieve fiatteurs fase 1 |
| 2 | Actie vereist: verzoek goedkeuren of weigeren op *[datum]* | Deze e-mail wordt verzonden naar de eerste goedkeurder, als escalatie is uitgeschakeld, om actie te ondernemen. | Eerste keurgever |
| 3 | Herinnering: Het verzoek goedkeuren of weigeren op *[datum]* voor *[aanvrager]* | Deze herinneringse-mail wordt naar de eerste goedkeurder verzonden als escalatie is uitgeschakeld. De e-mail vraagt hen om actie te ondernemen als ze dat niet hebben gedaan. | Eerste keurgever |
| 4 | Het verzoek *v.v. [tijd]* op *[datum]* goedkeuren of weigeren | Deze e-mail wordt verzonden naar de eerste goedkeurder (als escalatie is ingeschakeld) om actie te ondernemen. | Eerste keurgever |
| 5 | Herinnering waarvoor actie vereist is: het verzoek goedkeuren of weigeren op *[datum]* voor *[aanvrager]* | Deze herinneringse-mail wordt naar de eerste goedkeurder verzonden als escalatie is ingeschakeld. De e-mail vraagt hen om actie te ondernemen als ze dat niet hebben gedaan. | Eerste keurgever |
| 6 | Aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt verzonden naar de eerste goedkeurder en fase-1 alternatieve fiatteurs nadat het verzoek is verlopen. | Eerste keurgever, fase-1 alternatieve fiatteurs |
| 7 | Verzoek goedgekeurd voor *[aanvrager]* aan *[access_package]* | Deze e-mail wordt verzonden naar de eerste keursman en fase-1 alternatieve fiatteurs op verzoek voltooiing. | Eerste keurgever, fase-1 alternatieve fiatteurs |
| 8 | Verzoek goedgekeurd voor *[aanvrager]* aan *[access_package]* | Deze e-mail wordt verzonden naar de eerste goedkeurder en fase-1 alternatieve fiatteurs van een 2-fase aanvraag wanneer de fase-1 aanvraag wordt goedgekeurd. | Eerste keurgever, fase-1 alternatieve fiatteurs |
| 9 | Verzoek afgewezen om *[access_package]* | Deze e-mail wordt naar de aanvrager verzonden wanneer hun verzoek wordt geweigerd | Requestor |
| 10 | Uw aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt naar de aanvrager gestuurd aan het einde van een aanvraag of 2-fase aanvraag. De e-mail laat de aanvrager weten dat het verzoek is verlopen. | Requestor |
| 11 | Actie vereist: verzoek goedkeuren of weigeren op *[datum]* | Deze e-mail wordt naar de tweede goedkeurder gestuurd om actie te ondernemen als escalatie is uitgeschakeld. | Tweede keurgever |
| 12 | Herinnering voor vereiste actie: het verzoek goedkeuren of weigeren op *[datum]* | Deze herinneringse-mail wordt naar de tweede goedkeurder verzonden als escalatie is uitgeschakeld. De melding vraagt hen om actie te ondernemen als ze dat nog niet hebben gedaan. | Tweede keurgever |
| 13 | Vereiste actie: Het verzoek op *[datum]* goedkeuren of weigeren voor *[aanvrager]* | Deze e-mail wordt verzonden naar de tweede goedkeurder, als escalatie is ingeschakeld, om actie te ondernemen. | Tweede keurgever |
| 14 | Herinnering waarvoor actie vereist is: het verzoek goedkeuren of weigeren op *[datum]* voor *[aanvrager]* | Deze herinneringse-mail wordt naar de tweede goedkeurder verzonden als escalatie is ingeschakeld. De melding vraagt hen om actie te ondernemen als ze dat nog niet hebben gedaan. | Tweede keurgever |
| 15 | Vereiste actie: doorgestuurde aanvraag goedkeuren of weigeren op *[datum]* | Deze e-mail wordt verzonden naar fase-2 alternatieve fiatteurs, indien escalatie is ingeschakeld, om actie te ondernemen. | Alternatieve fiatteurs fase 2 |
| 16 | Verzoek goedgekeurd voor *[aanvrager]* aan *[access_package]* | Deze e-mail wordt verzonden naar de tweede keursman en fase-2 alternatieve fiatteurs bij goedkeuring van het verzoek. | Tweede keurgever, Stage-2 alternatieve fiatteurs |
| 17 | Een aanvraag is verlopen voor *[access_package]* | Deze e-mail wordt verzonden naar de tweede goedkeurder of alternatieve fiatteurs, nadat het verzoek is verlopen. | Tweede keurgever, fase-2 alternatieve fiatteurs |
| 18 | Je hebt nu toegang tot *[access_package]* | Deze e-mail wordt naar de eindgebruikers verzonden om hun toegang te gebruiken. | Requestor |
| 19 | Toegang voor *[access_package]* uitbreiden naar *[datum]* | Deze e-mail wordt naar de eindgebruikers verzonden voordat hun toegang verloopt. | Requestor |
| 20 | De toegang is beëindigd voor *[access_package]* | Deze e-mail wordt naar de eindgebruikers verzonden nadat hun toegang is verlopen. | Requestor |

### <a name="access-request-emails"></a>E-mails met toegangsverzoeken

Wanneer een aanvrager een toegangsaanvraag indient voor een toegangspakket dat is geconfigureerd om goedkeuring te vereisen, ontvangen alle fiatteurs die aan het beleid zijn toegevoegd, een e-mailmelding met details over het verzoek. De details in de e-mail zijn: requestor's name organization, and business justification; en de gevraagde begin- en einddatum van de toegang (indien aanwezig). De details zullen ook omvatten wanneer de aanvraag is ingediend en wanneer de aanvraag verloopt.

De e-mail bevat een link fiatteurs kunnen klikken om naar Mijn toegang te gaan om het toegangsverzoek goed te keuren of te weigeren. Hier is een voorbeeld e-mailmelding die wordt verzonden naar de eerste goedkeurder of tweede goedkeurder (als goedkeuring in twee fasen is ingeschakeld) om een toegangsaanvraag te voltooien:

![Verzoek om toegang tot pakkete-mail goed te keuren](./media/entitlement-management-shared/approver-request-email.png)

Goedkeurders kunnen ook een herinneringse-mail ontvangen. De e-mail vraagt de goedkeurder om een beslissing te nemen over het verzoek. Hier is een voorbeeld e-mail melding van de goedkeurder ontvangt om hen eraan te herinneren om actie te ondernemen:

![E-mail met aanvraag voor herinneringstoegang](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternatieve goedkeurders vragen e-mails aan

Als de alternatieve fiatteursinstelling is ingeschakeld en de aanvraag nog in behandeling is, wordt deze doorgestuurd. Alternatieve fiatteurs ontvangen een e-mail om het verzoek goed te keuren of te weigeren. U alternatieve fiatteurs inschakelen in fase 1 en fase-2. Hier is een voorbeeld e-mail van de melding van de alternatieve fiatteurs ontvangen:

![Alternatieve fiatteurs vragen e-mail aan](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Zowel de fiatteur als de alternatieve fiatteurs kunnen het verzoek goedkeuren of weigeren.

### <a name="approved-or-denied-emails"></a>Goedgekeurde of geweigerde e-mails

 Wanneer een fiatteur een toegangsverzoek ontvangt dat is ingediend door een aanvrager, kan hij of zij het toegangsverzoek goedkeuren of weigeren. De goedkeurder moet een zakelijke rechtvaardiging voor zijn beslissing toevoegen. Hier vindt u een voorbeelde-mail die naar de fiatteurs en alternatieve fiatteurs wordt verzonden nadat een verzoek is goedgekeurd:

![Goedgekeurd verzoek om toegang te krijgen tot e-mail met een pakket](./media/entitlement-management-process/approver-request-email-approved.png)

Wanneer een toegangsaanvraag wordt goedgekeurd en de toegang ervan is ingericht, wordt een e-mailmelding verzonden naar de aanvrager of dat ze nu toegang hebben tot het toegangspakket. Hier is een voorbeeld e-mailmelding die wordt verzonden naar een aanvrager wanneer ze toegang krijgen tot een toegangspakket:

![E-mail met goedgekeurde aanvrager-toegangsverzoek](./media/entitlement-management-process/requestor-email-approved.png)

Wanneer een toegangsverzoek wordt geweigerd, wordt een e-mailmelding naar de aanvrager verzonden. Hier is een voorbeeld e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangsverzoek wordt geweigerd:

![Verzoeker verzoek geweigerd e-mail](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>E-mails met goedkeuringsverzoeken in 2 fasen

Als goedkeuring in twee fasen is ingeschakeld, moeten ten minste twee fiatteurs het verzoek goedkeuren, één van elke fase, voordat de aanvrager toegang kan krijgen.

Tijdens fase-1 ontvangt de eerste goedkeurder de e-mail met het toegangsverzoek en neemt hij een beslissing. Als ze het verzoek goedkeuren, ontvangen alle eerste fiatteurs en alternatieve fiatteurs in fase-1 (als escalatie is ingeschakeld) een melding dat fase-1 is voltooid. Hier is een voorbeeld e-mail van de melding die wordt verzonden wanneer fase-1 is voltooid:

![E-mail met toegangsverzoeken in 2 fasen](./media/entitlement-management-process/approver-request-email-2stage.png)

Nadat de eerste of alternatieve fiatteurs de aanvraag in fase-1 hebben goedgekeurd, begint fase-2. Tijdens fase-2 ontvangt de tweede goedkeurder de e-mail met de e-mail met de toegangsaanvraag. Nadat de tweede fiatteurs of alternatieve fiatteurs in fase-2 (als escalatie is ingeschakeld) besluiten het verzoek goed te keuren of te weigeren, worden kennisgevingse-mails verzonden naar de eerste en tweede fiatteurs en alle alternatieve fiatteurs in fase-1 en fase-2, evenals de aanvrager.

### <a name="expired-access-request-emails"></a>Verlopen e-mails met toegangsverzoeken

Toegangsaanvragen kunnen verlopen als geen goedkeurder het verzoek heeft goedgekeurd of geweigerd. 

Wanneer de aanvraag de geconfigureerde vervaldatum heeft bereikt en verloopt, kan het niet langer worden goedgekeurd of geweigerd door de fiatteurs. Hier is een voorbeeld e-mail van de kennisgeving verzonden naar alle van de eerste, tweede (als 2-fase goedkeuring is ingeschakeld), en alternatieve fiatrovers:

![E-mail met verlopen toegangsverzoeken](./media/entitlement-management-process/approver-request-email-expired.png)

Er wordt ook een e-mailmelding naar de aanvrager verzonden, waarin wordt gemeld dat hun toegangsverzoek is verlopen en dat ze het toegangsverzoek opnieuw moeten indienen. Het volgende diagram toont de ervaring van de aanvrager en de e-mailmeldingen die ze ontvangen wanneer ze verzoeken om de toegang uit te breiden:

![Aanvrager se toegangsprocesstroom uitbreiden](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Hier is een voorbeeld e-mail melding die wordt verzonden naar een aanvrager wanneer hun toegangsverzoek is verlopen:

![Aanvrager verlopen toegangsverzoek e-mail](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot een toegangspakket aanvragen](entitlement-management-request-access.md)
- [Toegangsaanvragen goedkeuren of weigeren](entitlement-management-request-approve.md)
