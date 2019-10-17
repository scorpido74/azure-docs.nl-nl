---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met NetSuite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en NetSuite configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438490"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met NetSuite

In deze zelf studie leert u hoe u NetSuite integreert met Azure Active Directory (Azure AD). Wanneer u NetSuite integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Netsuite.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij NetSuite met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Abonnement voor eenmalige aanmelding (SSO) van NetSuite ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* NetSuite ondersteunt door **IDP** geïnitieerde SSO

* NetSuite biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

* NetSuite biedt ondersteuning voor het [automatisch inrichten van gebruikers](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-netsuite-from-the-gallery"></a>NetSuite toevoegen vanuit de galerie

Om de integratie van NetSuite in Azure AD te configureren, moet u NetSuite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **NetSuite** in het zoekvak.
1. Selecteer **NetSuite** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor NetSuite

Azure AD SSO met NetSuite configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Netsuite.

Als u Azure AD SSO wilt configureren en testen met Netsuite, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer de SSO van NetSuite](#configure-netsuite-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. Een gebruiker van de groep test van een **[NetSuite maken](#create-netsuite-test-user)** voor een soort van B. Simon in NetSuite die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **NetSuite** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [ondersteuningsteam van NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De toepassing NetSuite verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

1. In aanvulling op hierboven verwachtte NetSuite-toepassingen nog maar weinig kenmerken om te worden door gegeven in het SAML-antwoord. Voer in de sectie gebruikers claims van het dialoog venster gebruikers kenmerken de volgende stappen uit om het SAML-token kenmerk toe te voegen, zoals wordt weer gegeven in de onderstaande tabel:

    | Naam | Bronkenmerk | 
    | ---------------| --------------- |
    | account  | `account id` |

    1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer Bron bij **Kenmerk**.

    1. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    1. Klik op **OK**.

    1. Klik op **Opslaan**.

    >[!NOTE]
    >De waarde voor het accountkenmerk is niet echt. U gaat deze waarde later aanpassen. Dit wordt verderop in de zelfstudie uitgelegd.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **NetSuite instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan NetSuite.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **NetSuite**in de lijst toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netsuite-sso"></a>Netsuite-SSO configureren

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfssite van NetSuite.

2. Klik in de werk balk boven aan de pagina op **instellen**, ga naar **bedrijf** en klik op **functies inschakelen**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Klik in de werk balk aan het midden van de pagina op **SuiteCloud**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Onder **verificatie beheren** selecteert u **eenmalige aanmelding voor SAML** om de optie eenmalige aanmelding via SAML in te scha kelen in Netsuite.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Klik in de werk balk boven aan de pagina op **instellen**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

6. Klik in de lijst **SETUP TASKS** op **Integration**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-integration.png)

7. Klik in het gedeelte **MANAGE AUTHENTICATION** op **SAML Single Sign-on**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-saml.png)

8. Voer op de pagina **SAML Setup**, onder **NetSuite Configuration**, de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecteer **PRIMARY AUTHENTICATION METHOD**.

    b. Selecteer **UPLOAD IDP METADATA FILE** voor het veld met het label **SAMLV2 IDENTITY PROVIDER METADATA**. Klik vervolgens op **Browse** om het bestand met metagegevens dat u hebt gedownload van de Azure-portal te uploaden.

    c. Klik op **Submit**

9. Klik in NetSuite op **Setup**, ga naar **Company** en klik op **Company Information** in het bovenste navigatiemenu.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-com.png)

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-account-id.png)

    b. Kopieer de **ACCOUNT ID** in de rechterkolom op de pagina **Company Information**.

    c. Plak de **account-id** die u hebt gekopieerd uit het NetSuite-account in het veld **Waarde kenmerk** in Azure AD. 

10. Voordat gebruikers eenmalige aanmelding kunnen gebruiken met NetSuite moeten ze over de juiste machtigingen beschikken in NetSuite. Volg de onderstaande instructies om deze machtigingen toe te wijzen.

    a. Klik in het bovenste navigatiemenu op **Setup**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

    b. Selecteer in het navigatiemenu links **Users/Roles** en klik op **Manage Roles**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klik op **New Role**.

    d. Typ in het veld **Name** een naam voor de nieuwe rol.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klik op **Opslaan**.

    f. Klik in het menu aan de bovenkant op **Permissions**. Klik vervolgens op **Setup**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecteer **SAML Single Sign-on** en klik vervolgens op **Add**.

    h. Klik op **Opslaan**.

    i. Klik in het bovenste navigatiemenu op **Setup** en daarna op **Setup Manager**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

    j. Selecteer in het navigatiemenu links **Users/Roles** en klik op **Manage Users**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecteer een testgebruiker. Klik vervolgens op **Edit** en ga naar het tabblad **Access**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Wijs in het dialoogvenster Roles de juiste rol toe die u hebt gemaakt.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-add-role.png)

    m. Klik op **Opslaan**.

### <a name="create-netsuite-test-user"></a>Testgebruiker voor NetSuite maken

In dit gedeelte wordt er een gebruiker met de naam Britta Simon gemaakt in NetSuite. NetSuite biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in NetSuite, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel NetSuite klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Netsuite proberen met Azure AD](https://aad.portal.azure.com/)

