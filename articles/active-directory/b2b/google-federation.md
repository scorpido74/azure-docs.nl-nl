---
title: Google toevoegen als identiteitsprovider voor B2B - Azure AD
description: Federatie met Google om gastgebruikers in staat te stellen zich aan te melden bij uw Azure AD-apps met hun eigen Gmail-account
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126651"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google toevoegen als identiteitsprovider voor B2B-gastgebruikers

Door federatie met Google in te stellen, u uitgenodigde gebruikers toestaan zich aan te melden bij uw gedeelde apps en bronnen met hun eigen Gmail-accounts, zonder dat u Microsoft-accounts (MSA's) hoeft aan te maken. 

> [!NOTE]
> Google federation is speciaal ontworpen voor Gmail-gebruikers. Als u wilt reageren op G Suite-domeinen, gebruikt u de [functie Directe federatie](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Wat is de ervaring voor de Google-gebruiker?
Wanneer u een uitnodiging naar een Google Gmail-gebruiker verzendt, moet de gastgebruiker toegang krijgen tot uw gedeelde apps of bronnen via een koppeling die de context van de tenant bevat. Hun ervaring is afhankelijk van of ze al zijn aangemeld bij Google:
  - Als de gastgebruiker niet is aangemeld bij Google, wordt hem of haar gevraagd zich aan te melden bij Google.
  - Als de gastgebruiker al is aangemeld bij Google, wordt hem of haar gevraagd het account te kiezen dat hij of zij wil gebruiken. Ze moeten het account kiezen waarmee je ze hebt uitgenodigd.

Als de gastgebruiker een fout 'header te lang' ziet, kan hij of zij proberen zijn cookies te wissen, of kunnen ze een privé- of incognitovenster openen en opnieuw proberen in te melden.

![Schermafbeelding van de aanmeldingspagina van Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Beperkingen

Teams ondersteunt google-gastgebruikers volledig op alle apparaten. Google-gebruikers kunnen zich aanmelden bij Teams `https://teams.microsoft.com`vanaf een gemeenschappelijk eindpunt zoals .

De algemene eindpunten van andere toepassingen ondersteunen mogelijk geen Google-gebruikers. Gastgebruikers van Google moeten zich aanmelden via een koppeling met uw tenantgegevens. Hier volgen enkele voorbeelden:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Als Gastgebruikers van Google een `https://myapps.microsoft.com` koppeling `https://portal.azure.com`proberen te gebruiken, zoals of , krijgen ze een foutmelding.

U google-gastgebruikers ook een directe link naar een toepassing of bron geven, zolang deze link bijvoorbeeld `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`uw tenantgegevens bevat. 

## <a name="step-1-configure-a-google-developer-project"></a>Stap 1: Een Ontwikkelaarsproject van Google configureren
Maak eerst een nieuw project in de Google Developers Console om een client-id en een clientgeheim te verkrijgen dat u later aan Azure AD toevoegen. 
1. Ga naar de Google-API's op https://console.developers.google.comen meld u aan met uw Google-account. We raden je aan een google-account van een gedeeld team te gebruiken.
2. Een nieuw project maken: selecteer in het dashboard **Project maken**en selecteer **Vervolgens Maken**. Voer op de pagina Nieuw project een **projectnaam**in en selecteer **Vervolgens Maken**.
   
   ![Schermafbeelding van een nieuwe projectpagina voor Google](media/google-federation/google-new-project.png)

3. Zorg ervoor dat uw nieuwe project is geselecteerd in het projectmenu. Selecteer vervolgens onder **API's & Services**het **scherm OAuth-toestemming**.

4. Selecteer **Extern**en selecteer **Vervolgens Maken**. 
5. Voer op het **scherm OAuth-toestemming**een **toepassingsnaam**in . (Laat de andere instellingen achter.)

   ![Schermafbeelding van de optie Google OAuth-toestemmingsscherm](media/google-federation/google-oauth-consent-screen.png)

6. Schuif naar de sectie **Geautoriseerde domeinen** en voer microsoftonline.com in.

   ![Schermafbeelding van de sectie Geautoriseerde domeinen](media/google-federation/google-oauth-authorized-domains.png)

7. Selecteer **Opslaan**.

8. Kies **Referenties**. Kies In het menu **Referenties maken** de optie **OAuth-client-id**.

   ![Schermafbeelding van de optie Google-API's voor referenties maken](media/google-federation/google-api-credentials.png)

9. Voer **onder Toepassingstype** **Webtoepassing**en vervolgens onder **Geautoriseerde omleidings-URI's**de volgende URI's in:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(waar `<directory id>` is uw directory-id)
   
     > [!NOTE]
     > Als u uw adreslijst-id wilt zoeken, https://portal.azure.comgaat u naar en kiest u onder Azure Active **Directory**de optie **Eigenschappen** en kopieert u de **directory-id**.

   ![Schermafbeelding van de sectie Ani's voor geautoriseerde omleiding](media/google-federation/google-create-oauth-client-id.png)

10. Selecteer **Maken**. Kopieer de client-id en het clientgeheim, dat u gebruikt wanneer u de identiteitsprovider toevoegt aan de Azure AD-portal.

   ![Schermafbeelding van de OAuth-client-id en het clientgeheim](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Stap 2: Google-federatie configureren in Azure AD 
Nu stelt u de Google-client-id en client geheim in, door deze in te voeren in de Azure AD-portal of door PowerShell te gebruiken. Zorg ervoor dat u de configuratie van uw Google-federatie test door uzelf uit te nodigen via een Gmail-adres en te proberen de uitnodiging in te wisselen met uw uitgenodigde Google-account. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Google-federatie configureren in de Azure AD-portal 
1. Ga naar de [Azure-portal.](https://portal.azure.com) Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **Organisatierelaties**.
3. Selecteer **Identiteitsproviders**en klik op **de** google-knop.
4. Voer een naam in. Voer vervolgens de client-ID en het klantgeheim in dat u eerder hebt verkregen. Selecteer **Opslaan**. 

   ![Schermafbeelding van de pagina Google-identiteitsprovider toevoegen](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Google-federatie configureren met PowerShell
1. Installeer de nieuwste versie van de Azure AD PowerShell voor Grafiekmodule[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Voer de volgende `Connect-AzureAD`opdracht uit: .
3. Meld u bij de aanmeldingsprompt aan met het beheerde account van global administrator.  
4. Voer de volgende opdracht uit: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Gebruik de client-id en het clientgeheim van de app die u hebt gemaakt in 'Stap 1: Een Ontwikkelaarsproject van Google configureren'. Zie het artikel [Nieuw-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) voor meer informatie. 
 
## <a name="how-do-i-remove-google-federation"></a>Hoe verwijder ik de Google-federatie?
U de installatie van uw Google-federatie verwijderen. Als u dit doet, kunnen gastgebruikers van Google die hun uitnodiging al hebben ingewisseld, zich niet aanmelden, maar u ze opnieuw toegang geven tot uw bronnen door ze uit de directory te verwijderen en ze opnieuw uit te nodigen. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Ga als eerste over de verwijdering van Google-federatie in de Azure AD-portal: 
1. Ga naar de [Azure-portal.](https://portal.azure.com) Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **Organisatierelaties**.
3. Selecteer **identiteitsproviders**.
4. Selecteer in de **Google-regel** het contextmenu (**... )** en selecteer **Verwijderen**. 
   
   ![Schermafbeelding van de optie Verwijderen voor de aanbieder van sociale identiteit](media/google-federation/google-social-identity-providers.png)

1. Selecteer **Ja** om verwijdering te bevestigen. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Google Federation verwijderen met PowerShell: 
1. Installeer de nieuwste versie van de Azure AD PowerShell voor Grafiekmodule[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Voer `Connect-AzureAD` uit.  
4. Meld u in de aanmeldingsprompt aan met het beheerde Global Administrator-account.  
5. Voer de volgende opdracht in:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Zie [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)voor meer informatie. 
