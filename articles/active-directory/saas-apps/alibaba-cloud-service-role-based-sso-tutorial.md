---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Alibaba-Cloud service (op rollen gebaseerde SSO) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Alibaba-Cloud service (op rollen gebaseerde SSO).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77367934"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Alibaba-Cloud service (op rollen gebaseerde SSO)

In deze zelf studie leert u hoe u Alibaba Cloud service kunt integreren (op rollen gebaseerde SSO) met Azure Active Directory (Azure AD). Wanneer u Alibaba-Cloud service (SSO op basis van rollen) integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Alibaba-Cloud service (SSO op basis van functie).
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Alibaba-Cloud service (SSO) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Alibaba-Cloud service (SSO) eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Alibaba Cloud service (SSO op basis van rollen) ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Alibaba Cloud service (SSO op basis van rollen) toevoegen vanuit de galerie

Als u de integratie van Alibaba Cloud service (SSO op basis van een functie) wilt configureren in azure AD, moet u de Alibaba-Cloud service (op rollen gebaseerde SSO) toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Alibaba Cloud service (op rollen gebaseerde SSO)** in het zoekvak.
1. Selecteer **Alibaba-Cloud service (SSO op basis van functie)** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.
5. Klik op de pagina **Alibaba-Cloud service (SSO)** op **Eigenschappen** in het navigatie deel venster aan de linkerkant en kopieer de **object-id** en sla deze op uw computer op voor later gebruik.

    ![Configuratie van eigenschappen](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Eenmalige aanmelding van Azure AD voor Alibaba Cloud service configureren en testen (op rollen gebaseerde SSO)

Configureer en test Azure AD SSO met Alibaba Cloud service (SSO op basis van een functie) met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Alibaba Cloud service (op rollen gebaseerde SSO).

Als u Azure AD SSO wilt configureren en testen met Alibaba Cloud service (SSO op basis van een functie), voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Configureer eenmalige aanmelding op basis van een functie in Alibaba Cloud service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Alibaba-Cloud service (SSO) SSO configureren](#configure-alibaba-cloud-service-role-based-sso-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Alibaba-Cloud service (op rollen gebaseerde SSO) test gebruiker](#create-alibaba-cloud-service-role-based-sso-test-user)** : een equivalent van Julia Simon in Alibaba Cloud service (SSO op basis van een functie) die is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[Eén SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Alibaba-Cloud service (op rollen gebaseerde SSO)** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U ontvangt de meta gegevens van de service provider via deze [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Zodra het meta gegevensbestand is geüpload, worden de **id** -en **antwoord-URL** -waarden automatisch ingevuld in Alibaba Cloud service (op rollen gebaseerd SSO) tekstvak:

    > [!Note]
    > Als de **id** -en **antwoord-URL** -waarden niet automatisch worden ingevuld, vult u de waarden hand matig in volgens uw vereiste.

1. Alibaba-Cloud service (op rollen gebaseerde SSO) vereist dat rollen worden geconfigureerd in azure AD. De rol claim is vooraf geconfigureerd, zodat u deze niet hoeft te configureren, maar u moet ze nog steeds in azure AD maken met dit [artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Alibaba-Cloud service instellen (SSO op basis van functie)** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Alibaba-Cloud service (op rollen gebaseerde SSO).

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Alibaba Cloud service (SSO op basis van functie)**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer op het tabblad **gebruikers en groepen** de optie U2 in de lijst met gebruikers en klik op **selecteren**. Klik vervolgens op **toewijzen**.

    ![Configuratie testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Bekijk de toegewezen rol en test de Cloud service Alibaba (op rollen gebaseerde SSO).

    ![Configuratie testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Nadat u de gebruiker (U2) hebt toegewezen, wordt de gemaakte rol automatisch aan de gebruiker gekoppeld. Als u meerdere rollen hebt gemaakt, moet u de juiste rol aan de gebruiker koppelen, indien nodig. Als u op rollen gebaseerde SSO wilt implementeren vanuit Azure AD naar meerdere Alibaba-Cloud accounts, herhaalt u de voor gaande stappen.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Eenmalige aanmelding op basis van een functie configureren in Alibaba Cloud service

1. Meld u aan bij de Alibaba Cloud [RAM-console](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) met behulp van Account1.

2. Selecteer **SSO**in het navigatie deel venster aan de linkerkant.

3. Klik op het tabblad **op rollen gebaseerd SSO** op **IDP maken**.

4. Voer `AAD` op de weer gegeven pagina in het veld IDP naam een beschrijving in het veld **Opmerking** in, klik op **uploaden** om het bestand met federatieve meta gegevens te uploaden dat u eerder hebt gedownload en klik vervolgens op **OK**.

5. Nadat de IdP is gemaakt, klikt u op **RAM-rol maken**.

6. In het veld naam van de **RAM-rol** invoeren `AADrole`, selecteert u in de vervolg keuzelijst `AAD` **Select IDP** en klikt u op OK.

    >[!NOTE]
    >U kunt de rol zo nodig machtigen. Nadat u de IdP en de bijbehorende rol hebt gemaakt, raden we u aan de ARNs van de IdP en de rol voor later gebruik op te slaan. U kunt de ARNs verkrijgen op de pagina IdP Information en de functie gegevens.

7. De Alibaba Cloud RAM-rol (AADrole) koppelen aan de Azure AD-gebruiker (U2): als u de RAM-rol wilt koppelen aan de Azure AD-gebruiker, moet u een rol in azure AD maken door de volgende stappen te volgen:

    a. Meld u aan bij de [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Klik op **machtigingen wijzigen** om de vereiste machtigingen voor het maken van een rol te verkrijgen.

    ![Grafiek configuratie](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecteer de volgende machtigingen in de lijst en klik op **machtigingen wijzigen**, zoals wordt weer gegeven in de volgende afbeelding.

    ![Grafiek configuratie](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Nadat de machtigingen zijn verleend, meldt u zich opnieuw aan bij de Graph Explorer.

    d. Selecteer op de pagina Graph Explorer de optie **ophalen** in de eerste vervolg keuzelijst en **beta** in de tweede vervolg keuzelijst. Voer `https://graph.microsoft.com/beta/servicePrincipals` vervolgens in het veld naast de vervolg keuzelijsten in en klik op **query uitvoeren**.

    ![Grafiek configuratie](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Als u meerdere directory's gebruikt, kunt u in het `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` veld van de query invoeren.

    e. Extraheer de eigenschap appRoles van de service-principal voor later gebruik in de sectie **antwoord voorbeeld** .

    ![Grafiek configuratie](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >U kunt de eigenschap appRoles vinden door in `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` het veld van de query op te geven. Houd er rekening `objectID` mee dat de object-id die u hebt gekopieerd van de Azure AD- **Eigenschappen** pagina.

    f. Ga terug naar de Graph Explorer, wijzig de methode van **down load** to **patch**, plak de volgende inhoud in het gedeelte **hoofd tekst** van de aanvraag en klik op **query uitvoeren**:
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
    > De `value` is de ARNs van de IDP en de rol die u hebt gemaakt in de RAM-console. Hier kunt u meerdere rollen toevoegen als dat nodig is. Azure AD verzendt de waarde van deze rollen als de claim waarde in het SAML-antwoord. U kunt echter alleen nieuwe rollen toevoegen na het `msiam_access` onderdeel voor de patch-bewerking. Om het aanmaak proces te vereenvoudigen, wordt u aangeraden een ID-generator, zoals GUID-generator, te gebruiken om Id's in realtime te genereren.

    g. Wanneer de Service-Principal is geïnstalleerd met de vereiste rol, koppelt u de rol aan de Azure AD-gebruiker (U2) door de stappen van de **Azure AD-test gebruiker toewijzen** van de zelf studie te volgen.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Alibaba-Cloud service configureren (SSO) SSO

Als u eenmalige aanmelding wilt configureren op **Alibaba Cloud service (SSO)** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar het [ondersteunings team van de Alibaba-Cloud service (op rollen gebaseerd SSO)](https://www.aliyun.com/service/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Alibaba Cloud service maken (op rollen gebaseerde SSO) test gebruiker

In deze sectie maakt u een gebruiker met de naam Julia Simon in Alibaba Cloud service (op rollen gebaseerde SSO). Werk met het [ondersteunings team van Alibaba Cloud service (op rollen gebaseerd SSO)](https://www.aliyun.com/service/) om de gebruikers toe te voegen aan de Alibaba-Cloud service (op rollen gebaseerd SSO). Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen 

Nadat de voor gaande configuraties zijn voltooid, test u de Alibaba-Cloud service (SSO op basis van een functie) door de volgende stappen uit te voeren:

1. Ga in het Azure Portal naar de pagina **Alibaba Cloud service (SSO op basis van functie)** , selecteer **eenmalige aanmelding**en klik op **testen**.

    ![Configuratie testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klik op **Aanmelden als huidige gebruiker**.

    ![Configuratie testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Selecteer op de pagina voor het selecteren van accounts de optie U2.

    ![Configuratie testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. De volgende pagina wordt weer gegeven, waarmee wordt aangegeven dat op rollen gebaseerde SSO is geslaagd.

    ![Configuratie testen](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Alibaba-Cloud service (SSO op basis van een functie) met Azure AD](https://aad.portal.azure.com/)

