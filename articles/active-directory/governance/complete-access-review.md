---
title: Een toegangs beoordeling van groepen & toepassingen volt ooien-Azure AD
description: Meer informatie over het volt ooien van een toegangs beoordeling van groeps leden of toegang tot toepassingen in Azure Active Directory toegangs Beoordelingen.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23266043d5efd65a4e6e79dd515a54a2c321b077
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798831"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Een toegangs beoordeling van groepen en toepassingen in azure AD-toegangs beoordelingen volt ooien

Als beheerder [maakt u een toegangs beoordeling van groepen of toepassingen](create-access-review.md) en revisoren die [de toegangs beoordeling uitvoeren](perform-access-review.md). In dit artikel wordt beschreven hoe u de resultaten van de toegangs beoordeling kunt bekijken en de resultaten kunt Toep assen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2
- Globale beheerder, gebruikers beheerder, beveiligings beheerder of beveiligings lezer

Zie [Licentievereisten](access-reviews-overview.md#license-requirements) voor meer informatie.

## <a name="view-an-access-review"></a>Een toegangs beoordeling weer geven

U kunt de voortgang volgen wanneer de revisoren hun beoordelingen hebben voltooid.

1. Meld u aan bij de Azure Portal en open de [pagina voor identiteits](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)beheer.

1. Klik in het menu links op **toegangs beoordelingen**.

1. Klik in de lijst op een toegangs beoordeling.

    Als u een reeks toegangs beoordelingen wilt weer geven, gaat u naar de toegangs beoordeling en vindt u in geplande beoordelingen een aantal toekomstige instanties.

    Op de pagina **overzicht** ziet u de voortgang. Er worden geen toegangs rechten gewijzigd in de map totdat de controle is voltooid.

    ![Voortgang van toegangs beoordeling](./media/complete-access-review/overview-progress.png)

1. Als u een toegangs beoordeling wilt stoppen voordat de geplande eind datum is bereikt, klikt u op de knop **stoppen** .

    Als u een beoordeling stopt, kunnen revisoren geen reacties meer geven. U kunt een controle niet opnieuw starten nadat deze is gestopt.

1. Als u niet langer geïnteresseerd bent in de toegangs beoordeling, kunt u deze verwijderen door te klikken op de knop **verwijderen** .

## <a name="apply-the-changes"></a>De wijzigingen Toep assen

Als **automatisch Toep assen van resultaten op resource** is ingeschakeld en op basis van uw selecties tijdens de **voltooiings instellingen**, wordt automatisch Toep assen na de eind datum van de beoordeling of wanneer u de controle hand matig stopt.

Als **automatisch Toep assen van resultaten op resource** niet is ingeschakeld voor de beoordeling, klikt u op **Toep assen** om de wijzigingen hand matig toe te passen. Als de toegang van een gebruiker bij de controle is geweigerd, worden de lidmaatschaps-of toepassings toewijzing van Azure AD verwijderd als u op **Toep assen**klikt.

![Wijzigingen in toegangs beoordeling Toep assen](./media/complete-access-review/apply-changes.png)

De status van de beoordeling wordt gewijzigd van **voltooid** met behulp van tussenliggende statussen, zoals **Toep assen** en tot slot op status **toegepast resultaat**. U wordt gewend om geweigerde gebruikers te zien, indien aanwezig, die in een paar minuten worden verwijderd uit het groepslid maatschap of de toepassings toewijzing.

Een geconfigureerde automatische beoordeling of het selecteren van **Apply** heeft geen invloed op een groep die afkomstig is uit een on-premises Directory of een dynamische groep. Als u een groep wilt wijzigen die on-premises is, downloadt u de resultaten en past u deze wijzigingen toe op de weer gave van de groep in die map.

## <a name="retrieve-the-results"></a>De resultaten ophalen

Als u de resultaten voor een eenmalige toegangs beoordeling wilt weer geven, klikt u op de pagina **resultaten** . Als u alleen de toegang van een gebruiker wilt weer geven, typt u in het zoekvak de weergave naam of user principal name van een gebruiker van wie de toegang is gecontroleerd.

![Resultaten ophalen voor een toegangs beoordeling](./media/complete-access-review/retrieve-results.png)

Als u de voortgang van een terugkerende actieve toegangs beoordeling wilt weer geven, klikt u op de pagina **resultaten** .

Als u de resultaten wilt weer geven van een voltooid exemplaar van een terugkerende toegangs beoordeling, klikt u op **controle geschiedenis**en selecteert u vervolgens de specifieke instantie in de lijst met voltooide instanties voor toegangs controle, op basis van de begin-en eind datum van het exemplaar. U kunt de resultaten van deze instantie verkrijgen op de pagina met **resultaten** .

Als u alle resultaten van een toegangs beoordeling wilt ophalen, klikt u op de knop **downloaden** . Het resulterende CSV-bestand kan worden weer gegeven in Excel of in andere Program ma's waarmee CSV-bestanden met UTF-8-code ring worden geopend.

## <a name="remove-users-from-an-access-review"></a>Gebruikers verwijderen uit een toegangs beoordeling

 Standaard geldt dat een verwijderde gebruiker gedurende dertig dagen in Azure AD aanwezig blijft met de status Verwijderd. In deze periode kan de gebruiker eventueel door een beheerder worden teruggezet.  Na dertig dagen wordt die gebruiker definitief verwijderd.  Daarnaast kan een globale beheerder met behulp van de Azure Active Directory-Portal expliciet [een onlangs verwijderde gebruiker permanent verwijderen](../fundamentals/active-directory-users-restore.md) voordat die periode wordt bereikt.  Als een gebruiker definitief is verwijderd, worden gegevens over die gebruiker vervolgens uit actieve toegangsbeoordelingen verwijderd.  Controle-informatie over verwijderde gebruikers blijft in het auditlogboek aanwezig.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikerstoegang beheren met Azure AD-toegangsbeoordelingen](manage-user-access-with-access-reviews.md)
- [Gasttoegang beheren met Azure AD-toegangsbeoordelingen](manage-guest-access-with-access-reviews.md)
- [Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
- [Een toegangsbeoordeling maken van gebruikers met een Azure AD-beheerderrol](../privileged-identity-management/pim-how-to-start-security-review.md)
