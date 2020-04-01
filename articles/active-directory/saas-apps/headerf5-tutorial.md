---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met F5 | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 352f52a2a6d84d352bb46e09f104efde303307f5
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478047"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met F5

In deze zelfstudie leert u hoe u F5 integreert met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot F5.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij F5 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Enkele aanmelding](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor toepassingen in Azure Active Directory voor meer informatie over de integratie van de SaaS-app met eenmalige aanmelding in Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)

* F5 single sign-on (SSO) ingeschakeld abonnement.

* Voor het implementeren van de gezamenlijke oplossing is de volgende licentie vereist:

    * F5 BIG-IP® Beste bundel (of) 

    * Zelfstandige licentie F5 BIG-IP Access Policy Manager™ (APM) 

    * F5 BIG-IP Access Policy Manager™ (APM) add-on licentie op een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).

    * Naast de bovenstaande licentie kan het F5-systeem ook worden gelicentieerd met: 

        * Een URL-filteringsabonnement om de URL-categoriedatabase te gebruiken

        * Een F5 IP Intelligence-abonnement om bekende aanvallers en kwaadaardig verkeer te detecteren en te blokkeren
     
        * Een netwerkhardwarebeveiligingsmodule (HSM) om digitale sleutels te beveiligen en te beheren voor sterke verificatie

* F5 BIG-IP-systeem is ingericht met APM-modules (LTM is optioneel)

* Hoewel optioneel, is het ten zeerste aanbevolen om de F5-systemen te implementeren in een [synchronisatie/failover-apparaatgroep](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F-installatie), die het actieve stand-by-paar omvat, met een zwevend IP-adres voor hoge beschikbaarheid (HA). Verdere interfaceredundantie kan worden bereikt met behulp van het Link Aggregation Control Protocol (LACP). LACP beheert de verbonden fysieke interfaces als één virtuele interface (geaggregeerde groep) en detecteert eventuele interfacefouten binnen de groep.

* Voor Kerberos-toepassingen is een on-premises AD-serviceaccount voor beperkte delegatie.  Raadpleeg [F5-documentatie](https://support.f5.com/csp/article/K43063049) voor het maken van een AD-delegatieaccount.

## <a name="access-guided-configuration"></a>Toegang tot begeleide configuratie

* Access guided configuration' wordt ondersteund op F5 TMOS versie 13.1.0.8 en hoger. Als uw BIG-IP-systeem een versie onder 13.1.0.8 uitvoert, raadpleegt u de sectie **Geavanceerde configuratie.**

* Access guided configuration biedt een compleet nieuwe en gestroomlijnde gebruikerservaring. Deze workflowgebaseerde architectuur biedt intuïtieve, opnieuw toetrederconfiguratiestappen die zijn afgestemd op de geselecteerde topologie.

* Voordat u overgaat tot de configuratie, upgradet u de beheerde configuratie door het nieuwste use case pack van [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748)te downloaden. Om te upgraden, volg de onderstaande procedure.

    >[!NOTE]
    >De screenshots hieronder zijn voor de nieuwste versie (BIG-IP 15.0 met AGC versie 5.0). De onderstaande configuratiestappen zijn geldig voor deze use case van 13.1.0.8 tot de nieuwste BIG-IP-versie.

1. Klik op de F5 BIG-IP Web UI op **Access >> Guide Configuration**.

1. Klik op de pagina **Begeleide configuratie** op **Begeleide configuratie** op de linkerbovenhoek.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure14.png) 

1. Selecteer op het popscherm Configuratie van de upgradegids de optie **Bestand kiezen** om het gedownloade use casepack te uploaden en klik op de knop **Uploaden en installeren.**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure15.png) 

1. Wanneer de upgrade is voltooid, klikt u op **Doorgaan.**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* F5 SSO kan op drie verschillende manieren worden geconfigureerd.

- [F5-aanmelding configureren voor toepassing op basis van kopteksten](#configure-f5-single-sign-on-for-header-based-application)

- [F5-aanmelding configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [F5-aanmelding configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Sleutelverificatiescenario's

* Naast Azure Active Directory native integration support voor moderne authenticatieprotocollen zoals Open ID Connect, SAML en WS-Fed, breidt F5 veilige toegang uit voor legacy-gebaseerde authenticatie-apps voor zowel interne als externe toegang met Azure AD, waardoor moderne scenario's (bijvoorbeeld toegang zonder wachtwoord) tot deze toepassingen mogelijk zijn. Dit omvat:

* Verificatie-apps op basis van kopteksten

* Kerberos-verificatie-apps

* Anonieme verificatie of geen ingebouwde verificatie-apps

* NTLM-verificatie-apps (beveiliging met dubbele prompts voor de gebruiker)

* Formulieren gebaseerde toepassing (bescherming met dubbele prompts voor de gebruiker)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 in Azure AD wilt configureren, moet u F5 uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **F5** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **F5** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Azure AD-aanmelding voor F5 configureren en testen

Azure AD SSO configureren en testen met F5 met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in F5.

Als u Azure AD SSO met F5 wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[F5 SSO configureren](#configure-f5-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[F5-testgebruiker maken](#create-f5-test-user)** - om een tegenhanger van B.Simon in F5 te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **F5-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [f5 client support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** and Certificate **(Base64)** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **F5 instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot F5.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **F5**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**
1. Klik op **voorwaardelijke toegang** .
1. Klik op **Nieuw beleid**.
1. U uw F5-app nu zien als een bron voor CA-beleid en voorwaardelijke toegang toepassen, waaronder Multifactor Auth, apparaatgebaseerd toegangscontrole of identiteitsbeveiligingsbeleid.

## <a name="configure-f5-sso"></a>F5 SSO configureren

- [F5-aanmelding configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [F5-aanmelding configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>F5-aanmelding configureren voor toepassing op basis van kopteksten

### <a name="guided-configuration"></a>Begeleide configuratie

1. Open een nieuw webbrowservenster en meld u aan bij uw F5-bedrijfssite (Header Based) als beheerder en voer de volgende stappen uit:

1. Navigeer naar **systeembeheer > certificaatbeheer > verkeerscertificaatbeheer > SSL-certificaatlijst**. Selecteer **Importeren** vanuit de rechterhoek. Geef een **certificaatnaam op** (wordt later in de config genoemd). Selecteer In de **certificaatbron**de optie Bestand uploaden geef het certificaat op dat is gedownload van Azure terwijl u SAML-eenmalig aanmelding configureert. Klik **op Importeren**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure12.png)
 
1. Daarnaast hebt u **SSL-certificaat nodig voor de hostnaam van toepassing. Navigeer naar de lijst van systeembeheer >-certificaatbeheer > verkeerscertificaatbeheer > SSL-certificaat**. Selecteer **Importeren** vanuit de rechterhoek. **Importtype** is **PKCS 12(IIS)**. Geef een **sleutelnaam** op (wordt later in de config verwezen) en geef het PFX-bestand op. Geef het **wachtwoord** voor de PFX op. Klik **op Importeren**.

    >[!NOTE]
    >In het voorbeeld dat `Headerapp.superdemo.live`onze app naam is, gebruiken `WildCard-SuperDemo.live`we een Wild Card Certificate onze sleutelnaam is .

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure13.png)

1. We gebruiken de begeleide ervaring om de Azure AD Federation en Application Access in te stellen. Ga naar – F5 BIG-IP **Main** en selecteer **Access > Guided Configuration > Federation > SAML Service Provider**. Klik **op Volgende** en klik op **Volgende** om de configuratie te starten.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure01.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure02.png)
 
1. Geef een **configuratienaam op**. Geef de **entiteits-id** op (hetzelfde als wat u hebt geconfigureerd in de configuratie van de Azure AD-toepassingsconfiguratie). Geef de **hostnaam**op . Voeg een **beschrijving toe** voor referentie. Accepteer de resterende standaarditems en selecteer en klik op **Opslaan & Volgende**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure03.png) 

1. In dit voorbeeld maken we een nieuwe Virtuele Server als 192.168.30.20 met poort 443. Geef het IP-adres van de virtuele server op in het **doeladres**. Selecteer het **SSL-profiel**van client , selecteer Nieuw maken. Geef eerder geüpload aanvraagcertificaat (het wildcardcertificaat in dit voorbeeld) en de bijbehorende sleutel op en klik vervolgens op **Opslaan & Volgende**.

    >[!NOTE]
    >in dit voorbeeld draait onze Interne webserver op poort 888 en we willen deze publiceren met 443.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure04.png) 

1. Klik onder **Methode Selecteren om uw IdP-connector te configureren**, Geef Metagegevens op, klik op Bestand kiezen en upload het XML-bestand met metagegevens dat eerder is gedownload van Azure AD. Geef een unieke **naam** op voor saml IDP-connector. Kies het **certificaat voor het ondertekenen met met gegevens** dat eerder is geüpload. Klik **op Opslaan & volgende**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure05.png)
 
1. Geef **onder Een groep**selecteren op Nieuw **maken** op (selecteer ook een groep die al bestaat). Laat andere waarde standaard zijn. Typ onder Groepservers het IP-adres onder **IP-adres/knooppuntnaam**. Geef de **poort op**. Klik **op Opslaan & volgende**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure06.png)

1. Selecteer op het scherm Instellingen voor eenmalig aanmelden de optie **Eenmalig aanmelden inschakelen**. Kies onder Geselecteerd eenmalig aanmeldingstype **HTTP-header-based**. Plaats **session.saml.last.Identity** door **session.saml.last.attr.name.Identity** onder Gebruikersnaambron (deze variabele die is ingesteld met behulp van claimtoewijzing in het Azure AD ). Onder SSO-headers.

    * **HeaderName: MyAuthorization**

    * **Koptekstwaarde : %{session.saml.last.attr.name.Identity}**

    * Klik **op Volgende & opslaan**

    Verwijs bijlage voor de volledige lijst van variabelen en waarden. U naar behoefte meer kopteksten toevoegen.

    >[!NOTE]
    >Accountnaam is het F5-delegatieaccount dat is gemaakt (F5-documentatie controleren).

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure07.png) 

1. Voor deze richtlijnen slaan we eindpuntcontroles over.  Raadpleeg F5-documentatie voor meer informatie. Selecteer **& volgende opslaan**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure08.png)

1. Accepteer de standaardinstellingen en klik op **Volgende opslaan &**. Raadpleeg F5-documentatie voor meer informatie over de instellingen voor saml-sessiebeheer.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure09.png)

1. Bekijk het overzichtsscherm en selecteer **Implementeren** om het BIG-IP te configureren. klik op **Voltooien**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure10.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Geavanceerde configuratie

Deze sectie is bedoeld om te worden gebruikt als u de configuratie Begeleid of extra parameters niet gebruiken of extra parameters wilt toevoegen/wijzigen. U hebt een TLS/SSL-certificaat nodig voor de hostnaam van de toepassing.

1. Navigeer naar **systeembeheer > certificaatbeheer > verkeerscertificaatbeheer > SSL-certificaatlijst**. Selecteer **Importeren** vanuit de rechterhoek. **Importtype** is **PKCS 12(IIS)**. Geef een **sleutelnaam** op (wordt later in de config verwezen) en geef het PFX-bestand op. Geef het **wachtwoord** voor de PFX op. Klik **op Importeren**.

    >[!NOTE]
    >In het voorbeeld dat `Headerapp.superdemo.live`onze app naam is, gebruiken `WildCard-SuperDemo.live`we een Wild Card Certificate onze sleutelnaam is .
  
    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Een nieuwe webserver toevoegen aan BigIP-F5

1. Klik op **Hoofd> IApps > Application Services > toepassing > maken**.

1. Geef de **naam** op en kies onder **Sjabloon** **f5.http**.
 
    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure18.png)

1. We zullen onze HeaderApp2 extern publiceren als HTTPS in dit geval, **hoe moet het BIG-IP-systeem omgaan met SSL-verkeer?** we specificeren **Ssl beëindigen van client, Plaintext naar servers (SSL Offload).** Geef uw certificaat en sleutel op onder **Welk SSL-certificaat wilt u gebruiken en** welke **SSL-privésleutel wilt u gebruiken?**. Geef het IP-adres van de virtuele server op onder **Welk IP-adres wilt u gebruiken voor de virtuele server?**. 

    * **Andere details opgeven**

        * FQDN  

        * Geef het afsluiten van app-groep op of maak een nieuwe.

        * Als u een nieuwe appserver maakt, geeft u **een intern IP-adres** en **poortnummer op.**

        ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure19.png) 

1. Klik **op Voltooid**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure20.png) 

1. Zorg ervoor dat de app-eigenschappen kunnen worden gewijzigd. Klik **op Hoofd> IApps > Application Services: Toepassingen >> HeaderApp2**. Schakel **strikte updates uit** (we wijzigen een bepaalde instelling buiten de GUI). Klik **op knop Bijwerken.**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure21.png) 

1. Op dit punt moet je in staat zijn om de virtuele server te bladeren.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>F5 configureren als SP en Azure als IDP

1.  Klik **op Access > Federation> SAML Service Provider > Lokale SP-service > klik op maken of + ondertekenen.**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure22.png)

1. Details opgeven voor de Service Provider Service. Geef **een naam** op die F5 SP-configuratie vertegenwoordigt. **Entiteits-id** opgeven (over het algemeen hetzelfde als de URL van de toepassing).

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure23.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure24.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure25.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure26.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure27.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Idp-connector maken

1. Klik **op De idp-connectors binden/ontbinden,** selecteer **Nieuwe IdP-connector maken** en kies Uit **metagegevens,** en voer vervolgens de volgende stappen uit:
 
    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure29.png)

    a. Blader naar het bestand metadata.xml dat is gedownload van Azure AD en geef een **naam van de identiteitsprovider**op.

    b. Klik **op ok**.

    c. De connector wordt gemaakt en het certificaat is automatisch gereed vanuit het xml-bestand met metagegevens.
    
    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure30.png)

    d. Configureer F5BIG-IP om alle aanvragen naar Azure AD te verzenden.

    e. Klik **op Nieuwe rij toevoegen**, kies **AzureIDP** (zoals gemaakt in eerdere stappen, op te geven 

    f. **Overeenkomende bron = %{session.server.landinguri}** 

    g. **Overeenkomende waarde = /***

    h. Klik op **bijwerken**

    i. Klik **op OK**

    j. **SAML IDP-installatie is voltooid**
    
    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>F5-beleid configureren om gebruikers door te verwijzen naar Azure SAML IDP

1. Voer de volgende stappen uit om F5-beleid te configureren om gebruikers door te verwijzen naar Azure SAML IDP:

    a. Klik **op Hoofd> Toegang > Profiel/Beleid > Toegangsprofielen**.

    b. Klik op de knop **Maken.**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure32.png)
 
    c. **Geef naam** op (HeaderAppAzureSAMLPolicy in het voorbeeld).

    d. U andere instellingen aanpassen, verwijzen wij u naar F5-documentatie.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure33.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure34.png) 

    e. Klik **op Voltooid**.

    f. Zodra de beleidscreatie is voltooid, klikt u op het beleid en gaat u naar het tabblad **Toegangsbeleid.**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure35.png)
 
    g. Klik op de **koppeling Visual Policy,** bewerken **Toegangsbeleid voor profiel.**

    h. Klik op de + Teken in de visual policy editor en kies **SAML Auth**.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure36.png)

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure37.png)
 
    i. Klik **op Item toevoegen**.

    j. Klik **onder Eigenschappen** op **Naam** op en selecteer onder **AAA Server** de eerder geconfigureerde SP op **OPSLAAN**.
 
    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure38.png)

    k. Het basisbeleid is klaar, u het beleid aanpassen om extra bronnen/attribuutopslag op te nemen.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure39.png)
 
    l. Zorg ervoor dat u bovenaan op de koppeling **Toegangsbeleid toepassen** klikt.

### <a name="apply-access-profile-to-the-virtual-server"></a>Toegangsprofiel toepassen op de virtuele server

1. Wijs het toegangsprofiel toe aan de virtuele server om F5 BIG-IP APM toe te passen op binnenkomend verkeer en het eerder gedefinieerde toegangsbeleid uit te voeren.

    a. Klik **op Virtuele** > **hoofdservers****voor lokaal verkeer** > .

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure40.png)
 
    b. Klik op de virtuele server, schuif naar de sectie **Toegangsbeleid** in de vervolgkeuzelijst **Toegangsprofiel** en selecteer het SAML-beleid dat is gemaakt (in het voorbeeld HeaderAppAzureSAMLPolicy)

    c. Klik op **bijwerken**
 
    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure41.png)

    d. maak een F5 BIG-IP iRule® om de aangepaste SAML-kenmerken uit de binnenkomende bewering te halen en ze door te geven als HTTP-headers naar de backendtesttoepassing. Klik **op Hoofd > Lokaal verkeer > iRules > iRule List > klik op maken**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure42.png)
 
    e. Plak de f5 BIG-IP iRule-tekst hieronder in het venster Definitie.

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure43.png)
 
    wanneer RULE_INIT { stel statisch::debug 0 } wanneer ACCESS_ACL_ALLOWED {

    stel AZUREAD_USERNAME [ACCESS::sessiegegevens krijgenhttp://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"session.saml.last.attr.name. "] als { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } als { !( [HTTP::header bestaat "AZUREAD_USERNAME"]) } { HTTP::koptekst invoegen "AZUREAD_USERNAME" $AZUREAD_GEBRUIKERSNAAM }

    stel AZUREAD_DISPLAYNAME [ACCESS::sessiegegevens krijgenhttp://schemas.microsoft.com/identity/claims/displayname"session.saml.last.attr.name. "] als { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } als { !( [HTTP::header bestaat "AZUREAD_DISPLAYNAME"]) } { HTTP::koptekst invoegen "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    stel AZUREAD_EMAILADDRESS [ACCESS::sessiegegevens krijgenhttp://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"session.saml.last.attr.name. "] als { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } als { !( [HTTP::header bestaat "AZUREAD_EMAILADDRESS"]) } { HTTP::koptekst invoegen "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Voorbeelduitvoer hieronder**

    ![F5-configuratie (Op koptekstgebaseerd)](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>F5-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in F5. Werk samen met [f5-ondersteuningsteam](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen aan het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de F5-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de F5 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer F5 met Azure AD](https://aad.portal.azure.com/)

- [F5-aanmelding configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [F5-aanmelding configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

