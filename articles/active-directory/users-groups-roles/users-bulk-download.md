---
title: Down load een lijst met gebruikers (preview) in de Azure Active Directory-Portal | Microsoft Docs
description: Down load gebruikers records bulksgewijs in het Azure-beheer centrum in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d106e803fdef4f054b7e4717ba59a43359d1d68a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720300"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Een lijst met gebruikers (preview) downloaden in Azure Active Directory Portal

Azure Active Directory (Azure AD) biedt ondersteuning voor het importeren van bulk gebruikers (maken).

## <a name="required-permissions"></a>Vereiste machtigingen

Als u de lijst met gebruikers wilt downloaden vanuit het Azure AD-beheer centrum, moet u zijn aangemeld met een gebruiker die is toegewezen aan een of meer beheerders rollen op organisatie niveau in azure AD. Gast-uitnodiging en toepassings ontwikkelaar worden niet beschouwd als beheerders rollen.

## <a name="to-download-a-list-of-users"></a>Een lijst met gebruikers downloaden

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een Administrator-account van de gebruiker in de organisatie.
2. Navigeer naar Azure Active Directory > gebruikers. Selecteer vervolgens de gebruikers die u wilt opnemen in de down load door het vak in de linkerkolom naast elke gebruiker te tikken. Opmerking: op dit moment is het niet mogelijk om alle gebruikers te selecteren die u wilt exporteren. Elk item moet afzonderlijk worden geselecteerd.
3. Selecteer in azure AD **gebruikers** > **gebruikers te downloaden**.
4. Selecteer op de pagina **gebruikers downloaden** de optie **Start** om een CSV-bestand met gebruikers profiel eigenschappen te ontvangen. Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina resultaten van bulk bewerking. Het bestand bevat de reden voor elke fout.

   ![Selecteer waar u de lijst wilt opnemen met de gebruikers die u wilt downloaden](./media/users-bulk-download/bulk-download.png)

   Het Download bestand bevat de gefilterde lijst met gebruikers.

   De volgende gebruikers kenmerken worden opgenomen: 

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - Id
   - User type
   - jobTitle
   - department
   - Manager
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - regio
   - physicalDeliveryOfficeName
   - city
   - Code
   - telephoneNumber
   - provider
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Status controleren

U kunt de status van uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Controleer de upload status op de pagina resultaten van bulk bewerking](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Service limieten bulksgewijs downloaden

Elke bulk activiteit voor het maken van een lijst met gebruikers kan Maxi maal één uur worden uitgevoerd. Hiermee kunt u een lijst van ten minste 500.000 gebruikers maken en downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs toevoegen](users-bulk-add.md)
- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
