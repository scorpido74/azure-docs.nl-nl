---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Splashtop | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Splashtop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c05f63c2-4170-49ce-a967-be1cb1dbcd06
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6ecb03130e26d432f0bd10980c7c3553ce9f8b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77539784"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Splashtop

In deze zelf studie leert u hoe u Splashtop integreert met Azure Active Directory (Azure AD). Wanneer u Splashtop integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Splashtop.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Splashtop met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Splashtop-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Splashtop ondersteunt door **SP** GEÏNITIEERDe SSO

* Zodra u de Splashtop hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Splashtop toevoegen uit de galerie

Als u de integratie van Splashtop in azure AD wilt configureren, moet u Splashtop uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Splashtop** in het zoekvak.
1. Selecteer **Splashtop** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Splashtop

Azure AD SSO met Splashtop configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Splashtop.

Als u Azure AD SSO wilt configureren en testen met Splashtop, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SPLASHTOP SSO configureren](#configure-splashtop-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Splashtop-test gebruiker](#create-splashtop-test-user)** -om een equivalent van B. Simon in Splashtop te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Splashtop** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    Typ in het tekstvak **URL voor aanmelding** de URL:`https://my.splashtop.com/login/sso`

1. De Splashtop-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. De volgende scherm afbeelding ziet u de lijst met standaard kenmerken, terwijl **nameidentifier** wordt toegewezen aan **User. userPrincipalName**. TicketManager-toepassing verwacht dat **nameidentifier** moeten worden toegewezen aan **User. mail**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar het gedeelte SAML **-** **handtekening certificaat** en selecteer vervolgens **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de sectie **Splashtop instellen** .

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Splashtop.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Splashtop**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-splashtop-sso"></a>Splashtop SSO configureren

In deze sectie moet u een nieuwe SSO-methode van [Splashtop-webportal](https://my.splashtop.com/login)Toep assen.
1. Ga in de Splashtop-webportal naar **account info** / **team** tabblad en schuif omlaag om de sectie voor **eenmalige aanmelding** te vinden. Klik vervolgens op **Toep assen voor nieuwe SSO-methode**.

    ![installatiekopie](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Geef in het venster Toep assen een **SSO-naam**op. Bijvoorbeeld, nieuw Azure, selecteer **Azure** als het type IDP en voer **aanmeldings-URL** en **Azure ad-id** in die is gekopieerd van Splashtop-toepassing op Azure Portal.

    ![installatiekopie](media/splashtop-tutorial/azure-sso-1.png)

1. Klik met de rechter muisknop op het certificaat bestand dat is gedownload van Splashtop toepassing op Azure Portal, bewerk het certificaat met Klad blok en kopieer de inhoud en plak deze in het veld **Download certificaat (base64)** .

    ![](media/splashtop-tutorial/cert-1.png) ![afbeelding](media/splashtop-tutorial/cert-2.png) afbeelding ![](media/splashtop-tutorial/azure-sso-2.png)

1. Dat is alles. Klik op **Opslaan** en Splashtop SSO-validatie team neemt contact met u op voor de verificatie gegevens en activeer vervolgens de SSO-methode.

### <a name="create-splashtop-test-user"></a>Splashtop-test gebruiker maken

1. Nadat de SSO-methode is geactiveerd, controleert u de zojuist gemaakte SSO-methode om deze in te scha kelen in de sectie voor **eenmalige aanmelding** .

    ![installatiekopie](media/splashtop-tutorial/enable.png)

1. `B.Simon@contoso.com` U kunt de test gebruiker bijvoorbeeld uitnodigen voor uw Splashtop-team met de ZOJUIST gemaakte SSO-methode.

    ![installatiekopie](media/splashtop-tutorial/invite.png)

1. U kunt ook een bestaand Splashtop-account wijzigen in een SSO-account. Zie de [instructies](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Dat is alles. U kunt het SSO-account gebruiken om u aan te melden bij de Splashtop-webportal of Splashtop Business-app.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Splashtop in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Splashtop waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Splashtop met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Splashtop beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)