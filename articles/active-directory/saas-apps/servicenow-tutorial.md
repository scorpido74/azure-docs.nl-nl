---
title: 'Zelfstudie: SSO-integratie (Single Sign-On) van Azure Active Directory (SSO) met ServiceNow | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/25/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe6c857e5b0c2f48f27c167c177dbf1f4651986
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384106"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met ServiceNow

In deze zelfstudie leert u hoe u ServiceNow integreren met Azure Active Directory (Azure AD). Wanneer u ServiceNow integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot ServiceNow.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij ServiceNow met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een ServiceNow single sign-on (SSO) ingeschakeld abonnement.
* Voor ServiceNow, een instantie of huurder van ServiceNow, Calgary versie of hoger.
* Voor ServiceNow Express, een exemplaar van ServiceNow Express, Helsinki-versie of hoger.
* Voor de ServiceNow-tenant moet de [invoegtoepassing Multiple-Provider single sign-on (SSO)](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) zijn ingeschakeld. U dit doen door [een serviceaanvraag in te dienen.](https://hi.service-now.com)
* Schakel voor automatische configuratie de multi-provider-invoegtoepassing voor ServiceNow in.
* Als u de ServiceNow Classic -toepassing (Mobile) wilt installeren, gaat u naar de juiste winkel en zoekt u naar de ServiceNow Classic-toepassing. Download het dan.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. 

* ServiceNow ondersteunt **SP** geïnitieerde SSO.

* ServiceNow ondersteunt [geautomatiseerde gebruikersinrichting.](servicenow-provisioning-tutorial.md)

* Zodra u de ServiceNow configureert, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* U de ServiceNow Classic -toepassing (Mobile) configureren met Azure AD voor het inschakelen van SSO. Het ondersteunt zowel Android- als iOS-gebruikers. In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

## <a name="add-servicenow-from-the-gallery"></a>ServiceNow toevoegen vanuit de galerie

Voor het configureren van de integratie van ServiceNow in Azure AD moet u ServiceNow uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de **Azure Active Directory-service.**
1. Ga naar **Ondernemingstoepassingen**en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Voer **ServiceNow** in het zoekvak in in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **ServiceNow** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Azure AD-aanmelding voor ServiceNow configureren en testen

Azure AD SSO configureren en testen met ServiceNow met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ServiceNow.

Als u Azure AD SSO wilt configureren en testen met ServiceNow, voert u de volgende bouwstenen in:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD-enkele aanmelding met B.Simon te testen.
    1. [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
    1. [Configureer Azure AD SSO voor ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) zodat uw gebruikers deze functie kunnen gebruiken.
2. [Configureer ServiceNow](#configure-servicenow) om de SSO-instellingen aan de toepassingszijde te configureren.
    1. [Maak een ServiceNow-testgebruiker](#create-servicenow-test-user) om een tegenhanger van B.Simon in ServiceNow te hebben, gekoppeld aan de Azure AD-weergave van de gebruiker.
    1. [Configureer ServiceNow Express SSO](#configure-servicenow-express-sso) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.  
3. [Test SSO](#test-sso) om te controleren of de configuratie werkt.
4. [Test SSO voor ServiceNow Classic (Mobile)](#test-sso-for-servicenow-classic-mobile) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ServiceNow-toepassingsintegratie** de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het penpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, met het pictogram pen gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit:

    a. Voer **in Aanmelding smaken op URL**een URL in die het volgende patroon gebruikt:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. Voer in **Id (Entiteits-ID)** een URL in die het volgende patroon gebruikt:`https://<instance-name>.service-now.com`

    c. Voer voor **de URL van het antwoord**een van de volgende URL's in:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de werkelijke aanmeldings-URL en id, die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op **Certificaat (Base64)**. 

   ![Schermafbeelding van de sectie SAML-ondertekeningscertificaat, met Download gemarkeerd](common/certificatebase64.png)

   a. Selecteer de kopieerknop om **de url van de appfederatie-metagegevens**te kopiëren en deze in kladblok te plakken. Deze URL wordt later in de zelfstudie gebruikt.

    b. Selecteer **Downloaden** om **Certificaat(Base64)** te downloaden en sla het certificaatbestand op uw computer op.

1. Kopieer in de sectie **ServiceNow instellen** de juiste URL's op basis van uw vereiste.

   ![Schermafbeelding van de sectie ServiceNow instellen, met URL's gemarkeerd](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker, genaamd B.Simon, in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** > **Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer **Name**voor `B.Simon`Naam .  
   1. Voer **voor gebruikersnaam** username@companydomain.extensionde . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **Wachtwoord weergeven**en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot ServiceNow.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer **ServiceNow** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers en kies Selecteer **.**
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Azure AD SSO configureren voor ServiceNow Express

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **ServiceNow-toepassingsintegratie** de optie **eenmalige aanmelding**.

    ![Schermafbeelding van de pagina ServiceNow-toepassingsintegratie, met enkele aanmelding gemarkeerd](common/select-sso.png)

2. Selecteer in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![Schermafbeelding van Een enkele aanmeldingsmethode selecteren, waarbij SAML is gemarkeerd](common/select-saml-option.png)

3. Selecteer op de pagina **Eén aanmelding instellen met SAML** het penpictogram om het dialoogvenster **BasisSAML-configuratie** te openen.

    ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, waarbij het pictogram pen is gemarkeerd](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit:

    a. Voer **voor Aanmelding sMAKEN URL**een URL in die het volgende patroon gebruikt:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. Voer voor **Id (Entiteits-ID)** een URL in die het volgende patroon gebruikt:`https://<instance-name>.service-now.com`

    c. Voer voor **de URL van het antwoord**een van de volgende URL's in:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarden bijwerken met de werkelijke aanmeldings-URL en id, die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om het **certificaat (Base64)** te downloaden van de opgegeven opties, volgens uw vereiste. Sla het op uw computer op.

    ![Schermafbeelding van de sectie SAML-ondertekeningscertificaat, met Download gemarkeerd](common/certificatebase64.png)

6. U Azure AD ServiceNow automatisch laten configureren voor SAML-gebaseerde verificatie. Als u deze service wilt inschakelen, gaat u naar de sectie **ServiceNow instellen** en selecteert **u Stapsgewijze instructies weergeven** om het **aanmeldingsvenster configureren** te openen.

    ![Schermafbeelding van de sectie ServiceNow instellen, met stapsgewijze instructies weergeven gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Voer in het **aanmeldingsformulier configureren** de naam van uw ServiceNow-exemplaar, de gebruikersnaam van de beheerder en het beheerderswachtwoord in. Selecteer **Nu configureren**. De opgegeven beheerdersgebruikersnaam moet de **security_admin-rol** in ServiceNow hebben toegewezen om dit te laten werken. Als u ServiceNow vervolgens handmatig wilt configureren om Azure AD als SAML-identiteitsprovider te gebruiken, selecteert u **Handmatig eenmalige aanmelding configureren.** Kopieer de **URL van afmelden, Azure AD-id en aanmeldings-URL** vanuit de sectie Snelnaslag.

    ![Schermafbeelding van Aanmeldingsformulier configureren met Nu configureren gemarkeerd](./media/servicenow-tutorial/configure.png "APP-URL configureren")

## <a name="configure-servicenow"></a>ServiceNu configureren

1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

1. Activeer de **integratie - Single Sign-on Installer-plug-in** voor meerdere provideren door de volgende stappen te volgen:

    a. Zoek in het linkerdeelvenster naar de sectie **Systeemdefinitie** in het zoekvak en selecteer **Plug-ins**.

    ![Schermafbeelding van de sectie Systeemdefinitie, met systeemdefinitie en plug-ins gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_03.png "Plug-in activeren")

    b. Zoeken naar **integratie - Installatieprogramma voor één aanmelding voor meerdere provideren**.

     ![Schermafbeelding van de pagina Systeemplug-ins, met integratie - Installatieprogramma voor één aanmelding seinen voor meerdere providers gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_04.png "Plug-in activeren")

    c. Selecteer de plug-in. Klik met de rechtermuisknop en selecteer **Activeren/upgraden**.

     ![Schermafbeelding van het menu met de rechtermuisknop in plug-in, met activeren/bijwerken gemarkeerd](./media/servicenow-tutorial/tutorial_activate.png "Plug-in activeren")

    d. Selecteer **Activeren**.

     ![Schermafbeelding van het dialoogvenster Plug-in activeren, met Activeren gemarkeerd](./media/servicenow-tutorial/tutorial_activate1.png "Plug-in activeren")

1. Zoek in het linkerdeelvenster naar de **sectie SSO met meerdere provideren** in de zoekbalk en selecteer **Eigenschappen**.

    ![Schermafbeelding van de sectie SSO voor meerdere leveranciers, waarbij SSO en Eigenschappen van meerdere provideren zijn gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_06.png "APP-URL configureren")

1. Voer in het dialoogvenster **SSO-eigenschappen van meerdere provideren** de volgende stappen uit:

    ![Schermafbeelding van het dialoogvenster SSO-eigenschappen van meerdere provideren](./media/servicenow-tutorial/ic7694981.png "APP-URL configureren")

    * Selecteer **Ja** **voor Meerdere provider SSO inschakelen**.
  
    * Selecteer **Ja** **voor het automatisch importeren van gebruikers van alle identiteitsproviders in de gebruikerstabel**inschakelen .

    * Selecteer **Ja**voor **foutopsporingslogboekregistratie inschakelen voor de sso-integratie van meerdere provideren**.

    * Voor **Het veld in de gebruikerstabel dat...**, **e-mail**invoeren .
  
    * Selecteer **Opslaan**.

1. U ServiceNow automatisch of handmatig configureren. Voer de volgende stappen uit om ServiceNow automatisch te configureren:

    1. Ga terug naar de single sign-on-pagina **serviceNow** in de Azure-portal.

    1. ServiceNow met één klik configureren wordt geleverd voor ServiceNow. Als u deze service wilt inschakelen, gaat u naar de sectie **ServiceNow-configuratie** en selecteert **u ServiceNow configureren** om het **aanmeldingsvenster configureren** te openen.

        ![Schermafbeelding van ServiceNow instellen, met stapsgewijze instructies weergeven gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. Voer in het **aanmeldingsformulier configureren** de naam van uw ServiceNow-exemplaar, de gebruikersnaam van de beheerder en het beheerderswachtwoord in. Selecteer **Nu configureren**. De opgegeven beheerdersgebruikersnaam moet de **security_admin-rol** in ServiceNow hebben toegewezen om dit te laten werken. Als u ServiceNow vervolgens handmatig wilt configureren om Azure AD als SAML-identiteitsprovider te gebruiken, selecteert u **Handmatig eenmalige aanmelding configureren.** Kopieer de **URL voor afmelding, SAML-entiteits-id en DE ENKELE AANMELDINGsservice-URL** van SAML vanuit de sectie Snelnaslag.

        ![Schermafbeelding van Aanmeldingsformulier configureren met Nu configureren gemarkeerd](./media/servicenow-tutorial/configure.png "APP-URL configureren")

    1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

       * In de automatische configuratie zijn alle benodigde instellingen geconfigureerd aan de **Kant van ServiceNow,** maar het **X.509-certificaat** is niet standaard ingeschakeld. U moet het handmatig toewijzen aan uw identiteitsprovider in ServiceNow. Volg deze stappen:

         1. Zoek in het linkerdeelvenster naar de **sectie SSO voor meerdere providers** in het zoekvak en selecteer **Identiteitsproviders**.

            ![Schermafbeelding van de sectie SSO voor meerdere providers, waarbij identiteitsproviders zijn gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_07.png "Eenmalige aanmelding configureren")

         1. Selecteer de automatisch gegenereerde identiteitsprovider.

            ![Schermafbeelding van identiteitsproviders, waarbij automatisch gegenereerde identiteitsprovider is gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_08.png "Eenmalige aanmelding configureren")

         1.  Voer in de sectie **Identity Provider** de volgende stappen uit:

             ![Schermafbeelding van de sectie Identiteitsprovider](./media/servicenow-tutorial/automatic_config.png "Eenmalige aanmelding configureren")

               * Voer voor **Naam**een naam in voor uw configuratie (bijvoorbeeld **Microsoft Azure Federated single sign-on).**

               * Verwijder de waarde **singlelogoutRequest** van de populated Identity Provider uit het tekstvak.

               * Kopieer de waarde van de **ServiceNow-startpagina** en plak deze in de URL van aanmelding in de sectie **ServiceNow Basic SAML-configuratie** van de **Azure-portal.**

                  > [!NOTE]
                  > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld:`https://fabrikam.service-now.com/navpage.do`).

              * Kopieer de **waarde entiteits-id / emittent** en plak deze in **id** in de sectie **ServiceNow Basic SAML-configuratie** van de Azure-portal.

              * Controleer of **nameid-beleid** is ingesteld op `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` waarde. 

         1. Schuif omlaag naar de sectie **X.509-certificaat** en selecteer **Bewerken**.

             ![Schermafbeelding van de sectie X.509-certificaat, waarbij Bewerken is gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_09.png "Eenmalige aanmelding configureren")

         1. Selecteer het certificaat en selecteer het pijl-pictogram pijl-rechts om het certificaat toe te voegen

            ![Schermafbeelding van verzameling, met pictogram certificaat en pijl-rechts gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_11.png "Eenmalige aanmelding configureren")

          1. Selecteer **Opslaan**.

          1. Selecteer **Toetsverbinding**in de rechterbovenhoek van de pagina .

             ![Schermafbeelding van de pagina, met testverbinding gemarkeerd](./media/servicenow-tutorial/tutorial_activate2.png "Plug-in activeren")

             > [!NOTE]
             > Als de testverbinding mislukt en u deze verbinding niet activeren, biedt ServiceNow wel de overschakelschakelaar aan. Je moet **Sys_properties invoeren. LIJST** in de **navigatie zoeken** en het opent de nieuwe pagina met systeemeigenschappen. Hier moet u een nieuwe eigenschap maken met de naam **glide.authentic.multisso.test.connection.mandatory** met **datatype** als **Waar/Onwaar** en vervolgens de **waarde** instellen als **False**.

             > ![Schermafbeelding van de pagina Testresultaten](./media/servicenow-tutorial/testconnection-fail.png "Eenmalige aanmelding configureren")
        
          1. Wanneer u om uw referenties wordt gevraagd, voert u deze in. U ziet de volgende pagina. De fout **sso-testresultaten** wordt verwacht. Negeer de fout en selecteer **Activeren**.

             ![Schermafbeelding van de pagina Testresultaten](./media/servicenow-tutorial/servicenowactivate.png "Eenmalige aanmelding configureren")
  
1. Voer de volgende stappen uit om **ServiceNow** handmatig te configureren:

    1. Meld u aan bij uw ServiceNow-toepassing als beheerder.

    1. Selecteer **identiteitsproviders**in het linkerdeelvenster .

        ![Schermafbeelding van Multi-Provider SSO, met identiteitsproviders gemarkeerd](./media/servicenow-tutorial/tutorial_servicenow_07.png "Eenmalige aanmelding configureren")

    1. **Selecteer**Nieuw in het dialoogvenster **Identiteitsproviders** .

        ![Schermafbeelding van het dialoogvenster Identiteitsproviders, met Nieuw gemarkeerd](./media/servicenow-tutorial/ic7694977.png "Eenmalige aanmelding configureren")

    1. Selecteer **SAML**in het dialoogvenster **Identiteitsproviders** .

        ![Schermafbeelding van het dialoogvenster Identiteitsproviders, met SAML gemarkeerd](./media/servicenow-tutorial/ic7694978.png "Eenmalige aanmelding configureren")

    1. Voer in **Metagegevens van de identiteitsprovider**importeren de volgende stappen uit:

        ![Schermafbeelding van metagegevens van de identiteitsprovider importeren, met URL en importeren gemarkeerd](./media/servicenow-tutorial/idp.png "Eenmalige aanmelding configureren")

        1. Voer de url van de **appfederatie-metagegevens in** die u hebt gekopieerd van de Azure-portal.

        1. Selecteer **Importeren**.

    1. Het leest de IDP metadata URL, en vult alle velden informatie.

        ![Schermafbeelding van identiteitsprovider](./media/servicenow-tutorial/ic7694982.png "Eenmalige aanmelding configureren")

        * Voer voor **Naam**een naam in voor uw configuratie (bijvoorbeeld **Microsoft Azure Federated single sign-on).**

        * Verwijder de waarde **singlelogoutRequest** van de populated Identity Provider uit het tekstvak.

        * Kopieer de waarde van de **ServiceNow-startpagina.** Plak deze in de URL van Aanmelding in de sectie **ServiceNow Basic SAML-configuratie** van de **Azure-portal.**

            > [!NOTE]
            > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld:`https://fabrikam.service-now.com/navpage.do`).

        * Kopieer de **waarde entiteits-id / emittent.** Plak deze in **de sectie Identifier** in de sectie Basic **SAML-configuratie van ServiceNow Basic SAML-configuratie** van de Azure-portal.

        * Controleer of **nameid-beleid** is ingesteld op `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` waarde.

        * Selecteer **Geavanceerd**. Voer **e-mail**in **in gebruikersveld**.

            > [!NOTE]
            > U Azure AD configureren om de Azure AD-gebruikers-id (gebruikersnaam) of het e-mailadres uit te zenden als de unieke id in het SAML-token. Doe dit door naar het gedeelte **ServiceNow-kenmerken** > **Attributes** > Enkele aanmelding van de**Azure-portal** te gaan en het gewenste veld toe te geven aan het **kenmerk nameidentifier.** De waarde die is opgeslagen voor het geselecteerde kenmerk in Azure AD (bijvoorbeeld de hoofdnaam van de gebruiker) moet overeenkomen met de waarde die is opgeslagen in ServiceNow voor het ingevoerde veld (bijvoorbeeld user_name).

        * Selecteer **Verbinding testen** in de rechterbovenhoek van de pagina.

          > [!NOTE]
          > Als de testverbinding mislukt en u deze verbinding niet activeren, biedt ServiceNow wel de overschakelschakelaar aan. Je moet **Sys_properties invoeren. LIJST** in de **navigatie zoeken** en het opent de nieuwe pagina met systeemeigenschappen. Hier moet u een nieuwe eigenschap maken met de naam **glide.authentic.multisso.test.connection.mandatory** met **datatype** als **Waar/Onwaar** en vervolgens de **waarde** instellen als **False**.

          > ![Schermafbeelding van de pagina Testresultaten](./media/servicenow-tutorial/testconnection-fail.png "Eenmalige aanmelding configureren")

        * Wanneer u om uw referenties wordt gevraagd, voert u deze in. U ziet de volgende pagina. De fout **sso-testresultaten** wordt verwacht. Negeer de fout en selecteer **Activeren**.

          ![Schermafbeelding van de pagina Testresultaten](./media/servicenow-tutorial/servicenowactivate.png "Eenmalige aanmelding configureren")

### <a name="create-servicenow-test-user"></a>Een testgebruiker maken in ServiceNow

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in ServiceNow. ServiceNow ondersteunt automatische gebruikersinrichting, die standaard is ingeschakeld.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van ServiceNow Client](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>ServiceNow Express SSO configureren

1. Meld u aan bij uw ServiceNow Express-toepassing als beheerder.

2. Selecteer In het linkerdeelvenster de optie **Eén aanmelding**.

    ![Schermafbeelding van de ServiceNow Express-toepassing, waarbij één aanmelding is gemarkeerd](./media/servicenow-tutorial/ic7694980ex.png "APP-URL configureren")

3. Selecteer in het dialoogvenster Aanmelding voor **één aanmelding** het configuratiepictogram rechtsboven en stel de volgende eigenschappen in:

    ![Schermafbeelding van het dialoogvenster Eenmalig aanmelden](./media/servicenow-tutorial/ic7694981ex.png "APP-URL configureren")

    a. Schuif de schakelaar **Enable multiple provider SSO** naar rechts.

    b. Schuif de schakelaar **Enable debug logging for the multiple provider SSO integration** naar rechts.

    c. Voer **user_name**in **het veld op de gebruikerstabel in.**

4. Selecteer Nieuw **certificaat toevoegen**in het dialoogvenster **Aanmelding** toevoegen .

    ![Schermafbeelding van het dialoogvenster Eenmalig aanmelden, met Nieuw certificaat toevoegen gemarkeerd](./media/servicenow-tutorial/ic7694973ex.png "Eenmalige aanmelding configureren")

5. Voer in het dialoogvenster **X.509-certificaten** de volgende stappen uit:

    ![Schermafbeelding van het dialoogvenster X.509-certificaten](./media/servicenow-tutorial/ic7694975.png "Eenmalige aanmelding configureren")

    a. Voer voor **Naam**een naam in voor uw configuratie (bijvoorbeeld: **TestSAML2.0**).

    b. Selecteer **Active**.

    c. Selecteer **PEM**voor **Opmaak**.

    d. Selecteer Trust **store Cert**voor **Tekst**.

    e. Open uw basiscertificaat met een gecodeerd certificaat dat is gedownload van azure-portal in Kladblok. Kopieer de inhoud ervan in het klembord en plak deze vervolgens in het tekstvak **PEM-certificaat.**

    f. Selecteer **Bijwerken**

6. Selecteer In het dialoogvenster **Aanmelding** toevoegen de optie **Nieuwe IdP toevoegen**.

    ![Schermafbeelding van het dialoogvenster Eenmalig aanmelden, met Nieuwe IdP toevoegen gemarkeerd](./media/servicenow-tutorial/ic7694976ex.png "Eenmalige aanmelding configureren")

7. Voer in het dialoogvenster **Nieuwe identiteitsprovider toevoegen** onder **Identiteitsprovider configureren**de volgende stappen uit:

    ![Schermafbeelding van het dialoogvenster Nieuwe identiteitsprovider toevoegen](./media/servicenow-tutorial/ic7694982ex.png "Eenmalige aanmelding configureren")

    a. Voer voor **Naam**een naam in voor uw configuratie (bijvoorbeeld: **SAML 2.0**).

    b. Plak voor de URL van **de identiteitsprovider**de waarde van de identiteitsprovider-id die u hebt gekopieerd van de Azure-portal.

    c. Plak **voor AuthnRequest van de identiteitsprovider**de waarde van de URL van het verificatieverzoek die u hebt gekopieerd van de Azure-portal.

    d. Plak **voor SingleLogoutRequest**van de identiteitsprovider de waarde van de afmeld-URL die u hebt gekopieerd van de Azure-portal.

    e. Selecteer **voor het certificaat van identiteitsprovider**het certificaat dat u in de vorige stap hebt gemaakt.

8. Selecteer **Geavanceerde instellingen**. Voer **onder Eigenschappen van extra identiteitsprovider**de volgende stappen uit:

    ![Schermafbeelding van het dialoogvenster Nieuwe identiteitsprovider toevoegen, met geavanceerde instellingen gemarkeerd](./media/servicenow-tutorial/ic7694983ex.png "Eenmalige aanmelding configureren")

    a. Voer **voor Protocolbinding voor de SingleLogoutRequest van de IDP** **urn:oasis:names:tc:SAML:2.0:bindingen:HTTP-Redirect in**.

    b. Voer **voor NameID-beleid** **urn:oasis:names:tc:SAML:1.1:nameid-notatie:niet gespecificeerd in.**

    c. Voer **voor de AuthnContextClassRef-methode**. `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`

    d. Schakel deze in om deze in te schakelen voor **Een AuthnContextClass**maken om deze uit te schakelen (niet geselecteerd).

9. Voer onder **Additional Service Provider Properties** de volgende stappen uit:

    ![Schermafbeelding van het dialoogvenster Nieuwe identiteitsprovider toevoegen, met verschillende eigenschappen gemarkeerd](./media/servicenow-tutorial/ic7694984ex.png "Eenmalige aanmelding configureren")

    a. Voer **voor de startpagina van ServiceNow**de URL van uw startpagina van het ServiceNow-exemplaar in.

    > [!NOTE]
    > De startpagina van de ServiceNow-instantie is een samenvoeging van uw **ServiceNow-tenant-URL** en **/navpage.do** (bijvoorbeeld: `https://fabrikam.service-now.com/navpage.do`).

    b. Voer **voor Entiteits-ID / Uitgever**de URL van uw ServiceNow-tenant in.

    c. Voer **voor Audience URI**de URL van uw ServiceNow-tenant in.

    d. Voer **60** **in voor Klokscheefheid**.

    e. Voer **e-mail**in **voor gebruikersveld.**

    > [!NOTE]
    > U Azure AD configureren om de Azure AD-gebruikers-id (gebruikersnaam) of het e-mailadres uit te zenden als de unieke id in het SAML-token. Doe dit door naar het gedeelte **ServiceNow-kenmerken** > **Attributes** > Enkele aanmelding van de**Azure-portal** te gaan en het gewenste veld toe te geven aan het **kenmerk nameidentifier.** De waarde die is opgeslagen voor het geselecteerde kenmerk in Azure AD (bijvoorbeeld de hoofdnaam van de gebruiker) moet overeenkomen met de waarde die is opgeslagen in ServiceNow voor het ingevoerde veld (bijvoorbeeld user_name).

    f. Selecteer **Opslaan**.

## <a name="test-sso"></a>Test SSO

Wanneer u de tegel ServiceNow selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de ServiceNow waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-servicenow-classic-mobile"></a>Test SSO voor ServiceNow Classic (Mobiel)

1. Open uw **ServiceNow Classic (Mobile)** applicatie en voer de volgende stappen uit:

    a. Selecteer het plusteken in de rechterbenedenhoek.

    ![Schermafbeelding van de toepassing ServiceNow Classic, met plusteken gemarkeerd](./media/servicenow-tutorial/test03.png)

    b. Voer de naam van uw ServiceNow-instantie in en selecteer **Doorgaan**.

    ![Schermafbeelding van de pagina Instantie toevoegen, met Doorgaan gemarkeerd](./media/servicenow-tutorial/test04.png)

    c. Voer **op** de pagina Aanmelden de volgende stappen uit:

    ![Schermafbeelding van de aanmeldingspagina, waarbij externe aanmelding wordt gemarkeerd](./media/servicenow-tutorial/test01.png)

    *  Voer **gebruikersnaam** B.simon@contoso.comin, zoals .

    *  Selecteer **EXTERNE LOGIN GEBRUIKEN**. U wordt doorgestuurd naar de Azure AD-pagina voor aanmelding.

    *  Voer uw referenties in. Als er een verificatie van derden of een andere beveiligingsfunctie is ingeschakeld, moet de gebruiker dienovereenkomstig reageren. De **startpagina van** de toepassing wordt weergegeven.

        ![Schermafbeelding van de startpagina van de toepassing](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Gebruikersinrichting configureren](servicenow-provisioning-tutorial.md)

- [ServiceNow uitproberen met Azure AD](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [ServiceNow beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)