---
title: 'Azure AD Connect: naadloze aanmelding seinen - snel aan de slag | Microsoft Documenten'
description: In dit artikel wordt beschreven hoe u aan de slag met Azure Active Directory Seamless Single Sign-On
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
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec56d37ca2c0a199968707b3d93f4797be2beca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261201"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory Seamless Single Sign-On: snel aan de slag

## <a name="deploy-seamless-single-sign-on"></a>Naadloze aanmelding implementeren

Azure Active Directory (Azure AD) Seamless Single Sign-On (Seamless SSO) meldt zich automatisch aan bij gebruikers wanneer ze zich op hun bedrijfsdesktops bevinden die zijn verbonden met uw bedrijfsnetwerk. Naadloze SSO biedt uw gebruikers eenvoudig toegang tot uw cloudgebaseerde toepassingen zonder extra on-premises componenten nodig te hebben.

Volg deze stappen om Seamless SSO te implementeren.

## <a name="step-1-check-the-prerequisites"></a>Stap 1: Controleer de voorwaarden

Zorg ervoor dat de volgende voorwaarden zijn:

* **Uw Azure AD Connect-server instellen:** als u [Pass-through Authentication](how-to-connect-pta.md) als aanmeldingsmethode gebruikt, is er geen extra vereistecontrole vereist. Als u [wachtwoordhashsynchronisatie](how-to-connect-password-hash-synchronization.md) als aanmeldingsmethode gebruikt en als er een firewall is tussen Azure AD Connect en Azure AD, moet u ervoor zorgen dat:
   - U gebruikt versie 1.1.644.0 of hoger van Azure AD Connect. 
   - Als uw firewall of proxy DNS-whitelisting toestaat, whitelistt u de verbindingen met de ** \*.msappproxy.net** URL's via poort 443. Zo niet, geef dan toegang tot de [IP-bereiken van Azure-datacenters,](https://www.microsoft.com/download/details.aspx?id=41653)die wekelijks worden bijgewerkt. Deze voorwaarde is alleen van toepassing wanneer u de functie inschakelt. Dit is niet vereist voor daadwerkelijke gebruikersaanmeldingen.

    >[!NOTE]
    >Azure AD Connect-versies 1.1.557.0, 1.1.558.0, 1.1.561.0 en 1.1.614.0 hebben een probleem met betrekking tot wachtwoordhashsynchronisatie. Als u niet van plan _bent_ om wachtwoordhashsynchronisatie te gebruiken in combinatie met Pass-through Authentication, leest u de [releasenotities van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) voor meer informatie.

* **Gebruik een ondersteunde Azure AD Connect-topologie:** zorg ervoor dat u een van de ondersteunde topologieën van Azure AD Connect gebruikt die [hier](plan-connect-topologies.md)worden beschreven.

    >[!NOTE]
    >Naadloze SSO ondersteunt meerdere AD-forests, of er nu AD-vertrouwensrelaties tussen hen zijn of niet.

* **Referenties voor domeinbeheerders instellen:** u moet domeinbeheerdersreferenties hebben voor elk Active Directory-forest die:
    * U synchroniseert met Azure AD via Azure AD Connect.
    * Bevat gebruikers die u wilt inschakelen voor Seamless SSO.
    
* **Moderne verificatie inschakelen:** u moet [moderne verificatie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) op uw tenant inschakelen om deze functie te laten werken.

* **Gebruik de nieuwste versies van Office 365-clients:** Om een stille aanmeldingservaring te krijgen met Office 365-clients (Outlook, Word, Excel en anderen), moeten uw gebruikers versies 16.0.8730.xxxx of hoger gebruiken.

## <a name="step-2-enable-the-feature"></a>Stap 2: De functie inschakelen

Naadloze SSO inschakelen via [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> U Seamless SSO ook [inschakelen met PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) als Azure AD Connect niet aan uw vereisten voldoet. Gebruik deze optie als u meer dan één domein per Active Directory-forest hebt en u meer wilt zijn gericht op het domein waarvoor u Seamless SSO wilt inschakelen.

Als u een nieuwe installatie van Azure AD Connect doet, kiest u het [aangepaste installatiepad](how-to-connect-install-custom.md). Selecteer op de **aanmeldingspagina gebruiker** de optie **Eén aanmelding inschakelen.**

>[!NOTE]
> De optie is alleen beschikbaar voor selectie als de methode Aanmelden **wachtwoordhashsynchronisatie** of **doorpasverificatie**is.

![Azure AD Connect: aanmelding voor gebruikers](./media/how-to-connect-sso-quick-start/sso8.png)

Als u al een installatie van Azure AD Connect hebt, selecteert u de **aanmeldingspagina voor gebruikers wijzigen** in Azure AD Connect en selecteert u **Volgende**. Als u Azure AD Connect-versies 1.1.880.0 of hoger gebruikt, wordt de optie **Eenmalig aantekenen inschakelen** standaard geselecteerd. Als u oudere versies van Azure AD Connect gebruikt, selecteert u de optie **Eénteken inschakelen.**

![Azure AD Connect: de aanmelding van de gebruiker wijzigen](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Ga door de wizard totdat u de **pagina Eén teken inschakelen** hebt. Geef domeinbeheerdersreferenties op voor elk Active Directory-forest dat:

* U synchroniseert met Azure AD via Azure AD Connect.
* Bevat gebruikers die u wilt inschakelen voor Seamless SSO.

Na voltooiing van de wizard is Seamless SSO ingeschakeld op uw tenant.

>[!NOTE]
> De referenties van de domeinbeheerder worden niet opgeslagen in Azure AD Connect of in Azure AD. Ze worden alleen gebruikt om de functie in te schakelen.

Volg deze instructies om te controleren of u Seamless SSO correct hebt ingeschakeld:

1. Meld u aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met de algemene beheerdersreferenties voor uw tenant.
2. Selecteer **Azure Active Directory** in het linkerdeelvenster.
3. Selecteer **Azure AD Connect**.
4. Controleer of de functie **Naadloze enkele aanmelding** wordt weergegeven als **Ingeschakeld**.

![Azure-portal: deelvenster Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Naadloze SSO maakt een `AZUREADSSOACC` computeraccount met de naam in uw on-premises Active Directory (AD) in elk AD-forest. Het `AZUREADSSOACC` computeraccount moet om veiligheidsredenen sterk worden beschermd. Alleen domeinbeheerders moeten het computeraccount kunnen beheren. Controleer of Kerberos-delegatie op het computeraccount is uitgeschakeld en dat geen enkel `AZUREADSSOACC` ander account in Active Directory delegatiemachtigingen heeft voor het computeraccount. Sla het computeraccount op in een organisatie-eenheid (OU) waar ze niet per ongeluk kunnen worden verwijderd en waar alleen domeinbeheerders toegang hebben.

>[!NOTE]
> Als u Pass-the-Hash- en Credential Theft Mitigation-architecturen gebruikt in uw on-premises omgeving, brengt u de juiste wijzigingen aan om ervoor te zorgen dat het computeraccount niet in de `AZUREADSSOACC` quarantainecontainer terechtkomt. 

## <a name="step-3-roll-out-the-feature"></a>Stap 3: De functie uitrollen

U Seamless SSO geleidelijk uitrollen naar uw gebruikers met behulp van de onderstaande instructies. U begint met het toevoegen van de volgende Azure AD-URL aan de instellingen van alle of geselecteerde instellingen voor intranetvan gebruikers met groepsbeleid in Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Daarnaast moet u via groepsbeleid een beleidsinstelling intranetzone inschakelen met de naam **Updates toestaan voor de statusbalk via het script.** 

>[!NOTE]
> De volgende instructies werken alleen voor Internet Explorer en Google Chrome op Windows (als het een set vertrouwde site-URL's deelt met Internet Explorer). Lees het volgende gedeelte voor instructies over het instellen van Mozilla Firefox en Google Chrome op macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Waarom moet u de instellingen van de intranetzone van gebruikers wijzigen?

Standaard berekent de browser automatisch de juiste zone, internet of intranet, vanuit een specifieke URL. Bijvoorbeeld `http://contoso/` kaarten aan de intranetzone, terwijl `http://intranet.contoso.com/` kaarten naar de internetzone (omdat de URL een punt bevat). Browsers sturen geen Kerberos-tickets naar een eindpunt in de cloud, zoals de Azure AD-URL, tenzij u de URL expliciet toevoegt aan de intranetzone van de browser.

Er zijn twee manieren om de instellingen van de intranetzone van gebruikers te wijzigen:

| Optie | Beoordeling van beheerders | Gebruikerservaring |
| --- | --- | --- |
| Groepsbeleid | Beheerder vergrendelt bewerking van intranetzone-instellingen | Gebruikers kunnen hun eigen instellingen niet wijzigen |
| Voorkeur voor groepsbeleid |  Beheerder maakt bewerken op intranetzone-instellingen mogelijk | Gebruikers kunnen hun eigen instellingen wijzigen |

### <a name="group-policy-option---detailed-steps"></a>Optie Groepsbeleid - Gedetailleerde stappen

1. Open de tool Groepsbeleidsbeheer.
2. Bewerk het groepsbeleid dat is toegepast op sommige of al uw gebruikers. In dit voorbeeld wordt **standaarddomeinbeleid gebruikt**.
3. Blader naar**beheersjablonen voor** >  > **gebruikersconfiguratiebeleid** > **Windows Components** > **Internet Explorer** > **Internet Configuratiescherm** > **beveiligingspagina**. **User Configuration** Selecteer vervolgens **Lijst site naar zonetoewijzing**.
    ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso6.png)
4. Schakel het beleid in en voer de volgende waarden in het dialoogvenster in:
   - **Waardenaam:** de AZURE AD-URL waar de Kerberos-tickets worden doorgestuurd.
   - **Waarde** (gegevens): **1** geeft de intranetzone aan.

     Het resultaat ziet er als volgt uit:

     Waardenaam:`https://autologon.microsoftazuread-sso.com`
  
     Waarde (gegevens): 1

   >[!NOTE]
   > Als u sommige gebruikers wilt verbieden naadloze SSO te gebruiken (bijvoorbeeld als deze gebruikers zich aanmelden bij gedeelde kiosken), stelt u de voorgaande waarden in op **4**. Met deze actie wordt de AD-URL van Azure toegevoegd aan de beperkte zone en wordt naadloze SSO altijd mislukt.
   >

5. Selecteer **OK** en vervolgens weer **OK**.

    ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso7.png)

6. Blader naar**beheersjablonen** > **Windows Components** > voor > **gebruikersconfiguratiebeleid** > Windows Components**Internet Explorer** > **Internet Explorer Internet Configuratiepagina** > **Intranet** > **Zone**. **User Configuration** Selecteer vervolgens **Updates toestaan voor de statusbalk via het script**.

    ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso11.png)

7. Schakel de beleidsinstelling in en selecteer **OK**.

    ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Optie 'Voorkeursvoorkeur groepsbeleid' - Gedetailleerde stappen

1. Open de tool Groepsbeleidsbeheer.
2. Bewerk het groepsbeleid dat is toegepast op sommige of al uw gebruikers. In dit voorbeeld wordt **standaarddomeinbeleid gebruikt**.
3. Blader > naar **gebruikersconfiguratievoorkeuren** > **Preferences** > **Windows-instellingen** > **register Nieuw** > **New****registeritem**.

    ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso15.png)

4. Voer de volgende waarden in de juiste velden in en klik op **OK**.
   - **Hoofdpad:** ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Waardenaam**: ***https***.
   - **Waardetype**: ***REG_DWORD***.
   - **Waardegegevens**: ***00000001***.
 
     ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Browseroverwegingen

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alle platforms)

Mozilla Firefox maakt niet automatisch gebruik van Kerberos-verificatie. Elke gebruiker moet de AZURE AD-URL handmatig toevoegen aan zijn Firefox-instellingen met behulp van de volgende stappen:
1. Voer Firefox `about:config` uit en voer de adresbalk in. Alle meldingen die u ziet, verwijderen.
2. Zoek naar de **voorkeur van network.negotiate-auth.trusted-uris.** Deze voorkeur geeft een overzicht van de vertrouwde sites van Firefox voor Kerberos-verificatie.
3. Klik met de rechtermuisknop en selecteer **Wijzigen**.
4. Voer `https://autologon.microsoftazuread-sso.com` in het veld.
5. Selecteer **OK** en open de browser opnieuw.

#### <a name="safari-macos"></a>Safari (macOS)

Controleer of de machine waarop macOS wordt uitgevoerd, is verbonden met AD. Instructies voor het samenvoegen van AD-apparaten op je macOS-apparaat vallen buiten het bereik van dit artikel.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge op basis van Chromium (alle platforms)

Als u de [AuthNegotiateDelegateAllowlist-](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) of de beleidsinstellingen [van AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) in uw omgeving hebt`https://autologon.microsoftazuread-sso.com`overschreven, moet u ervoor zorgen dat u ook de URL van Azure AD ( ) aan deze instellingen toevoegt.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge op basis van Chromium (macOS en andere niet-Windows-platforms)

Raadpleeg voor Microsoft Edge op basis van Chromium op Mac OS en andere niet-Windows-platforms [de Microsoft Edge op basis van Chromium Policy List](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) voor informatie over het toevoegen van de AZURE AD URL voor geïntegreerde verificatie aan uw lijst met toegestane producten.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (alle platforms)

Als u de [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) of de [beleidsinstellingen van AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) in uw omgeving hebt`https://autologon.microsoftazuread-sso.com`overschreven, moet u ervoor zorgen dat u ook de URL van Azure AD ( ) aan deze url () toevoegt.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS en andere niet-Windows-platforms)

Raadpleeg voor Google Chrome op Mac OS en andere niet-Windows-platforms [de lijst met beleid voor chromium-projecten](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) voor informatie over het whitelisten van de AD-URL van Azure voor geïntegreerde verificatie.

Het gebruik van Active Directory Group Policy-extensies van derden om de Azure AD-URL uit te rollen naar Firefox- en Google Chrome-gebruikers van Mac valt buiten het bereik van dit artikel.

#### <a name="known-browser-limitations"></a>Bekende browserbeperkingen

Naadloze SSO werkt niet in de browsermodus voor privébrowsen in Firefox- en Microsoft Edge-browsers. Het werkt ook niet op Internet Explorer als de browser wordt uitgevoerd in de modus Verbeterd beveiligd. Voor de volgende versie van Microsoft Edge op basis van Chromium, zal het niet werken in InPrivate en Guest mode by design.

## <a name="step-4-test-the-feature"></a>Stap 4: Test de functie

Als u de functie voor een specifieke gebruiker wilt testen, moet u ervoor zorgen dat alle volgende voorwaarden aanwezig zijn:
  - De gebruiker meldt zich aan op een bedrijfsapparaat.
  - Het apparaat is verbonden met uw Active Directory-domein. Het apparaat _hoeft niet_ Azure AD Te zijn [aangesloten.](../active-directory-azureadjoin-overview.md)
  - Het apparaat heeft een directe verbinding met uw domeincontroller (DC), op het bekabelde of draadloze netwerk van bedrijven of via een verbinding met externe toegang, zoals een VPN-verbinding.
  - U hebt de functie via Groepsbeleid naar deze gebruiker [uitgerold.](#step-3-roll-out-the-feature)

Ga als het gaat om het scenario waarin de gebruiker alleen de gebruikersnaam invoert, maar niet het wachtwoord:
   - Meld u `https://myapps.microsoft.com/` aan bij een nieuwe browsersessie voor privégesprekken.

Als u het scenario wilt testen waarin de gebruiker de gebruikersnaam of het wachtwoord niet hoeft in te voeren, gebruikt u een van de volgende stappen: 
   - Meld u `https://myapps.microsoft.com/contoso.onmicrosoft.com` aan bij een nieuwe browsersessie voor privégesprekken. Vervang *contoso* door de naam van uw huurder.
   - Meld u `https://myapps.microsoft.com/contoso.com` aan bij een nieuwe browsersessie voor privégesprekken. Vervang *contoso.com* door een geverifieerd domein (geen federatief domein) op uw tenant.

## <a name="step-5-roll-over-keys"></a>Stap 5: Sleutels omrollen

In stap 2 maakt Azure AD Connect computeraccounts (die Azure AD vertegenwoordigen) in alle Active Directory-forests waarop u Seamless SSO hebt ingeschakeld. Zie [Azure Active Directory Seamless Single Sign-On: Technical deep dive voor](how-to-connect-sso-how-it-works.md)meer informatie.

>[!IMPORTANT]
>De Kerberos decryptiesleutel op een computeraccount, indien gelekt, kan worden gebruikt om Kerberos-tickets te genereren voor elke gebruiker in het AD-forest. Kwaadwillende actoren kunnen zich vervolgens voordoen als Azure AD-aanmeldingen voor gecompromitteerde gebruikers. We raden u ten zeerste aan deze Kerberos decryptiesleutels regelmatig om te draaien - ten minste eenmaal per 30 dagen.

Zie Azure Active Directory Seamless [Single Sign-On: Veelgestelde vragen voor](how-to-connect-sso-faq.md)instructies voor het overzetten van sleutels. We werken aan een mogelijkheid om geautomatiseerde roll over van sleutels te introduceren.

>[!IMPORTANT]
>U hoeft deze stap niet onmiddellijk te doen _nadat_ u de functie hebt ingeschakeld. Rol over de Kerberos decryptie sleutels ten minste eenmaal per 30 dagen.

## <a name="next-steps"></a>Volgende stappen

- [Technische diepe duik:](how-to-connect-sso-how-it-works.md)Begrijp hoe de Seamless Single Sign-On-functie werkt.
- [Veelgestelde vragen](how-to-connect-sso-faq.md): Krijg antwoorden op veelgestelde vragen over Naadloze Single Sign-On.
- [Problemen oplossen:](tshoot-connect-sso.md)meer informatie over het oplossen van veelvoorkomende problemen met de functie Naadloos eenmalig aanmelden.
- [UserVoice:](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)gebruik het Azure Active Directory Forum om nieuwe functieaanvragen in te dienen.
