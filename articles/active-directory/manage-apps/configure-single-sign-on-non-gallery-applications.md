---
title: SAML single sign-on - non-gallery applications - Microsoft identity platform | Microsoft Documenten
description: Eenmalige aanmelding (SSO) configureren voor niet-galerietoepassingen in Microsoft-identiteitsplatform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244210"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Saml-gebaseerde aanmelding configureren voor niet-galerietoepassingen

Wanneer u [een galerie-app](add-gallery-app.md) of een [web-app zonder galerie](add-non-gallery-app.md) toevoegt aan uw Azure AD Enterprise-toepassingen, is een van de opties voor één aanmelding die voor u beschikbaar [is, saml-gebaseerde eenmalige aanmelding](what-is-single-sign-on.md#saml-sso). Kies SAML waar mogelijk voor toepassingen die verifiëren met behulp van een van de SAML-protocollen. Met SAML single sign-on verifieert Azure AD de toepassing met behulp van het Azure AD-account van de gebruiker. Azure AD communiceert de aanmeldingsgegevens naar de toepassing via een verbindingsprotocol. U gebruikers toewijzen aan specifieke toepassingsrollen op basis van regels die u definieert in uw SAML-claims. In dit artikel wordt beschreven hoe u SAML-gebaseerde eenmalige aanmelding configureert voor een niet-galerij-app. 

> [!NOTE]
> Een galerie-app toevoegen? Stapsgewijze installatie-instructies zoeken in de [lijst met zelfstudies van de SaaS-app](../saas-apps/tutorial-list.md)

Als u SAML-een-aanmelding wilt configureren voor een niet-galerietoepassing zonder code te schrijven, moet u een abonnement hebben samen met een Azure AD Premium-licentie en moet de toepassing SAML 2.0 ondersteunen. Ga naar Azure AD-prijzen voor meer informatie over Azure [AD-versies.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="before-you-begin"></a>Voordat u begint

Zie [Een niet-galerie-app toevoegen](add-non-gallery-app.md)als de toepassing niet is toegevoegd aan uw Azure AD-tenant.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Step 1. De basisSAML-configuratie bewerken

1. Meld u bij [Azure Portal](https://portal.azure.com) aan als beheerder van de cloudtoepassing of als toepassingsbeheerder voor uw Azure Active Directory-tenant.

2. Navigeer naar **Azure Active Directory** > **Enterprise-toepassingen** en selecteer de toepassing in de lijst. 
   
   - Als u naar de toepassing wilt zoeken, selecteert u in het menu **Toepassingstype** **Alle toepassingen**en selecteert u **Toepassen**. Voer de naam van de toepassing in het zoekvak in en selecteer de toepassing in de resultaten.

3. Selecteer onder de sectie **Beheren** de optie **Eén aanmelding**. 

4. Selecteer **SAML**. De pagina **Eén aanmelding instellen met SAML - Voorbeeld** wordt weergegeven.

   ![Stap 1 De basisSAML-configuratie bewerken](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Als u de basisconfiguratieopties voor SAML wilt bewerken, selecteert u het pictogram **Bewerken** (een potlood) in de rechterbovenhoek van de sectie **BasisSAML-configuratie.**

1. Voer de volgende instellingen in. U moet de waarden van de toepassingsleverancier krijgen. U handmatig de waarden invoeren of een metagegevensbestand uploaden om de waarde van de velden te extraheren.

    | Basis-SAML-configuratie-instelling | SP-geïnitieerd | idP-geïnitieerd | Beschrijving |
    |:--|:--|:--|:--|
    | **Id (Entiteits-id)** | Vereist voor sommige apps | Vereist voor sommige apps | Identificeert de toepassing op unieke wijze. Azure Active Directory stuurt de id naar de toepassing als parameter van de doelgroep van het SAML-token. Er wordt verwacht dat de toepassing deze valideert. Deze waarde wordt ook weergegeven als de entiteit-ID in SAML-metagegevens die worden geleverd door de toepassing. Voer een URL in die het<subdomain>volgende patroon gebruikt: 'https:// .contoso.com' *U deze waarde vinden als het element **Uitgever** in het **AuthnRequest-verzoek** (SAML-verzoek) dat door de toepassing wordt verzonden.* |
    | **Url van antwoord beantwoorden** | Vereist | Vereist | Hiermee geeft u op waar de toepassing verwacht het SAML-token te ontvangen. De antwoord-URL wordt ook de ACS-URL (Assertion Consumer Service) genoemd. U de extra antwoord-URL-velden gebruiken om meerdere antwoord-URL's op te geven. U hebt bijvoorbeeld extra antwoord-URL's nodig voor meerdere subdomeinen. Of voor testdoeleinden u meerdere antwoord-URL's (lokale host- en openbare URL's) tegelijk opgeven. |
    | **Aanmeldings-URL** | Vereist | Niet opgeven | Wanneer een gebruiker deze URL opent, leidt de serviceprovider hem om naar Azure Active Directory om de gebruiker te verifiëren en aan te melden. Azure Active Directory maakt gebruik van de URL om de toepassing te starten vanuit Office 365 of het toegangsvenster van Azure Active Directory. Wanneer azure AD leeg is, voert u aanmelding met IdP uit wanneer een gebruiker de toepassing start vanuit Office 365, het Azure AD Access-paneel of de URL van Azure AD SSO.|
    | **Relaystatus** | Optioneel | Optioneel | Hiermee geeft u aan de toepassing op waar de gebruiker naar moet worden omgeleid nadat de verificatie is voltooid. Meestal is de waarde een geldige URL voor de toepassing. Sommige toepassingen gebruiken dit veld echter anders. Vraag de leverancier van de toepassing voor meer informatie.
    | **Afmeldings-URL** | Optioneel | Optioneel | Wordt gebruikt om de SAML Logout-antwoorden terug te sturen naar de toepassing.

Zie [Saml-protocol met één aanmelding voor](../develop/single-sign-on-saml-protocol.md)meer informatie .

## <a name="step-2-configure-user-attributes-and-claims"></a>Stap 2. Gebruikerskenmerken en -claims configureren 

Wanneer een gebruiker zich verifieert naar de toepassing, geeft Azure AD de toepassing een SAML-token uit met informatie (of claims) over de gebruiker die deze persoon op unieke wijze identificeert. Standaard bevat deze informatie de gebruikersnaam, het e-mailadres, de voornaam en de achternaam van de gebruiker. Mogelijk moet u deze claims aanpassen als de toepassing bijvoorbeeld specifieke claimwaarden of een **andere naamnotatie** dan gebruikersnaam vereist. Vereisten voor galerij-apps worden beschreven in de [toepassingsspecifieke zelfstudies](../saas-apps/tutorial-list.md)of u het de leverancier van de toepassing vragen. De algemene stappen voor het configureren van gebruikerskenmerken en claims worden hieronder beschreven.

1. Selecteer in de sectie **Gebruikerskenmerken en Claims** het pictogram **Bewerken** (een potlood) in de rechterbovenhoek.

   ![Stap 2 Gebruikerskenmerken en -claims configureren](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Controleer de waarde van de **naam-id**. De standaardwaarde is *user.principalname*. De gebruikers-id is uniek voor elke gebruiker in de toepassing. Als het e-mailadres bijvoorbeeld zowel de gebruikersnaam als de unieke id is, wordt de waarde ingesteld op *user.mail*.

3. Als u de waarde van de **naam-id wilt**wijzigen, selecteert u het pictogram **Bewerken** (een potlood) voor het veld Waarde van de **naam-id.** Breng indien nodig de juiste wijzigingen aan in de indeling en bron van de id. Zie [NameId bewerken](../develop/active-directory-saml-claims-customization.md#editing-nameid)voor meer informatie . Sla de wijzigingen op wanneer u klaar bent. 
 
4. Als u groepsclaims wilt configureren, selecteert u het pictogram **Bewerken** voor het veld Groepen dat is geretourneerd in het veld **Claim.** Zie [Groepsclaims configureren](../hybrid/how-to-connect-fed-group-claims.md)voor meer informatie .

5. Als u een claim wilt toevoegen, selecteert u **Nieuwe claim toevoegen** boven aan de pagina. Voer de **naam in** en selecteer de juiste bron. Als u de **kenmerkbron** selecteert, moet u het **kenmerk Bron** kiezen dat u wilt gebruiken. Als u de **vertaalbron** selecteert, moet u de **transformatie** en **parameter 1** kiezen die u wilt gebruiken. Zie [Toepassingsspecifieke claims toevoegen](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)voor meer informatie. Sla de wijzigingen op wanneer u klaar bent. 

6. Selecteer **Opslaan**. De nieuwe claim wordt weergegeven in de tabel.

   > [!NOTE]
   > Zie de volgende bronnen voor aanvullende manieren om het SAML-token van Azure AD aan uw toepassing aan te passen.
   >- Zie [Rolclaims configureren](../develop/active-directory-enterprise-app-role-management.md)als u aangepaste rollen wilt maken via de Azure-portal.
   >- Zie [Claims aanpassen - PowerShell](../develop/active-directory-claims-mapping.md)om de claims via PowerShell aan te passen.
   >- Zie [Optionele claims configureren](../develop/active-directory-optional-claims.md)als u het toepassingsmanifest wilt wijzigen om optionele claims voor uw toepassing te configureren.
   >- Zie [Token-levensduur](../develop/active-directory-configurable-token-lifetimes.md)instellen als u tokenlevenslangbeleid voor vernieuwingstokens wilt instellen, toegang tot tokens, sessietokens en ID-tokens. Of zie [verificatiesessiebeheermogelijkheden](https://go.microsoft.com/fwlink/?linkid=2083106)om verificatiesessies via Voorwaardelijke toegang voor Azure AD te beperken.

## <a name="step-3-manage-the-saml-signing-certificate"></a>Stap 3. Het SAML-ondertekeningscertificaat beheren

Azure AD gebruikt een certificaat om de SAML-tokens te ondertekenen die naar de toepassing worden verzendt. U hebt dit certificaat nodig om de vertrouwensrelatie tussen Azure AD en de toepassing in te stellen. Zie de SAML-documentatie van de toepassing voor meer informatie over de certificaatindeling. Zie [Certificaten beheren voor federatieve opties voor één aanmelding](manage-certificates-for-federated-single-sign-on.md) en Geavanceerde [certificaatondertekening in het SAML-token](certificate-signing-options.md)voor meer informatie.

Vanuit Azure AD u het actieve certificaat in Base64- of Raw-indeling rechtstreeks downloaden vanaf de hoofdpagina **Eén aanmelding instellen met SAML-pagina.** U het actieve certificaat ook ophalen door het XML-bestand met toepassingsmetagegevens te downloaden of door de URL van de app-federatiemetagegevens te gebruiken. Voer deze stappen uit om uw certificaten weer te geven, te maken of te downloaden (actief of inactief).

1. Ga naar de sectie **SAML-ondertekeningscertificaat.** 

   ![Stap 3 Het SAML-ondertekeningscertificaat beheren](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Controleer of het certificaat het:

   - *De gewenste vervaldatum.* U de vervaldatum tot drie jaar in de toekomst configureren.
   - *Een status van actief voor het gewenste certificaat.* Als de status **inactief**is, wijzigt u de status in **Actief**. Als u de status wilt wijzigen, klikt u met de rechtermuisknop op de rij van het gewenste certificaat en selecteert u **Certificaat actief maken**.
   - *De juiste ondertekeningsoptie en algoritme.*
   - *Het juiste e-mailadres(en) van de melding.* Wanneer het actieve certificaat zich in de buurt van de vervaldatum bevindt, stuurt Azure AD een melding naar het e-mailadres dat in dit veld is geconfigureerd.

2. Als u het certificaat wilt downloaden, selecteert u een van de opties voor Base64-indeling, Raw-indeling of Federatie-metagegevens XML. Azure AD biedt ook de **url met ametagegevens van de appfederatie** waar u toegang hebt tot de metagegevens die specifiek zijn voor de toepassing in de indeling. `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

3. Als u een certificaat wilt beheren, maken of importeren, selecteert u het pictogram **Bewerken** (een potlood) in de rechterbovenhoek van de sectie **SAML-ondertekeningscertificaat.**

   ![SAML-ondertekeningscertificaat](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Onderneem een van de volgende acties:

   - Als u een nieuw certificaat wilt maken, selecteert u **Nieuw certificaat,** selecteert u de **vervaldatum**en selecteert u **Opslaan**. Als u het certificaat wilt activeren, selecteert u het contextmenu (**... )** en selecteert u Certificaat **actief maken**.
   - Als u een certificaat wilt uploaden met persoonlijke sleutel- en pfx-referenties, selecteert u **Certificaat importeren** en bladert u naar het certificaat. Voer het **PFX-wachtwoord in**en selecteer **Toevoegen**.  
   - Als u geavanceerde opties voor het ondertekenen van certificaten wilt configureren, gebruikt u de volgende opties. Zie het artikel Opties voor [geavanceerde certificaatondertekeningvoor](certificate-signing-options.md) beschrijvingen van deze opties.
      - Kies in de vervolgkeuzelijst **Ondertekeningsoptie** de optie **SamL-antwoord ondertekenen,** **de bewering van Sign SAML**of De reactie en bewering van Sign **SAML**.
      - Kies in de vervolgkeuzelijst **Ondertekeningsalgoritme** de optie **SHA-1** of **SHA-256**.
   - Als u extra mensen wilt melden wanneer het actieve certificaat de vervaldatum is, voert u de e-mailadressen in de velden **E-mailadressen van de melding** in.

4. Als u wijzigingen hebt aangebracht, selecteert **u Opslaan** boven aan de sectie **SAML-ondertekeningscertificaat.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Stap 4. De toepassing instellen voor het gebruik van Azure AD

In de sectie ** \<ApplicationName>instellen** worden de waarden weergegeven die in de toepassing moeten worden geconfigureerd, zodat Azure AD wordt gebruikt als SAML-identiteitsprovider. De vereiste waarden variëren afhankelijk van de toepassing. Zie voor meer informatie de SAML-documentatie van de toepassing. Als u de documentatie wilt vinden, gaat u naar de ** \<van de toepassingsnaam>** instellen en selecteert **u Stapsgewijze instructies weergeven.** De documentatie wordt weergegeven op de **aanmeldingspagina configureren.** Op die pagina u de **URL voor aanmelding,** **Azure AD-id**en **URL-logboeken** invullen in de kop **>van de toepassing instellen. \<**

1. Schuif omlaag naar de sectie **ApplicationName>\<instellen.** 
   
   ![Stap 4 De toepassing instellen](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Kopieer de waarde van elke rij in deze sectie indien nodig en volg de toepassingsspecifieke instructies voor het toevoegen van de waarde aan de toepassing. Voor galerie-apps u de documentatie bekijken door **stapsgewijze instructies weergeven**te selecteren. 
   - De **URL-waarden voor aanmelding** en **bijloguit** worden beide opgelost tot hetzelfde eindpunt, het eindpunt voor het afhandelen van SAML-aanvragen voor uw exemplaar van Azure AD. 
   - De **Azure AD-id** is de waarde van de **emittent** in het SAML-token dat aan de toepassing is uitgegeven.
2. Wanneer u alle waarden in de juiste velden hebt geplakt, selecteert u **Opslaan**.

## <a name="step-5-validate-single-sign-on"></a>Stap 5. Eenmalige aanmelding valideren

Zodra u uw toepassing hebt geconfigureerd om Azure AD te gebruiken als een saml-gebaseerde identiteitsprovider, u de instellingen testen om te zien of eenmalige aanmelding werkt voor uw account. 

2. Schuif naar de **sectie Eén <applicationName> aanmelding valideren met** sectie.

   ![Stap 5 Eenmalige aanmelding valideren](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selecteer **Valideren**. De test-opties worden weergegeven.

4. Selecteer **Aanmelden als huidige gebruiker**. 

Als aanmelding succesvol is, u gebruikers en groepen toewijzen aan uw SAML-toepassing.
Als er een foutbericht wordt weergegeven, voert u de volgende stappen uit:

1. Kopieer en plak de gegevens in het vak **Hoe ziet de fout eruit?**.

    ![Hulp krijgen bij het oplossen van problemen](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecteer **Richtlijnen voor resolutie ontvangen**. De richtlijnen voor de oorzaak en de oplossing van het probleem worden weergegeven.  In dit voorbeeld was de gebruiker niet toegewezen aan de toepassing.

3. Lees de oplossingsrichtlijnen en los het probleem, indien mogelijk, op.

4. Voer de test opnieuw uit totdat de bewerking is voltooid.

Zie [SamL-gebaseerde aanmelding op basis van toepassingen in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](methods-for-assigning-users-and-groups.md)
- [Automatische gebruikersaccountinrichting configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
