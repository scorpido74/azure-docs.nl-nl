---
title: 'Zelfstudie: Azure Active Directory-integratie met Work.com | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087084"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Zelfstudie: Azure Active Directory-integratie met Work.com

In deze zelfstudie leert u hoe u Work.com integreert met Azure Active Directory (Azure AD).
Het integreren van Work.com met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Work.com.
* U uw gebruikers automatisch laten aanmelden bij Work.com (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Work.com, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Work.com abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Work.com steunt **SP** geïnitieerde SSO

## <a name="adding-workcom-from-the-gallery"></a>Work.com toevoegen vanuit de galerie

Als u de integratie van Work.com in Azure AD wilt configureren, moet u Work.com uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Work.com uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Work.com**in het zoekvak , selecteer **Work.com** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Work.com in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Work.com op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Work.com.

Als u Azure AD-eenmaligaanmelding wilt configureren en testen met Work.com, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Work.com Enkele aanmelding](#configure-workcom-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Work.com testgebruiker](#create-workcom-test-user)** - om een tegenhanger van Britta Simon te hebben in Work.com die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

>[!NOTE]
>Als u eenmalige aanmelding wilt configureren, moet u nog een aangepaste Work.com-domeinnaam instellen. U moet ten minste een domeinnaam definiëren, uw domeinnaam testen en implementeren in uw hele organisatie.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Work.com te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Work.com-toepassingsintegratie** de optie Eén **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Work.com- en URL's één aanmeldingsgegevens](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [Work.com Client support team](https://help.salesforce.com/articleView?id=000159855&type=3) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Work.com instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-workcom-single-sign-on"></a>Work.com enkele aanmelding configureren

1. Meld u aan bij uw Work.com tenant als beheerder.

2. Ga naar **Setup**.
   
    ![Installatie](./media/work-com-tutorial/ic794108.png "Instellen")

3. Klik in het linkernavigatiedeelvenster in de sectie **Beheren** op **Domeinbeheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
   
    ![Mijn domein](./media/work-com-tutorial/ic767825.png "Mijn domein")

4. Als u wilt controleren of uw domein correct is ingesteld, controleert u of het zich in Stap**4 geïmplementeerd in gebruikers**bevindt en controleert u uw " Mijn**domeininstellingen**".
   
    ![Domein geïmplementeerd op gebruiker](./media/work-com-tutorial/ic784377.png "Domein geïmplementeerd op gebruiker")

5. Meld u aan bij uw Work.com huurder.

6. Ga naar **Setup**.
    
    ![Installatie](./media/work-com-tutorial/ic794108.png "Instellen")

7. Vouw het menu **Beveiligingsbesturingselementen** uit en klik op **Instellingen voor eenmalig aanmelden**.
    
    ![Instellingen voor eenmalig aanmelden](./media/work-com-tutorial/ic794113.png "Instellingen voor eenmalig aanmelden")

8. Voer op de pagina **Instellingen voor aanmeldingsinstellingen** de volgende stappen uit:
    
    ![SAML ingeschakeld](./media/work-com-tutorial/ic781026.png "SAML ingeschakeld")
    
    a. Selecteer **SAML Enabled**.
    
    b. Klik op **Nieuw**.

9. Voer in de sectie **SAML Single Sign-On Settings** de volgende stappen uit:
    
    ![INSTELLING VOOR eenmalig aanmelden van SAML](./media/work-com-tutorial/ic794114.png "INSTELLING VOOR eenmalig aanmelden van SAML")
    
    a. Typ in het tekstvak **Name** een naam voor de configuratie.  
       
    > [!NOTE]
    > Als u een waarde voor **Naam** opgeeft, wordt het tekstvak **API-naam** automatisch ingevuld.
    
    b. Plak **in het** tekstvak Uitgever de waarde van Azure **AD-id** die u hebt gekopieerd vanuit Azure-portal.
    
    c. Als u het gedownloade certificaat wilt uploaden vanuit de Azure-portal, klikt u op **Bladeren**.
    
    d. Typ in het tekstvak `https://salesforce-work.com` **Entiteit-id** .
    
    e. Als **SAML-identiteitstype** **selecteert u De federatie-id van het object Gebruiker**.
    
    f. Als **SAML-identiteitslocatie**selecteert u **Identiteit in het element NameIdentfier van de instructie Onderwerp**.
    
    g. Plak in het tekstvak **URL van de login-account** van de gebruikersgroep de waarde van **de aanmeldings-URL** die u hebt gekopieerd vanuit azure-portal.

    h. Plak in **het tekstvak URL-logboeken** van de identiteitsprovider de waarde van **de URL van afmelden** die u hebt gekopieerd uit azure-portal.
    
    i. Selecteer **HTTP-bericht**als **serviceprovider in geïnitieerde aanvraagbinding**.
    
    j. Klik op **Opslaan**.

10. Klik in uw Work.com klassieke portal in het linkernavigatiedeelvenster op **Domeinbeheer** om de gerelateerde sectie uit te vouwen en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
    
    ![Mijn domein](./media/work-com-tutorial/ic794115.png "Mijn domein")

11. Klik op de pagina **Mijn domein** in de sectie **Aanmeldingspagina branding** op **Bewerken**.
    
    ![Aanmeldingspaginabranding](./media/work-com-tutorial/ic767826.png "Aanmeldingspaginabranding")

12. Op de pagina **Aanmeldingspagina branding** wordt in de sectie **Verificatieservice** de naam van uw **SAML SSO-instellingen** weergegeven. Selecteer deze en klik op **Opslaan**.
    
    ![Aanmeldingspaginabranding](./media/work-com-tutorial/ic784366.png "Aanmeldingspaginabranding")

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Work.com.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens Work.com**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Work.com**.

    ![De koppeling Work.com in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-workcom-test-user"></a>Work.com testgebruiker maken

Als Azure Active Directory-gebruikers zich kunnen aanmelden, moeten ze zijn ingericht om Work.com. In het geval van Work.com is inrichten een handmatige taak.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Voer de volgende stappen uit om de inrichting van gebruikers te configureren:

1. Meld u aan bij uw Work.com bedrijfssite als beheerder.

2. Ga naar **Setup**.
   
    ![Installatie](./media/work-com-tutorial/IC794108.png "Instellen")

3. Ga naar **Gebruikers \> beheren**.
   
    ![Gebruikers beheren](./media/work-com-tutorial/IC784369.png "Gebruikers beheren")

4. Klik op **New User**.
   
    ![Alle gebruikers](./media/work-com-tutorial/IC794117.png "Alle gebruikers")

5. Voer in de sectie Gebruikersbewerking de volgende stappen uit in kenmerken van een geldig Azure AD-account dat u wilt inrichten in de gerelateerde tekstvakken:
   
    ![Gebruiker bewerken](./media/work-com-tutorial/ic794118.png "Gebruiker bewerken")
   
    a. Typ in het tekstvak **Voornaam** de **voornaam** van de gebruiker **Britta**.
    
    b. Typ in het tekstvak **Achternaam** de **achternaam** van de gebruiker **Simon**.
    
    c. Typ in het tekstvak **Alias** de **naam** van de gebruiker **BrittaS**.
    
    d. Typ in het tekstvak **E-mail** het **e-mailadres** van de gebruiker Brittasimon@contoso.com.
    
    e. Typ in het tekstvak **Gebruikersnaam** een gebruikersnaam Brittasimon@contoso.comvan de gebruiker als .
    
    f. Typ **in** het tekstvak Nick Name een **nicknaam** van gebruiker **Simon**.
    
    g. Selecteer **Rol,** **Gebruikerslicentie**en **Profiel**.
    
    h. Klik op **Opslaan**.  
      
    > [!NOTE]
    > De houder van het Azure AD-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.
    > 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Work.com-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Work.com waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

