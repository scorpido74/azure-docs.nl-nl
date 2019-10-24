---
title: Toevoegen van uw organisatie privacy info - Azure Active Directory | Microsoft Docs
description: Instructies over het toevoegen van uw organisatie privacy-informatie naar het gebied van Azure Active Directory-eigenschappen.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2397662112bda6c6789b25691848344a4e611b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561805"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Privacy-informatie van uw organisatie met behulp van Azure Active Directory toevoegen
In dit artikel wordt uitgelegd hoe een tenantbeheerder met betrekking tot privacy-informatie kunt toevoegen aan de tenant Azure Active Directory (Azure AD) van een organisatie, via de Azure-portal.

Wij raden dat u zowel uw globaal privacycontact en de privacyverklaring van uw organisatie, toevoegen, zodat uw interne werknemers en externe gasten uw beleid kunnen bekijken. Omdat privacyverklaringen zijn unieke gemaakt en die is ontworpen voor elk bedrijf, wordt aangeraden u contact opnemen met een advocaat voor hulp.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Uw privacy-informatie die in Azure AD toevoegen
Toevoegen van privacy-informatie van uw organisatie in de **eigenschappen** gebied van Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Toegang tot het gedeelte Eigenschappen van en het toevoegen van uw privacy-informatie

1.  Meld u bij Azure portal aan als een tenantbeheerder.

2.  Selecteer op de navigatiebalk links **Azure Active Directory**, en selecteer vervolgens **eigenschappen**.

    De **eigenschappen** gebied wordt weergegeven.

    ![Azure AD-eigenschappengebied markeren van het gebied van privacy-informatie](media/active-directory-properties-area/properties-area.png)

3.  Uw privacy-informatie voor uw werknemers toevoegen:

    - **Technische contactpersoon.** Typ het e-mailadres voor de persoon die contact opnemen voor technische ondersteuning binnen uw organisatie.
    
    - **Globaal privacycontact.** Typ het e-mailadres voor de contactpersoon voor vragen over privacy van persoonlijke gegevens aan. Deze persoon is ook die Microsoft neemt contact op als er een inbreuk op gegevens. Als er niemand die hier worden vermeld, contact op met Microsoft uw globale beheerders.

    - **URL voor de privacyverklaring.** Typ de koppeling naar uw organisatie-document dat wordt beschreven hoe uw organisatie omgaat met zowel interne en externe van Gast gegevensprivacy.

        >[!Important]
        >Als u niet uw eigen privacyverklaring of uw privacycontact opneemt, ziet uw externe gasten tekst in de **revisie machtigingen** vak met de tekst,  **< _de naam van uw organisatie_> koppelingen naar hun voorwaarden ter controle niet is opgegeven**. Bijvoorbeeld, ziet een gastgebruiker dit bericht wanneer ze een uitnodiging ontvangen voor toegang tot een organisatie via B2B-samenwerking.

        ![B2B-samenwerking revisie machtigingen vak met het bericht](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
- [Azure Active Directory B2B-samenwerking uitnodiging inwisselen](https://aka.ms/b2bredemption)
- [Toevoegen of wijzigen van de profielgegevens van een gebruiker in Azure Active Directory](active-directory-users-profile-azure-portal.md)
