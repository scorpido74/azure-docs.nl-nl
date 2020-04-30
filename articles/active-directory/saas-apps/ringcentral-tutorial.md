---
title: 'Zelf studie: integratie Azure Active Directory met RingCentral | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: de7cf57d177902efdbb44524703481e8c65c75c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72991475"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Zelf studie: RingCentral integreren met Azure Active Directory

In deze zelf studie leert u hoe u RingCentral integreert met Azure Active Directory (Azure AD). Wanneer u RingCentral integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot RingCentral.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij RingCentral met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* RingCentral-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* RingCentral ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-ringcentral-from-the-gallery"></a>RingCentral toevoegen uit de galerie

Als u de integratie van RingCentral in azure AD wilt configureren, moet u RingCentral uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **RingCentral** in het zoekvak.
1. Selecteer **RingCentral** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met RingCentral met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RingCentral.

Als u Azure AD SSO wilt configureren en testen met RingCentral, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[RINGCENTRAL SSO configureren](#configure-ringcentral-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een RingCentral-test gebruiker](#create-ringcentral-test-user)** -om een equivalent van B. Simon in RingCentral te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **RingCentral** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    1. Klik op **Metagegevensbestand uploaden**.
    1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.
    1. Nadat het meta gegevensbestand is geüpload, worden de waarden voor de **id** en de **antwoord-URL** automatisch ingevuld in de **basis configuratie** sectie voor SAML.

    > [!Note]
    > U krijgt het **bestand met meta gegevens van de service provider** op de pagina RingCentral SSO-configuratie die verderop in de zelf studie wordt beschreven.

1. Als u geen **META gegevensbestand voor de service provider**hebt, voert u de waarden voor de volgende velden in:

    a. Typ een URL in het tekstvak **id** :

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Typ een URL in het tekstvak **Antwoord-URL**:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan RingCentral.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **RingCentral**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-ringcentral-sso"></a>RingCentral SSO configureren

1. Als u de configuratie wilt automatiseren in RingCentral, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **RingCentral instellen** gaat u naar de RingCentral-toepassing. Geef de beheerders referenties op om u aan te melden bij RingCentral. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u RingCentral hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw RingCentral-bedrijfs site als beheerder en voert u de volgende stappen uit:

1. Klik bovenaan op **extra**.

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral1.png)

1. Navigeer naar **eenmalige aanmelding**.

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral2.png)

1. Klik op de pagina **eenmalige aanmelding** onder **SSO-configuratie** in **stap 1** op **bewerken** en voer de volgende stappen uit:

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral3.png)

1. Voer de volgende stappen uit op de pagina **eenmalige aanmelding instellen** :

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klik op **Bladeren** om het meta gegevensbestand te uploaden dat u hebt gedownload van Azure Portal.

    b. Na het uploaden van meta gegevens worden de waarden automatisch ingevuld in de sectie **algemene informatie over SSO** .

    c. Selecteer in het gedeelte **kenmerk toewijzing** de optie **e-mail kenmerk toewijzen aan** als`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klik op **Opslaan**.

    e. Klik in **stap 2** op **downloaden** om het **META gegevensbestand van de service provider** te downloaden en dit te uploaden in de sectie **basis configuratie van SAML** om de **id** -en **antwoord-URL** -waarden in azure Portal automatisch in te vullen.

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Op dezelfde pagina gaat u naar de sectie **SSO inschakelen** en voert u de volgende stappen uit:

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecteer **SSO-service inschakelen**.

    * Selecteer **gebruikers toestaan zich aan te melden met SSO-of RingCentral-referentie**.

    * Klik op **Opslaan**.

### <a name="create-ringcentral-test-user"></a>RingCentral-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in RingCentral. Werk samen met het [RingCentral-client ondersteunings team](https://success.ringcentral.com/RCContactSupp) om de gebruikers toe te voegen in het RingCentral-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel RingCentral in het toegangs venster selecteert, wordt u automatisch aangemeld bij de RingCentral waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer RingCentral met Azure AD](https://aad.portal.azure.com/)