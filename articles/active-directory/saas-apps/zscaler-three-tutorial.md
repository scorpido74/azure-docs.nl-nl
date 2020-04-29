---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Zscaler drie | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler drie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23abcf9a39ce7f6d77bc40e7143505bc68e8b72
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72554984"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Zscaler drie

In deze zelf studie leert u hoe u Zscaler kunt integreren met Azure Active Directory (Azure AD). Wanneer u Zscaler drie integreert met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot Zscaler drie.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden om drie met hun Azure AD-accounts te Zscaler.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Zscaler-abonnement dat drie keer eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Zscaler drie ondersteunt door **SP** GEÏNITIEERDe SSO

* Zscaler drie ondersteunt **just-in-time** -gebruikers inrichting

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-zscaler-three-from-the-gallery"></a>Zscaler drie van de galerie toevoegen

Als u de integratie van Zscaler drie wilt configureren in azure AD, moet u Zscaler drie van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **Zscaler drie** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **Zscaler** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Zscaler drie

Azure AD SSO met Zscaler drie configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler drie.

Als u Azure AD SSO wilt configureren en testen met Zscaler drie, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Zscaler drie SSO configureren](#configure-zscaler-three-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Zscaler drie test gebruiker](#create-zscaler-three-test-user)** : als u een equivalent van B. Simon wilt hebben in Zscaler drie dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de **Zscaler** -pagina voor het **beheren** van drie toepassings integratie en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    Typ een URL in het tekstvak **URL voor aanmelding** :`https://login.zscalerthree.net/sfc_sso`

1. Uw Zscaler-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

6. Daarnaast verwacht Zscaler drie toepassingen nog maar weinig kenmerken die kunnen worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.
    
    | Naam | Bronkenmerk |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    > [!NOTE]
    > Klik [hier](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) als u wilt weten hoe u rollen in Azure AD moet configureren

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de juiste URL ('s) op basis van uw vereiste op de **Zscaler drie sectie instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zscaler drie.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Zscaler drie**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen****Britta Simon** in de lijst en klik op de knop **Selecteren** onder aan het scherm.

    ![installatiekopie](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. In het dialoogvenster **Rol selecteren** kiest u de desbetreffende gebruikersrol in de lijst en klikt u vervolgens op de knop **Selecteren** onder aan het scherm.

    ![installatiekopie](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

    ![installatiekopie](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Zscaler drie SSO configureren

1. Als u de configuratie binnen Zscaler drie wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Setup Zscaler drie** wordt u naar de Zscaler drie-toepassing geleid. Geef de beheerders referenties op om u aan te melden bij Zscaler drie. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Instellen](common/setup-sso.png)

3. Als u Zscaler drie hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Zscaler drie bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Ga naar **Beheer > Verificatie > Verificatie-instellingen** en voer de volgende stappen uit:
   
    ![Beheer](./media/zscaler-three-tutorial/ic800206.png "Beheer")

    a. Kies onder Verificatietype de optie **SAML**.

    b. Klik op **SAML configureren**.

5. Voer in het venster **SAML bewerken** de volgende stappen uit en klik op Opslaan.  
            
    ![Gebruikers & verificatie beheren](./media/zscaler-three-tutorial/ic800208.png "Gebruikers & verificatie beheren")
    
    a. Plak in het tekstvak **SAML Portal URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    b. Voer in het tekstvak **Login Name Attribute****NameID** in.

    c. Klik op **Uploaden** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in de Azure-portal in het **openbare SSL-certificaat** hebt gedownload.

    d. Schakel **Enable SAML Auto-Provisioning** in.

    e. Voer in het tekstvak **User Display Name Attribute****displayName** in als u automatische inrichting van SAML wilt inschakelen voor displayName-kenmerken.

    f. Voer in het tekstvak **Group Name Attribute****memberOf** in als u automatische inrichting van SAML wilt inschakelen voor memberOf-kenmerken.

    g. Voer in het tekstvak **Department Name Attribute****department** in als u automatische inrichting van SAML wilt inschakelen voor department-kenmerken.

    h. Klik op **Opslaan**.

6. Voer in het dialoogvenster **Configure User Authentication** de volgende stappen uit:

    ![Beheer](./media/zscaler-three-tutorial/ic800207.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Klik op **Activeren**.

## <a name="configuring-proxy-settings"></a>Proxyinstellingen configureren
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>De proxyinstellingen configureren in Internet Explorer

1. Start **Internet Explorer**.

2. Selecteer **Internetopties** in het menu **Extra** om het dialoogvenster **Internetopties** te openen.   
    
     ![Internet opties](./media/zscaler-three-tutorial/ic769492.png "Internet opties")

3. Klik op het tabblad **Verbindingen**.   
  
     ![Verbindingen](./media/zscaler-three-tutorial/ic769493.png "Verbindingen")

4. Klik op **LAN-instellingen** om het dialoogvenster **LAN-instellingen** te openen.

5. In het gedeelte Proxyserver voert u de volgende stappen uit:   
   
    ![Proxy server](./media/zscaler-three-tutorial/ic769494.png "Proxyserver")

    a. Selecteer **Een proxyserver voor uw LAN-netwerk gebruiken**.

    b. Typ in het tekstvak adres de tekst **gateway. Zscaler Three.net**.

    c. Typ **80** in het tekstvak Poort.

    d. Selecteer **Proxyserver niet voor lokale adressen gebruiken**.

    e. Klik op **OK** om het dialoogvenster **LAN-instellingen** te sluiten.

6. Klik op **OK** om het dialoogvenster **Internetopties** te sluiten.

### <a name="create-zscaler-three-test-user"></a>Zscaler drie test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Zscaler drie. Zscaler drie ondersteunt just-in-time-inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet in Zscaler drie aanwezig is, wordt er een nieuwe gemaakt wanneer u Zscaler drie probeert te openen.

>[!Note]
>Als u hand matig een gebruiker moet maken, neemt u contact op met [Zscaler drie ondersteunings team](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Zscaler drie tegels in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Zscaler drie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Zscaler drie met Azure AD](https://aad.portal.azure.com/)

