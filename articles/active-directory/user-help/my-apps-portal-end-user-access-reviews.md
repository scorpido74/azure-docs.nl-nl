---
title: Een toegangs beoordeling uitvoeren vanuit de portal mijn apps-Azure AD
description: Meer informatie over het weer geven en beheren van de beveiligings toegang voor de apps en groepen van uw organisatie.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf803027aac7c62ea500d1af104ae3234fadf8b5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73804046"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Een toegangs beoordeling uitvoeren vanuit de portal mijn apps

U kunt uw werk-of school account gebruiken met de portal van **mijn apps** op het web, om een aantal Cloud-apps in uw organisatie weer te geven en te starten, om een aantal van uw profiel-en account gegevens bij te werken, om uw **groeps** gegevens te bekijken en uit te voeren  **toegangs beoordelingen** voor uw apps en groepen. Als u geen toegang hebt tot de portal **mijn apps** , moet u contact opnemen met de Help Desk voor toestemming.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Deze inhoud is bedoeld voor gebruikers. Als u een beheerder bent, kunt u meer informatie vinden over het instellen en beheren van uw Cloud-apps in de documentatie van [toepassings beheer](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Toegangs beoordelingen beheren

Als uw beheerder u toestemming heeft gegeven om uw eigen toegangs beoordelingen uit te voeren, kunt u uw groepen of apps toegang beheren via de tegel **toegangs beoordelingen** op de portal-pagina **mijn apps** .

>[!Note]
>Als u de tegel **toegangs beoordelingen** niet ziet, betekent dit dat u niet gemachtigd bent om toegangs beoordelingen uit te voeren, of dat er nog geen beoordelingen zijn die wachten op goed keuring. Als u denkt dat u toegang moet hebben tot de tegel, neemt u contact op met de Help Desk voor ondersteuning.

### <a name="to-perform-your-access-reviews"></a>Uw toegangs beoordelingen uitvoeren

1. Meld u aan bij uw werk-of school account.

2. Open uw webbrowser en ga naar https://myapps.microsoft.comof gebruik de koppeling die is opgenomen in uw organisatie. U kunt bijvoorbeeld worden omgeleid naar een aangepaste pagina voor uw organisatie, zoals https://myapps.microsoft.com/contoso.com.

    De pagina **apps** wordt weer gegeven, met alle Cloud-apps die eigendom zijn van uw organisatie en die u kunt gebruiken.

    ![Pagina apps in de portal mijn apps](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Selecteer de tegel **toegangs beoordelingen** om een lijst weer te geven met toegangs beoordelingen die wachten op uw goed keuring.

    ![Pagina toegangs beoordelingen met openstaande toegangs Beoordelingen voor de organisatie](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selecteer **begin beoordeling** om uw toegangs beoordeling te starten.

5. Controleer uw toegang en bepaal of het nog steeds nodig is.

    ![Pagina toegangs controle, met de details van de beoordeling](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Als u een beheerder bent en toestemming wilt geven om de toegang van uw organisatie tot groepen en apps te controleren, ziet u een andere pagina. Zie [toegang tot groepen of toepassingen in azure AD-toegangs beoordelingen controleren](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)voor meer informatie over het controleren van groepen of apps voor uw organisatie.

6. Selecteer **Ja** om uw toegang te beperken of **Nee** om uw toegang te verwijderen.

    Als u **Ja**selecteert, moet u mogelijk een motivering opgeven in het vak **reden** .

    ![Pagina toegangs controle, met daarin het vak reden met voorbeeld tekst](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selecteer **Indienen**.

    De toegangs beoordeling is voltooid en u keert terug naar de portal **mijn apps** .

    >[!Note]
    >U kunt uw toegang op elk gewenst moment wijzigen tot de toegangs beoordelings periode afloopt. Als u de toegang tot een app of groep verwijdert, wordt deze niet onmiddellijk verwijderd. Het verwijderen treedt op wanneer de toegangs beoordelings periode eindigt of wanneer een beheerder de beoordeling sluit.

## <a name="next-steps"></a>Volgende stappen

- [Apps openen en gebruiken in de portal voor mijn apps](my-apps-portal-end-user-access.md).

- [Wijzig de profiel gegevens](my-apps-portal-end-user-update-profile.md).

- [Gegevens over groepen weer geven en bijwerken](my-apps-portal-end-user-groups.md).
