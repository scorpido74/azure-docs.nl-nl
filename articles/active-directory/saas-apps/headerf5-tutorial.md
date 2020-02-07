---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met F5 | Microsoft Docs'
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
ms.openlocfilehash: 7ad6b7150a43a286a4bec39a0482e08f50d95c06
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048063"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met F5

In deze zelf studie leert u hoe u F5 kunt integreren met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot F5.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij F5 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [eenmalige aanmelding bij toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van SaaS-apps met eenmalige aanmelding in azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.

* F5-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

* Voor de implementatie van de gezamenlijke oplossing is de volgende licentie vereist:

    * F5 BIG-IP-® beste bundel (of) 

    * F5 BIG-IP Access Policy Manager™ (APM) zelfstandige licentie 

    * F5 BIG-IP Access Policy Manager™ (APM)-invoeg toepassing voor een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).

    * Naast de bovenstaande licentie kan het F5-systeem ook worden gelicentieerd met: 

        * Een URL-filter abonnement voor het gebruik van de URL-categorie database

        * Een F5 IP Intelligence-abonnement voor het detecteren en blok keren van bekende aanvallers en schadelijk verkeer
     
        * Een HSM (Hardware Security module) voor het beveiligen en beheren van digitale sleutels voor sterke verificatie

* F5 BIG-IP-systeem is ingericht met APM-modules (LTM is optioneel)

* Hoewel dit optioneel is, is het raadzaam om de F5-systemen te implementeren in een [groep voor synchronisatie/failover-apparaten](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), die het actieve stand-by-paar omvat, met een zwevend IP-adres voor hoge beschik BAARHEID (ha). U kunt verdere interface redundantie bereiken met behulp van het Link Aggregation Control Protocol (LACP). LACP beheert de verbonden fysieke interfaces als één virtuele interface (statistische groep) en detecteert interface fouten binnen de groep.

* Voor Kerberos-toepassingen, een on-premises AD-service account voor beperkte delegering.  Raadpleeg de [F5-documentatie](https://support.f5.com/csp/article/K43063049) voor het maken van een AD delegering-account.

## <a name="access-guided-configuration"></a>Begeleide configuratie voor toegang

* De begeleide configuratie voor toegang wordt ondersteund op F5 TMOS-versie 13.1.0.8 en hoger. Als op uw BIG-IP-systeem een versie onder 13.1.0.8 wordt uitgevoerd, raadpleegt u de sectie **Geavanceerde configuratie** .

* De configuratie van de begeleide toegang biedt een volledig nieuwe en gestroomlijnde gebruikers ervaring. Deze architectuur op basis van een werk stroom biedt intuïtieve, herhaalde configuratie stappen die zijn afgestemd op de geselecteerde topologie.

* Voordat u doorgaat met de configuratie, moet u de begeleide configuratie bijwerken door het meest recente use-case Pack te downloaden van [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Volg de onderstaande procedure om een upgrade uit te voeren.

    >[!NOTE]
    >De onderstaande scherm afbeeldingen zijn voor de meest recente versie (BIG-IP 15,0 met AGC versie 5,0). De onderstaande configuratie stappen zijn geldig voor deze use case in van 13.1.0.8 tot de nieuwste BIG-IP-versie.

1. Klik in de F5 BIG-IP Web UI op **toegang > > Guide-configuratie**.

1. Klik op de pagina **begeleide configuratie** op **begeleide configuratie bijwerken** in de linkerbovenhoek.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure14.png) 

1. Selecteer in het pop-upvenster configuratie van upgrade gids de **optie bestand kiezen** om het gedownloade use-case Pack te uploaden en klik op de knop **uploaden en installeren** .

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure15.png) 

1. Wanneer de upgrade is voltooid, klikt u op de knop **door gaan** .

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* F5 SSO kan op drie verschillende manieren worden geconfigureerd.

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](#configure-f5-single-sign-on-for-header-based-application)

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenario's voor sleutel verificatie

* Naast de ondersteuning van Azure Active Directory systeem eigen integratie voor moderne verificatie protocollen, zoals Open ID Connect, SAML en WS-voeder, breidt F5 beveiligde toegang uit voor op verouderde verificatie-apps voor zowel interne als externe toegang met Azure AD, waardoor moderne scenario's (zoals wacht woord-minder toegang) voor deze toepassingen. Dit zijn onder andere:

* Op headers gebaseerde verificatie-apps

* Kerberos-verificatie-apps

* Anonieme verificatie of geen ingebouwde verificatie-apps

* NTLM-verificatie-apps (beveiliging met dubbele prompts voor de gebruiker)

* Op formulieren gebaseerde toepassing (beveiliging met dubbele prompts voor de gebruiker)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 wilt configureren in azure AD, moet u F5 toevoegen vanuit de galerie naar uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **F5** in het zoekvak.
1. Selecteer **F5** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Eenmalige aanmelding voor Azure AD voor F5 configureren en testen

Azure AD SSO met F5 configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker op F5.

Als u Azure AD SSO met F5 wilt configureren en testen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[F5 SSO configureren](#configure-f5-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een gebruiker van F5 test](#create-f5-test-user)** : als u een tegen hanger wilt hebben van B. Simon in F5 dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **F5** -toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) van de client om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en het **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **F5 instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan F5.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **F5**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.
1. Klik op **voorwaardelijke toegang** .
1. Klik op **Nieuw beleid**.
1. U kunt nu uw F5-app zien als een resource voor CA-beleid en voorwaardelijke toegang Toep assen, waaronder multi-factor Authentication, Toegangs beheer op basis van apparaten of beleid voor identiteits beveiliging.

## <a name="configure-f5-sso"></a>F5 SSO configureren

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst

### <a name="guided-configuration"></a>Begeleide configuratie

1. Open een nieuw webbrowser venster en meld u aan bij uw bedrijfs site van F5 (op basis van de koptekst) als beheerder en voer de volgende stappen uit:

1. Navigeer naar **System > Certificate management > Traffic Certificate management > SSL Certificate List**. Selecteer **importeren** in de rechter bovenhoek. Geef een **certificaat naam** op (later in de configuratie waarnaar wordt verwezen). Selecteer in de bron van het **certificaat**uploaden bestand Geef het certificaat op dat u van Azure hebt gedownload bij het configureren van eenmalige aanmelding voor SAML. Klik op **Import**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure12.png)
 
1. Daarnaast hebt u **een SSL-certificaat voor de hostnaam van de toepassing nodig. Navigeer naar System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **importeren** in de rechter bovenhoek. Het **import type** is **PKCS 12 (IIS)** . Geef een **sleutel naam** op (hiernaar wordt verwezen verderop in de configuratie) en geef het pfx-bestand op. Geef het **wacht woord** voor de pfx op. Klik op **Import**.

    >[!NOTE]
    >In het voor beeld is de naam van de app `Headerapp.superdemo.live`, we gebruiken een Joker certificaat met de naam `WildCard-SuperDemo.live`.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure13.png)

1. We gebruiken de begeleide ervaring voor het instellen van de Azure AD-Federatie en de toegang tot toepassingen. Ga naar – F5 BIG-IP **Main** en selecteer **toegang > begeleide configuratie > Federatie > SAML-service provider**. Klik op **volgende** en klik vervolgens op **volgende** om te beginnen met de configuratie.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure01.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure02.png)
 
1. Geef een **naam**op voor de configuratie. Geef de **entiteit-id** op (hetzelfde als wat u hebt geconfigureerd op de configuratie van de Azure AD-toepassing). Geef de **hostnaam**op. Voeg een **Beschrijving** voor de verwijzing toe. Accepteer de overige standaard vermeldingen en selecteer en klik vervolgens op **opslaan & volgende**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure03.png) 

1. In dit voor beeld maken we een nieuwe virtuele server als 192.168.30.20 met poort 443. Geef het IP-adres van de virtuele server op in het **doel adres**. Selecteer het client- **SSL-profiel**, selecteer nieuwe maken. Geef eerder geüpload toepassings certificaat op (het Joker certificaat in dit voor beeld) en de bijbehorende sleutel, en klik vervolgens op **& volgende opslaan**.

    >[!NOTE]
    >in dit voor beeld wordt onze interne webserver uitgevoerd op poort 888 en willen we deze publiceren met 443.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure04.png) 

1. Geef bij **methode selecteren om uw IDP-connector te configureren de**meta gegevens op, klik op bestand kiezen en upload het XML-bestand met meta gegevens dat u eerder hebt gedownload van Azure AD. Geef een unieke **naam** op voor de SAML IDP-connector. Kies het **handtekening certificaat voor meta gegevens** dat eerder is geüpload. Klik op **opslaan & volgende**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure05.png)
 
1. Geef onder **een pool selecteren de optie** **Nieuw maken** op (Selecteer eventueel een pool die al bestaat). Geef een andere waarde op als standaard. Onder pool servers typt u het IP-adres onder **IP-adres/knooppunt naam**. Geef de **poort**op. Klik op **opslaan & volgende**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure06.png)

1. Selecteer op het scherm instellingen voor eenmalige aanmelding de optie **eenmalige aanmelding inschakelen**. Kies onder geselecteerd type eenmalige aanmelding de optie **http-header**. Vervang **sessie. SAML. Last. Identity** met **Session. SAML. Last. attr. name. Identity** onder username source (deze variabele is ingesteld met behulp van claim toewijzing in azure AD). Onder SSO-headers.

    * **Kopnaam: MyAuthorization**

    * **Header waarde:% {Session. SAML. Last. attr. name. Identity}**

    * Klik op **opslaan & volgende**

    Raadpleeg de bijlage voor een volledige lijst met variabelen en waarden. U kunt indien nodig meer headers toevoegen.

    >[!NOTE]
    >Account naam is het account voor F5 delegering dat is gemaakt (Raadpleeg de F5-documentatie).

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure07.png) 

1. In het kader van deze richt lijnen worden de eindpunt controles overs Laan.  Raadpleeg de documentatie van F5 voor meer informatie. Selecteer **& volgende opslaan**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure08.png)

1. Accepteer de standaard instellingen en klik op **& volgende opslaan**. Raadpleeg de F5-documentatie voor meer informatie over de instellingen voor SAML-sessie beheer.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure09.png)

1. Bekijk het scherm samen vatting en selecteer **implementeren** om het Big-IP-adres te configureren. Klik op **volt ooien**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure10.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Geavanceerde configuratie

Deze sectie is bedoeld om te worden gebruikt als u de begeleide configuratie niet kunt gebruiken of als u extra para meters wilt toevoegen of wijzigen. U hebt een SSL-certificaat voor de hostnaam van de toepassing nodig.

1. Navigeer naar **System > Certificate management > Traffic Certificate management > SSL Certificate List**. Selecteer **importeren** in de rechter bovenhoek. Het **import type** is **PKCS 12 (IIS)** . Geef een **sleutel naam** op (hiernaar wordt verwezen verderop in de configuratie) en geef het pfx-bestand op. Geef het **wacht woord** voor de pfx op. Klik op **Import**.

    >[!NOTE]
    >In het voor beeld is de naam van de app `Headerapp.superdemo.live`, we gebruiken een Joker certificaat met de naam `WildCard-SuperDemo.live`.
  
    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Een nieuwe webserver toevoegen aan BigIP-F5

1. Klik op **Main > IApps > Toepassingsservices > toepassing > maken**.

1. Geef de **naam** op en kies onder **sjabloon** **F5. http**.
 
    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure18.png)

1. We zullen onze HeaderApp2 extern publiceren als HTTPS in dit geval, **Hoe moet het Big-IP-systeem SSL-verkeer afhandelen**? we opgeven **SSL beëindigen van client, lees bare tekst naar servers (SSL-offload)** . Geef uw certificaat en sleutel op waaronder het SSL-certificaat wilt gebruiken? en **welke SSL-persoonlijke sleutel wilt u gebruiken?** . Geef het IP-adres van de virtuele server op onder **welke IP-adressen wilt u gebruiken voor de virtuele server?** . 

    * **Andere details opgeven**

        * FQDN-NAAM  

        * Geef de groep voor het afsluiten van de app op of maak een nieuwe.

        * Als u een nieuwe app-server maakt, geeft u een **intern IP-adres** en **poort nummer**op.

        ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure19.png) 

1. Klik op **voltooid**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure20.png) 

1. Zorg ervoor dat de app-eigenschappen kunnen worden gewijzigd. Klik op **Main > IApps > toepassingsservices: toepassingen > > HeaderApp2**. Schakel **strikte updates** uit (er wordt een aantal instellingen buiten de gebruikers interface gewijzigd). Klik op de knop **bijwerken** .

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure21.png) 

1. Op dit moment moet u de virtuele server kunnen doorzoeken.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>F5 als SP en Azure configureren als IDP

1.  Klik op **toegang > Federation > SAML-Service Provider > lokale SP-service > op maken of + ondertekenen**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure22.png)

1. Geef details op voor de service provider service. Geef de **naam** op die staat voor een F5 SP-configuratie. Geef de **entiteit-id** op (meestal hetzelfde als de URL van de toepassing).

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure23.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure24.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure25.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure26.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure27.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>IDP-connector maken

1. Klik op de knop **verbinden/losmaken IDP connectors** , selecteer **nieuwe IDP-connector maken** en kies uit de optie voor **meta gegevens** en voer vervolgens de volgende stappen uit:
 
    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure29.png)

    a. Blader naar het meta gegevensbestand. XML-bestand dat u hebt gedownload van Azure AD en geef een naam op voor de **ID-provider**.

    b. Klik op **OK**.

    c. De connector wordt gemaakt en het certificaat is automatisch gereed vanuit het XML-bestand met meta gegevens.
    
    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure30.png)

    d. Configureer F5BIG-IP om alle aanvragen te verzenden naar Azure AD.

    e. Klik op **nieuwe rij toevoegen**, kies **AzureIDP** (zoals gemaakt in de vorige stappen) 

    f. **Overeenkomende bron =% {Session. server. landinguri}** 

    g. **Overeenkomende waarde =/** *

    h. Klik op **bijwerken**

    i. Klik op **OK**

    j. **Setup van SAML IDP is voltooid**
    
    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>F5-beleid configureren om gebruikers om te leiden naar Azure SAML IDP

1. Voer de volgende stappen uit om F5-beleid te configureren om gebruikers om te leiden naar Azure SAML IDP:

    a. Klik op **Main > access > profiel/policies > Access Profiles**.

    b. Klik op de knop **maken** .

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure32.png)
 
    c. Geef een **naam** op (HeaderAppAzureSAMLPolicy in het voor beeld).

    d. U kunt andere instellingen aanpassen Raadpleeg de F5-documentatie.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure33.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure34.png) 

    e. Klik op **voltooid**.

    f. Nadat het beleid is gemaakt, klikt u op het beleid en gaat u naar het tabblad **toegangs beleid** .

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure35.png)
 
    g. Klik op **Editor voor visuele beleids regels**, **toegangs beleid voor profiel** koppeling bewerken.

    h. Klik op het plus teken in de editor voor visuele beleids regels en kies **SAML auth**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure36.png)

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure37.png)
 
    i. Klik op **item toevoegen**.

    j. Onder **Eigenschappen** **naam** opgeven en onder **AAA server** de eerder geconfigureerde SP selecteren, klikt u op **Opslaan**.
 
    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure38.png)

    k. Het basis beleid is gereed. u kunt het beleid aanpassen om extra bronnen/kenmerk archieven op te nemen.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure39.png)
 
    l. Zorg ervoor dat u op de koppeling **toegangs beleid Toep assen** klikt.

### <a name="apply-access-profile-to-the-virtual-server"></a>Toegangs profiel Toep assen op de virtuele server

1. Wijs het toegangs profiel toe aan de virtuele server om F5 BIG-IP APM te gebruiken om de profiel instellingen toe te passen op binnenkomend verkeer en voer het eerder gedefinieerde toegangs beleid uit.

    a. Klik op **hoofd** > **lokaal verkeer** > **virtuele servers**.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure40.png)
 
    b. Klik op de virtuele server, blader naar het gedeelte **toegangs beleid** , in de vervolg keuzelijst voor het **toegangs profiel** en selecteer het SAML-beleid dat is gemaakt (in het voor beeld HeaderAppAzureSAMLPolicy)

    c. Klik op **bijwerken**
 
    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure41.png)

    d. Maak een F5-iRule® voor het extra heren van de aangepaste SAML-kenmerken van de binnenkomende bevestiging en geef ze door als HTTP-headers voor de test toepassing voor de back-end. Klik op **hoofd > lokaal verkeer > lijst iRules > iRule > Klik op maken**

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure42.png)
 
    e. Plak de F5 BIG-IP iRule-tekst hieronder in het definitie venster.

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure43.png)
 
    Wanneer RULE_INIT {set static::d ebug 0} wanneer ACCESS_ACL_ALLOWED {

    Stel AZUREAD_USERNAME [ACCESS:: session data Get ' session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if {$static::d ebug} {log local0. "AZUREAD_USERNAME = $AZUREAD _USERNAME"} als {! ( [HTTP:: header bestaat "AZUREAD_USERNAME"]) } {HTTP:: header Insert "AZUREAD_USERNAME" $AZUREAD _USERNAME}

    Stel AZUREAD_DISPLAYNAME [ACCESS:: session data Get ' session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if {$static::d ebug} {log local0. "AZUREAD_DISPLAYNAME = $AZUREAD _DISPLAYNAME"} als {! ( [HTTP:: header bestaat "AZUREAD_DISPLAYNAME"]) } {HTTP:: header Insert "AZUREAD_DISPLAYNAME" $AZUREAD _DISPLAYNAME}

    Stel AZUREAD_EMAILADDRESS [ACCESS:: session data Get ' session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if {$static::d ebug} {log local0. "AZUREAD_EMAILADDRESS = $AZUREAD _EMAILADDRESS"} als {! ( [HTTP:: header bestaat "AZUREAD_EMAILADDRESS"]) } {HTTP:: header Insert "AZUREAD_EMAILADDRESS" $AZUREAD _EMAILADDRESS}}

    **Voorbeeld uitvoer hieronder**

    ![F5-configuratie (op basis van koptekst)](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Een F5 test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in F5. Werk samen met het [ondersteunings team voor F5-clients](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen op het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de F5-tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de F5 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer F5 met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](kerbf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

