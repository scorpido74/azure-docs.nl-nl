---
title: Kenmerken van de interactie tussen meerdere tenants-Azure AD | Microsoft Docs
description: Uw Azure Active Tenant-tenants beheren door te zien wat uw tenants zijn als volledig onafhankelijke resources
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562255"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Meer informatie over hoe meerdere Azure Active Directory tenants werken

In Azure Active Directory (Azure AD) is elke Tenant een volledig onafhankelijke resource: een peer die logisch onafhankelijk is van de andere tenants die u beheert. Er is geen bovenliggende/onderliggende relatie tussen tenants. Deze onafhankelijkheid tussen tenants omvat bron onafhankelijkheid, administratieve onafhankelijkheid en synchronisatie onafhankelijkheid.

## <a name="resource-independence"></a>Resource onafhankelijkheid
* Als u een resource in een Tenant maakt of verwijdert, heeft dit geen invloed op resources in een andere Tenant, met een gedeeltelijke uitzonde ring van externe gebruikers. 
* Als u een van uw domein namen met één Tenant gebruikt, kan deze niet worden gebruikt met een andere Tenant.

## <a name="administrative-independence"></a>Beheer onafhankelijkheid
Als een gebruiker die geen beheerder is van de Tenant contoso, een test Tenant maken, dan:

* De gebruiker die een Tenant maakt, wordt standaard toegevoegd als een externe gebruiker in die nieuwe Tenant en heeft de rol van globale beheerder toegewezen in die Tenant.
* De Administrators van de Tenant contoso hebben geen directe beheerders bevoegdheden voor de test van de Tenant, tenzij een beheerder van ' test ' hen deze bevoegdheden verleent. Beheerders van ' Contoso ' kunnen echter de toegang tot de Tenant testen door te controleren of de gebruikers account die ' test ' heeft gemaakt.
* Als u een beheerdersrol voor een gebruiker toevoegt aan of verwijdert uit een Tenant, is de wijziging niet van invloed op de beheerders rollen die de gebruiker in een andere Tenant heeft.

## <a name="synchronization-independence"></a>Synchronisatie onafhankelijkheid
U kunt elke Azure AD-Tenant onafhankelijk configureren om gegevens op te halen die zijn gesynchroniseerd vanuit één exemplaar van ofwel:

* Het hulp programma Azure AD Connect om gegevens te synchroniseren met één AD-forest.
* De Azure Active Tenant connector voor Forefront Identity Manager, om gegevens te synchroniseren met een of meer on-premises forests en/of niet-Azure AD-gegevens bronnen.

## <a name="add-an-azure-ad-tenant"></a>Een Azure AD-Tenant toevoegen
Als u een Azure AD-Tenant wilt toevoegen aan de Azure Portal, meldt u zich aan bij [de Azure Portal](https://portal.azure.com) met een account dat een globale Azure AD-beheerder is en selecteert u op de linkerkant **Nieuw**.

> [!NOTE]
> In tegens telling tot andere Azure-resources zijn uw tenants geen onderliggende resources van een Azure-abonnement. Als uw Azure-abonnement is geannuleerd of verlopen, hebt u nog steeds toegang tot uw Tenant gegevens met behulp van Azure PowerShell, de Microsoft Graph-API of het Microsoft 365 beheer centrum. U kunt ook [een ander abonnement koppelen aan de Tenant](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Volgende stappen
Zie [Wat is Azure Active Tenant Licensing?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)voor een uitgebreid overzicht van Azure AD-licentie problemen en aanbevolen procedures.
