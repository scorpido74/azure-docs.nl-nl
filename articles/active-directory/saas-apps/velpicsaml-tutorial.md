---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Velpic SAML | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Velpic SAML.
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
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72241566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Velpic SAML

In deze zelfstudie leert u hoe U Velpic SAML integreren met Azure Active Directory (Azure AD). Wanneer u Velpic SAML integreert met Azure AD, u het:

* Controle in Azure AD die toegang heeft tot Velpic SAML.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Velpic SAML met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Velpic SAML single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Velpic SAML steunt **DOOR SSO** geïnitieerde SP

## <a name="adding-velpic-saml-from-the-gallery"></a>Toedoen van Velpic SAML uit de galerie

Als u de integratie van Velpic SAML in Azure AD wilt configureren, moet u Velpic SAML vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ In de sectie **Toevoegen van de galerie** **Velpic SAML** in het zoekvak.
1. Selecteer **Velpic SAML** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Azure AD-eenmaligaanmelding configureren en testen voor Velpic SAML

Azure AD SSO configureren en testen met Velpic SAML met behulp van een testgebruiker genaamd **B.Simon**. Als SSO werkt, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Velpic SAML.

Als u Azure AD SSO wilt configureren en testen met Velpic SAML, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Velpic SAML SSO](#configure-velpic-saml-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Velpic SAML-testgebruiker maken](#create-velpic-saml-test-user)** - om een tegenhanger van B.Simon in Velpic SAML te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Met de Velpic SAML-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<sub-domain>.velpicsaml.net`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Houd er rekening mee dat de URL van Sign on wordt verstrekt door het Velpic SAML-team en dat de waarde van de id beschikbaar is wanneer u de SSO-plug-in aan velpic SAML-zijde configureert. Je moet die waarde te kopiëren van Velpic SAML applicatie pagina en plak het hier.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Velpic SAML instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Velpic SAML.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Velpic SAML**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-velpic-saml-sso"></a>Velpic SAML SSO configureren

1. Als u de configuratie binnen Velpic SAML wilt automatiseren, moet u **de browserextensie Mijn apps Secure Sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **Het opzetten van Velpic SAML** zal u doorverwijzen naar de Velpic SAML applicatie. Van daaruit, bieden de admin geloofsbrieven aan te melden bij Velpic SAML. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-8.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Velpic SAML handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich aan op uw Velpic SAML-bedrijfssite als beheerder en voert u de volgende stappen uit:

4. Klik op het tabblad **Beheren** en ga naar **de sectie Integratie,** waar u op de knop **Plug-ins** moet klikken om nieuwe plug-in voor Aanmelden te maken.

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_1.png)

5. Klik op de knop **'Plug-in toevoegen'.**
    
    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_2.png)

6. Klik op de **SAML-tegel** op de pagina Plug-in toevoegen.
    
    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_3.png)

7. Voer de naam van de nieuwe SAML-plug-in in in en klik op de knop **'Toevoegen'.**

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_4.png)

8. Voer de volgende gegevens in:

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_5.png)

    a. Typ in het tekstvak **Naam** de naam saml-plug-in.

    b. Plak in het tekstvak **van de url** van de uitgever de Azure **AD-id** die u hebt gekopieerd vanuit het **aanmeldingsvenster configureren** van de Azure-portal.

    c. Upload in de **Metagegevens van de provider** het XML-bestand met metagegevens dat u hebt gedownload van azure-portal.

    d. U er ook voor kiezen om SAML net op tijd in te schakelen door het selectievakje **'Nieuwe gebruikers automatisch maken'** in te schakelen. Als een gebruiker niet bestaat in Velpic en deze vlag niet is ingeschakeld, mislukt de aanmelding van Azure. Als de vlag is ingeschakeld, wordt de gebruiker automatisch in Velpic ingerichte op het moment van inloggen. 

    e. Kopieer het **enkele teken op URL** vanuit het tekstvak en plak het in de Azure-portal.
    
    f. Klik op **Opslaan**.

### <a name="create-velpic-saml-test-user"></a>Velpic SAML-testgebruiker maken

Deze stap is meestal niet vereist omdat de toepassing net op tijd gebruikersinrichting ondersteunt. Als de automatische gebruikersinrichting niet is ingeschakeld, kan handmatige gebruikersmaken worden gedaan zoals hieronder beschreven.

Meld u aan op uw Velpic SAML-bedrijfssite als beheerder en voer de volgende stappen uit:
    
1. Klik op het tabblad Beheren en ga naar de sectie Gebruikers en klik vervolgens op Nieuwe knop om gebruikers toe te voegen.

    ![gebruiker toevoegen](./media/velpicsaml-tutorial/velpic_7.png)

2. Voer op de pagina **Nieuwe gebruiker maken** de volgende stappen uit.

    ![gebruiker](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Typ in het tekstvak **Voornaam** de voornaam van B.

    b. Typ in het tekstvak **Achternaam** de achternaam van Simon.

    c. Typ in het tekstvak **Gebruikersnaam** de gebruikersnaam van B.Simon.

    d. Typ in het tekstvak **E-mail** het e-mailadres van B.Simon@contoso.com het account.

    e. De rest van de informatie is optioneel, u deze indien nodig invullen.
    
    f. Klik **op OPSLAAN**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

1. Wanneer u op de Velpic SAML-tegel in het toegangspaneel klikt, moet u de aanmeldingspagina van de Velpic SAML-toepassing krijgen. Op de aanmeldingspagina ziet u de knop **'Aanmelden bij Azure AD'.**

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_6.png)

1. Klik op de knop **Aanmelden bij Azure AD** om u aan te melden bij Velpic met uw Azure AD-account.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Velpic SAML met Azure AD](https://aad.portal.azure.com/)

