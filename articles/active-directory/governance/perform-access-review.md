---
title: Toegang tot groepen & toepassingen in toegangs beoordelingen bekijken-Azure AD
description: Meer informatie over het controleren van toegang tot groeps leden of toegang tot toepassingen in Azure Active Directory toegangs Beoordelingen.
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
ms.openlocfilehash: 8393a1e4719137aa09233d0a87dabdf81d8ccb59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422456"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Toegang tot groepen en toepassingen in azure AD-toegangs beoordelingen controleren

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop bedrijven de toegang tot groepen en toepassingen in azure AD en andere online services van micro soft beheren met een functie die Azure AD-toegangs beoordelingen heet.

In dit artikel wordt beschreven hoe een aangewezen revisor een toegangs beoordeling uitvoert voor leden van een groep of gebruikers met toegang tot een toepassing.

## <a name="open-the-access-review"></a>Open de toegangs beoordeling

De eerste stap voor het uitvoeren van een toegangs beoordeling is het zoeken en openen van de toegangs beoordeling.

1. Zoek naar een e-mail bericht van micro soft waarin u wordt gevraagd de toegang te controleren. Hier volgt een voor beeld van een e-mail bericht om de toegang tot een groep te controleren.

    ![Voor beeld van een e-mail bericht van micro soft om de toegang tot een groep te controleren](./media/perform-access-review/access-review-email.png)

1. Klik op de koppeling **controle starten** om de toegangs beoordeling te openen.

Als u de e-mail niet hebt, kunt u uw openstaande toegangs beoordelingen vinden door de volgende stappen uit te voeren.

1. Meld u aan bij de MyApps-Portal op [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![MyApps-Portal met een lijst met machtigingen voor](./media/perform-access-review/myapps-access-panel.png)

1. Klik in de rechterbovenhoek van de pagina op het gebruikerssymbool, waar uw naam en standaardorganisatie worden weergegeven. Als er meerdere organisaties vermeld staan, selecteert u de organisatie waarvoor een toegangsbeoordeling is aangevraagd.

1. Klik op de tegel **toegangs beoordelingen** om een lijst met de openstaande toegangs beoordelingen weer te geven.

    Als de tegel niet zichtbaar is, zijn er geen toegangsbeoordelingen die voor die organisatie moeten worden uitgevoerd en hoeft er momenteel geen actie te worden ondernomen.

    ![Lijst met openstaande toegangs Beoordelingen voor apps en groepen](./media/perform-access-review/access-reviews-list.png)

1. Klik op de koppeling **controle starten** voor de toegangs beoordeling die u wilt uitvoeren.

## <a name="perform-the-access-review"></a>De toegangs beoordeling uitvoeren

Zodra u de toegangs beoordeling hebt geopend, ziet u de namen van de gebruikers die moeten worden gecontroleerd.

Als de aanvraag uw eigen toegang moet controleren, ziet de pagina er anders uit. Zie voor meer informatie [toegang tot groepen of toepassingen controleren](review-your-access.md).

![Open Access-beoordeling met een overzicht van de gebruikers die moeten worden beoordeeld](./media/perform-access-review/perform-access-review.png)

Er zijn twee manieren waarop u toegang kunt goed keuren of weigeren:

- U kunt de toegang voor een of meer gebruikers goed keuren of weigeren, of
- U kunt de aanbevelingen van het systeem accepteren. Dit is de eenvoudigste en snelste manier.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Toegang voor een of meer gebruikers goed keuren of weigeren

1. Bekijk de lijst met gebruikers om te bepalen of hun voortdurende toegang moet worden goedgekeurd of geweigerd.

1. Als u de toegang voor één gebruiker wilt goed keuren of weigeren, klikt u op de rij om een venster te openen om de te ondernemen actie op te geven. Als u de toegang voor meerdere gebruikers wilt goed keuren of weigeren, voegt u de selectie vakjes naast de gebruikers toe en klikt u op de knop **X-gebruiker (s) controleren** om een venster te openen waarin u de te ondernemen actie kunt opgeven.

1. Klik op **goed keuren** of **weigeren**. Als u niet zeker weet, kunt u klikken op **niet bekend**. Als u dit doet, wordt de gebruiker toegang tot de toegangs beheer, maar de selectie wordt weer gegeven in de audit Logboeken.

    ![Actie venster met opties goed keuren, weigeren en niet bekend](./media/perform-access-review/approve-deny.png)

1. Voer, indien nodig, een reden in het vak **reden** in.

    De beheerder van de toegangs beoordeling vereist mogelijk dat u een reden opgeeft voor het goed keuren van voortdurende toegang of groepslid maatschap.

1. Wanneer u de actie hebt opgegeven die u wilt uitvoeren, klikt u op **Opslaan**.

    Als u uw antwoord wilt wijzigen, selecteert u de rij en werkt u het antwoord bij. U kunt bijvoorbeeld een eerder geweigerde gebruiker goed keuren of een eerder goedgekeurde gebruiker weigeren. U kunt uw antwoord op elk gewenst moment wijzigen totdat de toegangs beoordeling is beëindigd.

    Als er meerdere revisoren zijn, wordt de laatste verzonden reactie vastgelegd. Bekijk een voor beeld waarin een beheerder twee revisoren (Alice en Bob) toewijst. Anja opent eerst de toegangs beoordeling en keurt de toegang goed. Voordat de controle eindigt, wordt de toegangs beoordeling geopend en wordt de toegang geweigerd. De laatste weigerings reactie is wat wordt vastgelegd.

    > [!NOTE]
    > Als een gebruiker de toegang wordt geweigerd, worden ze niet onmiddellijk verwijderd. Ze worden verwijderd wanneer de controle is beëindigd of wanneer een beheerder de controle stopt.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Toegang goed keuren of weigeren op basis van aanbevelingen

Om toegangs beoordelingen gemakkelijker en sneller te laten verlopen, bieden we ook aanbevelingen die u met één klik kunt accepteren. De aanbevelingen worden gegenereerd op basis van de aanmeldings activiteit van de gebruiker.

1. Klik in de blauwe balk onder aan de pagina op **aanbevelingen accepteren**.

    ![Open de toegangs beoordelings vermelding met de knop aanbevelingen accepteren](./media/perform-access-review/accept-recommendations.png)

    Er wordt een samen vatting van de aanbevolen acties weer gegeven.

    ![Venster waarin een samen vatting van de aanbevolen acties wordt weer gegeven](./media/perform-access-review/accept-recommendations-summary.png)

1. Klik op **OK** om de aanbevelingen te accepteren.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)
