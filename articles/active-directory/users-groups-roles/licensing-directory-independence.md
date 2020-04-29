---
title: Kenmerken van de interactie tussen meerdere tenants-Azure AD | Microsoft Docs
description: Meer informatie over uw Azure Active Directory-tenants als volledig onafhankelijke organisaties
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878116"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Meer informatie over de interactie tussen meerdere Azure Active Directory organisaties

In Azure Active Directory (Azure AD) is elke Tenant een volledig onafhankelijke organisatie: een peer die logisch onafhankelijk is van de andere Azure AD-organisaties die u beheert. Deze onafhankelijkheid tussen organisaties omvat bron onafhankelijkheid, administratieve onafhankelijkheid en synchronisatie onafhankelijkheid. Er is geen bovenliggende/onderliggende relatie tussen organisaties.

## <a name="resource-independence"></a>Resource onafhankelijkheid

* Als u een Azure AD-resource in een organisatie maakt of verwijdert, heeft dit geen invloed op resources in een andere organisatie, met een gedeeltelijke uitzonde ring van externe gebruikers.
* Als u een van uw domein namen registreert met één organisatie, kan deze niet worden gebruikt door een andere organisatie.

## <a name="administrative-independence"></a>Beheer onafhankelijkheid

Als een niet-administratieve gebruiker van de organisatie Contoso een test organisatie ' test ' maakt, dan:

* De gebruiker die een organisatie maakt, wordt standaard toegevoegd als een externe gebruiker in die nieuwe organisatie en heeft de rol van globale beheerder toegewezen in die organisatie.
* De Administrators van de organisatie ' Contoso ' hebben geen directe beheerders bevoegdheden voor de test van de organisatie, tenzij een beheerder van ' test ' hen deze bevoegdheden verleent. Beheerders van ' Contoso ' kunnen de toegang tot de organisatie ' test ' echter beheren als ze de gebruikers account beheren die ' test ' heeft gemaakt.
* Als u een Azure AD-rol toevoegt of verwijdert voor een gebruiker in de ene organisatie, heeft de wijziging geen invloed op de rollen die de gebruiker in een andere Azure AD-organisatie is toegewezen.

## <a name="synchronization-independence"></a>Synchronisatie onafhankelijkheid

U kunt elke Azure AD-organisatie onafhankelijk van elkaar configureren om gegevens op te halen uit één exemplaar van ofwel:

* Het hulp programma Azure AD Connect om gegevens te synchroniseren met één AD-forest.
* De Azure Active Directory-Connector voor Forefront Identity Manager, om gegevens te synchroniseren met een of meer on-premises forests en/of niet-Azure AD-gegevens bronnen.

## <a name="add-an-azure-ad-organization"></a>Een Azure AD-organisatie toevoegen

Als u een Azure AD-organisatie wilt toevoegen aan de Azure Portal, meldt u zich aan bij [de Azure Portal](https://portal.azure.com) met een account dat een globale Azure AD-beheerder is en selecteert u **Nieuw**.

> [!NOTE]
> In tegens telling tot andere Azure-resources zijn uw Azure AD-organisaties geen onderliggende resources van een Azure-abonnement. Als uw Azure-abonnement is geannuleerd of verlopen, hebt u nog steeds toegang tot de gegevens van uw Azure AD-organisatie met behulp van Azure PowerShell, de Microsoft Graph-API of het Microsoft 365-beheer centrum. U kunt ook [een ander abonnement koppelen aan de organisatie](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Active Directory Licensing?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)voor meer informatie over Azure AD-licenties en aanbevolen procedures.
