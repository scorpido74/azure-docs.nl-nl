---
title: 'Zelf studie: integratie Azure Active Directory met Zscaler private Access (ZPA) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler private Access (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67085734"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Zelf studie: Zscaler private Access (ZPA) integreren met Azure Active Directory

In deze zelf studie leert u hoe u Zscaler private Access (ZPA) kunt integreren met Azure Active Directory (Azure AD). Wanneer u Zscaler private Access (ZPA) integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Zscaler private Access (ZPA).
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld voor Zscaler private Access (ZPA) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Zscaler private Access (ZPA) eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Zscaler private Access (ZPA) ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler private Access (ZPA) toevoegen vanuit de galerie

Als u de integratie van Zscaler private Access (ZPA) wilt configureren in azure AD, moet u Zscaler private Access (ZPA) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Zscaler persoonlijke toegang (ZPA)** in het zoekvak.
1. Selecteer **Zscaler private Access (ZPA)** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Zscaler private Access (ZPA) met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler private Access (ZPA).

Als u Azure AD SSO wilt configureren en testen met Zscaler private Access (ZPA), voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Zscaler private Access (ZPA) configureren](#configure-zscaler-private-access-zpa)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding voor Azure ad te testen met Julia Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
5. **[Maak Zscaler private Access (ZPA) test gebruiker](#create-zscaler-private-access-zpa-test-user)** een equivalent van Julia Simon in Zscaler private Access (ZPA) die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Zscaler private Access (ZPA)** Application Integration de sectie **Manage (beheren** ) en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Typ een URL in het vak **Id (Entiteits-id)**: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > De waarde **voor de aanmeldings-URL** is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Zscaler private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **Zscaler private Access (ZPA)** de juiste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Zscaler private Access (ZPA) configureren

1. Als u de configuratie wilt automatiseren binnen Zscaler private Access (ZPA), moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **Setup Zscaler private Access (ZPA)** naar de Zscaler-toepassing voor persoonlijke toegang (ZPA). Geef de beheerders referenties op om u aan te melden bij Zscaler private Access (ZPA). Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Zscaler private Access (ZPA) hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij de bedrijfs site van Zscaler private Access (ZPA) als beheerder en voert u de volgende stappen uit:

4. Klik aan de linkerkant van het menu op **beheer** en ga naar de sectie **verificatie** en klik op **IDP-configuratie**.

    ![Beheer van Zscaler private Access-beheerder](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Klik in de rechter bovenhoek op **IDP-configuratie toevoegen**. 

    ![Zscaler private Access Administrator IDP](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Voer de volgende stappen uit op de pagina **IDP-configuratie toevoegen** :
 
    ![Zscaler-beheerder voor persoonlijke toegang selecteren](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klik op **bestand selecteren** om het gedownloade meta gegevensbestand te uploaden vanuit Azure AD in het IDP-bestand voor het **uploaden van meta gegevens** .

    b. De **meta gegevens van IDP** worden gelezen uit Azure AD en alle veld gegevens worden ingevuld zoals hieronder wordt weer gegeven.

    ![Zscaler persoonlijke toegang beheerder config](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selecteer uw domein in het veld **domeinen** .
    
    d. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Zscaler private Access (ZPA).

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Zscaler private Access (ZPA)**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-zscaler-private-access-zpa-test-user"></a>Test gebruiker voor Zscaler private Access (ZPA) maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Zscaler private Access (ZPA). Ga naar het [ondersteunings team van Zscaler private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) om de gebruikers toe te voegen in het ZPA-platform (Zscaler private Access).

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Zscaler private Access (ZPA) in het toegangs venster selecteert, wordt u automatisch aangemeld bij de Zscaler private Access (ZPA) waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)