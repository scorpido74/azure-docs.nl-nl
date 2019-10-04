---
title: 'Azure AD Connect: Naadloze, eenmalige aanmelding: veelgestelde vragen | Microsoft Docs'
description: Antwoorden op veelgestelde vragen over Azure Active Directory naadloze eenmalige aanmelding.
services: active-directory
keywords: Wat is Azure AD Connect, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b12acf083e83d42ff3e8d6967d747f4bb2d93543
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960203"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory naadloze eenmalige aanmelding: Veelgestelde vragen

In dit artikel adresseren we veelgestelde vragen over Azure Active Directory naadloze eenmalige aanmelding (naadloze SSO). Blijf op de hoogte van nieuwe inhoud.

**V: Met welke aanmeldings methoden kan naadloze SSO werken met @ no__t-0

Naadloze SSO kan worden gecombineerd met de aanmeldings methoden voor [wachtwoord-hash](how-to-connect-password-hash-synchronization.md) of [Pass-Through-verificatie](how-to-connect-pta.md) . Deze functie kan echter niet worden gebruikt met Active Directory Federation Services (ADFS).

**V: Is naadloze SSO een gratis functie?**

Naadloze SSO is een gratis functie en u hebt geen betaalde versies van Azure AD nodig om deze te gebruiken.

**V: Is naadloze SSO beschikbaar in de [Microsoft Azure Duitsland Cloud](https://www.microsoft.de/cloud-deutschland) en de [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/)?**

Nee. Naadloze SSO is alleen beschikbaar in het wereld wijde exemplaar van Azure AD.

**V: Welke toepassingen profiteren van de `domain_hint`-of `login_hint` parameter mogelijkheid van naadloze SSO?**

Hieronder vindt u een niet-limitatieve lijst met toepassingen die deze para meters naar Azure AD kunnen verzenden, en waarmee gebruikers zich op de achtergrond aanmelden met naadloze SSO (dat wil zeggen dat uw gebruikers geen gebruikers namen of wacht woorden hoeven in te voeren):

| Toepassingsnaam | URL van de toepassing die moet worden gebruikt |
| -- | -- |
| Toegangsvenster | https: \//MyApps. Microsoft. com/contoso. com |
| Outlook op Internet | https:\//outlook.office365.com/contoso.com |
| Office 365-portals | https: \//Portal. Office. com? domain_hint = contoso. com, https: \//www. Office. com? domain_hint = contoso. com |

Daarnaast krijgen gebruikers een stille aanmeldings ervaring als een toepassing aanmeldings aanvragen verzendt naar de eind punten van Azure AD die als tenants zijn ingesteld, dat wil zeggen https: \//login. microsoftonline. com/contoso. com/<.. > of https: \//login. microsoftonline. com/< tenant_ID >/<.. >-in plaats van het algemene eind punt van Azure AD, dat wil zeggen, https: \//login. microsoftonline. com/common/<... >. Hieronder vindt u een niet-limitatieve lijst van toepassingen die deze typen aanmeldings aanvragen maken.

| Toepassingsnaam | URL van de toepassing die moet worden gebruikt |
| -- | -- |
| SharePoint Online | https: \//contoso. share point. com |
| Azure Portal | https: \//Portal. Azure. com/contoso. com |

Vervang in de bovenstaande tabellen ' contoso.com ' door de domein naam om naar de juiste toepassings-Url's voor uw Tenant te gaan.

Als u wilt dat andere toepassingen gebruikmaken van de aanmeldings ervaring op de achtergrond, laat het ons weten in het gedeelte feedback.

**V: Biedt naadloze SSO ondersteuning `Alternate ID` als de gebruikers naam, in plaats van `userPrincipalName`?**

Ja. Naadloze SSO ondersteunt `Alternate ID` als de gebruikers naam wanneer deze is geconfigureerd in Azure AD Connect, zoals [hier](how-to-connect-install-custom.md)wordt weer gegeven. Niet alle Office 365-toepassingen ondersteunen `Alternate ID`. Raadpleeg de documentatie van de specifieke toepassing voor de ondersteunings verklaring.

**V: Wat is het verschil tussen de eenmalige aanmelding van [Azure AD-deelname](../active-directory-azureadjoin-overview.md) en naadloze SSO?**

[Azure AD-deelname](../active-directory-azureadjoin-overview.md) levert SSO aan gebruikers als hun apparaten zijn geregistreerd bij Azure AD. Deze apparaten hoeven geen lid te zijn van een domein. SSO wordt met behulp van *primaire vernieuwings tokens* of *PRTs*, en niet met Kerberos. De gebruikers ervaring is het meest optimaal op Windows 10-apparaten. SSO wordt automatisch uitgevoerd in de micro soft Edge-browser. Het werkt ook op Chrome met het gebruik van een browser extensie.

U kunt zowel Azure AD-deelname als naadloze SSO gebruiken voor uw Tenant. Deze twee functies zijn complementair. Als beide functies zijn ingeschakeld, heeft SSO van Azure AD-deelname voor rang op naadloze SSO.

**V: Ik wil niet-Windows 10-apparaten registreren bij Azure AD zonder AD FS te gebruiken. Kan ik in plaats daarvan naadloze SSO gebruiken?**

Ja, voor dit scenario is versie 2,1 of hoger van de [werk plek-client](https://www.microsoft.com/download/details.aspx?id=53554)vereist.

**V: Hoe kan ik de Kerberos-ontsleutelings sleutel van het `AZUREADSSOACC`-computer account inrollen?**

Het is belang rijk om regel matig over te brengen van de Kerberos-ontsleutelingssleutel van het `AZUREADSSOACC`-computer account (dat staat voor Azure AD) dat is gemaakt in uw on-premises AD-forest.

>[!IMPORTANT]
>We raden u ten zeerste aan de Kerberos-ontsleutelingssleutel ten minste elke 30 dagen uit te voeren.

Volg deze stappen op de on-premises server waarop u Azure AD Connect:

   **Step 1. Lijst met AD-forests ophalen waar naadloze SSO is ingeschakeld @ no__t-0

   1. Down load en installeer eerst [Azure AD Power shell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de naadloze SSO Power shell-module met de volgende opdracht: `Import-Module .\AzureADSSO.psd1`.
   4. Voer Power shell uit als beheerder. Roep `New-AzureADSSOAuthenticationContext` aan in Power shell. Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
   5. Roep `Get-AzureADSSOStatus | ConvertFrom-Json` aan. Met deze opdracht geeft u de lijst met AD-forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

   **Step 2. Werk de Kerberos-ontsleutelingssleutel bij op elk AD-forest waarop het is ingesteld op @ no__t-0

   1. Roep `$creds = Get-Credential` aan. Wanneer u hierom wordt gevraagd, voert u de referenties voor de domein beheerder in voor het beoogde AD-forest.

   > [!NOTE]
   > We gebruiken de gebruikers naam van de domein beheerder, opgegeven in de indeling User Principal Names (UPN) (johndoe@contoso.com) of de indeling van het domein gekwalificeerde SAM-account naam (contoso\johndoe of contoso. com\johndoe) om het beoogde AD-forest te vinden. Als u domein gekwalificeerde SAM-account naam gebruikt, gebruiken we het domein gedeelte van de gebruikers naam voor [het zoeken van de domein controller van de domein beheerder met behulp van DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Als u in plaats daarvan UPN gebruikt, [vertalen we het naar een domein gekwalificeerde SAM-account naam voordat u](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) de juiste domein controller zoekt.

   2. Roep `Update-AzureADSSOForest -OnPremCredentials $creds` aan. Met deze opdracht wordt de Kerberos-ontsleutelings sleutel voor het `AZUREADSSOACC`-computer account in dit specifieke AD-forest bijgewerkt en bijgewerkt in azure AD.
   3. Herhaal de voor gaande stappen voor elk AD-forest waarop u de functie hebt ingesteld.

   >[!IMPORTANT]
   >Zorg ervoor dat u de `Update-AzureADSSOForest`-opdracht _niet_ meer dan één keer uitvoert. Anders werkt de functie niet meer wanneer de Kerberos-tickets van uw gebruikers verlopen en opnieuw worden uitgegeven door uw on-premises Active Directory.

**V: Hoe kan ik naadloze SSO uitschakelen?**

   **Step 1. De functie op uw Tenant uitschakelen @ no__t-0

   **Option A: Uitschakelen met behulp van Azure AD Connect @ no__t-0
    
   1. Voer Azure AD Connect uit, kies **pagina gebruikers aanmelding wijzigen** en klik op **volgende**.
   2. Schakel de optie **eenmalige aanmelding inschakelen** uit. Ga door met de wizard.

   Na het volt ooien van de wizard wordt naadloze SSO uitgeschakeld voor uw Tenant. Er wordt echter een bericht weer gegeven op het scherm dat er als volgt uitziet:

   Eenmalige aanmelding is nu uitgeschakeld, maar er zijn aanvullende hand matige stappen die moeten worden uitgevoerd om de opschoon bewerking te volt ooien. Meer informatie '

   Volg de stappen 2 en 3 op de on-premises server waarop u Azure AD Connect hebt om het opschonings proces te volt ooien.

   **Option B: Uitschakelen met behulp van Power shell @ no__t-0

   Voer de volgende stappen uit op de on-premises server waarop u Azure AD Connect uitvoert:

   1. Down load en installeer eerst [Azure AD Power shell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de naadloze SSO Power shell-module met de volgende opdracht: `Import-Module .\AzureADSSO.psd1`.
   4. Voer Power shell uit als beheerder. Roep `New-AzureADSSOAuthenticationContext` aan in Power shell. Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
   5. Roep `Enable-AzureADSSO -Enable $false` aan.

   >[!IMPORTANT]
   >Door naadloze SSO uit te scha kelen met behulp van Power shell, wordt de status in Azure AD Connect niet gewijzigd. Naadloze SSO wordt weer gegeven als ingeschakeld op de aanmeldings pagina van de **gebruiker wijzigen** .

   **Step 2. Lijst met AD-forests ophalen waar naadloze SSO is ingeschakeld @ no__t-0

   Volg de onderstaande taken 1 tot en met 4 als u naadloze SSO hebt uitgeschakeld met Azure AD Connect. Als u naadloze SSO hebt uitgeschakeld met behulp van Power shell, gaat u verder met taak 5 hieronder.

   1. Down load en installeer eerst [Azure AD Power shell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de naadloze SSO Power shell-module met de volgende opdracht: `Import-Module .\AzureADSSO.psd1`.
   4. Voer Power shell uit als beheerder. Roep `New-AzureADSSOAuthenticationContext` aan in Power shell. Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
   5. Roep `Get-AzureADSSOStatus | ConvertFrom-Json` aan. Met deze opdracht geeft u de lijst met AD-forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

   **Step 3. Verwijder hand matig het `AZUREADSSOACCT`-computer account uit elk AD-forest dat wordt weer gegeven.**

## <a name="next-steps"></a>Volgende stappen

- [**Quick**](how-to-connect-sso-quick-start.md) start: krijg Azure AD naadloze SSO.
- [**Technisch diep gaande**](how-to-connect-sso-how-it-works.md) kennis van de werking van deze functie.
- [**Problemen oplossen**](tshoot-connect-sso.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -voor het indienen van nieuwe functie aanvragen.
