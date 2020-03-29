---
title: Uw toegang tot groepen &-apps controleren in toegangsbeoordelingen - Azure AD
description: Meer informatie over het controleren van uw eigen toegang tot groepen of toepassingen in Azure Active Directory-toegangsbeoordelingen.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422408"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Toegang voor uzelf controleren tot groepen of toepassingen in Azure AD-toegangsbeoordelingen

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop ondernemingen de toegang tot groepen of toepassingen in Azure AD en andere Microsoft Online Services beheren met een functie genaamd Azure AD-toegangsbeoordelingen.

In dit artikel wordt beschreven hoe u uw eigen toegang tot een groep of een toepassing bekijken.

## <a name="open-the-access-review"></a>De toegangscontrole openen

De eerste stap om een toegangscontrole uit te voeren, is het vinden en openen van de toegangscontrole.

1. Zoek naar een e-mail van Microsoft waarin u wordt gevraagd de toegang te bekijken. Hier is een voorbeeld e-mail om uw toegang tot een groep te controleren.

    ![Voorbeeld e-mail van Microsoft om uw toegang tot een groep te bekijken](./media/review-your-access/access-review-email.png)

1. Klik **op** de koppeling Toegangs controleren om de toegangscontrole te openen.

Als u de e-mail niet hebt, u uw in behandeling zijnde toegangsbeoordelingen vinden door deze stappen te volgen.

1. Meld u aan bij [https://myapps.microsoft.com](https://myapps.microsoft.com)de MyApps-portal op .

    ![MyApps-portalmetapps waartoe u machtigingen hebt](./media/review-your-access/myapps-access-panel.png)

1. Klik in de rechterbovenhoek van de pagina op het gebruikerssymbool, waar uw naam en standaardorganisatie worden weergegeven. Als er meerdere organisaties vermeld staan, selecteert u de organisatie waarvoor een toegangsbeoordeling is aangevraagd.

1. Klik aan de rechterkant van de pagina op de tegel **Beoordelingen openen** om een lijst met de in behandeling zijnde toegangsbeoordelingen weer te geven.

    Als de tegel niet zichtbaar is, zijn er geen toegangsbeoordelingen die voor die organisatie moeten worden uitgevoerd en hoeft er momenteel geen actie te worden ondernomen.

    ![Lijst met toegangsbeoordelingen in behandeling voor uw apps en groepen](./media/review-your-access/access-reviews-list.png)

1. Klik **op** de koppeling Beoordeling starten voor de toegangsbeoordeling die u wilt uitvoeren.

## <a name="perform-the-access-review"></a>De toegangscontrole uitvoeren

Zodra u de toegangscontrole hebt geopend, u uw toegang zien.

1. Bekijk uw toegang en bepaal of u nog toegang nodig hebt.

    Als het verzoek is om de toegang voor anderen te controleren, ziet de pagina er anders uit. Zie [Toegang tot groepen of toepassingen controleren voor](perform-access-review.md)meer informatie .

    ![Open access review met de vraag of je nog steeds toegang tot een groep nodig hebt](./media/review-your-access/perform-access-review.png)

1. Klik **op Ja** om uw toegang te behouden of klik op **Nee** om uw toegang te verwijderen.

1. Als u op **Ja**klikt, moet u mogelijk een motivering opgeven in het vak **Reden.**

    ![Voltooide toegangscontrole met de vraag of u nog steeds toegang tot een groep nodig hebt](./media/review-your-access/perform-access-review-submit.png)

1. Klik **op Verzenden**.

    Uw selectie wordt ingediend en u bent teruggekeerd naar de MyApps-portal.

    Als u uw reactie wilt wijzigen, opent u de pagina met toegangsbeoordelingen opnieuw en werkt u uw reactie bij. U uw reactie op elk gewenst moment wijzigen totdat de toegangscontrole is beëindigd.

    > [!NOTE]
    > Als u hebt aangegeven dat u geen toegang meer nodig hebt, wordt u niet onmiddellijk verwijderd. U wordt verwijderd wanneer de beoordeling is beëindigd of wanneer een beheerder de beoordeling stopt.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
