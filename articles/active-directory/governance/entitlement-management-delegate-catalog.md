---
title: Toegangsbeheer delegeren aan catalogusmakers in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het delegeren van toegangsbeheer van IT-beheerders aan catalogusmakers en projectmanagers, zodat ze de toegang zelf kunnen beheren.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120189"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Toegangsbeheer delegeren aan catalogusmakers in Azure AD-rechtenbeheer

Een catalogus is een container met resources en toegangspakketten. U maakt een catalogus wanneer u gerelateerde bronnen wilt groeperen en toegang wilt krijgen tot pakketten. Standaard kan een globale beheerder of een gebruikersbeheerder [een catalogus maken](entitlement-management-catalog-create.md)en kunnen extra gebruikers toevoegen als cataloguseigenaren.

Als u wilt delegeren aan gebruikers die geen beheerders zijn, zodat ze hun eigen catalogi kunnen maken, u deze gebruikers toevoegen aan de rol van Azure AD-rechtenbeheer. U afzonderlijke gebruikers toevoegen of een groep toevoegen, waarvan de leden vervolgens catalogi kunnen maken.  Nadat ze een catalogus hebben gemaakt, kunnen ze vervolgens resources die ze bezitten toevoegen aan hun catalogus.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Delegeren aan een catalogusmaker als IT-beheerder

Volg deze stappen om een gebruiker toe te wijzen aan de rol van de maker van de catalogus.

**Vereiste rol:** Globale beheerder of gebruikersbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu in de sectie **Beheer van rechten** op **Instellingen**.

1. Klik op **Bewerken**.

    ![Instellingen om catalogusmakers toe te voegen](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Klik in de sectie **Beheer van gemachtigden recht** op **Catalogusmakers toevoegen** om de gebruikers of groepen te selecteren waaraan u deze beheerrol voor rechten wilt delegeren.

1. Klik **op Selecteren**.

1. Klik op **Opslaan**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Gedelegeerde rollen toegang geven tot de Azure-portal

Als u gedelegeerde rollen, zoals catalogusmakers en toegangspakketbeheerders, toegang wilt geven tot de Azure-portal om toegangspakketten te beheren, moet u de instelling voor beheerportalen controleren.

**Vereiste rol:** Globale beheerder of gebruikersbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en klik vervolgens op **Gebruikers**.

1. Klik in het linkermenu op **Gebruikersinstellingen**.

1. Controleer of **De toegang tot azure AD-beheerportal** beperken is ingesteld op **Nee.**

    ![Azure AD-gebruikersinstellingen - Beheerportal](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Volgende stappen

- [Een catalogus met bronnen maken en beheren](entitlement-management-catalog-create.md)
- [Toegangsbeheer delegeren aan toegangspakketbeheerders](entitlement-management-delegate-managers.md)

