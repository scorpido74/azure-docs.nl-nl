---
title: Dynamische groepen en B2B-samenwerking - Azure Active Directory | Microsoft Documenten
description: Toont hoe u Azure AD-dynamische groepen gebruikt met Azure Active Directory B2B-samenwerking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226874"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamische groepen en Azure Active Directory B2B-samenwerking

## <a name="what-are-dynamic-groups"></a>Wat zijn dynamische groepen?
Dynamische configuratie van beveiligingsgroepslidmaatschap voor Azure Active Directory (Azure AD) is beschikbaar in [de Azure-portal.](https://portal.azure.com) Beheerders kunnen regels instellen voor het vullen van groepen die zijn gemaakt in Azure AD op basis van gebruikerskenmerken (zoals userType, afdeling of land/regio). Leden kunnen automatisch worden toegevoegd aan of verwijderd uit een beveiligingsgroep op basis van hun kenmerken. Deze groepen kunnen toegang bieden tot toepassingen of cloudbronnen (SharePoint-sites, documenten) en licenties toewijzen aan leden. Lees meer over dynamische groepen in [Specifieke groepen in Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

De juiste [Azure AD Premium P1- of P2-licenties](https://azure.microsoft.com/pricing/details/active-directory/) zijn vereist om dynamische groepen te maken en te gebruiken. Meer informatie in het artikel [Regels maken op basis van kenmerken voor dynamisch groepslidmaatschap in Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Een dynamische groep 'alle gebruikers' maken
U een groep maken met alle gebruikers binnen een tenant met behulp van een lidmaatschapsregel. Wanneer gebruikers in de toekomst worden toegevoegd of uit de tenant worden verwijderd, wordt het lidmaatschap van de groep automatisch aangepast.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account waaraan de rol Globale beheerder of gebruikersbeheerder in de tenant is toegewezen.
1. Selecteer **Azure Active Directory**.
2. Selecteer **onder Beheren**de optie **Groepen**en selecteer Vervolgens **Nieuwe groep**.
1. Selecteer op de pagina **Nieuwe groep** onder **Groepstype**de optie **Beveiliging**. Voer een **naam** en **beschrijving** in voor de nieuwe groep. 
2. Selecteer **onder Lidmaatschapstype** **Dynamische gebruiker**en selecteer Dynamische query **toevoegen**. 
4. **Selecteer**Bewerken boven het tekstvak **Regelsyntaxis** . Typ op de pagina **Syntaxis van de regel bewerken** de volgende expressie in het tekstvak:

   ```
   user.objectId -ne null
   ```
1. Selecteer **OK**. De regel wordt weergegeven in het vak Syntaxis van regel:

   ![Regelsyntaxis voor alle dynamische groep gebruikers](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Selecteer **Opslaan**. De nieuwe dynamische groep zal nu b2b-gastgebruikers en ledengebruikers omvatten.


1. Selecteer **Maken** op de **pagina Nieuw om** de groep te maken.

## <a name="creating-a-group-of-members-only"></a>Alleen een groep leden maken

Als u wilt dat uw groep gastgebruikers uitsluit en alleen leden van uw tenant opneemt, maakt u een dynamische groep zoals hierboven beschreven, maar voert u in het vak **Syntaxis van regel** de volgende expressie in:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

In de volgende afbeelding wordt de syntaxis van de regel weergegeven voor een dynamische groep die is gewijzigd om alleen leden op te nemen en gasten uit te sluiten.

![Toont regel waarin het gebruikerstype gelijk is aan lid](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Alleen een groep gasten maken

Mogelijk vindt u het ook handig om een nieuwe dynamische groep te maken die alleen gastgebruikers bevat, zodat u beleid (zoals Azure AD Conditional Access-beleidsregels) op hen toepassen. Maak een dynamische groep zoals hierboven beschreven, maar voer in het vak **Regelsyntaxis** de volgende expressie in:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

In de volgende afbeelding wordt de syntaxis van de regel weergegeven voor een dynamische groep die is gewijzigd om alleen gasten op te nemen en ledengebruikers uit te sluiten.

![Toont regel waarin het gebruikerstype gelijk is aan gast](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van gebruikers van B2B-samenwerking](user-properties.md)
- [Een B2B-samenwerkingsgebruiker toevoegen aan een rol](add-guest-to-role.md)
- [Voorwaardelijke toegang voor Gebruikers van B2B-samenwerkingsverbanden](conditional-access.md)

