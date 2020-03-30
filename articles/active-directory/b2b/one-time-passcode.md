---
title: Eenmalige wachtwoordverificatie voor B2B-gastgebruikers - Azure AD
description: Eenmalige toegangscode per e-mail gebruiken om B2B-gastgebruikers te verifiëren zonder dat u een Microsoft-account nodig hebt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263372"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Verificatie van eenmalige toegangscodeen e-mailen (voorbeeld)

|     |
| --- |
| E-mail eenmalige toegangscode is een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.|
|     |

In dit artikel wordt beschreven hoe u verificatie van eenmalige toegangscode voor B2B-gastgebruikers inschakelt. De functie One-time passcode e-mail verifieert B2B-gastgebruikers wanneer ze niet kunnen worden geverifieerd via andere middelen, zoals Azure AD, een Microsoft-account (MSA) of Google-federatie. Met eenmalige wachtwoordverificatie hoeft u geen Microsoft-account aan te maken. Wanneer de gastgebruiker een uitnodiging inwisselt of toegang krijgt tot een gedeelde bron, kan hij of zij een tijdelijke code aanvragen die naar zijn e-mailadres wordt verzonden. Vervolgens voeren ze deze code in om verder te gaan met inloggen.

Deze functie is momenteel beschikbaar voor preview (zie [Aanmelden voor de preview](#opting-in-to-the-preview) hieronder). Na een preview wordt deze functie standaard ingeschakeld voor alle tenants.

> [!NOTE]
> Gebruikers van eenmalige toegangscodemoeten zich aanmelden via een koppeling die `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>`de tenantcontext bevat (bijvoorbeeld of `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`in het geval van een geverifieerd domein). Directe koppelingen naar toepassingen en resources werken ook zolang ze de tenantcontext bevatten. Gastgebruikers kunnen zich momenteel niet aanmelden met eindpunten die geen tenantcontext hebben. Als u `https://myapps.microsoft.com`bijvoorbeeld `https://portal.azure.com`het gemeenschappelijk eindpunt Teams gebruikt, of het gemeenschappelijke eindpunt teams, resulteert dit in een fout. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Gebruikerservaring voor gastgebruikers die eenmalig toegangscodeen
Met eenmalige wachtwoordverificatie kan de gastgebruiker uw uitnodiging inwisselen door op een directe link te klikken of door de uitnodigingse-mail te gebruiken. In beide gevallen geeft een bericht in de browser aan dat er een code naar het e-mailadres van de gastgebruiker wordt verzonden. De gastgebruiker selecteert **Verzendcode:**
 
   ![Schermafbeelding van de knop Code verzenden](media/one-time-passcode/otp-send-code.png)
 
Er wordt een toegangscode naar het e-mailadres van de gebruiker verzonden. De gebruiker haalt de toegangscode uit de e-mail en voert deze in het browservenster in:
 
   ![Schermafbeelding van de pagina Code invoeren](media/one-time-passcode/otp-enter-code.png)
 
De gastgebruiker is nu geverifieerd en kan de gedeelde bron zien of zich blijven aanmelden. 

> [!NOTE]
> Eenmalige toegangscodes zijn 30 minuten geldig. Na 30 minuten is die specifieke eenmalige toegangscode niet langer geldig en moet de gebruiker een nieuwe aanvragen. Gebruikerssessies verlopen na 24 uur. Na die tijd ontvangt de gastgebruiker een nieuwe toegangscode wanneer hij toegang krijgt tot de bron. Het verlopen van sessies biedt extra beveiliging, vooral wanneer een gastgebruiker zijn bedrijf verlaat of geen toegang meer nodig heeft.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Wanneer krijgt een gastgebruiker een eenmalige toegangscode?

Wanneer een gastgebruiker een uitnodiging inwisselt of een koppeling gebruikt naar een resource die met hem of haar is gedeeld, ontvangt hij of zij een eenmalige toegangscode als:
- Ze hebben geen Azure AD-account 
- Ze hebben geen Microsoft-account 
- De uitnodigende tenant heeft geen @gmail.com @googlemail.com Google-federatie ingesteld voor en gebruikers 

Op het moment van uitnodiging is er geen indicatie dat de gebruiker die u uitnodigt een malige wachtwoordverificatie zal gebruiken. Maar wanneer de gastgebruiker zich aanmeldt, is eenmalige wachtwoordverificatie de terugvalmethode als er geen andere verificatiemethoden kunnen worden gebruikt. 

U gastgebruikers die verifiëren met eenmalige toegangscodes in de Azure-portal bekijken door naar Azure Active > **Directory-organisatierelaties** > te gaan**Gebruikers van andere organisaties**. **Azure Active Directory**

![Schermafbeelding van een eenmalige toegangscodegebruiker met bronwaarde van OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Wanneer een gebruiker een eenmalige toegangscode inwisselt en later een MSA-, Azure AD-account of een ander federatief account ontvangt, worden deze persoon nog steeds geverifieerd met behulp van een eenmalige toegangscode. Als u de verificatiemethode wilt bijwerken, u hun gastgebruikersaccount verwijderen en opnieuw uitnodigen.

### <a name="example"></a>Voorbeeld
Gastgebruiker alexdoe@gmail.com wordt uitgenodigd voor Fabrikam, waar geen Google-federatie is ingesteld. Alex heeft geen Microsoft-account. Ze ontvangen een eenmalige toegangscode voor verificatie.

## <a name="opting-in-to-the-preview"></a>Aanmelden voor de preview 
Het kan enkele minuten duren voordat de opt-in-actie van kracht wordt. Daarna gebruiken alleen nieuw uitgenodigde gebruikers die aan bovenstaande voorwaarden voldoen eenmalige wachtwoordverificatie. Gastgebruikers die eerder een uitnodiging hebben ingewisseld, blijven dezelfde verificatiemethode gebruiken.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Aanmelden voor de Azure AD-portal
1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/) als globale beheerder van Azure AD.
2.  Selecteer Azure Active **Directory**in het navigatiedeelvenster .
3.  Selecteer **onder Beheren**de optie **Organisatierelaties**.
4.  Selecteer **Instellingen**.
5.  Selecteer **Enable Email One-Time Passcode for guests (Preview)** **Ja**.
 
### <a name="to-opt-in-using-powershell"></a>Aanmelden voor PowerShell

Eerst moet u de nieuwste versie van de Azure AD PowerShell for Graph-module (AzureADPreview) installeren. Vervolgens bepaalt u of er al B2B-beleid bestaat en voert u de juiste opdrachten uit.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Voorwaarde: Installeer de nieuwste AzureADPreview-module
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Controleren op bestaand beleid en aanmelden

Controleer vervolgens of er momenteel een B2BManagementPolicy bestaat door het volgende uit te voeren:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Als de uitvoer onwaar is, bestaat het beleid momenteel niet. Maak een nieuw B2BManagementPolicy en kies voor de preview door het volgende uit te voeren:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Als de uitvoer waar is, bestaat momenteel het Beleid B2BManagementBeleid. Voer het volgende uit om het beleid bij te werken en u aan te melden voor het voorbeeld:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Afmelden voor de preview nadat u zich hebt afmelden
Het kan enkele minuten duren voordat de opt-out-actie van kracht wordt. Als u de preview uitschakelt, kunnen gastgebruikers die een eenmalige toegangscode hebben ingewisseld, zich niet aanmelden. U de gastgebruiker verwijderen en de gebruiker opnieuw uitnodigen om zich opnieuw aan te melden met een andere verificatiemethode.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>De preview uitschakelen met de Azure AD-portal
1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/) als globale beheerder van Azure AD.
2.  Selecteer Azure Active **Directory**in het navigatiedeelvenster .
3.  Selecteer **onder Beheren**de optie **Organisatierelaties**.
4.  Selecteer **Instellingen**.
5.  Selecteer **Nee**onder **E-mailtoegangscode voor één keer inschakelen voor gasten (Voorbeeld).**

### <a name="to-turn-off-the-preview-using-powershell"></a>De preview uitschakelen met PowerShell
Installeer de nieuwste AzureADPreview-module als u deze nog niet hebt (zie [Vereiste: Installeer de nieuwste AzureADPreview-module](#prerequisite-install-the-latest-azureadpreview-module) hierboven). Controleer vervolgens of het eenmalige wachtwoordvoorbeeldbeleid momenteel bestaat door het volgende uit te voeren:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Als de uitvoer True is, u zich afmelden voor het voorbeeld door het volgende uit te voeren:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

