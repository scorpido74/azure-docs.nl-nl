---
title: 'Zelfstudie: Azure Active Directory-integratie met JIRA SAML SSO by Microsoft (V5.2) | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JIRA SAML SSO by Microsoft (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b304bb35ea69906fc9576f45733134387be1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099520"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Zelfstudie: Azure Active Directory-integratie met JIRA SAML SSO by Microsoft (V5.2)

In deze zelfstudie leert u hoe u JIRA SAML SSO by Microsoft (V5.2) kunt integreren met Azure Active Directory (Azure AD).
De integratie van JIRA SAML SSO by Microsoft (V5.2) met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot JIRA SAML SSO by Microsoft (V5.2).
* U kunt uw gebruikers zich automatisch laten aanmelden bij JIRA SAML SSO by Microsoft (V5.2) (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="description"></a>Beschrijving

Gebruik uw Microsoft Azure Active Directory-account met JIRA Server van Atlassian om eenmalige aanmelding mogelijk te maken. Op deze manier kunnen al uw organisatiegebruikers de Azure AD-referenties gebruiken om zich aan te melden bij de JIRA-toepassing. Deze invoegtoepassing gebruikt SAML 2.0 voor federatie.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met JIRA SAML SSO by Microsoft (V5.2) hebt u het volgende nodig:

- Een Azure AD-abonnement
- JIRA Core en Software 5.2 moeten zijn geïnstalleerd en geconfigureerd op een 64-bits versie van Windows
- JIRA-server is ingeschakeld voor HTTPS
- In de volgende sectie kunt u zien wat de ondersteunde versies zijn van de JIRA-invoegtoepassing.
- De JIRA-server is bereikbaar op internet vanaf de aanmeldingspagina van Azure AD voor verificatie en moet token uit Azure AD kunnen ontvangen
- Beheerderreferenties zijn ingesteld in JIRA
- WebSudo is uitgeschakeld in JIRA
- Testgebruiker gemaakt in de JIRA-servertoepassing

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, raden wij u aan niet een productieomgeving van JIRA te gebruiken. Test de integratie eerst in een ontwikkel- of faseringsomgeving van de toepassing en gebruik dan pas de productieomgeving.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proefomgeving hebt, u hier een proefversie van één maand krijgen: [Proefaanbieding](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Ondersteunde versies van JIRA

* JIRA Kern en Software: 5.2
* JIRA ondersteunt ook 6.0 t/m 7.12. Klik voor meer informatie op [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Houd er rekening mee dat onze JIRA Plugin ook werkt op Ubuntu Versie 16.04

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* JIRA SAML SSO by Microsoft (V5.2) ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>JIRA SAML SSO by Microsoft (V5.2) toevoegen vanuit de galerie

Voor het configureren van de integratie van JIRA SAML SSO by Microsoft (V5.2) in Azure AD, moet u JIRA SAML SSO by Microsoft (V5.2) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om JIRA SAML SSO by Microsoft (V5.2) toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **JIRA SAML SSO by Microsoft (V5.2)** in het zoekvak, selecteer **JIRA SAML SSO by Microsoft (V5.2)** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![JIRA SAML SSO by Microsoft (V5.2) in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met JIRA SAML SSO by Microsoft (V5.2) configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in JIRA SAML SSO by Microsoft (V5.2) tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met JIRA SAML SSO by Microsoft (V5.2) wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor JIRA SAML SSO by Microsoft (V5.2) configureren](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken in JIRA SAML SSO by Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)**: om in JIRA SAML SSO by Microsoft (V5.2) een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met JIRA SAML SSO by Microsoft (V5.2) moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **JIRA SAML SSO by Microsoft (V5.2)**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij JIRA SAML SSO by Microsoft (V5.2)](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<domain:port>/plugins/servlet/saml/auth`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<domain:port>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. 'Port' is optioneel als het een benoemde URL betreft. Deze waarden krijgt u tijdens de configuratie van de JIRA-invoegtoepassing, wat later in de zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Eenmalige aanmelding voor JIRA SAML SSO by Microsoft (V5.2) configureren

1. Meld u in een ander browservenster aan bij uw JIRA-exemplaar als beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Eenmalige aanmelding configureren](./media/jira52microsoft-tutorial/addon1.png)

3. Klik onder de tabbladsectie Invoegtoepassingen op **Manage add-ons**.

    ![Eenmalige aanmelding configureren](./media/jira52microsoft-tutorial/addon7.png)

4. Download de invoegtoepassing uit het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56521). Upload de invoegtoepassing van Microsoft handmatig via het menu **Upload add-on**. Het downloaden van invoegtoepassingen valt onder de [Microsoft-serviceovereenkomst](https://www.microsoft.com/servicesagreement/).

    ![Eenmalige aanmelding configureren](./media/jira52microsoft-tutorial/addon12.png)

5. Zodra de invoegtoepassing is geïnstalleerd, wordt deze weergegeven in de sectie **User Installed** met door de gebruiker geïnstalleerde invoegtoepassingen. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Eenmalige aanmelding configureren](./media/jira52microsoft-tutorial/addon13.png)

6. Voer de volgende stappen uit op de configuratiepagina:

    ![Eenmalige aanmelding configureren](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Zorg ervoor dat er maar één certificaat is toegewezen aan de app, zodat er geen fout optreedt bij het omzetten van de metagegevens. Als er meerdere certificaten zijn, krijgt de beheerder een foutmelding bij het omzetten van de metagegevens.

    a. Plak in het tekstvak **Metadata URL** de waarde voor **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit de Azure-portal en klik op de knop **Resolve**. De URL met de IdP-metagegevens wordt gelezen en de overeenkomende velden worden ingevuld.

    b. Kopieer de waarden voor **Identifier, Reply URL, Sign on URL** en plak deze in respectievelijk de vakken **Id, Antwoord-URL en Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    c. Typ bij **Login Button Name** de naam van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.

    d. Selecteer in **SAML-gebruikersnaamlocaties** een **gebruikersnaam in het element NameIdentifier van de onderwerpinstructie** of gebruikersnaam in een **kenmerkelement**.  Deze ID moet de JIRA-gebruikersnaam zijn. Als de gebruikersnaam niet is afgestemd, kunnen gebruikers zich niet aanmelden.

    > [!Note]
    > De gebruikers-id in de standaard SAML-configuratie is de NameIdentifier. U kunt dit wijzigen in een kenmerkoptie en de juiste kenmerknaam invoeren.

    e. Als u **gebruikersnaam selecteert, bevindt u zich in een** element attribuut, typt u in **tekstvak Kenmerknaam** de naam van het kenmerk waar gebruikersnaam wordt verwacht. 

    f. Als u het federatieve domein (zoals ADFS, enzovoort) gebruikt met Azure AD, selecteert u de optie **Enable Home Realm Discovery** en geeft u een naam op voor **Domain Name**.

    g. Typ bij **Domain Name** de domeinnaam als het aanmelding op basis van ADFS betreft.

    h. Schakel **Eenmalig afmelden in** als u zich wilt afmelden bij Azure AD wanneer een gebruiker zich afmeldt bij JIRA. 

    i. Klik op **Save** om de instellingen op te slaan.

    > [!NOTE]
    > Raadpleeg de [beheerdershandleiding voor MS JIRA SSO Connector](../ms-confluence-jira-plugin-adminguide.md) en de [veelgestelde vragen](../ms-confluence-jira-plugin-faq.md) voor meer informatie over de installatie en probleemoplossing

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon\@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot JIRA SAML SSO by Microsoft (V5.2).

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **JIRA SAML SSO by Microsoft (V5.2)**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **JIRA SAML SSO by Microsoft (V5.2) ** in de lijst met toepassingen.

    ![De koppeling JIRA SAML SSO by Microsoft (V5.2) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Een testgebruiker maken in JIRA SAML SSO by Microsoft (V5.2)

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij de on-premises server van JIRA, moeten ze worden ingericht in de on-premises jira-server.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw JIRA on-premises server als beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Werknemer toevoegen](./media/jira52microsoft-tutorial/user1.png)

3. U wordt omgeleid naar de toegangspagina voor beheerders. Voer uw **wachtwoord** in en klik op de knop **Bevestigen**.

    ![Werknemer toevoegen](./media/jira52microsoft-tutorial/user2.png)

4. Onder de tabbladsectie **Gebruikersbeheer** klikt u op **Gebruiker maken**.

    ![Werknemer toevoegen](./media/jira52microsoft-tutorial/user3.png) 

5. Op de pagina **Nieuwe gebruiker maken** voert u de volgende stappen uit:

    ![Werknemer toevoegen](./media/jira52microsoft-tutorial/user4.png)

    a. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    b. In het tekstvak **Volledige naam** typt u de volledige naam van de gebruiker, bijvoorbeeld Britta Simon.

    c. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    e. Klik **op Gebruiker maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel JIRA SAML SSO by Microsoft (V5.2) klikt, zou u automatisch moeten worden aangemeld bij de instantie van JIRA SAML SSO by Microsoft (V5.2) waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
