---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met CakeHR | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en CakeHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c9bbdb1-ac47-4fb8-a1cc-1e647b0323a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0860411c95e48a16d75df4aeeedf3405a5b1835
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595025"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met CakeHR

In deze zelfstudie leert u hoe u CakeHR integreren met Azure Active Directory (Azure AD). Wanneer u CakeHR integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot CakeHR.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij CakeHR met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* CakeHR single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* CakeHR ondersteunt **SP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-cakehr-from-the-gallery"></a>CakeHR toevoegen vanuit de galerie

Als u de integratie van CakeHR in Azure AD wilt configureren, moet u CakeHR uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **CakeHR** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **CakeHR** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Azure AD-aanmelding voor CakeHR configureren en testen

Azure AD SSO configureren en testen met CakeHR met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in CakeHR.

Als u Azure AD SSO wilt configureren en testen met CakeHR, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer CakeHR SSO](#configure-cakehr-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak CakeHR-testgebruiker](#create-cakehr-test-user)** - om een tegenhanger van B.Simon in CakeHR te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **CakeHR-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<yourcakedomain>.cake.hr/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met [het supportteam van CakeHR-klanten](mailto:info@cake.hr) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-ondertekeningscertificaat** de **waarde DUIMAFDRUK** en sla deze op op uw Kladblok.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer in de sectie **CakeHR instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot CakeHR.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **CakeHR**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-cakehr-sso"></a>CakeHR SSO configureren

1. Als u de configuratie binnen CakeHR wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Na het toevoegen van extensie aan de browser, klik op **Het opzetten van CakeHR** zal u direct naar de CakeHR applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij CakeHR. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u CakeHR handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw CakeHR-bedrijfssite en voert u de volgende stappen uit:

1. Klik in de rechterbovenhoek van de pagina op **Profiel** en navigeer vervolgens naar **Instellingen**.

    ![CakeHR-configuratie](./media/cakehr-tutorial/config01.png)

1. Klik vanaf de linkerkant van de menubalk op **INTEGRATIES** > **SAML SSO** en voer de volgende stappen uit:

    ![CakeHR-configuratie](./media/cakehr-tutorial/config02.png)

    a. Typ in het tekstvak `cake.hr` **Entiteit-id** .

    b. Plak in het tekstvak **Verificatie-URL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.

    c. Plak in het tekstvak **Key fingerprint (SHA1)de** **duimafdrukwaarde** die u hebt gekopieerd van Azure-portal.

    d. Schakel het selectievakje **Eén teken inschakelen in.**

    e. Klik op **Opslaan**.

### <a name="create-cakehr-test-user"></a>CakeHR-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij CakeHR, moeten ze worden ingericht in CakeHR. In CakeHR is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij CakeHR als beveiligingsbeheerder.

2. Klik vanaf de linkerkant van de menubalk op **COMPANY** > **ADD**.

    ![CakeHR-configuratie](./media/cakehr-tutorial/config03.png)

3. Voer in de pop-up **Nieuwe werknemer** toevoegen de volgende stappen uit:

     ![CakeHR-configuratie](./media/cakehr-tutorial/config04.png)

    a. Voer in het tekstvak **Volledige naam** de naam van de gebruiker in, zoals B.Simon.

    b. Voer in **het e-mailtekstvak** `B.Simon@contoso.com`bewerken de e-mail in van de gebruiker, zoals .

    c. Klik **op Account MAKEN**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de cakehr-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de CakeHR waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [CakeHR uitproberen met Azure AD](https://aad.portal.azure.com/)
