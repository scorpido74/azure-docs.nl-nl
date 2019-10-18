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
ms.openlocfilehash: fd0829afca05058892d3a0ceeb50c9955d792dc3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517057"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Een lijst met gebruikers (preview) downloaden in Azure Active Directory Portal

Azure Active Directory (Azure AD) biedt ondersteuning voor het importeren van bulk gebruikers (maken).

## <a name="required-permissions"></a>Vereiste machtigingen

Als u de lijst met gebruikers wilt downloaden vanuit het Azure AD-beheer centrum, moet u zijn aangemeld met een gebruiker die is toegewezen aan een of meer beheerders rollen op organisatie niveau in azure AD. Gast-uitnodiging en toepassings ontwikkelaar worden niet beschouwd als beheerders rollen.

## <a name="to-download-a-list-of-users"></a>Een lijst met gebruikers downloaden

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een Administrator-account van de gebruiker in de organisatie.
1. Selecteer in azure AD **gebruikers** > **gebruikers downloaden**.
1. Selecteer op de pagina **gebruikers downloaden** de optie **Start** om een CSV-bestand met gebruikers profiel eigenschappen te ontvangen. Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina resultaten van bulk bewerking. Het bestand bevat de reden voor elke fout.

   ![Selecteer waar u de lijst wilt opnemen met de gebruikers die u wilt downloaden](./media/users-bulk-download/bulk-download.png)

   Het Download bestand bevat de gefilterde lijst met gebruikers.

   De volgende gebruikers kenmerken worden opgenomen: 

   - userPrincipalName
   - displayName
   - surname
   - mail
   - GivenName
   - Id
   - User type
   - JobTitle
   - department
   - Manager
   - AccountEnabled
   - usageLocation
   - streetAddress
   - state
   - Regio
   - physicalDeliveryOfficeName
   - city
   - Code
   - TelephoneNumber
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
