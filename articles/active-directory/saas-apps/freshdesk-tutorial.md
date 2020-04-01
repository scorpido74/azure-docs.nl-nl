---
title: 'Zelfstudie: Azure Active Directory-integratie met FreshDesk | Microsoft Documenten'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227620"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Zelfstudie: Azure Active Directory-integratie met FreshDesk

In deze zelfstudie leert u hoe u FreshDesk kunt integreren met Azure Active Directory (Azure AD).
De integratie van FreshDesk met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot FreshDesk.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij FreshDesk (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met FreshDesk hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op FreshDesk waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FreshDesk ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-freshdesk-from-the-gallery"></a>FreshDesk toevoegen vanuit de galerie

Om de integratie van FreshDesk te configureren in Azure AD, moet u FreshDesk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om FreshDesk vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **FreshDesk** in het zoekvak, selecteer **FreshDesk** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![FreshDesk toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met FreshDesk op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in FreshDesk tot stand is gebracht.

Om eenmalige aanmelding van Azure AD met FreshDesk te configureren en testen, moet u de volgende procedures voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor FreshDesk configureren](#configure-freshdesk-single-sign-on)**: de instellingen voor eenmalige aanmelding configureren aan de toepassingszijde.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor FreshDesk maken](#create-freshdesk-test-user)**: als u een tegenhanger van Britta Simon in FreshDesk wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met FreshDesk:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **FreshDesk** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van FreshDesk](common/sp-identifier.png)

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende notatie: `https://<tenant-name>.freshdesk.com` of een andere waarde die door Freshdesk is voorgesteld.

    b. Typ in het tekstvak **Id (Entiteits-id)** een URL met de volgende notatie: `https://<tenant-name>.freshdesk.com` of een andere waarde die door Freshdesk is voorgesteld.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [klantondersteuningsteam van FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In FreshDesk worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermopname ziet u een voorbeeld hiervan. De standaardwaarde van **Unieke gebruikers-id** is **user.userprincipalname**, maar in FreshDesk wordt verwacht dat aan deze waarde het e-mailadres van de gebruiker is toegewezen. Hiervoor kunt u het kenmerk **user.mail** in de lijst gebruiken of de juiste kenmerkwaarde op basis van uw organisatieconfiguratie. 

    ![installatiekopie](common/edit-attribute.png)

6. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit:
    
    | Name | Bronkenmerk |
    | ---------------| --------------- |
    | Unieke gebruikers-id | user.mail |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](common/new-save-attribute.png)

    ![installatiekopie](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. Open een **opdrachtprompt** en voer de volgende opdracht uit:

    a. Voer `certutil.exe -dump FreshDesk.cer` uit op de opdrachtprompt.

    > [!NOTE]
    > Hier is **FreshDesk.cer** het certificaat dat u hebt gedownload uit de Azure-portal.

    b. Kopieer de waarde **Cert Hash(sha256)** en plak deze in Kladblok. 

9. Kopieer in de sectie **FreshDesk instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-freshdesk-single-sign-on"></a>Eenmalige aanmelding configureren voor FreshDesk

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Freshdesk.

2. Selecteer het pictogram **Settings** en voer in de sectie **Security** de volgende stappen uit:

    ![Eenmalige aanmelding](./media/freshdesk-tutorial/IC776770.png "Eenmalige aanmelding")
  
    a. Zet **Single Sign On (SSO)** op **On** (vinkje).

    b. Selecteer **SAML SSO**.

    c. Plak in het tekstvak **SAML Login URL** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Plak in het tekstvak **Security Certificate Fingerprint** de waarde van **Cert Hash(sha256)** die u eerder hebt ontvangen.
  
    f. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

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

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-freshdesk-test-user"></a>Testgebruiker maken voor FreshDesk

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij FreshDesk, moeten ze worden ingericht bij FreshDesk.  
In het geval van FreshDesk is dat een handmatige taak.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u aan bij uw **Freshdesk**-tenant.

2. Klik in het menu bovenaan op **Admin**.

    ![Beheerder](./media/freshdesk-tutorial/IC776772.png "Beheerder")

3. Klik op het tabblad **General Settings** op **Agents**.
  
    ![Agents](./media/freshdesk-tutorial/IC776773.png "Agents")

4. Klik op **New Agent**.

    ![Nieuwe agent](./media/freshdesk-tutorial/IC776774.png "Nieuwe agent")

5. Voer de volgende stappen uit in het dialoogvenster Agent Information:

    ![Agentinformatie](./media/freshdesk-tutorial/IC776775.png "Agentinformatie")

    a. Typ in het tekstvak **Email** het e-mailadres van het Azure AD-account dat u wilt inrichten.

    b. Typ in het tekstvak **Full name** de naam van het Azure AD-account dat u wilt inrichten.

    c. Typ in het tekstvak **Title** de titel van het Azure AD-account dat u wilt inrichten.

    d. Klik op **Opslaan**.

    >[!NOTE]
    >De houder van het Azure AD-account krijgt een e-mailbericht met een koppeling om het account te bevestigen voordat dit wordt geactiveerd.
    >
    >[!NOTE]
    >U alle andere tools voor het maken van gebruikersaccounts van Freshdesk gebruiken die door Freshdesk worden verstrekt om Azure AD-gebruikersaccounts in te richten op FreshDesk.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel FreshDesk klikt, wordt u automatisch aangemeld bij de instantie van FreshDesk waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

