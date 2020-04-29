---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SignalFx | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149718dcd325ef6bd6a6754ba100ffdc34be0a07
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79136411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SignalFx

In deze zelf studie leert u hoe u SignalFx integreert met Azure Active Directory (Azure AD). Wanneer u SignalFx integreert met Azure AD, kunt u het volgende doen:

* Besturings element van Azure AD die toegang heeft tot SignalFx;
* Stel in dat uw gebruikers automatisch worden aangemeld bij SignalFx met hun Azure AD-accounts. maar
* Beheer uw accounts op één locatie (de Azure Portal).

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van SaaS-toepassingen met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u het volgende nodig:

* Een Azure AD-abonnement
    * Als u geen abonnement hebt, kunt u [hier een gratis account](https://azure.microsoft.com/free/)aanvragen.
* SignalFx-abonnement met eenmalige aanmelding (SSO)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie gaat u Azure AD-SSO configureren en testen in een test omgeving.

* SignalFx ondersteunt door **IDP** GEÏNITIEERDe SSO
* SignalFx ondersteunt **just-in-time** -gebruikers inrichting
* Nadat u SignalFx hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Stap 1: de toepassing SignalFx toevoegen in azure

Gebruik deze instructies om de toepassing SignalFx toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory**in het navigatie venster aan de linkerkant.
1. Selecteer **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Nieuwe toepassing**.
1. In de sectie **toevoegen vanuit de galerie** typt en selecteert u **SignalFx**in het zoekvak.
     * Mogelijk moet u enkele minuten wachten totdat de toepassing is toegevoegd aan uw Tenant.
1. Verlaat de Azure Portal open en open vervolgens een nieuw tabblad Web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Stap 2: SignalFx SSO-configuratie starten

Gebruik deze instructies om het configuratie proces voor de SignalFx SSO te starten.

1. Open het tabblad geopend en meld u aan bij de SignalFx-gebruikers interface. 
1. Klik in het bovenste menu op **integraties**. 
1. Voer in het zoek veld **Azure Active Directory**in en selecteer het.
1. Klik op **nieuwe integratie maken**.
1. Voer bij **naam**een gemakkelijk herken bare naam in die uw gebruikers begrijpen.
1. Markeer **weer geven op de aanmeldings pagina**.
    * Met deze functie wordt een aangepaste knop op de aanmeldings pagina weer gegeven waarop uw gebruikers kunnen klikken. 
    * De gegevens die u in **naam** hebt ingevoerd, worden weer gegeven op de knop. Als gevolg hiervan voert u een **naam** in die door uw gebruikers wordt herkend. 
    * Deze optie werkt alleen als u een aangepast subdomein gebruikt voor de SignalFx-toepassing, zoals **yourcompanyname.signalfx.com**. Neem contact op met SignalFx-ondersteuning om een aangepast subdomein te verkrijgen. 
1. Kopieer de **integratie-id**. U hebt deze informatie nodig in een latere stap. 
1. De SignalFx-gebruikers interface geopend laten. 

## <a name="step-3-configure-azure-ad-sso"></a>Stap 3: Azure AD SSO configureren

Gebruik deze instructies om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga terug naar het [Azure Portal](https://portal.azure.com/)en zoek op de pagina Toepassings integratie van **SignalFx** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram pen (bewerken) voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Vul op de pagina **eenmalige aanmelding met SAML instellen** de volgende velden in: 

    a. Voer in **id**de volgende URL `https://api.<realm>.signalfx.com/v1/saml/metadata` in en vervang `<realm>` door uw SignalFx-realm. 

    b. Voer **bij antwoord-URL**de volgende URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` in en `<realm>` Vervang door uw SignalFx-realm, evenals `<integration ID>` met de **integratie-id** die u eerder hebt gekopieerd via de SignalFx-gebruikers interface.

1. De SignalFx-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. 
    
1. Controleer en controleer of de volgende claims worden toegewezen aan de bron kenmerken die zijn ingevuld in de Active Directory. 

    | Naam |  Bronkenmerk|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | Gebruiker. e-mail adres  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Dit proces vereist dat uw Active Directory is geconfigureerd met ten minste één geverifieerd aangepast domein en dat toegang heeft tot de e-mail accounts in dit domein. Als u niet zeker weet of hulp nodig hebt bij deze configuratie, neemt u contact op met de SignalFx-ondersteuning.  

1. Op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** zoeken naar **certificaat (base64)** en selecteert u **downloaden**. Down load het certificaat en sla het op uw computer op. Kopieer vervolgens de URL-waarde voor de **federatieve meta gegevens** van de app. u hebt deze informatie nodig in een latere stap in de SignalFx-gebruikers interface. 

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de **Azure ad-id** -waarde in de sectie **SignalFx instellen** . U hebt deze informatie nodig in een latere stap in de SignalFx-gebruikers interface. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Stap 4: een Azure AD-test gebruiker maken

Gebruik deze instructies om een test gebruiker te maken in de Azure Portal met de naam **B. Simon**.

1. In de Azure Portal, in het navigatie venster aan de linkerkant, selecteert u **Azure Active Directory**, selecteert u **gebruikers**en selecteert u vervolgens **alle gebruikers**.
1. Selecteer aan de bovenkant van de pagina **nieuwe gebruiker**.
1. In de **gebruikers** eigenschappen:
   1. Voer `username@companydomain.extension`in `b.simon@contoso.com` **gebruikers naam**, zoals.
   1. Voer **Name** `B.Simon`bij naam in.
   1. Markeer **wacht woord weer geven**en kopieer de weer gegeven waarde in het **wacht woord**. U hebt deze informatie nodig in de volgende stap om deze integratie te testen. 
   1. Klik op **maken**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Stap 5: de Azure AD-test gebruiker toewijzen

Gebruik deze instructies om de test gebruiker in staat te stellen om eenmalige aanmelding van Azure te gebruiken voor SignalFx.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SignalFx**.
1. Zoek de sectie **beheren** op de pagina overzicht van de app en selecteer vervolgens **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** in de lijst **gebruikers** de optie **B. Simon**en klik onder aan de pagina op **selecteren**.
1. Als u een wille keurige rol in de SAML-bewering verwacht, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op **selecteren** onder aan de pagina.
1. Klik in het dialoog venster **toewijzing toevoegen** op de **toewijzen**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Stap 6: de SignalFx SSO-configuratie volt ooien 

1. Open het vorige tabblad en ga terug naar de SignalFx-gebruikers interface om de huidige Azure Active Directory Integration-pagina weer te geven. 
1. Klik naast **certificaat (base64)** op **bestand uploaden**en zoek het met **Base64 gecodeerde certificaat** bestand dat u eerder hebt gedownload van Azure Portal.
1. Plak naast **Azure ad id**de waarde voor de **Azure ad-id** die u eerder hebt gekopieerd uit het Azure Portal. 
1. Plak naast **URL voor federatieve meta**gegevens de waarde voor de URL van de **app Federation-meta gegevens** die u eerder hebt gekopieerd uit de Azure Portal. 
1. Klik op **Opslaan**.

## <a name="step-7-test-sso"></a>Stap 7: SSO testen

Bekijk de volgende informatie over het testen van SSO, evenals verwachtingen voor de eerste keer aanmelden bij SignalFx. 

### <a name="test-logins"></a>Aanmeldingen testen

* Als u de aanmelding wilt testen, gebruikt u een privé-incognito venster of kunt u zich afmelden bij de Azure Portal. Als dat niet het geval is, zorgt u ervoor dat de cookies voor de gebruiker die de toepassing heeft geconfigureerd, een geslaagde aanmelding met de test gebruiker belemmeren.

* Wanneer een nieuwe test gebruiker zich voor de eerste keer aanmeldt, wordt een wachtwoord wijziging door Azure geforceerd. Als dit gebeurt, wordt het SSO-aanmeldings proces niet voltooid. de test gebruiker wordt omgeleid naar de Azure Portal. Om problemen op te lossen, moet de test gebruiker het wacht woord wijzigen, naar de aanmeldings pagina van SignalFx of naar het toegangs paneel gaan en het opnieuw proberen.
    * Wanneer u op de tegel SignalFx in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SignalFx. 
        * Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

* SignalFx-toepassing kan worden geopend vanuit het toegangs venster of via een aangepaste aanmeldings pagina die aan de organisatie is toegewezen. De test gebruiker moet de integratie testen vanaf een van deze locaties.
    * De test gebruiker kan de referenties gebruiken die eerder in dit proces zijn **b.simon@contoso.com**gemaakt voor.

### <a name="first-time-logins"></a>Aanmeldingen voor de eerste keer

* Wanneer een gebruiker zich voor de eerste keer aanmeldt bij SignalFx van de SAML SSO, ontvangt de gebruiker een SignalFx-e-mail met een koppeling. De gebruiker moet op de koppeling klikken voor verificatie doeleinden. Deze e-mail validatie vindt alleen plaats voor de eerste keer dat gebruikers worden uitgevoerd. 

* SignalFx ondersteunt **just-in-time** gebruiker maken, wat betekent dat als een gebruiker niet aanwezig is in SignalFx, het account van de gebruiker wordt gemaakt bij de eerste aanmeldings poging.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Probeer SignalFx met Azure AD](https://aad.portal.azure.com/)