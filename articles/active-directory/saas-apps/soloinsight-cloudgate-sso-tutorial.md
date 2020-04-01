---
title: 'Zelfstudie: Azure Active Directory-integratie met Soloinsight-CloudGate SSO | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159927"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Zelfstudie: Soloinsight-CloudGate SSO integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Soloinsight-CloudGate SSO integreren met Azure Active Directory (Azure AD). Wanneer u Soloinsight-CloudGate SSO integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Soloinsight-CloudGate SSO.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Soloinsight-CloudGate SSO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* Soloinsight-CloudGate SSO single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Soloinsight-CloudGate SSO ondersteunt **SP** geïnitieerde SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO toevoegen uit de galerie

Voor de configuratie van de integratie van Soloinsight-CloudGate SSO in Azure Active Directory, moet u Soloinsight-CloudGate SSO vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Soloinsight-CloudGate SSO** in de sectie **Toevoegen vanuit de galerie** in het zoekvak.
1. Selecteer **Soloinsight-CloudGate SSO** in het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Soloinsight-CloudGate SSO met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Soloinsight-CloudGate SSO.

Als u Azure AD SSO wilt configureren en testen met Soloinsight-CloudGate SSO, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met Britta Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om Britta Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak Soloinsight-CloudGate SSO-testgebruiker](#create-soloinsight-cloudgate-sso-test-user)** om een tegenhanger van Britta Simon te hebben in Soloinsight-CloudGate SSO die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Soloinsight-CloudGate SSO-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.sigateway.com/login`

    1. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL voor eenmalige aanmelding en de id, zoals later in de zelfstudie wordt uitgelegd, in de sectie **Eenmalige aanmelding voor Soloinsight-CloudGate SSO configureren**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Soloinsight-CloudGate SSO** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO configureren

1. Als u de configuratie binnen Soloinsight-CloudGate SSO wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure Sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Soloinsight-CloudGate SSO** en verwijst u naar de Soloinsight-CloudGate SSO-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Soloinsight-CloudGate SSO. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-8.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Soloinsight-CloudGate SSO handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw Soloinsight-CloudGate SSO-bedrijfssite en voert u de volgende stappen uit:

4. Als u de waarden wilt krijgen die in de Azure-portal moeten worden geplakt tijdens het configureren van Basic SAML, meldt u zich aan bij de CloudGate Web Portal met behulp van uw referenties en opent u de SSO-instellingen, die kunnen worden gevonden op het volgende pad **Home>Beheer>Systeeminstellingen>Algemeen**.

    ![CloudGate SSO-instellingen](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL voor SAML-consument**

    * Kopieer de koppelingen die beschikbaar zijn tegen respectievelijk de **URL van De Consument van Saml** en de URL **omleiden** en plak deze in de sectie **BasisSAML-configuratie** van Azure portal voor **respectievelijk id-id** en **URL-antwoord.**

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-handtekeningcertificaat**

    * Ga naar de bron van het certificaatbestand (Base64) dat is gedownload van azure portal SAML Signing Certificate lists en klik er met de rechtermuisknop op. Kies in de lijst de optie **Bewerken met Notepad++**. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopieer de inhoud in het (Base64)-certificaat naar het Notepad++-bestand.

        ![Certificaat kopiëren](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Plak de inhoud in de CloudGate Web Portal SSO-instellingen in het veld **Certificaat** en klik op de knop Opslaan.

        ![Certificaatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Standaardgroep**

    * Selecteer in de CloudGate Web Portal in de vervolgkeuzelijst voor de optie **Default Group** (Standaardgroep) **Business Admin** (Bedrijfsbeheerder)

        ![Standaardgroep](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-id en aanmeldings-URL**

    * De gekopieerde **login-URL** van de **Azure-portal Soloinsight-CloudGate SSO-configuraties** instellen, moet worden ingevoerd in de sectie SSO-instellingen van CloudGate Web Portal.

    * Plak de **koppeling URL-aanmelding** van Azure-portal in het **veld URL van AD-aanmelding spoort** cloudgate.

    * De **koppeling Azure AD-id plakken** vanuit Azure-portal in het **veld AD-id van** CloudGate-webportal

        ![AD-aanmelding](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd Britta Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Soloinsight-CloudGate SSO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Soloinsight-CloudGate SSO**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight CloudGate SSO-testgebruiker maken

Om een testgebruiker te maken, selecteert u **Employees** (Werknemers) in het hoofdmenu van uw CloudGate Web Portal en vult u het formulier voor het toevoegen van nieuwe werknemers in. Het machtigingsniveau dat aan de testgebruiker moet worden toegewezen is **Business Admin** (Bedrijfsbeheerder). Klik op **Create** (Maken) wanneer u alle vereiste velden hebt ingevuld.

![Werknemertest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Test SSO

Wanneer u de Soloinsight-CloudGate SSO-tegel in het toegangspaneel selecteert, moet u automatisch worden aangemeld bij de Soloinsight-CloudGate SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)