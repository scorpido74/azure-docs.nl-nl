---
title: 'Zelfstudie: Azure Active Directory-integratie met Aha! | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Aha!
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a63872680d28664c6d5a7ff109f6de72817173d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68989647"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Tutorial: Integreer Aha! met Azure Active Directory

In deze tutorial leer je hoe je Aha integreren! met Azure Active Directory (Azure AD). Wanneer u Aha integreert! met Azure AD u het als nog niet doen:

* Beheer in Azure AD die toegang heeft tot Aha!.
* Stel uw gebruikers in staat om automatisch aangemeld te worden bij Aha! met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Aha!- eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Aha!- ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Aha!- ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-aha-from-the-gallery"></a>Aha! toevoegen vanuit de galerie

Om de integratie van Aha! te configureren in Azure AD, moet u Aha! vanuit de galerie  toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Add from the gallery** **Aha!** in het zoekvak.
1. Selecteer **Aha!** van het deelvenster Resultaten en voeg vervolgens de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Azure AD-aanmelding voor Aha configureren en testen!

Azure AD SSO configureren en testen met Aha! met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Aha!.

Als u Azure AD SSO wilt configureren en testen met Aha!, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Configureer Aha! SSO](#configure-aha-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Aha! testgebruiker](#create-aha-test-user)** - om een tegenhanger van B.Simon in Aha te hebben! die is gekoppeld aan de weergave van de gebruiker in Azure AD.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de pagina van toepassingsintegratie van**Aha!** pagina met toepassingsintegratie, zoek de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.aha.io/session/new`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<companyname>.aha.io`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [Aha! Client support team](https://www.aha.io/company/contact) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **Aha! instellen.** kopieer de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Aha!.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Aha!** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-aha-sso"></a>Aha! configureren SSO

1. Als u de configuratie binnen Aha wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **Setup Aha!** zal u naar de Aha leiden! Toepassing. Van daaruit, bieden de admin referenties aan te melden bij Aha!. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-8.

    ![Configuratie instellen](common/setup-sso.png)

3. Als je Aha wilt instellen! handmatig, open een nieuwe webbrowser venster en meld u aan bij uw Aha! bedrijfssite als beheerder en voer de volgende stappen uit:

4. Klik in het menu aan de bovenkant op **Settings**.

    ![Instellingen](./media/aha-tutorial/IC798950.png "Instellingen")

5. Klik op **Account**.
  
    ![Profiel](./media/aha-tutorial/IC798951.png "Profiel")

6. Klik op **Security and single sign-on**.

    ![Beveiliging en single sign-on](./media/aha-tutorial/IC798952.png "Beveiliging en single sign-on")

7. Ga naar de sectie **Single Sign-On** en selecteer als **Identity Provider** de optie **SAML2.0**.

    ![Beveiliging en single sign-on](./media/aha-tutorial/IC798953.png "Beveiliging en single sign-on")

8. Voer op de configuratiepagina **Single Sign-On** de volgende stappen uit:

    ![Eén aanmelding](./media/aha-tutorial/IC798954.png "Eenmalige aanmelding")

    a. Typ in het tekstvak **Name** een naam voor de configuratie.

    b. Selecteer voor **Configure using** de optie **Metadata File**.

    c. Klik op **Browse** om het gedownloade metagegevensbestand te uploaden.

    d. Klik op **Update**.

### <a name="create-aha-test-user"></a>Testgebruiker voor Aha! maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Aha!. Aha!- biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Aha! bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Aha! in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Aha! waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

