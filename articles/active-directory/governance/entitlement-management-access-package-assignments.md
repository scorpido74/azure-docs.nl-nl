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
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262020"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Toewijzingen voor een toegangs pakket in azure AD-rechts beheer weer geven, toevoegen en verwijderen

In het beheer van rechten van Azure AD kunt u zien wie er toegang tot pakketten, hun beleid en status heeft gekregen. Als een toegangs pakket het juiste beleid heeft, kunt u ook rechtstreeks een gebruiker toewijzen aan een toegangs pakket. In dit artikel wordt beschreven hoe u toewijzingen voor toegangs pakketten kunt weer geven, toevoegen en verwijderen.

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

### <a name="viewing-assignments-programmatically"></a>Toewijzingen weer geven via een programma

U kunt ook toewijzingen in een toegangs pakket ophalen met behulp van Microsoft Graph.  Een gebruiker in een geschikte rol met een toepassing die de machtiging gedelegeerde `EntitlementManagement.ReadWrite.All` heeft, kan de API aanroepen om de [lijst accessPackageAssignments te vermelden](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

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

### <a name="directly-assigning-users-programmatically"></a>Gebruikers rechtstreeks via een programma toewijzen

U kunt ook rechtstreeks een gebruiker toewijzen aan een toegangs pakket met behulp van Microsoft Graph.  Een gebruiker in een geschikte rol met een toepassing die de machtiging gedelegeerde `EntitlementManagement.ReadWrite.All` heeft, kan de API aanroepen om [een accessPackageAssignmentRequest te maken](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Een toewijzing verwijderen

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het menu links op **toewijzingen**.
 
1. Klik op het selectie vakje naast de gebruiker van wie u de toewijzing wilt verwijderen uit het toegangs pakket. 

1. Klik op de knop **verwijderen** boven aan het linkerdeel venster. 
 
    ![Toewijzingen-gebruiker uit toegangs pakket verwijderen](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Er wordt een melding weer gegeven waarin wordt gemeld dat de toewijzing is verwijderd. 

## <a name="next-steps"></a>Volgende stappen

- [Aanvraag en instellingen voor een toegangs pakket wijzigen](entitlement-management-access-package-request-policy.md)
- [Rapporten en logboeken weer geven](entitlement-management-reports.md)
