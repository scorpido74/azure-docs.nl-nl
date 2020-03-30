---
title: Aangepaste beleidsregels beheren met PowerShell
titleSuffix: Azure AD B2C
description: Gebruik de PowerShell-cmdlet (Azure Active Directory) (Azure AD) voor programmatisch beheer van uw aangepaste Azure AD B2C-beleid. Aangepaste beleidsregels maken, lezen, bijwerken en verwijderen met PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187403"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Aangepaste Azure AD B2C-beleidsregels beheren met Azure PowerShell

Azure PowerShell biedt verschillende cmdlets voor opdrachtregel- en scriptgebaseerd aangepast beleidsbeheer in uw Azure AD B2C-tenant. Meer informatie over het gebruik van de Azure AD PowerShell-module om:

* Het aangepaste beleid weergeven in een Azure AD B2C-tenant
* Een beleid downloaden van een tenant
* Een bestaand beleid bijwerken door de inhoud ervan te overschrijven
* Een nieuw beleid uploaden naar uw Azure AD B2C-tenant
* Een aangepast beleid verwijderen uit een tenant

## <a name="prerequisites"></a>Vereisten

* [Azure AD B2C-tenant](tutorial-create-tenant.md)en referenties voor een gebruiker in de map met de rol [B2C IEF-beleidsbeheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Aangepast beleid](custom-policy-get-started.md) geüpload naar uw tenant
* [Azure AD PowerShell voor graph **preview module**](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>PowerShell-sessie aansluiten op B2C-tenant

Als u wilt werken met aangepaste beleidsregels in uw Azure AD B2C-tenant, moet u eerst uw PowerShell-sessie met de tenant verbinden met de opdracht [Verbinding-AzureAD.][Connect-AzureAD]

Voer de volgende opdracht `{b2c-tenant-name}` uit, waarbij u de naam van uw Azure AD B2C-tenant vervangt. Meld u aan met een account waarbij de rol [B2C IEF-beleidsbeheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) in de map is toegewezen.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Voorbeeldopdrachtuitvoer met een geslaagde aanmelding:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Alle aangepaste beleidsregels in de tenant weergeven

Als u aangepaste beleidsregels ontdekt, kan een Azure AD B2C-beheerder bedrijfslogica controleren, beheren en toevoegen aan hun activiteiten. Gebruik de opdracht [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] om een lijst met de id's van het aangepaste beleid in een Azure AD B2C-tenant terug te sturen.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Opdrachtuitvoer:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Een beleid downloaden

Nadat u de lijst met beleids-id's hebt bekeken, u een specifiek beleid targeten met [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] om de inhoud ervan te downloaden.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

In dit voorbeeld wordt het beleid met *ID-B2C_1A_signup_signin* gedownload:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Als u de beleidsinhoud lokaal wilt bewerken, leidt `-OutputFilePath` u de opdrachtuitvoer naar een bestand met het argument en opent u het bestand in uw favoriete editor.

Voorbeeldopdracht die uitvoer naar een bestand verzendt:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Een bestaand beleid bijwerken

Nadat u een beleidsbestand hebt bewerkt of gedownload, u het bijgewerkte beleid publiceren naar Azure AD B2C met de opdracht [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

Als u `Set-AzureADMSTrustFrameworkPolicy` de opdracht uitgeeft met de id van een beleid dat al bestaat in uw Azure AD B2C-tenant, wordt de inhoud van dat beleid overschreven.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Voorbeeldopdracht:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Zie de opdracht [Referentie set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] voor meer voorbeelden.

## <a name="upload-a-new-policy"></a>Een nieuw beleid uploaden

Wanneer u een wijziging aanbrengt in een aangepast beleid dat in productie wordt uitgevoerd, wilt u mogelijk meerdere versies van het beleid voor fallback- of A/B-testscenario's publiceren. Of u een kopie van een bestaand beleid maken, wijzigen met een paar kleine wijzigingen en het vervolgens uploaden als een nieuw beleid voor gebruik door een andere toepassing.

Gebruik de opdracht [Nieuw-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] om een nieuw beleid te uploaden:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Voorbeeldopdracht:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Een aangepast beleid verwijderen

Als u een schone bedrijfslevenscyclus wilt behouden, raden we u aan ongebruikt aangepaste beleidsregels periodiek te verwijderen. U bijvoorbeeld oude beleidsversies verwijderen nadat u een migratie naar een nieuwe set beleidsregels hebt uitgevoerd en de functionaliteit van het nieuwe beleid hebt geverifieerd. Als u bovendien een set aangepaste beleidsregels probeert te publiceren en een fout wilt ontvangen, is het mogelijk om het beleid te verwijderen dat is gemaakt als onderdeel van de mislukte release.

Gebruik de opdracht [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] om een beleid uit uw tenant te verwijderen.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Voorbeeldopdracht:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Problemen met het uploaden van beleid oplossen

Wanneer u een nieuw aangepast beleid probeert te publiceren of een bestaand beleid probeert bij te werken, kunnen onjuiste XML-opmaak en fouten in de overervingsketen van het beleidsbestand validatiefouten veroorzaken.

Hier is bijvoorbeeld een poging om een beleid bij te werken met inhoud die misvormde XML bevat (uitvoer wordt afgekapt voor beknoptheid):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Zie Problemen met het [aangepaste azure AD B2C-beleid en het Identity Experience Framework oplossen](active-directory-b2c-guide-troubleshooting-custom.md)voor informatie over het oplossen van aangepaste beleidsregels voor aangepaste problemen.

## <a name="next-steps"></a>Volgende stappen

Zie Aangepaste beleidsregels implementeren [van een Azure DevOps-pijplijn](deploy-custom-policies-devops.md)voor informatie over het gebruik van PowerShell om aangepaste beleidsregels te implementeren als onderdeel van een CI/CD-pijplijn (continuous delivery) voor informatie over het gebruik van aangepaste beleidsregels.

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
