---
title: Naslag informatie over SAP SuccessFactors-kenmerken
description: Meer informatie over de kenmerken van SuccessFactors die worden ondersteund door SuccessFactors-HR ingericht inrichten
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 12/06/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 25541b76dda55db1ec26f4d8e3ec63573a47e7b1
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781545"
---
# <a name="sap-successfactors-attribute-reference"></a>Naslag informatie over SAP SuccessFactors-kenmerken

In dit artikel vindt u informatie over:

- [Ondersteunde SuccessFactors-entiteiten en-kenmerken](#supported-successfactors-entities-and-attributes)
- [Toewijzing van standaard kenmerk](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Ondersteunde SuccessFactors-entiteiten en-kenmerken

In de onderstaande tabel wordt de lijst met SuccessFactors-kenmerken die worden ondersteund door de volgende twee Provisioning-apps vastgelegd:

- [SuccessFactors Active Directory gebruikers inrichten](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors naar Azure AD-gebruikers inrichten](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)


| \# | SuccessFactors entiteit                  | SuccessFactors-kenmerk     | Bewerkings type |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Raadplegen           |
| 2  | PerPerson                              | personId                     | Raadplegen           |
| 3  | PerPerson                              | perPersonUuid                | Raadplegen           |
| 4  | PerPersonal                            | displayName                  | Raadplegen           |
| 5  | PerPersonal                            | voornaam                    | Raadplegen           |
| 6  | PerPersonal                            | geslacht                       | Raadplegen           |
| 7  | PerPersonal                            | achternaam                     | Raadplegen           |
| 8  | PerPersonal                            | middelste naam                   | Raadplegen           |
| 9  | PerPersonal                            | voorkeursnaam                | Raadplegen           |
| 10 | Gebruiker                                   | addressLine1                 | Raadplegen           |
| 11 | Gebruiker                                   | addressLine2                 | Raadplegen           |
| 12 | Gebruiker                                   | addressLIne3                 | Raadplegen           |
| 13 | Gebruiker                                   | Kaart Businessphone                | Raadplegen           |
| 14 | Gebruiker                                   | cellPhone                    | Raadplegen           |
| 15 | Gebruiker                                   | city                         | Raadplegen           |
| 16 | Gebruiker                                   | country                      | Raadplegen           |
| 17 | Gebruiker                                   | custom01                     | Raadplegen           |
| 18 | Gebruiker                                   | custom02                     | Raadplegen           |
| 19 | Gebruiker                                   | custom03                     | Raadplegen           |
| 20 | Gebruiker                                   | custom04                     | Raadplegen           |
| 21 | Gebruiker                                   | custom05                     | Raadplegen           |
| 22 | Gebruiker                                   | custom06                     | Raadplegen           |
| 23 | Gebruiker                                   | custom07                     | Raadplegen           |
| 24 | Gebruiker                                   | custom08                     | Raadplegen           |
| 25 | Gebruiker                                   | custom09                     | Raadplegen           |
| 26 | Gebruiker                                   | custom10                     | Raadplegen           |
| 27 | Gebruiker                                   | custom11                     | Raadplegen           |
| 28 | Gebruiker                                   | custom12                     | Raadplegen           |
| 29 | Gebruiker                                   | custom13                     | Raadplegen           |
| 30 | Gebruiker                                   | custom14                     | Raadplegen           |
| 31 | Gebruiker                                   | empId                        | Raadplegen           |
| 32 | Gebruiker                                   | homePhone                    | Raadplegen           |
| 33 | Gebruiker                                   | jobFamily                    | Raadplegen           |
| 34 | Gebruiker                                   | naam                     | Raadplegen           |
| 35 | Gebruiker                                   | state                        | Raadplegen           |
| 36 | Gebruiker                                   | timeZone                     | Raadplegen           |
| 37 | Gebruiker                                   | gebruikersnaam                     | Raadplegen           |
| 38 | Gebruiker                                   | zipCode                      | Raadplegen           |
| 39 | PerPhone                               | Nummer                     | Raadplegen           |
| 40 | PerPhone                               | countryCode                  | Raadplegen           |
| 41 | PerPhone                               | switch                    | Raadplegen           |
| 42 | PerPhone                               | phoneNumber                  | Raadplegen           |
| 43 | PerPhone                               | phoneType                    | Raadplegen           |
| 44 | PerEmail                               | emailAddress                 | Lezen, schrijven    |
| 45 | PerEmail                               | emailType                    | Raadplegen           |
| 46 | EmpEmployment                          | firstDateWorked              | Raadplegen           |
| 47 | EmpEmployment                          | lastDateWorked               | Raadplegen           |
| 48 | EmpEmployment                          | userId                       | Raadplegen           |
| 49 | EmpEmployment                          | isContingentWorker           | Raadplegen           |
| 50 | EmpJob                                 | countryOfCompany             | Raadplegen           |
| 51 | EmpJob                                 | emplStatus                   | Raadplegen           |
| 52 | EmpJob                                 | endDate                      | Raadplegen           |
| 53 | EmpJob                                 | Begin                    | Raadplegen           |
| 54 | EmpJob                                 | jobTitle                     | Raadplegen           |
| 55 | EmpJob                                 | position                     | Raadplegen           |
| 65 | EmpJob                                 | customString13               | Raadplegen           |
| 56 | EmpJob                                 | managerId                    | Raadplegen           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Raadplegen           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Raadplegen           |
| 59 | \.Bedrijf EmpJob                        | bedrijf                      | Raadplegen           |
| 60 | \.Bedrijf EmpJob                        | companyId                    | Raadplegen           |
| 61 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode           | Raadplegen           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Raadplegen           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Raadplegen           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Raadplegen           |
| 65 | EmpJob \. afdeling                     | department                   | Raadplegen           |
| 66 | EmpJob \. afdeling                     | departmentId                 | Raadplegen           |
| 67 | EmpJob- \. deling                       | daarvan                     | Raadplegen           |
| 68 | EmpJob- \. deling                       | divisionId                   | Raadplegen           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Raadplegen           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Raadplegen           |
| 71 | Locatie van EmpJob \.                       | Locatie                 | Raadplegen           |
| 72 | Locatie van EmpJob \.                       | officeLocationAddress        | Raadplegen           |
| 73 | Locatie van EmpJob \.                       | officeLocationCity           | Raadplegen           |
| 74 | Locatie van EmpJob \.                       | officeLocationCustomString4  | Raadplegen           |
| 75 | Locatie van EmpJob \.                       | officeLocationZipCode        | Raadplegen           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Raadplegen           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Raadplegen           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Raadplegen           |

## <a name="default-attribute-mapping"></a>Toewijzing van standaard kenmerk

De volgende tabel bevat de standaard kenmerk toewijzing tussen de hierboven vermelde SuccessFactors-kenmerken en AD/Azure AD-kenmerken. In de Blade ' toewijzing ' van Azure AD-inrichtings toepassing kunt u deze standaard toewijzing wijzigen zodat deze kenmerken bevat uit de bovenstaande lijst. 

| \# | SuccessFactors entiteit                  | SuccessFactors-kenmerk | Standaard AD/Azure AD-kenmerk toewijzing   | Verwerkings Opmerking                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Gebruikt als overeenkomend kenmerk                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Niet toegewezen \- als bron anker\] | Tijdens de eerste synchronisatie koppelt de inrichtings service de personUuid aan bestaande objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | voornaam                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | achternaam                 | sn                                      | NA                                                                                           |
| 6  | Gebruiker                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | Gebruiker                                   | city                     | l                                       | NA                                                                                           |
| 8  | Gebruiker                                   | country                  | collega's                                      | NA                                                                                           |
| 9  | Gebruiker                                   | state                    | st                                      | NA                                                                                           |
| 10 | Gebruiker                                   | gebruikersnaam                 | samAccountName                          | NA                                                                                           |
| 11 | Gebruiker                                   | zipCode                  | Code                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | titel                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob \. afdeling                     | department               | department                              | NA                                                                                           |
| 17 | EmpJob- \. deling                       | daarvan                 | bedrijf                                 | NA                                                                                           |
| 18 | Locatie van EmpJob \.                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | Locatie van EmpJob \.                       | officeLocationZipCode    | Code                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Als activeEmploymentsCount = 0, schakelt u de account\. uit                                           |
