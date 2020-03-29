---
title: 'Azure Active Directory Connect: problemen oplossen met naadloze aanmelding | Microsoft Documenten'
description: In dit onderwerp wordt beschreven hoe u problemen oplossen met Azure Active Directory Seamless Single Sign-On
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759748124893a8f906a4bc336f835546202b0b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049498"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Problemen oplossen azure Active Directory Seamless Single Sign-On

Met dit artikel u informatie vinden over probleemoplossingsproblemen met betrekking tot Azure Active Directory (Azure AD) Seamless Single Sign-On (Seamless SSO).

## <a name="known-issues"></a>Bekende problemen

- In een paar gevallen kan het inschakelen van Seamless SSO tot 30 minuten duren.
- Als u Seamless SSO op uw tenant uitschakelt en opnieuw inschakelt, krijgen gebruikers de enkele aanmeldingservaring pas als hun Kerberos-tickets in de cache, die doorgaans 10 uur geldig zijn, zijn verlopen.
- Als Seamless SSO slaagt, heeft de gebruiker niet de mogelijkheid om **Keep me signed in**te selecteren. Vanwege dit gedrag werken [SharePoint- en OneDrive-toewijzingsscenario's](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) niet.
- Office 365 Win32-clients (Outlook, Word, Excel en anderen) met versies 16.0.8730.xxxx en hoger worden ondersteund met behulp van een niet-interactieve stroom. Andere versies worden niet ondersteund; op deze versies, gebruikers zullen hun gebruikersnamen, maar niet wachtwoorden, aan te melden. Voor OneDrive moet u de [OneDrive-functie voor stille config](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) activeren voor een stille aanmeldingservaring.
- Naadloze SSO werkt niet in de privébrowsermodus in Firefox.
- Naadloze SSO werkt niet in Internet Explorer wanneer de modus Verbeterd beveiligd is ingeschakeld.
- Naadloze SSO werkt niet op mobiele browsers op iOS en Android.
- Als een gebruiker deel uitmaakt van te veel groepen in Active Directory, is het Kerberos-ticket van de gebruiker waarschijnlijk te groot om te verwerken en zal dit ervoor zorgen dat Naadloze SSO mislukt. Azure AD HTTPS-aanvragen kunnen kopteksten hebben met een maximale grootte van 50 KB; Kerberos-tickets moeten kleiner zijn dan die limiet voor andere Azure AD-artefacten (meestal 2 - 5 KB) zoals cookies. Onze aanbeveling is om het groepslidmaatschap van de gebruiker te verminderen en het opnieuw te proberen.
- Als u 30 of meer Active Directory-forests synchroniseert, u Seamless SSO niet inschakelen via Azure AD Connect. Als tijdelijke oplossing u de functie [handmatig inschakelen](#manual-reset-of-the-feature) op uw tenant.
- Als u de URL`https://autologon.microsoftazuread-sso.com`van de Azure AD-service () toevoegt aan de zone Vertrouwde sites in plaats van de zone Lokaal *intranet, kunnen gebruikers zich niet aanmelden.*
- Seamless SSO ondersteunt de AES256_HMAC_SHA1, AES128_HMAC_SHA1 en RC4_HMAC_MD5 encryptietypen voor Kerberos. Het wordt aanbevolen dat het versleutelingstype voor het AzureADSSOAcc$-account is ingesteld op AES256_HMAC_SHA1 of een van de AES-typen vs. RC4 voor extra beveiliging. Het versleutelingstype wordt opgeslagen in het kenmerk msDS-SupportedEncryptionTypes van het account in uw Active Directory.  Als het AzureADSSOAcc$-accountversleutelingstype is ingesteld op RC4_HMAC_MD5 en u deze wilt wijzigen in een van de AES-versleutelingstypen, moet u ervoor zorgen dat u eerst de Kerberos-decryptiesleutel van het AzureADSSOAcc$-account overrolt, zoals uitgelegd in het [FAQ-document](how-to-connect-sso-faq.md) onder de relevante vraag, anders gebeurt Naadloze SSO niet.

## <a name="check-status-of-feature"></a>Status van functie controleren

Zorg ervoor dat de Seamless SSO-functie nog steeds **is ingeschakeld** op uw tenant. U de status controleren door naar het deelvenster **Azure AD Connect** in het Azure Active [Directory-beheercentrum](https://aad.portal.azure.com/)te gaan.

![Azure Active Directory-beheercentrum: deelvenster Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Klik door om alle AD-forests te zien die zijn ingeschakeld voor Seamless SSO.

![Azure Active Directory-beheercentrum: naadloos SSO-deelvenster](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Redenen voor aanmeldingsfout in het Azure Active Directory-beheercentrum (heeft een Premium-licentie nodig)

Als aan uw tenant een Azure AD Premium-licentie is gekoppeld, u ook het [aanmeldingsactiviteitsrapport](../reports-monitoring/concept-sign-ins.md) bekijken in het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com/).

![Azure Active Directory-beheercentrum: rapport Aanmeldingen](./media/tshoot-connect-sso/sso9.png)

Blader naar Azure Active > **Directory-aanmeldingen** in het Azure Active [Directory-beheercentrum](https://aad.portal.azure.com/)en selecteer vervolgens de aanmeldingsactiviteit van een specifieke gebruiker. **Azure Active Directory** Zoek naar het veld **AANMELDINGsFOUTCODE.** Breng de waarde van dat veld in kaart met de volgende tabel:

|Aanmeldingsfoutcode|Reden voor aanmeldingsfout|Oplossing
| --- | --- | ---
| 81001 | Kerberos-ticket van de gebruiker is te groot. | Reduceer het aantal groepslidmaatschappen van de gebruiker en probeer het opnieuw.
| 81002 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Zie de [checklist voor probleemoplossing.](#troubleshooting-checklist)
| 81003 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Zie de [checklist voor probleemoplossing.](#troubleshooting-checklist)
| 81004 | Poging tot Kerberos-verificatie is mislukt. | Zie de [checklist voor probleemoplossing.](#troubleshooting-checklist)
| 81008 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Zie de [checklist voor probleemoplossing.](#troubleshooting-checklist)
| 81009 | Kan het Kerberos-ticket van de gebruiker niet valideren. | Zie de [checklist voor probleemoplossing.](#troubleshooting-checklist)
| 81010 | Naadloze eenmalige aanmelding is mislukt omdat het Kerberos-ticket van de gebruiker is verlopen of ongeldig is. | De gebruiker moet zich aanmelden vanaf een apparaat dat is verbonden aan het domein binnen uw bedrijfsnetwerk.
| 81011 | Kan het object van de gebruiker niet vinden op basis van de informatie in het Kerberos-ticket van de gebruiker. | Gebruik Azure AD Connect om de gegevens van de gebruiker te synchroniseren met Azure AD.
| 81012 | De gebruiker die zich probeert aan te melden bij Azure AD is anders dan de gebruiker die is aangemeld bij het apparaat. | De gebruiker moet zich aanmelden vanaf een ander apparaat.
| 81013 | Kan het object van de gebruiker niet vinden op basis van de informatie in het Kerberos-ticket van de gebruiker. |Gebruik Azure AD Connect om de gegevens van de gebruiker te synchroniseren met Azure AD. 

## <a name="troubleshooting-checklist"></a>Controlelijst voor probleemoplossing

Gebruik de volgende checklist om naadloze SSO-problemen op te lossen:

- Controleer of de seamless sso-functie is ingeschakeld in Azure AD Connect. Als u de functie niet inschakelen (bijvoorbeeld vanwege een geblokkeerde poort), moet u ervoor zorgen dat u over alle [vereisten](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) beschikt.
- Als u zowel [Azure AD Join](../active-directory-azureadjoin-overview.md) als Seamless SSO op uw tenant hebt ingeschakeld, moet u ervoor zorgen dat het probleem niet bij Azure AD Join is. SSO van Azure AD Join heeft voorrang op Seamless SSO als het apparaat zowel is geregistreerd bij Azure AD als met een domein is verbonden. Met SSO van Azure AD Join ziet de gebruiker een aanmeldingstegel met de tekst 'Verbonden met Windows'.
- Controleer of de Azure`https://autologon.microsoftazuread-sso.com`AD URL ( ) deel uitmaakt van de instellingen van de intranetzone van de gebruiker.
- Controleer of het bedrijfsapparaat is verbonden met het Active Directory-domein. Het apparaat _hoeft niet_ [Azure AD](../active-directory-azureadjoin-overview.md) te zijn aangesloten om Seamless SSO te laten werken.
- Controleer of de gebruiker is aangemeld bij het apparaat via een Active Directory-domeinaccount.
- Controleer of het account van de gebruiker afkomstig is uit een Active Directory-forest waar Seamless SSO is ingesteld.
- Controleer of het apparaat is verbonden met het bedrijfsnetwerk.
- Zorg ervoor dat de tijd van het apparaat wordt gesynchroniseerd met de tijd in zowel Active Directory als de domeincontrollers en dat ze zich binnen vijf minuten van elkaar bevinden.
- Controleer of `AZUREADSSOACC` het computeraccount aanwezig en ingeschakeld is in elk AD-forest waarvan u wilt dat Seamless SSO is ingeschakeld. Als het computeraccount is verwijderd of ontbreekt, u [PowerShell-cmdlets](#manual-reset-of-the-feature) gebruiken om ze opnieuw te maken.
- Vermeld de bestaande Kerberos-tickets op `klist` het apparaat met behulp van de opdracht uit een opdrachtprompt. Zorg ervoor dat de `AZUREADSSOACC` tickets die voor het computeraccount zijn uitgegeven, aanwezig zijn. De Kerberos-tickets van gebruikers zijn doorgaans 10 uur geldig. Mogelijk hebt u verschillende instellingen in Active Directory.
- Als u Seamless SSO op uw tenant hebt uitgeschakeld en opnieuw hebt ingeschakeld, krijgen gebruikers de enkele aanmeldingservaring pas als hun Kerberos-tickets in de cache zijn verlopen.
- Verwijder bestaande Kerberos-tickets van `klist purge` het apparaat met behulp van de opdracht en probeer het opnieuw.
- Als u wilt bepalen of er JavaScript-gerelateerde problemen zijn, controleert u de consolelogboeken van de browser (onder **Hulpprogramma's voor ontwikkelaars).**
- Controleer de [logboeken van](#domain-controller-logs)de domeincontroller .

### <a name="domain-controller-logs"></a>Logboeken van domeincontroller

Als u succescontrole op uw domeincontroller inschakelt, wordt elke keer dat een gebruiker zich aanmeldt via Seamless SSO, een beveiligingsitem geregistreerd in het gebeurtenislogboek. U deze beveiligingsgebeurtenissen vinden met behulp van de volgende query. (Zoek naar gebeurtenis **4769** die is gekoppeld aan het computeraccount **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Handmatig opnieuw instellen van de functie

Als het oplossen van problemen niet heeft geholpen, u de functie handmatig opnieuw instellen op uw tenant. Volg deze stappen op de on-premises server waar u Azure AD Connect uitvoert.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Stap 1: De Seamless SSO PowerShell-module importeren

1. Download en installeer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Blader naar de map `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importeer de Seamless SSO PowerShell-module `Import-Module .\AzureADSSO.psd1`met deze opdracht: .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Stap 2: Download de lijst met Active Directory-forests waarop Seamless SSO is ingeschakeld

1. Voer PowerShell uit als beheerder. Bel in `New-AzureADSSOAuthenticationContext`PowerShell. Voer de algemene beheerdersreferenties van uw tenant in wanneer u daarom wordt gevraagd.
2. Bel `Get-AzureADSSOStatus`. Deze opdracht biedt u de lijst met Active Directory-forests (kijk naar de lijst Domeinen) waarop deze functie is ingeschakeld.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Stap 3: Naadloze SSO uitschakelen voor elk Active Directory-forest waar u de functie hebt ingesteld

1. Bel `$creds = Get-Credential`. Wanneer u daarom wordt gevraagd, voert u de referenties van de domeinbeheerder in voor het beoogde Active Directory-forest.

   > [!NOTE]
   >De gebruikersnaam van de domeinbeheerder moet worden ingevoerd in de SAM-accountnaamnotatie (contoso\johndoe of contoso.com\johndoe). We gebruiken het domeingedeelte van de gebruikersnaam om de domeincontroller van de domeinbeheerder te vinden met BEHULP van DNS.

   >[!NOTE]
   >Het gebruikte domeinbeheerdersaccount mag geen lid zijn van de groep Beveiligde gebruikers. Als dat het zo is, zal de bewerking mislukken.

2. Bel `Disable-AzureADSSOForest -OnPremCredentials $creds`. Met deze `AZUREADSSOACC` opdracht wordt het computeraccount verwijderd van de on-premises domeincontroller voor dit specifieke Active Directory-forest.
3. Herhaal de voorgaande stappen voor elk Active Directory-forest waarin u de functie hebt ingesteld.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Stap 4: Naadloze SSO inschakelen voor elk Active Directory-forest

1. Bel `Enable-AzureADSSOForest`. Wanneer u daarom wordt gevraagd, voert u de referenties van de domeinbeheerder in voor het beoogde Active Directory-forest.

   > [!NOTE]
   >De gebruikersnaam van de domeinbeheerder moet worden ingevoerd in de SAM-accountnaamnotatie (contoso\johndoe of contoso.com\johndoe). We gebruiken het domeingedeelte van de gebruikersnaam om de domeincontroller van de domeinbeheerder te vinden met BEHULP van DNS.

   >[!NOTE]
   >Het gebruikte domeinbeheerdersaccount mag geen lid zijn van de groep Beveiligde gebruikers. Als dat het zo is, zal de bewerking mislukken.

2. Herhaal de vorige stap voor elk Active Directory-forest waarin u de functie wilt instellen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Stap 5. De functie op uw tenant inschakelen

Als u de functie op `Enable-AzureADSSO -Enable $true`uw tenant wilt inschakelen, belt u.
