---
title: 'Azure AD Connect: Beheerders van AD DS-connectoraccountmachtigingen configureren | Microsoft Documenten'
description: In dit document wordt beschreven hoe u het AD DS-connector-account configureert met de nieuwe ADSyncConfig PowerShell-module
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72515815"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Beheerders van AD DS-connectoraccountmachtigingen configureren 

De PowerShell-module met de naam [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) is geïntroduceerd met build 1.1.880.0 (uitgebracht in augustus 2018) met een verzameling cmdlets waarmee u de juiste Active Directory-machtigingen voor uw Azure AD Connect-implementatie configureren. 

## <a name="overview"></a>Overzicht 
De volgende PowerShell-cmdlets kunnen worden gebruikt om Active Directory-machtigingen in te stellen voor het AD DS-connectoraccount, voor elke functie die u selecteert om in Azure AD Connect in te schakelen. Als u problemen wilt voorkomen, moet u Active Directory-machtigingen van tevoren voorbereiden wanneer u Azure AD Connect wilt installeren met behulp van een aangepast domeinaccount om verbinding te maken met uw forest. Deze ADSyncConfig-module kan ook worden gebruikt om machtigingen te configureren nadat Azure AD Connect is geïmplementeerd.

![overzicht van ad ds-account](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Voor azure AD Connect Express-installatie wordt een automatisch gegenereerd account (MSOL_nnnnnnnnnn) gemaakt in Active Directory met alle benodigde machtigingen, zodat u deze ADSyncConfig-module niet hoeft te gebruiken, tenzij u machtigingen hebt geblokkeerd overerving op organisatie-eenheden of op specifieke Active Directory-objecten die u wilt synchroniseren met Azure AD. 
 
### <a name="permissions-summary"></a>Overzicht van bevoegdheden 
In de volgende tabel vindt u een overzicht van de machtigingen die nodig zijn voor AD-objecten: 

| Functie | Machtigingen |
| --- | --- |
| ms-DS-ConsistencyGuid-functie |Lees- en schrijfmachtigingen voor het kenmerk ms-DS-ConsistencyGuid dat is gedocumenteerd in [ontwerpconcepten - Ms-DS-ConsistencyGuid gebruiken als sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Wachtwoordhashsynchronisatie |<li>Mapwijzigingen repliceren</li>  <li>Mapwijzigingen repliceren Alles |
| Hybride implementatie van Exchange |Lees- en schrijfmachtigingen voor de kenmerken die zijn gedocumenteerd in [hybride terugschrijftekst](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) van Exchange voor gebruikers, groepen en contactpersonen. |
| Openbare map van Exchange Mail |Lees machtigingen voor de kenmerken die zijn gedocumenteerd in [de openbare map van Exchange Mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) voor openbare mappen. | 
| Wachtwoord terugschrijven |Machtigingen voor lezen en schrijven voor de kenmerken die zijn gedocumenteerd in [Aan de slag met wachtwoordbeheer](../authentication/howto-sspr-writeback.md) voor gebruikers. |
| Apparaat terugschrijven |Machtigingen voor lezen en schrijven voor apparaatobjecten en containers die zijn gedocumenteerd in [apparaatterugschrijftijd](how-to-connect-device-writeback.md). |
| Groep terugschrijven |Groepsobjecten voor gesynchroniseerde **Office 365-groepen**lezen, maken, bijwerken en verwijderen .  Zie [Groepsterugschrijven](how-to-connect-preview.md#group-writeback)voor meer informatie .|

## <a name="using-the-adsyncconfig-powershell-module"></a>De ADSyncConfig PowerShell-module gebruiken 
De ADSyncConfig-module vereist de [Remote Server Administration Tools (RSAT) voor AD DS,](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) omdat deze afhankelijk is van de AD DS PowerShell-module en -hulpprogramma's. Als u RSAT voor AD DS wilt installeren, opent u een Windows PowerShell-venster met 'Als administrator uitvoeren' en voert u het uitvoeren uit: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configureren](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>U het bestand **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** ook kopiëren naar een domeincontroller waarvan RSAT al is geïnstalleerd voor AD DS en deze PowerShell-module vanaf daar gebruiken.

Als u de ADSyncConfig wilt gebruiken, moet u de module in een Windows PowerShell-venster laden: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Om alle cmdlets in deze module te controleren u typen:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Selecteren](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Elke cmdlet heeft dezelfde parameters om het AD DS-connectoraccount en een knop adminsdhouder in te voeren. Als u uw AD DS-connectoraccount wilt opgeven, u de accountnaam en het domein opgeven, of alleen de dn (Account Distinguished Name),

Bijvoorbeeld:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Of;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Zorg ervoor `<ADAccountName>`dat `<ADDomainName>` `<ADAccountDN>` u vervangt, en met de juiste waarden voor uw omgeving.

Als u geen machtigingen voor de container AdminSDHolder wilt wijzigen, gebruikt u de schakelaar `-SkipAdminSdHolders`. 

Standaard proberen alle cmdlets voor setmachtigingen AD DS-machtigingen in te stellen op de root van elk domein in het forest, wat betekent dat de gebruiker die de PowerShell-sessie uitvoert, domeinbeheerdersrechten vereist voor elk domein in het forest.  Vanwege deze vereiste wordt aanbevolen om een Enterprise Administrator uit de Forest-root te gebruiken. Als uw Azure AD Connect-implementatie meerdere AD DS-connectors heeft, moet deze dezelfde cmdlet uitvoeren op elk forest met een AD DS-connector. 

U ook machtigingen instellen voor een specifieke organisatie-eenheid `-ADobjectDN` of AD DS-object met behulp van de parameter gevolgd door de DN van het doelobject waar u machtigingen wilt instellen. Bij het gebruik van een doel-ADobjectDN stelt de cmdlet alleen machtigingen in voor dit object en niet op de domeinroot of de container AdminSDHolder. Deze parameter kan handig zijn als u bepaalde OE's of AD DS-objecten met een overerving van machtigingen hebt uitgeschakeld (zie AD DS-objecten zoeken met overerving met toestemming uitgeschakeld) 

Uitzonderingen op deze algemene `Set-ADSyncRestrictedPermissions` parameters zijn de cmdlet die wordt gebruikt om de machtigingen `Set-ADSyncPasswordHashSyncPermissions` op het AD DS-connectoraccount zelf in te stellen, en de cmdlet `-ObjectDN` `-SkipAdminSdHolders` omdat de machtigingen die nodig zijn voor Password Hash Sync alleen bij de domeinhoofdse worden ingesteld, vandaar dat deze cmdlet de of parameters niet bevat.

### <a name="determine-your-ad-ds-connector-account"></a>Uw AD DS-connectoraccount bepalen 
Als Azure AD Connect al is geïnstalleerd en u wilt controleren wat het AD DS-connectoraccount is dat momenteel wordt gebruikt door Azure AD Connect, u de cmdlet uitvoeren: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>AD DS-objecten zoeken met overerving van machtigingen uitgeschakeld 
Als u wilt controleren of er een AD DS-object is met een overerving met toestemming uitgeschakeld, u het gewenste uitvoeren: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Standaard zoekt deze cmdlet alleen naar OU's met een overerving met uitgeschakelde waarde, maar u andere AD DS-objectklassen opgeven in `-ObjectClass` parameter of '*' gebruiken voor alle objectklassen, als volgt: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>AD DS-machtigingen van een object weergeven 
U de onderstaande cmdlet gebruiken om de lijst met machtigingen weer te geven die momenteel zijn ingesteld op een Active Directory-object door de DistinguishedName op te geven: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>De machtigingen van een AD DS Connector-account configureren 
 
### <a name="configure-basic-read-only-permissions"></a>Basismachtigingen alleen-lezen configureren 
Voer het op voorlezen instellen van alleen-lezenmachtigingen voor het AD DS-connector-account wanneer u geen Azure AD Connect-functie gebruikt: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Met deze cmdlet worden de volgende machtigingen ingesteld: 
 

|Type |Name |Toegang |Van toepassing op| 
|-----|-----|-----|-----|
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen |Objecten van het afstammelingenapparaat| 
|Toestaan |AD DS-connectoraccount|Alle eigenschappen lezen |Afstammeling InetOrgPerson-objecten| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen |Afstammelingcomputerobjecten| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen |Afstammeling foreignSecurityPrincipal objecten| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen |Objecten van afstammelinggroep| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen |Afstammelinggebruikersobjecten| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen |Objecten van nakomeling contact| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Machtigingen voor MS-DS-Consistency-Guid configureren 
Machtigingen instellen voor het AD DS Connector-account wanneer u het kenmerk ms-Ds-Consistency-Guid als bronanker (ook bekend als "Laat Azure het bronanker voor mij beheren) instelt, voert het volgende uit: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

of; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Name |Toegang |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan|AD DS-connectoraccount|Lezen/schrijven, eigenschap|Afstammelinggebruikersobjecten|

### <a name="permissions-for-password-hash-synchronization"></a>Machtigingen voor synchronisatie van wachtwoordhash 
Voer het als nodig op om machtigingen in te stellen voor het AD DS-connectoraccount bij het gebruik van wachtwoordhashsynchronisatie: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


of; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Name |Toegang |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS-connectoraccount |Mapwijzigingen repliceren |Alleen dit object (domeinhoofdhoofd)| 
|Toestaan |AD DS-connectoraccount |Mapwijzigingen repliceren Alles |Alleen dit object (domeinhoofdhoofd)| 
  
### <a name="permissions-for-password-writeback"></a>Machtigingen voor terugschrijven van wachtwoorden 
Voer het als nodig op om machtigingen in te stellen voor het AD DS Connector-account wanneer u Wachtwoord writeback gebruikt: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Name |Toegang |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS-connectoraccount |Wachtwoord opnieuw instellen |Afstammelinggebruikersobjecten| 
|Toestaan |AD DS-connectoraccount |Lockout Time van eigenschapschrijven |Afstammelinggebruikersobjecten| 
|Toestaan |AD DS-connectoraccount |Eigenschap pwdLastSet schrijven |Afstammelinggebruikersobjecten| 

### <a name="permissions-for-group-writeback"></a>Machtigingen voor groepsafschrijving 
Voer het als nodig op om machtigingen in te stellen voor het AD DS-connectoraccount wanneer u Groepsafschrijving gebruikt: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
of; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Name |Toegang |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS-connectoraccount |Algemeen lezen/schrijven |Alle kenmerken van objecttekstgroep en subobjecten| 
|Toestaan |AD DS-connectoraccount |Onderliggend object maken/verwijderen |Alle kenmerken van objecttekstgroep en subobjecten| 
|Toestaan |AD DS-connectoraccount |Boomobjecten verwijderen/verwijderen|Alle kenmerken van objecttekstgroep en subobjecten|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Machtigingen voor hybride implementatie van Exchange 
Voer het als nodig op om machtigingen in te stellen voor het AD DS-connector-account wanneer u de implementatie van Exchange Hybrid gebruikt: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Met deze cmdlet worden de volgende machtigingen ingesteld:  
 

|Type |Name |Toegang |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen/schrijven |Afstammelinggebruikersobjecten| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen/schrijven |Afstammeling InetOrgPerson-objecten| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen/schrijven |Objecten van afstammelinggroep| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen/schrijven |Objecten van nakomeling contact| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Machtigingen voor openbare mappen van Exchange Mail (voorbeeld) 
Voer het als nodig op om machtigingen in te stellen voor het AD DS-connectoraccount wanneer u de functie Openbare mappen van Exchange Mail gebruikt: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Name |Toegang |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS-connectoraccount |Alle eigenschappen lezen |Objecten van nakomeling PublicFolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Machtigingen voor het AD DS-connectoraccount beperken 
Met dit PowerShell-script worden de machtigingen voor het AD-connectoraccount als parameter aangescherpt. Het aanscherpen van machtigingen omvat de volgende stappen: 

- Overerving op het opgegeven object uitschakelen 
- Verwijder alle AME's op het specifieke object, met uitzondering van AME's die specifiek zijn voor SELF, omdat we de standaardmachtigingen intact willen houden als het gaat om SELF. 
 
  De parameter -ADConnectorAccountDN is het AD-account waarvan de machtigingen moeten worden aangescherpt. Dit is meestal het MSOL_nnnnnnnnnnnn-domeinaccount dat is geconfigureerd in de AD DS-connector (zie Uw AD DS-connectoraccount bepalen). De parameter -Credential is nodig om het administratoraccount op te geven dat de benodigde bevoegdheden heeft om Active Directory-machtigingen voor het doel-AD-object te beperken. Dit is meestal de Enterprise- of Domeinbeheerder.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Bijvoorbeeld: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Name |Toegang |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |SYSTEEM |Volledig beheer |Dit object 
|Toestaan |Ondernemingsadministrators |Volledig beheer |Dit object 
|Toestaan |Domeinadministrators |Volledig beheer |Dit object 
|Toestaan |Beheerders |Volledig beheer |Dit object 
|Toestaan |Domeincontrollers voor bedrijfsdomeinen |Lijstinhoud |Dit object 
|Toestaan |Domeincontrollers voor bedrijfsdomeinen |Alle eigenschappen lezen |Dit object 
|Toestaan |Domeincontrollers voor bedrijfsdomeinen |Leesmachtigingen |Dit object 
|Toestaan |Geverifieerde gebruikers |Lijstinhoud |Dit object 
|Toestaan |Geverifieerde gebruikers |Alle eigenschappen lezen |Dit object 
|Toestaan |Geverifieerde gebruikers |Leesmachtigingen |Dit object 

## <a name="next-steps"></a>Volgende stappen
- [Azure AD Connect: accounts en machtigingen](reference-connect-accounts-permissions.md)
- [Express-installatie](how-to-connect-install-express.md)
- [Aangepaste installatie](how-to-connect-install-custom.md)
- [ADSyncConfig-verwijzing](reference-connect-adsyncconfig.md)

