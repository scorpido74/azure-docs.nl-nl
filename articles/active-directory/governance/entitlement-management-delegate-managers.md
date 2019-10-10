---
title: Het toegangs beheer voor toegang tot pakket managers in azure AD-rechts Management (preview) overdragen-Azure Active Directory
description: Meer informatie over het overdragen van toegangs beheer van IT-beheerders om toegang te krijgen tot pakket beheerders en project managers, zodat ze de toegang zelf kunnen beheren.
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
ms.openlocfilehash: c7a2b6bfdb4904e11ffba3a9fe1097c7f5cfe9d6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170735"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management-preview"></a>Toegangs beheer delegeren voor toegang tot pakket beheerders in azure AD-recht-Management (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als u het maken en beheren van toegangs pakketten in een catalogus wilt delegeren, voegt u gebruikers toe aan de rol toegangs pakket beheer. Toegangs pakket beheerders moeten vertrouwd zijn met de behoeften van gebruikers om toegang te vragen tot bronnen in een catalogus. Als er bijvoorbeeld een catalogus voor een project wordt gebruikt, kan een project lead een Access Package Manager voor die catalogus zijn.  Toegangs pakket beheerders kunnen geen resources toevoegen aan een catalogus, maar ze kunnen de toegangs pakketten en het beleid in een catalogus beheren.  Bij het delegeren naar een Access package manager kan die persoon vervolgens verantwoordelijk zijn voor:

- Welke rollen een gebruiker heeft voor de resources in een catalogus
- Wie moet er toegang toe hebben
- Wie moet de toegangs aanvragen goed keuren?
- Hoe lang het duurt voordat het project wordt

In deze video vindt u een overzicht van de manier waarop u Access governance van de catalogus eigenaar kunt delegeren voor toegang tot pakket beheer.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Als een catalogus eigenaar, delegeren aan een toegangs pakket beheer

Volg deze stappen om een gebruiker toe te wijzen aan de rol toegangs pakket beheer:

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het menu links op **rollen en beheerders**.

    ![Catalogiseert rollen en beheerders](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klik op **toegangs pakket beheerders toevoegen** om de leden voor deze rollen te selecteren.

1. Klik op **selecteren** om deze leden toe te voegen.

## <a name="remove-an-access-package-manager"></a>Een Access Package Manager verwijderen

Volg deze stappen om een gebruiker te verwijderen uit de rol Access Package Manager:

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het menu links op **rollen en beheerders**.

1. Voeg een selectie vakje toe naast een Access package manager die u wilt verwijderen.

1. Klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- [Een nieuw toegangs pakket maken](entitlement-management-access-package-create.md)
