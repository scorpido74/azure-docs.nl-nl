---
title: 'Zelfstudie: Azure Active Directory-integratie met Salesforce Sandbox | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Salesforce Sandbox.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76290000"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Salesforce Sandbox

In deze zelfstudie leert u hoe u Salesforce Sandbox integreren met Azure Active Directory (Azure AD). Wanneer u Salesforce Sandbox integreert met Azure AD, u het als nog niet doen:

* Beheer in Azure AD die toegang heeft tot Salesforce Sandbox.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Salesforce Sandbox met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Salesforce Sandbox single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Salesforce Sandbox ondersteunt **SP en IDP** gestart met SSO
* Salesforce Sandbox ondersteunt **Just In Time** gebruikersinrichting
* Salesforce Sandbox ondersteunt [ **geautomatiseerde** gebruikersinrichting](salesforce-sandbox-provisioning-tutorial.md)
* Zodra u de Salesforce Sandbox hebt geconfigureerd, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce Sandbox toevoegen vanuit de galerie

Als u de integratie van Salesforce Sandbox in Azure AD wilt configureren, moet u Salesforce Sandbox vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Salesforce Sandbox** in het zoekvak in de sectie Toevoegen in **de galeriesectie.**
1. Selecteer **Salesforce Sandbox** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Azure AD-aanmelding voor Salesforce Sandbox configureren en testen

Azure AD SSO configureren en testen met Salesforce Sandbox met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Salesforce Sandbox.

Als u Azure AD SSO wilt configureren en testen met Salesforce Sandbox, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Salesforce Sandbox SSO configureren](#configure-salesforce-sandbox-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Salesforce Sandbox-testgebruiker maken](#create-salesforce-sandbox-test-user)** - om een tegenhanger van B.Simon te hebben in Salesforce Sandbox die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Salesforce Sandbox-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u **het metagegevensbestand van serviceproviderprovider** hebt en wilt configureren in de **idp-modus:**

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    > [!NOTE]
    > U krijgt het metadatabestand van de serviceprovider van de Salesforce Sandbox-beheerportal, dat later in de zelfstudie wordt uitgelegd.

    c. Nadat het metagegevensbestand is geüpload, wordt de **url-waarde van het antwoord** automatisch ingevuld in het tekstvak **Van de URL van antwoord.**

    ![installatiekopie](common/both-replyurl.png)

    > [!Note]
    > Als de **URL-waarde van het antwoord** niet automatisch wordt gepolueerd, vult u de waarde handmatig in op basis van uw vereiste.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Salesforce Sandbox** de juiste URL(s) naar uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Salesforce Sandbox.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Salesforce Sandbox**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-salesforce-sandbox-sso"></a>Salesforce Sandbox SSO configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij uw Salesforce Sandbox-beheerdersaccount.

2. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure1.png)

3. Schuif omlaag naar de **instellingen** in het linkernavigatiedeelvenster en klik op **Identiteit** om de gerelateerde sectie uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

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

9. Klik op de pagina **Instellingen voor één aanmelding** op de knop **Metagegevens downloaden** om het metagegevensbestand van de serviceprovider te downloaden. Gebruik dit bestand in de sectie **BasisSAML-configuratie** in de Azure-portal voor het configureren van de benodigde URL's zoals hierboven uitgelegd.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure4.png)

10. Als u de toepassing in de door **SP** gestarte modus wilt configureren, volgen de volgende voorwaarden hiervoor:

    a. U moet een geverifieerd domein hebben.

    b. U moet uw domein configureren en inschakelen in Salesforce Sandbox, stappen hiervoor worden later in deze zelfstudie uitgelegd.

    c. Klik in de Azure-portal in de sectie **BasisSAML-configuratie** op **Extra URL's instellen** en voer de volgende stap uit:
  
    ![Salesforce Sandbox Domain en URL's single sign-on information](common/both-signonurl.png)

    Typ in het tekstvak **AANmeldings-URL** de waarde met het volgende patroon:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Deze waarde moet worden gekopieerd van de Salesforce Sandbox-portal zodra u het domein hebt ingeschakeld.

11. Klik in de sectie **SAML-ondertekeningscertificaat** op **Federation Metadata XML** en sla het xml-bestand op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

12. Open een nieuw tabblad in uw browser en meld u aan bij uw Salesforce Sandbox-beheerdersaccount.

13. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure1.png)

14. Schuif omlaag naar de **instellingen** in het linkernavigatiedeelvenster en klik op **Identiteit** om de gerelateerde sectie uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klik **op Bestand kiezen** om het XML-bestand met metagegevens te uploaden en klik op **Maken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Op de pagina **SAML-aanmeldingsinstellingen** worden velden automatisch ingevuld, typt u de naam van de configuratie *(bijvoorbeeld: SPSSOWAAD_Test),* in het tekstvak **Naam** en klik op Opslaan.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Voer de volgende stappen uit om uw domein in Te schakelen in Salesforce Sandbox:

    > [!NOTE]
    > Voordat u het domein inschakelt, moet u hetzelfde maken op Salesforce Sandbox. Zie [Uw domeinnaam definiëren](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)voor meer informatie. Zodra het domein is gemaakt, moet u ervoor zorgen dat het correct is geconfigureerd.

21. Klik in het linkernavigatiedeelvenster in Salesforce Sandbox op **Bedrijfsinstellingen** om de gerelateerde sectie uit te vouwen en klik vervolgens op **Mijn domein**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Klik in de sectie **Verificatieconfiguratie** op **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Selecteer in de sectie **Verificatieconfiguratie** als **Verificatieservice**de naam van de SAML Single Sign-On-instelling die u hebt ingesteld tijdens de SSO-configuratie in Salesforce Sandbox en klik op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Salesforce Sandbox-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Salesforce Sandbox. Salesforce Sandbox ondersteunt just-in-time provisioning, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Salesforce Sandbox, wordt er een nieuwe gemaakt wanneer u probeert toegang te krijgen tot Salesforce Sandbox. Salesforce Sandbox ondersteunt ook automatische gebruikersinrichting, u [hier](salesforce-sandbox-provisioning-tutorial.md) meer informatie vinden over het configureren van automatische gebruikersinrichting.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Salesforce Sandbox in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Salesforce Sandbox waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Salesforce Sandbox uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Gebruikersinrichting configureren](salesforce-sandbox-provisioning-tutorial.md)

- [Salesforce Sandbox beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
