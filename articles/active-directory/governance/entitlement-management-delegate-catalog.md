---
title: Delegeren toegang tot de catalogus makers in azure AD rechten beheer (preview)-Azure Active Directory
description: Meer informatie over het overdragen van het toegangs beheer van IT-beheerders naar het catalogiseren van makers en project managers, zodat ze de toegang zelf kunnen beheren.
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
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170761"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Toegangs beheer van de catalogus voor ontwikkel aars in azure AD-rechten delegeren (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als u wilt delegeren aan gebruikers die geen beheerder zijn, zodat ze hun eigen catalogi kunnen maken, kunt u deze gebruikers toevoegen aan de rol van Azure AD-bevoegdheids beheer-gedefinieerde catalogus Maker. U kunt afzonderlijke gebruikers toevoegen, maar u kunt ook een groep toevoegen, waarvan de leden vervolgens catalogi kunnen maken.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Als IT-beheerder delegeren aan een maker van de catalogus

Volg deze stappen om een gebruiker toe te wijzen aan de maker van de catalogus.

**Vereiste rol:** Globale beheerder of gebruikers beheerder

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links in het gedeelte **beheer rechten** op **instellingen**.

1. Klik op **Bewerken**.

    ![Instellingen voor het toevoegen van catalogus makers](./media/entitlement-management-delegate/settings-delegate.png)

1. Klik in de sectie bevoegdheids **beheer voor gemachtigden** op **catalogus makers toevoegen** om de gebruikers of groepen te selecteren aan wie u deze rechten wilt delegeren.

1. Klik op **Selecteren**.

1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- [Een catalogus met resources maken en beheren](entitlement-management-catalog-create.md)
- [Toegangs beheer delegeren voor toegang tot pakket beheerders](entitlement-management-delegate-managers.md)

