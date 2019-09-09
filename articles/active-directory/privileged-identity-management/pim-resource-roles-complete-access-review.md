---
title: Een toegangs beoordeling van Azure-resource rollen in PIM-Azure Active Directory volt ooien | Microsoft Docs
description: Meer informatie over het volt ooien van een toegangs beoordeling van Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b9563a4032011b999bf867fc782ba4cbb9c3fac
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804252"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Een toegangs beoordeling van Azure-resource rollen in PIM volt ooien
Beheerders van geprivilegieerde rollen kunnen privileged Access bekijken nadat een [toegangs beoordeling is gestart](pim-resource-roles-start-access-review.md). Azure Active Directory (Azure AD) Privileged Identity Management (PIM) verzendt automatisch een e-mail bericht waarin gebruikers wordt gevraagd hun toegang te controleren. Als een gebruiker geen e-mail bericht ontvangt, kunt u de instructies voor het [uitvoeren van een toegangs beoordeling](pim-resource-roles-perform-access-review.md)verzenden.

Volg de stappen in dit artikel om de controle te beheren en de resultaten te bekijken nadat de toegangs beoordelings periode is afgelopen of nadat alle gebruikers de zelf beoordeling hebben voltooid.

## <a name="manage-access-reviews"></a>Toegangs beoordelingen beheren
1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer vervolgens in het dash board de toepassing **Azure-resources** .

2. Selecteer uw resource.

3. Selecteer de sectie **toegangs beoordelingen** van het dash board.

    ![Azure-resources-lijst met toegangs beoordelingen met rol, eigenaar, begin datum, eind datum en status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecteer de toegangs beoordeling die u wilt beheren.

Op de Blade Details van de toegangs beoordeling zijn er een aantal opties voor het beheren van die beoordeling. U hebt de volgende opties:

![Opties voor het beheren van een beoordeling-stoppen, opnieuw instellen, Toep assen, verwijderen](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppen
Alle toegangs beoordelingen hebben een eind datum, maar u kunt de knop **stoppen** gebruiken om deze te volt ooien. Alle gebruikers die op dit moment hun beoordeling niet hebben voltooid, kunnen deze niet meer volt ooien nadat u de beoordeling hebt gestopt. U kunt een beoordeling niet opnieuw starten nadat deze is gestopt.

### <a name="reset"></a>opnieuw instellen
U kunt een toegangs beoordeling opnieuw instellen om alle beslissingen te verwijderen die erop zijn gemaakt. Nadat u een toegangs beoordeling opnieuw hebt ingesteld, worden alle gebruikers gemarkeerd als niet-gecontroleerd. 

### <a name="apply"></a>Toepassen
Wanneer een toegangs beoordeling is voltooid, gebruikt u de knop **Toep assen** om het resultaat van de beoordeling te implementeren. Als de toegang van een gebruiker bij de controle is geweigerd, wordt met deze stap de roltoewijzing verwijderd.  

### <a name="delete"></a>Verwijderen
Als u de controle niet meer wilt doen, verwijdert u deze. De knop **verwijderen** verwijdert de beoordeling van de PIM-toepassing.

## <a name="results"></a>Resultaten
Op de pagina **resultaten** kunt u een lijst met de resultaten van de beoordeling bekijken en downloaden. 

![Pagina met resultaten met vermelding van gebruikers, resultaat, reden, gereviseerd door, toegepast op en resultaat Toep assen](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisor
Revisoren weer geven en toevoegen aan uw bestaande toegangs beoordeling. Herinner revisoren om hun beoordelingen te volt ooien.

![Pagina revisoren naam en user principal name](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling starten voor mijn Azure AD-resourcerollen in PIM](pim-resource-roles-start-access-review.md)
- [Perform an access review of my Azure AD resource roles in PIM](pim-resource-roles-perform-access-review.md) (Een toegangsbeoordeling uitvoeren van mijn Azure AD-resourcerollen in PIM)
