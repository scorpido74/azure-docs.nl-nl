---
title: 'Azure AD Connect: Cloudverificatie via gefaseerde implementatie | Microsoft Documenten'
description: In dit artikel wordt uitgelegd hoe u migreren van federatieve verificatie naar cloudverificatie met behulp van een gefaseerde implementatie.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915234"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Migreren naar cloudverificatie met behulp van gefaseerde implementatie (voorbeeld)

Met behulp van een gefaseerde implementatiebenadering u migreren van federatieve verificatie naar cloudverificatie. In dit artikel wordt besproken hoe u de overstap maken. Voordat u de gefaseerde implementatie begint, moet u echter rekening houden met de implicaties als een of meer van de volgende voorwaarden waar zijn:
    
-  U gebruikt momenteel een on-premises multifactorverificatieserver. 
-  U gebruikt smartcards voor verificatie. 
-  Uw huidige server biedt bepaalde functies die alleen voor de federatie zijn.

Voordat u deze functie probeert, raden we u aan onze handleiding te bekijken bij het kiezen van de juiste verificatiemethode. Zie de tabel Methoden vergelijken voor meer informatie in [Kies de juiste verificatiemethode voor uw azure Active Directory hybride identiteitsoplossing.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)

Voor een overzicht van de functie u deze Azure Active Directory: Wat is gefaseerde implementatie? bekijken. Video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Vereisten

-   U hebt een Azure Active Directory-tenant (Azure AD) met federatieve domeinen.

-   U hebt besloten om over te stappen op een van de twee opties:
    - **Optie Een** - *wachtwoordhashsynchronisatie (synchronisatie)* + *naadloze single sign-on (SSO)*
    - **Optie B** - *pass-through authenticatie* + *naadloze SSO*
    
    Hoewel *naadloze SSO* optioneel is, raden we u aan om een stille aanmeldingservaring te bereiken voor gebruikers die met domeinverbonden machines uitvoeren vanuit een bedrijfsnetwerk.

-   U hebt alle juiste tenant-branding en voorwaardelijke toegangsbeleidsregels geconfigureerd die u nodig hebt voor gebruikers die worden gemigreerd naar cloudverificatie.

-   Als u Azure Multi-Factor Authentication wilt gebruiken, raden we u aan [geconvergeerde registratie te gebruiken voor selfservice wachtwoordreset (SSPR) en Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) om uw gebruikers hun verificatiemethoden één keer te laten registreren.

-   Als u de gefaseerde implementatiefunctie wilt gebruiken, moet u een globale beheerder op uw tenant zijn.

-   Als u *naadloze SSO* in schakelt voor een specifiek Active Directory-forest, moet u domeinbeheerder zijn.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De volgende scenario's worden ondersteund voor gefaseerde implementatie. De functie werkt alleen voor:

- Gebruikers die zijn ingericht op Azure AD met Azure AD Connect. Het is niet van toepassing op alleen cloudgebruikers.

- Gebruikersaanmeldingsverkeer op browsers en *moderne verificatieclients.* Toepassingen of cloudservices die oudere verificatie gebruiken, vallen terug op federatieve verificatiestromen. Een voorbeeld hiervan is Exchange online met moderne verificatie uitgeschakeld of Outlook 2010, dat geen moderne verificatie ondersteunt.

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden niet ondersteund voor gefaseerde implementatie:

- Bepaalde toepassingen sturen de queryparameter 'domain_hint' tijdens verificatie naar Azure AD. Deze stromen worden voortgezet en gebruikers die zijn ingeschakeld voor gefaseerde implementatie, blijven federatie gebruiken voor verificatie.

<!-- -->

- Beheerders kunnen cloudverificatie uitrollen met behulp van beveiligingsgroepen. Om synchronisatielatentie te voorkomen wanneer u on-premises Active Directory-beveiligingsgroepen gebruikt, raden we u aan cloudbeveiligingsgroepen te gebruiken. De volgende voorwaarden zijn van toepassing:

    - U maximaal 10 groepen per functie gebruiken. Dat wil zeggen, u 10 groepen elk gebruiken voor *wachtwoord hash sync,* *pass-through authenticatie,* en *naadloze SSO*.
    - Geneste groepen *worden niet ondersteund.* Dit bereik is ook van toepassing op openbare preview.
    - Dynamische groepen *worden niet ondersteund* voor gefaseerde implementatie.
    - Contactobjecten in de groep blokkeren dat de groep wordt toegevoegd.

- U moet nog steeds de uiteindelijke cut-over maken van federatieve naar cloudverificatie met Azure AD Connect of PowerShell. Gefaseerde implementatie schakelt niet over van federatief naar beheerd.

- Wanneer u voor het eerst een beveiligingsgroep toevoegt voor gefaseerde implementatie, bent u beperkt tot 200 gebruikers om een UX-time-out te voorkomen. Nadat u de groep hebt toegevoegd, u er naar behoefte meer gebruikers direct aan toevoegen.

## <a name="get-started-with-staged-rollout"></a>Aan de slag met gefaseerde implementatie

Als u de *aanmelding voor het synchroniseren van wachtwoordhash* wilt testen met behulp van gefaseerde implementatie, volgt u de pre-work-instructies in de volgende sectie.

Zie [Azure AD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)voor informatie over welke PowerShell-cmdlets u moet gebruiken.

## <a name="pre-work-for-password-hash-sync"></a>Vooraf werken voor het synchroniseren van wachtwoordhash

1.  *Wachtwoordhashsynchronisatie* inschakelen vanaf de pagina [Optionele functies](how-to-connect-install-custom.md#optional-features) in Azure AD Connect. 

   ![Schermafbeelding van de pagina 'Optionele onderdelen' in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Controleer of een volledige *wachtwoordhashsynchronisatiecyclus* is uitgevoerd, zodat alle wachtwoordhashes van de gebruikers zijn gesynchroniseerd met Azure AD. Als u de status van *wachtwoordhashsynchronisatie*wilt controleren, u de PowerShell-diagnose gebruiken in [Het oplossen van wachtwoordhashsynchronisatie met Azure AD Connect-synchronisatie](tshoot-connect-password-hash-synchronization.md).

   ![Schermafbeelding van het logboek aadconnect-probleemoplossing](./media/how-to-connect-staged-rollout/sr2.png)

Als u *aanmelding voor passthrough-verificatie* wilt testen met gefaseerde implementatie, schakelt u deze in door de vooraf werkende instructies in de volgende sectie te volgen.

## <a name="pre-work-for-pass-through-authentication"></a>Vooraf werken voor pass-through-verificatie

1. Identificeer een server waarop Windows Server 2012 R2 of hoger wordt uitgevoerd, waar u de verificatieagent voor *doorgeefverificatie* wilt uitvoeren. 

   *Kies niet* voor de Azure AD Connect-server.Controleer of de server is gekoppeld aan het domein, dat geselecteerde gebruikers kan verifiëren met Active Directory en kan communiceren met Azure AD op uitgaande poorten en URL's. Zie het gedeelte 'Stap 1: De vereisten controleren' van [Quickstart: Azure AD-naadloze eenmalige aanmelding](how-to-connect-sso-quick-start.md).

1. [Download de Azure AD Connect-verificatieagent](https://aka.ms/getauthagent)en installeer deze op de server. 

1. Als u [hoge beschikbaarheid wilt](how-to-connect-sso-quick-start.md)inschakelen, installeert u extra verificatieagents op andere servers.

1. Zorg ervoor dat u uw [Smart Lockout-instellingen](../authentication/howto-password-smart-lockout.md) op de juiste manier hebt geconfigureerd. Als u dit doet, u ervoor zorgen dat de on-premises Active Directory-accounts van uw gebruikers niet worden vergrendeld door slechte acteurs.

We raden u aan *naadloze SSO* in te schakelen, ongeacht de aanmeldingsmethode *(wachtwoordhashsynchronisatie* of *pass-through-verificatie)* die u selecteert voor gefaseerde implementatie. Om *naadloze SSO*mogelijk te maken, volgt u de pre-work instructies in het volgende gedeelte.

## <a name="pre-work-for-seamless-sso"></a>Pre-work voor naadloze SSO

Schakel *naadloze SSO* in de Active Directory-forests in met PowerShell. Als u meer dan één Active Directory-forest hebt, schakelt u het voor elk forest afzonderlijk in.  *Naadloze SSO* wordt alleen geactiveerd voor gebruikers die zijn geselecteerd voor gefaseerde implementatie. Dit heeft geen invloed op de bestaande federatie-instelling.

Schakel *naadloze SSO in* door het volgende te doen:

1. Meld u aan bij Azure AD Connect Server.

2. Ga naar de map *\\%programfiles% Microsoft Azure Active Directory Connect.* 

3. Importeer de *naadloze SSO* PowerShell-module door de volgende opdracht uit te voeren: 

   `Import-Module .\AzureADSSO.psd1`

4. Voer PowerShell uit als beheerder. Bel in `New-AzureADSSOAuthenticationContext`PowerShell. Met deze opdracht wordt een deelvenster geopend waarin u de algemene beheerdersreferenties van uw tenant invoeren.

5. Bel `Get-AzureADSSOStatus | ConvertFrom-Json`. Met deze opdracht wordt een lijst met Active Directory-forests weergegeven (zie de lijst 'Domeinen') waarop deze functie is ingeschakeld. Standaard is deze ingesteld op false op tenantniveau.

   ![Voorbeeld van de Windows PowerShell-uitvoer](./media/how-to-connect-staged-rollout/sr3.png)

6. Bel `$creds = Get-Credential`. Voer bij de prompt de referenties van de domeinbeheerder in voor het beoogde Active Directory-forest.

7. Bel `Enable-AzureADSSOForest -OnPremCredentials $creds`. Met deze opdracht wordt het AZUREADSSOACC-computeraccount gemaakt van de on-premises domeincontroller voor het Active Directory-forest dat vereist is voor *naadloze SSO.*

8. *Naadloze SSO* vereist URL's om zich in de intranetzone te bevinden. Zie [Quickstart: Azure AD naadloze single sign-on](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)als u deze URL's wilt implementeren met groepsbeleid.

9. Voor een volledige walkthrough u ook onze [implementatieplannen](https://aka.ms/SeamlessSSODPDownload) voor *naadloze SSO*downloaden.

## <a name="enable-staged-rollout"></a>Gefaseerde implementatie inschakelen

Als u een specifieke functie *(pass-through-authenticatie,* *wachtwoordhashsynchronisatie*of *naadloze SSO)* wilt uitrollen naar een bepaalde groep gebruikers in een groep, volgt u de instructies in de volgende secties.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Een gefaseerde implementatie van een specifieke functie op uw tenant inschakelen

U een van de volgende opties uitrollen:

- **Optie Een** - *wachtwoordhash synchroniseert* + *naadloze SSO*
- **Optie B** - *pass-through authenticatie* + *naadloze SSO*
- **Niet ondersteund** - *wachtwoord hash sync* + *pass-through authenticatie* + *naadloze SSO*

Ga als volgt te werk:

1. Als u de voorbeeldUX wilt openen, meldt u zich aan bij de [Azure AD-portal](https://aka.ms/stagedrolloutux).

2. Selecteer de koppeling **Gefaseerde implementatie voor beheerde gebruikersaanmelding (Voorbeeld).**

   Als u bijvoorbeeld optie *A*wilt inschakelen, schuift u de **besturingselementen wachtwoordhashsynchronisatie** en **Naadloze besturingselementen voor eenmalige aanmelding** naar **Aan,** zoals in de volgende afbeeldingen wordt weergegeven.

   ![De pagina Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![De pagina 'Gefaseerde implementatiefuncties (voorbeeld)' inschakelen](./media/how-to-connect-staged-rollout/sr5.png)

3. Voeg de groepen toe aan de functie om *pass-through-authenticatie* en *naadloze SSO*in te schakelen. Om een UX-time-out te voorkomen, moet u ervoor zorgen dat de beveiligingsgroepen in eerste instantie niet meer dan 200 leden bevatten.

   ![De pagina 'Groepen beheren voor wachtwoordhashsynchronisatie (voorbeeld)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >De leden in een groep worden automatisch ingeschakeld voor gefaseerde implementatie. Geneste en dynamische groepen worden niet ondersteund voor gefaseerde implementatie.

## <a name="auditing"></a>Controleren

We hebben auditgebeurtenissen ingeschakeld voor de verschillende acties die we uitvoeren voor gefaseerde implementatie:

- Controlegebeurtenis wanneer u een gefaseerde implementatie inschakelt voor *wachtwoordhashsynchronisatie,* *pass-through-verificatie*of *naadloze SSO*.

  >[!NOTE]
  >Een auditgebeurtenis wordt geregistreerd wanneer *naadloze SSO* is ingeschakeld met behulp van gefaseerde implementatie.

  ![Het deelvenster 'Implementatiebeleid voor functie maken' - tabblad Activiteit](./media/how-to-connect-staged-rollout/sr7.png)

  ![Het deelvenster 'Implementatiebeleid voor functie maken' - tabblad Gewijzigde eigenschappen](./media/how-to-connect-staged-rollout/sr8.png)

- Controlegebeurtenis wanneer een groep wordt toegevoegd aan *wachtwoordhashsynchronisatie,* *pass-through-verificatie*of *naadloze SSO*.

  >[!NOTE]
  >Een controlegebeurtenis wordt geregistreerd wanneer een groep wordt toegevoegd aan *wachtwoordhashsynchronisatie* voor gefaseerde implementatie.

  ![Het deelvenster 'Een groep toevoegen om de uitrol functie' te geven - tabblad Activiteit](./media/how-to-connect-staged-rollout/sr9.png)

  ![Het deelvenster 'Een groep toevoegen om de uitrol functie' te geven - Tabblad Gewijzigde eigenschappen](./media/how-to-connect-staged-rollout/sr10.png)

- Controlegebeurtenis wanneer een gebruiker die aan de groep is toegevoegd, is ingeschakeld voor gefaseerde implementatie.

  ![Het deelvenster 'Gebruiker toevoegen aan functie-implementatie' - tabblad Activiteit](media/how-to-connect-staged-rollout/sr11.png)

  ![Het deelvenster 'Gebruiker toevoegen aan uitrol van functies' - tabblad Doel(en)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validatie

Ga als volgt te werk om de aanmelding te testen met *wachtwoordhashsynchronisatie* of *pass-through-verificatie* (aanmelding van gebruikersnaam en wachtwoord):

1. Ga op het extranet naar de [pagina Apps](https://myapps.microsoft.com) in een privébrowsersessie en voer vervolgens de UserPrincipalName (UPN) in van het gebruikersaccount dat is geselecteerd voor gefaseerde implementatie.

   Gebruikers die zijn getarget voor gefaseerde implementatie worden niet doorgestuurd naar uw federatieve aanmeldingspagina. In plaats daarvan wordt hen gevraagd zich aan te melden op de aanmeldingspagina van azure AD-tenant.

1. Controleer of de aanmelding is weergegeven in het [aanmeldingsrapport van Azure AD](../reports-monitoring/concept-sign-ins.md) door te filteren met de UserPrincipalName.

Aanmelden testen met *naadloze SSO:*

1. Ga op het intranet naar de [pagina Apps](https://myapps.microsoft.com) in een privébrowsersessie en voer vervolgens de UserPrincipalName (UPN) in van het gebruikersaccount dat is geselecteerd voor gefaseerde implementatie.

   Gebruikers die zijn gericht op gefaseerde uitrol van *naadloze SSO* worden gepresenteerd met een "Proberen om u in te loggen ..." bericht voordat ze in stilte zijn aangemeld.

1. Controleer of de aanmelding is weergegeven in het [aanmeldingsrapport van Azure AD](../reports-monitoring/concept-sign-ins.md) door te filteren met de UserPrincipalName.

   Als u aanmeldingen van gebruikers wilt bijhouden die nog steeds voorkomen op Active Directory Federation Services (AD FS) voor geselecteerde gefaseerde implementatiegebruikers, volgt u de instructies bij [AD FS-probleemoplossing: Gebeurtenissen en logboekregistratie](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Controleer leveranciersdocumentatie over hoe u dit controleren op externe federatieproviders.

## <a name="remove-a-user-from-staged-rollout"></a>Een gebruiker verwijderen uit gefaseerde implementatie

Als u een gebruiker uit de groep verwijdert, wordt de gefaseerde implementatie voor die gebruiker uitgeschakeld. Als u de gefaseerde implementatiefunctie wilt uitschakelen, schuift u het besturingselement terug naar **Uit**.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Kan ik deze mogelijkheid gebruiken in de productie?**

A: Ja, u deze functie gebruiken in uw productietenant, maar we raden u aan deze eerst uit te proberen in uw testtenant.

**V: Kan deze functie worden gebruikt om een permanente "coëxistentie" te behouden, waarbij sommige gebruikers federatieve verificatie gebruiken en anderen cloudverificatie gebruiken?**

A: Nee, deze functie is ontworpen voor het migreren van federatieve naar cloudauthenticatie in fasen en vervolgens om uiteindelijk over te gaan tot cloudverificatie. We raden het gebruik van een permanente gemengde status af, omdat deze aanpak kan leiden tot onverwachte verificatiestromen.

**V: Kan ik PowerShell gebruiken om gefaseerde implementatie uit te voeren?**

A: Ja. Zie [Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)voor meer informatie over het gebruik van PowerShell om gefaseerde implementatie uit te voeren.

## <a name="next-steps"></a>Volgende stappen
- [Azure AD 2.0-voorbeeld](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
