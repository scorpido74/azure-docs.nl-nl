---
title: Een catalogus met resources maken en beheren in azure AD-beheer-Azure Active Directory
description: Meer informatie over het maken van een nieuwe container met resources en toegangs pakketten in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66e3dee83ccf4a857fda9d5804848108c0cc251
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174438"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Een catalogus met resources maken en beheren in azure AD-rechts beheer

## <a name="create-a-catalog"></a>Een catalogus maken

Een catalogus is een container met resources en toegangs pakketten. U maakt een catalogus wanneer u gerelateerde resources en toegangs pakketten wilt groeperen. Degene die de catalogus maakt, wordt de eerste eigenaar van de catalogus. Een catalogus eigenaar kan extra catalogus eigenaren toevoegen.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of maker van catalogus

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi**.

    ![Rechten beheer catalogi in de Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klik op **nieuwe catalogus**.

1. Voer een unieke naam in voor de catalogus en geef een beschrijving op.

    Gebruikers zien deze informatie in de details van een toegangs pakket.

1. Als u wilt dat de toegangs pakketten in deze catalogus beschikbaar zijn voor gebruikers om aan te vragen zodra ze zijn gemaakt, stelt u **ingeschakeld** in op **Ja**.

1. Als u wilt toestaan dat gebruikers in geselecteerde externe directory's toegangs pakketten kunnen aanvragen in deze catalogus, stelt u **ingeschakeld voor externe gebruikers** in op **Ja**.

    ![Deel venster nieuwe catalogus](./media/entitlement-management-shared/new-catalog.png)

1. Klik op **maken** om de catalogus te maken.

## <a name="add-resources-to-a-catalog"></a>Resources toevoegen aan een catalogus

Als u resources wilt toevoegen aan een toegangs pakket, moeten de resources aanwezig zijn in een catalogus. De typen resources die u kunt toevoegen, zijn groepen, toepassingen en share point online-sites. De groepen kunnen bestaan uit in de Cloud gemaakte Office 365-groepen of in de Cloud gemaakte Azure AD-beveiligings groepen. De toepassingen kunnen Azure AD-bedrijfs toepassingen zijn, met inbegrip van zowel SaaS-toepassingen als uw eigen toepassingen die federatieve zijn voor Azure AD. De sites kunnen share point online-sites of share point online-site verzamelingen zijn.

**Vereiste rol:** De [vereiste rollen weer geven om resources toe te voegen aan een catalogus](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u resources wilt toevoegen.

1. Klik in het linkermenu op **resources**.

1. Klik op **resources toevoegen**.

1. Klik op een resource type: **groepen en teams**, **toepassingen**of **share point-sites**.

    Als u een resource die u wilt toevoegen niet ziet of u geen resource kunt toevoegen, moet u ervoor zorgen dat u beschikt over de vereiste functie voor Azure AD-Directory en rechten beheer. Mogelijk moet u iemand met de vereiste rollen hebben om de resource toe te voegen aan uw catalogus. Zie [vereiste rollen voor het toevoegen van resources aan een catalogus](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)voor meer informatie.

1. Selecteer een of meer resources van het type dat u wilt toevoegen aan de catalogus.

    ![Resources toevoegen aan een catalogus](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Wanneer u klaar bent, klikt u op **toevoegen**.

    Deze resources kunnen nu worden opgenomen in toegangs pakketten in de catalogus.

## <a name="remove-resources-from-a-catalog"></a>Resources uit een catalogus verwijderen

U kunt resources verwijderen uit een catalogus. Een resource kan alleen uit een catalogus worden verwijderd als deze niet wordt gebruikt in een van de toegangs pakketten van de catalogus.

**Vereiste rol:** De [vereiste rollen weer geven om resources toe te voegen aan een catalogus](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaruit u resources wilt verwijderen.

1. Klik in het linkermenu op **resources**.

1. Selecteer de resources die u wilt verwijderen.

1. Klik op **verwijderen** (of klik op het weglatings teken ( **...** ) en klik vervolgens op **resource verwijderen**.

## <a name="add-additional-catalog-owners"></a>Aanvullende catalogus eigenaren toevoegen

De gebruiker die een catalogus heeft gemaakt, wordt de eerste eigenaar van de catalogus. Als u het beheer van een catalogus wilt delegeren, voegt u gebruikers toe aan de rol van de catalogus eigenaar. Zo kunt u de verantwoordelijkheden voor het beheer van catalogi delen. 

Volg deze stappen om een gebruiker toe te wijzen aan de rol van de catalogus eigenaar:

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het menu links op **rollen en beheerders**.

    ![Catalogiseert rollen en beheerders](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klik op **eigen aren toevoegen** om de leden voor deze rollen te selecteren.

1. Klik op **selecteren** om deze leden toe te voegen.

## <a name="edit-a-catalog"></a>Een catalogus bewerken

U kunt de naam en beschrijving voor een catalogus bewerken. Gebruikers zien deze informatie in de details van een toegangs pakket.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus die u wilt bewerken.

1. Klik op de pagina **overzicht** van de catalogus op **bewerken**.

1. Bewerk de naam, beschrijving of ingeschakelde instellingen van de catalogus.

    ![Catalogus instellingen bewerken](./media/entitlement-management-shared/catalog-edit.png)

1. Klik op **Opslaan**.

## <a name="delete-a-catalog"></a>Een catalogus verwijderen

U kunt een catalogus verwijderen, maar alleen als deze geen toegangs pakketten heeft.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus die u wilt verwijderen.

1. Klik op **verwijderen**in het **overzicht**van de catalogus.

1. Klik op **Ja**in het bericht venster dat wordt weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Toegangs beheer delegeren voor toegang tot pakket beheerders](entitlement-management-delegate-managers.md)
