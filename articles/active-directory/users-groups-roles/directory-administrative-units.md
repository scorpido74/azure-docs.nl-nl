---
title: Beheer van beheer van beheereenheden (voorbeeld) - Azure AD | Microsoft Documenten
description: Beheereenheden gebruiken voor meer gedetailleerde overdracht van machtigingen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028412"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Beheer van beheer van beheereenheden in Azure Active Directory (voorbeeld)

In dit artikel worden beheereenheden beschreven in Azure Active Directory (Azure AD). Een administratieve eenheid is een Azure AD-bron die een container kan zijn voor andere Azure AD-resources. In deze preview-release kunnen deze bronnen alleen gebruikers zijn. Een beheerder van een gebruikersaccount met een beheereenheid kan bijvoorbeeld profielgegevens bijwerken, wachtwoorden opnieuw instellen en licenties toewijzen voor gebruikers alleen in hun beheereenheid.

U administratieve eenheden gebruiken om beheerdersmachtigingen te delegeren voor subsets van gebruikers en beleid toe te passen op een subset van gebruikers. U beheereenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om beleid op een gedetailleerd niveau in te stellen.

## <a name="deployment-scenario"></a>Implementatiescenario

Administratieve eenheden kunnen nuttig zijn in organisaties met onafhankelijke afdelingen. Denk aan het voorbeeld van een grote universiteit die bestaat uit veel autonome scholen (School of Business, School of Engineering, enzovoort) die elk hun eigen IT-beheerders hebben die de toegang beheren, gebruikers beheren en beleid instellen voor hun school. Een centrale beheerder kan een administratieve eenheid voor de School of Business maken en deze vullen met alleen de business schoolstudenten en -medewerkers. Vervolgens kan de centrale beheerder het IT-personeel van de Business school toevoegen aan een scoped-rol die beheerdersmachtigingen verleent voor alleen Azure AD-gebruikers in de beheereenheid van de business school.

## <a name="license-requirements"></a>Licentievereisten

Voor het gebruik van administratieve eenheden is een Azure Active Directory Premium-licentie vereist voor elke beheerder van de beheereenheid. Zie [Aan de slag met Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)voor meer informatie.

## <a name="managing-administrative-units"></a>Administratieve eenheden beheren

In deze preview-versie u alleen de Azure Active Directory Module voor Windows PowerShell-cmdlets gebruiken zoals beschreven in [Werken met administratieve eenheden](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Zie [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)voor meer informatie over softwarevereisten en het installeren van de Azure AD-module en voor referentiegegevens over de cmdlets van de Azure AD-module voor het beheren van beheereenheden, waaronder syntaxis, parameterbeschrijvingen en voorbeelden.

## <a name="next-steps"></a>Volgende stappen

[Azure Active Directory-edities](../fundamentals/active-directory-whatis.md)
