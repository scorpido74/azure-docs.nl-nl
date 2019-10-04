---
title: Toegang tot groepen of toepassingen in toegangs beoordelingen bekijken-Azure Active Directory | Microsoft Docs
description: Meer informatie over het controleren van uw eigen toegang tot groepen of toepassingen in Azure Active Directory toegangs Beoordelingen.
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
ms.openlocfilehash: df8b03d513e2d78083031fbc9a3f6dbc1b7d15d9
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958569"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Toegang tot groepen of toepassingen in azure AD-toegangs beoordelingen bekijken

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop bedrijven toegang beheren tot groepen of toepassingen in azure AD en andere online services van micro soft met een functie die Azure AD-toegangs beoordelingen heet.

In dit artikel wordt beschreven hoe u uw eigen toegang tot een groep of een toepassing kunt controleren.

## <a name="open-the-access-review"></a>Open de toegangs beoordeling

De eerste stap voor het uitvoeren van een toegangs beoordeling is het zoeken en openen van de toegangs beoordeling.

1. Zoek naar een e-mail bericht van micro soft waarin u wordt gevraagd de toegang te controleren. Hier volgt een voor beeld van een e-mail bericht om uw toegang tot een groep te controleren.

    ![Voor beeld van een e-mail bericht van micro soft om de toegang tot een groep te controleren](./media/review-your-access/access-review-email.png)

1. Klik op de koppeling **toegang controleren** om de toegangs beoordeling te openen.

Als u de e-mail niet hebt, kunt u uw openstaande toegangs beoordelingen vinden door de volgende stappen uit te voeren.

1. Meld u aan bij de MyApps-Portal op [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![MyApps-Portal met een lijst met machtigingen voor](./media/review-your-access/myapps-access-panel.png)

1. Klik in de rechterbovenhoek van de pagina op het gebruikerssymbool, waar uw naam en standaardorganisatie worden weergegeven. Als er meerdere organisaties vermeld staan, selecteert u de organisatie waarvoor een toegangsbeoordeling is aangevraagd.

1. Klik aan de rechter kant van de pagina op de tegel **toegangs beoordelingen** om een lijst met de openstaande toegangs beoordelingen te bekijken.

    Als de tegel niet zichtbaar is, zijn er geen toegangsbeoordelingen die voor die organisatie moeten worden uitgevoerd en hoeft er momenteel geen actie te worden ondernomen.

    ![Lijst met openstaande toegangs Beoordelingen voor uw apps en groepen](./media/review-your-access/access-reviews-list.png)

1. Klik op de koppeling **controle starten** voor de toegangs beoordeling die u wilt uitvoeren.

## <a name="perform-the-access-review"></a>De toegangs beoordeling uitvoeren

Zodra u de toegangs beoordeling hebt geopend, kunt u uw toegang zien.

1. Controleer uw toegang en beslis of u nog steeds toegang nodig hebt.

    Als de aanvraag de toegang voor anderen wil controleren, ziet de pagina er anders uit. Zie [toegang tot groepen of toepassingen controleren](perform-access-review.md)voor meer informatie.

    ![Open toegangs beoordeling waarin u wordt gevraagd of u nog steeds toegang tot een groep nodig hebt](./media/review-your-access/perform-access-review.png)

1. Klik op **Ja** om uw toegang te beperken of op **Nee** om uw toegang te verwijderen.

1. Als u op **Ja**klikt, moet u mogelijk een motivering opgeven in het vak **reden** .

    ![Voltooide toegangs beoordeling waarin u wordt gevraagd of u nog steeds toegang tot een groep nodig hebt](./media/review-your-access/perform-access-review-submit.png)

1. Klik op **Indienen**.

    Uw selectie wordt verzonden en u keert terug naar de MyApps-Portal.

    Als u uw antwoord wilt wijzigen, opent u de pagina toegangs beoordelingen opnieuw en werkt u uw antwoord bij. U kunt uw antwoord op elk gewenst moment wijzigen totdat de toegangs beoordeling is beëindigd.

    > [!NOTE]
    > Als u hebt aangegeven dat u geen toegang meer nodig hebt, wordt u niet onmiddellijk verwijderd. U wordt verwijderd wanneer de controle is beëindigd of wanneer een beheerder de controle stopt.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)
