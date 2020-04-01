---
title: 'Zelfstudie: Azure Active Directory-integratie met Sectigo Certificate Manager | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67588239"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Zelfstudie: Azure Active Directory-integratie met Sectigo Certificate Manager

In deze zelfstudie leert u hoe u Sectigo Certificate Manager integreert met Azure Active Directory (Azure AD).

De integratie van Sectigo Certificate Manager met Azure AD biedt u de volgende voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot Sectigo Certificate Manager.
* Gebruikers kunnen automatisch worden aangemeld bij Sectigo Certificate Manager met hun Azure AD-accounts (single sign-on).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Single sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor toepassingen in Azure Active Directory voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Sectigo Certificate Manager, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Sectigo Certificate Manager-abonnement met één aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureer en test u Azure AD single sign-on in een testomgeving en integreert u Sectigo Certificate Manager met Azure AD.

Sectigo Certificate Manager ondersteunt de volgende functies:

* **SP-geïnitieerde single sign-on**
* **Door IDP geïnitieerde eenmalige aanmelding**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Sectigo-certificaatbeheer toevoegen aan de Azure-portal

Als u Sectigo Certificate Manager wilt integreren met Azure AD, moet u Sectigo Certificate Manager toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer Azure Active **Directory**in het linkermenu .

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Selecteer **Enterprise-toepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **Sectigo Certificate Manager**in . Selecteer in de zoekresultaten **Sectigo Certificate Manager**en selecteer **Vervolgens Toevoegen**.

    ![Sectigo Certificate Manager in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Sectigo Certificate Manager op basis van een testgebruiker met de naam **Britta Simon**. Voor eenmalige aanmelding aan het werk moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Sectigo Certificate Manager.

Als u Azure AD-singlesign-aan met Sectigo Certificate Manager wilt configureren en testen, moet u de volgende bouwstenen voltooien:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Secigo Certificate Manager-aanmelding configureren](#configure-sectigo-certificate-manager-single-sign-on)** | Hiermee configureert u de instellingen voor één aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test Azure AD single sign-on voor een gebruiker genaamd Britta Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Hiermee kan Britta Simon azure AD-enkele aanmelding gebruiken. |
| **[Een testgebruiker van Sectigo Certificate Manager maken](#create-a-sectigo-certificate-manager-test-user)** | Hiermee maakt u een tegenhanger van Britta Simon in Sectigo Certificate Manager die is gekoppeld aan de Azure AD-weergave van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Controleert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie configureert u Azure AD-eenmaligaanmelden met Sectigo Certificate Manager in de Azure-portal.

1. Selecteer in de [Azure-portal](https://portal.azure.com/)in het deelvenster **Toepassingsintegratie van Sectigo Certificate Manager** de optie **Eén aanmelding**.

    ![Optie eenmalig aanmelden configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Eén aanmeldingsmethode** de optie **SAML-** of **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. **Selecteer** bewerken (het potloodpictogram) in het deelvenster Eén aanmelding instellen met SAML om het deelvenster **BasisSAML-configuratie** te openen. **Set up Single Sign-On with SAML**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **BasisSAML-configuratie,** om de door *IDP geïnitieerde modus*te configureren, de volgende stappen uit:

    1. Voer in het vak **Id** een van deze URL's in:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Voer in het vak **URL beantwoorden** een van deze URL's in:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Selecteer **Extra URL's instellen**.

    1. Voer in het vak **Relaisstatus** een van deze URL's in:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Certificate Manager-domein- en URL's-informatie met eenmalige aanmelding](common/idp-relay.png)

1.  Voer de volgende stappen uit om de toepassing in *de door SP geïnitieerde modus te*configureren:

    * Voer **in** het vak Aanmelden op URL een van deze URL's in:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Certificate Manager-domein- en URL's-informatie met eenmalige aanmelding](common/both-signonurl.png)

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** naast **certificaat (Base64)**. Selecteer een downloadoptie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De downloadoptie Certificaat (Base64)](common/certificatebase64.png)

1. Kopieer in de sectie **Sectigo-certificaatbeheer instellen** de volgende URL's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Secigo Certificate Manager-aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de kant van Sectigo Certificate Manager, verzendt u het gedownloade certificaatbestand (Base64) en de relevante URL's die u hebt gekopieerd van de Azure-portal naar het [ondersteuningsteam van Sectigo Certificate Manager.](https://sectigo.com/support) Het ondersteuningsteam van Sectigo Certificate Manager gebruikt de informatie die u ze stuurt om ervoor te zorgen dat de SAML-single sign-on-verbinding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** > **Users** > **All users All users**.

    ![De opties voor gebruikers en alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **de> van brittasimon\@\<your-company-domain in.\< uitbreiding\>**. Bijvoorbeeld, **brittasimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

    ![Het deelvenster Gebruiker](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toegang tot Sectigo Certificate Manager, zodat ze Azure-enkele aanmelding kan gebruiken.

1. Selecteer in de Azure-portal **Enterprise-toepassingen** > **Alle toepassingen** > **Sectigo Certificate Manager**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Sectigo Certificate Manager**in de lijst met toepassingen .

    ![Sectigo Certificate Manager in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer **Britta Simon** in het deelvenster **Gebruikers en groepen** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het **rolvenster Selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer **Toewijzing toewijzen** in het deelvenster Toewijzing **toevoegen**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Een testgebruiker van Sectigo Certificate Manager maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Sectigo Certificate Manager. Werk samen met het [ondersteuningsteam van Sectigo Certificate Manager](https://sectigo.com/support) om de gebruiker toe te voegen aan het Sectigo Certificate Manager-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Nadat u één aanmelding hebt ingesteld, wordt u automatisch aangemeld bij Sectigo Certificate Manager wanneer u **Sectigo Certificate Manager** selecteert in de portal Mijn apps. Zie Apps openen en gebruiken [in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over de portal Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Bekijk deze artikelen voor meer informatie:

- [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


