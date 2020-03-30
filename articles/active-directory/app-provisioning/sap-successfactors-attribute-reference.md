---
title: Referentie voor kenmerk kenmerken van SAP-succesfactoren | Microsoft Documenten
description: Ontdek welke kenmerken van SuccessFactors worden ondersteund door SuccessFactors-HR gedreven inrichting
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522353"
---
# <a name="sap-successfactors-attribute-reference"></a>Naslaginformatie over kenmerk sap-succesfactoren

## <a name="supported-successfactors-entities-and-attributes"></a>Ondersteunde entiteiten en kenmerken van succesfactoren

De onderstaande tabel bevat de lijst met kenmerken van SuccessFactors die worden ondersteund door de volgende twee inrichtingsapps: 
* [SuccesFactoren naar Active Directory User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccesFactoren naar Azure AD-gebruikersinrichting](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Succesfactoren Entiteit                  | Kenmerk SuccessFactors     | Type bewerking |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Per persoon                              | personIdExternal             | Lezen           |
| 2  | Per persoon                              | personId                     | Lezen           |
| 3  | Per persoon                              | perPersonUuid                | Lezen           |
| 4  | PerPersoonlijk                            | displayName                  | Lezen           |
| 5  | PerPersoonlijk                            | voornaam                    | Lezen           |
| 6  | PerPersoonlijk                            | geslacht                       | Lezen           |
| 7  | PerPersoonlijk                            | achternaam                     | Lezen           |
| 8  | PerPersoonlijk                            | middleName                   | Lezen           |
| 9  | PerPersoonlijk                            | preferredName                | Lezen           |
| 10 | Gebruiker                                   | adresLine1                 | Lezen           |
| 11 | Gebruiker                                   | adresLine2                 | Lezen           |
| 12 | Gebruiker                                   | adresLIne3                 | Lezen           |
| 13 | Gebruiker                                   | businessPhone                | Lezen           |
| 14 | Gebruiker                                   | Mobiel                    | Lezen           |
| 15 | Gebruiker                                   | city                         | Lezen           |
| 16 | Gebruiker                                   | land                      | Lezen           |
| 17 | Gebruiker                                   | aangepast01                     | Lezen           |
| 18 | Gebruiker                                   | aangepast02                     | Lezen           |
| 19 | Gebruiker                                   | aangepast03                     | Lezen           |
| 20 | Gebruiker                                   | aangepast04                     | Lezen           |
| 21 | Gebruiker                                   | aangepast05                     | Lezen           |
| 22 | Gebruiker                                   | aangepast06                     | Lezen           |
| 23 | Gebruiker                                   | aangepast07                     | Lezen           |
| 24 | Gebruiker                                   | aangepast08                     | Lezen           |
| 25 | Gebruiker                                   | aangepast09                     | Lezen           |
| 26 | Gebruiker                                   | aangepaste10                     | Lezen           |
| 27 | Gebruiker                                   | aangepast11                     | Lezen           |
| 28 | Gebruiker                                   | aangepast12                     | Lezen           |
| 29 | Gebruiker                                   | aangepast13                     | Lezen           |
| 30 | Gebruiker                                   | aangepast14                     | Lezen           |
| 31 | Gebruiker                                   | empId                        | Lezen           |
| 32 | Gebruiker                                   | homePhone (homePhone)                    | Lezen           |
| 33 | Gebruiker                                   | jobFamilie                    | Lezen           |
| 34 | Gebruiker                                   | Bijnaam                     | Lezen           |
| 35 | Gebruiker                                   | state                        | Lezen           |
| 36 | Gebruiker                                   | timeZone                     | Lezen           |
| 37 | Gebruiker                                   | gebruikersnaam                     | Lezen           |
| 38 | Gebruiker                                   | Postcode                      | Lezen           |
| 39 | Perphone                               | areaCode                     | Lezen           |
| 40 | Perphone                               | landCode                  | Lezen           |
| 41 | Perphone                               | Extensie                    | Lezen           |
| 42 | Perphone                               | phoneNumber                  | Lezen           |
| 43 | Perphone                               | phoneType                    | Lezen           |
| 44 | Pere-mail                               | Emailaddress                 | Lezen, schrijven    |
| 45 | Pere-mail                               | emailType                    | Lezen           |
| 46 | EmpEmployment                          | firstDateWorked              | Lezen           |
| 47 | EmpEmployment                          | lastDateWorked               | Lezen           |
| 48 | EmpEmployment                          | userId                       | Lezen           |
| 49 | EmpEmployment                          | isContingentWorker           | Lezen           |
| 50 | EmpJob (EmpJob)                                 | landOfCompany             | Lezen           |
| 51 | EmpJob (EmpJob)                                 | emplStatus                   | Lezen           |
| 52 | EmpJob (EmpJob)                                 | Enddate                      | Lezen           |
| 53 | EmpJob (EmpJob)                                 | Startdate                    | Lezen           |
| 54 | EmpJob (EmpJob)                                 | jobTitel                     | Lezen           |
| 55 | EmpJob (EmpJob)                                 | position                     | Lezen           |
| 65 | EmpJob (EmpJob)                                 | aangepaste String13               | Lezen           |
| 56 | EmpJob (EmpJob)                                 | managerId                    | Lezen           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Lezen           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Lezen           |
| 59 | EmpJob\.Bedrijf                        | bedrijf                      | Lezen           |
| 60 | EmpJob\.Bedrijf                        | companyId                    | Lezen           |
| 61 | EmpJob\.\.bedrijf CountryOfRegistration | twoCharCountryCode           | Lezen           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Lezen           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Lezen           |
| 64 | EmpJob\.CostCenter                     | costCenterBeschrijving        | Lezen           |
| 65 | Afdeling\.EmpJob                     | department                   | Lezen           |
| 66 | Afdeling\.EmpJob                     | departmentId                 | Lezen           |
| 67 | EmpJob\.Division                       | Divisie                     | Lezen           |
| 68 | EmpJob\.Division                       | divisionId                   | Lezen           |
| 69 | EmpJob\.Werkcode                        | jobCode                      | Lezen           |
| 70 | EmpJob\.Werkcode                        | jobCodeId                    | Lezen           |
| 71 | Locatie\.EmpJob                       | Locatienaam                 | Lezen           |
| 72 | Locatie\.EmpJob                       | officeLocationAddress        | Lezen           |
| 73 | Locatie\.EmpJob                       | officeLocationCity           | Lezen           |
| 74 | Locatie\.EmpJob                       | officeLocationCustomString4  | Lezen           |
| 75 | Locatie\.EmpJob                       | officeLocationZipCode        | Lezen           |
| 76 | EmpJob\.PayGrade                       | payGrade payGrade                     | Lezen           |
| 77 | EmpEmployment               | actiefEmploymentsCount       | Lezen           |
| 78 | EmpEmployment               | laatsteTerminationDate        | Lezen           |


## <a name="default-attribute-mapping"></a>Standaardtoewijzing van kenmerken

In de onderstaande tabel vindt u de standaardtoewijzing van kenmerken tussen de hierboven vermelde kenmerken van SuccessFactors en AD/Azure AD-kenmerken. In het Azure AD-inrichtingsblad 'Mapping' u deze standaardtoewijzing wijzigen om kenmerken uit de bovenstaande lijst op te nemen. 

| \# | Succesfactoren Entiteit                  | Kenmerk SuccessFactors | Standaardtoewijzing voor AD/Azure AD-kenmerk   | Opmerking over verwerking                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Per persoon                              | personIdExternal         | Employeeid                              | Gebruikt als overeenkomend kenmerk                                                                   |
| 2  | Per persoon                              | perPersonUuid            | \[Niet toegewezen \- gebruikt als bronanker\] | Tijdens de eerste synchronisatie koppelt de inrichtingsservice de persoonUuid aan bestaand objectGuid\.  |
| 3  | PerPersoonlijk                            | displayName              | displayName                             | N.v.t.                                                                                           |
| 4  | PerPersoonlijk                            | voornaam                | givenName                               | N.v.t.                                                                                           |
| 5  | PerPersoonlijk                            | achternaam                 | sn                                      | N.v.t.                                                                                           |
| 6  | Gebruiker                                   | adresLine1             | streetAddress                           | N.v.t.                                                                                           |
| 7  | Gebruiker                                   | city                     | l                                       | N.v.t.                                                                                           |
| 8  | Gebruiker                                   | land                  | Co                                      | N.v.t.                                                                                           |
| 9  | Gebruiker                                   | state                    | st                                      | N.v.t.                                                                                           |
| 10 | Gebruiker                                   | gebruikersnaam                 | samAccountName                          | N.v.t.                                                                                           |
| 11 | Gebruiker                                   | Postcode                  | Postcode                              | N.v.t.                                                                                           |
| 12 | Pere-mail                               | Emailaddress             | mail                                    | N.v.t.                                                                                           |
| 13 | EmpJob (EmpJob)                                 | jobTitel                 | titel                                   | N.v.t.                                                                                           |
| 14 | EmpJob (EmpJob)                                 | managerId                | manager                                 | N.v.t.                                                                                           |
| 15 | EmpJob\.\.bedrijf CountryOfRegistration | twoCharCountryCode       | c                                       | N.v.t.                                                                                           |
| 16 | Afdeling\.EmpJob                     | department               | department                              | N.v.t.                                                                                           |
| 17 | EmpJob\.Division                       | Divisie                 | bedrijf                                 | N.v.t.                                                                                           |
| 18 | Locatie\.EmpJob                       | officeLocationAddress    | streetAddress                           | N.v.t.                                                                                           |
| 19 | Locatie\.EmpJob                       | officeLocationZipCode    | Postcode                              | N.v.t.                                                                                           |
| 20 | EmpEmployment               | actiefEmploymentsCount   | accountIngeschakeld                          | als activeEmploymentsCount=0, schakelt u het account uit\.                                           |

