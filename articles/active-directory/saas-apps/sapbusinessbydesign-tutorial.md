---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091678"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Zelfstudie: Azure Active Directory-integratie met SAP Business ByDesign

In deze zelfstudie leert u hoe u SAP Business ByDesign integreert met Azure Active Directory (Azure AD).
De integratie van SAP Business ByDesign met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot SAP Business ByDesign.
* U uw gebruikers automatisch laten inloggen op SAP Business ByDesign (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met SAP Business ByDesign, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* SAP Business ByDesign-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Business ByDesign ondersteunt **DOOR SP** geïnitieerde SSO

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business ByDesign toevoegen vanuit de galerie

Als u de integratie van SAP Business ByDesign in Azure AD wilt configureren, moet u SAP Business ByDesign vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SAP Business ByDesign vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **SAP**Business ByDesign in het zoekvak , selecteer **SAP Business ByDesign** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![SAP Business ByDesign in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met SAP Business ByDesign op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP Business ByDesign.

Als u Azure AD single sign-aan wilt configureren en testen met SAP Business ByDesign, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer SAP Business ByDesign Single Sign-On](#configure-sap-business-bydesign-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Sap Business ByDesign-testgebruiker maken](#create-sap-business-bydesign-test-user)** - om een tegenhanger van Britta Simon in SAP Business ByDesign te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met SAP Business ByDesign te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **SAP Business ByDesign-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![SAP Business ByDesign-domein en URL's met eenmalige aanmeldingsgegevens](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<servername>.sapbydesign.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van SAP Business ByDesign Client](https://www.sap.com/products/cloud-analytics.support.html) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. SAP Business ByDesign applicatie verwacht de SAML beweringen in een specifiek formaat. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

6. Klik op het pictogram **Bewerken** om de waarde van de **naam-id**te bewerken.

    ![installatiekopie](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Voer in de sectie **Gebruikersclaims** beheren ![de volgende stappen uit: afbeelding](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecteer **Transformatie** als **bron**.

    b. Selecteer in de vervolgkeuzelijst **Transformatie** de optie **ExtractMailPrefix()**.

    c. Selecteer **in** de vervolgkeuzelijst Parameter 1 het gebruikerskenmerk dat u wilt gebruiken voor uw implementatie. Als u bijvoorbeeld de werknemer-id wilt gebruiken als unieke gebruikers-id en u de waarde van het kenmerk in de ExtensionAttribute2 hebt opgeslagen, selecteert u vervolgens user.extensionattribute2.

    d. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

9. Kopieer in de sectie **SAP Business ByDesign** de juiste URL(s) naar uw behoefte.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>SAP Business ByDesign-aanmelding configureren

1. Meld u aan bij uw SAP Business ByDesign-portal met beheerdersrechten.

2. Navigeer naar **Algemene taak voor toepassing en gebruikersbeheer** en klik op het tabblad **Identiteitsprovider.**

3. Klik op **Nieuwe identiteitsprovider** en selecteer het XML-bestand met metagegevens dat u hebt gedownload van de Azure-portal. Door het importeren van de metagegevens worden automatisch het vereiste handtekeningcertificaat en versleutelingscertificaat geüpload.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Als u de URL van de **Bevestigingsconsumentenservice** wilt opnemen in het SAML-verzoek, selecteert u **De URL van de Bevestigingsservice van de consument opnemen**.

5. Klik op **Eenmalige aanmelding activeren**.

6. Sla uw wijzigingen op.

7. Klik op het tabblad **Mijn systeem**.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Plak in het tekstvak **AZURE AD Sign On URL** de **url-waarde van aanmelding,** die u hebt gekopieerd van de Azure-portal.

    ![Eenmalige aanmelding configureren](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Geef op of de werknemer handmatig kan kiezen tussen inloggen met gebruikersnaam en wachtwoord of SSO door **Handmatige identiteitsproviderselectie te selecteren.**

10. Geef in de sectie **SSO URL** de URL op die de werknemer moet gebruiken om zich bij het systeem aan te melden.
    In de vervolgkeuzelijst URL verzonden naar werknemer u kiezen tussen de volgende opties:

    **Niet-SSO-URL**

    Alleen de normale systeem-URL wordt naar de werknemer verstuurd. De werknemer kan zich niet aanmelden met SSO en moet in plaats daarvan een wachtwoord of certificaat gebruiken.

    **SSO-URL**

    Alleen de SSO-URL wordt naar de werknemer verzonden. De werknemer kan signon met behulp van SSO. Een verificatieaanvraag wordt omgeleid via de IdP.

    **Automatische selectie**

    Als eenmalige aanmelding niet actief is, wordt alleen de normale systeem-URL naar de werknemer verstuurd. Als eenmalige aanmelding actief is, controleert het systeem of de werknemer een wachtwoord heeft. Als er een wachtwoord beschikbaar is, worden zowel de SSO-URL als de niet-SSO-URL naar de werknemer verzonden. Als de werknemer geen wachtwoord heeft, wordt echter alleen de URL voor eenmalige aanmelding naar de werknemer verzonden.

11. Sla uw wijzigingen op.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot SAP Business ByDesign.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer vervolgens SAP **Business ByDesign**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer SAP Business **ByDesign**in de lijst met toepassingen .

    ![De SAP Business ByDesign-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sap-business-bydesign-test-user"></a>Sap Business ByDesign-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in SAP Business ByDesign. Werk samen met [het SAP Business ByDesign Client Support-team](https://www.sap.com/products/cloud-analytics.support.html) om de gebruikers toe te voegen aan het SAP Business ByDesign-platform. 

> [!NOTE]
> Zorg ervoor dat de waarde van NameID overeenkomt met het gebruikersnaamveld in het SAP Business ByDesign-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Business ByDesign in het Access-paneel klikt, moet u automatisch worden aangemeld bij het SAP Business ByDesign waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
