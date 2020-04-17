---
title: Een lijst met gebruikers downloaden in de Azure Active Directory-portal | Microsoft Documenten
description: Download gebruikersrecords in bulk in het Azure-beheercentrum in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532634"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Een lijst met gebruikers downloaden in Azure Active Directory-portal

Azure Active Directory (Azure AD) ondersteunt bewerkingen voor het importeren van bulkgebruikers (maken).

## <a name="required-permissions"></a>Vereiste machtigingen

Als u de lijst met gebruikers wilt downloaden uit het Azure AD-beheercentrum, moet u zijn aangemeld met een gebruiker die is toegewezen aan een of meer beheerdersrollen op organisatieniveau in Azure AD (gebruikersbeheerder is de vereiste minimale rol). Gastgenodigden en toepassingsontwikkelaars worden niet beschouwd als beheerdersrollen.

## <a name="to-download-a-list-of-users"></a>Een lijst met gebruikers downloaden

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een gebruikersbeheerdersaccount in de organisatie.
2. Navigeer naar Azure Active Directory > Gebruikers. Selecteer vervolgens de gebruikers die u in de download wilt opnemen door het vakje in de linkerkolom naast elke gebruiker aan te vinken. Opmerking: Op dit moment is er geen manier om alle gebruikers te selecteren voor export. Elk moet individueel worden geselecteerd.
3. Selecteer in Azure AD **gebruikers** > **downloaden**.
4. Selecteer op de pagina **Gebruikers downloaden** de optie **Start** om de eigenschappen van het CSV-bestand met gebruikersprofiel te ontvangen. Als er fouten zijn, u het resultatenbestand downloaden en bekijken op de pagina Bulk-bewerkingsresultaten. Het bestand bevat de reden voor elke fout.

   ![Selecteer waar u de lijst wilt hebben die u wilt downloaden](./media/users-bulk-download/bulk-download.png)

   Het downloadbestand bevat de gefilterde lijst met gebruikers.

   De volgende gebruikerskenmerken zijn opgenomen:

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitel
   - department
   - accountIngeschakeld
   - gebruikLocatie
   - streetAddress
   - state
   - land
   - physicalDeliveryOfficeName
   - city
   - Postcode
   - telefoonNummer
   - mobiel
   - verificatiePhoneNummer
   - verificatieAlternativePhoneNumber
   - verificatieE-mail
   - alternatiefEmailAdres
   - ageGroup
   - toestemmingProvidedForMinor
   - legalAgeGroupClassificatie

## <a name="check-status"></a>Status controleren

U de status van uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk-bewerking.**

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limieten voor bulkdownloadservice

Elke bulkactiviteit om een lijst met gebruikers te maken, kan maximaal een uur duren. Dit maakt het mogelijk om een lijst van ten minste 500.000 gebruikers aan te maken en te downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Bulkgebruikers toevoegen](users-bulk-add.md)
- [Bulk verwijderen gebruikers](users-bulk-delete.md)
- [Bulkherstelgebruikers](users-bulk-restore.md)
