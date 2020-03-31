---
title: 'Azure AD Connect: ADSyncConfig PowerShell-referentie | Microsoft Documenten'
description: Dit document bevat referentie-informatie voor de ADSyncConfig.psm1 PowerShell-module.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381192"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig PowerShell-referentie
De volgende documentatie bevat referentiegegevens voor de ADSyncConfig.psm1 PowerShell-module die is opgenomen in Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SAMENVATTING
Hiermee krijgt u de accountnaam en het domein dat is geconfigureerd in elke AD-connector

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>BESCHRIJVING
Deze functie maakt gebruik van de cmdlet 'Get-ADSyncConnector' die aanwezig is in AAD Connect om uit Connectivity Parameters een tabel met het AD Connector-account(s) op te halen.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SAMENVATTING
Hiermee wordt AD-objecten met overerving van machtigingen uitgeschakeld

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Zoekt in AD vanaf de parameter SearchBase en retourneert alle objecten, gefilterd op objectklasse, waarop de ACL-overerving momenteel is uitgeschakeld.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>VOORBEELD 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>VOORBEELD 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARAMETERS

#### <a name="-searchbase"></a>-SearchBase
De SearchBase voor de LDAP-query die een onderscheiden AD-domein of een FQDN kan zijn

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectKlasse
De klasse van de objecten die moeten zoeken en die '*' kunnen zijn (voor elke objectklasse), 'gebruiker', 'groep', 'container', enz. Standaard zoekt deze functie naar objectklasse 'organizationalUnit'.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncbasicreadpermissions"></a>AdSyncBasicReadPermissions instellen

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en -domein voor basisleesmachtigingen.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Set-ADSyncBasicReadPermissions geeft vereiste machtigingen aan het AD-synchronisatieaccount, waaronder het volgende:
1.
Toegang tot eigenschap lezen op alle kenmerken voor alle nakomelingcomputerobjecten
2.
Toegang tot eigenschap lezen op alle kenmerken voor alle objecten van het nakomelingapparaat
3.
Toegang tot eigendom lezen op alle kenmerken voor alle nakomelende foreignsecurityprincipal-objecten
5.
Toegang tot eigenschap lezen op alle kenmerken voor alle gebruikersobjecten van afstammelingen
6.
Eigenschapstoegang lezen op alle kenmerken voor alle afstammelinginetorgperson-objecten
7.
Eigenschapstoegang lezen op alle kenmerken voor alle afstammelingengroepobjecten
8.
Eigenschapstoegang lezen op alle kenmerken voor alle objecten met afstammelingencontact

Deze machtigingen worden toegepast op alle domeinen in het forest.
Optioneel u een parameter DistinguishedName in ADobjectDN opgeven om deze machtigingen alleen op dat AD-object in te stellen (inclusief overerving naar subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOORBEELD 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOORBEELD 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountNaam
De naam van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomein
Het domein van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DistinguishedName van het Active Directory-account dat wordt of zal worden gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName van het doel-AD-object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele parameter om aan te geven of de container AdminSDHolder niet met deze machtigingen moet worden bijgewerkt

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncexchangehybridpermissions"></a>Machtigingen voor ADSyncExchange-hybriden instellen

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en -domein voor de functie Hybride exchange.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Set-ADSyncExchangeHybridPermissions geeft vereiste machtigingen aan het AD-synchronisatieaccount, waaronder het volgende:
1.
Toegang tot de eigenschap Lezen/schrijven op alle kenmerken voor alle afstammelingengebruikersobjecten
2.
Toegang tot de eigenschap Lezen/schrijven op alle kenmerken voor alle afstammelinginetorgperson-objecten
3.
Toegang tot de eigenschap Lezen/schrijven op alle kenmerken voor alle afstammelingengroepobjecten
4.
Toegang tot de eigenschap Lezen/schrijven op alle kenmerken voor alle objecten met afstammelingencontact

Deze machtigingen worden toegepast op alle domeinen in het forest.
Optioneel u een parameter DistinguishedName in ADobjectDN opgeven om deze machtigingen alleen op dat AD-object in te stellen (inclusief overerving naar subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOORBEELD 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOORBEELD 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountNaam
De naam van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomein
Het domein van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DistinguishedName van het Active Directory-account dat wordt of zal worden gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName van het doel-AD-object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele parameter om aan te geven of de container AdminSDHolder niet met deze machtigingen moet worden bijgewerkt

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Machtigingen voor ADSyncExchangeMailPublicFolder instellen

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en -domein voor de functie Openbare map van Exchange Mail.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Machtigingen voor set-ADSyncExchangeMailPublicFoldergeeft vereiste machtigingen aan het AD-synchronisatieaccount, waaronder het volgende:
1.
Toegang tot eigenschap lezen op alle kenmerken voor alle objecten van de nakomeling openbare map

Deze machtigingen worden toegepast op alle domeinen in het forest.
Optioneel u een parameter DistinguishedName in ADobjectDN opgeven om deze machtigingen alleen op dat AD-object in te stellen (inclusief overerving naar subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOORBEELD 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOORBEELD 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountNaam
De naam van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomein
Het domein van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DistinguishedName van het Active Directory-account dat wordt of zal worden gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName van het doel-AD-object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele parameter om aan te geven of de container AdminSDHolder niet met deze machtigingen moet worden bijgewerkt

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Consistentieguidpermissions instellen-ADSyncMsDs

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en -domein voor de mS-DS-ConsistencyGuid-functie.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Set-ADSyncMsDsConsistencyGuidPermissions geeft vereiste machtigingen aan het AD-synchronisatieaccount, waaronder het volgende:
1.
Toegang tot de eigenschap Lezen/schrijven op het kenmerk mS-DS-ConsistencyGuid voor alle gebruikersobjecten van afstammelingen

Deze machtigingen worden toegepast op alle domeinen in het forest.
Optioneel u een parameter DistinguishedName in ADobjectDN opgeven om deze machtigingen alleen op dat AD-object in te stellen (inclusief overerving naar subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOORBEELD 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOORBEELD 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountNaam
De naam van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomein
Het domein van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DistinguishedName van het Active Directory-account dat wordt of zal worden gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName van het doel-AD-object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele parameter om aan te geven of de container AdminSDHolder niet met deze machtigingen moet worden bijgewerkt

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en -domein voor wachtwoordhashsynchronisatie.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Set-ADSyncPasswordHashSyncPermissions geeft vereiste machtigingen aan het AD-synchronisatieaccount, waaronder het volgende:
1.
Mapwijzigingen repliceren
2.
Mapwijzigingen repliceren Alles

Deze machtigingen worden gegeven aan alle domeinen in het forest.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountNaam
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomein
Het domein van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DistinguishedName van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en -domein voor het terugschrijven van wachtwoorden vanuit Azure AD.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Set-ADSyncPasswordWritebackPermissions geeft vereiste machtigingen aan het AD-synchronisatieaccount, waaronder het volgende:
1.
Wachtwoord opnieuw instellen op gebruikersobjecten van afstammelingen
2.
Eigenschapstoegang schrijven op het kenmerk LockoutTime voor alle gebruikersobjecten van afstammelingen
3.
Eigenschapstoegang schrijven op het kenmerk PWDLastSet voor alle gebruikersobjecten van afstammelingen

Deze machtigingen worden toegepast op alle domeinen in het forest.
Optioneel u een parameter DistinguishedName in ADobjectDN opgeven om deze machtigingen alleen op dat AD-object in te stellen (inclusief overerving naar subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOORBEELD 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOORBEELD 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountNaam
De naam van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomein
Het domein van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DistinguishedName van het Active Directory-account dat wordt of zal worden gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName van het doel-AD-object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele parameter om aan te geven of de container AdminSDHolder niet met deze machtigingen moet worden bijgewerkt

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncrestrictedpermissions"></a>Machtigingen voor SET-ADSyncRestrictedS

### <a name="synopsis"></a>SAMENVATTING
Verscherp machtigingen voor een AD-object dat anders niet is opgenomen in een ad-beveiligde beveiligingsgroep.
Een typisch voorbeeld is het AD Connect-account (MSOL) dat door AAD Connect automatisch is gemaakt.
Dit account heeft repliceren machtigingen op alle domeinen, maar kan gemakkelijk worden gecompromitteerd als het niet is beschermd.

### <a name="syntax"></a>SYNTAXIS

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Set-ADSyncRestrictedPermissions scherpt de machtigingen aan die het opgegeven account hebben.
Het aanscherpen van machtigingen omvat de volgende stappen:
1.
Overerving op het opgegeven object uitschakelen
2.
Verwijder alle AME's op het specifieke object, met uitzondering van AME's die specifiek zijn voor SELF.
We willen de standaard machtigingen intact te houden als het gaat om SELF.
3.
Wijs deze specifieke machtigingen toe:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName van het Active Directory-account waarvan de machtigingen moeten worden aangescherpt.
Dit is meestal het MSOL_nnnnnnnnnn-account of een aangepast domeinaccount dat is geconfigureerd in uw AD-connector.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credential"></a>-Credential
Beheerdersreferenties die de nodige bevoegdheden hebben om de machtigingen voor het ADConnectorAccountDN-account te beperken. Dit is meestal de Enterprise- of Domeinbeheerder. Gebruik de volledig gekwalificeerde domeinnaam van het beheerdersaccount om fouten in het opzoeken van accounts te voorkomen.
Voorbeeld: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-CredentialValidation uitschakelen
Wanneer DisableCredentialValidation wordt gebruikt, controleert de functie niet of de referenties die zijn opgegeven in -Referentie geldig zijn in AD en of het opgegeven account de nodige bevoegdheden heeft om de machtigingen op het ADConnectorAccountDN-account te beperken.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en -domein voor groepsterugschrijven vanuit Azure AD.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De functie Set-ADSyncUnifiedGroupWritebackPermissions geeft vereiste machtigingen voor het AD-synchronisatieaccount, waaronder het volgende:
1.
Algemene teksttekst lezen/schrijven, verwijderen, structuur verwijderen en onderliggend verwijderen voor alle typen groepsobject en subobjecten

Deze machtigingen worden toegepast op alle domeinen in het forest.
Optioneel u een parameter DistinguishedName in ADobjectDN opgeven om deze machtigingen alleen op dat AD-object in te stellen (inclusief overerving naar subobjecten).
In dit geval is ADobjectDN de distinguished name of the Container die u wilt koppelen aan de GroupWriteback-functie.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOORBEELD 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOORBEELD 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountNaam
De naam van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomein
Het domein van het Active Directory-account dat door Azure AD Connect Sync wordt of zal worden gebruikt om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DistinguishedName van het Active Directory-account dat wordt of zal worden gebruikt door Azure AD Connect Sync om objecten in de map te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName van het doel-AD-object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele parameter om aan te geven of de container AdminSDHolder niet met deze machtigingen moet worden bijgewerkt

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="show-adsyncadobjectpermissions"></a>Toon-ADSyncADObjectMachtigingen

### <a name="synopsis"></a>SAMENVATTING
Hiermee worden machtigingen van een opgegeven AD-object weergegeven.

### <a name="syntax"></a>SYNTAXIS

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met deze functie worden alle AD-machtigingen geretourneerd die momenteel zijn ingesteld voor een bepaald AD-object dat is opgegeven in de parameter -ADobjectDN.
De ADobjectDN moet worden geleverd in een DistinguishedName-indeling.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adobjectdn"></a>-ADobjectDN
{{ADobjectDN-beschrijving invullen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .
