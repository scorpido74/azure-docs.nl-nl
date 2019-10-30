---
title: 'Azure AD Connect: implementatie van Cloud-authenticatie-gefaseerd | Microsoft Docs'
description: Hierin wordt uitgelegd hoe u migreert van Federated Authentication naar Cloud auth met behulp van een gefaseerde implementatie.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/28/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8f4250d2df7703ad2960c2d68cd015cb6ec2bc
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052876"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Cloud authenticatie: gefaseerde implementatie (open bare preview)

Met deze functie kunt u migreren van Federated Authentication naar Cloud verificatie met behulp van een gefaseerde benadering.

Het weggooien van Federated Authentication heeft gevolgen voor de implicaties. Als u bijvoorbeeld een van de volgende hebt:
    
-  een on-premises MFA-server 
-  Smart Cards gebruiken voor verificatie 
-  andere alleen-Federatie functies

Deze functies moeten in aanmerking worden genomen voordat wordt overgeschakeld naar Cloud authenticatie.  Voordat u deze functie uitvoert, raden we u aan onze hand leiding te bekijken om de juiste verificatie methode te kiezen. Zie [deze tabel](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) voor meer informatie.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Vereisten

-   U hebt een Azure AD-Tenant met federatieve domeinen.

-   U hebt besloten om over te stappen op wacht woord hash Sync + naadloze SSO **(optie a)** of Pass-Through-verificatie + naadloze SSO **(optie B).** Hoewel naadloze SSO optioneel is, is het raadzaam naadloze SSO in te scha kelen voor gebruikers die gebruikmaken van computers die lid zijn van een domein in het bedrijfs netwerk.

-   U hebt alle juiste Tenant huismerk en beleids regels voor voorwaardelijke toegang geconfigureerd die u nodig hebt voor gebruikers die worden gemigreerd naar Cloud authenticatie.

-   Als u van plan bent Azure Multi-Factor Authentication te gebruiken, raden we u aan om [geconvergeerde registratie voor selfservice voor wachtwoord herstel (SSPR) en Azure MFA](../authentication/concept-registration-mfa-sspr-combined.md) te gebruiken om uw gebruikers één keer te doen hun verificatie methoden te registreren.

-   Als u deze functie wilt gebruiken, moet u globale beheerder zijn voor uw Tenant.

-   Als u naadloze SSO wilt inschakelen voor een specifiek AD-forest, moet u een domein beheerder zijn.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Deze scenario's worden ondersteund voor gefaseerde implementatie:

- Deze functie werkt alleen voor gebruikers die zijn ingericht voor Azure AD met behulp van Azure AD Connect en is niet van toepassing op Cloud gebruikers.

- Deze functie werkt alleen voor gebruikers die zich aanmelden bij browsers en moderne verificatie-clients. Toepassingen of Cloud Services die gebruikmaken van verouderde verificatie, vallen terug op Federated Authentication stromen. (Bijvoorbeeld: Exchange Online met moderne verificatie uitgeschakeld of Outlook 2010, die geen ondersteuning biedt voor moderne verificatie.)

## <a name="unsupported-scenarios"></a>Niet-ondersteunde Scenario's

Deze scenario's worden niet ondersteund voor gefaseerde implementatie:

- Bepaalde toepassingen verzenden tijdens de verificatie de query parameter ' Domain\_Hint ' naar Azure AD. Deze stromen blijven door en gebruikers die zijn ingeschakeld voor gefaseerde implementatie, blijven Federatie voor verificatie gebruiken.

<!-- -->

- De beheerder kan Cloud verificatie implementeren met behulp van beveiligings groepen. (Cloud beveiligings groepen worden aanbevolen om synchronisatie latentie te voor komen wanneer u on-premises AD-beveiligings groepen gebruikt.)

    - U kunt **Maxi maal 10 groepen per functie**gebruiken. dat wil zeggen voor elk wacht woord hash Sync/Pass-Through-verificatie/naadloze SSO.

    - Geneste groepen worden **niet ondersteund**. Dit is ook het bereik voor open bare preview-versie.

    - Dynamische groepen worden **niet ondersteund** voor gefaseerde implementatie.

    - Met de objecten in de groep wordt het groeps formulier geblokkeerd.

- De uiteindelijke cutover van federatieve naar Cloud authenticatie moet nog steeds plaatsvinden met behulp van Azure AD Connect of Power shell. Met deze functie wordt niet overgeschakeld naar een ander domein dan de domeinen.

- Wanneer u een beveiligings groep voor het eerst toevoegt voor de gefaseerde implementatie, is het beperkt tot 200 gebruikers om te voor komen dat de UX van time-out optreedt. Zodra de groep in de UX is toegevoegd, kunt u indien nodig meer gebruikers rechtstreeks aan de groep toevoegen.

## <a name="get-started-with-staged-rollout"></a>Aan de slag met gefaseerde implementatie

Als u aanmelding met een wacht woord-hash Sync (PHS) wilt testen met behulp van gefaseerde implementatie, voert u de onderstaande stappen uit om de gefaseerde implementatie van wacht woord-hash-synchronisatie in te scha kelen.

Zie [AzureAD 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout) (Engelstalig) voor meer informatie over de gebruikte Power shell-cmdlets.

## <a name="pre-work-for-password-hash-sync"></a>Vooraf werken voor wachtwoord-hash-synchronisatie

1. Schakel wachtwoord-hash-synchronisatie in op de pagina [optionele functies](how-to-connect-install-custom.md#optional-features) in azure AD Connect. 

   ![Scherm afbeelding van de pagina optionele functies in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Zorg ervoor dat een volledige hash-synchronisatie cyclus voor wacht woorden is uitgevoerd, zodat de wachtwoord hashes van de gebruikers zijn gesynchroniseerd met Azure AD. U kunt de diagnostische gegevens van Power shell [hier](tshoot-connect-password-hash-synchronization.md) gebruiken om de status van de synchronisatie van wacht woord-hashes te controleren.

   ![Scherm opname van het AADConnect-probleemoplossings logboek](./media/how-to-connect-staged-rollout/sr2.png)

Als u de aanmelding Pass-Through-verificatie (PTA) wilt testen met behulp van gefaseerde implementatie, voert u de onderstaande stappen uit om PTA in te scha kelen voor gefaseerde implementatie.

## <a name="pre-work-for-pass-through-authentication"></a>Vooraf-werk voor Pass-Through-verificatie

1. Identificeer een server met Windows Server 2012 R2 of hoger waar u de Pass Through-verificatie agent wilt uitvoeren (**Kies niet de Azure AD Connect-server**). Zorg ervoor dat de server lid is van een domein, geselecteerde gebruikers kan verifiëren met Active Directory en kan communiceren met Azure AD via uitgaande poorten/Url's (Zie gedetailleerde [vereisten](how-to-connect-sso-quick-start.md)).

1. [Down load](https://aka.ms/getauthagent) & de Microsoft Azure AD Connect authentication-agent op de-server te installeren. 

1. Als u [maximale Beschik baarheid](how-to-connect-sso-quick-start.md)wilt inschakelen, installeert u extra verificatie agenten op andere servers.

1. Zorg ervoor dat u de [instellingen voor slim vergren delen](../authentication/howto-password-smart-lockout.md) op de juiste manier hebt geconfigureerd. Dit is om ervoor te zorgen dat de on-premises Active Directory accounts van uw gebruikers niet worden vergrendeld door ongeldige actors.

We raden u aan naadloze SSO in te scha kelen, onafhankelijk van de aanmeldings methode (PHS of PTA) die u hebt geselecteerd voor gefaseerde implementatie. Voer de onderstaande stappen uit om naadloze SSO in te scha kelen voor gefaseerde implementatie.

## <a name="pre-work-for-seamless-sso"></a>Pre-work voor naadloze SSO

Schakel naadloze SSO in voor de AD-forests met behulp van Power shell. Als u meer dan één AD-forest hebt, moet u hetzelfde voor elk forest afzonderlijk inschakelen. Naadloze SSO wordt alleen geactiveerd voor gebruikers die zijn geselecteerd voor gefaseerde implementatie en die geen invloed hebben op uw bestaande federatie-installatie.

**Samen vatting van de stappen**

1. Meld u eerst aan bij Azure AD Connect server.

2. Navigeer naar de map% Program Files%\\Microsoft Azure Active Directory Connect.

3. Importeer de naadloze SSO Power shell-module met behulp van deze opdracht: `Import-Module .\\AzureADSSO.psd1`.

4. Voer Power shell uit als beheerder. Roep `New-AzureADSSOAuthenticationContext`aan in Power shell. Met deze opdracht geeft u een dialoog venster weer waarin u de referenties van de globale beheerder van uw Tenant kunt invoeren.

5.  `Get-AzureADSSOStatus \| ConvertFrom-Json`aanroepen. Met deze opdracht geeft u de lijst met AD-forests weer (Bekijk de \"domeinen\" lijst) waarop deze functie is ingeschakeld. Standaard is deze ingesteld op False op Tenant niveau.

   > **Voor beeld:** 
   > ![voor beeld van de Windows Power shell-uitvoer](./media/how-to-connect-staged-rollout/sr3.png)

6.  `\$creds = Get-Credential`aanroepen. Wanneer u hierom wordt gevraagd, voert u de referenties voor de domein beheerder in voor het beoogde AD-forest.

7. `Enable-AzureADSSOForest -OnPremCredentials \$creds`aanroepen. Met deze opdracht maakt u het AZUREADSSOACC-computer account van de on-premises domein controller voor dit specifieke Active Directory-forest dat is vereist voor naadloze SSO.

8. Voor naadloze SSO moeten Url's zich in de intranet zone bevindt. Raadpleeg de Snelstartgids voor [naadloze eenmalige aanmelding](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) om deze url's te implementeren met behulp van groeps beleid.

9.  U kunt ook onze [implementatie plannen](https://aka.ms/SeamlessSSODPDownload) voor naadloze SSO downloaden voor een volledige beschrijving.

## <a name="enable-staged-rollout"></a>Gefaseerde implementatie inschakelen

Voer de volgende stappen uit om een specifieke functie (Pass-Through-verificatie/wacht woord-hash Sync/naadloze SSO) uit te voeren voor een geselecteerde set gebruikers in een groep:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>De gefaseerde implementatie van een specifieke functie in uw Tenant inschakelen

U kunt een van deze opties implementeren

-   Wachtwoord hash Sync + naadloze SSO **(optie a)**

-   Pass-Through-verificatie + naadloze SSO **(optie B)**

-   Wachtwoord hash-synchronisatie + Pass Through-verificatie + naadloze SSO- **-\>** ***niet ondersteund***

Voer de volgende stappen uit:

1. Meld u aan bij de Azure AD-Portal met behulp van de onderstaande URL voor toegang tot de preview-versie.

   > <https://aka.ms/stagedrolloutux>

2. Klik op gefaseerde implementatie voor beheerde gebruikers aanmelding inschakelen (preview)

   *Bijvoorbeeld:* (**optie B**) als u wachtwoord hash-synchronisatie en naadloze SSO wilt inschakelen, moet u de wacht woord-hash-synchronisatie en naadloze eenmalige aanmeldingen gebruiken **voor aan, zoals hieronder** wordt weer gegeven.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Voeg de betreffende groepen toe aan de functie om Pass Through-verificatie en naadloze eenmalige aanmelding in te scha kelen. Zorg ervoor dat de beveiligings groepen in eerste instantie niet meer dan 200 leden hebben om de time-out van UX te voor komen.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >De leden in een groep worden automatisch ingeschakeld voor gefaseerde implementatie. Geneste en dynamische groepen worden niet ondersteund voor gefaseerde implementatie.

## <a name="auditing"></a>Controle

We hebben controle gebeurtenissen ingeschakeld voor de verschillende acties die we uitvoeren voor de gefaseerde implementatie.

- Controle gebeurtenis bij het inschakelen van gefaseerde implementatie voor wachtwoord hash Sync/Pass-Through-verificatie/naadloze SSO.

  >[!NOTE]
  >Controle gebeurtenis die wordt geregistreerd wanneer SeamlessSSO wordt ingeschakeld **met StagedRollout** .

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Controle gebeurtenis wanneer een groep wordt toegevoegd aan een wachtwoord hash Sync/Pass-Through-verificatie/naadloze SSO.

  >[!NOTE]
  >Controle gebeurtenis vastgelegd wanneer een groep wordt toegevoegd aan de wachtwoord-hash-synchronisatie voor gefaseerde implementatie

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Controle gebeurtenis wanneer een gebruiker die is toegevoegd aan de groep is ingeschakeld voor gefaseerde implementatie

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validatie

Aanmelden met hash-synchronisatie of Pass-Through-verificatie (gebruikers naam/wacht woord aanmelden) testen:

1. Blader naar <https://myapps.microsoft.com> in een persoonlijke browser sessie van extranet en voer de UserPrincipalName (UPN) in van het gebruikers account dat is geselecteerd voor gefaseerde implementatie.

1. Als de gebruiker is bedoeld voor gefaseerde implementatie, wordt de gebruiker niet omgeleid naar uw federatieve aanmeldings pagina en wordt u gevraagd zich aan te melden op de aanmeldings pagina van de Azure AD-Tenant.

1. Zorg ervoor dat de aanmelding correct wordt weer gegeven in het rapport voor de [registratie van Azure AD-activiteiten](../reports-monitoring/concept-sign-ins.md) door te filteren met de userPrincipalName..

Aanmelden met naadloze SSO testen:

1. Blader naar <https://myapps.microsoft.com>/op een persoonlijke browser sessie van intranet en voer de UserPrincipalName (UPN) in van het gebruikers account dat is geselecteerd voor gefaseerde implementatie.

1. Als de gebruiker is aangewezen voor de gefaseerde implementatie van naadloze SSO, ziet de gebruiker een ' poging om u aan te melden... ' bericht voordat u zich op de achtergrond aanmeldt.

1. Zorg ervoor dat de aanmelding correct wordt weer gegeven in het [rapport met aanmeld activiteiten van Azure AD](../reports-monitoring/concept-sign-ins.md) door te filteren met de userPrincipalName.

De gebruikers aanmeldingen controleren die zich nog steeds voordoen bij Federatie providers:

Hier vindt u informatie over het bijhouden van gebruikers aanmeldingen die zich nog steeds voordoen op AD FS voor geselecteerde gebruikers met een gefaseerde implementatie die [deze instructies](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)gebruiken. Raadpleeg de documentatie van de leverancier voor informatie over hoe u dit kunt controleren op Federation-providers van derden.

## <a name="roll-back"></a>Terugdraaien

### <a name="remove-a-user-from-staged-rollout"></a>Een gebruiker verwijderen uit de gefaseerde implementatie

1.  Als u de gebruiker uit de groep verwijdert, wordt de gefaseerde implementatie voor de gebruiker uitgeschakeld.

2.  Als u de functie voor gefaseerde implementatie wilt uitschakelen, schuift u de functie terug naar **uit** om de gefaseerde implementatie uit te scha kelen.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

-   **V: kan een klant deze mogelijkheid gebruiken in productie?**

-   A: Ja, deze functie kan worden gebruikt in uw productie Tenant, maar we raden u aan deze mogelijkheid voor het eerst uit te proberen in uw test Tenant.

-   **V: kan deze functie worden gebruikt voor het behouden van een permanente ' samen werking ' waarbij sommige gebruikers Federated Authentication gebruiken en andere Cloud authenticatie?**

-   A: Nee, deze functie is ontworpen voor de migratie van federatieve naar Cloud authenticatie in fasen en uiteindelijk naar Cloud authenticatie. Het wordt niet aangeraden een permanente gemengde status te maken, omdat dit kan leiden tot onverwachte verificatie stromen.

-   **V: kunnen we Power shell gebruiken voor het uitvoeren van een gefaseerde implementatie?**

-   A: Ja, ga naar de documentatie om Power shell te gebruiken voor het uitvoeren van de gefaseerde implementatie.

## <a name="next-steps"></a>Volgende stappen
- [AzureAD 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
