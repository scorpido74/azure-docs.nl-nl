---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Analytics Cloud | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Analytics Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68347797"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Zelfstudie: SAP Analytics Cloud integreren met Azure Active Directory

In deze zelfstudie leert u hoe u SAP Analytics Cloud integreren met Azure Active Directory (Azure AD). Wanneer u SAP Analytics Cloud integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot SAP Analytics Cloud.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Analytics Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SAP Analytics Cloud single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SAP Analytics Cloud ondersteunt **DOOR SP** geïnitieerde SSO

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>SAP Analytics Cloud toevoegen vanuit de galerie

Als u de integratie van SAP Analytics Cloud in Azure AD wilt configureren, moet u SAP Analytics Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SAP Analytics Cloud** in het zoekvak in de sectie Toevoegen vanuit de **galerie.**
1. Selecteer **SAP Analytics Cloud** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met SAP Analytics Cloud met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP Analytics Cloud.

Als u Azure AD SSO wilt configureren en testen met SAP Analytics Cloud, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer SAP Analytics Cloud SSO](#configure-sap-analytics-cloud-sso)** - om de instellingen voor één aanmelding aan toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak SAP Analytics Cloud-testgebruiker](#create-sap-analytics-cloud-test-user)** - om een tegenhanger van B.Simon te hebben in SAP Analytics Cloud die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Manage** SAP Analytics Cloud-applicatie-integratie de sectie Beheren en selecteer **Enkele aanmelding**. **SAP Analytics Cloud**
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: 

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: 
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > De waarden in deze URL's zijn alleen ter demonstratie. Werk de waarden bij met de werkelijke aanmeldings-URL en identificatie-URL. Neem contact op met het [ondersteuningsteam](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)van SAP Analytics Cloud Client om de URL van de aanmelding te krijgen. U de URL van de id ophalen door de metagegevens van de SAP Analytics Cloud te downloaden van de beheerdersconsole. Dit wordt verderop in de zelfstudie uitgelegd.

4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **SAP Analytics Cloud** instellen de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics Cloud SSO configureren

1. Meld u in een ander browservenster aan bij uw SAP Analytics Cloud-bedrijfssite als beheerder.

2. Selecteer**System** >  **Systeembeheer** > **menu .**
    
    ![Selecteer achtereenvolgens Menu, System en Administration](./media/sapboc-tutorial/config1.png)

3. Selecteer het pictogram **Edit** (pen) op het tabblad **Security**.
    
    ![Selecteer het pictogram Edit op het tabblad Security](./media/sapboc-tutorial/config2.png)  

4. Selecteer **SAML Single Sign-On (SSO)** bij **Authentication Method**.

    ![Selecteer SAML Single Sign-On als de verificatiemethode](./media/sapboc-tutorial/config3.png)  

5. Selecteer **Download** om de metagegevens van de serviceprovider te downloaden (stap 1). Zoek in het bestand met metagegevens de waarde voor **entityID** en kopieer deze. Ga in de Azure-portal naar het dialoogvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Id**.

    ![Kopieer en plak de waarde van entityID](./media/sapboc-tutorial/config4.png)  

6. Selecteer **Upload** onder **Upload Identity Provider metadata** om de metagegevens van de provider (stap 2) te uploaden die u van de Azure-portal hebt gedownload.  

    ![Selecteer Upload onder Upload Identity Provider metadata](./media/sapboc-tutorial/config5.png)

7. Selecteer in de lijst **User Attribute** het gebruikerskenmerk (stap 3) dat u wilt gebruiken voor uw implementatie. Dit gebruikerskenmerk wordt toegewezen aan de id-provider. Als u een aangepast kenmerk wilt invoeren op de pagina van de gebruiker, gebruikt u de optie **Custom SAML Mapping**. Selecteer anders **Email** of **USER ID** als het gebruikerskenmerk. In ons voorbeeld hebben we **Email** geselecteerd omdat we de claim voor de gebruikers-id met het kenmerk **userprincipalname** hebben toegewezen in de sectie **Gebruikerskenmerken en claims** in de Azure-portal. Dit biedt een unieke gebruikerse-mail, die in elke succesvolle SAML-reactie naar de SAP Analytics Cloud-toepassing wordt verzonden.

    ![Gebruikerskenmerk selecteren](./media/sapboc-tutorial/config6.png)

8. Om het account te controleren bij de id-provider (stap 4), voert u in het vak **Login Credential (Email)** het e-mailadres van de gebruiker in. Selecteer vervolgens **Verify Account**. Het systeem voegt aanmeldingsreferenties toe aan het gebruikersaccount.

    ![E-mailadres invoeren en Verify Account selecteren](./media/sapboc-tutorial/config7.png)

9. Selecteer het pictogram **Save**.

    ![Het pictogram Save](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SAP Analytics Cloud.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer SAP Analytics **Cloud**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-sap-analytics-cloud-test-user"></a>Sap Analytics Cloud-testgebruiker maken

Azure AD-gebruikers moeten zijn ingericht in SAP Analytics Cloud voordat ze zich kunnen aanmelden bij SAP Analytics Cloud. In SAP Analytics Cloud is inrichten een handmatige taak.

Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u aan bij uw SAP Analytics Cloud-bedrijfssite als beheerder.

2. Selecteer**gebruikers**van > **menubeveiliging** > . **Menu**

    ![Werknemer toevoegen](./media/sapboc-tutorial/user1.png)

3. Voeg op de pagina **Users** gegevens toe van de nieuwe gebruiker en selecteer **+**. 

    ![Pagina voor toevoegen van gebruikers](./media/sapboc-tutorial/user4.png)

    Voer de volgende stappen uit:

    a. Voer **in** het vak GEBRUIKERSNAAM de gebruikersnaam van de gebruiker in, zoals **B**.

    b. Voer in het vak **VOORNAAM** de voornaam van de gebruiker in, zoals **B**.

    c. Voer in het vak **LAST NAME** de achternaam van de gebruiker in, zoals **Simon**.

    d. Voer in het vak **WEERGAVENAAM** de volledige naam van de gebruiker in, zoals **B.Simon**.

    e. Voer in het vak **E-MAIL** het e-mailadres van de gebruiker in, zoals `b.simon@contoso.com`.

    f. Selecteer op de pagina **Select Roles** de juiste rol voor de gebruiker en selecteer vervolgens **OK**.

      ![Rol selecteren](./media/sapboc-tutorial/user3.png)

    g. Selecteer het pictogram **Save**.

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de TEGEL SAP Analytics Cloud in het Access Panel klikt, moet u automatisch worden aangemeld bij de SAP Analytics Cloud waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

