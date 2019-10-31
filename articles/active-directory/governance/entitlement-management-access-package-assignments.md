---
title: Toewijzingen voor een toegangs pakket in azure AD-rechten beheer weer geven, toevoegen en verwijderen-Azure Active Directory
description: Meer informatie over het weer geven, toevoegen en verwijderen van toewijzingen voor een toegangs pakket in Azure Active Directory rechten beheer.
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f06da43e758c24da65664b34dd83f876a00e3a0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174911"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Toewijzingen voor een toegangs pakket in azure AD-rechts beheer weer geven, toevoegen en verwijderen

In het beheer van rechten van Azure AD kunt u zien wie er toegang tot pakketten, hun beleid en status heeft gekregen. Als een toegangs pakket het juiste beleid heeft, kunt u ook rechtstreeks een gebruiker toewijzen aan een toegangs pakket. In dit artikel wordt beschreven hoe u toewijzingen voor een toegangs pakket kunt weer geven, toevoegen en verwijderen.

## <a name="view-who-has-an-assignment"></a>Weer geven wie een toewijzing heeft

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **toewijzingen** voor een overzicht van actieve toewijzingen.

    ![Lijst met toewijzingen aan een toegangs pakket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klik op een specifieke toewijzing om meer details weer te geven.

1. Als u een lijst wilt weer geven met toewijzingen waarvoor niet alle resource rollen op de juiste wijze zijn ingericht, klikt u op de filter status en selecteert u **leveren**.

    U kunt aanvullende informatie over bezorgings fouten zien door de overeenkomende aanvraag van de gebruiker op de pagina **aanvragen** te vinden.

1. Als u verlopen toewijzingen wilt zien, klikt u op de filter status en selecteert u **verlopen**.

1. Als u een CSV-bestand van de gefilterde lijst wilt downloaden, klikt u op **downloaden**.

## <a name="directly-assign-a-user"></a>Een gebruiker rechtstreeks toewijzen

In sommige gevallen wilt u mogelijk rechtstreeks specifieke gebruikers toewijzen aan een toegangs pakket, zodat gebruikers niet hoeven door te gaan met het proces van het aanvragen van het toegangs pakket. Om gebruikers rechtstreeks toe te wijzen, moet het toegangs pakket beschikken over een beleid waarmee beheerders direct toewijzingen kunnen toestaan.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het menu links op **toewijzingen**.

1. Klik op **nieuwe toewijzing** om het pakket gebruiker toevoegen voor toegang te openen.

    ![Toewijzingen-gebruiker toevoegen aan toegangs pakket](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klik op **gebruikers toevoegen** om de gebruikers te selecteren aan wie u dit toegangs pakket wilt toewijzen.

1. Selecteer in de lijst **beleid selecteren** een beleid dat de toekomstige aanvragen en levens cyclus van de gebruikers worden onderhevig en gevolgd door. Als u wilt dat de geselecteerde gebruikers verschillende beleids instellingen hebben, kunt u op **Nieuw beleid maken** klikken om een nieuw beleid toe te voegen.

1. Stel de datum en tijd in waarop u de toewijzing van de geselecteerde gebruikers wilt starten en beëindigen. Als er geen eind datum wordt gegeven, worden de levens cyclus instellingen van het beleid gebruikt.

1. Geef eventueel een reden op voor uw directe toewijzing voor het bijhouden van de record.

1. Klik op **toevoegen** om de geselecteerde gebruikers rechtstreeks toe te wijzen aan het toegangs pakket.

    Na enkele ogen blikken klikt u op **vernieuwen** om de gebruikers in de lijst toewijzingen weer te geven.

## <a name="next-steps"></a>Volgende stappen

- [Aanvraag en instellingen voor een toegangs pakket wijzigen](entitlement-management-access-package-request-policy.md)
- [Rapporten en logboeken weer geven](entitlement-management-reports.md)
