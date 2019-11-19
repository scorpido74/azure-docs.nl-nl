---
title: SSO-toepassingen voor eenmalige aanmelding-micro soft-identiteits platform | Microsoft Docs
description: Eenmalige aanmelding (SSO) configureren voor niet-galerie toepassingen in het micro soft Identity platform (Azure AD)
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
ms.openlocfilehash: 2dcc2d6fc252f288f15e2583012798b4d0e9cee6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169438"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Eenmalige aanmelding op basis van SAML configureren voor niet-galerie toepassingen

Wanneer u [een galerie-app](add-gallery-app.md) of een [niet-galerij web-app](add-non-gallery-app.md) toevoegt aan uw Azure AD-zakelijke toepassingen, is een van de opties voor eenmalige aanmelding op basis van het op [SAML gebaseerde eenmalige aanmelding](what-is-single-sign-on.md#saml-sso). Kies zo mogelijk SAML voor toepassingen die worden geverifieerd met een van de SAML-protocollen. Met eenmalige aanmelding via SAML verifieert Azure AD bij de toepassing met behulp van het Azure AD-account van de gebruiker. Azure AD communiceert de informatie aanmelding voor de toepassing via een verbindingsprotocol. U kunt gebruikers toewijzen aan specifieke toepassings rollen op basis van regels die u in uw SAML-claims definieert. In dit artikel wordt beschreven hoe u eenmalige aanmelding op basis van SAML kunt configureren voor een niet-galerij-app. 

> [!NOTE]
> Een galerie-app toevoegen? Stapsgewijze instructies voor het instellen van de installatie vindt u in de [lijst met zelf studies over SaaS-apps](../saas-apps/tutorial-list.md)

Als u eenmalige SAML-aanmelding wilt configureren voor een niet-galerie toepassing zonder code te schrijven, moet u een abonnement of Azure AD Premium hebben en moet de toepassing SAML 2,0 ondersteunen. Ga voor meer informatie over Azure AD-versies naar [Azure ad-prijzen](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Voordat u begint

Als de toepassing niet is toegevoegd aan uw Azure AD-Tenant, raadpleegt u [een niet-galerie-app toevoegen](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Stap 1. De basis-SAML-configuratie bewerken

1. Meld u bij [Azure Portal](https://portal.azure.com) aan als beheerder van de cloudtoepassing of als toepassingsbeheerder voor uw Azure Active Directory-tenant.

2. Ga naar **Azure Active Directory** > **bedrijfs toepassingen** en selecteer de toepassing in de lijst. 
   
   - Als u wilt zoeken naar de toepassing, selecteert u in het menu **toepassings type** **alle toepassingen**en selecteert u vervolgens **Toep assen**. Voer de naam van de toepassing in het zoekvak in en selecteer vervolgens de toepassing in de resultaten.

3. Selecteer onder de sectie **beheren** de optie **eenmalige aanmelding**. 

4. Selecteer **SAML**. De pagina **eenmalige aanmelding met SAML-preview instellen** wordt weer gegeven.

   ![Stap 1 de eenvoudige SAML-configuratie bewerken](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Als u de basis opties voor SAML-configuratie wilt bewerken, selecteert u het **bewerkings** pictogram (een potlood) in de rechter bovenhoek van de **basis-SAML-configuratie** sectie.

1. Voer de volgende instellingen in. U moet de waarden van de leverancier van de toepassing ophalen. U kunt de waarden hand matig invoeren of een meta gegevensbestand uploaden om de waarde van de velden te extra heren.

    | Basis configuratie-instelling voor SAML | SP-geïnitieerd | idP-geïnitieerd | Beschrijving |
    |:--|:--|:--|:--|
    | **Id (Entiteits-ID)** | Vereist voor sommige apps | Vereist voor sommige apps | Unieke identificatie van de toepassing. Azure Active Directory stuurt de id naar de toepassing als parameter van de doelgroep van het SAML-token. Er wordt verwacht dat de toepassing deze valideert. Deze waarde wordt ook weergegeven als de entiteit-ID in SAML-metagegevens die worden geleverd door de toepassing. Voer een URL in die gebruikmaakt van het volgende patroon: ' https://<subdomain>. contoso.com ' *u kunt deze waarde vinden als het element van de **Uitgever** in de **AuthnRequest** (SAML-aanvraag) die door de toepassing is verzonden.* |
    | **Antwoord-URL** | Vereist | Vereist | Hiermee geeft u op waar de toepassing verwacht het SAML-token te ontvangen. De antwoord-URL wordt ook de ACS-URL (Assertion Consumer Service) genoemd. U kunt de extra antwoord-URL velden gebruiken om meerdere antwoord-Url's op te geven. Stel dat u aanvullende antwoord-Url's nodig hebt voor meerdere subdomeinen. Voor test doeleinden kunt u meerdere antwoord-Url's (lokale host en open bare Url's) tegelijk opgeven. |
    | **Aanmeldings-URL** | Vereist | Niet opgeven | Wanneer een gebruiker deze URL opent, leidt de serviceprovider hem om naar Azure Active Directory om de gebruiker te verifiëren en aan te melden. Azure Active Directory maakt gebruik van de URL om de toepassing te starten vanuit Office 365 of het toegangsvenster van Azure Active Directory. Als deze leeg is, voert Azure AD een door IdP geïnitieerde aanmelding uit wanneer een gebruiker de toepassing start vanuit Office 365, het Azure AD-toegangs venster of de Azure AD SSO-URL.|
    | **Relay-status** | Optioneel | Optioneel | Hiermee geeft u aan de toepassing op waar de gebruiker naar moet worden omgeleid nadat de verificatie is voltooid. De waarde is doorgaans een geldige URL voor de toepassing. Sommige toepassingen gebruiken dit veld echter anders. Vraag de leverancier van de toepassing voor meer informatie.
    | **Afmeldings-URL** | Optioneel | Optioneel | Wordt gebruikt om de antwoorden op SAML-afmeldingen terug te sturen naar de toepassing.

Zie [het SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md)voor meer informatie.

## <a name="step-2-configure-user-attributes-and-claims"></a>Stap 2. Gebruikers kenmerken en claims configureren 

Wanneer een gebruiker zich verifieert bij de toepassing, geeft Azure AD aan de toepassing een SAML-token door met informatie (of claims) over de gebruiker die deze uniek identificeert. Standaard bevat deze informatie de gebruikers naam, het e-mail adres, de voor naam en de achternaam van de gebruiker. Mogelijk moet u deze claims aanpassen als de toepassing bijvoorbeeld specifieke claim waarden of een andere **naam** indeling dan username vereist. De vereisten voor galerij-apps worden beschreven in de [toepassingsspecifieke zelf studies](../saas-apps/tutorial-list.md), of u kunt de leverancier van de toepassing vragen. De algemene stappen voor het configureren van gebruikers kenmerken en claims worden hieronder beschreven.

1. Selecteer in de sectie **gebruikers kenmerken en claims** het **bewerkings** pictogram (een potlood) in de rechter bovenhoek.

   ![Stap 2 gebruikers kenmerken en claims configureren](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Controleer de **waarde**van de naam-id. De standaard waarde is *User. Principal*name. De gebruikers-id is uniek voor elke gebruiker in de toepassing. Als het e-mailadres bijvoorbeeld zowel de gebruikersnaam als de unieke id is, wordt de waarde ingesteld op *user.mail*.

3. Als u de **naam-id-waarde**wilt wijzigen, selecteert u het **bewerkings** pictogram (een potlood) voor het veld **naam-id** . Breng de gewenste wijzigingen aan in de id-indeling en-bron, indien nodig. Zie voor meer informatie [bewerken van NameID](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Sla de wijzigingen op wanneer u klaar bent. 
 
4. Als u groeps claims wilt configureren, selecteert u het **bewerkings** pictogram voor de groepen die zijn **geretourneerd in claim** veld. Zie [groeps claims configureren](../hybrid/how-to-connect-fed-group-claims.md)voor meer informatie.

5. Als u een claim wilt toevoegen, selecteert u **nieuwe claim toevoegen** boven aan de pagina. Voer de **naam** in en selecteer de juiste bron. Als u de **kenmerk** bron selecteert, moet u het **bron kenmerk** kiezen dat u wilt gebruiken. Als u de **Vertaal** bron selecteert, moet u de **trans formatie** en **para meter 1** kiezen die u wilt gebruiken. Zie voor meer informatie [toepassingsspecifieke claims toevoegen](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Sla de wijzigingen op wanneer u klaar bent. 

6. Selecteer **Opslaan**. De nieuwe claim wordt weer gegeven in de tabel.

   > [!NOTE]
   > Raadpleeg de volgende bronnen voor meer manieren om het SAML-token van Azure AD aan uw toepassing aan te passen.
   >- Zie [Configure Role claims](../develop/active-directory-enterprise-app-role-management.md)voor het maken van aangepaste rollen via de Azure Portal.
   >- Zie [claims aanpassen-Power shell](../develop/active-directory-claims-mapping.md)voor informatie over het aanpassen van claims via Power shell.
   >- Zie [optionele claims configureren](../develop/active-directory-optional-claims.md)om het toepassings manifest te wijzigen voor het configureren van optionele claims voor uw toepassing.
   >- Zie [token levensduur configureren](../develop/active-directory-configurable-token-lifetimes.md)voor meer informatie over het instellen van de token levensduur beleid voor vernieuwings tokens, toegangs tokens, sessie tokens en id-tokens. Als u verificatie sessies via voorwaardelijke toegang van Azure AD wilt beperken, raadpleegt u de [beheer mogelijkheden voor verificatie sessies](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Stap 3. Het SAML-handtekening certificaat beheren

Azure AD maakt gebruik van een certificaat voor het ondertekenen van de SAML-tokens die worden verzonden naar de toepassing. U hebt dit certificaat nodig om de vertrouwens relatie tussen Azure AD en de toepassing in te stellen. Zie de SAML-documentatie van de toepassing voor meer informatie over de indeling van het certificaat. Zie voor meer informatie [certificaten beheren voor federatieve eenmalige aanmelding](manage-certificates-for-federated-single-sign-on.md) en [Geavanceerde opties voor certificaat ondertekening in het SAML-token](certificate-signing-options.md).

Vanuit Azure AD kunt u het actieve certificaat in base64-of RAW-indeling rechtstreeks downloaden vanuit de hoofd **set eenmalige aanmelding met de SAML-** pagina. U kunt ook het actieve certificaat ophalen door het XML-bestand met meta gegevens van de toepassing te downloaden of door gebruik te maken van de URL voor de federatieve meta gegevens van de app. Als u uw certificaten (actief of inactief) wilt weer geven, maken of downloaden, voert u de volgende stappen uit.

1. Ga naar de sectie **SAML-handtekening certificaat** . 

   ![Stap 3 het SAML-handtekening certificaat beheren](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Controleer of het certificaat het volgende heeft:

   - *De gewenste verloop datum.* U kunt de verval datum Maxi maal drie jaar in de toekomst configureren.
   - *De status actief voor het gewenste certificaat.* Als de status niet **actief**is, wijzigt u de status in **actief**. Als u de status wilt wijzigen, klikt u met de rechter muisknop op de rij van het gewenste certificaat en selecteert **u certificaat actief maken**.
   - *De juiste handtekening optie en algoritme.*
   - *Het juiste e-mail adres (sen) van de melding.* Wanneer het actieve certificaat bijna de verloop datum heeft, stuurt Azure AD een melding naar het e-mail adres dat in dit veld is geconfigureerd.

2. Als u het certificaat wilt downloaden, selecteert u een van de opties voor Base64-indeling, RAW-indeling of federatieve meta gegevens-XML. Azure AD biedt ook de **URL voor de federatieve meta gegevens** van de app, waar u toegang kunt krijgen tot de meta gegevens die specifiek zijn voor de toepassing in de indeling `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Als u een certificaat wilt beheren, maken of importeren, selecteert u het **bewerkings** pictogram (een potlood) in de rechter bovenhoek van de sectie **SAML-handtekening certificaat** .

   ![SAML-handtekening certificaat](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Voer een van de volgende acties uit:

   - Als u een nieuw certificaat wilt maken, selecteert u **Nieuw certificaat**, selecteert u de **verval datum**en selecteert u **Opslaan**. Als u het certificaat wilt activeren, selecteert u het context menu ( **...** ) en selecteert **u certificaat actief maken**.
   - Als u een certificaat met persoonlijke sleutel-en pfx-referenties wilt uploaden, selecteert u **certificaat importeren** en bladert u naar het certificaat. Voer het **PFX-wacht woord**in en selecteer vervolgens **toevoegen**.  
   - Als u geavanceerde opties voor certificaat ondertekening wilt configureren, gebruikt u de volgende opties. Zie het artikel [Geavanceerde opties voor certificaat ondertekening](certificate-signing-options.md) voor een beschrijving van deze opties.
      - Kies in de vervolg keuzelijst **handtekening opties** de optie **SAML-respons ondertekenen**, **SAML-bevestiging ondertekenen**of **SAML-respons en-bevestiging ondertekenen**.
      - Kies in de vervolg keuzelijst **handtekening algoritme** **SHA-1** of **SHA-256**.
   - Geef de e-mail adressen in de velden **e-mail adres** van de melding op als u meer mensen wilt laten weten wanneer het actieve certificaat bijna is verlopen.

4. Als u wijzigingen hebt aangebracht, selecteert u **Opslaan** boven aan de sectie **SAML-handtekening certificaat** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Stap 4. De toepassing instellen voor gebruik van Azure AD

De sectie **\<toepassings naam instellen >** bevat de waarden die moeten worden geconfigureerd in de toepassing, zodat Azure AD wordt gebruikt als een SAML-ID-provider. De vereiste waarden variëren afhankelijk van de toepassing. Zie de SAML-documentatie van de toepassing voor meer informatie. Ga naar de sectie **\<toepassings naam instellen >** en selecteer **Stapsgewijze instructies weer geven**om de documentatie te vinden. De documentatie wordt weer gegeven op de pagina **aanmelden configureren** . Met deze pagina kunt u de waarden van **de aanmeldings-URL**, de **Azure ad-id**en de **afmeldings-URL** invullen in de **\<toepassings naam > kop instellen** .

1. Schuif omlaag naar de sectie **\<toepassingsnaam instellen >** . 
   
   ![Stap 4 de toepassing instellen](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Kopieer de waarde van elke rij in deze sectie naar wens en volg de toepassingsspecifieke instructies om de waarde toe te voegen aan de toepassing. Voor galerie-apps kunt u de documentatie weer geven door stapsgewijze **instructies weer geven**te selecteren. 
   - De waarden van de **aanmeldings-URL** en de **afmeldings-URL** worden beide omgezet naar hetzelfde eind punt. Dit is het SAML-eind punt voor aanvraag verwerking voor uw exemplaar van Azure AD. 
   - De **Azure ad-id** is de waarde van de **VERLENER** in het SAML-token dat is uitgegeven aan de toepassing.
2. Wanneer u alle waarden in de juiste velden hebt geplakt, selecteert u **Opslaan**.

## <a name="step-5-validate-single-sign-on"></a>Stap 5. Eenmalige aanmelding valideren

Nadat u uw toepassing hebt geconfigureerd voor het gebruik van Azure AD als id-provider op basis van SAML, kunt u de instellingen testen om te zien of eenmalige aanmelding werkt voor uw account. 

2. Schuif naar de sectie **eenmalige aanmelding valideren met <applicationName>** .

   ![Stap 5 eenmalige aanmelding valideren](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selecteer **Valideren**. De test-opties worden weergegeven.

4. Selecteer **Aanmelden als huidige gebruiker**. 

Als de aanmelding is geslaagd, kunt u gebruikers en groepen toewijzen aan uw SAML-toepassing.
Als er een fout bericht wordt weer gegeven, voert u de volgende stappen uit:

1. Kopieer en plak de gegevens in het vak **Hoe ziet de fout eruit?** .

    ![Hulp krijgen bij het oplossen van problemen](media/configure-single-sign-on-portal/error-guidance.png)

2. Selecteer **ondersteuning**voor het oplossen van oplossingen. De richtlijnen voor de oorzaak en de oplossing van het probleem worden weergegeven.  In dit voorbeeld was de gebruiker niet toegewezen aan de toepassing.

3. Lees de richt lijnen voor oplossingen en los indien mogelijk het probleem op.

4. Voer de test opnieuw uit totdat de bewerking is voltooid.

Zie voor meer informatie [debug op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](methods-for-assigning-users-and-groups.md)
- [Automatisch inrichten van gebruikers accounts configureren](configure-automatic-user-provisioning-portal.md)
