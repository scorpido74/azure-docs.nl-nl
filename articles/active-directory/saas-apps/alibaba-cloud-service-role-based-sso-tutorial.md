---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Alibaba Cloud Service (Role-based SSO) | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Alibaba Cloud Service (Role-based SSO).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e22bec224d185d0306f2b0032aef929f627c910e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77367934"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Alibaba Cloud Service (Role-based SSO)

In deze zelfstudie leert u hoe u Alibaba Cloud Service (Role-based SSO) integreren met Azure Active Directory (Azure AD). Wanneer u Alibaba Cloud Service (Role-based SSO) integreert met Azure AD, u het:

* Controle in Azure AD die toegang heeft tot Alibaba Cloud Service (Role-based SSO).
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Alibaba Cloud Service (Role-based SSO) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Alibaba Cloud Service (Role-based SSO) single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Alibaba Cloud Service (Role-based SSO) ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Alibaba Cloud Service (Role-based SSO) toevoegen vanuit de galerij

Om de integratie van Alibaba Cloud Service (Role-based SSO) in Azure AD te configureren, moet u Alibaba Cloud Service (Role-based SSO) vanuit de galerij toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Alibaba Cloud Service (Role-based SSO)** in de sectie **Toevoegen vanuit de galerij** in het zoekvak.
1. Selecteer **Alibaba Cloud Service (Role-based SSO)** in het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.
5. Klik op de pagina **Alibaba Cloud Service (Role-based SSO)** op **Eigenschappen** in het navigatiedeelvenster aan de linkerkant en kopieer de **object-id** en sla deze op uw computer op voor later gebruik.

    ![Eigenschappen config](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Azure AD-singlesign-on configureren en testen voor Alibaba Cloud Service (Op rollen gebaseerde SSO)

Azure AD SSO configureren en testen met Alibaba Cloud Service (Role-based SSO) met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Alibaba Cloud Service (Role-based SSO).

Als u Azure AD SSO wilt configureren en testen met Alibaba Cloud Service (Role-based SSO), voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Configureer Role-Based Single Sign-On in Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Configureer Alibaba Cloud Service (Role-based SSO) SSO](#configure-alibaba-cloud-service-role-based-sso-sso)** - om de Single Sign-On-instellingen aan de toepassingszijde te configureren.
    1. **[Maak Alibaba Cloud Service (Role-based SSO) testgebruiker](#create-alibaba-cloud-service-role-based-sso-test-user)** - om een tegenhanger van Britta Simon in Alibaba Cloud Service (Role-based SSO) die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
3. **[Test enkele SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Alibaba Cloud Service (Role-based SSO) applicatie-integratie,** de sectie **Beheren** en selecteer **eenmalige aanmelding.**
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U krijgt de metadata van de serviceprovider van deze [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Zodra het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in het tekstvak van de sectie SSO (Role-based SSO):

    > [!Note]
    > Als de **URL-waarden Id** en **Antwoord** niet automatisch worden ingevuld, vult u de waarden handmatig in op basis van uw vereiste.

1. Alibaba Cloud Service (Role-based SSO) vereist dat rollen worden geconfigureerd in Azure AD. De rolclaim is vooraf geconfigureerd, zodat u deze niet hoeft te configureren, maar u moet ze nog steeds maken in Azure AD met behulp van dit [artikel.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Alibaba Cloud Service (Role-based SSO)** de juiste URL(s) op basis van uw behoefte.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Alibaba Cloud Service (Role-based SSO).

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Alibaba Cloud Service (Role-based SSO)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer op het tabblad **Gebruikers en groepen** u2 in de gebruikerslijst en klik op **Selecteren**. Klik vervolgens op **Toewijzen**.

    ![Testconfig](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Bekijk de toegewezen rol en test Alibaba Cloud Service (Role-based SSO).

    ![Testconfig](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Nadat u de gebruiker (u2) hebt toegewezen, wordt de gemaakte rol automatisch aan de gebruiker gekoppeld. Als u meerdere rollen hebt gemaakt, moet u de juiste rol zo nodig aan de gebruiker koppelen. Als u op rollen gebaseerde SSO wilt implementeren van Azure AD naar meerdere Alibaba Cloud-accounts, herhaalt u de voorgaande stappen.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Functiegebaseerde aanmelding configureren in Alibaba Cloud Service

1. Meld u aan bij de Alibaba Cloud [RAM-console](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) via Account1.

2. Selecteer **SSO**in het navigatiedeelvenster aan de linkerkant .

3. Klik op het tabblad **SSO op basis van rollen** op **IdP maken**.

4. Voer `AAD` op de weergegeven pagina het veld IdP-naam in, voer een beschrijving in het veld **Notitie** in, klik op **Uploaden** om het eerder gedownloade bestand metagegevens van de federatie te uploaden en klik op **OK.**

5. Nadat de IdP is gemaakt, klikt u op **RAM-rol maken**.

6. Selecteer in het veld `AADrole` **RAM-rolnaam** enter , selecteer `AAD` in de vervolgkeuzelijst **IdP selecteren** en klik op OK.

    >[!NOTE]
    >U indien nodig toestemming geven voor de rol. Nadat u de IdP en de bijbehorende rol hebt gemaakt, raden we u aan de ARN's van de IdP en de rol voor volgend gebruik op te slaan. U de ARN's verkrijgen op de idp-informatiepagina en de pagina rolinformatie.

7. De AADrole (Alibaba Cloud RAM) koppelen aan de Azure AD-gebruiker (u2): Als u de RAM-rol wilt koppelen aan de Azure AD-gebruiker, moet u een rol in Azure AD maken door de volgende stappen te volgen:

    a. Meld u aan bij de [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Klik **op Machtigingen wijzigen** om vereiste machtigingen voor het maken van een rol te verkrijgen.

    ![Grafiek config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecteer de volgende machtigingen in de lijst en klik op **Machtigingen wijzigen,** zoals in de volgende afbeelding wordt weergegeven.

    ![Grafiek config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Nadat machtigingen zijn verleend, meldt u zich opnieuw aan bij de Grafiekverkenner.

    d. Selecteer op de pagina Graph Explorer **GET** in de eerste vervolgkeuzelijst en **bèta** in de tweede vervolgkeuzelijst. Voer `https://graph.microsoft.com/beta/servicePrincipals` vervolgens in het veld naast de vervolgkeuzelijsten in en klik op **Query uitvoeren**.

    ![Grafiek config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Als u meerdere mappen gebruikt, `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` kunt u deze invoeren in het veld van de query.

    e. Haal in de sectie **Reactievoorbeeld** de eigenschap appRollen uit de 'Serviceprincipal' voor later gebruik.

    ![Grafiek config](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >U de eigenschap appRoles vinden door in het veld van de query in te voeren. `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` Houd er `objectID` rekening mee dat het object-id is dat u hebt gekopieerd van de pagina Azure **AD-eigenschappen.**

    f. Ga terug naar de Grafiekverkenner, wijzig de methode van **GET** naar **PATCH,** plak de volgende inhoud in de sectie **Hoofdaanvraag** en klik op **Query uitvoeren:**
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > Het `value` zijn de ARN's van de IdP en de rol die u hebt gemaakt in de RAM-console. Hier u meerdere rollen toevoegen als dat nodig is. Azure AD verzendt de waarde van deze rollen als claimwaarde in SAML-respons. U echter alleen nieuwe `msiam_access` rollen toevoegen na het onderdeel voor de patchbewerking. Om het creatieproces soepel te laten verlopen, raden we u aan een ID-generator, zoals GUID Generator, te gebruiken om id's in realtime te genereren.

    g. Nadat de 'Serviceprincipal' is gepatcht met de vereiste rol, koppelt u de rol aan de Azure AD-gebruiker (u2) door de stappen te volgen van het gedeelte **Azure AD-testgebruikers** toewijzen van de zelfstudie.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Alibaba Cloud Service (Role-based SSO) SSO configureren

Als u eenmalige aanmelding wilt configureren aan de Kant **van Alibaba Cloud Service (Role-based SSO),** moet u de gedownloade **Federation Metadata XML** en de juiste gekopieerde URL's van Azure portal naar [Alibaba Cloud Service (Role-based SSO) support team](https://www.aliyun.com/service/)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Alibaba Cloud Service (Role-based SSO) testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Alibaba Cloud Service (Role-based SSO). Werk samen met [alibaba Cloud Service (Role-based SSO) support team](https://www.aliyun.com/service/) om de gebruikers toe te voegen in het Alibaba Cloud Service (Role-based SSO) platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO 

Nadat de voorgaande configuraties zijn voltooid, test u Alibaba Cloud Service (Role-based SSO) door de volgende stappen te volgen:

1. Ga in de Azure-portal naar de pagina **Alibaba Cloud Service (Role-based SSO),** selecteer **Single sign-on**en klik op **Testen**.

    ![Testconfig](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klik op **Aanmelden als huidige gebruiker**.

    ![Testconfig](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Selecteer u2 op de pagina accountselectie.

    ![Testconfig](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. De volgende pagina wordt weergegeven, wat aangeeft dat op rollen gebaseerde SSO succesvol is.

    ![Testconfig](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Alibaba Cloud Service (Role-based SSO) met Azure AD](https://aad.portal.azure.com/)

