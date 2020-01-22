---
title: 'Zelf studie: integratie met Sales Force-sandbox Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen de sandbox van Azure Active Directory en Sales Force.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290000"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Sales Force-sandbox

In deze zelf studie leert u hoe u de Sales Force-sandbox integreert met Azure Active Directory (Azure AD). Wanneer u de Sales Force-sandbox integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Sales Force-sandbox.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij Sales Force in de sandbox met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Abonnement voor eenmalige aanmelding voor Sales Force-sandbox (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* De Sales Force-sandbox ondersteunt SSO die **met SP en IDP** is gestart
* De Sales Force-sandbox ondersteunt **just-in-time** -gebruikers inrichting
* De Sales Force-sandbox ondersteunt [ **geautomatiseerde** gebruikers inrichting](salesforce-sandbox-provisioning-tutorial.md)
* Zodra u de Sales Force-sandbox hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Een Sales Force-sandbox toevoegen vanuit de galerie

Als u de integratie van Sales Force-sandbox wilt configureren in azure AD, moet u de Sales Force-sandbox vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Sales Force sandbox** in het zoekvak.
1. Selecteer **Sales Force sandbox** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Eenmalige aanmelding van Azure AD voor Sales Force-sandbox configureren en testen

Azure AD SSO met Sales Force sandbox configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sales Force.

Als u Azure AD SSO met Sales Force sandbox wilt configureren en testen, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configure Sales Force SANDBOX SSO](#configure-salesforce-sandbox-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    * Een **[Sales Force-test gebruiker voor de sandbox maken](#create-salesforce-sandbox-test-user)** : u hebt een tegen hanger van B. Simon in Sales Force die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Sales Force sandbox** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u het **META gegevensbestand van de service provider** hebt en wilt configureren in de gestarte modus **IDP** , voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    > [!NOTE]
    > U ontvangt het meta gegevensbestand van de service provider in de beheer portal van de Sales Force-sandbox. dit wordt verderop in de zelf studie uitgelegd.

    c. Nadat het meta gegevensbestand is geüpload, wordt de waarde van de **antwoord-URL** automatisch ingevuld in het tekstvak **antwoord-URL** .

    ![installatiekopie](common/both-replyurl.png)

    > [!Note]
    > Als de waarde van de **antwoord-URL** geen automatische polulated krijgt, vult u de waarde hand matig in volgens uw vereiste.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer de gewenste URL ('s) in het gedeelte **Sales Force sandbox instellen** conform uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot Sales Force sandbox.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Sales Force**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-salesforce-sandbox-sso"></a>Sales Force-SSO voor sandbox configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij uw Sales Force-account voor de sandbox-beheerder.

2. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure1.png)

3. Schuif omlaag naar de **instellingen** in het navigatie deel venster links en klik op **identiteit** om de gerelateerde sectie uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klik op **Bestand kiezen** om het XML-bestand met metagegevens te uploaden dat u hebt gedownload uit Azure Portal. Klik dan op **Maken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Op de pagina **SAML-instellingen voor eenmalige aanmelding** worden de velden automatisch ingevuld. Klik op Opslaan.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Klik op de pagina **instellingen voor eenmalige aanmelding** op de knop **meta gegevens downloaden** om het bestand met meta gegevens van de service provider te downloaden. Gebruik dit bestand in het gedeelte **basis configuratie van SAML** in de Azure portal voor het configureren van de benodigde url's zoals hierboven is uitgelegd.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure4.png)

10. Als u de toepassing in de modus door **SP** gestart wilt configureren, volgt u de vereisten voor die:

    a. U moet een geverifieerd domein hebben.

    b. U moet uw domein configureren en inschakelen in de sandbox voor Sales Force, de stappen hiervoor worden verderop in deze zelf studie beschreven.

    c. Klik in het Azure Portal, in de sectie **basis configuratie van SAML** , op **extra url's instellen** en voer de volgende stap uit:
  
    ![Informatie over eenmalige aanmelding voor het sandbox-domein en Url's voor Sales Force](common/both-signonurl.png)

    Typ in het tekstvak **URL voor aanmelding** de waarde met behulp van het volgende patroon: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Deze waarde moet worden gekopieerd uit de Sales Force-sandbox-Portal wanneer u het domein hebt ingeschakeld.

11. Klik in de sectie **SAML-handtekening certificaat** op **federatieve meta gegevens-XML** en sla het XML-bestand op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

12. Open een nieuw tabblad in uw browser en meld u aan bij uw Sales Force-account voor de sandbox-beheerder.

13. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure1.png)

14. Schuif omlaag naar de **instellingen** in het navigatie deel venster links en klik op **identiteit** om de gerelateerde sectie uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klik op **bestand kiezen** om het XML-bestand met meta gegevens te uploaden en op **maken**te klikken.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Op de pagina **instellingen voor eenmalige SAML-aanmelding** worden velden automatisch gevuld, typt u de naam van de configuratie (bijvoorbeeld: *SPSSOWAAD_Test*) in het tekstvak **naam** en klikt u op opslaan.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Voer de volgende stappen uit om uw domein in te scha kelen in de sandbox voor Sales Force:

    > [!NOTE]
    > Voordat u het domein inschakelt, moet u hetzelfde maken in de Sales Force-sandbox. Zie [uw domein naam definiëren](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)voor meer informatie. Nadat het domein is gemaakt, controleert u of het correct is geconfigureerd.

21. Klik in het navigatie deel venster links in Sales Force **-sandbox op bedrijfs instellingen** om de gerelateerde sectie uit te vouwen en klik vervolgens op **mijn domein**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Klik in de sectie **verificatie configuratie** op **bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Selecteer in de sectie **verificatie configuratie** , als **verificatie service**, de naam van de instelling voor eenmalige SAML-aanmelding die u hebt ingesteld tijdens de SSO-configuratie in Sales Force-sandbox en klik op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Gebruikers van de Sales Force-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Sales Force. De Sales Force-sandbox ondersteunt just-in-time-inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al aanwezig is in de Sales Force-sandbox, wordt er een nieuwe gemaakt wanneer u toegang probeert te krijgen tot Sales Force sandbox. De Sales Force-sandbox ondersteunt ook automatische gebruikers inrichting. u vindt [hier](salesforce-sandbox-provisioning-tutorial.md) meer informatie over het configureren van automatische gebruikers inrichting.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Sales Force-sandbox in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Sales Force-sandbox waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Een Sales Force-sandbox met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Inrichten van gebruikers configureren](salesforce-sandbox-provisioning-tutorial.md)

- [Sales Force-sandbox beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
