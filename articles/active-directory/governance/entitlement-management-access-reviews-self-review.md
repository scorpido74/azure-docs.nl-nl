---
title: Zelfbeoordeling van een toegangspakket in Azure AD-rechtenbeheer
description: Meer informatie over het controleren van de toegang van gebruikers tot toegangspakketten voor beheer van rechten in Azure Active Directory-toegangsbeoordelingen (Preview).
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
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967762"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Zelfbeoordeling van een toegangspakket in Azure AD-rechtenbeheer

Azure AD-rechtenbeheer vereenvoudigt de manier waarop ondernemingen de toegang tot groepen, toepassingen en SharePoint-sites beheren. In dit artikel wordt beschreven hoe een gebruiker zijn toegewezen toegangspakket(en) zelf controleert.

## <a name="open-the-access-review"></a>De toegangscontrole openen

Als u een toegangscontrole wilt doen, moet u eerst de toegangscontrole openen. Gebruik de volgende procedure om de toegangscontrole te zoeken en te openen:

1. U ontvangt mogelijk een e-mail van Microsoft waarin u wordt gevraagd de toegang te bekijken. Zoek de e-mail om de toegangscontrole te openen. Hier is een voorbeeld van een e-mail met het verzoek om een herziening van de toegang: 
    
    ![E-mail voor beoordeling van de beoordeling van de toegang](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Klik op de koppeling **Toegangs controleren.**

1. Je ook https://myaccess.microsoft.com rechtstreeks naar je in behandeling zijnde toegangsbeoordelingen gaan als je geen e-mail ontvangt.  (Voor de Amerikaanse `https://myaccess.microsoft.us` regering, gebruik in plaats daarvan.)

1. Klik op **Toegangsbeoordelingen** op de linkernavigatiebalk om een lijst met in behandeling zijnde toegangsbeoordelingen te bekijken die aan u zijn toegewezen.


1.  Klik op de beoordeling waarmee u wilt beginnen.

## <a name="perform-the-access-review"></a>De toegangscontrole uitvoeren

Zodra u de toegangscontrole opent, u uw toegang zien. Gebruik de volgende procedure om de toegangscontrole uit te voeren:

1.  Bepaal of u nog steeds toegang nodig hebt tot het toegangspakket. Het project waaraan u werkt, is bijvoorbeeld niet voltooid, dus u hebt nog steeds toegang nodig om aan het project te blijven werken.

1.  Klik **op Ja** om uw toegang te behouden of klik op **Nee** om uw toegang te verwijderen.
    >[!NOTE]
    >Als u hebt aangegeven dat u geen toegang meer nodig hebt, wordt u niet onmiddellijk uit het toegangspakket verwijderd. U wordt uit het toegangspakket verwijderd wanneer de beoordeling afloopt of als een beheerder de beoordeling stopt.

1.  Als u op **Ja**hebt geklikt, moet u mogelijk een motiveringsverklaring opnemen in het vak **Reden.**

1.  Klik **op Verzenden**.

U terugkeren naar de beoordeling als u van gedachten verandert en besluit uw reactie te wijzigen voor het einde van de beoordeling.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot toegangspakketten controleren](entitlement-management-access-reviews-review-access.md) 
