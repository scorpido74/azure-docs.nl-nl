---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Splashtop | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Splashtop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c05f63c2-4170-49ce-a967-be1cb1dbcd06
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6ecb03130e26d432f0bd10980c7c3553ce9f8b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77539784"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Splashtop

In deze zelfstudie leert u hoe U Splashtop integreren met Azure Active Directory (Azure AD). Wanneer u Splashtop integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Splashtop.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Splashtop met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Splashtop single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Splashtop ondersteunt **SP** geïnitieerde SSO

* Zodra u de Splashtop hebt geconfigureerd, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Splashtop toevoegen vanuit de galerie

Als u de integratie van Splashtop in Azure AD wilt configureren, moet u Splashtop uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Splashtop** in het zoekvak in de sectie Toevoegen in de sectie Toevoegen in **de galerie.**
1. Selecteer **Splashtop** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Azure AD-aanmelding voor Splashtop configureren en testen

Azure AD SSO configureren en testen met Splashtop met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Splashtop.

Als u Azure AD SSO wilt configureren en testen met Splashtop, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Splashtop SSO](#configure-splashtop-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Splashtop-testgebruiker maken](#create-splashtop-test-user)** - om een tegenhanger van B.Simon in Splashtop te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Splashtop-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    Typ de URL in het tekstvak **AANmeldings-URL:**`https://my.splashtop.com/login/sso`

1. Splashtop-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven, terwijl **naam-id** is toegewezen met **user.userprincipalname**. TicketManager-toepassing verwacht **dat naam-id** wordt toegewezen met **user.mail,** dus u moet de toewijzing van het kenmerk bewerken door te klikken op pictogram **Bewerken** en de toewijzing van het kenmerk wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Splashtop instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Splashtop.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Splashtop**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-splashtop-sso"></a>Splashtop SSO configureren

In deze sectie moet u een nieuwe SSO-methode aanvragen bij [splashtop-webportal.](https://my.splashtop.com/login)
1. Ga in de Splashtop-webportal naar het tabblad **Accountinfo** / **Team** en scrol naar beneden om de sectie **Eén aanmelding** te zoeken. Klik vervolgens op **Nieuwe SSO-methode toepassen**.

    ![installatiekopie](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Geef op het solliciteervenster een **SSO-naam**. New Azure selecteer bijvoorbeeld **Azure** als Het IDP-type en voeg **inlog-URL** en **Azure AD-id** in die zijn gekopieerd uit de Splashtop-toepassing op Azure-portal.

    ![installatiekopie](media/splashtop-tutorial/azure-sso-1.png)

1. Voor certificaatgegevens klikt u met de rechtermuisknop op het cert-bestand dat is gedownload van de Splashtop-toepassing op de Azure-portal, bewerkt u deze met Kladblok en kopieert u de inhoud en plakt u het in het veld **Certificaat downloaden (Base64).**

    ![](media/splashtop-tutorial/cert-1.png) ![afbeelding](media/splashtop-tutorial/cert-2.png) ![](media/splashtop-tutorial/azure-sso-2.png)

1. Dat is alles. Klik **op Opslaan** en het Splashtop SSO-validatieteam neemt contact met u op voor de verificatiegegevens en activeert vervolgens de SSO-methode.

### <a name="create-splashtop-test-user"></a>Splashtop-testgebruiker maken

1. Nadat de SSO-methode is geactiveerd, controleert u de nieuw gemaakte SSO-methode om deze in te schakelen in de sectie **Eén aanmelding.**

    ![installatiekopie](media/splashtop-tutorial/enable.png)

1. Nodig de testgebruiker bijvoorbeeld `B.Simon@contoso.com` uit voor uw Splashtop-team met de nieuw gemaakte SSO-methode.

    ![installatiekopie](media/splashtop-tutorial/invite.png)

1. U ook een bestaand Splashtop-account wijzigen in een SSO-account, zie [instructies](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Dat is alles. U het SSO-account gebruiken om u aan te melden bij splashtop-webportal of splashtop business-app.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de splashtop-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Splashtop waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Splashtop uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Splashtop beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)