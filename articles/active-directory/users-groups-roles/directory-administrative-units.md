---
title: Beheer van beheer eenheden (preview)-Azure Active Directory | Microsoft Docs
description: Beheer eenheden gebruiken voor meer gedetailleerde delegering van machtigingen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336922"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Beheer van beheer eenheden in Azure Active Directory (preview-versie)

In dit artikel worden administratieve eenheden in Azure Active Directory (Azure AD) beschreven. Een beheer eenheid is een Azure AD-resource die een container kan zijn voor andere Azure AD-resources. In deze preview-versie kunnen deze resources alleen gebruikers zijn. Een beheerder van een gebruikers account met beheerders rechten kan bijvoorbeeld profiel gegevens bijwerken, wacht woorden opnieuw instellen en licenties voor gebruikers alleen in hun administratieve eenheid toewijzen.

U kunt beheer eenheden gebruiken om beheerders machtigingen te delegeren via subsets van gebruikers en beleids regels toe te passen op een subset van gebruikers. U kunt administratieve eenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om beleid op een gedetailleerd niveau in te stellen.

## <a name="deployment-scenario"></a>Implementatie scenario

Beheer eenheden kunnen nuttig zijn in organisaties met onafhankelijke divisies. Bekijk het voor beeld van een grote universiteit die bestaat uit een groot aantal zelfstandige scholen (school van bedrijf, school van engineering, enzovoort) die elk een eigen IT-beheerder heeft die de toegang beheert, gebruikers beheert en beleids regels voor hun school instellen. Een centrale beheerder kan een administratieve eenheid voor het school werk maken en deze vullen met alleen de studenten en mede werkers van de zakelijke school. De centrale beheerder kan de IT-afdeling van de zakelijke school toevoegen aan een scoped rol die beheerders machtigingen verleent voor alleen Azure AD-gebruikers in de beheer eenheid van de zakelijke school.

## <a name="license-requirements"></a>Licentievereisten

Voor het gebruik van beheer eenheden is een Azure Active Directory Premium licentie vereist voor elke beheerder van de beheer eenheid. Zie [aan de slag met Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)voor meer informatie.

## <a name="managing-administrative-units"></a>Beheer eenheden beheren

In deze preview-versie kunt u de enige manier voor het maken en beheren van beheer eenheden gebruikmaken van de Azure Active Directory-module voor Windows Power shell-cmdlets zoals beschreven in [werken met beheer eenheden](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Zie Azure Active (Engelstalig) voor meer informatie over software vereisten en het installeren van de Azure AD-module, en voor naslag informatie over de Azure AD-module-cmdlets voor het beheren van beheer eenheden, inclusief syntaxis, parameter beschrijvingen en voor beelden. [ Directory Power shell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Volgende stappen

[Azure Active Directory-edities](../fundamentals/active-directory-whatis.md)
