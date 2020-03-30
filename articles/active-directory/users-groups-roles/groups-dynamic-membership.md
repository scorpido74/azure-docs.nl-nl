---
title: Regels voor dynamisch groepslidmaatschap - Azure AD | Microsoft Documenten
description: Lidmaatschapsregels maken om groepen automatisch te vullen en een regelverwijzing.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: da983f87977de922ec547c3ade2972dfb4d69363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253076"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Dynamische lidmaatschapsregels voor groepen in Azure Active Directory

In Azure Active Directory (Azure AD) u complexe regels op basis van kenmerken maken om dynamische lidmaatschappen voor groepen in te schakelen. Dynamisch groepslidmaatschap vermindert de administratieve overhead van het toevoegen en verwijderen van gebruikers. In dit artikel worden de eigenschappen en syntaxis beschreven om dynamische lidmaatschapsregels voor gebruikers of apparaten te maken. U kunt een regel instellen voor dynamisch lidmaatschap voor beveiligingsgroepen of Office 365-groepen.

Wanneer kenmerken van een gebruiker of apparaat veranderen, evalueert het systeem alle dynamische groepsregels in een map om te zien of de wijziging een groep toevoegt of verwijdert. Als een gebruiker of apparaat voldoet aan een regel voor een groep, wordt deze toegevoegd als lid van die groep. Als ze niet meer voldoen aan de regel, worden ze verwijderd. U een lid van een dynamische groep niet handmatig toevoegen of verwijderen.

- U een dynamische groep maken voor apparaten of gebruikers, maar u geen regel maken die zowel gebruikers als apparaten bevat.
- U geen apparaatgroep maken op basis van de kenmerken van de apparaateigenaren. Apparaatlidmaatschapsregels kunnen alleen verwijzen naar apparaatkenmerken.

> [!NOTE]
> Deze functie vereist een Azure AD Premium P1-licentie voor elke unieke gebruiker die lid is van een of meer dynamische groepen. U hoeft geen licenties toe te wijzen aan gebruikers om lid te zijn van dynamische groepen, maar u moet het minimum aantal licenties in de tenant hebben om al deze gebruikers te dekken. Als u bijvoorbeeld in totaal 1.000 unieke gebruikers in alle dynamische groepen in uw tenant had, hebt u ten minste 1.000 licenties nodig voor Azure AD Premium P1 om aan de licentievereiste te voldoen.
> Er is geen licentie vereist voor apparaten die lid zijn van een dynamische apparaatgroep.

## <a name="rule-builder-in-the-azure-portal"></a>Regelbouwer in de Azure-portal

Azure AD biedt een regelbouwer om uw belangrijke regels sneller te maken en bij te werken. De regelbouwer ondersteunt de constructie tot vijf expressies. De regelbouwer maakt het gemakkelijker om een regel te vormen met een paar eenvoudige expressies, maar het kan niet worden gebruikt om elke regel te reproduceren. Als de regelbouwer de regeldie u wilt maken niet ondersteunt, u het tekstvak gebruiken.

Hier volgen enkele voorbeelden van geavanceerde regels of syntaxis waarvoor u het tekstvak samenstellen:

- Regel met meer dan vijf expressies
- De regel Direct-rapporten
- Voorrang [van de operator instellen](groups-dynamic-membership.md#operator-precedence)
- [Regels met complexe uitdrukkingen](groups-dynamic-membership.md#rules-with-complex-expressions); bijvoorbeeld`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> De regelbouwer kan mogelijk bepaalde regels die in het tekstvak zijn gemaakt, niet weergeven. Mogelijk ziet u een bericht wanneer de regelbouwer de regel niet kan weergeven. De regelbouwer wijzigt op geen enkele manier de ondersteunde syntaxis, validatie of verwerking van dynamische groepsregels.

Zie Een dynamische groep maken [of bijwerken](groups-create-rule.md)voor meer stapsgewijze instructies.

![Lidmaatschapsregel toevoegen voor een dynamische groep](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Syntaxis van de regel voor één expressie

Een enkele uitdrukking is de eenvoudigste vorm van een lidmaatschapregel en heeft alleen de drie hierboven genoemde delen. Een regel met één expressie lijkt `Property Operator Value`op deze: , waarbij de syntaxis voor de eigenschap de naam van object.eigenschap is.

Het volgende is een voorbeeld van een goed geconstrueerde lidmaatschapsregel met één expressie:

```
user.department -eq "Sales"
```

Haakjes zijn optioneel voor één expressie. De totale lengte van de hoofdtekst van uw lidmaatschapsregel mag niet hoger zijn dan 2048 tekens.

## <a name="constructing-the-body-of-a-membership-rule"></a>Het construeren van het lichaam van een lidmaatschapsregel

Een lidmaatschapsregel die automatisch een groep vult met gebruikers of apparaten, is een binaire expressie die resulteert in een true of false outcome. De drie delen van een eenvoudige regel zijn:

- Eigenschap
- Operator
- Waarde

De volgorde van de onderdelen binnen een expressie is belangrijk om syntaxisfouten te voorkomen.

## <a name="supported-properties"></a>Ondersteunde eigenschappen

Er zijn drie typen eigenschappen die kunnen worden gebruikt om een lidmaatschapsregel te construeren.

- Booleaans
- Tekenreeks
- Tekenreeksverzameling

Hieronder volgen de gebruikerseigenschappen die u gebruiken om één expressie te maken.

### <a name="properties-of-type-boolean"></a>Eigenschappen van type booleaan

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| accountIngeschakeld |waar onwaar |user.accountEnabled -eq true |
| dirSyncEnabled |waar onwaar |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Eigenschappen van teksttekenreeks

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| city |Tekenreekswaarde of *null* |(user.city -eq "waarde") |
| land |Tekenreekswaarde of *null* |(user.country -eq "waarde") |
| Bedrijfsnaam | Tekenreekswaarde of *null* | (user.companyName -eq "waarde") |
| department |Tekenreekswaarde of *null* |(user.department -eq "waarde") |
| displayName |Tekenreekswaarde |(user.displayName -eq "waarde") |
| Employeeid |Tekenreekswaarde |(user.employeeId -eq "waarde")<br>(user.employeeId -ne *null*) |
| facsimileTelefoonnummer |Tekenreekswaarde of *null* |(user.facsimilePhoneNumber -eq "waarde") |
| givenName |Tekenreekswaarde of *null* |(user.givenName -eq "waarde") |
| jobTitel |Tekenreekswaarde of *null* |(user.jobTitle -eq "waarde") |
| mail |Tekenreekswaarde of *null* (SMTP-adres van de gebruiker) |(user.mail -eq "waarde") |
| mailNickName |Tekenreekswaarde (e-mailalias van de gebruiker) |(user.mailNickName -eq "waarde") |
| mobiel |Tekenreekswaarde of *null* |(user.mobile -eq "waarde") |
| objectId |GUID van het gebruikersobject |(user.objectId -eq "1111111-1111-1111-1111-11111-11111111111") |
| onPremisesSecurityIdentifier | On-premises beveiligingsid (SID) voor gebruikers die zijn gesynchroniseerd van on-premises naar de cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-11111111-111111111-1111111-1111111-11111111") |
| wachtwoordBeleid |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Tekenreekswaarde of *null* |(user.physicalDeliveryOfficeName -eq "waarde") |
| Postcode |Tekenreekswaarde of *null* |(user.postalCode -eq "waarde") |
| voorkeurTaal |ISO 639-1 code |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Tekenreekswaarde of *null* |(user.sipProxyAddress -eq "waarde") |
| state |Tekenreekswaarde of *null* |(user.state -eq "waarde") |
| streetAddress |Tekenreekswaarde of *null* |(user.streetAddress -eq "waarde") |
| surname |Tekenreekswaarde of *null* |(user.surname -eq "waarde") |
| telefoonNummer |Tekenreekswaarde of *null* |(user.phoneNumber -eq "waarde") |
| gebruikLocatie |Twee letters landcode |(user.usageLocatie -eq "US") |
| userPrincipalName |Tekenreekswaarde |(user.userPrincipalName -eqalias@domain" ") |
| userType |lid gast *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Eigenschappen van teksttekenreeksverzameling

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| andereMails |Tekenreekswaarde |(user.otherMails -bevatalias@domain" ") |
| proxyAddresses |SMTP: alias@domain smtp:alias@domain |(user.proxyAddresses -bevat "SMTP: alias@domain") |

Zie [Regels voor apparaten voor](#rules-for-devices)de eigenschappen die worden gebruikt voor apparaatregels.

## <a name="supported-expression-operators"></a>Ondersteunde expressieoperatoren

In de volgende tabel worden alle ondersteunde operatoren en hun syntaxis voor één expressie weergegeven. Operators kunnen worden gebruikt met of zonder het koppelteken (-) voorvoegsel.

| Operator | Syntaxis |
| --- | --- |
| Niet gelijk aan |-ne |
| Is gelijk aan |-eq |
| Niet begint met |-notStartsMet |
| Begint met |-startsMet |
| Bevat geen |-nietBevat |
| Contains |-bevat |
| Niet overeenkomen |-notMatch |
| Match |-match |
| In | -in |
| Niet in | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Het gebruik van de -in en -notIn operators

Als u de waarde van een gebruikerskenmerk wilt vergelijken met een aantal verschillende waarden, u de operatoren -in of -notIn gebruiken. Gebruik de beugelsymbolen "[" en "]" om de lijst met waarden te beginnen en te beëindigen.

 In het volgende voorbeeld wordt de expressie geëvalueerd als de waarde van user.department gelijk is aan een van de waarden in de lijst:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>De operator -match gebruiken 
De **operator -match** wordt gebruikt voor het afstemmen van een reguliere expressie. Voorbeelden:

```
user.displayName -match "Da.*"   
```
Da, Dav, David evalueren om waar, aDa evalueert om vals.

```
user.displayName -match ".*vid"
```
David evalueert aan waar, Da evalueert aan vals.

## <a name="supported-values"></a>Ondersteunde waarden

De waarden die in een expressie worden gebruikt, kunnen uit verschillende typen bestaan, waaronder:

* Tekenreeksen
* Booleaans – waar, onwaar
* Nummers
* Arrays – getalarray, tekenreeksarray

Bij het opgeven van een waarde binnen een expressie is het belangrijk om de juiste syntaxis te gebruiken om fouten te voorkomen. Enkele syntaxistips zijn:

* Dubbele aanhalingstekens zijn optioneel, tenzij de waarde een tekenreeks is.
* String- en regex-bewerkingen zijn niet hoofdlettergevoelig.
* Wanneer een tekenreekswaarde dubbele aanhalingstekens bevat, \` moeten beide aanhalingstekens \`worden\`ontweken met behulp van het teken, bijvoorbeeld user.department -eq "Sales" is de juiste syntaxis wanneer "Verkoop" de waarde is.
* U ook Null-controles uitvoeren, bijvoorbeeld met `user.department -eq null`null als waarde.

### <a name="use-of-null-values"></a>Gebruik van Null-waarden

Als u een null-waarde in een regel wilt opgeven, u de *null-waarde* gebruiken. 

* Gebruik -eq of -ne bij het vergelijken van de *null-waarde* in een expressie.
* Gebruik aanhalingstekens rond het woord *null* alleen als u wilt dat het wordt geïnterpreteerd als een letterlijke tekenreekswaarde.
* De operator kan niet worden gebruikt als vergelijkende operator voor null. Als u het gebruikt, krijgt u een foutmelding of u null of $null gebruikt.

De juiste manier om naar de null-waarde te verwijzen is als volgt:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regels met meerdere expressies

Een groepslidmaatschapsregel kan bestaan uit meer dan één expressie die is verbonden door de -en, -of- en -niet logische operatoren. Logische operatoren kunnen ook in combinatie worden gebruikt. 

Hieronder volgen voorbeelden van goed geconstrueerde lidmaatschapsregels met meerdere expressies:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Bewerkingsvolgorde van operators

Alle operatoren worden hieronder vermeld in volgorde van voorrang van hoog naar laag. Exploitanten op dezelfde lijn hebben dezelfde prioriteit:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Het volgende is een voorbeeld van operatorprioriteit waarbij twee expressies voor de gebruiker worden geëvalueerd:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Haakjes zijn alleen nodig als de voorrang niet aan uw vereisten voldoet. Als u bijvoorbeeld wilt dat de afdeling eerst wordt geëvalueerd, ziet u als volgt hoe haakjes kunnen worden gebruikt om de volgorde te bepalen:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regels met complexe expressies

Een lidmaatschapsregel kan bestaan uit complexe expressies waarbij de eigenschappen, operators en waarden complexere vormen aannemen. Expressies worden als complex beschouwd wanneer een van de volgende uitdrukkingen waar is:

* Het pand bestaat uit een verzameling van waarden; met name multi-gewaardeerde eigenschappen
* De expressies maken gebruik van de -alle operatoren
* De waarde van de expressie kan zelf een of meer expressies zijn

## <a name="multi-value-properties"></a>Eigenschappen met meerdere waarde

Eigenschappen met meerdere waarden zijn verzamelingen van objecten van hetzelfde type. Ze kunnen worden gebruikt om lidmaatschapsregels te maken met behulp van de -alle logische operatoren.

| Eigenschappen | Waarden | Gebruik |
| --- | --- | --- |
| toegewezenPlannen | Elk object in de verzameling onthult de volgende tekenreekseigenschappen: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -en toegewezenPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses -any\_ ( -bevat "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Het gebruik van de -alle operatoren

U -alle operatoren gebruiken om een voorwaarde toe te passen op respectievelijk een of alle items in de collectie.

* -elke (tevreden wanneer ten minste één artikel in de collectie overeenkomt met de voorwaarde)
* -alle (tevreden wanneer alle items in de collectie overeenkomen met de voorwaarde)

#### <a name="example-1"></a>Voorbeeld 1

assignedPlans is een eigenschap met meerdere waarden met alle serviceplannen die aan de gebruiker zijn toegewezen. De volgende expressie selecteert gebruikers die het Exchange Online-serviceplan (Plan 2) hebben (als GUID-waarde) dat ook in de status Ingeschakeld is:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Een regel als deze kan worden gebruikt om alle gebruikers te groeperen voor wie een Office 365 -mogelijkheid (of andere Microsoft Online Service) is ingeschakeld. U dan een aanvraag indienen met een set beleidsregels voor de groep.

#### <a name="example-2"></a>Voorbeeld 2

De volgende expressie selecteert alle gebruikers die een serviceplan hebben dat is gekoppeld aan de Intune-service (aangeduid met de servicenaam "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Met behulp van\_de syntaxis ( )

De syntaxis\_( ) komt overeen met het optreden van een specifieke waarde in een van de eigenschappen van de tekenreeksverzameling met meerdere waarden om gebruikers of apparaten toe te voegen aan een dynamische groep. Het wordt gebruikt met de -alle of -alle exploitanten.

Hier is een voorbeeld van het\_gebruik van de underscore ( ) in een regel om leden toe te voegen op basis van user.proxyAddress (het werkt hetzelfde voor user.otherMails). Deze regel voegt elke gebruiker met proxyadres met "contoso" toe aan de groep.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Andere eigenschappen en gemeenschappelijke regels

### <a name="create-a-direct-reports-rule"></a>Een regel 'Directe rapporten' maken

U een groep maken met alle directe rapporten van een manager. Wanneer de directe rapporten van de manager in de toekomst veranderen, wordt het lidmaatschap van de groep automatisch aangepast.

De regel directe rapporten wordt geconstrueerd met de volgende syntaxis:

```
Direct Reports for "{objectID_of_manager}"
```

Hier is een voorbeeld van een geldige regel waarbij "62e19b97-8b3d-4d4a-a106-4ce66896a863" de objectID van de manager is:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Met de volgende tips u de regel goed gebruiken.

- De **manager-id** is de object-id van de manager. Het is te vinden in het **profiel**van de manager.
- Zorg ervoor dat de eigenschap **Manager** correct is ingesteld voor gebruikers in uw tenant. U de huidige waarde controleren in het **profiel**van de gebruiker.
- Deze regel ondersteunt alleen de directe rapporten van de manager. Met andere woorden, u geen groep maken met de directe rapporten van de manager *en* hun rapporten.
- Deze regel kan niet worden gecombineerd met andere lidmaatschapsregels.

### <a name="create-an-all-users-rule"></a>Een regel 'Alle gebruikers' maken

U een groep maken met alle gebruikers binnen een tenant met behulp van een lidmaatschapsregel. Wanneer gebruikers in de toekomst worden toegevoegd of uit de tenant worden verwijderd, wordt het lidmaatschap van de groep automatisch aangepast.

De regel 'Alle gebruikers' is opgebouwd met één expressie met behulp van de operator -ne en de null-waarde. Deze regel voegt B2B-gastgebruikers en ledengebruikers toe aan de groep.

```
user.objectId -ne null
```
Als u wilt dat uw groep gastgebruikers uitsluit en alleen leden van uw tenant opneemt, u de volgende syntaxis gebruiken:

```
(user.objectId -ne null) -and (user.userType -eq “Member”)
```

### <a name="create-an-all-devices-rule"></a>Een regel 'Alle apparaten' maken

U een groep maken met alle apparaten binnen een tenant met behulp van een lidmaatschapsregel. Wanneer apparaten in de toekomst worden toegevoegd of verwijderd uit de tenant, wordt het lidmaatschap van de groep automatisch aangepast.

De regel 'Alle apparaten' is opgebouwd met één expressie met de operator -ne en de null-waarde:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Uitbreidingseigenschappen en aangepaste extensie-eigenschappen

Extensiekenmerken en aangepaste extensie-eigenschappen worden ondersteund als tekenreekseigenschappen in dynamische lidmaatschapsregels. [Extensiekenmerken](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) worden gesynchroniseerd vanaf on-premises Window Server AD en nemen de indeling van 'ExtensionAttributeX', waarbij X gelijk is aan 1 - 15. Hier is een voorbeeld van een regel die een extensiekenmerk als eigenschap gebruikt:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Aangepaste extensie-eigenschappen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions) worden gesynchroniseerd vanaf on-premises Windows Server AD of vanaf een `user.extension_[GUID]_[Attribute]`verbonden SaaS-toepassing en hebben de indeling van , waar:

* [GUID] is de unieke id in Azure AD voor de toepassing die de eigenschap in Azure AD heeft gemaakt
* [Attribuut] is de naam van de eigenschap die is gemaakt

Een voorbeeld van een regel die een aangepaste extensieeigenschap gebruikt, is:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

De aangepaste eigenschapsnaam is te vinden in de map door de eigenschap van een gebruiker op te vragen met Grafiekverkenner en te zoeken naar de naam van de eigenschap. U nu ook de koppeling **Aangepaste extensieeigenschappen** in de regelbouwer dynamische gebruikersgroep selecteren om een unieke app-id in te voeren en de volledige lijst met aangepaste extensie-eigenschappen te ontvangen die u wilt gebruiken bij het maken van een dynamische lidmaatschapsregel. Deze lijst kan ook worden vernieuwd om nieuwe aangepaste extensie-eigenschappen voor die app te krijgen.

## <a name="rules-for-devices"></a>Regels voor apparaten

U ook een regel maken die apparaatobjecten selecteert voor lidmaatschap in een groep. U niet zowel gebruikers als apparaten als groepsleden hebben. 

> [!NOTE]
> Het kenmerk **organizationalUnit** wordt niet meer vermeld en mag niet worden gebruikt. Deze tekenreeks wordt ingesteld door Intune in specifieke gevallen, maar wordt niet herkend door Azure AD, zodat er geen apparaten worden toegevoegd aan groepen op basis van dit kenmerk.

> [!NOTE]
> systemlabels is een alleen-lezen kenmerk dat niet kan worden ingesteld met Intune.
>
> Voor Windows 10 is de juiste indeling van het kenmerk deviceOSVersion als volgt: (device.deviceOSVersion -eq "10.0.17763"). De opmaak kan worden gevalideerd met de Get-MsolDevice PowerShell-cmdlet.

De volgende apparaatkenmerken kunnen worden gebruikt.

 Apparaatkenmerk  | Waarden | Voorbeeld
 ----- | ----- | ----------------
 accountIngeschakeld | waar onwaar | (device.accountEnabled -eq true)
 displayName | een tekenreekswaarde |(device.displayName -eq "Rob iPhone")
 deviceOSType | een tekenreekswaarde | (device.deviceOSType -eq "iPad") -of (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -bevat 'AndroidEnterprise')<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | een tekenreekswaarde | (device.deviceOSVersion -eq "9.1")
 apparaatCategorie | een geldige naam van apparaatcategorie | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | een tekenreekswaarde | (device.deviceFabrikant -eq "Samsung")
 deviceModel | een tekenreekswaarde | (device.deviceModel -eq "iPad Air")
 deviceOwnership deviceOwnership deviceOwnership deviceOwnership | Persoonlijk, Bedrijf, Onbekend | (device.deviceOwnership -eq "Company")
 inschrijvingProfielNaam | Apple Device Enrollment Profile, Device enrollment - Corporate device identifiers (Android - Kiosk) of Windows Autopilot profielnaam | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | waar onwaar | (device.isRooted -eq true)
 managementType | MDM (voor mobiele apparaten)<br>PC (voor computers die worden beheerd door de intune-pc-agent) | (device.managementType -eq "MDM")
 deviceId | een geldige Azure AD-apparaat-id | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717dd")
 objectId | een geldige Azure AD-object-id |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | tekenreekswaarde die door Autopilot wordt gebruikt, zoals alle Autopilot-apparaten, OrderID of PurchaseOrderID  | (device.devicePhysicalIDs -any _ -bevat "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | elke tekenreeks die overeenkomt met de eigenschap Intune-apparaat voor het taggen van moderne werkplekapparaten | (device.systemLabels -bevat "M365Managed")

> [!Note]  
> Voor het apparaatEigendom bij het maken van dynamische groepen voor apparaten moet u de waarde gelijk stellen aan 'Bedrijf'. Op Intune wordt het eigendom van het apparaat in plaats daarvan weergegeven als Corporate. Raadpleeg [OwnerTypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over groepen in Azure Active Directory.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een nieuwe groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-create-rule.md)
