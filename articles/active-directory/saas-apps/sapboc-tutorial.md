---
title: 'Zelf studie: integratie Azure Active Directory met SAP Analytics-Cloud | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de SAP Analytics-Cloud.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68347797"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Zelf studie: SAP Analytics-Cloud integreren met Azure Active Directory

In deze zelf studie leert u hoe u de SAP Analytics-Cloud integreert met Azure Active Directory (Azure AD). Wanneer u de SAP Analytics-Cloud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de SAP Analytics-Cloud.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij een SAP Analytics-Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SAP Analytics Cloud-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* De SAP Analytics-Cloud ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Een SAP Analytics-Cloud toevoegen vanuit de galerie

Als u de integratie van SAP Analytics-Cloud wilt configureren in azure AD, moet u de SAP Analytics-Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAP Analytics-Cloud** in het zoekvak.
1. Selecteer een **SAP Analytics-Cloud** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met SAP Analytics-Cloud met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de SAP Analytics-Cloud.

Als u Azure AD SSO wilt configureren en testen met de SAP Analytics-Cloud, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[SAP Analytics Cloud-SSO configureren](#configure-sap-analytics-cloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak een SAP Analytics-test gebruiker](#create-sap-analytics-cloud-test-user)** voor de Cloud, zodat er een equivalent van B. Simon in de SAP Analytics-Cloud is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de integratie pagina van de **SAP Analytics-Cloud** toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

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
    > De waarden in deze URL's zijn alleen ter demonstratie. Werk de waarden bij met de werkelijke aanmeldings-URL en identificatie-URL. Neem contact op met het [ondersteunings team voor de cloud client van SAP Analytics](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)om de AANMELDINGS-URL op te halen. U kunt de id-URL ophalen door de meta gegevens van de SAP Analytics-Cloud te downloaden via de beheer console. Dit wordt verderop in de zelfstudie uitgelegd.

4. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Op de sectie **SAP Analytics-Cloud instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics-Cloud-SSO configureren

1. Meld u in een ander webbrowser venster aan bij uw SAP Analytics-Cloud bedrijfs site als beheerder.

2. Selecteer **menu** > **systeem** > **beheer**.
    
    ![Selecteer achtereenvolgens Menu, System en Administration](./media/sapboc-tutorial/config1.png)

3. Selecteer het pictogram **Edit** (pen) op het tabblad **Security**.
    
    ![Selecteer het pictogram Edit op het tabblad Security](./media/sapboc-tutorial/config2.png)  

4. Selecteer **SAML Single Sign-On (SSO)** bij **Authentication Method**.

    ![Selecteer SAML Single Sign-On als de verificatiemethode](./media/sapboc-tutorial/config3.png)  

5. Selecteer **Download** om de metagegevens van de serviceprovider te downloaden (stap 1). Zoek in het bestand met metagegevens de waarde voor **entityID** en kopieer deze. Ga in de Azure-portal naar het dialoogvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Id**.

    ![Kopieer en plak de waarde van entityID](./media/sapboc-tutorial/config4.png)  

6. Selecteer **Upload** onder **Upload Identity Provider metadata** om de metagegevens van de provider (stap 2) te uploaden die u van de Azure-portal hebt gedownload.  

    ![Selecteer Upload onder Upload Identity Provider metadata](./media/sapboc-tutorial/config5.png)

7. Selecteer in de lijst **User Attribute** het gebruikerskenmerk (stap 3) dat u wilt gebruiken voor uw implementatie. Dit gebruikerskenmerk wordt toegewezen aan de id-provider. Als u een aangepast kenmerk wilt invoeren op de pagina van de gebruiker, gebruikt u de optie **Custom SAML Mapping**. Selecteer anders **Email** of **USER ID** als het gebruikerskenmerk. In ons voorbeeld hebben we **Email** geselecteerd omdat we de claim voor de gebruikers-id met het kenmerk **userprincipalname** hebben toegewezen in de sectie **Gebruikerskenmerken en claims** in de Azure-portal. Dit biedt een unieke gebruikers-e-mail, die wordt verzonden naar de SAP Analytics-Cloud toepassing in elke geslaagde SAML-reactie.

    ![Gebruikerskenmerk selecteren](./media/sapboc-tutorial/config6.png)

8. Om het account te controleren bij de id-provider (stap 4), voert u in het vak **Login Credential (Email)** het e-mailadres van de gebruiker in. Selecteer vervolgens **Verify Account**. Het systeem voegt aanmeldingsreferenties toe aan het gebruikersaccount.

    ![E-mailadres invoeren en Verify Account selecteren](./media/sapboc-tutorial/config7.png)

9. Selecteer het pictogram **Save**.

    ![Het pictogram Save](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot de SAP Analytics-Cloud.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SAP Analytics-Cloud**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-sap-analytics-cloud-test-user"></a>Een SAP Analytics-Cloud test gebruiker maken

Azure AD-gebruikers moeten worden ingericht in de SAP Analytics-Cloud voordat ze zich kunnen aanmelden bij de SAP Analytics-Cloud. In de SAP Analytics-Cloud is inrichting een hand matige taak.

Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als beheerder aan bij uw SAP Analytics-Cloud bedrijfs site.

2. Selecteer **menu** > **beveiligings** > **gebruikers**.

    ![Werknemer toevoegen](./media/sapboc-tutorial/user1.png)

3. Voeg op de pagina **Users** gegevens toe van de nieuwe gebruiker en selecteer **+**. 

    ![Pagina voor toevoegen van gebruikers](./media/sapboc-tutorial/user4.png)

    Voer de volgende stappen uit:

    a. Voer in het vak **gebruikers-id** de gebruikers-id van de gebruiker in, zoals **B**.

    b. Voer in het vak **voor naam** de voor naam van de gebruiker in, zoals **B**.

    c. Voer in het vak **LAST NAME** de achternaam van de gebruiker in, zoals **Simon**.

    d. Voer in het vak **weergave naam** de volledige naam van de gebruiker in, zoals **B. Simon**.

    e. Voer in het vak **E-MAIL** het e-mailadres van de gebruiker in, zoals `b.simon@contoso.com`.

    f. Selecteer op de pagina **Select Roles** de juiste rol voor de gebruiker en selecteer vervolgens **OK**.

      ![Rol selecteren](./media/sapboc-tutorial/user3.png)

    g. Selecteer het pictogram **Save**.

### <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Analytics Cloud in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SAP Analytics-Cloud waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

