---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SignalFx | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SignalFx

In deze zelfstudie leert u hoe u SignalFx integreren met Azure Active Directory (Azure AD). Wanneer u SignalFx integreert met Azure AD, u het als nog niet doen:

* Beheer vanuit Azure AD die toegang heeft tot SignalFx;
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SignalFx met hun Azure AD-accounts; En
* Beheer uw accounts op één locatie (de Azure-portal).

Zie [Wat is toepassingstoegang voor toepassingen en eenmalige aanmelding met Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van SaaS-toepassingen met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u:

* Een Azure AD-abonnement
    * Wanneer je nog geen abonnement hebt kun je hier een [gratis account aanmaken.](https://azure.microsoft.com/free/)
* SignalFx single sign-on (SSO) ingeschakeld abonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SignalFx ondersteunt **IDP** geïnitieerde SSO
* SignalFx ondersteunt **Just In Time** gebruikersinrichting
* Zodra u SignalFx hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselement strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Stap 1: De SignalFx-toepassing toevoegen in Azure

Gebruik deze instructies om de SignalFx-toepassing toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory**in het navigatievenster aan de linkerkant .
1. Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing**.
1. Voer in de sectie **Toevoegen vanuit de galerie** in het zoekvak **SignalFx**in en selecteer deze .
     * Het kan nodig zijn om een paar minuten te wachten voor de toepassing wordt toegevoegd aan uw tenant.
1. Laat de Azure-portal openen en open een nieuw webtabblad.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Stap 2: SignalFx SSO-configuratie starten

Gebruik deze instructies om het configuratieproces voor de SignalFx SSO te starten.

1. In het onlangs geopende tabblad u toegang krijgen tot en inloggen op de SignalFx-gebruikersinterface. 
1. Klik in het bovenste menu op **Integraties**. 
1. Voer in het zoekveld **Azure Active Directory**in en selecteer deze .
1. Klik **op Nieuwe integratie maken**.
1. Voer in **Naam**een gemakkelijk herkenbare naam in die uw gebruikers zullen begrijpen.
1. Markeren **Weergeven op de aanmeldingspagina**.
    * Deze functie geeft een aangepaste knop weer op de aanmeldingspagina waarop uw gebruikers kunnen klikken. 
    * De gegevens die u in **Naam** hebt ingevoerd, worden op de knop weergegeven. Voer daarom een **naam** in die uw gebruikers zullen herkennen. 
    * Deze optie werkt alleen als u een aangepast subdomein gebruikt voor de SignalFx-toepassing, zoals **yourcompanyname.signalfx.com.** Neem contact op met SignalFx-ondersteuning om een aangepast subdomein te verkrijgen. 
1. Kopieer de **integratie-id**. U zult deze informatie in een latere stap nodig hebben. 
1. Laat de SignalFx UI open. 

## <a name="step-3-configure-azure-ad-sso"></a>Stap 3: Azure AD SSO configureren

Gebruik deze instructies om Azure AD SSO in te schakelen in de Azure-portal.

1. Ga terug naar de [Azure-portal](https://portal.azure.com/)en zoek op de integratiepagina **van de SignalFx-toepassing** de sectie **Beheren** en selecteer **Vervolgens Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram pen (bewerken) voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Vul op de pagina **Eén aanmelding instellen met SAML** de volgende velden in: 

    a. Voer in **Id**de `https://api.<realm>.signalfx.com/v1/saml/metadata` volgende `<realm>` URL in en vervang deze door uw SignalFx-realm. 

    b. Voer in De URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` van `<realm>` **antwoord**de volgende URL in `<integration ID>` en vervang deze door uw SignalFx-realm, evenals met de **integratie-id** die u eerder hebt gekopieerd van de SignalFx-gebruikersinterface.

1. SignalFx-toepassing verwacht de SAML-beweringen in een specifiek formaat, waarvoor u aangepaste attribuuttoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. 
    
1. Controleer en controleer of de volgende claims worden toegewezen aan de bronkenmerken die worden ingevuld in de Active Directory. 

    | Name |  Bronkenmerk|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Dit proces vereist dat uw Active Directory is geconfigureerd met ten minste één geverifieerd aangepast domein en toegang heeft tot de e-mailaccounts in dit domein. Als u niet zeker weet of hulp nodig hebt bij deze configuratie, neem dan contact op met signalfx-ondersteuning.  

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op **Certificaat (Base64)** en selecteer **Download**. Download het certificaat en sla het op uw computer op. Kopieer vervolgens de **url-waarde van de appfederatie.** u deze informatie in een latere stap in de SignalFx UI nodig hebt. 

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **SignalFx instellen** de waarde **van azure AD-id.** U hebt deze informatie in een latere stap in de SignalFx-gebruikersinterface nodig. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Stap 4: Een Azure AD-testgebruiker maken

Gebruik deze instructies om een testgebruiker te maken in de Azure-portal genaamd **B.Simon**.

1. Selecteer in de Azure-portal in het navigatievenster aan de linkerkant **Azure Active Directory,** selecteer **vervolgens Gebruikers**en selecteer alle **gebruikers**.
1. Selecteer boven aan de pagina De optie **Nieuwe gebruiker**.
1. In de eigenschappen **gebruiker:**
   1. Voer **User name**in gebruikersnaam `username@companydomain.extension`, `b.simon@contoso.com`zoals .
   1. In **Naam** `B.Simon`, voer .
   1. Het **wachtwoord weergeven**markeren en kopieer de weergegeven waarde in **Wachtwoord**. U hebt deze informatie later nodig om deze integratie te testen. 
   1. Klik **op Maken**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Stap 5: De Azure AD-testgebruiker toewijzen

Gebruik deze instructies om de testgebruiker in staat te stellen Azure single sign-on voor SignalFx te gebruiken.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **SignalFx**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **vervolgens Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens in het dialoogvenster Toewijzing **toevoegen** de optie Gebruikers **en groepen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** in de lijst **Gebruikers** de optie **B.Simon**en klik vervolgens onder aan de pagina op **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u onder aan de pagina op **Selecteren.**
1. Klik in het dialoogvenster **Toewijzing toevoegen** op **toewijzen**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Stap 6: Voltooi de SignalFx SSO-configuratie 

1. Open het vorige tabblad en ga terug naar de Gebruikersinterface van SignalFx om de huidige Azure Active Directory-integratiepagina weer te geven. 
1. Klik naast **Certificate (Base64)** op **Bestand uploaden**en zoek vervolgens het bestand bestand **base64 gecodeerd certificaat** dat u eerder hebt gedownload van azure-portal.
1. Plak naast **Azure AD-id**de Azure **AD-id-waarde** die u eerder hebt gekopieerd uit de Azure-portal. 
1. Plak naast **de URL van federatiemetagegevens**de **url-waarde van de appfederatie-metagegevens** die u eerder hebt gekopieerd van de Azure-portal. 
1. Klik op **Opslaan**.

## <a name="step-7-test-sso"></a>Stap 7: Test SSO

Bekijk de volgende informatie over het testen van SSO, evenals de verwachtingen voor het inloggen op SignalFx voor de eerste keer. 

### <a name="test-logins"></a>Aanmeldingen testen

* Als u de aanmelding wilt testen, moet u een privé-/incognitovenster gebruiken of u zich afmelden bij de Azure-portal. Zo niet, dan worden cookies voor de gebruiker die de toepassing heeft geconfigureerd, interfereert en voorkomen u een succesvolle aanmelding bij de testgebruiker.

* Wanneer een nieuwe testgebruiker zich voor de eerste keer aanmeldt, forceert Azure een wachtwoordwijziging. Wanneer dit gebeurt, wordt het SSO-aanmeldingsproces niet voltooid; de testgebruiker wordt naar de Azure-portal geleid. Om problemen op te lossen, moet de testgebruiker zijn wachtwoord wijzigen en naar de signalfx-aanmeldingspagina of naar het toegangspaneel navigeren en het opnieuw proberen.
    * Wanneer u op de SignalFx-tegel in het access panel klikt, moet u automatisch worden aangemeld bij de SignalFx. 
        * Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

* SignalFx applicatie kan worden benaderd vanuit het Access Panel of via een aangepaste login pagina toegewezen aan de organisatie. De testgebruiker moet de integratie vanaf een van deze locaties testen.
    * De testgebruiker kan de eerder in dit **b.simon@contoso.com**proces gemaakte referenties gebruiken voor .

### <a name="first-time-logins"></a>Eerste aanmeldingen

* Wanneer een gebruiker zich voor het eerst aanmeldt bij SignalFx van de SAML SSO, ontvangt de gebruiker een SignalFx-e-mail met een link. De gebruiker moet op de koppeling klikken voor verificatiedoeleinden. Deze e-mailvalidatie vindt alleen plaats voor nieuwe gebruikers. 

* SignalFx ondersteunt **Just In Time** user creation, wat betekent dat als een gebruiker niet bestaat in SignalFx, het account van de gebruiker wordt gemaakt bij de eerste inlogpoging.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Probeer SignalFx met Azure AD](https://aad.portal.azure.com/)