---
title: 'Zelfstudie: Azure Active Directory-integratie met Dropbox voor Bedrijven | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Dropbox voor Bedrijven.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7fc366c5087e66c3022c212870397d77e6e34d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046766"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Zelfstudie: Dropbox voor Bedrijven integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Dropbox voor Bedrijven integreren met Azure Active Directory (Azure AD). Wanneer je Dropbox voor Bedrijven integreert met Azure AD, kun je het sein doen:

* Beheer in Azure AD wie toegang heeft tot Dropbox voor Bedrijven.
* Stel je gebruikers in staat om automatisch te worden aangemeld bij Dropbox voor Bedrijven met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* Abonnement op single sign-on (Dropbox for Business) met één aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

* In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Dropbox voor Bedrijven ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Dropbox voor Bedrijven ondersteunt [geautomatiseerde gebruikersinrichting en -deprovisioning](dropboxforbusiness-tutorial.md)
* Zodra je Dropbox hebt geconfigureerd, kun je sessiebeheer afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van je organisatie in realtime beschermt. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox voor Bedrijven toevoegen vanuit de galerie

Voor het configureren van de integratie van Dropbox voor Bedrijven in Azure AD moet u Dropbox voor Bedrijven uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Dropbox voor Bedrijven** in het zoekvak in de sectie Toevoegen in de **galerijsectie.**
1. Selecteer **Dropbox voor Bedrijven** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Dropbox voor Bedrijven met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppeling smaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Dropbox voor Bedrijven.

Voer de volgende bouwstenen in om Azure AD SSO met Dropbox for Business te configureren en te testen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.    
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Configureer Dropbox for Business SSO](#configure-dropbox-for-business-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Een testgebruiker maken in Dropbox voor Bedrijven](#create-dropbox-for-business-test-user)**: om in Dropbox voor Bedrijven een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina De integratie **van toepassingen van Dropbox voor Bedrijven** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.dropbox.com/sso/<id>`

    b. Typ een waarde in het vak **Id (Entiteits-id)**: `Dropbox`

    > [!NOTE]
    > De bovenstaande waarde voor de aanmeldings-URL is geen echte waarde. U werkt de waarde bij met de werkelijke aanmeldings-URL, zoals later in de zelfstudie wordt uitgelegd.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer onder **Dropbox voor Bedrijven instellen** de URL('s) die u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL


### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd Britta Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie kun je Britta Simon inschakelen om Eenmalig aanmelden in Azure te gebruiken door toegang te verlenen tot Dropbox voor Bedrijven.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer Dropbox voor **Bedrijven**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-dropbox-for-business-sso"></a>Dropbox for Business SSO configureren

1. Als u de configuratie binnen Dropbox voor Bedrijven wilt automatiseren, moet u **de beveiligingsextensie Mijn apps veilig aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat je de extensie aan de browser hebt toegevoegd, klik je op **Dropbox for Business instellen** en stuur je door naar de Dropbox voor Bedrijven-applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Dropbox voor Bedrijven. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-8.

    ![Configuratie instellen](common/setup-sso.png)

3. Als je Dropbox voor Bedrijven handmatig wilt instellen, open je een nieuw browservenster en ga je naar je Dropbox voor Bedrijven-tenant en meld je je aan bij je Dropbox voor bedrijven-tenant. en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/ic769509.png "Eenmalige aanmelding configureren")

4. Klik op het **Gebruikerspictogram** en selecteer het tabblad **Instellingen**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure1.png "Eenmalige aanmelding configureren")

5. Klik in het navigatievenster aan de linkerkant op **Beheerconsole**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure2.png "Eenmalige aanmelding configureren")

6. Klik in de **Beheerconsole** op **Instellingen** in het navigatievenster links.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure3.png "Eenmalige aanmelding configureren")

7. Selecteer de optie **Eenmalige aanmelding** in het gedeelte **Verificatie**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure4.png "Eenmalige aanmelding configureren")

8. Voer in de sectie **Eén aanmelding** de volgende stappen uit:  

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure5.png "Eenmalige aanmelding configureren")

    a. Selecteer **Vereist** als optie in de vervolgkeuzelijst voor de **aanmelding eenmalig**.

    b. Klik op **Aanmeldings-URL toevoegen**, plak in het tekstvak **Aanmeldings-URL identiteitsprovider** de **Aanmeldings-URL** die u hebt gekopieerd van de Azure-portal en selecteer **Gereed**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure6.png "Eenmalige aanmelding configureren")

    c. Klik op **Certificaat uploaden** en blader naar het **Base64-gecodeerde certificaatbestand** dat u hebt gedownload uit de Azure-portal.

    d. Klik op **Koppeling kopiëren** en plak de gekopieerde waarde in het tekstvak **Aanmeldings-URL** van het gedeelte **Domein en URL’s voor Dropbox voor Bedrijven** in Azure Portal.

    e. Klik op **Opslaan**.

### <a name="create-dropbox-for-business-test-user"></a>Een testgebruiker maken in Dropbox voor Bedrijven

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in Dropbox voor Bedrijven. Dropbox voor Bedrijven ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Dropbox voor Bedrijven bestaat, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u handmatig een gebruiker moet maken, neemt u contact op met [het ondersteuningsteam van Dropbox voor Bedrijven](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Test SSO

Wanneer je de tegel Dropbox voor Bedrijven selecteert in het toegangspaneel, moet je automatisch zijn aangemeld bij de Dropbox voor Bedrijven waarvoor je SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Dropbox beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-dropbox)