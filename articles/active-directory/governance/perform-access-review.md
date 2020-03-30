---
title: Toegang tot groepen & toepassingen in toegangsbeoordelingen controleren - Azure AD
description: Meer informatie over het controleren van de toegang tot groepsleden of toepassingstoegang in Azure Active Directory-toegangsbeoordelingen.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128455"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Toegang tot groepen en toepassingen in Azure AD-toegangsbeoordelingen controleren

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop ondernemingen de toegang tot groepen en toepassingen in Azure AD en andere Microsoft Online Services beheren met een functie genaamd Azure AD-toegangsbeoordelingen.

In dit artikel wordt beschreven hoe een aangewezen revisor een toegangscontrole uitvoert voor leden van een groep of gebruikers die toegang hebben tot een toepassing.

## <a name="open-the-access-review"></a>De toegangscontrole openen

De eerste stap om een toegangscontrole uit te voeren, is het vinden en openen van de toegangscontrole.

1. Zoek naar een e-mail van Microsoft waarin u wordt gevraagd de toegang te bekijken. Hier is een voorbeeld e-mail om de toegang voor een groep te bekijken.

    ![Voorbeeld e-mail van Microsoft om de toegang tot een groep te controleren](./media/perform-access-review/access-review-email.png)

1. Klik **op** de koppeling Controleren starten om de toegangscontrole te openen.

Als u de e-mail niet hebt, u uw in behandeling zijnde toegangsbeoordelingen vinden door deze stappen te volgen.

1. Meld u aan bij [https://myapps.microsoft.com](https://myapps.microsoft.com)de MyApps-portal op .

    ![MyApps-portalmetapps waartoe u machtigingen hebt](./media/perform-access-review/myapps-access-panel.png)

1. Klik in de rechterbovenhoek van de pagina op het gebruikerssymbool, waar uw naam en standaardorganisatie worden weergegeven. Als er meerdere organisaties vermeld staan, selecteert u de organisatie waarvoor een toegangsbeoordeling is aangevraagd.

1. Klik op de tegel **Beoordelingen van Access** om een lijst met de beoordelingen in behandeling te bekijken.

    Als de tegel niet zichtbaar is, zijn er geen toegangsbeoordelingen die voor die organisatie moeten worden uitgevoerd en hoeft er momenteel geen actie te worden ondernomen.

    ![Lijst met toegangsbeoordelingen in behandeling voor apps en groepen](./media/perform-access-review/access-reviews-list.png)

1. Klik **op** de koppeling Beoordeling starten voor de toegangsbeoordeling die u wilt uitvoeren.

## <a name="perform-the-access-review"></a>De toegangscontrole uitvoeren

Zodra u de toegangscontrole hebt geopend, ziet u de namen van gebruikers die moeten worden beoordeeld.

Als het verzoek is om uw eigen toegang te bekijken, ziet de pagina er anders uit. Zie [Toegang voor uzelf tot groepen of toepassingen voor](review-your-access.md)meer informatie bekijken.

![Open access review met de gebruikers die moeten worden beoordeeld](./media/perform-access-review/perform-access-review.png)

Er zijn twee manieren waarop u de toegang goedkeuren of weigeren:

- U de toegang voor een of meer gebruikers goedkeuren of weigeren, of
- U de systeemaanbevelingen accepteren, wat de gemakkelijkste en snelste manier is.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Toegang voor een of meer gebruikers goedkeuren of weigeren

1. Bekijk de lijst met gebruikers om te beslissen of ze hun voortdurende toegang moeten goedkeuren of weigeren.

1. Als u de toegang voor één gebruiker wilt goedkeuren of weigeren, klikt u op de rij om een venster te openen om de actie op te geven die u moet uitvoeren. Als u de toegang voor meerdere gebruikers wilt goedkeuren of weigeren, voegt u vinkjes naast de gebruikers toe en klikt u vervolgens op de knop **X-gebruiker(s) controleren** om een venster te openen om de actie op te geven die u moet uitvoeren.

1. Klik **op Goedkeuren** of **Weigeren**. Als u het niet zeker weet, u op **Weet niet weten**. Als u dit doet, behoudt de gebruiker zijn toegang, maar wordt de selectie weergegeven in de controlelogboeken.

    ![Actievenster met opties Goedkeuren, Weigeren en Weten](./media/perform-access-review/approve-deny.png)

1. Voer indien nodig een reden in het vak **Reden** in.

    De beheerder van de toegangscontrole kan vereisen dat u een reden voor het goedkeuren van voortgezette toegang of groepslidmaatschap verstrekt.

1. Nadat u de actie hebt opgegeven die u wilt uitvoeren, klikt u op **Opslaan**.

    Als u uw reactie wilt wijzigen, selecteert u de rij en werkt u het antwoord bij. U bijvoorbeeld een eerder geweigerde gebruiker goedkeuren of een eerder goedgekeurde gebruiker weigeren. U uw reactie op elk gewenst moment wijzigen totdat de toegangscontrole is beëindigd.

    Als er meerdere revisoren zijn, wordt het laatst ingediende antwoord geregistreerd. Houd rekening met een voorbeeld waarin een beheerder twee revisoren aanwijst: Alice en Bob. Alice opent de toegangsbeoordeling als eerste en keurt de toegang goed. Voordat de beoordeling afloopt, opent Bob de toegangsbeoordeling en weigert hij de toegang. De laatste ontkennen reactie is wat wordt opgenomen.

    > [!NOTE]
    > Als een gebruiker de toegang wordt geweigerd, worden deze niet onmiddellijk verwijderd. Ze worden verwijderd wanneer de beoordeling is beëindigd of wanneer een beheerder de beoordeling stopt.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Toegang goedkeuren of weigeren op basis van aanbevelingen

Om toegangsbeoordelingen voor u eenvoudiger en sneller te maken, geven we ook aanbevelingen die u met één klik accepteren. De aanbevelingen worden gegenereerd op basis van de aanmeldingsactiviteit van de gebruiker.

1. Klik in de blauwe balk onder aan de pagina op **Aanbevelingen accepteren**.

    ![Aanbieding voor open access-controle met de knop Aanbevelingen accepteren](./media/perform-access-review/accept-recommendations.png)

    U ziet een overzicht van de aanbevolen acties.

    ![Venster met een overzicht van de aanbevolen acties](./media/perform-access-review/accept-recommendations-summary.png)

1. Klik **op Ok** om de aanbevelingen te accepteren.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
