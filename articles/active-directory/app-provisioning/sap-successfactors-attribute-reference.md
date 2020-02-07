---
title: Naslag informatie over SAP SuccessFactors-kenmerken | Microsoft Docs
description: Meer informatie over de kenmerken van SuccessFactors die worden ondersteund door SuccessFactors-HR ingericht inrichten
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066185"
---
# <a name="sap-successfactors-attribute-reference"></a>Naslag informatie over SAP SuccessFactors-kenmerken

## <a name="supported-successfactors-entities-and-attributes"></a>Ondersteunde SuccessFactors-entiteiten en-kenmerken

In de onderstaande tabel wordt de lijst met SuccessFactors-kenmerken die worden ondersteund door de volgende twee Provisioning-apps vastgelegd: 
* [SuccessFactors Active Directory gebruikers inrichten](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors naar Azure AD-gebruikers inrichten](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors entiteit                  | SuccessFactors-kenmerk     | Bewerkings type |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Lezen           |
| 2  | PerPerson                              | personId                     | Lezen           |
| 3  | PerPerson                              | perPersonUuid                | Lezen           |
| 4  | PerPersonal                            | displayName                  | Lezen           |
| 5  | PerPersonal                            | voornaam                    | Lezen           |
| 6  | PerPersonal                            | geslacht                       | Lezen           |
| 7  | PerPersonal                            | achternaam                     | Lezen           |
| 8  | PerPersonal                            | Middelste naam                   | Lezen           |
| 9  | PerPersonal                            | voorkeursnaam                | Lezen           |
| 10 | Gebruiker                                   | addressLine1                 | Lezen           |
| 11 | Gebruiker                                   | addressLine2                 | Lezen           |
| 12 | Gebruiker                                   | addressLIne3                 | Lezen           |
| 13 | Gebruiker                                   | Kaart Businessphone                | Lezen           |
| 14 | Gebruiker                                   | cellPhone                    | Lezen           |
| 15 | Gebruiker                                   | city                         | Lezen           |
| 16 | Gebruiker                                   | regio                      | Lezen           |
| 17 | Gebruiker                                   | custom01                     | Lezen           |
| 18 | Gebruiker                                   | custom02                     | Lezen           |
| 19 | Gebruiker                                   | custom03                     | Lezen           |
| 20 | Gebruiker                                   | custom04                     | Lezen           |
| 21 | Gebruiker                                   | custom05                     | Lezen           |
| 22 | Gebruiker                                   | custom06                     | Lezen           |
| 23 | Gebruiker                                   | custom07                     | Lezen           |
| 24 | Gebruiker                                   | custom08                     | Lezen           |
| 25 | Gebruiker                                   | custom09                     | Lezen           |
| 26 | Gebruiker                                   | custom10                     | Lezen           |
| 27 | Gebruiker                                   | custom11                     | Lezen           |
| 28 | Gebruiker                                   | custom12                     | Lezen           |
| 29 | Gebruiker                                   | custom13                     | Lezen           |
| 30 | Gebruiker                                   | custom14                     | Lezen           |
| 31 | Gebruiker                                   | empId                        | Lezen           |
| 32 | Gebruiker                                   | homePhone                    | Lezen           |
| 33 | Gebruiker                                   | jobFamily                    | Lezen           |
| 34 | Gebruiker                                   | naam                     | Lezen           |
| 35 | Gebruiker                                   | state                        | Lezen           |
| 36 | Gebruiker                                   | timeZone                     | Lezen           |
| 37 | Gebruiker                                   | gebruikersnaam                     | Lezen           |
| 38 | Gebruiker                                   | zipCode                      | Lezen           |
| 39 | PerPhone                               | Nummer                     | Lezen           |
| 40 | PerPhone                               | countryCode                  | Lezen           |
| 41 | PerPhone                               | switch                    | Lezen           |
| 42 | PerPhone                               | phoneNumber                  | Lezen           |
| 43 | PerPhone                               | phoneType                    | Lezen           |
| 44 | PerEmail                               | emailAddress                 | Lezen, schrijven    |
| 45 | PerEmail                               | emailType                    | Lezen           |
| 46 | EmpEmployment                          | firstDateWorked              | Lezen           |
| 47 | EmpEmployment                          | lastDateWorked               | Lezen           |
| 48 | EmpEmployment                          | userId                       | Lezen           |
| 49 | EmpEmployment                          | isContingentWorker           | Lezen           |
| 50 | EmpJob                                 | countryOfCompany             | Lezen           |
| 51 | EmpJob                                 | emplStatus                   | Lezen           |
| 52 | EmpJob                                 | endDate                      | Lezen           |
| 53 | EmpJob                                 | Begin                    | Lezen           |
| 54 | EmpJob                                 | Functie                     | Lezen           |
| 55 | EmpJob                                 | Positie                     | Lezen           |
| 65 | EmpJob                                 | customString13               | Lezen           |
| 56 | EmpJob                                 | managerId                    | Lezen           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Lezen           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Lezen           |
| 59 | EmpJob\.bedrijf                        | bedrijf                      | Lezen           |
| 60 | EmpJob\.bedrijf                        | companyId                    | Lezen           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | Lezen           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Lezen           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Lezen           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Lezen           |
| 65 | EmpJob\.afdeling                     | department                   | Lezen           |
| 66 | EmpJob\.afdeling                     | DepartmentId                 | Lezen           |
| 67 | EmpJob\.-deling                       | daarvan                     | Lezen           |
| 68 | EmpJob\.-deling                       | divisionId                   | Lezen           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Lezen           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Lezen           |
| 71 | \.locatie van EmpJob                       | Locatie                 | Lezen           |
| 72 | \.locatie van EmpJob                       | officeLocationAddress        | Lezen           |
| 73 | \.locatie van EmpJob                       | officeLocationCity           | Lezen           |
| 74 | \.locatie van EmpJob                       | officeLocationCustomString4  | Lezen           |
| 75 | \.locatie van EmpJob                       | officeLocationZipCode        | Lezen           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Lezen           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Lezen           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Lezen           |


## <a name="default-attribute-mapping"></a>Toewijzing van standaard kenmerk

De volgende tabel bevat de standaard kenmerk toewijzing tussen de hierboven vermelde SuccessFactors-kenmerken en AD/Azure AD-kenmerken. In de Blade ' toewijzing ' van Azure AD-inrichtings toepassing kunt u deze standaard toewijzing wijzigen zodat deze kenmerken bevat uit de bovenstaande lijst. 

| \# | SuccessFactors entiteit                  | SuccessFactors-kenmerk | Standaard AD/Azure AD-kenmerk toewijzing   | Verwerkings Opmerking                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Gebruikt als overeenkomend kenmerk                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[niet toegewezen \- gebruikt als bron anker\] | Tijdens de eerste synchronisatie koppelt de inrichtings service de personUuid aan bestaande objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | N.v.t.                                                                                           |
| 4  | PerPersonal                            | voornaam                | givenName                               | N.v.t.                                                                                           |
| 5  | PerPersonal                            | achternaam                 | SN                                      | N.v.t.                                                                                           |
| 6  | Gebruiker                                   | addressLine1             | streetAddress                           | N.v.t.                                                                                           |
| 7  | Gebruiker                                   | city                     | l                                       | N.v.t.                                                                                           |
| 8  | Gebruiker                                   | regio                  | CO                                      | N.v.t.                                                                                           |
| 9  | Gebruiker                                   | state                    | St                                      | N.v.t.                                                                                           |
| 10 | Gebruiker                                   | gebruikersnaam                 | samAccountName                          | N.v.t.                                                                                           |
| 11 | Gebruiker                                   | zipCode                  | Postcode                              | N.v.t.                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | N.v.t.                                                                                           |
| 13 | EmpJob                                 | Functie                 | titel                                   | N.v.t.                                                                                           |
| 14 | EmpJob                                 | managerId                | Manager                                 | N.v.t.                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | N.v.t.                                                                                           |
| 16 | EmpJob\.afdeling                     | department               | department                              | N.v.t.                                                                                           |
| 17 | EmpJob\.-deling                       | daarvan                 | bedrijf                                 | N.v.t.                                                                                           |
| 18 | \.locatie van EmpJob                       | officeLocationAddress    | streetAddress                           | N.v.t.                                                                                           |
| 19 | \.locatie van EmpJob                       | officeLocationZipCode    | Postcode                              | N.v.t.                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Als activeEmploymentsCount = 0, schakelt u de account\. uit                                           |

