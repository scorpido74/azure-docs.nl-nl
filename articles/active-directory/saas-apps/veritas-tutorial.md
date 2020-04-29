---
title: 'Zelf studie: integratie Azure Active Directory met de Veritas Enter prise-kluis. Cloud-SSO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de Veritas Enter prise-kluis. Cloud-SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 43094cabab3cfc93e0dffa59a15867d01b036d38
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67087606"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Zelf studie: integratie Azure Active Directory met de Veritas Enter prise-kluis. Cloud-SSO

In deze zelf studie leert u hoe u de Veritas Enter prise-kluis integreert. Cloud-SSO met Azure Active Directory (Azure AD).
Het integreren van de Veritas Enter prise-kluis. Cloud-SSO met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de Veritas Enter prise-kluis. Cloud-SSO.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij de Veritas Enter prise-kluis. Cloud-SSO (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met de Veritas Enter prise-kluis. Cloud-SSO, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Veritas Enter prise-kluis. Cloud SSO-abonnement voor eenmalige aanmelding ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Veritas Enter prise-kluis. Cloud-SSO ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>De Veritas Enter prise-kluis toevoegen. Cloud-SSO vanuit de galerie

Als u de integratie van de Veritas Enter prise-kluis wilt configureren. Cloud-SSO in azure AD, moet u de Veritas Enter prise-kluis. Cloud-SSO van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Veritas Enter prise kluis. Cloud-SSO uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak de tekst **Veritas Enter prise kluis. Cloud-SSO**, selecteer **Veritas Enter prise kluis. Cloud-SSO** in het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Veritas Enter prise-kluis. Cloud-SSO in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met de Veritas Enter prise-kluis. Cloud-SSO op basis van een test gebruiker met de naam **Julia Simon**.
Voor eenmalige aanmelding werkt een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Veritas Enter prise-kluis. Cloud-SSO moet worden ingesteld.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met de Veritas Enter prise-kluis. Cloud-SSO, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer de Veritas Enter prise-kluis. eenmalige aanmelding voor Cloud-SSO](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Veritas Enter prise-kluis. Cloud-SSO testen gebruiker](#create-veritas-enterprise-vaultcloud-sso-test-user)** : voor een soort Julia Simon in de Veritas Enter prise-kluis. Cloud-SSO die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met de Veritas Enter prise-kluis. Cloud-SSO:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de **Veritas Enter prise-kluis** de pagina voor de integratie van de Cloud SSO **-** toepassing.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Veritas Enter prise-kluis. Cloud-SSO-domein en Url's eenmalige aanmelding](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. In het vak **id** gebruikt u de URL volgens het Data Center:

    | Datacenter| URL |
    |----------|----|
    | Noord-Amerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azië en Stille Oceaan| `https://auth.syd.archivecloud.net`|

    c. Gebruik in het tekstvak **antwoord-URL** de URL volgens het Data Center:

    | Datacenter| URL |
    |----------|----|
    | Noord-Amerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azië en Stille Oceaan| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met de [Veritas Enter prise-kluis. Cloud SSO client support team](https://www.veritas.com/support/.html) om deze waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Op de **Veritas Enter prise-kluis instellen. Cloud-SSO** sectie Kopieer de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>De Veritas Enter prise-kluis configureren. eenmalige aanmelding voor Cloud SSO

Voor het configureren van eenmalige aanmelding op een **Veritas Enter prise-kluis. Cloud-SSO** -zijde moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar de [Veritas Enter PRISE-kluis. Cloud SSO-ondersteunings team](https://www.veritas.com/support/.html). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de Veritas Enter prise-kluis. Cloud-SSO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Veritas Enter PRISE kluis. Cloud-SSO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Veritas Enter prise kluis. Cloud-SSO**.

    ![De Veritas Enter prise-kluis. Cloud-SSO-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Een Veritas Enter prise-kluis maken. Cloud-SSO test gebruiker

In deze sectie maakt u een gebruiker met de naam Julia Simon in de Veritas Enter prise-kluis. Cloud-SSO. Werk met de [Veritas Enter prise-kluis. Cloud SSO-ondersteunings team](https://www.veritas.com/support/.html) om de gebruikers toe te voegen in de Veritas Enter prise-kluis. Cloud-SSO-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Veritas Enter prise-kluis klikt. Cloud SSO-tegel in het toegangs venster, moet u automatisch worden aangemeld bij de Veritas Enter prise-kluis. Cloud-SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

