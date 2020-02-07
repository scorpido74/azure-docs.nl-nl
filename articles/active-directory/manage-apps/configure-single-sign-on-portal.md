---
title: Eenmalige aanmelding configureren - Azure Active Directory | Microsoft Docs
description: In deze zelfstudie wordt de Azure-portal gebruikt om eenmalige aanmelding voor een toepassing met Azure Active Directory (Azure AD) op basis van SAML te configureren.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: a415ac749d0d322bc2f71f64d4bec6e32ad1f12e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063489"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Eenmalige aanmelding op basis van SAML configureren

Nadat u een app hebt toegevoegd aan uw Azure AD-zakelijke toepassingen, configureert u instellingen voor eenmalige aanmelding. In dit artikel wordt beschreven hoe u eenmalige aanmelding op basis van SAML kunt configureren voor een niet-galerij-app. 

> [!NOTE]
> Een galerie-app toevoegen? Stapsgewijze instructies voor het instellen van de installatie vindt u in de [lijst met zelf studies over SaaS-apps](../saas-apps/tutorial-list.md)

Als u eenmalige aanmelding wilt configureren voor een niet-galerie toepassing *zonder code te schrijven*, moet u een abonnement of Azure AD Premium hebben en moet de toepassing SAML 2,0 ondersteunen. Ga voor meer informatie over Azure AD-versies naar [Azure ad-prijzen](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Voordat u begint

- Als de toepassing niet is toegevoegd aan uw Azure AD-Tenant, raadpleegt u [een galerie-app toevoegen](add-gallery-app.md) of [een niet-galerij-app toevoegen](add-non-gallery-app.md).
- Neem contact op met de leverancier van uw toepassing om de juiste informatie te verkrijgen voor de volgende instellingen:

    | Basis configuratie-instelling voor SAML | SP-geïnitieerd | idP-geïnitieerd | Beschrijving |
    |:--|:--|:--|:--|
    | Id (Entiteits-id) | Vereist voor sommige apps | Vereist voor sommige apps | Unieke identificatie van de toepassing waarvoor eenmalige aanmelding wordt geconfigureerd. Azure Active Directory stuurt de id naar de toepassing als parameter van de doelgroep van het SAML-token. Er wordt verwacht dat de toepassing deze valideert. Deze waarde wordt ook weergegeven als de entiteit-ID in SAML-metagegevens die worden geleverd door de toepassing. *U kunt deze waarde vinden als het element van de **verlener** in het **AuthnRequest** (SAML-aanvraag) dat door de toepassing is verzonden.* |
    | Antwoord-URL | Optioneel | Vereist | Hiermee geeft u op waar de toepassing verwacht het SAML-token te ontvangen. De antwoord-URL wordt ook de ACS-URL (Assertion Consumer Service) genoemd. |
    | Aanmeldings-URL | Vereist | Niet opgeven | Wanneer een gebruiker deze URL opent, leidt de serviceprovider hem om naar Azure Active Directory om de gebruiker te verifiëren en aan te melden. Azure Active Directory maakt gebruik van de URL om de toepassing te starten vanuit Office 365 of het toegangsvenster van Azure Active Directory. Als dit leeg is, is Azure AD afhankelijk van de ID-provider om eenmalige aanmelding te starten wanneer een gebruiker de toepassing start.|
    | Relaystatus | Optioneel | Optioneel | Hiermee geeft u aan de toepassing op waar de gebruiker naar moet worden omgeleid nadat de verificatie is voltooid. De waarde is doorgaans een geldige URL voor de toepassing. Sommige toepassingen gebruiken dit veld echter anders. Vraag de leverancier van de toepassing voor meer informatie.
    | Afmeldings-URL | Optioneel | Optioneel | Wordt gebruikt om de antwoorden op SAML-afmeldingen terug te sturen naar de toepassing.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Stap 1. De basis-SAML-configuratie bewerken

1. Meld u bij [Azure Portal](https://portal.azure.com) aan als beheerder van de cloudtoepassing of als toepassingsbeheerder voor uw Azure Active Directory-tenant.

1. Ga naar **Azure Active Directory** > **bedrijfs toepassingen** en selecteer de toepassing in de lijst. 
   
   - Als u wilt zoeken naar de toepassing, selecteert u in het menu **toepassings type** **alle toepassingen**en selecteert u vervolgens **Toep assen**. Voer de naam van de toepassing in het zoekvak in en selecteer vervolgens de toepassing in de resultaten.

1. Selecteer onder de sectie **beheren** de optie **eenmalige aanmelding**. 

1. Selecteer **SAML**. De pagina **eenmalige aanmelding met SAML-preview instellen** wordt weer gegeven.

1. Als u de basis opties voor SAML-configuratie wilt bewerken, selecteert u het **bewerkings** pictogram (een potlood) in de rechter bovenhoek van de **basis-SAML-configuratie** sectie.

     ![Certificaten configureren](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Voer in de juiste velden de informatie in die wordt beschreven in de sectie [voordat u begint](#before-you-begin) .

1. Selecteer boven aan de pagina **Opslaan**.

## <a name="step-2-configure-user-attributes-and-claims"></a>Stap 2. Gebruikers kenmerken en claims configureren 

Een toepassing kan specifieke gebruikers kenmerken of claims vereisen in het SAML-token dat wordt ontvangen van Azure AD wanneer een gebruiker zich aanmeldt. U kunt bijvoorbeeld specifieke claim-Uri's of claim waarden opgeven, of de **naam** moet mogelijk iets anders zijn dan de gebruikers naam die is opgeslagen in het micro soft Identity platform. De vereisten voor galerij-apps worden beschreven in de [toepassingsspecifieke zelf studies](../saas-apps/tutorial-list.md), of u kunt de leverancier van de toepassing vragen. De algemene stappen voor het configureren van gebruikers kenmerken en claims worden hieronder beschreven.

1. Selecteer in de sectie **gebruikers kenmerken en claims** het **bewerkings** pictogram (een potlood) in de rechter bovenhoek.

1. Controleer de **waarde**van de naam-id. De standaard waarde is *User. Principal*name. De gebruikers-id is uniek voor elke gebruiker in de toepassing. Als het e-mailadres bijvoorbeeld zowel de gebruikersnaam als de unieke id is, wordt de waarde ingesteld op *user.mail*.

1. Als u de **naam-id-waarde**wilt wijzigen, selecteert u het **bewerkings** pictogram (een potlood) voor het veld **naam-id** . Breng de gewenste wijzigingen aan in de id-indeling en-bron, indien nodig. Zie voor meer informatie [bewerken van NameID](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Sla de wijzigingen op wanneer u klaar bent. 
 
1. Als u groeps claims wilt configureren, selecteert u het **bewerkings** pictogram voor de groepen die zijn **geretourneerd in claim** veld. Zie [groeps claims configureren](../hybrid/how-to-connect-fed-group-claims.md)voor meer informatie.

3. Als u een claim wilt toevoegen, selecteert u **nieuwe claim toevoegen** boven aan de pagina. Voer de **naam** in en selecteer de juiste bron. Als u de **kenmerk** bron selecteert, moet u het **bron kenmerk** kiezen dat u wilt gebruiken. Als u de **Vertaal** bron selecteert, moet u de **trans formatie** en **para meter 1** kiezen die u wilt gebruiken. Zie voor meer informatie [toepassingsspecifieke claims toevoegen](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Sla de wijzigingen op wanneer u klaar bent. 

4. Selecteer **Opslaan**. De nieuwe claim wordt weer gegeven in de tabel.

   > [!NOTE]
   > Raadpleeg de volgende bronnen voor meer manieren om het SAML-token van Azure AD aan uw toepassing aan te passen.
   >- Zie [Configure Role claims](../develop/active-directory-enterprise-app-role-management.md)voor het maken van aangepaste rollen via de Azure Portal.
   >- Zie [claims aanpassen-Power shell](../develop/active-directory-claims-mapping.md)voor informatie over het aanpassen van claims via Power shell.
   >- Zie [optionele claims configureren](../develop/active-directory-optional-claims.md)om het toepassings manifest te wijzigen voor het configureren van optionele claims voor uw toepassing.
   >- Zie [token levensduur configureren](../develop/active-directory-configurable-token-lifetimes.md)voor meer informatie over het instellen van de token levensduur beleid voor vernieuwings tokens, toegangs tokens, sessie tokens en id-tokens. Als u verificatie sessies via voorwaardelijke toegang van Azure AD wilt beperken, raadpleegt u de [beheer mogelijkheden voor verificatie sessies](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Stap 3. Het SAML-handtekening certificaat beheren

Azure AD maakt gebruik van een certificaat voor het ondertekenen van de SAML-tokens die worden verzonden naar de toepassing. Op de pagina **eenmalige aanmelding met SAML instellen** kunt u het actieve certificaat weer geven of downloaden. U kunt ook een certificaat bijwerken, maken of importeren. Voor galerie toepassingen zijn details over de certificaat indeling beschikbaar in de SAML-documentatie van de toepassing (Zie de [toepassingsspecifieke zelf studies](../saas-apps/tutorial-list.md)). 

1. Ga naar de sectie **SAML-handtekening certificaat** . Afhankelijk van het type toepassing worden de opties voor het downloaden van het certificaat in Base64-indeling, RAW-indeling of federatieve meta gegevens-XML weer geven. Azure AD biedt ook de **URL voor de federatieve meta gegevens** van de app, waar u toegang kunt krijgen tot de meta gegevens die specifiek zijn voor de toepassing in de indeling `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

1. Als u een certificaat wilt beheren, maken of importeren, selecteert u het **bewerkings** pictogram (een potlood) in de rechter bovenhoek van de sectie **SAML-handtekening certificaat** en voert u vervolgens een van de volgende handelingen uit:

   - Als u een nieuw certificaat wilt maken, selecteert u **Nieuw certificaat**, selecteert u de **verval datum**en selecteert u **Opslaan**. Als u het certificaat wilt activeren, selecteert u het context menu ( **...** ) en selecteert **u certificaat actief maken**.
   - Als u een certificaat met persoonlijke sleutel-en pfx-referenties wilt uploaden, selecteert u **certificaat importeren** en bladert u naar het certificaat. Voer het **PFX-wacht woord**in en selecteer vervolgens **toevoegen**.  
   - Als u geavanceerde opties voor certificaat ondertekening wilt configureren, gebruikt u de volgende opties. Zie het artikel [Geavanceerde opties voor certificaat ondertekening](certificate-signing-options.md) voor een beschrijving van deze opties.
      - Kies in de vervolg keuzelijst **handtekening opties** de optie **SAML-respons ondertekenen**, **SAML-bevestiging ondertekenen**of **SAML-respons en-bevestiging ondertekenen**.
      - Kies in de vervolg keuzelijst **handtekening algoritme** **SHA-1** of **SHA-256**.
   - Geef de e-mail adressen in de velden **e-mail adres** van de melding op als u meer mensen wilt laten weten wanneer het actieve certificaat bijna is verlopen.

1. Selecteer **Opslaan** boven aan de sectie **SAML-handtekening certificaat** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Stap 4. De toepassing instellen voor gebruik van Azure AD

De sectie **\<toepassings naam instellen >** bevat de waarden die moeten worden geconfigureerd in de toepassing, zodat Azure AD wordt gebruikt als een SAML-ID-provider. De vereiste waarden variëren afhankelijk van de toepassing. Zie de SAML-documentatie van de toepassing voor meer informatie.

1. Schuif omlaag naar de sectie **\<toepassingsnaam instellen >** . 
2. Kopieer de waarde van elke rij in deze sectie naar wens en volg de toepassingsspecifieke instructies om de waarde toe te voegen aan de toepassing. Voor galerie-apps kunt u de documentatie weer geven door stapsgewijze **instructies weer geven**te selecteren. 
   - De waarden van de **aanmeldings-URL** en de **afmeldings-URL** worden beide omgezet naar hetzelfde eind punt. Dit is het SAML-eind punt voor aanvraag verwerking voor uw exemplaar van Azure AD. 
   - De **Azure ad-id** is de waarde van de **VERLENER** in het SAML-token dat is uitgegeven aan de toepassing.
1. Wanneer u alle waarden in de juiste velden hebt geplakt, selecteert u **Opslaan**.

## <a name="step-5-validate-single-sign-on"></a>Stap 5. Eenmalige aanmelding valideren

U kunt de instellingen testen om te zien of eenmalige aanmelding werkt voor u, de beheerder.  

1. Open de instellingen voor eenmalige aanmelding voor uw toepassing. 
2. Schuif naar de sectie **eenmalige aanmelding valideren met <applicationName>** . Voor deze zelf studie wordt deze sectie ' **set up github-test**' genoemd.
3. Selecteer **testen**. De test-opties worden weergegeven.
4. Selecteer **Aanmelden als huidige gebruiker**. 

Als de aanmelding is geslaagd, kunt u gebruikers en groepen toewijzen aan uw SAML-toepassing.
Als er een fout bericht wordt weer gegeven, voert u de volgende stappen uit:

1. Kopieer en plak de gegevens in het vak **Hoe ziet de fout eruit?** .

    ![Gebruik het vak ' wat heeft de fout melding? ' voor hulp bij het oplossen van oplossingen](media/configure-single-sign-on-portal/error-guidance.png)

1. Selecteer **ondersteuning**voor het oplossen van oplossingen. De richtlijnen voor de oorzaak en de oplossing van het probleem worden weergegeven.  In dit voorbeeld was de gebruiker niet toegewezen aan de toepassing.
1. Lees de richt lijnen voor oplossingen en los indien mogelijk het probleem op.
1. Voer de test opnieuw uit totdat de bewerking is voltooid.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](methods-for-assigning-users-and-groups.md)
- [Automatisch inrichten van gebruikers accounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
