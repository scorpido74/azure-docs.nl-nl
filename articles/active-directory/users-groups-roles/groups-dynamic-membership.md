---
title: Dynamische regels voor automatisch groepslid maatschap-Azure Active Directory | Microsoft Docs
description: Lidmaatschaps regels maken voor het automatisch invullen van groepen en een verwijzing naar een regel.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/10/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb9b3a4add951079ab918d3ac02ca5e38eff6161
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241175"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Dynamische lidmaatschaps regels voor groepen in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u complexe op kenmerken gebaseerde regels maken om dynamische lidmaatschappen voor groepen in te scha kelen. Dynamisch groepslid maatschap vermindert de administratieve overhead van het toevoegen en verwijderen van gebruikers. In dit artikel vindt u informatie over de eigenschappen en syntaxis voor het maken van dynamische lidmaatschaps regels voor gebruikers of apparaten. U kunt een regel instellen voor dynamisch lidmaatschap voor beveiligingsgroepen of Office 365-groepen.

Wanneer een van de kenmerken van een gebruiker of apparaat wordt gewijzigd, evalueert het systeem alle dynamische groeps regels in een map om te zien of de wijziging een groep toevoegt of verwijdert. Als een gebruiker of apparaat voldoet aan een regel voor een groep, worden deze toegevoegd als lid van die groep. Als ze niet meer voldoen aan de regel, worden ze verwijderd. U kunt een lid van een dynamische groep niet hand matig toevoegen of verwijderen.

- U kunt een dynamische groep maken voor apparaten of voor gebruikers, maar u kunt geen regel maken die zowel gebruikers als apparaten bevat.
- U kunt geen apparaatgroep maken op basis van de kenmerken van de apparaat-eigen aren. Regels voor lidmaatschap van apparaten kunnen alleen verwijzen naar kenmerken van het apparaat.

> [!NOTE]
> Voor deze functie is een Azure AD Premium P1-licentie vereist voor elke unieke gebruiker die lid is van een of meer dynamische groepen. U hoeft geen licenties toe te wijzen aan gebruikers, zodat ze lid zijn van dynamische groepen, maar u moet het minimum aantal licenties in de Tenant hebben om al deze gebruikers te kunnen behandelen. Als u bijvoorbeeld in totaal 1.000 unieke gebruikers in alle dynamische groepen in uw Tenant hebt, moet u ten minste 1.000 licenties voor Azure AD Premium P1 hebben om te voldoen aan de licentie vereisten.
>

## <a name="rule-builder-in-the-azure-portal"></a>De opbouw functie voor regels in de Azure Portal

Azure AD biedt een regel bouwer om uw belang rijke regels sneller te maken en bij te werken. De opbouw functie voor regels ondersteunt de bouw Maxi maal vijf expressies. Met de opbouw functie voor regels kunt u eenvoudig een regel maken met enkele eenvoudige expressies, maar deze kan niet worden gebruikt om elke regel te reproduceren. Als de regel functie geen ondersteuning biedt voor de regel die u wilt maken, kunt u het tekstvak gebruiken.

Hier volgen enkele voor beelden van geavanceerde regels of syntaxis voor het maken van het gebruik van het tekstvak:

- Regel met meer dan vijf expressies
- De regel voor directe rapporten
- De [prioriteit van Opera tors](groups-dynamic-membership.md#operator-precedence) instellen
- [Regels met complexe expressies](groups-dynamic-membership.md#rules-with-complex-expressions); bijvoorbeeld `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> De opbouw functie voor regels kan mogelijk geen regels weer geven die zijn gemaakt in het tekstvak. Mogelijk wordt er een bericht weer gegeven wanneer de regel functie de regel niet kan weer geven. De opbouw functie voor regels wijzigt niet de ondersteunde syntaxis, validatie of verwerking van dynamische groeps regels op enigerlei wijze.

Zie [een dynamische groep bijwerken](groups-update-rule.md)voor meer stapsgewijze instructies.

![Lidmaatschaps regel voor een dynamische groep toevoegen](./media/groups-update-rule/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Syntaxis van regels voor één expressie

Eén expressie is de eenvoudigste vorm van een lidmaatschaps regel en heeft alleen de drie onderdelen die hierboven worden genoemd. Een regel met één expressie ziet er ongeveer als volgt uit: `Property Operator Value`, waarbij de syntaxis van de eigenschap de naam van object. Property is.

Hier volgt een voor beeld van een goed opgebouwde lidmaatschaps regel met één expressie:

```
user.department -eq "Sales"
```

Haakjes zijn optioneel voor één expressie. De totale lengte van de hoofd tekst van de lidmaatschaps regel mag niet langer zijn dan 2048 tekens.

## <a name="constructing-the-body-of-a-membership-rule"></a>De hoofd tekst van een lidmaatschaps regel samen stellen

Een lidmaatschaps regel die automatisch een groep met gebruikers of apparaten vult, is een binaire expressie die resulteert in het resultaat True of false. De drie delen van een eenvoudige regel zijn:

- Eigenschap
- Operator
- Waarde

De volg orde van de onderdelen binnen een expressie is belang rijk om syntaxis fouten te voor komen.

## <a name="supported-properties"></a>Ondersteunde eigenschappen

Er zijn drie soorten eigenschappen die kunnen worden gebruikt om een lidmaatschaps regel samen te stellen.

- Booleaans
- Tekenreeks
- Teken reeks verzameling

Hier volgen de gebruikers eigenschappen die u kunt gebruiken om één expressie te maken.

### <a name="properties-of-type-boolean"></a>Eigenschappen van het type Boolean

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| AccountEnabled |waar onwaar |User. accountEnabled-EQ True |
| dirSyncEnabled |waar onwaar |User. dirSyncEnabled-EQ True |

### <a name="properties-of-type-string"></a>Eigenschappen van het type teken reeks

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| city |Een wille keurige teken reeks waarde of *Null* |(User. City-EQ "waarde") |
| Regio |Een wille keurige teken reeks waarde of *Null* |(User. country-EQ "waarde") |
| CompanyName | Een wille keurige teken reeks waarde of *Null* | (User. companyName-EQ "waarde") |
| department |Een wille keurige teken reeks waarde of *Null* |(User. Department-EQ "waarde") |
| displayName |Wille keurige teken reeks waarde |(User. displayName-EQ "waarde") |
| employeeId |Wille keurige teken reeks waarde |(User. employeeId-EQ "waarde")<br>(User. employeeId-ne *Null*) |
| facsimileTelephoneNumber |Een wille keurige teken reeks waarde of *Null* |(User. facsimileTelephoneNumber-EQ "waarde") |
| GivenName |Een wille keurige teken reeks waarde of *Null* |(gebruiker. OpgegevenNaam-EQ "waarde") |
| JobTitle |Een wille keurige teken reeks waarde of *Null* |(User. jobTitle-EQ "waarde") |
| mail |Een wille keurige teken reeks waarde of *Null* (SMTP-adres van de gebruiker) |(gebruiker. mail-EQ "waarde") |
| mailNickName |Een wille keurige teken reeks waarde (e-mail alias van de gebruiker) |(gebruiker. mailnickname-EQ "waarde") |
| provider |Een wille keurige teken reeks waarde of *Null* |(gebruiker. Mobile-EQ "waarde") |
| Id |GUID van het gebruikers object |(User. objectId-EQ "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Een on-premises beveiligings-id (SID) voor gebruikers die zijn gesynchroniseerd van on-premises naar de Cloud. |(User. onPremisesSecurityIdentifier-EQ "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Geen DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(User. passwordPolicies-EQ "DisableStrongPassword") |
| physicalDeliveryOfficeName |Een wille keurige teken reeks waarde of *Null* |(User. physicalDeliveryOfficeName-EQ "waarde") |
| Code |Een wille keurige teken reeks waarde of *Null* |(gebruiker. post code-EQ "waarde") |
| preferredLanguage |ISO 639-1-code |(User. preferredLanguage-EQ "en-US") |
| sipProxyAddress |Een wille keurige teken reeks waarde of *Null* |(User. sipProxyAddress-EQ "waarde") |
| state |Een wille keurige teken reeks waarde of *Null* |(User. State-EQ "waarde") |
| streetAddress |Een wille keurige teken reeks waarde of *Null* |(User. streetAddress-EQ "waarde") |
| surname |Een wille keurige teken reeks waarde of *Null* |(User. achternaam-EQ "waarde") |
| TelephoneNumber |Een wille keurige teken reeks waarde of *Null* |(User. telephoneNumber-EQ "waarde") |
| usageLocation |Land code van twee letters |(User. usageLocation-EQ "US") |
| userPrincipalName |Wille keurige teken reeks waarde |(User. userPrincipalName-EQ "alias@domain") |
| User type |*Null* voor leden gast |(User. User type-EQ "lid") |

### <a name="properties-of-type-string-collection"></a>Eigenschappen van het type teken reeks verzameling

| Eigenschappen | Toegestane waarden | Gebruik |
| --- | --- | --- |
| otherMails |Wille keurige teken reeks waarde |(User. otherMails-bevat "alias@domain") |
| proxyAddresses |SMTP: alias@domain SMTP: alias@domain |(User. proxyAddresses-bevat "SMTP: alias@domain") |

Zie [regels voor apparaten](#rules-for-devices)voor de eigenschappen die worden gebruikt voor apparaat regels.

## <a name="supported-expression-operators"></a>Ondersteunde expressie operators

De volgende tabel geeft een lijst van alle ondersteunde Opera tors en hun syntaxis voor één expressie. Opera tors kunnen worden gebruikt met of zonder het afbreek streepje (-).

| Operator | Syntaxis |
| --- | --- |
| Niet gelijk aan |-ne |
| Is gelijk aan |-EQ |
| Begint niet met |-notStartsWith |
| Begint met |-startsWith |
| Bevat niet |-notContains |
| Contains |-bevat |
| Niet overeen |-notMatch |
| overeen met |-overeenkomst |
| Naast | -in |
| Niet in | -notIn |

### <a name="using-the--in-and--notin-operators"></a>De Opera tors-in en notIn gebruiken

Als u de waarde van een gebruikers kenmerk wilt vergelijken met een aantal verschillende waarden, kunt u de Opera tors-in of-notIn gebruiken. Gebruik de vier Kante symbolen ' [' en '] ' om de lijst met waarden te starten en te beëindigen.

 In het volgende voor beeld wordt de expressie geëvalueerd als waar als de waarde van gebruiker. Department gelijk is aan een van de waarden in de lijst:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>De operator-match gebruiken 
De operator **-match** wordt gebruikt voor het vergelijken van een reguliere expressie. Voorbeelden:

```
user.displayName -match "Da.*"   
```
Da, DAV, David evalueert naar waar, aDa resulteert in ONWAAR.

```
user.displayName -match ".*vid"
```
David evalueert naar waar, da resulteert in ONWAAR.

## <a name="supported-values"></a>Ondersteunde waarden

De waarden die in een expressie worden gebruikt, kunnen bestaan uit verschillende typen, waaronder:

* Tekenreeksen
* Boolean – True, False
* Rijnummers
* Matrices – nummer matrix, teken reeks matrix

Wanneer u een waarde in een expressie opgeeft, is het belang rijk dat u de juiste syntaxis gebruikt om fouten te voor komen. Enkele syntaxis tips zijn:

* Dubbele aanhalings tekens zijn optioneel tenzij de waarde een teken reeks is.
* Teken reeks-en regex-bewerkingen zijn niet hoofdletter gevoelig.
* Wanneer een teken reeks waarde dubbele aanhalings tekens bevat, moeten beide aanhalings tekens worden voorafgegaan door het \`-teken, bijvoorbeeld User. Department-EQ \` "Sales @ no__t-2" is de juiste syntaxis als "Sales" de waarde is.
* U kunt ook null-controles uitvoeren, waarbij null als waarde wordt gebruikt, bijvoorbeeld `user.department -eq null`.

### <a name="use-of-null-values"></a>Gebruik van Null-waarden

Als u een null-waarde in een regel wilt opgeven, kunt u de *Null* -waarde gebruiken. 

* Gebruik-EQ of-ne bij het vergelijken van de *Null* -waarde in een expressie.
* Gebruik alleen aanhalings tekens rondom het woord *Null* als u wilt dat het wordt geïnterpreteerd als een letterlijke teken reeks waarde.
* De operator-Not kan niet worden gebruikt als een vergelijkings operator voor Null. Als u het gebruikt, wordt er een fout bericht weer geven of u null of $null gebruikt.

De juiste manier om te verwijzen naar de null-waarde is als volgt:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regels met meerdere expressies

Een regel voor groepslid maatschap kan bestaan uit meer dan één expressie die is verbonden door de Opera tors-en,-or en-not. Logische Opera tors kunnen ook worden gebruikt in combi natie. 

Hier volgen enkele voor beelden van goed geconstrueerde lidmaatschaps regels met meerdere expressies:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Operator prioriteit

Alle Opera tors worden hieronder weer gegeven in volg orde van prioriteit van hoog naar laag. Opera tors op dezelfde regel zijn gelijk aan de prioriteit:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Hier volgt een voor beeld van de prioriteit van Opera tors waarbij twee expressies worden geëvalueerd voor de gebruiker:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Haakjes zijn alleen nodig wanneer de prioriteit niet voldoet aan uw vereisten. Als u bijvoorbeeld wilt dat de afdeling eerst wordt geëvalueerd, ziet u in het volgende voor beeld hoe haakjes kunnen worden gebruikt om de volg orde te bepalen:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regels met complexe expressies

Een lidmaatschaps regel kan bestaan uit complexe expressies waarbij de eigenschappen, Opera tors en waarden meer complexe formulieren hebben. Expressies worden beschouwd als complex wanneer een van de volgende voor waarden waar is:

* De eigenschap bestaat uit een verzameling waarden; met name eigenschappen met meerdere waarden
* De expressies gebruiken de Opera tors-any en-all
* De waarde van de expressie kan zelf een of meer expressies zijn

## <a name="multi-value-properties"></a>Eigenschappen van meerdere waarden

Eigenschappen van meerdere waarden zijn verzamelingen van objecten van hetzelfde type. Ze kunnen worden gebruikt om lidmaatschaps regels te maken met behulp van de logische Opera tors-any en-all.

| Eigenschappen | Waarden | Gebruik |
| --- | --- | --- |
| assignedPlans | Elk object in de verzameling bevat de volgende teken reeks eigenschappen: capabilityStatus, service, servicePlanId |User. assignedPlans-any (assignedPlan. servicePlanId-EQ "efb87545-963c-4e0d-99df-69c6916d9eb0"-en assignedPlan. capabilityStatus-EQ "enabled") |
| proxyAddresses| SMTP: alias@domain SMTP: alias@domain | (User. proxyAddresses-any (\_-bevat "Contoso")) |

### <a name="using-the--any-and--all-operators"></a>De Opera tors-any en-all gebruiken

U kunt-alle Opera tors en gebruiken om een voor waarde toe te passen op een of alle items in de verzameling.

* -any (voldaan wanneer ten minste één item in de verzameling overeenkomt met de voor waarde)
* -alle (voldaan wanneer alle items in de verzameling overeenkomen met de voor waarde)

#### <a name="example-1"></a>Voor beeld 1

assignedPlans is een eigenschap met meerdere waarden waarmee alle service plannen worden weer gegeven die aan de gebruiker zijn toegewezen. Met de volgende expressie selecteert u gebruikers die het service plan Exchange Online (abonnement 2) hebben (als GUID-waarde) die ook de status ingeschakeld heeft:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Een regel zoals deze kan worden gebruikt voor het groeperen van alle gebruikers voor wie een Office 365 (of een andere micro soft online service)-mogelijkheid is ingeschakeld. Vervolgens kunt u een set beleids regels Toep assen op de groep.

#### <a name="example-2"></a>Voor beeld 2

Met de volgende expressie worden alle gebruikers geselecteerd die een service plan hebben dat is gekoppeld aan de intune-service (geïdentificeerd door de service naam ' SCO '):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>De syntaxis van het onderstrepings teken (@no__t 0) gebruiken

De syntaxis van het onderstrepings teken (@no__t 0) komt overeen met een specifieke waarde in een van de eigenschappen van teken reeks verzameling met meerdere waarden om gebruikers of apparaten aan een dynamische groep toe te voegen. Het wordt gebruikt met de Opera tors-any of-all.

Hier volgt een voor beeld van het gebruik van het onderstrepings teken (\_) in een regel om leden toe te voegen op basis van User. proxyAddress attribuut (de functie werkt hetzelfde voor User. otherMails). Met deze regel wordt een gebruiker met een proxy adres dat ' Contoso ' bevat aan de groep toegevoegd.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Andere eigenschappen en algemene regels

### <a name="create-a-direct-reports-rule"></a>Een regel voor directe rapporten maken

U kunt een groep maken met alle directe ondergeschikten van een manager. Wanneer de directe rapporten van de manager in de toekomst worden gewijzigd, wordt het lidmaatschap van de groep automatisch aangepast.

De regel voor directe rapporten is samengesteld met behulp van de volgende syntaxis:

```
Direct Reports for "{objectID_of_manager}"
```

Hier volgt een voor beeld van een geldige regel waarbij ' 62e19b97-8b3d-4d4a-A106-4ce66896a863 ' de objectID van de Manager is:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Aan de hand van de volgende tips kunt u de regel op de juiste manier gebruiken.

- De **manager-id** is de object-id van de Manager. Dit kan worden gevonden in het **profiel**van de Manager.
- Zorg ervoor dat de eigenschap **Manager** correct is ingesteld voor gebruikers in uw Tenant om de regel te laten werken. U kunt de huidige waarde in het **profiel**van de gebruiker controleren.
- Deze regel ondersteunt alleen de directe ondergeschikten van de Manager. Met andere woorden, u kunt geen groep maken met de directe rapporten van de Manager *en* de bijbehorende rapporten.
- Deze regel kan niet worden gecombineerd met andere lidmaatschaps regels.

### <a name="create-an-all-users-rule"></a>Een regel voor ' alle gebruikers ' maken

U kunt een groep met alle gebruikers binnen een Tenant maken met behulp van een lidmaatschaps regel. Wanneer gebruikers in de toekomst worden toegevoegd of verwijderd uit de Tenant, wordt het lidmaatschap van de groep automatisch aangepast.

De regel ' alle gebruikers ' is gemaakt met behulp van een enkele expressie met behulp van de-ne-operator en de null-waarde. Deze regel voegt B2B-gast gebruikers toe, evenals gebruikers van leden aan de groep.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>Een regel voor alle apparaten maken

U kunt een groep met alle apparaten binnen een Tenant maken met behulp van een lidmaatschaps regel. Wanneer apparaten in de toekomst worden toegevoegd of verwijderd uit de Tenant, wordt het lidmaatschap van de groep automatisch aangepast.

De regel ' alle apparaten ' is gemaakt met behulp van een enkele expressie met behulp van de-ne-operator en de null-waarde:

```
device.objectid -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Extensie-eigenschappen en aangepaste extensie-eigenschappen

Extensie kenmerken en aangepaste extensie-eigenschappen worden ondersteund als teken reeks eigenschappen in dynamische lidmaatschaps regels. Extensie kenmerken worden gesynchroniseerd vanaf de on-premises venster server AD en hebben de indeling ' ExtensionAttributeX ', waarbij X gelijk is aan 1-15. Hier volgt een voor beeld van een regel die gebruikmaakt van een uitbreidings kenmerk als een eigenschap:

```
(user.extensionAttribute15 -eq "Marketing")
```

Aangepaste extensie-eigenschappen worden gesynchroniseerd vanuit on-premises Windows Server AD of vanuit een verbonden SaaS-toepassing en hebben de indeling van `user.extension_[GUID]_[Attribute]`, waarbij:

* [GUID] is de unieke id in azure AD voor de toepassing die de eigenschap heeft gemaakt in azure AD
* [Kenmerk] is de naam van de eigenschap die is gemaakt

Een voor beeld van een regel die gebruikmaakt van een aangepaste extensie-eigenschap is:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

De naam van de aangepaste eigenschap kan worden gevonden in de map door de eigenschap Graph Explorer te gebruiken om een query uit te zoeken naar de eigenschaps naam. U kunt nu ook de koppeling **aangepaste extensie eigenschappen ophalen** selecteren in de opbouw functie voor de groep voor dynamische gebruikers om een unieke app-id in te voeren en de volledige lijst met aangepaste extensie-eigenschappen te ontvangen die moeten worden gebruikt bij het maken van een dynamische lidmaatschaps regel. Deze lijst kan ook worden vernieuwd om nieuwe aangepaste extensie-eigenschappen voor die app te krijgen.

## <a name="rules-for-devices"></a>Regels voor apparaten

U kunt ook een regel maken waarmee objecten worden geselecteerd voor lidmaatschap van een groep. U kunt niet zowel gebruikers als groeps leden hebben. Het kenmerk **organizationalUnit** wordt niet meer weer gegeven en mag niet worden gebruikt. Deze teken reeks wordt in specifieke gevallen door intune ingesteld, maar wordt niet herkend door Azure AD, zodat er geen apparaten worden toegevoegd aan groepen op basis van dit kenmerk.

> [!NOTE]
> systemlabels is een alleen-lezen kenmerk dat niet kan worden ingesteld met intune.
>
> Voor Windows 10 is de juiste indeling van het kenmerk deviceOSVersion als volgt: (Device. deviceOSVersion-EQ "10,0 (17763)"). De opmaak kan worden gevalideerd met de Power shell-cmdlet Get-MsolDevice.

De volgende kenmerken van apparaten kunnen worden gebruikt.

 Kenmerk apparaat  | Waarden | Voorbeeld
 ----- | ----- | ----------------
 AccountEnabled | waar onwaar | (Device. accountEnabled-EQ True)
 displayName | Wille keurige teken reeks waarde |(apparaat. displayName-EQ "Rob iPhone")
 deviceOSType | Wille keurige teken reeks waarde | (Device. deviceOSType-EQ "iPad")-of (Device. deviceOSType-EQ "iPhone")<br>(Device. deviceOSType-bevat "AndroidEnterprise")<br>(Device. deviceOSType-EQ "AndroidForWork")
 deviceOSVersion | Wille keurige teken reeks waarde | (Device. deviceOSVersion-EQ "9,1")
 deviceCategory | een geldige naam voor een apparaatcategorie | (Device. deviceCategory-EQ "BYOD")
 deviceManufacturer | Wille keurige teken reeks waarde | (Device. deviceManufacturer-EQ "Samsung")
 deviceModel | Wille keurige teken reeks waarde | (Device. deviceModel-EQ "iPad-Air")
 deviceOwnership | Persoonlijk, bedrijf, onbekend | (apparaat. deviceOwnership-EQ "bedrijf")
 enrollmentProfileName | Apple-inschrijvings profiel voor apparaten, registratie van apparaten-bedrijfs apparaat-id's (Android-kiosk) of Windows auto pilot-profiel naam | (apparaat. enrollmentProfileName-EQ "DEP iPhones")
 isRooted | waar onwaar | (Device. isRooted-EQ True)
 managementType | MDM (voor mobiele apparaten)<br>PC (voor computers die worden beheerd door de intune-PC-agent) | (Device. managementType-EQ "MDM")
 deviceId | een geldige Azure AD-apparaat-ID | (apparaat. deviceId-EQ "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Id | een geldige Azure AD-object-ID |  (apparaat. objectId-EQ 76ad43c9-32c5-45e8-a272-7b58b58f596d ")
 systemLabels | een teken reeks die overeenkomt met de eigenschap van het intune-apparaat voor het labelen van moderne werkplek apparaten | (Device. systemLabels-bevat "M365Managed")

> [!Note]  
> Voor de deviceOwnership bij het maken van dynamische groepen voor apparaten moet u de waarde instellen op ' bedrijf '. Op intune wordt het eigendom van het apparaat in plaats van het bedrijf weer gegeven. Raadpleeg [OwnerTypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

In deze artikelen vindt u aanvullende informatie over groepen in Azure Active Directory.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een nieuwe groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-create-rule.md)
