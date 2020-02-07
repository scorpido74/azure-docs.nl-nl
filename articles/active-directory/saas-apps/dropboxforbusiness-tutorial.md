---
title: 'Zelf studie: integratie Azure Active Directory met Dropbox voor bedrijven | Microsoft Docs'
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
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046766"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Zelf studie: Dropbox voor bedrijven integreren met Azure Active Directory

In deze zelf studie leert u hoe u Dropbox voor bedrijven integreert met Azure Active Directory (Azure AD). Wanneer u Dropbox voor bedrijven integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Dropbox voor bedrijven.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij Dropbox voor bedrijven met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Dropbox voor Business SSO (single sign-on) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

* In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Dropbox voor Bedrijven ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Dropbox voor bedrijven ondersteunt [automatische gebruikers inrichting en](dropboxforbusiness-tutorial.md) het ongedaan maken van de inrichting
* Zodra u Dropbox hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox voor Bedrijven toevoegen vanuit de galerie

Voor het configureren van de integratie van Dropbox voor Bedrijven in Azure AD moet u Dropbox voor Bedrijven uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Dropbox voor bedrijven** in het zoekvak.
1. Selecteer **Dropbox voor bedrijven** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met Dropbox voor bedrijven configureren en testen met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Dropbox voor bedrijven.

Als u Azure AD SSO wilt configureren en testen met Dropbox voor bedrijven, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.    
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Dropbox voor Business SSO configureren](#configure-dropbox-for-business-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Een testgebruiker maken in Dropbox voor Bedrijven](#create-dropbox-for-business-test-user)** : om in Dropbox voor Bedrijven een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **Dropbox voor Business** Application Integration en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.dropbox.com/sso/<id>`

    b. Typ een waarde in het vak **Id (Entiteits-id)** : `Dropbox`

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

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Dropbox voor bedrijven.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Dropbox voor bedrijven**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-dropbox-for-business-sso"></a>Dropbox voor Business SSO configureren

1. Als u de configuratie wilt automatiseren in Dropbox voor bedrijven, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op de map voor het **instellen van Dropbox voor** bedrijven, wordt u doorgestuurd naar de Dropbox voor zakelijke toepassing. Geef de beheerders referenties op om u aan te melden bij Dropbox voor bedrijven. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-8 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Dropbox voor bedrijven hand matig wilt instellen, opent u een nieuw webbrowser venster en gaat u naar uw Dropbox voor zakelijke Tenant en meldt u zich aan bij uw Dropbox voor bedrijven-Tenant. en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/ic769509.png "Eenmalige aanmelding configureren")

4. Klik op het **Gebruikerspictogram** en selecteer het tabblad **Instellingen**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure1.png "Eenmalige aanmelding configureren")

5. Klik in het navigatievenster aan de linkerkant op **Beheerconsole**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure2.png "Eenmalige aanmelding configureren")

6. Klik in de **Beheerconsole** op **Instellingen** in het navigatievenster links.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure3.png "Eenmalige aanmelding configureren")

7. Selecteer de optie **Eenmalige aanmelding** in het gedeelte **Verificatie**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure4.png "Eenmalige aanmelding configureren")

8. Voer in het gedeelte **Eenmalige aanmelding** de volgende stappen uit:  

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure5.png "Eenmalige aanmelding configureren")

    a. Selecteer **vereist** als een optie in de vervolg keuzelijst voor de **eenmalige aanmelding**.

    b. Klik op **Aanmeldings-URL toevoegen**, plak in het tekstvak **Aanmeldings-URL identiteitsprovider** de **Aanmeldings-URL** die u hebt gekopieerd van de Azure-portal en selecteer **Gereed**.

    ![Eenmalige aanmelding configureren](./media/dropboxforbusiness-tutorial/configure6.png "Eenmalige aanmelding configureren")

    c. Klik op **Certificaat uploaden** en blader naar het **Base64-gecodeerde certificaatbestand** dat u hebt gedownload uit de Azure-portal.

    d. Klik op **Koppeling kopiëren** en plak de gekopieerde waarde in het tekstvak **Aanmeldings-URL** van het gedeelte **Domein en URL’s voor Dropbox voor Bedrijven** in Azure Portal.

    e. Klik op **Opslaan**.

### <a name="create-dropbox-for-business-test-user"></a>Een testgebruiker maken in Dropbox voor Bedrijven

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in Dropbox voor Bedrijven. Dropbox voor Bedrijven ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Dropbox voor Bedrijven bestaat, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u handmatig een gebruiker moet maken, neemt u contact op met [het ondersteuningsteam van Dropbox voor Bedrijven](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Dropbox voor bedrijven selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de Dropbox voor bedrijven waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Dropbox beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/protect-dropbox)