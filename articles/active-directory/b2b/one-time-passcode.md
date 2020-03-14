---
title: Verificatie met eenmalige wachtwoord code voor B2B-gast gebruikers-Azure AD
description: Eenmalige e-mail wachtwoord verificatie gebruiken om B2B-gast gebruikers te verifiëren zonder dat hiervoor een Microsoft-account nodig is.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263372"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Verificatie met eenmalige e-mail code voor e-mail (preview-versie)

|     |
| --- |
| De e-mail wachtwoord voor eenmalige e-mail is een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.|
|     |

In dit artikel wordt beschreven hoe u eenmalige verificatie via E-mail voor B2B-gast gebruikers inschakelt. Met de functie voor eenmalige e-mail code worden B2B-gast gebruikers geverifieerd wanneer ze niet kunnen worden geverifieerd via andere manieren, zoals Azure AD, een Microsoft-account (MSA) of Google Federatie. Met authenticatie op basis van eenmalige wachtwoord code hoeft u geen Microsoft-account te maken. Wanneer de gast gebruiker een uitnodiging heeft ingewisseld of een gedeelde resource opent, kunnen ze een tijdelijke code aanvragen, die wordt verzonden naar hun e-mail adres. Vervolgens voeren ze deze code in om door te gaan met aanmelden.

Deze functie is momenteel beschikbaar voor preview (Zie [de preview-versie in het voor beeld](#opting-in-to-the-preview) hieronder). Na de preview-versie wordt deze functie standaard ingeschakeld voor alle tenants.

> [!NOTE]
> Eenmalige wachtwoord code gebruikers moeten zich aanmelden met behulp van een koppeling die de Tenant context bevat (bijvoorbeeld `https://myapps.microsoft.com/?tenantid=<tenant id>` of `https://portal.azure.com/<tenant id>`, of in het geval van een geverifieerd domein `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Directe koppelingen naar toepassingen en bronnen werken ook zolang ze de context van de Tenant bevatten. Gast gebruikers kunnen zich momenteel niet aanmelden met eind punten die geen Tenant context hebben. Als u bijvoorbeeld `https://myapps.microsoft.com`, `https://portal.azure.com`of het algemene eind punt teams gebruikt, resulteert dit in een fout. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Gebruikers ervaring voor eenmalige wachtwoord code gast gebruikers
Met verificatie met eenmalige wachtwoord code kan de gast gebruiker de uitnodiging inwisselen door te klikken op een directe koppeling of door de uitnodiging-e-mail te gebruiken. In beide gevallen geeft een bericht in de browser aan dat een code wordt verzonden naar het e-mail adres van de gast gebruiker. De gast gebruiker selecteert **Verzend code**:
 
   ![Scherm afbeelding met de knop code verzenden](media/one-time-passcode/otp-send-code.png)
 
Er wordt een wachtwoord code verzonden naar het e-mail adres van de gebruiker. De gebruiker haalt de wachtwoord code op uit het e-mail bericht en voert deze in het browser venster in:
 
   ![Scherm opname met de code pagina invoeren](media/one-time-passcode/otp-enter-code.png)
 
De gast gebruiker is nu geverifieerd en ze kunnen de gedeelde bron zien of zich blijven aanmelden. 

> [!NOTE]
> Eenmalige wachtwoord codes zijn 30 minuten geldig. Na 30 minuten is de specifieke eenmalige wachtwoord code niet meer geldig en moet de gebruiker een nieuwe wacht woord aanvragen. Gebruikers sessies verlopen na 24 uur. Daarna ontvangt de gast gebruiker een nieuwe wachtwoord code wanneer hij of zij toegang tot de bron heeft. Sessie verloop biedt extra beveiliging, vooral wanneer een gast gebruiker het bedrijf verlaat of geen toegang meer nodig heeft.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Wanneer krijgt een gast gebruiker een eenmalige wachtwoord code?

Wanneer een gast gebruiker een uitnodiging heeft ingewisseld of een koppeling gebruikt naar een resource die met hen is gedeeld, ontvangt deze een eenmalige wachtwoord code als:
- Ze hebben geen Azure AD-account 
- Ze hebben geen Microsoft-account 
- De uitnodigende Tenant heeft geen Google Federation ingesteld voor @gmail.com en @googlemail.com gebruikers 

Op het moment van de uitnodiging is er geen indicatie dat de gebruiker die u uitnodigt, gebruikmaakt van verificatie met een eenmalige wachtwoord code. Maar wanneer de gast gebruiker zich aanmeldt, is verificatie met een eenmalige wachtwoord code de terugval methode als er geen andere verificatie methoden kunnen worden gebruikt. 

U kunt gast gebruikers die zich verifiëren met eenmalige wachtwoord codes in de Azure Portal weer geven door naar **Azure Active Directory** > **organisatie relaties** > **gebruikers van andere organisaties**te gaan.

![Scherm afbeelding met een eenmalige wachtwoord code gebruiker met bron waarde van OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Wanneer een gebruiker een eenmalige wachtwoord code inwisselt en later een MSA-, Azure AD-account of een ander federatief account ontvangt, worden ze nog steeds geverifieerd met een eenmalige wachtwoord code. Als u de verificatie methode wilt bijwerken, kunt u het gebruikers account van de gast verwijderen en opnieuw uitnodigen.

### <a name="example"></a>Voorbeeld
Gast gebruikers alexdoe@gmail.com wordt uitgenodigd voor fabrikam, waarvoor geen Google Federation is ingesteld. Alex heeft geen Microsoft-account. Er wordt een eenmalige wachtwoord code voor verificatie ontvangen.

## <a name="opting-in-to-the-preview"></a>Inkiezen voor de preview 
Het kan enkele minuten duren voordat de opt-in-actie van kracht wordt. Daarna gebruiken alleen recent uitgenodigde gebruikers die voldoen aan bovenstaande voor waarden verificatie met eenmalige wachtwoord code. Gast gebruikers die eerder een uitnodiging hebben ingewisseld, blijven dezelfde verificatie methode gebruiken.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Aanmelden met de Azure AD-Portal
1.  Meld u aan bij de [Azure Portal](https://portal.azure.com/) als een globale Azure AD-beheerder.
2.  Selecteer **Azure Active Directory**in het navigatie deel venster.
3.  Selecteer onder **beheren**de optie **organisatie relaties**.
4.  **Instellingen**selecteren.
5.  Selecteer onder **E-mail eenmalige wachtwoord code voor gasten inschakelen (preview)** de optie **Ja**.
 
### <a name="to-opt-in-using-powershell"></a>Aanmelden met Power shell

Eerst moet u de nieuwste versie van de Azure AD Power shell for Graph-module (AzureADPreview) installeren. Vervolgens bepaalt u of B2B-beleids regels al bestaan en voert u de juiste opdrachten uit.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Vereiste: Installeer de meest recente AzureADPreview-module
Controleer eerst welke modules die u hebt geïnstalleerd. Open PowerShell als een gebruiker met verhoogde bevoegdheid (Uitvoeren als administrator) en voer de volgende opdracht uit:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Als de AzureADPreview-module zonder bericht verschijnt dat aangeeft dat er een nieuwere versie bestaat, bent u klaar. Anders doet u op basis van de uitvoer, het volgende:

- Als er geen resultaten worden geretourneerd, voert u de volgende opdracht uit om de AzureADPreview-module te installeren:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Als alleen de AzureAD-module wordt weergegeven in de resultaten, voer de volgende opdrachten uit om de AzureADPreview-module installeren: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Als alleen de AzureADPreview-module wordt weergegeven in de resultaten, maar u een bericht ontvangt dat aangeeft dat er een latere versie, voer de volgende opdrachten uit om de module bij te werken: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Mogelijk krijgt u een opdrachtprompt dat u de module vanuit een niet-vertrouwde opslagplaats installeert. Dit gebeurt als u de opslagplaats PSGallery eerder niet hebt ingesteld als een vertrouwde opslagplaats. Druk op **Y** om de module te installeren.

#### <a name="check-for-existing-policies-and-opt-in"></a>Controleren op bestaande beleids regels en aanmelden

Controleer vervolgens of er momenteel een B2BManagementPolicy bestaat door de volgende handelingen uit te voeren:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Als de uitvoer onwaar is, bestaat het beleid momenteel niet. Maak een nieuwe B2BManagementPolicy en meld u aan voor de preview-versie door het volgende uit te voeren:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Als de uitvoer waar is, bestaat het B2BManagementPolicy-beleid momenteel. Voer de volgende handelingen uit om het beleid bij te werken en u aan te melden bij de preview:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Uitkiezen uit het voor beeld na het inkiezen
Het kan enkele minuten duren voordat de opt-out-actie van kracht wordt. Als u de preview-versie uitschakelt, kunnen gast gebruikers die een eenmalige wachtwoord code hebben ingewisseld, zich niet meer aanmelden. U kunt de gast gebruiker verwijderen en de gebruiker opnieuw uitnodigen om zich opnieuw aan te melden met een andere verificatie methode.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>De preview-versie uitschakelen met behulp van de Azure AD-Portal
1.  Meld u aan bij de [Azure Portal](https://portal.azure.com/) als een globale Azure AD-beheerder.
2.  Selecteer **Azure Active Directory**in het navigatie deel venster.
3.  Selecteer onder **beheren**de optie **organisatie relaties**.
4.  **Instellingen**selecteren.
5.  Selecteer onder **E-mail eenmalige wachtwoord code voor gasten inschakelen (preview)** de optie **Nee**.

### <a name="to-turn-off-the-preview-using-powershell"></a>De preview-versie uitschakelen met Power shell
Installeer de meest recente AzureADPreview-module als u deze nog niet hebt (Zie [vereisten: Installeer de meest recente AzureADPreview-module](#prerequisite-install-the-latest-azureadpreview-module) ). Controleer vervolgens of het beleid voor de eenmalige wachtwoord code al bestaat door het volgende uit te voeren:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Als de uitvoer waar is, meldt u zich af bij het voor beeld door de volgende handelingen uit te voeren:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

