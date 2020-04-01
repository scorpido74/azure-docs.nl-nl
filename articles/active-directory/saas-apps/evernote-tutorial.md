---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Evernote | Microsoft Documenten'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121623"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Evernote

In deze zelfstudie leert u hoe U Evernote integreren met Azure Active Directory (Azure AD). Wanneer u Evernote integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Evernote.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Evernote met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Evernote single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Evernote ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-evernote-from-the-gallery"></a>Evernote toevoegen vanuit de galerie

Om de configuratie van Evernote in Microsoft Azure Active Directory te configureren, moet u Evernote vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Evernote in** het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **Evernote in** het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Azure AD-aanmelding voor Evernote configureren en testen

Azure AD SSO configureren en testen met Evernote met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Evernote.

Als u Azure AD SSO met Evernote wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Evernote SSO](#configure-evernote-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Evernote-testgebruiker maken](#create-evernote-test-user)** - om een tegenhanger van B.Simon in Evernote te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Evernote-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    In het tekstvak **Id** typt u een URL: `https://www.evernote.com/saml2`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://www.evernote.com/Login.action`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Als u de **ondertekeningsopties wilt** wijzigen, klikt u op de knop **Bewerken** om het dialoogvenster **SAML-ondertekeningscertificaat** te openen.

    ![installatiekopie](common/edit-certificate.png) 

    ![installatiekopie](./media/evernote-tutorial/samlassertion.png)

    a. Selecteer de optie **SAML-antwoord en -bewering ondertekenen** voor **de optie Ondertekenen**.

    b. Klik **op Opslaan**

1. Kopieer in de sectie **Evernote instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Evernote.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Evernote** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-evernote-sso"></a>Evernote SSO configureren

1. Als u de configuratie binnen Evernote wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **setup Evernote** zal u doorverwijzen naar de Evernote applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Evernote. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Evernote handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich aan bij uw Evernote-bedrijfssite als beheerder en voert u de volgende stappen uit:

4. Ga naar **'Admin Console'**

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Ga vanuit de **'Admin Console'** naar **‘Security’** en selecteer **‘Single Sign-On’**

    ![SSO-instelling](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configureer de volgende waarden:

    ![Certificaatinstelling](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **SSO inschakelen:** SSO is standaard ingeschakeld (klik op **Eén aanmelding uitschakelen** om de SSO-vereiste te verwijderen)

    b. Plak de waarde van de **aanmeldings-URL**, die u uit de Microsoft Azure-portal naar het tekstvak **SAML HTTP Request URL** hebt gekopieerd.

    c. Open het gedownloade certificaat van Microsoft Azure Active Directory in Kladblok en kopieer de inhoud, inclusief "BEGIN CERTIFICATE" en "END CERTIFICATE" en plak deze in het tekstvak **X.509 Certificate**. 

    d. Klik op **Save Changes**

### <a name="create-evernote-test-user"></a>Evernote-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Evernote, moeten ze in Evernote worden ingericht.  
In het geval van Evernote is inrichten een handmatige taak.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u aan bij uw Evernote-bedrijfssite als beheerder.

2. Klik op de **'Admin Console'**.

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Ga vanuit de **'Admin Console'** naar **‘Add users’**.

    ![Testgebruiker toevoegen](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Voeg teamleden toe** in het tekstvak **Email**, typ het e-mailadres van het gebruikersaccount en klik op **Invite.**

    ![Testgebruiker toevoegen](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Nadat de uitnodiging is verzonden, ontvangt de houder van het Microsoft Azure Active Directory-account een e-mail om de uitnodiging te accepteren.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Evernote klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Evernote waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Evernote met Azure AD](https://aad.portal.azure.com/)

