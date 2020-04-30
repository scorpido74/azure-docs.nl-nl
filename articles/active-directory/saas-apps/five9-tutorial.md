---
title: 'Zelf studie: integratie Azure Active Directory met Five9 plus-adapter (CTI, contact center-agents) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Five9 plus-adapter (CTI, contact center-agents).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: dd4eab2d8e877cac468097c0a6ed6aa3cfb750b9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102456"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Zelf studie: integratie Azure Active Directory met Five9 plus-adapter (CTI, contact center-agents)

In deze zelf studie leert u hoe u Five9 plus-adapter (CTI, contact center-agents) integreert met Azure Active Directory (Azure AD).
Het integreren van Five9 plus-adapter (CTI, contact center-agents) met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de Five9 plus-adapter (CTI, contact center-agents).
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Five9 plus-adapter (CTI, contact center-agents) (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Five9 plus-adapter (CTI, contact center-agents) wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Five9 plus-adapter (CTI, contact center-agents) ingeschakeld abonnement voor eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Five9 plus-adapter (CTI, contact center-agents) ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Five9 plus-adapter (CTI, contact center-agents) toevoegen vanuit de galerie

Als u de integratie van Five9 plus-adapter (CTI, contact center-agents) wilt configureren in azure AD, moet u Five9 plus-adapter (CTI-, contact punt-agents) toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Five9 plus-adapter (CTI, contact center-agents) toe te voegen aan de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Five9 plus adapter (CTI, contact center-agents)**, selecteer **Five9 plus adapter (CTI, contact center-agents)** in het resultaten paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Five9 plus-adapter (CTI, contact center agents) in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Five9 plus-adapter (CTI, contact center-agents) op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Five9 plus-adapter (CTI, contact center-agents) tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Five9 plus-adapter (CTI, contact center-agents), moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer de Five9 plus-adapter (CTI, contact center-agents) eenmalige aanmelding](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Five9 plus-adapter (CTI, contact center-agents) test gebruiker](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** -om een equivalent van Julia Simon te hebben in Five9 plus-adapter (CTI, contact center-agents) die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Five9 plus-adapter (CTI, contact center-agents):

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de Application Integration-pagina van de **Five9 plus-adapter (CTI, contact center-agents)** de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Five9 plus-adapter (CTI, contact center-agents) domein-en URL-informatie over eenmalige aanmelding](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 
    
    |    Omgeving      |       URL      |
    | :-- | :-- |
    | Voor ' Five9 plus-adapter voor micro soft Dynamics CRM ' | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Voor ' Five9 plus-adapter voor Zendesk ' | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Voor ' Five9 plus-adapter voor Agent Desktop Toolkit ' | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    |      Omgeving     |      URL      |
    | :--                  | :--           |
    | Voor ' Five9 plus-adapter voor micro soft Dynamics CRM ' | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Voor ' Five9 plus-adapter voor Zendesk ' | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Voor ' Five9 plus-adapter voor Agent Desktop Toolkit ' | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer op de sectie **Five9 plus-adapter instellen (CTI, contact center-agents)** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Five9 plus-adapter (CTI, contact center-agents) configureren eenmalige aanmelding

1. Voor het configureren van eenmalige aanmelding op **Five9 plus-adapter (CTI, contact center-agents)** , moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde URL ('s) verzenden naar het [ondersteunings team van de FIVE9 plus-adapter (CTI, contact center-agents)](https://www.five9.com/about/contact). Daarnaast moet u voor het configureren van eenmalige aanmelding de onderstaande stappen volgen op basis van de adapter:

    a. Beheerders handleiding voor de Five9 plus-adapter voor de agent Desktop Toolkit:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Beheerders handleiding voor de Five9 plus-adapter voor micro soft Dynamics CRM:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Beheerders handleiding voor de Five9 plus-adapter voor Zendesk:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen aan Five9 plus-adapter (CTI, contact center-agents).

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **FIVE9 plus-adapter (CTI, contact center-agents)**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **Five9 plus-adapter (CTI, contact center-agents)**.

    ![De koppeling van de Five9 plus-adapter (CTI, contact center agents) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 plus-adapter (CTI, contact center-agents) test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Five9 plus adapter (CTI, contact center-agents). Werk met het [ondersteunings team van Five9 plus-adapter (CTI, contact center-agents)](https://www.five9.com/about/contact) om de gebruikers toe te voegen in het platform Five9 plus adapter (CTI, contact center agents). Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Five9 plus adapter (CTI, contact center agents in het toegangs venster) klikt, moet u automatisch worden aangemeld bij de Five9 plus-adapter (CTI, contact center-agents) waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

