---
title: 'Azure AD Connect: ADConnectivityTools PowerShell-referentie | Microsoft Documenten'
description: Dit document bevat referentie-informatie voor de ADConnectivityTools.psm1 PowerShell-module.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b90ff82601acca1249c7d8c353944e39e89f95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66473787"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: ADConnectivityTools PowerShell-referentie

De volgende documentatie bevat referentiegegevens voor de ADConnectivityTools.psm1 PowerShell-module die is opgenomen in Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Bevestigde-DnsConnectivity

### <a name="synopsis"></a>SAMENVATTING

Detecteert lokale Dns-problemen.

### <a name="syntax"></a>SYNTAXIS

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Met lokale Dns-connectiviteitstests wordt lokale Dns-connectiviteitstests uitgevoerd.
Om de Active Directory-connector te configureren, moet de gebruiker zowel een naamresolutie hebben voor het forest waarmee hij verbinding probeert te maken als in de domeincontrollers die aan dit forest zijn gekoppeld.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Hiermee geeft u de naam op van het forest waartegen moet worden getest.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DC's

Geef DC's op om tegen te testen.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Geeft als resultaat het resultaat van deze diagnose in de vorm van een PSObject.
Niet nodig tijdens handmatige interactie met deze tool.

```yml
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

## <a name="confirm-forestexists"></a>Bevestigen-forestexists

### <a name="synopsis"></a>SAMENVATTING

Hiermee bepaalt u of er een opgegeven forest bestaat.

### <a name="syntax"></a>SYNTAXIS

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Hiermee wordt een DNS-server opgevraagd voor de IP-adressen die aan een forest zijn gekoppeld.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Hiermee geeft u de naam op van het forest waartegen moet worden getest.

```yml
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

## <a name="confirm-functionallevel"></a>Bevestigen-functioneel niveau

### <a name="synopsis"></a>SAMENVATTING

Hiermee controleert u het functionaliteitsniveau van AD-forest.

### <a name="syntax"></a>SYNTAXIS

#### <a name="samaccount"></a>SamAccount (SamAccount)

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Controleert of het functionaliteitsniveau van het AD-forest gelijk is aan of meer is dan een bepaalde MinAdForestVersion (WindowsServer2003).
Account (Domein\Gebruikersnaam) en Wachtwoord kunnen worden aangevraagd.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>VOORBEELD 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Doelbos.
Standaardwaarde is het forest van de momenteel ingelogde gebruiker.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

DoelforestFQDn-object.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

De functie gebruikt de referenties van de gebruiker die momenteel op de computer is ingelogd, in plaats van aangepaste referenties van de gebruiker op te vragen.

```yml
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

## <a name="confirm-networkconnectivity"></a>Bevestig-NetworkConnectivity

### <a name="synopsis"></a>SAMENVATTING

Detecteert problemen met lokale netwerkconnectiviteit.

### <a name="syntax"></a>SYNTAXIS

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Voert lokale netwerkconnectiviteitstests uit.

Voor de lokale netwerktests moet AAD Connect kunnen communiceren met de benoemde domeincontrollers op poorten 53 (DNS), 88 (Kerberos) en 389 (LDAP) De meeste organisaties draaien DNS op hun DC's, daarom is deze test momenteel geïntegreerd.
Poort 53 moet worden overgeslagen als een andere DNS-server is opgegeven.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-dcs"></a>-DC's

Geef DC's op om tegen te testen.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Als de gebruiker geen DNS-services van de AD-site / Logon DC gebruikt, kan het zijn dat de controlepoort 53 wordt overgeslagen.
De gebruiker moet nog steeds in staat zijn om _.ldap._tcp op te lossen. \<forestfqdn\> om de Active Directory Connector-configuratie te laten slagen.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Geeft als resultaat het resultaat van deze diagnose in de vorm van een PSObject.
Niet nodig tijdens handmatige interactie met deze tool.

```yml
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

## <a name="confirm-targetsarereachable"></a>Bevestigingsdoelen zijn bereikbaar

### <a name="synopsis"></a>SAMENVATTING

Hiermee bepaalt u of een opgegeven forest en de bijbehorende domeincontrollers bereikbaar zijn.

### <a name="syntax"></a>SYNTAXIS

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Voert "ping"-tests uit (of een computer een doelcomputer kan bereiken via het netwerk en/of het internet)

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Hiermee geeft u de naam op van het forest waartegen moet worden getest.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DC's

Geef DC's op om tegen te testen.

```yml
Type: Array
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

## <a name="confirm-validdomains"></a>Domeinen bevestigen-geldig

### <a name="synopsis"></a>SAMENVATTING

Valideren dat de domeinen in de verkregen Forest FQDN bereikbaar zijn

### <a name="syntax"></a>SYNTAXIS

#### <a name="samaccount"></a>SamAccount (SamAccount)

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Valideer dat alle domeinen in de verkregen Forest FQDN bereikbaar zijn door te proberen DomainGuid en DomainDN op te halen.
Account (Domein\Gebruikersnaam) en Wachtwoord kunnen worden aangevraagd.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>VOORBEELD 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Doelbos.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

DoelforestFQDn-object.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

De functie gebruikt de referenties van de gebruiker die momenteel op de computer is ingelogd, in plaats van aangepaste referenties van de gebruiker op te vragen.

```yml
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

## <a name="confirm-validenterpriseadmincredentials"></a>Bevestigde-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SAMENVATTING

Controleert of een gebruiker enterprise-beheerdersreferenties heeft.

### <a name="syntax"></a>SYNTAXIS

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Zoekt als de opgegeven gebruiker enterprise admin referenties heeft.
Account (Domein\Gebruikersnaam) en Wachtwoord kunnen worden aangevraagd.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

De functie gebruikt de referenties van de gebruiker die momenteel op de computer is ingelogd, in plaats van aangepaste referenties van de gebruiker op te vragen.

```yml
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

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SAMENVATTING

Haalt een DomainFQDN op uit een combinatie van account en wachtwoorden.

### <a name="syntax"></a>SYNTAXIS

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Pogingen om een domeinFQDN-object te verkrijgen uit verstrekte referenties.
Als het domeinFQDN geldig is, wordt een DomainFQDNName of RootDomainName geretourneerd, afhankelijk van de keuze van de gebruiker.
Account (Domein\Gebruikersnaam) en Wachtwoord kunnen worden aangevraagd.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Gewenste soort gegevens die worden opgehaald.
Momenteel beperkt tot "DomainFQDNName" of "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

De functie gebruikt de referenties van de gebruiker die momenteel op de computer is ingelogd, in plaats van aangepaste referenties van de gebruiker op te vragen.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptiononError

Hulpparameter die wordt gebruikt door de functie Start-NetworkConnectivityDiagnosisTools, functie

```yml
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

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SAMENVATTING

Haalt een ForestFQDN op uit een combinatie van account en wachtwoorden.

### <a name="syntax"></a>SYNTAXIS

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Pogingen om een ForestFQDN te verkrijgen uit de verstrekte referenties.
Account (Domein\Gebruikersnaam) en Wachtwoord kunnen worden aangevraagd.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Doelbos. Standaardwaarde is het domein van de momenteel ingelogde gebruiker.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

De functie gebruikt de referenties van de gebruiker die momenteel op de computer is ingelogd, in plaats van aangepaste referenties van de gebruiker op te vragen.

```yml
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

## <a name="start-connectivityvalidation"></a>Validatie van startconnectiviteit

### <a name="synopsis"></a>SAMENVATTING

Hoofdfunctie.

### <a name="syntax"></a>SYNTAXIS

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Hier worden alle beschikbare mechanismen uitgevoerd die controleren of AD-referenties geldig zijn.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Doelbos.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

Voor aangepaste installaties: vlag die wordt $True als de gebruiker 'Nieuw AD-account maken' heeft gekozen in het venster AD-forestaccount van de wizard AADConnect.
$False als de gebruiker 'Bestaand AD-account gebruiken' kiest.
Voor Express-installaties: De waarde van deze variabele moet worden $True voor Express-installaties.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-GebruikersNaam

Parameter die vooraf het veld Gebruikersnaam vult wanneer de referenties van de gebruiker worden aangevraagd.

```yml
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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SAMENVATTING

Belangrijkste functie voor netwerkconnectiviteitstests.

### <a name="syntax"></a>SYNTAXIS

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING

Voert lokale netwerkconnectiviteitstests uit.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOORBEELD 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>VOORBEELD 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Bos

Hiermee geeft u de forestnaam op waartegen moet worden getest.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Referenties

De gebruikersnaam en het wachtwoord van de gebruiker die de test uitvoert.
Hiervoor is hetzelfde niveau van machtigingen vereist dat nodig is om de wizard Azure AD Connect uit te voeren.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocatie

Hiermee geeft u de locatie op van een logboekbestand dat de uitvoer van deze functie bevat.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DC's

Geef DC's op om tegen te testen.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-Informatief bericht weergeven

Vlag waarmee een bericht over het doel van deze functie kan worden weergegeven.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Geeft als resultaat het resultaat van deze diagnose in de vorm van een PSObject.
Niet nodig om op te geven tijdens handmatige interactie met deze tool.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Geeft aan of de referenties die de gebruiker heeft getypt geldig zijn.
Niet nodig om op te geven tijdens handmatige interactie met deze tool.

```yml
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
