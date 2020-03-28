---
title: 'Zelfstudie: Azure Active Directory-integratie met dmarcian | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68823693"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Zelfstudie: Dmarcian integreren met Azure Active Directory

In deze zelfstudie leert u hoe u dmarcian integreert met Azure Active Directory (Azure AD). Wanneer u dmarcian integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot dmarcian.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij dmarcian met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* dmarcian single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* dmarcian ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-dmarcian-from-the-gallery"></a>Dmarcian vanuit de galerie toevoegen

Om de integratie van dmarcian in Azure AD te configureren, moet u dmarcian vanuit de galerij toevoegen aan uw lijst van beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **dmarcian** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **dmarcian** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met dmarcian met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in dmarcian.

Als u Azure AD SSO met dmarcian wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer dmarcian SSO](#configure-dmarcian-sso)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak dmarcian testgebruiker](#create-dmarcian-test-user)** - om een tegenhanger van B.Simon in dmarcian te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **dmarcian** application integration de sectie **Beheren** en selecteer **Single sign-on**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: 
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Dit zijn geen echte waarden. U gaat deze waarden bijwerken met de daadwerkelijke id, antwoord-URL en URL voor eenmalige aanmelding, wat later in de zelfstudie nog wordt uitgelegd.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Dmarcian SSO configureren

1. Als u de configuratie binnen dmarcian wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure installeren** door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **Setup dmarcian** zal u doorverwijzen naar de dmarcian applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij dmarcian. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u dmarcian handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw dmarcian bedrijfssite en voert u de volgende stappen uit:

4. Klik op **Profiel** in de rechterbovenhoek hoek en navigeer naar **Voorkeuren**.

    ![Voorkeuren](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Schuif omlaag en klik op de sectie **Eenmalige aanmelding** en klik vervolgens op **Configureren**.

    ![Eenmalig](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Zet op de **Eenmalige aanmelding op basis van SAML** de **Status** op **Ingeschakeld** en voer de volgende stappen uit:

    ![Verificatie](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * In de sectie **dmarcian aan uw id-provider toevoegen** klikt u op **Kopiëren** om de **ACS-URL** voor uw exemplaar te kopiëren, en plak deze in het tekstvak **Antwoord-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    * In de sectie **dmarcian aan uw id-provider toevoegen** klikt u op **Kopiëren** om de **Entiteits-id** voor uw exemplaar te kopiëren, en plak deze in het tekstvak **Id** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    * In de sectie **Verificatie instellen** plakt u in het testvak **Metadata id-provider** de **App-URL voor federatieve metagegevens** die u uit de Azure-portal hebt gekopieerd.

    * Onder de sectie **Verificatie instellen** plakt u in het tekstvak **Kenmerkinstructies** tekstvak de URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * In de sectie **Aanmeldings-URL instellen** kopieert u de **aanmeldings-URL** voor uw exemplaar en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

        > [!Note]
        > U kunt de **aanmeldings-URL** aanpassen in overeenstemming met uw organisatie.

    * Klik op **Opslaan**.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot dmarcian.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **dmarcian** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-dmarcian-test-user"></a>Testgebruiker voor dmarcian maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij dmarcian, moeten ze worden ingericht in dmarcian. In het geval van dmarcian is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u bij dmarcian aan als een beveiligingsbeheerder.

2. Klik op **Profiel** in de rechterbovenhoek hoek en navigeer naar **Gebruiker beheren**.

    ![Gebruiker](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Aan de rechterkant van de sectie **SSO-gebruikers** klikt u op **Nieuwe gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Voer in de pop-up **Nieuwe gebruiker toevoegen** de volgende stappen uit:

    ![Nieuwe gebruiker](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Voer in het tekstvak Nieuwe **gebruiker e-mail** de e-mail van de gebruiker in, zoals **\@brittasimon contoso.com**.

    b. Als u beheerdersrechten aan de gebruiker wilt verlenen, selecteert u **Gebruiker beheerder maken**.

    c. Klik **op Gebruiker toevoegen**.

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel dmarcian in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van dmarcian waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

