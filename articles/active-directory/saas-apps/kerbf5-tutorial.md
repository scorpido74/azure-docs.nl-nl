---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met F5 | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f06b0b5aa59328d2fe39d501cfdf3ad7524427
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75431457"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met F5

In deze zelf studie leert u hoe u F5 kunt integreren met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot F5.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij F5 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

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

1. Klik in de F5 BIG-IP-webgebruikersinterface op **toegang >> Guide-configuratie**.

2. Klik op de pagina **begeleide configuratie** op **begeleide configuratie bijwerken** in de linkerbovenhoek.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. Selecteer in het pop-upvenster configuratie van upgrade gids de **optie bestand kiezen** om het gedownloade use-case Pack te uploaden en klik op de knop **uploaden en installeren** .

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. Wanneer de upgrade is voltooid, klikt u op de knop **door gaan** .

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* F5 ondersteunt door **SP en IDP** GEÏNITIEERDe SSO
* F5 SSO kan op drie verschillende manieren worden geconfigureerd.

- [Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing](#configure-f5-single-sign-on-for-kerberos-application)

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](headerf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenario's voor sleutel verificatie

Afgezien van Azure Active Directory systeem eigen integratie ondersteuning voor moderne verificatie protocollen zoals Open ID Connect, SAML en WS-voeder, wordt met F5 beveiligde toegang uitgebreid voor op verouderde gebaseerde verificatie-apps voor zowel interne als externe toegang met Azure AD, waardoor moderne scenario's (zoals wacht woord-minder toegang) worden ingeschakeld voor deze toepassingen. Dit zijn onder andere:

* Op headers gebaseerde verificatie-apps

* Kerberos-verificatie-apps

* Anonieme verificatie of geen ingebouwde verificatie-apps

* NTLM-verificatie-apps (beveiliging met dubbele prompts voor de gebruiker)

* Op formulieren gebaseerde toepassing (beveiliging met dubbele prompts voor de gebruiker)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 wilt configureren in azure AD, moet u F5 toevoegen vanuit de galerie naar uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
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

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **F5** -toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) van de client om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en het **certificaat (base64)** en selecteer vervolgens **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **F5 instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

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
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .
1. Klik op **voorwaardelijke toegang** .
1. Klik op **Nieuw beleid**.
1. U kunt nu uw F5-app zien als een resource voor CA-beleid en voorwaardelijke toegang Toep assen, waaronder multi-factor Authentication, Toegangs beheer op basis van apparaten of beleid voor identiteits beveiliging.

## <a name="configure-f5-sso"></a>F5 SSO configureren

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](headerf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Eenmalige aanmelding voor F5 configureren voor Kerberos-toepassing

### <a name="guided-configuration"></a>Begeleide configuratie

1. Open een nieuw webbrowser venster en meld u als beheerder aan bij uw service-bedrijfs site van F5 (Kerberos) en voer de volgende stappen uit:

1. U moet het meta gegevens certificaat importeren in F5, dat later wordt gebruikt in het installatie proces.

1. Navigeer naar **System > Certificate management > Traffic Certificate management > SSL Certificate List**. Selecteer **importeren** in de rechter bovenhoek. Geef een **certificaat naam** op (later in de configuratie waarnaar wordt verwezen). Selecteer in de bron van het **certificaat**uploaden bestand Geef het certificaat op dat u van Azure hebt gedownload bij het configureren van eenmalige aanmelding voor SAML. Klik op **importeren**.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Daarnaast hebt u **een SSL-certificaat voor de hostnaam van de toepassing nodig. Navigeer naar System > Certificate Management > Traffic Certificate Management > SSL Certificate List**. Selecteer **importeren** in de rechter bovenhoek. Het **import type** is **PKCS 12 (IIS)**. Geef een **sleutel naam** op (hiernaar wordt verwezen verderop in de configuratie) en geef het pfx-bestand op. Geef het **wacht woord** voor de pfx op. Klik op **importeren**.

    >[!NOTE]
    >In het voor beeld van de naam `Kerbapp.superdemo.live`van de app gebruiken we een Joker certificaat met de naam`WildCard-SuperDemo.live`

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. We gebruiken de begeleide ervaring voor het instellen van de Azure AD-Federatie en de toegang tot toepassingen. Ga naar – F5 BIG-IP **Main** en selecteer **toegang > begeleide configuratie > Federatie > SAML-service provider**. Klik op **volgende** en klik vervolgens op **volgende** om te beginnen met de configuratie.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Geef een **naam**op voor de configuratie. Geef de **entiteit-id** op (hetzelfde als wat u hebt geconfigureerd op de configuratie van de Azure AD-toepassing). Geef de **hostnaam**op. Voeg een **Beschrijving** voor de verwijzing toe. Accepteer de overige standaard vermeldingen en selecteer en klik vervolgens op **opslaan & volgende**.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. In dit voor beeld maken we een nieuwe virtuele server als 192.168.30.200 met poort 443. Geef het IP-adres van de virtuele server op in het **doel adres**. Selecteer het client- **SSL-profiel**, selecteer nieuwe maken. Geef eerder geüpload toepassings certificaat op (het Joker certificaat in dit voor beeld) en de bijbehorende sleutel, en klik vervolgens op **& volgende opslaan**.

    >[!NOTE]
    >in dit voor beeld wordt onze interne webserver uitgevoerd op poort 80 en willen we deze publiceren met 443.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. Geef bij **methode selecteren om uw IDP-connector te configureren de**meta gegevens op, klik op bestand kiezen en upload het XML-bestand met meta gegevens dat u eerder hebt gedownload van Azure AD. Geef een unieke **naam** op voor de SAML IDP-connector. Kies het **handtekening certificaat voor meta gegevens** dat eerder is geüpload. Klik op **opslaan & volgende**.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. Geef onder **een pool selecteren de optie** **Nieuw maken** op (Selecteer eventueel een pool die al bestaat). Geef een andere waarde op als standaard. Onder pool servers typt u het IP-adres onder **IP-adres/knooppunt naam**. Geef de **poort**op. Klik op **opslaan & volgende**.
 
    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. Selecteer op het scherm instellingen voor eenmalige aanmelding de optie **eenmalige aanmelding inschakelen**. Kies **Kerberos**onder **geselecteerd type eenmalige aanmelding** . Vervang **sessie. SAML. Last. Identity** met **Session. SAML. Last. attr. name. Identity** onder **username source** (deze variabele is ingesteld met behulp van claim toewijzing in azure AD). Selecteer **geavanceerde instelling weer geven**. Onder **Kerberos realm** typt u de domein naam. Geef bij **account naam/account wachtwoord** het APM-overdrachts account en het wacht woord op. Geef het IP-adres van de domein controller op in het veld **KDC** . Klik op **opslaan & volgende**.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. In het kader van deze richt lijnen worden de eindpunt controles overs Laan.  Raadpleeg de documentatie van F5 voor meer informatie.  Selecteer **& volgende opslaan**op het scherm.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Accepteer de standaard instellingen en klik op **& volgende opslaan**. Raadpleeg de documentatie van F5 voor meer informatie over de instellingen voor het beheer van SAML-sessies.


    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Bekijk het scherm samen vatting en selecteer **implementeren** om het Big-IP-adres te configureren.
 
    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. Klik op **volt ooien**zodra de toepassing is geconfigureerd.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Geavanceerde configuratie

>[!NOTE]
>Klik [hier](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) voor naslag informatie

### <a name="configuring-an-active-directory-aaa-server"></a>Een Active Directory AAA-server configureren

U configureert een Active Directory AAA-server in Access Policy Manager (APM) om domein controllers en referenties op te geven voor APM die moeten worden gebruikt voor de verificatie van gebruikers.

1.  Klik op het tabblad Main op **Access Policy > AAA Servers > Active Directory**. Het scherm Active Directory servers lijst wordt geopend.

2.  Klik op **maken**. Het scherm nieuwe server eigenschappen wordt geopend.

3.  Typ in het veld **naam** een unieke naam voor de verificatie server.

4.  In het veld **domein naam** typt u de naam van het Windows-domein.

5.  Selecteer een van de volgende opties voor de instelling voor de **Server verbinding** :

    * Selecteer **groep gebruiken** om een hoge Beschik baarheid voor de AAA-server in te stellen.

    * Selecteer **direct** om de AAA-server in te stellen voor zelfstandige functionaliteit.

6.  Als u **direct**hebt geselecteerd, typt u een naam in het veld **domein controller** .

7.  Als u **groep**gebruiken hebt geselecteerd, configureert u de groep:

    * Typ een naam in het veld **groeps naam van domein controller** .

    * Geef de **domein controllers** in de groep op door het IP-adres en de hostnaam voor elke naam te typen en op de knop **toevoegen** te klikken.

    * Als u de status van de AAA-server wilt controleren, kunt u een health monitor selecteren: alleen de **gateway_icmp** monitor is geschikt in dit geval. u kunt deze selecteren in de lijst monitor van de **Server groep** .

8.  Typ in het veld **naam van beheerder** een naam die hoofdletter gevoelig is voor een beheerder met Active Directory beheerders machtigingen. APM maakt gebruik van de informatie in de velden **beheerders naam** en **beheerders wachtwoord** voor een AD-query. Als Active Directory is geconfigureerd voor anonieme query's, hoeft u geen beheerders naam op te geven. Anders heeft APM een account met voldoende bevoegdheden nodig om verbinding te maken met een Active Directory server, informatie over de gebruikers groep op te halen en Active Directory wachtwoord beleid op te halen ter ondersteuning van wachtwoord functionaliteit. (APM moet wachtwoord beleid ophalen, bijvoorbeeld als u de optie gebruiker vragen om wacht woord te wijzigen voor verloop datum in een AD-query actie selecteert.) Als u geen admin-account gegevens in deze configuratie opgeeft, gebruikt APM het gebruikers account om gegevens op te halen. Dit werkt als het gebruikers account over voldoende bevoegdheden beschikt.

9.  Typ in het veld **beheerders wachtwoord** het beheerders wachtwoord dat is gekoppeld aan de domein naam.

10. Typ in het veld **Administrator-wacht woord controleren** het beheerders wachtwoord voor de **domein naam** in.

11. In het veld **levens duur van groeps cache** typt u het aantal dagen. De standaard levensduur is 30 dagen.

12. In het veld **levens duur van cache voor wachtwoord beveiliging** typt u het aantal dagen. De standaard levensduur is 30 dagen.

13. Selecteer een versleutelings type in de lijst **versleutelings type Kerberos-verificatie** vooraf. De standaard waarde is **geen**. Als u een versleutelings type opgeeft, bevat het BIG-IP-systeem Kerberos preauthentication-gegevens binnen het eerste pakket voor de verificatie service aanvraag (AS-REQ).

14. In het veld **time-out** typt u een time-outinterval (in seconden) voor de AAA-server. (Deze instelling is optioneel.)

15. Klik op **voltooid**. De nieuwe server wordt weer gegeven in de lijst. Hiermee voegt u de nieuwe Active Directory-server toe aan de lijst Active Directory servers.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML-configuratie

1. U moet het meta gegevens certificaat importeren in F5, dat later wordt gebruikt in het installatie proces. Navigeer naar **System > Certificate management > Traffic Certificate management > SSL Certificate List**. Selecteer **importeren** in de rechter bovenhoek.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. Voor het instellen van de SAML-IDP **gaat u naar toegang > federatie > SAML: Service Provider > externe IDP-connectors**en klikt u op **> maken op basis van meta gegevens**.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. Voor het instellen van het SAML-SP gaat u naar **toegang > Federation > SAML-Service Provider > lokale SP-Services** en klikt u op **maken**. Voer de volgende informatie in en klik op **OK**.

    * Type naam: KerbApp200SAML
    * Entiteit-ID *:https://kerbapp200.superdemo.live
    * SP-naam instellingen
    * Schema: https
    * Host: kerbapp200. superdemo. Live
    * Beschrijving: kerbapp200. superdemo. Live

     ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Selecteer de SP-configuratie KerbApp200SAML en klik op **binding/losmaken IDP-connectors**.

     ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     c. Klik op **nieuwe rij toevoegen** en selecteer de **externe IDP-connector** die u in de vorige stap hebt gemaakt, klik op **bijwerken**en klik vervolgens op **OK**.

     ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. Voor het configureren van Kerberos SSO gaat u naar **toegang > eenmalige aanmelding > Kerberos**, voert u de gegevens uit en klikt u op **voltooid**.

    >[!Note]
    > U moet het account voor Kerberos-delegering maken en opgeven. Zie de sectie KCD (Zie de bijlage voor variabelen verwijzingen)

    * **Gebruikersnaam bron**: Session. SAML. Last. attr. name. http:\//schemas.xmlsoap.org/WS/2005/05/Identity/claims/givenName

    * **Gebruikers realm-bron**: sessie. Logon. Last. Domain

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. Voor het configureren van het toegangs profiel gaat u naar **toegang > profiel/beleids regels > toegangs Profiel (per sessie beleid)**, klikt u op **maken**, voert u de volgende gegevens in en klikt u op **voltooid**.

    * Naam: KerbApp200
    * Profiel type: alle
    * Profiel bereik: profiel
    * Talen: Engels

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Klik op de naam KerbApp200, voer de volgende informatie in en klik op **bijwerken**.

    * Domein cookie: superdemonstratie. Live
    * Configuratie van eenmalige aanmelding: KerAppSSO_sso

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Klik op **toegangs beleid** en klik vervolgens op **toegangs beleid bewerken** voor profiel ' KerbApp200 '.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **Session. Logon. Last. usernameUPN expr {[mcget {sessie. SAML. Last. Identity}]}**

    * **Session. ad. lastactualdomain tekst-superdemo. Live**

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName =% {Session. Logon. Last. usernameUPN})**

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **sessie. Logon. Last. gebruikers naam expr {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}**

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {Session. Logon. Last. username}**
    * **mcget {Session. Logon. Last. password**

1. Voor het toevoegen van een nieuw knoop punt gaat u naar **lokaal verkeer > knoop punten > knooppunt lijst, klikt u op maken**, voert u de volgende informatie in en klikt u op **voltooid**.

    * Naam: KerbApp200
    * Beschrijving: KerbApp200
    * Adres: 192.168.20.200

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. Voor het maken van een nieuwe groep gaat u naar **lokaal verkeer > pools > groeps lijst, klikt u op maken**, voert u de volgende gegevens in en klikt u op **voltooid**.

    * Naam: KerbApp200-pool
    * Beschrijving: KerbApp200-pool
    * Status monitors: http
    * Adres: 192.168.20.200
    * Service poort: 81

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. Voor het maken van een virtuele server navigeert u naar **lokaal verkeer > Virtuele Servers > lijst met virtuele server > +**, voert u de volgende gegevens in en klikt u op **voltooid**.

    * Naam: KerbApp200
    * Doel adres/masker: 192.168.30.200 host
    * Service poort: poort 443 HTTPS
    * Toegangs profiel: KerbApp200
    * Geef het toegangs profiel op dat u in de vorige stap hebt gemaakt

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Kerberos-delegatie instellen 

>[!NOTE]
>Klik [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) voor naslag informatie

*  **Stap 1:** Een delegerings account maken

    **Hierbij**
    * Domein naam: **superdemonstratie. live**

    * Sam-account naam: **Big-ipuser**

    * New-ADUser-name "APM-delegerings account host/big-ipuser.superdemo.live@superdemo.live "-userPrincipalName-samAccountName "Big-ipuser"-PasswordNeverExpires $True-ingeschakeld $True-AccountPassword (Read-host-AsSecureString "wacht woord! 1234")

* **Stap 2:** SPN instellen (op het APM delegerings account)

    **Hierbij**
    * setspn – een **host/Big-ipuser. superdemo. live** -ipuser

* **Stap 3:** Met SPN-overdracht (voor het App Service-account) stelt u de juiste overdracht voor het account voor de F5-delegering in.
    In het onderstaande voor beeld wordt APM-delegering account geconfigureerd voor KCD voor FRP-App1. superdemo. Live-App.

    ![F5-configuratie (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Geef de [Details op zoals](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)vermeld in het bovenstaande referentie document.

### <a name="create-f5-test-user"></a>Een F5 test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in F5. Werk samen met het [ondersteunings team voor F5-clients](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen op het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de F5-tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de F5 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer F5 met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor F5 configureren voor een toepassing op basis van koptekst](headerf5-tutorial.md)

- [Eenmalige aanmelding voor F5 configureren voor de geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

