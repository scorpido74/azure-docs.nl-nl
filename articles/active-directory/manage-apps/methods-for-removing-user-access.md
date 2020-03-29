---
title: Toegang van een gebruiker tot een toepassing verwijderen | Microsoft Documenten
description: Begrijpen hoe u de toegang van een gebruiker tot een toepassing verwijderen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65826099"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>De toegang van een gebruiker tot een toepassing verwijderen

Met dit artikel u begrijpen hoe u de toegang van een gebruiker tot een toepassing verwijderen.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ik wil de toewijzing van een specifieke gebruiker of groep naar een toepassing verwijderen

Als u een gebruiker of groepstoewijzing wilt verwijderen naar een toepassing, voert u de stappen uit die worden vermeld in de [taak van een gebruiker of groep verwijderen uit een bedrijfsapp in het](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) Azure Active Directory-artikel.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ik wil alle toegang tot een toepassing voor elke gebruiker uitschakelen

Als u alle aanmeldingen van gebruikers bij een toepassing wilt uitschakelen, voert u de stappen uit die worden vermeld in de [aanmeldingen van gebruikers voor een bedrijfsapp uitschakelen in het](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) Azure Active Directory-artikel.

## <a name="i-want-to-delete-an-application-entirely"></a>Ik wil een toepassing volledig verwijderen

Volg de volgende instructies om een toepassing te **verwijderen:**

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. Klik **op Bedrijfstoepassingen** in het navigatiemenu voor de linkernavigatie van Azure Active Directory.

5. Klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt verwijderen.

7. Zodra de toepassing is geladen, klikt u op Pictogram **Verwijderen** in het **deelvenster Overzicht** van de bovenste toepassing.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ik wil alle toekomstige bewerkingen voor gebruikerstoestemming voor elke toepassing uitschakelen

Het uitschakelen van de toestemming van de gebruiker voor uw hele map voorkomt dat eindgebruikers toestemming geven voor een toepassing. Beheerders kunnen nog steeds toestemming geven namens de gebruiker. Lees [Toestemming van gebruiker en beheerder](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)begrijpen voor meer informatie over toestemming voor toepassingen en waarom u dit wel of niet wilt doen. Zie ook, [Machtigingen en toestemming](../develop/v2-permissions-and-consent.md).

Volg de volgende instructies om **alle toekomstige bewerkingen voor toestemming van gebruikers in uw hele map uit**te schakelen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  De **Azure Active Directory-extensie openen** 

3.  Klik op **Enterprise-toepassingen** in het navigatiemenu.

5.  Klik **op Gebruikersinstellingen**.

6.  Als u de **gebruikers instelt, kunnen apps namens hen toegang krijgen tot bedrijfsgegevens** en op De knop Opslaan klikken. **No**


## <a name="next-steps"></a>Volgende stappen

[Toegang tot apps beheren](what-is-access-management.md)
