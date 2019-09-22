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
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176657"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory naadloze eenmalige aanmelding: Veelgestelde vragen

In dit artikel adresseren we veelgestelde vragen over Azure Active Directory naadloze eenmalige aanmelding (naadloze SSO). Blijf op de hoogte van nieuwe inhoud.

**V: Met welke aanmeldings methoden kan naadloze SSO werken**

Naadloze SSO kan worden gecombineerd met de aanmeldings methoden voor [wachtwoord-hash](how-to-connect-password-hash-synchronization.md) of [Pass-Through-verificatie](how-to-connect-pta.md) . Deze functie kan echter niet worden gebruikt met Active Directory Federation Services (ADFS).

**V: Is naadloze SSO een gratis functie?**

Naadloze SSO is een gratis functie en u hebt geen betaalde versies van Azure AD nodig om deze te gebruiken.

**V: Is naadloze SSO beschikbaar in de [Microsoft Azure Duitsland Cloud](https://www.microsoft.de/cloud-deutschland) en de [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/)?**

Nee. Naadloze SSO is alleen beschikbaar in het wereld wijde exemplaar van Azure AD.

**V: Welke toepassingen profiteren van `domain_hint` de of `login_hint` de mogelijkheden van naadloze SSO?**

Hieronder vindt u een niet-limitatieve lijst met toepassingen die deze para meters naar Azure AD kunnen verzenden, en waarmee gebruikers zich op de achtergrond aanmelden met naadloze SSO (dat wil zeggen dat uw gebruikers geen gebruikers namen of wacht woorden hoeven in te voeren):

| Toepassingsnaam | URL van de toepassing die moet worden gebruikt |
| -- | -- |
| Toegangsvenster | https:\//MyApps.Microsoft.com/contoso.com |
| Outlook op Internet | https:\//outlook.office365.com/contoso.com |
| Office 365-portals | https:\//Portal.Office.com? domain_hint = contoso. com, https:\//www.Office.com? domain_hint = contoso. com |

Daarnaast krijgen gebruikers een stille aanmeldings ervaring als een toepassing aanmeldings aanvragen verzendt naar de eind punten van Azure AD die als tenants zijn ingesteld, dat wil zeggen, https:\//login.microsoftonline.com/contoso.com/<.. > of https:\//login.microsoftonline.com/<tenant_ID>/<.. >-in plaats van het algemene eind punt van Azure AD, dat wil\/zeggen, https:/Login.microsoftonline.com/common/<... >. Hieronder vindt u een niet-limitatieve lijst van toepassingen die deze typen aanmeldings aanvragen maken.

| Toepassingsnaam | URL van de toepassing die moet worden gebruikt |
| -- | -- |
| SharePoint Online | https:\//contoso.SharePoint.com |
| Azure Portal | https:\//Portal.Azure.com/contoso.com |

Vervang in de bovenstaande tabellen ' contoso.com ' door de domein naam om naar de juiste toepassings-Url's voor uw Tenant te gaan.

Als u wilt dat andere toepassingen gebruikmaken van de aanmeldings ervaring op de achtergrond, laat het ons weten in het gedeelte feedback.

**V: Biedt naadloze SSO- `Alternate ID` ondersteuning als de gebruikers naam, `userPrincipalName`in plaats van?**

Ja. Naadloze SSO ondersteunt `Alternate ID` als de gebruikers naam wanneer deze is geconfigureerd in azure AD Connect, zoals [hier](how-to-connect-install-custom.md)wordt weer gegeven. Niet alle Office 365-toepassingen `Alternate ID`ondersteunen. Raadpleeg de documentatie van de specifieke toepassing voor de ondersteunings verklaring.

**V: Wat is het verschil tussen de eenmalige aanmelding van [Azure AD](../active-directory-azureadjoin-overview.md) en naadloze SSO?**

[Azure AD-deelname](../active-directory-azureadjoin-overview.md) levert SSO aan gebruikers als hun apparaten zijn geregistreerd bij Azure AD. Deze apparaten hoeven geen lid te zijn van een domein. SSO wordt met behulp van *primaire vernieuwings tokens* of *PRTs*, en niet met Kerberos. De gebruikers ervaring is het meest optimaal op Windows 10-apparaten. SSO wordt automatisch uitgevoerd in de micro soft Edge-browser. Het werkt ook op Chrome met het gebruik van een browser extensie.

U kunt zowel Azure AD-deelname als naadloze SSO gebruiken voor uw Tenant. Deze twee functies zijn complementair. Als beide functies zijn ingeschakeld, heeft SSO van Azure AD-deelname voor rang op naadloze SSO.

**V: Ik wil niet-Windows 10-apparaten registreren bij Azure AD zonder AD FS te gebruiken. Kan ik in plaats daarvan naadloze SSO gebruiken?**

Ja, voor dit scenario is versie 2,1 of hoger van de [werk plek-client](https://www.microsoft.com/download/details.aspx?id=53554)vereist.

**V: Hoe kan ik de Kerberos-ontsleutelings sleutel van het `AZUREADSSOACC` computer account inrollen?**

Het is belang rijk dat u de Kerberos-ontsleutelings sleutel voor `AZUREADSSOACC` het computer account (dat staat voor Azure AD) die is gemaakt in uw on-premises AD-forest regel matig wilt door lopen.

>[!IMPORTANT]
>We raden u ten zeerste aan de Kerberos-ontsleutelingssleutel ten minste elke 30 dagen uit te voeren.

Volg deze stappen op de on-premises server waarop u Azure AD Connect:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Down load en installeer eerst [Azure AD Power shell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importeer de naadloze SSO Power shell-module met de `Import-Module .\AzureADSSO.psd1`volgende opdracht:.
    4. Voer Power shell uit als beheerder. Bel `New-AzureADSSOAuthenticationContext`in Power shell. Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
    5. Aanroep `Get-AzureADSSOStatus | ConvertFrom-Json`. Met deze opdracht geeft u de lijst met AD-forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

    **Stap 2. De Kerberos-ontsleutelings sleutel bijwerken op elk AD-forest waarop deze is ingesteld**

    1. Aanroep `$creds = Get-Credential`. Wanneer u hierom wordt gevraagd, voert u de referenties voor de domein beheerder in voor het beoogde AD-forest.

    > [!NOTE]
    > We gebruiken de gebruikers naam van de domein beheerder, opgegeven in de indeling User Principal Names (johndoe@contoso.comUPN) () of de indeling van het domein gekwalificeerde SAM-account naam (contoso\johndoe of contoso. com\johndoe), om het beoogde AD-forest te vinden. Als u domein gekwalificeerde SAM-account naam gebruikt, gebruiken we het domein gedeelte van de gebruikers naam voor [het zoeken van de domein controller van de domein beheerder met behulp van DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Als u in plaats daarvan UPN gebruikt, [vertalen we het naar een domein gekwalificeerde SAM-account naam voordat u](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) de juiste domein controller zoekt.

    2. Aanroep `Update-AzureADSSOForest -OnPremCredentials $creds`. Met deze opdracht wordt de Kerberos-ontsleutelings `AZUREADSSOACC` sleutel voor het computer account in dit specifieke AD-forest bijgewerkt en bijgewerkt in azure AD.
    3. Herhaal de voor gaande stappen voor elk AD-forest waarop u de functie hebt ingesteld.

    >[!IMPORTANT]
    >Zorg ervoor dat u de `Update-AzureADSSOForest` opdracht niet meer dan één keer uitvoert. Anders werkt de functie niet meer wanneer de Kerberos-tickets van uw gebruikers verlopen en opnieuw worden uitgegeven door uw on-premises Active Directory.

**V: Hoe kan ik naadloze SSO uitschakelen?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Down load en installeer eerst [Azure AD Power shell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importeer de naadloze SSO Power shell-module met de `Import-Module .\AzureADSSO.psd1`volgende opdracht:.
    4. Voer Power shell uit als beheerder. Bel `New-AzureADSSOAuthenticationContext`in Power shell. Met deze opdracht geeft u een pop-up om de globale beheerders referenties van uw Tenant in te voeren.
    5. Aanroep `Get-AzureADSSOStatus | ConvertFrom-Json`. Met deze opdracht geeft u de lijst met AD-forests weer (Bekijk de lijst ' domeinen ') waarop deze functie is ingeschakeld.

    **Stap 3. Verwijder het `AZUREADSSOACCT` computer account hand matig uit elk AD-forest dat wordt weer gegeven.**

## <a name="next-steps"></a>Volgende stappen

- [**Quick Start**](how-to-connect-sso-quick-start.md) : krijg Azure AD naadloze SSO en voer deze uit.
- [**Technisch diep gaande**](how-to-connect-sso-how-it-works.md) kennis van de werking van deze functie.
- [**Problemen oplossen**](tshoot-connect-sso.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -voor het indienen van nieuwe functie aanvragen.
