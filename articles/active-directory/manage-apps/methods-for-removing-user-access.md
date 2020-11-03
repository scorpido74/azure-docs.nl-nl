---
title: De toegang van een gebruiker tot een toepassing in Azure Active Directory verwijderen
description: Meer informatie over het verwijderen van de toegang van een gebruiker tot een toepassing in Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 8544c35d77dfc32ece9b21a602f301ab15c22439
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288320"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>De toegang van een gebruiker tot een toepassing verwijderen

In dit artikel wordt uitgelegd hoe u de toegang van een gebruiker tot een toepassing kunt verwijderen.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ik wil de toewijzing van een specifieke gebruiker of groep aan een toepassing verwijderen

Als u een gebruiker of groeps toewijzing wilt verwijderen uit een toepassing, volgt u de stappen in de [toewijzing een gebruiker of groep verwijderen uit een bedrijfs-app in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) -artikel.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ik wil alle toegang tot een toepassing uitschakelen voor elke gebruiker

Als u alle aanmeldingen van gebruikers voor een toepassing wilt uitschakelen, volgt u de stappen in het artikel [Gebruikers aanmeldingen voor een bedrijfs-app uitschakelen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) .

## <a name="i-want-to-delete-an-application-entirely"></a>Ik wil een toepassing volledig verwijderen

De [Quick Start Series op toepassings beheer](delete-application-portal.md) bevat richt lijnen voor het verwijderen van een toepassing uit uw Azure Active Directory-Tenant.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ik wil alle toekomstige bewerkingen voor gebruikers toestemming voor elke toepassing uitschakelen

Door de toestemming van de gebruiker voor uw hele map uit te scha kelen, voor komt u dat eind gebruikers aan elke toepassing worden doorgestuurd. Beheerders kunnen namens de gebruiker nog steeds toestemming geven. Meer informatie over de toestemming van de toepassing en waarom u dit mogelijk niet wilt doen, kunt u lezen [over de toestemming van gebruikers en beheerders](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Zie ook [machtigingen en toestemming](../develop/v2-permissions-and-consent.md).

Volg de volgende instructies om **alle toekomstige bewerkingen van de gebruikers toestemming in uw hele directory uit te scha kelen** :

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory extensie** 

3.  Klik in het navigatie menu op **bedrijfs toepassingen** .

5.  Klik op **gebruikers instellingen**.

6.  Stel de **gebruikers in staat om apps toegang tot Bedrijfs gegevens te geven in hun naam in-of uit** te scha **kelen en klik** op de knop Opslaan.


## <a name="next-steps"></a>Volgende stappen

[Toegang tot apps beheren](what-is-access-management.md)
