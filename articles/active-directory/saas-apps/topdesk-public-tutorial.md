---
title: 'Zelfstudie: Azure Active Directory-integratie met TOPdesk - Openbaar | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TOPdesk - Openbaar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: e5575a2e8f776e87fcd4e6f4a7a9244752ebfd9a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71950412"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Zelfstudie: Azure Active Directory-integratie met TOPdesk - Openbaar

In deze zelfstudie leert u hoe u TOPdesk - Openbaar integreert met Azure Active Directory (Azure AD).
Integratie van TOPdesk - Openbaar maken met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot TOPdesk - Openbaar.
* U uw gebruikers automatisch laten aanmelden bij TOPdesk - Openbaar (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met TOPdesk - Openbaar, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* TOPdesk - Abonnement met eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TOPdesk - Publieke ondersteuning **SP** geïnitieerdse SSO

## <a name="adding-topdesk---public-from-the-gallery"></a>TOPdesk toevoegen - Openbaar vanuit de galerie

Als u de integratie van TOPdesk - Openbaar in Azure AD wilt configureren, moet u TOPdesk - Openbaar vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u TOPdesk wilt toevoegen - Openbaar vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ IN het zoekvak **TOPdesk - Openbaar**, selecteer **TOPdesk - Openbaar** van het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

     ![TOPdesk - Openbaar in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met TOPdesk - Public op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding op het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TOPdesk - Openbaar.

Als u Azure AD-single sign-aan wilt configureren en testen met TOPdesk - Openbaar, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Topdesk configureren - Openbare aanmelding](#configure-topdesk---public-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Topdesk - Openbare testgebruiker](#create-topdesk---public-test-user)** maken - om een tegenhanger van Britta Simon in TOPdesk te hebben - Openbaar dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met TOPdesk - Openbaar te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de **pagina TOPdesk - Integratie** van openbare toepassingen de optie Enkele **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4.  Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U krijgt het **metagegevensbestand van** de serviceprovider van de sectie **ToPdesk configureren - Openbare aanmelding smaken,** die later in de zelfstudie wordt uitgelegd.

    a. Klik op **Metagegevensbestand uploaden**.
    
    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in de sectie BasisSAML-configuratie.

    ![TOPdesk - Informatie over openbaar domein en URL's met eenmalige aanmelding](common/sp-identifier-reply.png)

    d. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<companyname>.topdesk.net`

    e. Vul in het tekstvak **Id-URL** de URL metagegevens van TOPdesk in die u ophalen uit de TOPdesk-configuratie. Het moet het volgende patroon gebruiken:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Als de **URL-waarden Id** en **Antwoord** niet automatisch worden ingevuld, moet u deze handmatig invoeren. Volg voor Id het patroon zoals hierboven vermeld en u krijgt de URL-waarde van het antwoord van de sectie **TOPdesk configureren - Openbaar eenmalig aanmelden,** die later in de zelfstudie wordt uitgelegd. De **URL-waarde aanmelding** is niet echt, dus u moet de waarde bijwerken met de werkelijke URL voor aanmelding. Neem contact op met [TOPdesk - Public Client support team](https://help.topdesk.com/saas/enterprise/user/) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **TOPdesk instellen - Openbaar** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-topdesk---public-single-sign-on"></a>TOPdesk configureren - Openbare aanmelding

1. Meld u aan bij uw **TOPdesk - Openbare** bedrijfssite als beheerder.

2. Klik in het menu van **TOPdesk** op **Settings**.
   
    ![Instellingen](./media/topdesk-public-tutorial/ic790598.png "Instellingen")

3. Klik op **Login Settings**.
   
    ![Aanmeldingsinstellingen](./media/topdesk-public-tutorial/ic790599.png "Aanmeldingsinstellingen")

4. Vouw het menu **Login Settings** uit en klik op **General**.
   
    ![Algemeen](./media/topdesk-public-tutorial/ic790600.png "Algemeen")

5. Voer in het gedeelte **Openbaar** van de **saml-aanmeldingsconfiguratie** de volgende stappen uit:
   
    ![Technische instellingen](./media/topdesk-public-tutorial/ic790601.png "Technische instellingen")
   
    a. Klik op **Downloaden** om het openbare metagegevensbestand te downloaden en sla het lokaal op uw computer op.
   
    b. Open het gedownloade metagegevensbestand en zoek vervolgens het knooppunt **AssertionConsumerService.**

    ![BeweringConsumerService](./media/topdesk-public-tutorial/ic790619.png "BeweringConsumerService")
   
    c. Kopieer de waarde **BevestigingsconsumentService** en plak deze waarde in het tekstvak **Voor het beantwoorden VAN URL's** in de sectie **BasisSAML-configuratie.**      
   
6. Als u een certificaatbestand wilt maken, moet u de volgende stappen uitvoeren:
    
    ![Certificaat](./media/topdesk-public-tutorial/ic790606.png "Certificaat")
    
    a. Open het gedownloade metagegevensbestand vanuit de Azure-portal.
    
    b. Vouw het knooppunt **RoleDescriptor** uit waarvan **xsi:type** is ingesteld op **fed:ApplicationServiceType**.
    
    c. Kopieer de waarde van het knooppunt **X509Certificate**.
    
    d. Sla de gekopieerde waarde van **X509Certificate** lokaal op uw computer op in een bestand.

7. Klik in de sectie **Public** op **Add**.
    
    ![SAML-aanmelding](./media/topdesk-public-tutorial/ic790625.png "SAML-aanmelding")

8. Voer de volgende stappen uit in het dialoogvenster **SAML configuration assistant**:
    
    ![SAML-configuratieassistent](./media/topdesk-public-tutorial/ic790608.png "SAML-configuratieassistent")
    
    a. Klik onder **Federatieve metagegevens** op **Bladeren** om het gedownloade metagegevensbestand te uploaden vanuit de Azure-portal.

    b. Klik onder **Certificaat (RSA)** op **Bladeren** om het certificaatbestand te uploaden.

    c. Klik onder het **Logo-pictogram** op **Bladeren** om het logobestand dat u van het TOPdesk-ondersteuningsteam hebt verkregen, te uploaden.

    d. In het tekstvak voor het **gebruikersnaamkenmerk** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Typ in het tekstvak **Weergavenaam** een naam voor de configuratie.

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
  
    b. In **User name** het veld brittasimon@yourcompanydomain.extensiontype gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot TOPdesk - Public.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **vervolgens TOPdesk - Openbaar**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer IN de lijst met toepassingen DE optie **TOPdesk - Openbaar**.

    ![De TOPdesk - Openbare koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-topdesk---public-test-user"></a>TOPdesk maken - Openbare testgebruiker

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij TOPdesk - Openbaar, moeten ze worden ingericht in TOPdesk - Openbaar. In het geval van TOPdesk - Openbaar is inrichten een handmatige taak.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Voer de volgende stappen uit om de inrichting van gebruikers te configureren:

1. Meld u aan bij uw **TOPdesk - Openbare** bedrijfssite als beheerder.

2. Klik in het menu bovenaan op **TOPdesk \> New \> Support Files \> Person**.
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. Voer in het dialoogvenster Nieuwe persoon de volgende stappen uit:
   
    ![Nieuwe persoon](./media/topdesk-public-tutorial/ic790629.png "Nieuwe persoon")
   
    a. Klik op het tabblad General.

    b. Typ achternaam van de gebruiker zoals Simon in het tekstvak **Achternaam**
 
    c. Selecteer een **site** voor het account.
 
    d. Klik op **Opslaan**.

> [!NOTE]
> U alle andere TOPdesk - Hulpprogramma's voor het maken van openbare gebruikersaccounts of API's van TOPdesk gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de TOPdesk - Openbare tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de TOPdesk - Public waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
