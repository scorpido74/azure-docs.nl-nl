---
title: Een catalogus met resources beheren in beheer van rechten - Azure AD & beheren
description: Meer informatie over het maken van een nieuwe container met resources en toegangspakketten in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffa6f2fbdcde41d81e268779c3a0586bd8310792
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437556"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Een catalogus met resources maken en beheren in Azure AD-rechtenbeheer

## <a name="create-a-catalog"></a>Een catalogus maken

Een catalogus is een container met resources en toegangspakketten. U maakt een catalogus wanneer u gerelateerde bronnen wilt groeperen en toegang wilt krijgen tot pakketten. Wie de catalogus maakt, wordt de eerste cataloguseigenaar. Een cataloguseigenaar kan extra cataloguseigenaren toevoegen.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of catalogusmaker

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi**.

    ![Rechtenbeheercatalogi in de Azure-portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klik **op Nieuwe catalogus**.

1. Voer een unieke naam voor de catalogus in en geef een beschrijving.

    Gebruikers zien deze informatie in de details van een toegangspakket.

1. Als u wilt dat de toegangspakketten in deze catalogus beschikbaar zijn voor gebruikers om te vragen zodra ze zijn gemaakt, stelt u **Ingeschakeld in op** **Ja**.

1. Als u wilt dat gebruikers in geselecteerde externe mappen toegangspakketten in deze catalogus kunnen aanvragen, stelt u **Ingeschakeld voor externe gebruikers** in op **Ja**.

    ![Nieuw catalogusvenster](./media/entitlement-management-shared/new-catalog.png)

1. Klik **op Maken** om de catalogus te maken.

### <a name="creating-a-catalog-programmatically"></a>Een catalogus programmatisch maken

U ook een catalogus maken met Microsoft Graph.  Een gebruiker in een geschikte rol met `EntitlementManagement.ReadWrite.All` een toepassing met de gedelegeerde machtiging kan de API aanroepen om [een accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta)te maken.

## <a name="add-resources-to-a-catalog"></a>Bronnen toevoegen aan een catalogus

Als u resources wilt opnemen in een toegangspakket, moeten de bronnen in een catalogus bestaan. De typen resources die u toevoegen, zijn groepen, toepassingen en SharePoint Online-sites. De groepen kunnen office 365-groepen in de cloud zijn of azure AD-beveiligingsgroepen die in de cloud zijn gemaakt. De toepassingen kunnen Azure AD Enterprise-toepassingen zijn, inclusief zowel SaaS-toepassingen als uw eigen toepassingen die worden gefedereerd naar Azure AD. De sites kunnen SharePoint Online-sites of SharePoint Online-siteverzamelingen zijn.

**Vereiste rol:** Vereiste [rollen bekijken om resources aan een catalogus toe te voegen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi** en open vervolgens de catalogus waaraan u resources wilt toevoegen.

1. Klik in het linkermenu op **Resources**.

1. Klik **op Resources toevoegen**.

1. Klik op een resourcetype: **groepen en teams,** **toepassingen**of **SharePoint-sites**.

    Als u geen resource ziet die u wilt toevoegen of als u geen resource toevoegen, controleert u of u over de vereiste Azure AD-maprol en beheerrol voor rechten beschikt. Mogelijk moet u iemand met de vereiste rollen de bron aan uw catalogus toevoegen. Zie [Vereiste rollen om resources toe te voegen aan een catalogus voor](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)meer informatie.

1. Selecteer een of meer bronnen van het type dat u aan de catalogus wilt toevoegen.

    ![Bronnen toevoegen aan een catalogus](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Klik op **Toevoegen**als u klaar bent.

    Deze bronnen kunnen nu worden opgenomen in toegangspakketten in de catalogus.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Een bron programmatisch toevoegen aan een catalogus

U ook een bron toevoegen aan een catalogus met Microsoft Graph.  Een gebruiker in een geschikte rol of een catalogus- en broneigenaar, met een toepassing met de gedelegeerde `EntitlementManagement.ReadWrite.All` machtiging kan de API aanroepen om een [accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)te maken.

## <a name="remove-resources-from-a-catalog"></a>Resources uit een catalogus verwijderen

U resources uit een catalogus verwijderen. Een bron kan alleen uit een catalogus worden verwijderd als deze niet wordt gebruikt in een van de toegangspakketten van de catalogus.

**Vereiste rol:** Vereiste [rollen bekijken om resources aan een catalogus toe te voegen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi** en open vervolgens de catalogus waaruit u resources wilt verwijderen.

1. Klik in het linkermenu op **Resources**.

1. Selecteer de resources die u wilt verwijderen.

1. Klik **op Verwijderen** (of klik op de ellips (**...**) en klik vervolgens op **Bron verwijderen).**

## <a name="add-additional-catalog-owners"></a>Extra cataloguseigenaren toevoegen

De gebruiker die een catalogus heeft gemaakt, wordt de eerste cataloguseigenaar. Als u het beheer van een catalogus wilt delegeren, voegt u gebruikers toe aan de rol van de cataloguseigenaar. Dit helpt bij het delen van de verantwoordelijkheden voor het beheer van de catalogus. 

Volg deze stappen om een gebruiker toe te wijzen aan de rol voor eigenaar van de catalogus:

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het linkermenu op **Rollen en beheerders**.

    ![Rollen en beheerders van catalogi](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klik **op Eigenaren toevoegen** om de leden voor deze rollen te selecteren.

1. Klik **op Selecteren** om deze leden toe te voegen.

## <a name="edit-a-catalog"></a>Een catalogus bewerken

U de naam en beschrijving voor een catalogus bewerken. Gebruikers zien deze informatie in de gegevens van een toegangspakket.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi** en open de catalogus die u wilt bewerken.

1. Klik op de **pagina Overzicht** van de catalogus op **Bewerken**.

1. Bewerk de naam, beschrijving of ingeschakelde instellingen van de catalogus.

    ![Catalogusinstellingen bewerken](./media/entitlement-management-shared/catalog-edit.png)

1. Klik op **Opslaan**.

## <a name="delete-a-catalog"></a>Een catalogus verwijderen

U een catalogus verwijderen, maar alleen als deze geen toegangspakketten heeft.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi** en open vervolgens de catalogus die u wilt verwijderen.

1. Klik in het **overzicht**van de catalogus op **Verwijderen**.

1. Klik in het berichtvak dat wordt weergegeven op **Ja**.

### <a name="deleting-a-catalog-programmatically"></a>Een catalogus programmatisch verwijderen

U ook een catalogus verwijderen met Microsoft Graph.  Een gebruiker in een geschikte rol met `EntitlementManagement.ReadWrite.All` een toepassing met de gedelegeerde machtiging kan de API aanroepen om [een accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta)te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Toegangsbeheer delegeren aan toegangspakketbeheerders](entitlement-management-delegate-managers.md)
