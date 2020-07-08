---
title: De toegang van een gebruiker tot een toepassing verwijderen | Microsoft Docs
description: Meer informatie over het verwijderen van de toegang van een gebruiker tot een toepassing
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/17/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f9626c256755e2fce81b593d95b8680f4bb55ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763156"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>De toegang van een gebruiker tot een toepassing verwijderen

In dit artikel wordt uitgelegd hoe u de toegang van een gebruiker tot een toepassing kunt verwijderen.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ik wil de toewijzing van een specifieke gebruiker of groep aan een toepassing verwijderen

Als u een gebruiker of groeps toewijzing wilt verwijderen uit een toepassing, volgt u de stappen in de [toewijzing een gebruiker of groep verwijderen uit een bedrijfs-app in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) -artikel.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ik wil alle toegang tot een toepassing uitschakelen voor elke gebruiker

Als u alle aanmeldingen van gebruikers voor een toepassing wilt uitschakelen, volgt u de stappen in het artikel [Gebruikers aanmeldingen voor een bedrijfs-app uitschakelen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) .

## <a name="i-want-to-delete-an-application-entirely"></a>Ik wil een toepassing volledig verwijderen

Voer de volgende instructies uit om **een toepassing te verwijderen**:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing die u wilt verwijderen.

7. Zodra de toepassing is geladen, klikt u op pictogram **verwijderen** in het **overzichts** venster van de bovenste toepassing.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ik wil alle toekomstige bewerkingen voor gebruikers toestemming voor elke toepassing uitschakelen

Door de toestemming van de gebruiker voor uw hele map uit te scha kelen, voor komt u dat eind gebruikers aan elke toepassing worden doorgestuurd. Beheerders kunnen namens de gebruiker nog steeds toestemming geven. Meer informatie over de toestemming van de toepassing en waarom u dit mogelijk niet wilt doen, kunt u lezen [over de toestemming van gebruikers en beheerders](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Zie ook [machtigingen en toestemming](../develop/v2-permissions-and-consent.md).

Volg de volgende instructies om **alle toekomstige bewerkingen van de gebruikers toestemming in uw hele directory uit te scha kelen**:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory extensie** 

3.  Klik in het navigatie menu op **bedrijfs toepassingen** .

5.  Klik op **gebruikers instellingen**.

6.  Stel de **gebruikers in staat om apps toegang tot Bedrijfs gegevens te geven in hun naam in-of uit** te scha **kelen en klik** op de knop Opslaan.


## <a name="next-steps"></a>Volgende stappen

[Toegang tot apps beheren](what-is-access-management.md)
