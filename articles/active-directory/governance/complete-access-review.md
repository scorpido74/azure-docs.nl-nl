---
title: Een toegangsbeoordeling van groepen &-toepassingen voltooien - Azure AD
description: Meer informatie over het voltooien van een toegangscontrole van groepsleden of toepassingstoegang in Azure Active Directory-toegangsbeoordelingen.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932547"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Een toegangscontrole van groepen en toepassingen in Azure AD-toegangsbeoordelingen voltooien

Als beheerder maakt u [een toegangscontrole van groepen of toepassingen](create-access-review.md) en revisoren voeren de toegangscontrole [uit.](perform-access-review.md) In dit artikel wordt beschreven hoe u de resultaten van de toegangscontrole bekijken en de resultaten toepassen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2
- Globale beheerder, gebruikersbeheerder, beveiligingsbeheerder of beveiligingslezer

Zie [Licentievereisten voor](access-reviews-overview.md#license-requirements)meer informatie.

## <a name="view-an-access-review"></a>Een toegangscontrole weergeven

U de voortgang bijhouden terwijl de revisoren hun beoordelingen voltooien.

1. Meld u aan bij de Azure-portal en open de [pagina Identiteitsbeheer](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klik in het linkermenu op **Toegangsrecensies**.

1. Klik in de lijst op een toegangscontrole.

    Als u een reeks toegangsbeoordelingen wilt bekijken, navigeert u naar de toegangscontrole en vindt u aankomende gebeurtenissen in Geplande beoordelingen.

    Op de **overzichtspagina** u de voortgang zien. Er worden geen toegangsrechten gewijzigd in de map totdat de beoordeling is voltooid.

    ![Voortgang van access-beoordelingen](./media/complete-access-review/overview-progress.png)

1. Als u een toegangscontrole wilt stoppen voordat deze de geplande einddatum heeft bereikt, klikt u op de knop **Stoppen.**

    Wanneer revisoren een beoordeling stoppen, kunnen ze geen antwoorden meer geven. U een beoordeling niet opnieuw starten nadat deze is gestopt.

1. Als u niet langer geïnteresseerd bent in de toegangscontrole, u deze verwijderen door op **Verwijderen** te klikken.

## <a name="apply-the-changes"></a>De wijzigingen toepassen

Als **Automatisch toepassen van resultaten** op resource is ingeschakeld en op basis van uw selecties in Instellingen na **voltooiing,** wordt automatisch toepassen uitgevoerd na de einddatum van de beoordeling of wanneer u de controle handmatig stopt.

Als **De resultaten automatisch toepassen op resource** niet is ingeschakeld voor de controle, klikt u op **Toepassen** om de wijzigingen handmatig toe te passen. Als de toegang van een gebruiker in de beoordeling is geweigerd, verwijdert Azure AD bij **het**toepassen van de toegang van een gebruiker zijn lidmaatschap of toepassingstoewijzing.

![Wijzigingen in toegangscontrole toepassen](./media/complete-access-review/apply-changes.png)

De status van de beoordeling verandert van **Voltooid** via tussenliggende staten zoals **Toepassen** en ten slotte naar **status Resultaat toegepast**. U mag verwachten dat geweigerde gebruikers binnen enkele minuten worden verwijderd uit het groepslidmaatschap of de toewijzing van toepassingen.

Een geconfigureerde automatische toepassing sherziet er in of het selecteren **van Toepassen** heeft geen effect op een groep die afkomstig is uit een on-premises directory of een dynamische groep. Als u een groep wilt wijzigen die on-premises is ontstaan, downloadt u de resultaten en past u deze wijzigingen toe op de weergave van de groep in die map.

## <a name="retrieve-the-results"></a>De resultaten ophalen

Als u de resultaten voor een eenmalige toegangscontrole wilt bekijken, klikt u op de pagina **Resultaten.** Als u alleen de toegang van een gebruiker wilt weergeven, typt u in het vak Zoeken de weergavenaam of gebruikersnaam van een gebruiker wiens toegang is beoordeeld.

![Resultaten ophalen voor een toegangscontrole](./media/complete-access-review/retrieve-results.png)

Als u de voortgang wilt weergeven van een actieve toegangscontrole die wordt herhaald, klikt u op de pagina **Resultaten.**

Als u de resultaten wilt weergeven van een voltooide instantie van een terugkerende toegangscontrole, klikt u op **Geschiedenis controleren**en selecteert u de specifieke instantie in de lijst met voltooide toegangsrevisie-instanties, op basis van de begin- en einddatum van de instantie. De resultaten van deze instantie zijn te verkrijgen op de pagina **Resultaten.**

Als u alle resultaten van een toegangscontrole wilt ophalen, klikt u op de knop **Downloaden.** Het resulterende CSV-bestand kan worden weergegeven in Excel of in andere programma's die utf-8 gecodeerde CSV-bestanden openen.

## <a name="remove-users-from-an-access-review"></a>Gebruikers verwijderen uit een toegangscontrole

 Standaard geldt dat een verwijderde gebruiker gedurende dertig dagen in Azure AD aanwezig blijft met de status Verwijderd. In deze periode kan de gebruiker eventueel door een beheerder worden teruggezet.  Na dertig dagen wordt die gebruiker definitief verwijderd.  Bovendien kan een globale beheerder met behulp van de Azure Active Directory-portal [een onlangs verwijderde gebruiker](../fundamentals/active-directory-users-restore.md) expliciet permanent verwijderen voordat die periode is bereikt.  Als een gebruiker definitief is verwijderd, worden gegevens over die gebruiker vervolgens uit actieve toegangsbeoordelingen verwijderd.  Controle-informatie over verwijderde gebruikers blijft in het auditlogboek aanwezig.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikerstoegang beheren met Azure AD-toegangsbeoordelingen](manage-user-access-with-access-reviews.md)
- [Gasttoegang beheren met Azure AD-toegangsbeoordelingen](manage-guest-access-with-access-reviews.md)
- [Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
- [Een toegangsbeoordeling maken van gebruikers met een Azure AD-beheerderrol](../privileged-identity-management/pim-how-to-start-security-review.md)
