---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504390"
---
## <a name="steps-to-enable-single-sign-on"></a>Stappen om eenmalig aanmelden in te schakelen
Cloudprovisioning werkt met Single Sign-on.  Momenteel is er geen optie om SSO in te schakelen wanneer de agent is geïnstalleerd, maar u de onderstaande stappen gebruiken om SSO in te schakelen en te gebruiken. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Stap 1: Azure AD Connect-bestanden downloaden en extraheren
1.  Download eerst de nieuwste versie van [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Open een opdrachtprompt met beheerdersbevoegdheden en navigeer naar de msi die u zojuist hebt gedownload.
3.  Voer het volgende uit:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Wijzig bestandspad en extractmap om overeen te komen met uw bestandspad en de naam van uw extractiemap.  De inhoud moet nu in de extractiemap staan.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Stap 2: De Seamless SSO PowerShell-module importeren

1. Azure AD [PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)downloaden en installeren .
2. Blader naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importeer de Seamless SSO PowerShell-module `Import-Module .\AzureADSSO.psd1`met deze opdracht: .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Stap 3: Download de lijst met Active Directory-forests waarop Seamless SSO is ingeschakeld

1. Voer PowerShell uit als beheerder. Bel in `New-AzureADSSOAuthenticationContext`PowerShell. Voer de algemene beheerdersreferenties van uw tenant in wanneer u daarom wordt gevraagd.
2. Bel `Get-AzureADSSOStatus`. Deze opdracht biedt u de lijst met Active Directory-forests (kijk naar de lijst Domeinen) waarop deze functie is ingeschakeld.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Stap 4: Naadloze SSO inschakelen voor elk Active Directory-forest

1. Bel `Enable-AzureADSSOForest`. Wanneer u daarom wordt gevraagd, voert u de referenties van de domeinbeheerder in voor het beoogde Active Directory-forest.

   > [!NOTE]
   >De gebruikersnaam van de domeinbeheerder moet worden ingevoerd in de SAM-accountnaamnotatie (contoso\johndoe of contoso.com\johndoe). We gebruiken het domeingedeelte van de gebruikersnaam om de domeincontroller van de domeinbeheerder te vinden met BEHULP van DNS.

   >[!NOTE]
   >Het gebruikte domeinbeheerdersaccount mag geen lid zijn van de groep Beveiligde gebruikers. Als dat het zo is, zal de bewerking mislukken.

2. Herhaal de vorige stap voor elk Active Directory-forest waarin u de functie wilt instellen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Stap 5. De functie op uw tenant inschakelen

Als u de functie op `Enable-AzureADSSO -Enable $true`uw tenant wilt inschakelen, belt u.
