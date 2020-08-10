---
title: 'Zelfstudie: Azure Active Directory-integratie met FreshDesk | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en FreshDesk configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b7188708c44d00d075a02d54b0a3b10cbc81954
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513519"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Zelfstudie: Azure Active Directory-integratie met FreshDesk

In deze zelfstudie leert u hoe u FreshDesk kunt integreren met Azure Active Directory (Azure AD).
De integratie van FreshDesk met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot FreshDesk.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij FreshDesk (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met FreshDesk hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op FreshDesk waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FreshDesk ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)
* Zodra u FreshDesk hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>FreshDesk toevoegen vanuit de galerie

Om de integratie van FreshDesk te configureren in Azure AD, moet u FreshDesk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **FreshDesk**.
1. Selecteer **FreshDesk** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshdesk"></a>Eenmalige aanmelding van Azure AD configureren en testen voor FreshDesk

Configureer en test eenmalige aanmelding bij Azure AD met FreshDesk met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FreshDesk.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met FreshDesk te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Eenmalige aanmelding voor FreshDesk configureren](#configure-freshdesk-single-sign-on)** : de instellingen voor eenmalige aanmelding configureren aan de toepassingszijde.
    1. **[Een testgebruiker voor FreshDesk maken](#create-freshdesk-test-user)** : als u een tegenhanger van Britta Simon in FreshDesk wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

## <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **FreshDesk** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende notatie: `https://<tenant-name>.freshdesk.com` of een andere waarde die door FreshDesk is voorgesteld.

    b. Typ in het tekstvak **Id (Entiteits-id)** een URL met de volgende notatie: `https://<tenant-name>.freshdesk.com` of een andere waarde die door Freshdesk is voorgesteld.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In FreshDesk worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermopname ziet u de lijst met standaardkenmerken, waarbij **Unieke gebruikers-id** is toegewezen aan **user.userprincipalname** terwijl FreshDesk verwacht dat deze claim moet worden toegewezen aan **user.mail**. Dit betekent dat u de kenmerktoewijzing moet bewerken door op het pictogram Bewerken te klikken en de kenmerktoewijzing aan te passen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **FreshDesk instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.


### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot FreshDesk.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **FreshDesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **FreshDesk** in de lijst met toepassingen.

    ![De koppeling naar FreshDesk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-freshdesk-single-sign-on"></a>Eenmalige aanmelding configureren voor FreshDesk

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Freshdesk.

2. Selecteer het pictogram **Settings** en voer in de sectie **Security** de volgende stappen uit:

    ![Eenmalige aanmelding](./media/freshdesk-tutorial/configure-1.png "Eenmalige aanmelding")
  
    a. Selecteer **On** voor **Single Sign On**.

    b. Selecteer **SAML SSO** bij **Login Method**.

    c. Plak in het tekstvak **Entity ID provided by the IdP** de waarde van **Entiteit-id** die u uit de Azure-portal hebt gekopieerd.

    d. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Selecteer **Only Signed Assertions** in de vervolgkeuzelijst  **Signing Options**.

    f. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    g. Plak in het tekstvak **Security Certificate** de waarde van **Certificaat (Base64)** die u eerder hebt vastgesteld.
  
    h. Klik op **Opslaan**.

## <a name="create-freshdesk-test-user"></a>Testgebruiker maken voor FreshDesk

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij FreshDesk, moeten ze worden ingericht bij FreshDesk.  
In het geval van FreshDesk is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Freshdesk**-tenant.

1. Klik in het menu aan de linkerkant op **Admin** en klik op het tabblad **General Settings** op **Agents**.
  
    ![Agents](./media/freshdesk-tutorial/create-user-1.png "Agents")

1. Klik op **New Agent**.

    ![New Agent](./media/freshdesk-tutorial/create-user-2.png "New Agent")

1. Voer in het dialoogvenster Agent Information de vereiste velden in en klik op **Create agent**.

    ![Agent Information](./media/freshdesk-tutorial/create-user-3.png "Agent Information")

    >[!NOTE]
    >De houder van het Azure AD-account krijgt een e-mailbericht met een koppeling om het account te bevestigen voordat dit wordt geactiveerd.
    >
    >[!NOTE]
    >U kunt de AAD-gebruikersaccounts ook inrichten met behulp van andere hulpprogramma's of API's van Freshdesk voor het maken van Azure AD-gebruikersaccounts.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel FreshDesk klikt, wordt u automatisch aangemeld bij de instantie van FreshDesk waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

