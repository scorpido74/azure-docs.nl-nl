---
title: 'Zelfstudie: Azure Active Directory-integratie met Freedcamp | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67101916"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Zelfstudie: Freedcamp integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Freedcamp integreren met Azure Active Directory (Azure AD). Wanneer u Freedcamp integreert met Azure AD, u het:

* Beheer in Azure AD die toegang heeft tot Freedcamp.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Freedcamp met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Freedcamp single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Freedcamp steunt **SP en IDP** geïnitieerd SSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Freedcamp toevoegen uit de galerie

Als u de integratie van Freedcamp in Azure AD wilt configureren, moet u Freedcamp uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Freedcamp** in de sectie **Toevoegen vanuit de galerie** in het zoekvak.
1. Selecteer **Freedcamp** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Freedcamp met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Freedcamp.

Als u Azure AD SSO wilt configureren en testen met Freedcamp, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Freedcamp](#configure-freedcamp)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met Britta Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om Britta Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak de testgebruiker](#create-freedcamp-test-user)** van Freedcamp om een tegenhanger van Britta Simon in Freedcamp te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina Met de integratie van **de Freedcamp-toepassing** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    1. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Gebruikers kunnen ook de url-waarden invoeren met betrekking tot hun eigen `freedcamp.com`klantdomein en ze kunnen niet noodzakelijkerwijs van het patroon zijn, ze kunnen elke specifieke waarde van het klantdomein invoeren, specifiek voor hun toepassingsexemplaar. Ook u contact opnemen met [Freedcamp Client support team](mailto:devops@freedcamp.com) voor meer informatie over url patronen.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Freedcamp instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Freedcamp configureren

1. Als u de configuratie binnen Freedcamp wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **Setup Freedcamp** zal u doorverwijzen naar de Freedcamp applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Freedcamp. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Freedcamp handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan op uw Freedcamp-bedrijfssite en voert u de volgende stappen uit:

4. Klik in de rechterbovenhoek van de pagina op **het profiel** en navigeer vervolgens naar **Mijn account**.

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config01.png)

5. Klik vanaf de linkerkant van de menubalk op **SSO** en voer op de pagina **Uw SSO-verbindingen** de volgende stappen uit:

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config02.png)

    a. Typ de titel in het tekstvak **Titel.**

    b. Plak in het tekstvak **Entiteit-id** de waarde **van de Azure AD-id,** die u hebt gekopieerd van de Azure-portal.

    c. Plak in het tekstvak **Inlog-URL** de **waarde van de inlog-URL** plakken die u hebt gekopieerd van de Azure-portal.

    d. Open het certificaat Base64 gecodeerd in kladblok, kopieer de inhoud ervan en plak het in het **tekstvak Certificaat.**

    e. Klik **op Verzenden**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Freedcamp.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Freedcamp**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-freedcamp-test-user"></a>Freedcamp-testgebruiker maken

Als u Azure AD-gebruikers wilt inschakelen, moet u zich aanmelden bij Freedcamp, deze moeten worden ingericht in Freedcamp. In Freedcamp is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u in een ander browservenster aan bij Freedcamp als beveiligingsbeheerder.

2. Klik in de bovenhoek van de pagina op **het profiel** en navigeer vervolgens naar **Systeem beheren.**

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config03.png)

3. Voer aan de rechterkant van de pagina Systeem beheren de volgende stappen uit:

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config04.png)

    a. Klik op **Gebruikers toevoegen of uitnodigen**.

    b. Voer in het **tekstvak E-mail** de e-mail van de gebruiker in, zoals `Brittasimon@contoso.com`.

    c. Klik **op Gebruiker toevoegen**.

### <a name="test-sso"></a>Test SSO

Wanneer u de Tegel Freedcamp selecteert in het Toegangspaneel, moet u automatisch worden aangemeld bij de Freedcamp waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)