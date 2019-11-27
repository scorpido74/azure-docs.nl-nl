---
title: 'Zelf studie: integratie Azure Active Directory met Zendesk | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4887457f457ff533a0eaf10be7db1627a950f5bd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233273"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Zelf studie: integratie Azure Active Directory met Zendesk

In deze zelfstudie leert u hoe u Zendesk kunt integreren met Azure Active Directory (Azure AD).
De integratie van Zendesk met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Zendesk.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Zendesk (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Zendesk hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Zendesk waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zendesk ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

* Zendesk ondersteunt het [**geautomatiseerd** inrichten van gebruikers](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk toevoegen vanuit de galerie

Voor het configureren van de integratie van Zendesk in Azure AD moet u Zendesk uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Zendesk toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zendesk** in het zoekvak, selecteer **Zendesk** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Zendesk in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met Zendesk configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Zendesk tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met Zendesk wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Zendesk configureren](#configure-zendesk-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken in Zendesk](#create-zendesk-test-user)** : om in Zendesk een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Zendesk moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Zendesk**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Zendesk](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.zendesk.com`

   b. In het tekstvak **Id (entiteits-id)** typt u een URL met het volgende patroon: `https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In de Zendesk-toepassing wordt verwacht dat de SAML-asserties een specifieke indeling hebben. Er zijn geen verplichte SAML-kenmerken, maar u kunt kenmerken desgewenst beheren vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](common/new-save-attribute.png)

    ![installatiekopie](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Selecteer de juiste kenmerkwaarde in de lijst **Bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

    > [!NOTE]
    > U gebruikt extensiekenmerken om attributen toe te voegen die niet standaard in Azure AD zitten. Klik op [Gebruikerskenmerken die kunnen worden ingesteld in SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) om de volledige lijst van SAML-kenmerken op te halen die in **Zendesk** worden geaccepteerd.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** kopieert u in het gedeelte **SAML-handtekeningcertificaat** de **Vingerafdruk** en slaat u deze op op uw computer.

    ![Waarde van vingerafdruk kopiëren](common/copy-certificatethumbprint.png)

8. In de sectie **Zendesk instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

9. Er zijn twee manieren waarop Zendesk kan worden geconfigureerd: automatisch en handmatig.
  
10. Als u de configuratie in Zendesk wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![installatiekopie](./media/zendesk-tutorial/install_extension.png)

11. Als u op **Zendesk instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Zendesk-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij Zendesk. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en wordt de sectie **Eenmalige aanmelding met Zendesk configureren** geautomatiseerd.

    ![installatiekopie](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Eenmalige aanmelding voor Zendesk configureren

1. Als u Zendesk handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Zendesk-bedrijfssite. Voer daarna de volgende stappen uit:

2. Klik op **Admin**.

3. Klik in het linkernavigatievenster op **Settings** en vervolgens op **Security**.

4. Voer op de pagina **Security** de volgende stappen uit:

    ![Beveiliging](././media/zendesk-tutorial/ic773089.png "Beveiliging")

    ![Eenmalige aanmelding](././media/zendesk-tutorial/ic773090.png "Eenmalige aanmelding")

    a. Klik op het tabblad **Admin & Agents**.

    b. Selecteer **Single sign-on (SSO) and SAML** en selecteer vervolgens **SAML**.

    c. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Remote Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **Certificate Fingerprint** de waarde van **Vingerafdruk** die u hebt gekopieerd uit de Azure-portal.

    f. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Zendesk.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Zendesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Zendesk** in de lijst met toepassingen.

    ![De koppeling Zendesk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zendesk-test-user"></a>Een testgebruiker maken in Zendesk

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in Zendesk. Zendesk ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](Zendesk-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

> [!NOTE]
> **Eindgebruikers**accounts worden automatisch ingericht bij het aanmelden. **Agent**- en **beheerders**accounts moeten handmatig worden ingericht in **Zendesk** voordat u zich aanmeldt.

1. Meld u aan bij uw **Zendesk**-tenant.

2. Selecteer het tabblad **Customer List**.

3. Selecteer het tabblad **User** en klik op **Add**.

    ![Gebruiker toevoegen](././media/zendesk-tutorial/ic773632.png "Gebruiker toevoegen")
4. Typ in **Name** en **Email** de naam en het e-mailadres van een bestaand Azure AD-account dat u wilt inrichten en klik op **Save**.

    ![Nieuwe gebruiker](././media/zendesk-tutorial/ic773633.png "Nieuwe gebruiker")

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van Zendesk-gebruikers accounts of Api's die worden geleverd door Zendesk, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zendesk in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Zendesk waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Inrichten van gebruikers configureren](zendesk-provisioning-tutorial.md)