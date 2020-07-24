---
title: Naslag informatie over Azure Active Directory en SAP SuccessFactors-integratie
description: Technisch diep gaande kennis in SAP SuccessFactors-HR ingericht inrichten
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 3c1d0d05554fafb4b18d8dc7043cca3e8479b35e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097048"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Hoe Azure Active Directory inrichting kan worden geïntegreerd met SAP-SuccessFactors 

[Azure Active Directory User Provisioning Service](../app-provisioning/user-provisioning.md) kan worden geïntegreerd met [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) om de identiteits levenscyclus van gebruikers te beheren. Azure Active Directory biedt drie vooraf samengestelde integraties: 

* SuccessFactors naar on-premises Active Directory gebruikers inrichten
* SuccessFactors Azure Active Directory gebruikers inrichten
* SuccessFactors write-back

In dit artikel wordt uitgelegd hoe de integratie werkt en hoe u het inrichtings gedrag kunt aanpassen voor verschillende HR-scenario's. 

## <a name="establishing-connectivity"></a>Verbinding maken 
De Azure AD-inrichtings Engine maakt gebruik van basis verificatie om verbinding te maken met werk nemers Central OData API-eind punten. Bij het instellen van de SuccessFactors-inrichtings-app gebruikt u de para meter *Tenant-URL* in de sectie *beheerders referenties* om de URL van het [Data Center van API](https://apps.support.sap.com/sap/support/knowledge/en/2215682)te configureren. 

Als u de verbinding tussen Azure AD Provisioning Service en SuccessFactors wilt verbeteren, kunt u de IP-adresbereiken van Azure AD toevoegen aan de lijst met SuccessFactors IP-adressen met behulp van de stappen die hieronder worden beschreven:

* De [meest recente IP-bereiken](https://www.microsoft.com/download/details.aspx?id=56519) voor de open bare Azure-Cloud downloaden 
* Open het bestand en zoek naar labels **AzureActiveDirectory** en **AzureActiveDirectoryDomainServices** 

  >[!div class="mx-imgBorder"] 
  >![Azure AD IP-bereik](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

* Kopieer alle IP-adresbereiken die worden vermeld in het element *addressPrefixes* en gebruik het bereik om uw lijst met IP-adres beperkingen samen te stellen.
* Vertaal de CIDR-waarden naar IP-bereiken.  
* Meld u aan bij de SuccessFactors-beheer Portal om IP-adresbereiken toe te voegen aan de acceptatie lijst. Raadpleeg de SAP- [ondersteunings opmerking 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). U kunt nu [IP-bereiken invoeren](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) in dit hulp programma. 

## <a name="supported-entities"></a>Ondersteunde entiteiten
Voor elke gebruiker in SuccessFactors haalt Azure AD Provisioning Service de volgende entiteiten op. Elke entiteit wordt uitgebreid met behulp van de OData-API *$expand* query parameter. Raadpleeg de kolom voor de *ophaal regel* hieronder. Sommige entiteiten worden standaard uitgebreid, terwijl sommige entiteiten alleen worden uitgevouwen als een specifiek kenmerk aanwezig is in de toewijzing. 

| \# | SuccessFactors entiteit                  | OData-knoop punt     | Ophaal regel |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *hoofd knooppunt*                  | Altijd           |
| 2  | PerPersonal                            | personalInfoNav              | Altijd           |
| 3  | PerPhone                               | phoneNav                     | Altijd           |
| 4  | PerEmail                               | emailNav                     | Altijd           |
| 5  | EmpEmployment                          | employmentNav                | Altijd           |
| 6  | Gebruiker                                   | employmentNav/userNav        | Altijd           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Altijd           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Altijd           |
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | Alleen als het bedrijfs-of companyId kenmerk is toegewezen |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Alleen als het kenmerk Department of departmentId is toegewezen |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Alleen als het kenmerk businessUnit of businessUnitId is toegewezen |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Alleen als het kenmerk costCenter of costCenterId is toegewezen |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Alleen als het kenmerk divisie of divisionId is toegewezen |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Alleen als het kenmerk jobCode of jobCodeId is toegewezen |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Alleen als het kenmerk payGrade is toegewezen |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Alleen als het locatie kenmerk is toegewezen |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Als de toewijzing een van de volgende kenmerken bevat: officeLocationAddress, officeLocationCity, officeLocationZipCode |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Alleen als het kenmerk eventReason is toegewezen |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Alleen als assignmentType is toegewezen |
| 20 | Selectie lijst EmploymentType                | employmentNav/jobInfoNav/employmentTypeNav | Alleen als employmentType is toegewezen |
| 21 | Selectie lijst EmployeeClass                 | employmentNav/jobInfoNav/employeeClassNav | Alleen als employeeClass is toegewezen |
| 22 | Selectie lijst EmplStatus                    | employmentNav/jobInfoNav/emplStatusNav | Alleen als emplStatus is toegewezen |
| 23 | Selectie lijst AssignmentType                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Alleen als assignmentType is toegewezen |

## <a name="how-full-sync-works"></a>Hoe volledige synchronisatie werkt
Op basis van de kenmerk toewijzing wordt tijdens de volledige synchronisatie Azure AD-inrichtings service de volgende ' GET '-OData API-query verzonden om effectief gegevens van alle actieve gebruikers op te halen. 

> [!div class="mx-tdCol2BreakAll"]
>| Parameter | Beschrijving |
>| ----------|-------------|
>| OData API-host | HTTPS wordt toegevoegd aan de *Tenant-URL*. Voorbeeld: `https://api4.successfactors.com` |
>| OData-API-eind punt | `/odata/v2/PerPerson` |
>| Query parameter van OData-$format | `json` |
>| Query parameter van OData-$filter | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| Query parameter van OData-$expand | Deze parameter waarde is afhankelijk van de toegewezen kenmerken. Voorbeeld: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| Query parameter van OData customPageSize | `100` |

> [!NOTE]
> Tijdens de eerste volledige synchronisatie worden door Azure AD Provisioning Service geen niet-actieve/beëindigde werknemers gegevens opgehaald.

Voor elke SuccessFactors gebruiker zoekt de inrichtings service naar een account in het doel (Azure AD/on-premises Active Directory) met behulp van het overeenkomende kenmerk dat in de toewijzing is gedefinieerd. Bijvoorbeeld: als *personIdExternal* is toegewezen aan *employeeId* en is ingesteld als het overeenkomende kenmerk, gebruikt de inrichtings service de *personIdExternal* -waarde om te zoeken naar de gebruiker met het filter *employeeId* . Als er een overeenkomende gebruiker wordt gevonden, worden de doel kenmerken bijgewerkt. Als er geen overeenkomst wordt gevonden, wordt er een nieuw item in het doel gemaakt. 

Als u de gegevens wilt valideren die door uw OData API-eind punt worden geretourneerd voor een specifieke *personIdExternal*, werkt u de *SuccessFactorsAPIEndpoint* in de onderstaande API-query bij met de URL van uw API Data Center-Server en gebruikt u een hulp programma zoals het [bericht ' postman](https://www.postman.com/downloads/) ' om de query te openen. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Hoe incrementele synchronisatie werkt

Na volledige synchronisatie beheert Azure AD Provisioning Service *LastExecutionTimestamp* en gebruikt deze om Delta query's te maken voor het ophalen van incrementele wijzigingen. De tijds tempel kenmerken die aanwezig zijn in elke SuccessFactors entiteit, zoals *lastModifiedDateTime*, *start date*, *EndDate*en *latestTerminationDate*, worden geëvalueerd om te zien of de wijziging tussen de *LastExecutionTimestamp* en de *CurrentExecutionTime*ligt. Zo ja, dan wordt de wijziging van de invoer als effectief beschouwd en wordt deze verwerkt voor synchronisatie. 

## <a name="reading-attribute-data"></a>Kenmerk gegevens lezen

Wanneer Azure AD Provisioning Service query's uitvoert op SuccessFactors, wordt er een JSON-resultatenset opgehaald. De JSON-resultatenset bevat een aantal kenmerken die zijn opgeslagen in werk nemers centraal. Het inrichtings schema is standaard zo geconfigureerd dat er slechts een subset van die kenmerken wordt opgehaald. 

Volg de onderstaande stappen voor het ophalen van aanvullende kenmerken:
    
* Blader naar de pagina **Enter prise Applications**  ->  **SuccessFactors app**  ->  **Provisioning**  ->  **bewerken**  ->  **toewijzings kenmerk**.
* Schuif omlaag en klik op **Geavanceerde opties weer geven**.
* Klik op **kenmerk lijst bewerken voor SuccessFactors**. 

> [!NOTE] 
> Als de optie **kenmerk lijst bewerken voor SuccessFactors** niet wordt weer gegeven in de Azure Portal, gebruikt u de URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* voor toegang tot de pagina. 

* In de **expressie kolom API** in deze weer gave worden de JSONPath-expressies weer gegeven die door de connector worden gebruikt.
  >[!div class="mx-imgBorder"] 
  >![API-expressie](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  
* U kunt een bestaande JSONPath-waarde bewerken of een nieuw kenmerk met een geldige JSONPath-expressie aan het schema toevoegen. 

In de volgende sectie vindt u een lijst met algemene scenario's voor het bewerken van de JSONPath-waarden. 

## <a name="handling-different-hr-scenarios"></a>Verwerken van verschillende HR-scenario's

JSONPath is een query taal voor JSON die vergelijkbaar is met XPath voor XML. Net als XPath biedt JSONPath de mogelijkheid om gegevens uit een JSON-nettolading te extra heren en te filteren.
Met behulp van JSONPath-trans formatie kunt u het gedrag van de Azure AD-inrichtings toepassing aanpassen voor het ophalen van aangepaste kenmerken en het afhandelen van scenario's zoals opnieuw inhuren, Worker-conversie en globale toewijzing. 

In deze sectie wordt beschreven hoe u de inrichtings-app kunt aanpassen voor de volgende HR-scenario's: 
* [Aanvullende kenmerken ophalen](#retrieving-additional-attributes)
* [Aangepaste kenmerken ophalen](#retrieving-custom-attributes)
* [Conversie scenario voor werk nemers verwerken](#handling-worker-conversion-scenario)
* [Scenario voor het opnieuw inhuren verwerken](#handling-rehire-scenario)
* [Scenario voor globale toewijzing verwerken](#handling-global-assignment-scenario)
* [Scenario voor gelijktijdige taken verwerken](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Aanvullende kenmerken ophalen

Het standaard Azure AD SuccessFactors-inrichtings app-schema wordt geleverd met [90 + vooraf gedefinieerde kenmerken](sap-successfactors-attribute-reference.md). Volg de onderstaande stappen om meer out-of-the-SuccessFactors-kenmerken toe te voegen aan het inrichtings schema: 

* Gebruik de OData-query hieronder om gegevens op te halen voor een geldige test gebruiker van werk nemers centraal. 

```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

* De centrale entiteit van de werk nemer bepalen die aan het kenmerk is gekoppeld
  * Als het kenmerk deel uitmaakt van de *EmpEmployment* -entiteit, zoekt u naar het kenmerk onder het knoop punt *employmentNav* . 
  * Als het kenmerk deel uitmaakt van de *gebruikers* entiteit, zoekt u naar het kenmerk onder het knoop punt *employmentNav/userNav* .
  * Als het kenmerk deel uitmaakt van de *EmpJob* -entiteit, zoekt u naar het kenmerk onder het knoop punt *employmentNav/jobInfoNav* . 
* Maak het JSON-pad dat is gekoppeld aan het kenmerk en voeg dit nieuwe kenmerk toe aan de lijst met SuccessFactors-kenmerken. 
  * Voor beeld 1: Stel dat u het kenmerk *okToRehire*, dat deel uitmaakt van de *employmentNav* -entiteit, wilt toevoegen en gebruik vervolgens de JSONPath`$.employmentNav.results[0].okToRehire`
  * Voor beeld 2: Stel dat u de *Tijdzone*kenmerken wilt toevoegen, die deel uitmaakt van de *userNav* -entiteit en gebruik vervolgens de JSONPath`$.employmentNav.results[0].userNav.timeZone`
  * Voor beeld 3: Stel dat u het kenmerk *flsaStatus*, dat deel uitmaakt van de *jobInfoNav* -entiteit, wilt toevoegen en gebruik vervolgens de JSONPath`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
* Sla het schema op. 
* Inrichting opnieuw starten.

### <a name="retrieving-custom-attributes"></a>Aangepaste kenmerken ophalen

Standaard zijn de volgende aangepaste kenmerken vooraf gedefinieerd in de Azure AD SuccessFactors-inrichtings-app: 
* *custom01-custom15* van de gebruiker (userNav)
* *customString1-customString15* van de entiteit EmpEmployment (employmentNav) met de naam *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* van de entiteit EmpJobInfo (jobInfoNav) met de naam *empJobNavCustomString1-empNavJobCustomString15*

Stel dat in het centrale exemplaar van uw werk nemers het kenmerk *customString35* in *EmpJobInfo* de locatie beschrijving opslaat. U wilt deze waarde debiet naar Active Directory kenmerk *physicalDeliveryOfficeName* . Gebruik de volgende stappen om kenmerk toewijzing te configureren voor dit scenario: 

* Bewerk de kenmerk lijst SuccessFactors om een nieuw kenmerk met de naam *empJobNavCustomString35*toe te voegen.
* Stel de JSONPath API-expressie voor dit kenmerk in op:`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
* Sla de wijziging van de toewijzing op en laad deze opnieuw in de Azure Portal.  
* Wijs in de Blade kenmerk toewijzing *empJobNavCustomString35* toe aan *physicalDeliveryOfficeName*.
* Sla de toewijzing op.

Dit scenario uitbreiden: 
* Als u *custom35* kenmerk van de *gebruikers* entiteit wilt toewijzen, gebruikt u de JSONPath`$.employmentNav.results[0].userNav.custom35`
* Als u *customString35* kenmerk van de *EmpEmployment* -entiteit wilt toewijzen, gebruikt u de JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Conversie scenario voor werk nemers verwerken

De conversie van werk nemers is het proces van het converteren van een bestaande fulltime werk nemer naar een contractant of andersom. In dit scenario voegt werk nemer centraal een nieuwe *EmpEmployment* -entiteit toe, samen met een nieuwe *gebruikers* entiteit voor dezelfde *persoon* . De entiteit van de *gebruiker* die is genest onder de vorige *EmpEmployment* entiteit, is ingesteld op null. Als u dit scenario wilt afhandelen zodat de nieuwe dienstverband gegevens worden weer gegeven wanneer een conversie plaatsvindt, kunt u het schema van de inrichtings toepassing bulksgewijs bijwerken met behulp van de onderstaande stappen:  

* Open de Blade kenmerk toewijzing van uw SuccessFactors-inrichtings toepassing. 
* Schuif omlaag en klik op **Geavanceerde opties weer geven**.
* Klik op de koppeling om **uw schema hier te controleren** om de schema-editor te openen. 
  >![beoordeling-schema](media/sap-successfactors-integration-reference/review-schema.png#lightbox)
* Klik op de **Download** koppeling om een kopie van het schema op te slaan voordat u deze bewerkt. 
  >![downloaden-schema](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
* Druk in de schema-editor op de toets CTRL-H om het besturings element zoeken-vervangen te openen.
* Kopieer en plak de waarde in het tekstvak zoeken.`$.employmentNav.results[0]`
* Kopieer en plak de waarde in het tekstvak vervangen `$.employmentNav.results[?(@.userNav != null)]` . Let op de witruimte rond de `!=` operator, wat belang rijk is voor een succes volle verwerking van de JSONPath-expressie. 
  >![zoeken-vervangen-conversie](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
* Klik op de optie Alles vervangen om het schema bij te werken. 
* Sla het schema op. 
* Met het bovenstaande proces worden alle JSONPath-expressies als volgt bijgewerkt: 
  * Oude JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Nieuwe JSONPath:`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
* Inrichting opnieuw starten. 

### <a name="handling-rehire-scenario"></a>Scenario voor het opnieuw inhuren verwerken

Normaal gesp roken zijn er twee opties voor het verwerken van opnieuw inhuren: 
* Optie 1: een nieuw persoons profiel maken in werk nemers centraal
* Optie 2: het profiel voor een bestaande persoon opnieuw gebruiken in werk nemers centraal

Als uw HR-proces optie 1 gebruikt, zijn er geen wijzigingen vereist voor het inrichtings schema. Als uw HR-proces optie 2 gebruikt, voegt Employee Central een nieuwe *EmpEmployment* -entiteit toe, samen met een nieuwe *gebruikers* entiteit voor dezelfde *persoon* . In tegens telling tot het conversie scenario behoudt de vorige *EmpEmployment* -entiteit de *gebruikers* entiteit en is deze niet ingesteld op null. 

Als u dit scenario voor het opnieuw inhuren wilt afhandelen (optie 2), zodat de meest recente werkgelegenheids gegevens worden weer gegeven voor profielen voor opnieuw inhuren, kunt u het schema van de inrichtings toepassing bulksgewijs bijwerken met behulp van de onderstaande stappen:  

* Open de Blade kenmerk toewijzing van uw SuccessFactors-inrichtings toepassing. 
* Schuif omlaag en klik op **Geavanceerde opties weer geven**.
* Klik op de koppeling om **uw schema hier te controleren** om de schema-editor te openen.   
* Klik op de **Download** koppeling om een kopie van het schema op te slaan voordat u deze bewerkt.   
* Druk in de schema-editor op de toets CTRL-H om het besturings element zoeken-vervangen te openen.
* Kopieer en plak de waarde in het tekstvak zoeken.`$.employmentNav.results[0]`
* Kopieer en plak de waarde in het tekstvak vervangen `$.employmentNav.results[-1:]` . Met deze JSONPath-expressie wordt de meest recente *EmpEmployment* -record geretourneerd.   
* Klik op de optie Alles vervangen om het schema bij te werken. 
* Sla het schema op. 
* Met het bovenstaande proces worden alle JSONPath-expressies als volgt bijgewerkt: 
  * Oude JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Nieuwe JSONPath:`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
* Inrichting opnieuw starten. 

### <a name="handling-global-assignment-scenario"></a>Scenario voor globale toewijzing verwerken

Wanneer een gebruiker in werk nemers centraal wordt verwerkt voor globale toewijzing, voegt SuccessFactors een nieuwe *EmpEmployment* -entiteit toe en wordt de *assignmentClass* ingesteld op "Ga". Er wordt ook een nieuwe *gebruikers* entiteit gemaakt. De gebruiker heeft daarom nu:
* Een *EmpEmployment*-  +  *gebruikers* entiteit die overeenkomt met de start toewijzing met *assignmentClass* ingesteld op St en 
* Een andere *EmpEmployment*-  +  *gebruikers* entiteit die overeenkomt met de globale toewijzing met *assignmentClass* ingesteld op "Ga"

Als u kenmerken wilt ophalen die horen bij het standaard gebruikers profiel voor toewijzing en globale toewijzing, gebruikt u de volgende stappen: 

* Open de Blade kenmerk toewijzing van uw SuccessFactors-inrichtings toepassing. 
* Schuif omlaag en klik op **Geavanceerde opties weer geven**.
* Klik op de koppeling om **uw schema hier te controleren** om de schema-editor te openen.   
* Klik op de **Download** koppeling om een kopie van het schema op te slaan voordat u deze bewerkt.   
* Druk in de schema-editor op de toets CTRL-H om het besturings element zoeken-vervangen te openen.
* Kopieer en plak de waarde in het tekstvak zoeken.`$.employmentNav.results[0]`
* Kopieer en plak de waarde in het tekstvak vervangen `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
* Klik op de optie Alles vervangen om het schema bij te werken. 
* Sla het schema op. 
* Met het bovenstaande proces worden alle JSONPath-expressies als volgt bijgewerkt: 
  * Oude JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Nieuwe JSONPath:`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
* Laad de Blade kenmerk toewijzing van de app opnieuw. 
* Schuif omlaag en klik op **Geavanceerde opties weer geven**.
* Klik op **kenmerk lijst bewerken voor SuccessFactors**.
* Nieuwe kenmerken toevoegen om globale toewijzings gegevens op te halen. Bijvoorbeeld: als u de afdelings naam wilt ophalen die is gekoppeld aan een globaal toewijzings profiel, kunt u het kenmerk **globalAssignmentDepartment** toevoegen waarbij de JSONPath-expressie is ingesteld op `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
* U kunt nu beide afdelings waarden door lopen naar Active Directory kenmerken of een waarde selectief door lopen met expressie toewijzing. Voor beeld: met de onderstaande expressie wordt de waarde van het kenmerk AD *Department* ingesteld op *globalAssignmentDepartment* als deze aanwezig is, anders wordt de waarde ingesteld op *afdeling* die aan standaard toewijzing is gekoppeld. 
  * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`
* Sla de toewijzing op. 
* Inrichting opnieuw starten. 

### <a name="handling-concurrent-jobs-scenario"></a>Scenario voor gelijktijdige taken verwerken

Wanneer een gebruiker in werk nemers centraal gelijktijdige/meerdere taken heeft, zijn er twee *EmpEmployment* en *gebruikers* entiteiten waarvoor *ASSIGNMENTCLASS* is ingesteld op "St". Als u kenmerken wilt ophalen die horen bij beide taken, gebruikt u de volgende stappen: 

* Open de Blade kenmerk toewijzing van uw SuccessFactors-inrichtings toepassing. 
* Schuif omlaag en klik op **Geavanceerde opties weer geven**.
* Klik op **kenmerk lijst bewerken voor SuccessFactors**.
* Stel dat u de afdeling wilt ophalen die is gekoppeld aan taak 1 en taak 2. De vooraf gedefinieerde kenmerk *afdeling* haalt al de waarde van afdeling voor de eerste taak op. U kunt een nieuw kenmerk met de naam *secondJobDepartment* definiëren en de JSONPath-expressie instellen op`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
* U kunt nu beide afdelings waarden door lopen naar Active Directory kenmerken of een waarde selectief door lopen met expressie toewijzing. 
* Sla de toewijzing op. 
* Inrichting opnieuw starten. 

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het configureren van SuccessFactors voor Active Directory inrichting](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Meer informatie over het configureren van write-back naar SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Meer informatie over ondersteunde SuccessFactors-kenmerken voor inkomende inrichting](sap-successfactors-attribute-reference.md)










