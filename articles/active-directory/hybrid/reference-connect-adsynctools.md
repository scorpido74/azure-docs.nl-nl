---
title: 'Azure AD Connect: ADSyncTools PowerShell-referentie | Microsoft Documenten'
description: Dit document bevat referentie-informatie voor de ADSyncTools.psm1 PowerShell-module.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60454656"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell-referentie
De volgende documentatie bevat referentiegegevens voor de ADSyncTools.psm1 PowerShell-module die is opgenomen in Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMENVATTING
De mS-Ds-consistencyGuid wissen van AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De waarde in mS-Ds-ConsistencyGuid voor de doel-AD-gebruiker wissen

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doelgebruiker in AD instellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SAMENVATTING
{{Vul de Synopsis}}

### <a name="syntax"></a>SYNTAXIS

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Voorbeeldbeschrijving toevoegen }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SAMENVATTING
{{Vul de Synopsis}}

### <a name="syntax"></a>SYNTAXIS

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Voorbeeldbeschrijving toevoegen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-database"></a>-Database
{{Vullingdatabasebeschrijving}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instantie
{{Beschrijving van de instantie vulling}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Beschrijving wachtwoord invullen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Fill Server Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-GebruikersNaam
{{Beschrijving gebruikersnaam invullen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMENVATTING
Consistentie exporterenGuid-rapport

### <a name="syntax"></a>SYNTAXIS

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Genereert een ConsistencyGuid-rapport op basis van een CSV-bestand importeren uit Import-ADSyncToolsImmutableIdMigration

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-alternativeloginid"></a>-AlternatiefLoginId
Alternatieve inlog-id gebruiken (e-mail)

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

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-Onveranderlijke IdGUID
OnveranderlijkE IdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Uitvoer
Bestandsnaam uitvoer voor CSV- en LOG-bestanden

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SAMENVATTING
{{Vul de Synopsis}}

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Voorbeeldbeschrijving toevoegen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostNaam
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SAMENVATTING
Gebruiker ophalen bij AD

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Geeft als resultaat een AD-object TO DO: Ondersteuning voor meerdere foresten

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doelgebruiker in AD om Consistentie Guid in te stellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMENVATTING
Haal de mS-Ds-ConsistencyGuid van AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Geeft als resultaat de waarde in het kenmerk mS-Ds-ConsistencyGuid van de doel-AD-gebruiker in de GUID-indeling

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doelgebruiker in AD instellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SAMENVATTING
De ObjectGuid ophalen van AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Geeft als resultaat de waarde in objectGUId-kenmerk van de doel-AD-gebruiker in de GUID-indeling

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doelgebruiker in AD instellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SAMENVATTING
Ontvang aad connect run-geschiedenis

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Functie waarin de AAD Connect Run-geschiedenis in XML-indeling wordt geretourneerd

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>VOORBEELD 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-days"></a>-Dagen
{{Vuldagen Beschrijving}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorGewijzigd

### <a name="synopsis"></a>SAMENVATTING
Gebruikers met gewijzigde fouten van SourceAnchor krijgen

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Functiequery's AAD Connect Run History en exporteert alle gebruikers die de fout melden: 'Het kenmerk SourceAnchor is gewijzigd'.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
#Required Parameters
```

$sourcePath = Lees-Host -Prompt "Voer uw logboekbestandspad\<\>met bestandsnaam in" Source_Path " $outputPath = Lees-Host\<-Prompt "Voer uw bestandspad met bestandsnaam in" Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-sourcepath"></a>-sourcePath
{{VulbronPath-beschrijving}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPad
{{VullingoutputPath-beschrijving}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SAMENVATTING
ImmutableID importeren van AAD

### <a name="syntax"></a>SYNTAXIS

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Hiermee genereert u een bestand met alle Azure AD-gesynchroniseerde gebruikers die de waarde Onveranderlijke ID bevatten in GUID-indelingsvereisten: MSOnline PowerShell-module

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-output"></a>-Uitvoer
CSV-bestand uitvoeren

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

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecyclbin
Gesynchroniseerde gebruikers ophalen vanuit Azure AD-prullenbak

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

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .


## <a name="invoke-adsyncdatabasequery"></a>Aanroepen-AdSyncDatabaseQuery

### <a name="synopsis"></a>SAMENVATTING
{{Vul de Synopsis}}

### <a name="syntax"></a>SYNTAXIS

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Voorbeeldbeschrijving toevoegen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-query"></a>-Query
{{Querybeschrijving invullen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fill SqlConnection Description}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Verlopen certificaten verwijderen-ADSyncToolsverlopen

### <a name="synopsis"></a>SAMENVATTING
Script om verlopen certificaten te verwijderen uit kenmerk UserCertificate

### <a name="syntax"></a>SYNTAXIS

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met dit script worden alle objecten uit een doelorganisatie-eenheid in uw Active Directory-domein - gefilterd op objectklasse (gebruiker/computer) en worden alle verlopen certificaten in het kenmerk UserCertificate verwijderd.
Standaard (BackupOnly-modus) wordt alleen een back-up gemaakt van verlopen certificaten in een bestand en worden er geen wijzigingen aangebracht in AD.
Als u -BackupOnly $false, wordt elk verlopen certificaat dat aanwezig is in het kenmerk UserCertificate voor deze objecten uit AD verwijderd nadat ze naar het bestand zijn gekopieerd.
Er wordt een back-up van elk certificaat gemaakt met een gescheiden bestandsnaam: ObjectClass_ObjectGUID_CertThumprint.cer Het script maakt ook een logbestand in CSV-indeling met alle gebruikers met certificaten die geldig zijn of verlopen, inclusief de werkelijke actie die is ondernomen (Overgeslagen/Geëxporteerd/Verwijderd).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass-gebruiker

#### <a name="example-2"></a>VOORBEELD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass-computer -BackupOnly $false

### <a name="parameters"></a>PARAMETERS

#### <a name="-targetou"></a>-TargetOU
Doel-eenheid om ad-objecten op te zoeken

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

#### <a name="-backuponly"></a>-Alleen back-up
BackupOnly verwijdert geen certificaten uit AD

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectKlasse
Objectklasse, filter

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="repair-adsynctoolsautoupgradestate"></a>Reparatie-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SAMENVATTING
Korte beschrijving

### <a name="syntax"></a>SYNTAXIS

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>BESCHRIJVING
Lange beschrijving

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAdres

### <a name="synopsis"></a>SAMENVATTING
{{Vul de Synopsis}}

### <a name="syntax"></a>SYNTAXIS

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Voorbeeldbeschrijving toevoegen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostNaam
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Herstel-ADSyncToolsVerlopencertificaten

### <a name="synopsis"></a>SAMENVATTING
(TE DOEN) Kenmerk AD-gebruikerscertificaat herstellen uit een certificaatbestand

### <a name="syntax"></a>SYNTAXIS

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>BESCHRIJVING
Lange beschrijving

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Consistentie guid instellen-ADSyncTools

### <a name="synopsis"></a>SAMENVATTING
MS-Ds-consistencyGuid instellen op AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Een waarde instellen in het kenmerk mS-Ds-ConsistencyGuid voor de doel-AD-gebruiker

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doelgebruiker in AD om Consistentie Guid in te stellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Waarde
Onveranderlijke id (matrix byte, GUID, GUID-tekenreeks of Base64-tekenreeks)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SAMENVATTING
{{Vul de Synopsis}}

### <a name="syntax"></a>SYNTAXIS

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Voorbeeldbeschrijving toevoegen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostNaam
{{Fill hostName Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-poort
{{Beschrijving van de vulpoort}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>SAMENVATTING
Maakt een traceerbestand van en AD Import Stap

### <a name="syntax"></a>SYNTAXIS

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Hiermee worden alle ldap-query's van een AAD Connect AD Import-run traceert vanuit een bepaald CONTROLEpunt voor ad-watermerk (partitiecookie). Hiermee maakt u een traceerbestand '.\ADimportTrace_yyyyMMddHHmmss.log' op de huidige map.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML-beschrijving}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
XML-bestand van AD-connectorexporteren

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Doeldomeincontroller

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
Boswortel DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-Referenties overslaan
Typen AD-objecten \> die moeten worden getraceerd * = alle objecttypen

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

#### <a name="-adwatermark"></a>-ADwatermerk
Als u al als domeinbeheerder wordt uitgevoerd, hoeft u geen AD-referenties op te geven.
Handmatige invoer van watermerk, in plaats van XML-bestand bijvoorbeeld $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SAMENVATTING
Korte beschrijving

### <a name="syntax"></a>SYNTAXIS

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Lange beschrijving

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOORBEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-context"></a>-Context
Param1 help beschrijving

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

#### <a name="-server"></a>-Server
Param2 help beschrijving

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Param2 help beschrijving

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Param2 help beschrijving

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters voor meerhttps://go.microsoft.com/fwlink/?LinkID=113216)informatie .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMENVATTING
Werkt gebruikers bij met de nieuwe ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SYNTAXIS

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Updates gebruikers met de nieuwe ConsistencyGuid (ImmutableId) waarde uit de ConsistencyGuid Report Deze functie ondersteunt de WhatIf-schakelaar Opmerking: ConsistencyGuid Report moet worden geïmporteerd met Tab Demiliter

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>VOORBEELD 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-Onveranderlijke IdGUID
OnveranderlijkE IdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Actie
Actie

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Uitvoer
Bestandsnaam uitvoervoor LOG-bestanden

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
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
