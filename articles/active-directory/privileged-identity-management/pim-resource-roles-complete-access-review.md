---
title: Toegangscontrole van Azure-bronrollen voltooien in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het voltooien van een toegangscontrole van Azure-bronrollen Privileged Identity Management in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021982"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Een toegangscontrole van Azure-bronrollen voltooien in Privileged Identity Management

Beheerders van bevoorrechte rollen kunnen bevoorrechte toegang controleren nadat ze [een toegangscontrole hebben gestart.](pim-resource-roles-start-access-review.md) Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) stuurt automatisch een e-mail waarin gebruikers worden gevraagd hun toegang te controleren. Als een gebruiker geen e-mail ontvangt, u hem of haar de instructies sturen voor [het uitvoeren van een toegangscontrole.](pim-resource-roles-perform-access-review.md)

Nadat de periode voor toegangscontrole voorbij is, of nadat alle gebruikers hun zelfbeoordeling hebben voltooid, volgt u de stappen in dit artikel om de beoordeling te beheren en de resultaten te bekijken.

## <a name="manage-access-reviews"></a>Toegangsbeoordelingen beheren

1. Ga naar de [Azure-portal.](https://portal.azure.com/) Selecteer op het dashboard de **Azure-resourcesservice.**

2. Selecteer uw resource.

3. Selecteer het gedeelte **Toegangsrecensies** van het dashboard.

    ![Azure-bronnen - Lijst met toegangsbeoordelingen met rol, eigenaar, begindatum, einddatum en status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecteer de toegangscontrole die u wilt beheren.

Op de detailpagina voor de toegangscontrole zijn er een aantal opties voor het beheren van die beoordeling. Dit zijn de opties:

![Opties voor het beheren van een beoordeling - Stoppen, opnieuw instellen, toepassen, verwijderen](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppen

Alle toegangsbeoordelingen hebben een einddatum. Selecteer **Stoppen** om het eerder af te maken. Gebruikers die hun beoordeling op dat moment nog niet hebben voltooid, kunnen deze niet voltooien nadat u de beoordeling hebt gestopt. U een beoordeling niet opnieuw starten nadat deze is gestopt.

### <a name="reset"></a>Opnieuw instellen

U een toegangscontrole opnieuw instellen om alle beslissingen die erop worden genomen te verwijderen. Nadat u een toegangscontrole opnieuw hebt ingesteld, worden alle gebruikers gemarkeerd als niet opnieuw beoordeeld.

### <a name="apply"></a>Toepassen

Nadat een toegangscontrole is voltooid, selecteert u **Toepassen** om de resultaten van de beoordeling te implementeren. Als de toegang van een gebruiker in de beoordeling is geweigerd, wordt in deze stap de roltoewijzing verwijderd.  

### <a name="delete"></a>Verwijderen

Als u niet meer geïnteresseerd bent in de beoordeling, verwijdert u deze. Selecteer **Yo** verwijderen de revisie verwijderen uit de service Bevoegde identiteitsbeheer.

## <a name="results"></a>Resultaten

Bekijk en download op de pagina **Resultaten** een lijst met de resultaten van uw beoordeling.

![Resultatenpagina met gebruikers, resultaat, reden, beoordeeld door, toegepast op en resultaat toepassen](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisoren

Bekijk en voeg revisoren toe aan uw bestaande toegangscontrole. Herinner reviewers eraan om hun beoordelingen in te vullen.

![Paginanaam van revisoren en gebruikersnaam](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Volgende stappen

- [Een toegangscontrole voor Azure-bronrollen starten in Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Een toegangscontrole uitvoeren van mijn Azure-bronrollen in Privileged Identity Management](pim-resource-roles-perform-access-review.md)
