---
title: Toegang tot een toegangspakket controleren in Azure AD-rechtenbeheer
description: Meer informatie over het voltooien van een toegangscontrole van toegangsbeheerpakketten voor rechten in Azure Active Directory-toegangsbeoordelingen (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968745"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Toegang tot een toegangspakket controleren in Azure AD-rechtenbeheer

Azure AD-rechtenbeheer vereenvoudigt de manier waarop ondernemingen de toegang tot groepen, toepassingen en SharePoint-sites beheren. In dit artikel wordt beschreven hoe u toegangsbeoordelingen uitvoert voor andere gebruikers die als aangewezen revisor aan een toegangspakket zijn toegewezen.

## <a name="prerequisites"></a>Vereisten

Als u de actieve toegangspakkettoewijzingen van gebruikers wilt bekijken, moet u voldoen aan de vereisten voor het uitvoeren van een toegangscontrole:
- Azure AD Premium P2
- Globale beheerder
- Aangewezen gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

Zie [Licentievereisten voor](entitlement-management-overview.md#license-requirements)meer informatie.


## <a name="open-the-access-review"></a>De toegangscontrole openen

Gebruik de volgende stappen om de toegangscontrole te zoeken en te openen:

1. U ontvangt mogelijk een e-mail van Microsoft waarin u wordt gevraagd de toegang te bekijken. Zoek de e-mail om de toegangscontrole te openen. Hier is een voorbeeld e-mail om toegang te bekijken:
    
    ![E-mail van de controleur van de toegang](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Klik op de koppeling **Gebruikerstoegang controleren** om de toegangscontrole te openen. 

1. Als u de e-mail niet hebt, u uw https://myaccess.microsoft.comopenstaande toegangsbeoordelingen vinden door rechtstreeks naar.  (Voor de Amerikaanse `https://myaccess.microsoft.us` regering, gebruik in plaats daarvan.)

1. Klik op **Toegangsbeoordelingen** op de linkernavigatiebalk om een lijst met in behandeling zijnde toegangsbeoordelingen te bekijken die aan u zijn toegewezen.
    
    ![Toegangsrecensies selecteren op Mijn toegang](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Klik op de beoordeling waarmee u wilt beginnen.
    
    ![De toegangscontrole selecteren](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>De toegangscontrole uitvoeren

Zodra u de toegangscontrole opent, ziet u de namen van gebruikers waarvoor u moet controleren. Er zijn twee manieren waarop u de toegang goedkeuren of weigeren:
- U de toegang voor een of meer gebruikers handmatig goedkeuren of weigeren
- U de systeemaanbevelingen accepteren

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Toegang voor een of meer gebruikers handmatig goedkeuren of weigeren
1. Bekijk de lijst met gebruikers en bepaal welke gebruikers toegang moeten blijven hebben.

    ![Lijst met gebruikers die u wilt bekijken](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Als u de toegang wilt goedkeuren of weigeren, selecteert u de keuzerondjelinks van de naam van de gebruiker.

1. Selecteer **Goedkeuren** of **Weigeren** op de balk boven de gebruikersnamen.

    ![De gebruiker selecteren](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Als u het niet zeker weet, u op de knop **Weet niet klikken.**

    Als u deze selectie maakt, behoudt de gebruiker de toegang en deze selectie vindt deze plaats in de controlelogboeken. In het logboek worden alle andere revisoren weergegeven die u nog steeds hebt voltooid.

1. Mogelijk moet u een reden voor uw beslissing geven. Typ een reden in en klik op **Verzenden**.

    ![Toegang goedkeuren of weigeren](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. U uw beslissing op elk gewenst moment voor het einde van de beoordeling wijzigen. Selecteer hiervoor de gebruiker in de lijst en wijzigt de beslissing. U bijvoorbeeld de toegang goedkeuren voor een gebruiker die u eerder hebt geweigerd.

Als er meerdere revisoren zijn, wordt het laatst ingediende antwoord geregistreerd. Houd rekening met een voorbeeld waarin een beheerder twee revisoren aanwijst: Alice en Bob. Alice opent de beoordeling eerst en keurt de toegang goed. Voordat de beoordeling afloopt, opent Bob de beoordeling en weigert toegang. In dit geval wordt de laatste beslissing tot weigeringstoegang geregistreerd.

>[!NOTE]
>Als een gebruiker de toegang wordt geweigerd, wordt deze niet onmiddellijk uit het toegangspakket verwijderd. De gebruiker wordt uit het toegangspakket verwijderd wanneer de beoordeling afloopt of een beheerder beëindigt de beoordeling.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Toegang goedkeuren of weigeren met behulp van de door het systeem gegenereerde aanbevelingen

Als u de toegang voor meerdere gebruikers sneller wilt controleren, u de door het systeem gegenereerde aanbevelingen gebruiken en de aanbevelingen met één klik accepteren. De aanbevelingen worden gegenereerd op basis van de aanmeldingsactiviteit van de gebruiker.

1.  Klik op de balk boven aan de pagina op **Aanbevelingen accepteren**.
    
    ![Aanbevelingen accepteren selecteren](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    U ziet een overzicht van de aanbevolen acties.

1.  Klik **op Verzenden** om de aanbevelingen te accepteren.

## <a name="next-steps"></a>Volgende stappen

- [Zelfbeoordeling van toegangspakketten](entitlement-management-access-reviews-self-review.md)