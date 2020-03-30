---
title: 'Azure AD Connect: naadloze aanmeldingsprobleem - veelgestelde vragen | Microsoft Documenten'
description: Antwoorden op veelgestelde vragen over Azure Active Directory Seamless Single Sign-On.
services: active-directory
keywords: wat is Azure AD Connect, Active Directory installeren, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025673"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory Seamless Single Sign-On: veelgestelde vragen

In dit artikel behandelen we veelgestelde vragen over Azure Active Directory Seamless Single Sign-On (Seamless SSO). Blijf terugkomen voor nieuwe inhoud.

**V: Met welke aanmeldingsmethoden werken Seamless SSO**

Naadloze SSO kan worden gecombineerd met de [aanmeldingsmethoden voor wachtwoordhashsynchronisatie](how-to-connect-password-hash-synchronization.md) of [pass-through-verificatie.](how-to-connect-pta.md) Deze functie kan echter niet worden gebruikt met Active Directory Federation Services (ADFS).

**V: Is Seamless SSO een gratis functie?**

Naadloze SSO is een gratis functie en u hebt geen betaalde edities van Azure AD nodig om het te gebruiken.

**V: Is Seamless SSO beschikbaar in de [Microsoft Azure Germany-cloud](https://www.microsoft.de/cloud-deutschland) en de [Microsoft Azure Government-cloud?](https://azure.microsoft.com/features/gov/)**

Nee. Naadloze SSO is alleen beschikbaar in het wereldwijde exemplaar van Azure AD.

**V: Welke toepassingen `domain_hint` maken `login_hint` gebruik van of parametermogelijkheden van Seamless SSO?**

Hieronder vindt u een niet-limitatieve lijst met toepassingen die deze parameters naar Azure AD kunnen verzenden en biedt gebruikers daarom een stille aanmeldingservaring met Naadloze SSO (d.w.z. uw gebruikers hoeven hun gebruikersnamen of wachtwoorden niet in te voeren):

| De naam van de toepassing | Te gebruiken URL van toepassing |
| -- | -- |
| Toegangsvenster | https:\//myapps.microsoft.com/contoso.com |
| Outlook on Web | https:\//outlook.office365.com/contoso.com |
| Office 365-portals | https:\//portal.office.com?domain_hint=contoso.com,\/https: /www.office.com?domain_hint=contoso.com |

Daarnaast krijgen gebruikers een stille aanmeldingservaring als een toepassing aanmeldingsverzoeken verzendt naar de eindpunten van\/Azure AD\/die zijn ingesteld als tenants - https: /login.microsoftonline.com/contoso.com/<..> of https: /login.microsoftonline.com/<tenant_ID>/<.. > - in plaats van het algemene eindpunt van\/Azure AD - https: /login.microsoftonline.com/common/<...>. Hieronder vindt u een niet-limitatieve lijst van toepassingen die dit soort aanmeldingsverzoeken indienen.

| De naam van de toepassing | Te gebruiken URL van toepassing |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure Portal | https:\//portal.azure.com/contoso.com |

Vervang in de bovenstaande tabellen 'contoso.com' door uw domeinnaam om bij de juiste toepassings-URL's voor uw tenant te komen.

Als u andere toepassingen wilt gebruiken met onze stille aanmeldingservaring, laat het ons dan weten in de feedbacksectie.

**V: Ondersteunt Seamless `Alternate ID` SSO als gebruikersnaam, in plaats van `userPrincipalName`?**

Ja. Naadloze SSO `Alternate ID` ondersteunt als gebruikersnaam wanneer deze is geconfigureerd in Azure AD Connect, zoals [hier](how-to-connect-install-custom.md)wordt weergegeven. Niet alle Office 365-toepassingen ondersteunen `Alternate ID`. Raadpleeg de documentatie van de specifieke toepassing voor de ondersteuningsverklaring.

**V: Wat is het verschil tussen de single sign-on ervaring van [Azure AD Join](../active-directory-azureadjoin-overview.md) en Seamless SSO?**

[Azure AD Join](../active-directory-azureadjoin-overview.md) biedt SSO aan gebruikers als hun apparaten zijn geregistreerd bij Azure AD. Deze apparaten hoeven niet per se met het domein te worden verbonden. SSO wordt geleverd met behulp van *primaire refresh tokens* of *PWT's,* en niet Kerberos. De gebruikerservaring is het meest optimaal op Windows 10-apparaten. SSO gebeurt automatisch in de Microsoft Edge-browser. Het werkt ook op Chrome met het gebruik van een browserextensie.

U zowel Azure AD Join als Seamless SSO gebruiken op uw tenant. Deze twee functies zijn complementair. Als beide functies zijn ingeschakeld, heeft SSO van Azure AD Join voorrang op Seamless SSO.

**V: Ik wil niet-Windows 10-apparaten registreren bij Azure AD, zonder AD FS te gebruiken. Kan ik in plaats daarvan Seamless SSO gebruiken?**

Ja, dit scenario vereist versie 2.1 of hoger van de client die lid is van de [werkplek.](https://www.microsoft.com/download/details.aspx?id=53554)

**V: Hoe kan ik de Kerberos-decryptiesleutel van het `AZUREADSSOACC` computeraccount omrollen?**

Het is belangrijk om de Kerberos-decryptiesleutel van het `AZUREADSSOACC` computeraccount (dat Azure AD vertegenwoordigt) die is gemaakt in uw on-premises AD-forest, regelmatig over te rollen.

>[!IMPORTANT]
>We raden u ten zeerste aan om de Decryptiesleutel van Kerberos minstens elke 30 dagen te omrollen.

Volg de volgende stappen op de on-premises server waar u Azure AD Connect uitvoert:

   **Stap 1. Bekijk een lijst met AD-forests waar Seamless SSO is ingeschakeld**

   1. Download en installeer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de Seamless SSO PowerShell-module met deze opdracht: `Import-Module .\AzureADSSO.psd1`.
   4. Voer PowerShell uit als beheerder. Bel in `New-AzureADSSOAuthenticationContext`PowerShell. Met deze opdracht moet u een pop-up geven om de globale beheerdersreferenties van uw tenant in te voeren.
   5. Bel `Get-AzureADSSOStatus | ConvertFrom-Json`. Met deze opdracht vindt u de lijst met AD-forests (kijk naar de lijst Domeinen) waarop deze functie is ingeschakeld.

   **Stap 2. Werk de Kerberos decryptiesleutel op elk AD-forest dat het is ingesteld op**

   1. Bel `$creds = Get-Credential`. Wanneer u daarom wordt gevraagd, voert u de referenties van de domeinbeheerder in voor het beoogde AD-forest.

   > [!NOTE]
   >De gebruikersnaam van de domeinbeheerder moet worden ingevoerd in de SAM-accountnaamnotatie (contoso\johndoe of contoso.com\johndoe). We gebruiken het domeingedeelte van de gebruikersnaam om de domeincontroller van de domeinbeheerder te vinden met BEHULP van DNS.

   >[!NOTE]
   >Het gebruikte domeinbeheerdersaccount mag geen lid zijn van de groep Beveiligde gebruikers. Als dat het zo is, zal de bewerking mislukken.

   2. Bel `Update-AzureADSSOForest -OnPremCredentials $creds`. Met deze opdracht wordt de `AZUREADSSOACC` Kerberos-decryptiesleutel voor het computeraccount in dit specifieke AD-forest bijgewerkt en wordt deze bijgewerkt in Azure AD.
   3. Herhaal de voorgaande stappen voor elk AD-forest waarop u de functie hebt ingesteld.

   >[!IMPORTANT]
   >Zorg ervoor _don't_ dat u `Update-AzureADSSOForest` de opdracht niet meer dan één keer uitvoert. Anders werkt de functie niet meer totdat de Kerberos-tickets van uw gebruikers verlopen en worden ze opnieuw uitgegeven door uw on-premises Active Directory.

**V: Hoe kan ik Seamless SSO uitschakelen?**

   **Stap 1. De functie op uw tenant uitschakelen**

   **Optie A: Uitschakelen met Azure AD Connect**
    
   1. Voer Azure AD Connect uit, kies **Aanmeldingspagina voor gebruikers wijzigen** en klik op **Volgende**.
   2. Schakel de optie **Eén teken inschakelen uit.** Ga verder door de wizard.

   Na het voltooien van de wizard wordt Seamless SSO uitgeschakeld op uw tenant. U ziet echter een bericht op het scherm dat als volgt luidt:

   "Single sign-on is nu uitgeschakeld, maar er zijn extra handmatige stappen uit te voeren om de opruiming te voltooien. Meer weten?

   Als u het opruimproces wilt voltooien, voert u de stappen 2 en 3 uit op de on-premises server waar u Azure AD Connect uitvoert.

   **Optie B: PowerShell uitschakelen**

   Voer de volgende stappen uit op de on-premises server waar u Azure AD Connect uitvoert:

   1. Download en installeer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de Seamless SSO PowerShell-module met deze opdracht: `Import-Module .\AzureADSSO.psd1`.
   4. Voer PowerShell uit als beheerder. Bel in `New-AzureADSSOAuthenticationContext`PowerShell. Met deze opdracht moet u een pop-up geven om de globale beheerdersreferenties van uw tenant in te voeren.
   5. Bel `Enable-AzureADSSO -Enable $false`.

   >[!IMPORTANT]
   >Als u Naadloze SSO uitschakelt met PowerShell, wordt de status in Azure AD Connect niet gewijzigd. Naadloze SSO wordt weergegeven zoals ingeschakeld op de **aanmeldingspagina Gebruiker wijzigen.**

   **Stap 2. Bekijk een lijst met AD-forests waar Seamless SSO is ingeschakeld**

   Volg de taken 1 tot en met 4 hieronder als u Seamless SSO hebt uitgeschakeld met Azure AD Connect. Als u Seamless SSO hebt uitgeschakeld met PowerShell in plaats daarvan, gaat u vooruit naar taak 5 hieronder.

   1. Download en installeer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigeer naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importeer de Seamless SSO PowerShell-module met deze opdracht: `Import-Module .\AzureADSSO.psd1`.
   4. Voer PowerShell uit als beheerder. Bel in `New-AzureADSSOAuthenticationContext`PowerShell. Met deze opdracht moet u een pop-up geven om de globale beheerdersreferenties van uw tenant in te voeren.
   5. Bel `Get-AzureADSSOStatus | ConvertFrom-Json`. Met deze opdracht vindt u de lijst met AD-forests (kijk naar de lijst Domeinen) waarop deze functie is ingeschakeld.

   **Stap 3. Verwijder het `AZUREADSSOACCT` computeraccount handmatig uit elk AD-forest dat u ziet.**

## <a name="next-steps"></a>Volgende stappen

- [**Snelstart**](how-to-connect-sso-quick-start.md) - Ga aan de slag met Azure AD Seamless SSO.
- [**Technical Deep Dive**](how-to-connect-sso-how-it-works.md) - Begrijp hoe deze functie werkt.
- [**Problemen oplossen**](tshoot-connect-sso.md) : meer informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Voor het indienen van nieuwe functieaanvragen.
