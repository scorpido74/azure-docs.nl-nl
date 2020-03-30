---
title: Een nieuw toegangspakket maken in beheer van rechten - Azure AD
description: Meer informatie over het maken van een nieuw toegangspakket met resources die u wilt delen in Azure Active Directory-rechtenbeheer.
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
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262007"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Een nieuw toegangspakket maken in Azure AD-rechtenbeheer

Met een toegangspakket u een eenmalige installatie uitvoeren van resources en beleidsregels die automatisch toegang beheren voor de levensduur van het toegangspakket. In dit artikel wordt beschreven hoe u een nieuw toegangspakket maakt.

## <a name="overview"></a>Overzicht

Alle toegangspakketten moeten in een container worden geplaatst die een catalogus wordt genoemd. Een catalogus definieert welke bronnen u aan uw toegangspakket toevoegen. Als u geen catalogus opgeeft, wordt uw toegangspakket in de algemene catalogus geplaatst. Momenteel u een bestaand toegangspakket niet verplaatsen naar een andere catalogus.

Als u een toegangspakketbeheerder bent, u geen resources die u bezit toevoegen aan een catalogus. U bent beperkt tot het gebruik van de bronnen die beschikbaar zijn in de catalogus. Als u resources aan een catalogus wilt toevoegen, u dit aan de eigenaar van de catalogus vragen.

Alle toegangspakketten moeten ten minste één beleid hebben. Beleidsregels geven aan wie het toegangspakket kan aanvragen en ook goedkeurings- en levenscyclusinstellingen. Wanneer u een nieuw toegangspakket maakt, u een eerste beleid maken voor gebruikers in uw directory, voor gebruikers die niet in uw directory staan, alleen voor directe toewijzingen van beheerders of u ervoor kiezen om het beleid later te maken.

![Een toegangspakket maken](./media/entitlement-management-access-package-create/access-package-create.png)

Dit zijn de stappen op hoog niveau om een nieuw toegangspakket te maken.

1. Start in Identity Governance het proces om een nieuw toegangspakket te maken.

1. Selecteer de catalogus waarin u het toegangspakket wilt maken.

1. Voeg bronnen uit de catalogus toe aan uw toegangspakket.

1. Resourcerollen toewijzen voor elke resource.

1. Geef gebruikers op die toegang kunnen aanvragen.

1. Geef eventuele goedkeuringsinstellingen op.

1. Geef levenscyclusinstellingen op.

## <a name="start-new-access-package"></a>Nieuw toegangspakket starten

**Vereiste rol:** Globale beheerder, gebruikersbeheerder, cataloguseigenaar of Access-pakketbeheer

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Klik op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Toegangspakketten**.

1. Klik **op Nieuw toegangspakket**.
   
    ![Rechtenbeheer in de Azure-portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Basisbeginselen

Geef op het tabblad **Basisbeginselen** het toegangspakket een naam en geef op in welke catalogus u het toegangspakket wilt maken.

1. Voer een weergavenaam en beschrijving in voor het toegangspakket. Gebruikers zien deze informatie wanneer ze een verzoek indienen voor het toegangspakket.

1. Selecteer in de vervolgkeuzelijst **Catalogus** de catalogus waarin u het toegangspakket wilt maken. U hebt bijvoorbeeld een cataloguseigenaar die alle marketingbronnen beheert die kunnen worden aangevraagd. In dit geval u de marketingcatalogus selecteren.

    U ziet alleen catalogi waarin u toestemming hebt om toegangspakketten te maken. Als u een toegangspakket in een bestaande catalogus wilt maken, moet u een globale beheerder of gebruikersbeheerder zijn of moet u een cataloguseigenaar of toegangspakketbeheerder in die catalogus zijn.

    ![Toegangspakket - Basisbeginselen](./media/entitlement-management-access-package-create/basics.png)

    Als u een globale beheerder, een gebruikersbeheerder of catalogusmaker bent en u uw toegangspakket wilt maken in een nieuwe catalogus die niet wordt weergegeven, klikt u op **Nieuwe catalogus maken**. Voer de naam en beschrijving van de catalogus in en klik op **Maken**.

    Het toegangspakket dat u maakt en alle bronnen die erin zijn opgenomen, worden toegevoegd aan de nieuwe catalogus. U later ook extra cataloguseigenaren toevoegen.

1. Klik op **Volgende**.

## <a name="resource-roles"></a>Resourcerollen

Selecteer op het tabblad **Resourcerollen** de bronnen die u in het toegangspakket wilt opnemen. Gebruikers die het toegangspakket aanvragen en ontvangen, ontvangen alle resourcerollen in het toegangspakket.

1. Klik op het resourcetype dat u wilt toevoegen **(Groepen en Teams,** **Toepassingen**of **SharePoint-sites).**

1. Selecteer in het deelvenster Selecteren dat wordt weergegeven een of meer bronnen in de lijst.

    ![Toegangspakket - Resourcerollen](./media/entitlement-management-access-package-create/resource-roles.png)

    Als u het toegangspakket maakt in de algemene catalogus of een nieuwe catalogus, u alle bronnen kiezen uit de map die u bezit. U moet ten minste een globale beheerder, een gebruikersbeheerder of catalogusmaker zijn.

    Als u het toegangspakket in een bestaande catalogus maakt, u alle bronnen selecteren die al in de catalogus staan zonder deze te bezitten.

    Als u een globale beheerder, een gebruikersbeheerder of cataloguseigenaar bent, hebt u de extra optie om resources te selecteren waarvan u eigenaar bent en die nog niet in de catalogus staan. Als u resources selecteert die momenteel niet in de geselecteerde catalogus staan, worden deze bronnen ook aan de catalogus toegevoegd waarmee andere catalogusbeheerders toegangspakketten kunnen maken. Als u alleen resources wilt selecteren die zich momenteel in de geselecteerde catalogus bevinden, schakelt u het selectievakje **Alleen weergeven** boven aan het deelvenster Selecteren in.

1. Nadat u de resources hebt geselecteerd, selecteert u in de lijst **Rol** de rol die gebruikers voor de resource moeten toegewezen krijgen.

    ![Toegangspakket - Selectie van resourcerollen](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klik op **Volgende**.

## <a name="requests"></a>Aanvragen

Op het tabblad **Aanvragen** maakt u het eerste beleid om op te geven wie het toegangspakket en ook goedkeuringsinstellingen kan aanvragen. Later u meer aanvraagbeleid maken om extra groepen gebruikers toe te staan het toegangspakket met hun eigen goedkeuringsinstellingen aan te vragen.

![Toegangspakket - tabblad Aanvragen](./media/entitlement-management-access-package-create/requests.png)

Afhankelijk van wie u dit toegangspakket wilt kunnen aanvragen, voert u de stappen uit in een van de volgende secties.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Controleren + maken

Op het tabblad **Controleren + maken** u uw instellingen controleren en controleren op validatiefouten.

1. De instellingen van het toegangspakket controleren

    ![Toegangspakket - Beleid- Beleidsinstelling inschakelen](./media/entitlement-management-access-package-create/review-create.png)

1. Klik **op Maken** om het toegangspakket te maken.

    Het nieuwe toegangspakket wordt weergegeven in de lijst met toegangspakketten.

## <a name="creating-an-access-package-programmatically"></a>Een toegangspakket programmatisch maken

U ook een toegangspakket maken met Microsoft Graph.  Een gebruiker in een geschikte rol met `EntitlementManagement.ReadWrite.All` een toepassing met de gedelegeerde machtiging kan de API aanroepen om

1. [Vermeld de accessPackageResources in de catalogus](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) en [maak een accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) voor bronnen die nog niet in de catalogus staan.
1. [Vermeld de accessPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) van elke accessPackageResource in een accessPackageCatalog. Deze lijst met rollen wordt vervolgens gebruikt om een rol te selecteren, wanneer u vervolgens een accessPackageResourceRoleScope maakt.
1. [Maak een accessPackage](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta).
1. [Maak een accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. [Maak een accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) voor elke resourcerol die nodig is in het toegangspakket.

## <a name="next-steps"></a>Volgende stappen

- [Koppeling delen om een toegangspakket aan te vragen](entitlement-management-access-package-settings.md)
- [Resourcerollen voor een toegangspakket wijzigen](entitlement-management-access-package-resources.md)
- [Een gebruiker rechtstreeks toewijzen aan het toegangspakket](entitlement-management-access-package-assignments.md)
