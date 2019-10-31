---
title: 'Zelf studie: integratie met webmethodes integratie pakket Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen de integratie suite van Azure Active Directory en webmethoden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97261535-7a2d-4d73-94c8-38116b8a776e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 30fc55cb608189e1be82c2b28393c5e242e262fd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160861"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Zelf studie: integratie met webmethodes integratie pakket Azure Active Directory

In deze zelf studie leert u hoe u integratie pakket met webmethodes integreert met Azure Active Directory (Azure AD).
Het integreren van webmethoden integratie pakket met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de integratie suite van webmethoden.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij de integratie Suite webmethoden (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig voor het configureren van Azure AD-integratie met integratie pakket voor webmethoden:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* webmethodes integratie pakket met eenmalige aanmelding ingeschakeld abonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* de integratie suite van webmethoden ondersteunt SSO met **SP** en **IDP**

* Integratie pakket webmethoden ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-webmethods-integration-suite-from-the-gallery"></a>Integratie pakket voor webmethodes toevoegen vanuit de galerie

Als u de integratie van integratie pakketten van webmethodes wilt configureren in azure AD, moet u de integratie Suite webmethoden toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om de integratie Suite webmethoden toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Webmethodes Integration Suite**in het zoekvak, selecteer **integratie pakket met webmethodes** in het paneel resultaten en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Integratie pakket webmethoden in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met webmethodes integratie pakket op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de integratie pakket met webmethodes tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met de webmethodes Integration Suite, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Webmethodes configureren integratie pakket eenmalige aanmelding](#configure-webmethods-integration-suite-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. Een **[Webmethodes Integration Suite-gebruiker maken](#create-webmethods-integration-suite-test-user)** -een soort tegen partij Julia Simon in webmethodes integratie pakket dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met de webmethodes Integration Suite:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **webmethoden Integration Suite** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de **integratie-Cloud voor webmethoden**wilt configureren, voert u in de sectie **basis configuratie van SAML** de volgende stappen uit als u de toepassing in de gestarte modus **IDP** wilt configureren:

    ![informatie over webmethodes integratie Suite domein en Url's eenmalige aanmelding](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | |
    |--|
    | `<SUBDOMAIN>.webmethodscloud.com` |
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse` |

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![informatie over webmethodes integratie Suite domein en Url's eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    d. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met de [Webmethodes Integration Suite-ondersteunings team](https://empower.softwareag.com/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Voer de volgende stappen uit als u de **API-Cloud voor webmethoden**wilt configureren in het gedeelte **basis configuratie van SAML** als u de toepassing in de gestarte modus **IDP** wilt configureren:

    ![informatie over webmethodes integratie Suite domein en Url's eenmalige aanmelding](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | |
    |--|
    | `<SUBDOMAIN>.webmethodscloud.com` |
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso` |

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![informatie over webmethodes integratie Suite domein en Url's eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    d. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>` |
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>` |
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met de [Webmethodes Integration Suite-ondersteunings team](https://empower.softwareag.com/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer op de sectie **Webmethodes integratie pakket instellen** de juiste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-webmethods-integration-suite-single-sign-on"></a>Webmethodes configureren integratie pakket eenmalige aanmelding

Voor het configureren van eenmalige aanmelding op **webmethoden integratie pakket** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [webmethodes Integration Suite ondersteunings team](https://empower.softwareag.com/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot de integratie suite van webmethoden.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **webmethoden integratie pakket**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **webmethoden integratie Suite**.

    ![De koppeling naar de integratie suite van webmethodes in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-webmethods-integration-suite-test-user"></a>Webmethodes maken integratie Suite gebruiker testen

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in integratie pakket met webmethoden. de integratie suite van webmethoden ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al bestaat in de integratie Suite voor webmethoden, wordt er na verificatie een nieuwe gemaakt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel webmethodes Integration Suite in het toegangs venster klikt, moet u automatisch worden aangemeld bij de integratie suite van webmethoden waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

