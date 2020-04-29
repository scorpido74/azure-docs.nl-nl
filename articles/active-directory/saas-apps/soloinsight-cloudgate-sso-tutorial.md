---
title: 'Zelf studie: integratie Azure Active Directory met Soloinsight-CloudGate SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Soloinsight-CloudGate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b88822f164e0fe8cdf55eddfa981644f725e01f3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73159927"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Zelf studie: Soloinsight-CloudGate-SSO integreren met Azure Active Directory

In deze zelf studie leert u hoe u Soloinsight-CloudGate-SSO integreert met Azure Active Directory (Azure AD). Wanneer u Soloinsight-CloudGate SSO integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SSO van Soloinsight-CloudGate.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Soloinsight-CloudGate SSO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Soloinsight-CloudGate SSO-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Soloinsight-CloudGate SSO ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO toevoegen uit de galerie

Voor de configuratie van de integratie van Soloinsight-CloudGate SSO in Azure Active Directory, moet u Soloinsight-CloudGate SSO vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **Soloinsight-CLOUDGATE SSO** in het zoekvak in de sectie **toevoegen vanuit de galerie** .
1. Selecteer **Soloinsight-CLOUDGATE SSO** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met Soloinsight-CloudGate SSO configureren en testen met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Soloinsight-CloudGate SSO.

Als u Azure AD SSO wilt configureren en testen met Soloinsight-CloudGate SSO, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Soloinsight-CLOUDGATE SSO](#configure-soloinsight-cloudgate-sso)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding voor Azure ad te testen met Julia Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
5. **[Maak een Soloinsight-CLOUDGATE SSO-test gebruiker](#create-soloinsight-cloudgate-sso-test-user)** een equivalent van Julia Simon in Soloinsight-CloudGate SSO te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **SOLOINSIGHT-CloudGate SSO-** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.sigateway.com/login`

    1. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL voor eenmalige aanmelding en de id, zoals later in de zelfstudie wordt uitgelegd, in de sectie **Eenmalige aanmelding voor Soloinsight-CloudGate SSO configureren**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de **SSO-sectie Soloinsight-CloudGate instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate-SSO configureren

1. Als u de configuratie wilt automatiseren in Soloinsight-CloudGate SSO, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Setup Soloinsight-CLOUDGATE SSO** stuurt u naar de SSO-toepassing Soloinsight-CloudGate. Geef de beheerders referenties op om u aan te melden bij Soloinsight-CloudGate SSO. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-8 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Soloinsight-CloudGate SSO hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw SSO-bedrijfs site Soloinsight-CloudGate als beheerder en voert u de volgende stappen uit:

4. Als u de waarden wilt ophalen die in de Azure Portal moeten worden geplakt tijdens het configureren van basis-SAML, meldt u zich met uw referenties aan bij de CloudGate-webportal en gaat u naar de SSO-instellingen, die u kunt vinden op de volgende locatie **Home>beheer>systeem instellingen>algemeen**.

    ![CloudGate SSO-instellingen](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL voor SAML-consument**

    * Kopieer de beschik bare koppelingen voor de URL van de **SAML-consument** en de **omleidings-URL** velden en plak ze in de sectie Azure Portal **Basic SAML-configuratie** voor **id (Entiteits-ID)** en **antwoord-URL** -velden.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-handtekeningcertificaat**

    * Ga naar de bron van het certificaat bestand (base64) dat is gedownload van Azure Portal SAML-handtekening certificaat lijsten en klik er met de rechter muisknop op. Kies in de lijst de optie **Bewerken met Notepad++**. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopieer de inhoud in het (Base64)-certificaat naar het Notepad++-bestand.

        ![Certificaat kopiëren](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Plak de inhoud in de CloudGate Web Portal SSO-instellingen in het veld **Certificaat** en klik op de knop Opslaan.

        ![Certificaatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Standaard groep**

    * Selecteer in de CloudGate Web Portal in de vervolgkeuzelijst voor de optie **Default Group** (Standaardgroep) **Business Admin** (Bedrijfsbeheerder)

        ![Standaardgroep](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-id en aanmeldings-URL**

    * De gekopieerde **aanmeldings-URL** uit het Azure Portal **instellen van SOLOINSIGHT-CloudGate SSO-** configuraties moet worden ingevoerd in de sectie CloudGate Web Portal SSO Settings.

    * Plak de koppeling van de **aanmeldings-URL** van Azure Portal in het veld CloudGate Web Portal AD- **aanmeldings-URL** .

    * Plak de koppeling **Azure ad id** van Azure Portal in het veld CloudGate Web Portal **ad id**

        ![AD-aanmelding](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Soloinsight-CloudGate SSO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Soloinsight-CloudGate SSO**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight CloudGate SSO-testgebruiker maken

Om een testgebruiker te maken, selecteert u **Employees** (Werknemers) in het hoofdmenu van uw CloudGate Web Portal en vult u het formulier voor het toevoegen van nieuwe werknemers in. Het machtigingsniveau dat aan de testgebruiker moet worden toegewezen is **Business Admin** (Bedrijfsbeheerder). Klik op **Create** (Maken) wanneer u alle vereiste velden hebt ingevuld.

![Werknemertest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>SSO testen

Wanneer u de SSO-tegel Soloinsight-CloudGate in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de Soloinsight-CloudGate-SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)