---
title: Toegangsbeheer delegeren aan toegangsbeheer in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het delegeren van toegangsbeheer van IT-beheerders aan toegang tot pakketbeheerders en projectmanagers, zodat ze zelf toegang kunnen beheren.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174367"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Toegangsbeheer delegeren aan toegangsbeheer in Azure AD-rechtenbeheer

Als u het maken en beheren van toegangspakketten in een catalogus wilt delegeren, voegt u gebruikers toe aan de rol toegangspakketbeheer. Beheerders van toegangspakketten moeten vertrouwd zijn met de noodzaak voor gebruikers om toegang te vragen tot bronnen in een catalogus. Als een catalogus bijvoorbeeld wordt gebruikt voor een project, kan een projectlead een toegangspakketbeheerder voor die catalogus zijn.  Beheerders van toegangspakketten kunnen geen resources toevoegen aan een catalogus, maar ze kunnen de toegangspakketten en -beleidsregels in een catalogus beheren.  Bij het delegeren aan een access package manager kan die persoon dan verantwoordelijk zijn voor:

- Welke rollen een gebruiker heeft voor de bronnen in een catalogus
- Wie heeft toegang nodig
- Wie moet de toegangsaanvragen goedkeuren
- Hoe lang het project zal duren

In deze video vindt u een overzicht van hoe u toegangsbeheer delegeren van de eigenaar van de catalogus naar toegang tot pakketbeheer.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Delegeren aan een toegangspakketbeheerder aan een toegangspakketbeheerder als eigenaar van een catalogus

Volg deze stappen om een gebruiker toe te wijzen aan de rol access package manager:

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het linkermenu op **Rollen en beheerders**.

    ![Rollen en beheerders van catalogi](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klik **op Access-pakketbeheerders toevoegen** om de leden voor deze rollen te selecteren.

1. Klik **op Selecteren** om deze leden toe te voegen.

## <a name="remove-an-access-package-manager"></a>Een toegangspakketbeheerder verwijderen

Volg de volgende stappen om een gebruiker uit de rol access package manager te verwijderen:

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het linkermenu op **Rollen en beheerders**.

1. Voeg een vinkje toe naast een toegangspakketbeheer dat u wilt verwijderen.

1. Klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md)
