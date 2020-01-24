---
title: De toegang tot apps in uw organisatie beheren & groepen-Azure AD
description: Meer informatie over het uitvoeren van een toegangs beoordeling voor het beheren van de toegang tot de apps en groepen van uw organisatie vanuit de portal mijn apps.
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
ms.openlocfilehash: e739024686bdac497b9b7dd450c5ed46e3cf9a63
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705017"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Een toegangs beoordeling uitvoeren vanuit de portal mijn apps

U kunt uw werk-of school account gebruiken met de portal van **mijn apps** op het web, om een aantal Cloud-apps in uw organisatie weer te geven en te starten, om een aantal van uw profiel-en account gegevens bij te werken, om uw **groeps** gegevens te bekijken en **toegangs beoordelingen** uit te voeren voor uw apps en groepen. Als u geen toegang hebt tot de portal **mijn apps** , moet u contact opnemen met de Help Desk voor toestemming.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Deze inhoud is bedoeld voor gebruikers. Als u een beheerder bent, kunt u meer informatie vinden over het instellen en beheren van uw Cloud-apps in de documentatie van [toepassings beheer](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Toegangs beoordelingen beheren

Als uw beheerder u toestemming heeft gegeven om uw eigen toegangs beoordelingen uit te voeren, kunt u uw groepen of apps toegang beheren via de tegel **toegangs beoordelingen** op de portal-pagina **mijn apps** .

>[!Note]
>Als u de tegel **toegangs beoordelingen** niet ziet, betekent dit dat u niet gemachtigd bent om toegangs beoordelingen uit te voeren, of dat er nog geen beoordelingen zijn die wachten op goed keuring. Als u denkt dat u toegang moet hebben tot de tegel, neemt u contact op met de Help Desk voor ondersteuning.

### <a name="to-perform-your-access-reviews"></a>Uw toegangs beoordelingen uitvoeren

1. Meld u aan bij uw werk-of school account.

2. Open uw webbrowser en ga naar https://myapps.microsoft.com of gebruik de koppeling die is opgenomen in uw organisatie. U kunt bijvoorbeeld worden omgeleid naar een aangepaste pagina voor uw organisatie, zoals https://myapps.microsoft.com/contoso.com.

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
