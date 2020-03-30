---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Adobe Creative Cloud | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25dd638c15fecbef787e4ceabea9ae7cb4359582
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120329"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Adobe Creative Cloud

> [!NOTE]
> In dit artikel wordt de aangepaste SAML-installatie van Adobe Admin Console voor Azure Active Directory (Azure AD) beschreven. Voor gloednieuwe configuraties raden we u aan de [Azure AD Connector te](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html)gebruiken. Azure AD Connector kan binnen enkele minuten worden ingesteld en verkort het proces van domeinclaim, single sign-on setup en gebruikerssynchronisatie.

In deze zelfstudie leert u hoe u Adobe Creative Cloud integreren met Azure Active Directory (Azure AD). Wanneer u Adobe Creative Cloud integreert met Azure AD, u het als nog niet doen:

* Beheer in Azure AD die toegang heeft tot Adobe Creative Cloud.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Adobe Creative Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement met één aanmelding (Adobe Creative Cloud( SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Adobe Creative Cloud ondersteunt door **SP** geïnitieerde eenmalige aanmelding





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adobe Creative Cloud toevoegen vanuit de galerie

Om de integratie van Adobe Creative Cloud met Azure AD te configureren, moet u Adobe Creative Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Adobe Creative Cloud** in het zoekvak in de sectie Toevoegen in de **galerie.**
1. Selecteer **Adobe Creative Cloud** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Azure AD-aanmelding configureren en testen voor Adobe Creative Cloud

Azure AD SSO configureren en testen met Adobe Creative Cloud met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Adobe Creative Cloud.

Als u Azure AD SSO wilt configureren en testen met Adobe Creative Cloud, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Adobe Creative Cloud SSO configureren](#configure-adobe-creative-cloud-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Adobe Creative Cloud-testgebruiker maken](#create-adobe-creative-cloud-test-user)** - om een tegenhanger van B.Simon in Adobe Creative Cloud te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Adobe Creative Cloud-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://adobe.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > De id-waarde is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [ondersteuningsteam van Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Adobe Creative Cloud-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste toewijzingen voor kenmerken moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Adobe Creative Cloud applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.

    | Name | Bronkenmerk|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > De waarde voor de claim Email kan alleen worden gevuld in het SAML-antwoord als gebruikers beschikken over een geldige licentie voor Office 365 ExO.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **Federation Data XML**en selecteer Vervolgens **Downloaden** om het XML-metagegevensbestand te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Adobe Creative Cloud instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Adobe Creative Cloud.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Adobe Creative Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-adobe-creative-cloud-sso"></a>Adobe Creative Cloud SSO configureren

1. Meld u in een ander browservenster aan bij [Adobe Admin Console](https://adminconsole.adobe.com) als systeembeheerder.

1. Ga naar **Instellingen** op de bovenste navigatiebalk en kies **Identiteit**. De lijst van mappen wordt geopend. Selecteer de gewenste federatielijst.

1. Selecteer op de pagina **Adreslijstdetails** de optie **Configureren**.

1. Kopieer de entiteits-id en de ACS-URL (URL van de Bevestigingsconsumentenservice of antwoord-URL). Voer de URL's in op de juiste velden in de Azure-portal.

    ![Eenmalige aanmelding configureren aan de app-kant](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Gebruik de entiteits-id-waarde die Adobe hebt opgegeven voor **id** in het dialoogvenster **App-instellingen configureren.**

    b. Gebruik de ACS-URL -waarde (Assertion Consumer Service URL) Adobe heeft u in het dialoogvenster **App-instellingen configureren** opgegeven voor **de URL van Antwoord.**

1. Upload onder aan de pagina het **XML-bestand Federatiegegevens** dat u hebt gedownload van de Azure-portal. 

    ![XML-bestand federatiegegevens](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "XML met idp-metagegevens")

1. Selecteer **Opslaan**.


### <a name="create-adobe-creative-cloud-test-user"></a>Een testgebruiker voor Adobe Creative Cloud definiëren

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Adobe Creative Cloud, moeten ze worden ingericht in Adobe Creative Cloud. In het geval van Adobe Creative Cloud moet dit handmatig gebeuren.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als een beheerder aan bij [Adobe Admin Console](https://adminconsole.adobe.com).

2. Voeg de gebruiker toe als een federatieve id en wijs een productprofiel toe aan de gebruiker. Zie [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) voor uitgebreide informatie over het toevoegen van gebruikers. 

3. Typ op dit moment uw e-mailadres/upn in het adobe-aanmeldingsformulier, druk op het tabblad en moet u worden teruggezet naar Azure AD:
   * Webtoegang:\.www adobe.com > aanmelding
   * In het hulpprogramma voor desktop-app > aanmelden
   * In de toepassing > help > aanmelden

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Adobe Creative Cloud klikt in het toegangsvenster, wordt u als het goed is automatisch aangemeld bij het exemplaar van Adobe Creative Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Adobe Creative Cloud uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Een identiteit instellen (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Configure Microsoft Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

