---
title: 'Zelf studie: integratie Azure Active Directory met dmarcian | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68823693"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Zelf studie: dmarcian integreren met Azure Active Directory

In deze zelf studie leert u hoe u dmarcian integreert met Azure Active Directory (Azure AD). Wanneer u dmarcian integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot dmarcian.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij dmarcian met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* dmarcian-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* dmarcian ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-dmarcian-from-the-gallery"></a>Dmarcian vanuit de galerie toevoegen

Om de integratie van dmarcian in Azure AD te configureren, moet u dmarcian vanuit de galerij toevoegen aan uw lijst van beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **dmarcian** in het zoekvak.
1. Selecteer **dmarcian** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met dmarcian configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in dmarcian.

Als u Azure AD SSO wilt configureren en testen met dmarcian, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[DMARCIAN SSO configureren](#configure-dmarcian-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak een dmarcian-test gebruiker](#create-dmarcian-test-user)** -om een equivalent van B. Simon in dmarcian te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Dmarcian** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

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

1. Als u de configuratie wilt automatiseren in dmarcian, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup dmarcian** gaat u naar de dmarcian-toepassing. Geef de beheerders referenties op om u aan te melden bij dmarcian. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u dmarcian hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw dmarcian-bedrijfs site als beheerder en voert u de volgende stappen uit:

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

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan dmarcian.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **dmarcian** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

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

    a. In het tekstvak **nieuwe gebruiker e-mail** voert u het e-mail adres van de gebruiker in, zoals **\@brittasimon contoso.com**.

    b. Als u beheerdersrechten aan de gebruiker wilt verlenen, selecteert u **Gebruiker beheerder maken**.

    c. Klik op **gebruiker toevoegen**.

### <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel dmarcian in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van dmarcian waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

