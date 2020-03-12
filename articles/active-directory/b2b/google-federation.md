---
title: Google toevoegen als een id-provider voor B2B-Azure AD
description: Communiceren met Google om gast gebruikers in staat te stellen zich aan te melden bij uw Azure AD-apps met hun eigen Gmail-account
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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126651"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google toevoegen als een id-provider voor B2B-gast gebruikers

Door Federatie met Google in te stellen, kunt u ervoor zorgen dat uitgenodigde gebruikers zich aanmelden bij uw gedeelde apps en resources met hun eigen Gmail-accounts, zonder dat u micro soft-accounts (Msa's) hoeft te maken. 

> [!NOTE]
> Google Federation is speciaal ontworpen voor Gmail-gebruikers. Als u wilt communiceren met G suite-domeinen, gebruikt u de [functie directe Federatie](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Wat is de ervaring van de Google-gebruiker?
Wanneer u een uitnodiging naar een Google Gmail-gebruiker verzendt, moet de gast gebruiker toegang krijgen tot uw gedeelde apps of bronnen met behulp van een koppeling die de context van de Tenant bevat. Hun ervaring varieert afhankelijk van of ze al zijn aangemeld bij Google:
  - Als de gast gebruiker niet is aangemeld bij Google, wordt u gevraagd zich aan te melden bij Google.
  - Als de gast gebruiker al is aangemeld bij Google, wordt u gevraagd het account te kiezen dat u wilt gebruiken. Ze moeten het account kiezen dat u hebt gebruikt om ze te nodigen.

Als de gast gebruiker de fout bericht ' header is te lang ' ziet, kan hij of zij de cookies proberen te wissen of een privé-of incognito-venster openen en het opnieuw proberen aan te melden.

![Scherm opname van de Google-aanmeldings pagina](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Beperkingen

Teams bieden volledige ondersteuning voor Google gast-gebruikers op alle apparaten. Google-gebruikers kunnen zich aanmelden bij teams vanaf een gemeen schappelijk eind punt zoals `https://teams.microsoft.com`.

De algemene eind punten van andere toepassingen bieden mogelijk geen ondersteuning voor Google-gebruikers. Google gast gebruikers moeten zich aanmelden met een koppeling die uw Tenant gegevens bevat. Hier volgen enkele voorbeelden:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Als gebruikers van Google gast een koppeling proberen te gebruiken, zoals `https://myapps.microsoft.com` of `https://portal.azure.com`, wordt er een fout bericht weer geven.

U kunt Google gast gebruikers ook een rechtstreekse koppeling geven naar een toepassing of resource, mits deze koppeling uw Tenant gegevens bevat, bijvoorbeeld `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Stap 1: een Google-ontwikkelaars project configureren
Maak eerst een nieuw project in de Google developers-console om een client-ID en een client geheim op te halen dat u later kunt toevoegen aan Azure AD. 
1. Ga naar de Google-Api's op https://console.developers.google.comen meld u aan met uw Google-account. We raden u aan een Google-account voor een gedeeld team te gebruiken.
2. Een nieuw project maken: Selecteer in het dash board **project maken**en selecteer vervolgens **maken**. Voer op de pagina nieuw project een **project naam**in en selecteer **maken**.
   
   ![Scherm opname met een nieuwe project pagina voor Google](media/google-federation/google-new-project.png)

3. Zorg ervoor dat het nieuwe project is geselecteerd in het menu Project. Selecteer vervolgens onder **api's & Services**het **venster OAuth-toestemming**.

4. Selecteer **extern**en selecteer vervolgens **maken**. 
5. Voer op het **scherm OAuth-toestemming**een **toepassings naam**in. (Behoud de andere instellingen.)

   ![Scherm opname van de Google OAuth-toestemming scherm optie](media/google-federation/google-oauth-consent-screen.png)

6. Ga naar de sectie **geautoriseerde domeinen** en voer microsoftonline.com in.

   ![Scherm opname van de sectie met gemachtigde domeinen](media/google-federation/google-oauth-authorized-domains.png)

7. Selecteer **Opslaan**.

8. **Referenties**kiezen. Kies in het menu **referenties maken** de optie **OAuth-client-id**.

   ![Scherm opname van de opties voor het maken van referenties met Google Api's](media/google-federation/google-api-credentials.png)

9. Onder **toepassings type**kiest u **Webtoepassing**en voert u onder **geautoriseerde omleidings-uri's**de volgende uri's in:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(waarbij `<directory id>` uw directory-ID is)
   
     > [!NOTE]
     > Om uw directory-ID te vinden, gaat u naar https://portal.azure.comen kiest u onder **Azure Active Directory**de optie **Eigenschappen** en kopieert u de **map-id**.

   ![Scherm afbeelding van de sectie geautoriseerde omleidings-Uri's](media/google-federation/google-create-oauth-client-id.png)

10. Selecteer **Maken**. Kopieer de client-ID en het client geheim dat u gebruikt wanneer u de ID-provider toevoegt in de Azure AD-Portal.

   ![Scherm opname met de OAuth-client-ID en het client geheim](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Stap 2: Google Federation configureren in azure AD 
Nu gaat u de Google client-ID en het client geheim instellen, hetzij door het in te voeren in de Azure AD-portal of met behulp van Power shell. Zorg ervoor dat u uw Google Federation-configuratie test door uzelf uit te nodigen met behulp van een Gmail-adres en probeert de uitnodiging te verwisselen met uw geuitgenodigde Google-account. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Google Federation configureren in de Azure AD-Portal 
1. Ga naar de [Azure Portal](https://portal.azure.com). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **organisatie relaties**.
3. Selecteer **id-providers**en klik vervolgens op de knop **Google** .
4. Voer een naam in. Voer vervolgens de client-ID en het client geheim in die u eerder hebt verkregen. Selecteer **Opslaan**. 

   ![Scherm afbeelding van de pagina Google-ID-provider toevoegen](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Google Federation configureren met behulp van Power shell
1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Voer de volgende opdracht uit: `Connect-AzureAD`.
3. Meld u bij de aanmeldings prompt aan met het Managed Global Administrator-account.  
4. Voer de volgende opdracht uit: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Gebruik de client-id en het client geheim van de app die u hebt gemaakt in ' stap 1: een Google-ontwikkelaars project configureren '. Zie het artikel [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) voor meer informatie. 
 
## <a name="how-do-i-remove-google-federation"></a>Google Federation Hoe kan ik verwijderen?
U kunt de installatie van Google Federation verwijderen. Als u dit doet, kunnen Google gast-gebruikers die hun uitnodiging al hebben ingewisseld, zich niet meer aanmelden, maar u kunt ze ook weer toegang geven tot uw resources door ze te verwijderen uit de map en ze opnieuw uit te nodigen. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Google Federation verwijderen in de Azure AD-portal: 
1. Ga naar de [Azure Portal](https://portal.azure.com). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **organisatie relaties**.
3. Selecteer **id-providers**.
4. Selecteer op de **Google** -regel het snelmenu ( **...** ) en selecteer vervolgens **verwijderen**. 
   
   ![Scherm afbeelding van de optie verwijderen voor de sociale ID-provider](media/google-federation/google-social-identity-providers.png)

1. Selecteer **Ja** om de verwijdering te bevestigen. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Google Federation verwijderen met behulp van Power shell: 
1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Voer `Connect-AzureAD` uit.  
4. Meld u bij de prompt aanmelden op met het beheerde account voor globale beheerders.  
5. Voer de volgende opdracht in:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Zie [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)voor meer informatie. 
