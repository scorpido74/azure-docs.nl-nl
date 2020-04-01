---
title: 'Zelfstudie: Azure Active Directory Single sign-on (SSO) integratie met Cisco Webex | Microsoft Documenten'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01fe4d06d5f73eacee1d1cdaf1963232b84daf05
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046788"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Zelfstudie: Azure Active Directory Single sign-on (SSO) integratie met Cisco Webex

In deze zelfstudie leert u hoe u Cisco Webex integreert met Azure Active Directory (Azure AD). Wanneer u Cisco Webex integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Cisco Webex.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Cisco Webex met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Cisco Webex single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Cisco Webex ondersteunt **SP** geïnitieerde SSO.
* Cisco Webex ondersteunt **geautomatiseerde** gebruikersinrichting.
* Zodra u Cisco Webex hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex toevoegen vanuit de galerie

Voor het configureren van de integratie van Cisco Webex in Azure AD moet u Cisco Webex vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Cisco Webex** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **Cisco Webex** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Azure AD-aanmelding voor Cisco Webex configureren en testen

Azure AD SSO configureren en testen met Cisco Webex met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Cisco Webex.

Als u Azure AD SSO wilt configureren en testen met Cisco Webex, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
    1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Configureer Cisco Webex](#configure-cisco-webex)** om de SSO-instellingen aan toepassingszijde te configureren.
    1. **[Cisco Webex-testgebruiker maken](#create-cisco-webex-test-user)** om een tegenhanger van B.Simon in Cisco Webex te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de cisco **Webex-toepassingsintegratiepagina** de sectie **Beheren** en selecteer Enkele **aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** uploadt u het bestand met metagegevens dat u hebt gedownload van de **serviceprovider** en configureert u de toepassing door de volgende stappen uit te voeren:

    >[!Note]
    >U krijgt het bestand met metagegevens van de serviceprovider van de sectie **Cisco Webex configureren,** dat later in de zelfstudie wordt uitgelegd. 

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Als het uploaden van het metagegevensbestand van de serviceprovider is geslaagd, worden de waarden voor de **id** en **Antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    Plak in het tekstvak **Aanborden op URL** de waarde van **de URL van antwoord**, die automatisch wordt ingevuld door het uploaden van sp-metagegevens.

1. In de Cisco Webex-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Cisco Webex applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.
  
    | Name |  Bronkenmerk|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Cisco Webex instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Cisco Webex.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Typ en selecteer **Cisco Webex** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-cisco-webex"></a>Cisco Webex configureren

1. Als u de configuratie binnen Cisco Webex wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure installeren** door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u extensie aan de browser hebt toegevoegd, klikt u op **Cisco Webex instellen,** wordt u doorverwijzen naar de Cisco Webex-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Cisco Webex. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-8.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Cisco Webex handmatig wilt instellen, meldt u zich aan bij [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) met uw volledige beheerdersreferenties.

4. Selecteer **Settings** en klik in de sectie **Authentication** op **Modify**.

    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Selecteer **Een identiteitsprovider van derden integreren. (Geavanceerd)** en ga naar het volgende scherm.

6. Sleep het bestand met de Azure AD-metagegevens naar de pagina **Import Idp Metadata** of gebruik de optie 'file browser' om het bestand te zoeken en te uploaden. Selecteer vervolgens **Require certificate signed by a certificate authority in Metadata (more secure)** en klik op **Next**.

    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Selecteer **Test SSO Connection**. Wanneer er een nieuw browsertabblad wordt geopend, moet u zich verifiëren bij Azure AD door u aan te melden.

8. Ga terug naar het tabblad **Cisco Cloud Collaboration Management.** Als de test succesvol is, selecteert u **Deze test is geslaagd. Optie Eenmalig aanmelden inschakelen** en klik op **Volgende**.

### <a name="create-cisco-webex-test-user"></a>Testgebruiker voor Cisco Webex maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Cisco Webex. In deze sectie maakt u een gebruiker genaamd B.Simon in Cisco Webex.

1. Meld u aan bij [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) met uw volledige beheerdersreferenties.

2. Klik op **Users** en vervolgens op **Manage Users**.
   
    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. Selecteer **Manually Add or Modify Users** in het venster **Manage User** en klik vervolgens op **Next**.

4. Selecteer **Names and Email address**. Vul vervolgens de tekstvakken als volgt in:

    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker zoals **B**.

    b. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld b.simon@contoso.com.

5. Klik op het plusteken om B.Simon toe te voegen. Klik vervolgens op **Volgende.**

6. Klik in het venster **Add Services for Users** op **Save** en daarna op **Finish**.

## <a name="test-sso"></a>Test SSO

Wanneer u de Cisco Webex-tegel selecteert in het Access-paneel, moet u automatisch worden aangemeld bij de Cisco Webex waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Cisco Webex uitproberen met Azure AD](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Cisco Webex beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-webex)