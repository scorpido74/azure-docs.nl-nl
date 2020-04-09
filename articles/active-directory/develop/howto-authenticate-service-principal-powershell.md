---
title: Een Azure-app-identiteit maken (PowerShell) | Azure
titleSuffix: Microsoft identity platform
description: Hierin wordt beschreven hoe u met Azure PowerShell een Azure Active Directory-toepassing en service-principal maakt en toegang tot resources verleent via toegangsbeheer op basis van rollen. Het laat zien hoe u de toepassing verifieert met een certificaat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.date: 10/10/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: 7bd8c3b25c23ba8586e38ec8eb7d1baefaa21633
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884177"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>How to: Azure PowerShell gebruiken om een serviceprincipal met een certificaat te maken

Wanneer u een app of een script hebt waarvoor toegang tot resources vereist is, kunt u een identiteit voor de app instellen en de app met eigen referenties verifiëren. Deze identiteit staat bekend als een service-principal. Hiermee kunt u het volgende doen:

* Andere machtigingen aan de identiteit van de app toekennen dan uw eigen machtigingen. Deze machtigingen worden meestal beperkt tot de functies die met de app moeten kunnen worden uitgevoerd.
* Een certificaat voor verificatie gebruiken bij het uitvoeren van een onbewaakt script.

> [!IMPORTANT]
> In plaats van een serviceprincipal te maken, u overwegen beheerde identiteiten te gebruiken voor Azure-bronnen voor uw toepassingsidentiteit. Als uw code wordt uitgevoerd op een service die beheerde identiteiten ondersteunt en toegang krijgt tot resources die Azure Active Directory (Azure AD)-verificatie ondersteunen, zijn beheerde identiteiten een betere optie voor u. Zie [Wat is beheerde identiteiten voor Azure-resources voor Azure-bronnen voor Azure-bronnen.](../managed-identities-azure-resources/overview.md)

In dit artikel leest u hoe een service-principal maakt die zichzelf verifieert met een certificaat. Zie [Een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) voor het instellen van een service-principal met een wachtwoord.

Voor dit artikel hebt u de [meest recente versie](/powershell/azure/install-az-ps) van PowerShell nodig.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Vereiste machtigingen

Als u dit artikel wilt voltooien, moet u over voldoende machtigingen beschikken in zowel uw Azure AD- als Azure-abonnement. U moet in het bijzonder een app kunnen maken in het Azure AD en de serviceprincipal aan een rol kunnen toewijzen.

De eenvoudigste manier om te controleren of uw account over de juiste machtigingen beschikt, verloopt via de portal. Zie [Check required permission](howto-create-service-principal-portal.md#required-permissions) (Vereiste machtiging controleren).

## <a name="assign-the-application-to-a-role"></a>De toepassing toewijzen aan een rol
Als u toegang wilt krijgen tot bronnen in uw abonnement, moet u de toepassing toewijzen aan een rol. Bepaal welke rol de juiste machtigingen voor de toepassing biedt. Zie [RBAC: Ingebouwde rollen voor](/azure/role-based-access-control/built-in-roles)meer informatie over de beschikbare rollen.

U het bereik instellen op het niveau van het abonnement, de resourcegroep of de resource. Machtigingen worden overgenomen naar lagere niveaus van bereik. Als u bijvoorbeeld een toepassing toevoegt aan *de* leesrol voor een resourcegroep, kan deze de brongroep en de resources die deze bevat, lezen. Als u wilt dat de toepassing acties zoals opnieuw opstarten, starten en stoppen, wilt uitvoeren, selecteert u de rol *Inzender.*

## <a name="create-service-principal-with-self-signed-certificate"></a>Service-principal met een zelfondertekend certificaat maken

In het volgende voorbeeld wordt een eenvoudig scenario behandeld. Het maakt gebruik [van New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) om een serviceprincipal te maken met een zelfondertekend certificaat en gebruikt [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) om de [Reader-rol](/azure/role-based-access-control/built-in-roles#reader) toe te wijzen aan de serviceprincipal. De roltoewijzing is afgestemd op uw huidige, geselecteerde Azure-abonnement. Als u een ander abonnement wilt selecteren, gebruikt u [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> De cmdlet New-SelfSignedCertificate en de PKI-module worden momenteel niet ondersteund in PowerShell Core. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

Het voorbeeld wordt 20 seconden weergegeven, zodat de nieuwe serviceprincipal zich kan voortplanten in Azure AD. Als de wachttijd in het script te kort is, wordt de foutmelding "Principal {ID} bestaat niet in de map {DIR-ID}." weergegeven. Als u deze fout wilt oplossen, wacht u even en voert u de opdracht **Nieuw-AzRoleAssignment** opnieuw uit.

U kunt de roltoewijzing beperken tot een specifieke resourcegroep met behulp van de parameter **ResourceGroupName**. Of tot een specifieke resource met behulp van de parameters **ResourceType** en **ResourceName**. 

Als u **niet beschikt over Windows 10 of Windows Server 2016**, moet u het [programma voor het genereren van zelfondertekende certificaten](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) downloaden van Microsoft Script Center. Pak de inhoud uit en importeer de cmdlet die u nodig hebt.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Vervang de volgende twee regels in het script om het certificaat te genereren.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Certificaat opgeven via een automatisch PowerShell-script

Wanneer u zich aanmeldt als een service-principal, moet u de tenant-ID van de map voor uw AD-app opgeven. Een tenant is een instantie van Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Service-principal met certificaat van certificeringsinstantie maken

In het volgende voorbeeld wordt een service-principal gemaakt met behulp van een certificaat dat is uitgegeven door een certificeringsinstantie. De toewijzing is afgestemd op het opgegeven Azure-abonnement. Het voegt de serviceprincipal toe aan de [Reader-rol.](../../role-based-access-control/built-in-roles.md#reader) Als er een fout optreedt bij het toewijzen van de rol, wordt het opnieuw geprobeerd.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Certificaat opgeven via een automatisch PowerShell-script
Wanneer u zich aanmeldt als een service-principal, moet u de tenant-ID van de map voor uw AD-app opgeven. Een tenant is een instantie van Azure AD.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

De toepassings-id en tenant-id zijn geen gevoelige gegevens, dus u kunt deze rechtstreeks in uw script insluiten. Als u de tenant-id wilt ophalen, gebruikt u:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Als u de toepassings-id wilt ophalen, gebruikt u:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Referenties wijzigen

Als u de referenties voor een AD-app wilt wijzigen, vanwege een beveiligingscompromis of een vervaldatum van de referenties, gebruikt u de cmdlets [Verwijderen-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) en [Nieuw-AzADAppCredential.](/powershell/module/az.resources/new-azadappcredential)

Als u alle referenties voor een toepassing wilt verwijderen, gebruikt u:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Als u een waarde voor een certificaat wilt toevoegen, maakt u een zelfondertekend certificaat zoals aangegeven in dit artikel. Vervolgens gebruikt u:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Fouten opsporen

Bij het maken van een service-principal kunnen de volgende foutmeldingen worden weergegeven:

* **Authentication_Unauthorized** (Verificatie geweigerd) of **No subscription found in the context.** (Geen abonnement gevonden in de context.) - U ziet deze fout wanneer uw account niet over de [vereiste machtigingen](#required-permissions) voor het Azure AD beschikt om een app te registreren. Normaal gesproken ziet u deze fout wanneer alleen beheerdersgebruikers in uw Azure Active Directory apps kunnen registreren en uw account geen beheerder is. Vraag uw beheerder om u toe te wijzen aan een beheerdersrol of om gebruikers in staat te stellen apps te registreren.

* Uw account **heeft geen toestemming om actie 'Microsoft.Authorization/roleAssignments/write' uit te voeren over bereik '/abonnementen/{guid}'."** - U ziet deze fout wanneer uw account niet over voldoende machtigingen beschikt om een rol aan een identiteit toe te wijzen. Vraag de abonnementsbeheerder om u toe te voegen aan de rol Administrator voor gebruikerstoegang.

## <a name="next-steps"></a>Volgende stappen

* Zie [Een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) voor het instellen van een service-principal met een wachtwoord.
* Zie [Toepassingsobjecten en service-principalobjecten](app-objects-and-service-principals.md) voor meer informatie over toepassingen en service-principals.
* Zie [Verificatiescenario's voor Azure AD voor](authentication-scenarios.md)meer informatie over Azure AD-verificatie.
