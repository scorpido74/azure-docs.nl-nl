---
title: 'Zelfstudie: Azure Active Directory-integratie met Kontiki | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098492"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Zelfstudie: Azure Active Directory-integratie met Kontiki

In deze zelfstudie leert u hoe Kontiki wordt geïntegreerd met Azure Active Directory (Azure AD).

De integratie van Kontiki met Azure AD biedt u de volgende voordelen:

* U Azure AD gebruiken om te bepalen wie toegang heeft tot Kontiki.
* Gebruikers kunnen automatisch worden aangemeld bij Kontiki met hun Azure AD-accounts (single sign-on).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Single sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor toepassingen in Azure Active Directory voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie met Kontiki voor Azure wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Kontiki-abonnement met enkele aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureer en test u Azure AD single sign-on in een testomgeving en integreert u Kontiki met Azure AD.

Kontiki ondersteunt de volgende functies:

* **SP-geïnitieerde single sign-on**
* **Just In Time inrichten van gebruikers**

## <a name="add-kontiki-in-the-azure-portal"></a>Kontiki toevoegen aan de Azure-portal

Als u Kontiki wilt integreren met Azure AD, moet u Kontiki toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer Azure Active **Directory**in het linkermenu .

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Selecteer **Enterprise-toepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **Kontiki**in. Selecteer **Kontiki**in de zoekresultaten en selecteer **Vervolgens Toevoegen**.

    ![Kontiki in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Kontiki op basis van een testgebruiker genaamd **Britta Simon**. Voor eenmalige aanmelding aan het werk moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Kontiki.

Als u Azure AD-singlesign-aan met Kontiki wilt configureren en testen, moet u de volgende bouwstenen voltooien:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Kontiki-aanmelding configureren](#configure-kontiki-single-sign-on)** | Hiermee configureert u de instellingen voor één aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test Azure AD single sign-on voor een gebruiker genaamd Britta Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Hiermee kan Britta Simon azure AD-enkele aanmelding gebruiken. |
| **[Een Kontiki-testgebruiker maken](#create-a-kontiki-test-user)** | Hiermee maakt u een tegenhanger van Britta Simon in Kontiki die is gekoppeld aan de Azure AD-weergave van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Controleert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie configureert u Azure AD-enkeleaanmelding met Kontiki in de Azure-portal.

1. Selecteer in de [Azure-portal](https://portal.azure.com/)in het deelvenster Integratie van **de Toepassing Kontiki** de optie **Eén aanmelding**.

    ![Optie eenmalig aanmelden configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Eén aanmeldingsmethode** de optie **SAML-** of **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. **Selecteer** bewerken (het potloodpictogram) in het deelvenster Eén aanmelding instellen met SAML om het deelvenster **BasisSAML-configuratie** te openen. **Set up Single Sign-On with SAML**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **BasisSAML-configuratie** in het tekstvak **URL ondertekenen** een URL in met het volgende patroon:`https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki-domein- en URL's meteenmeldingsinformatie](common/sp-signonurl.png)

    > [!NOTE]
    > Neem contact op met het [Ondersteuningsteam van Kontiki Client](https://customersupport.kontiki.com/enterprise/contactsupport.html) om de juiste waarde te gebruiken. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** naast XML **met federatiemetagegevens**. Selecteer een downloadoptie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De optie XML-certificaatdownloaden met federatiemetagegevens](common/metadataxml.png)

1. Kopieer in de sectie **Kontiki instellen** de volgende URL's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Kontiki-aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de Kontiki-kant, verzendt u het gedownloade XML-bestand federatiemetagegevens en de relevante URL's die u hebt gekopieerd van de Azure-portal naar het [Kontiki-ondersteuningsteam.](https://customersupport.kontiki.com/enterprise/contactsupport.html) Het Kontiki-ondersteuningsteam gebruikt de informatie die u ze stuurt om ervoor te zorgen dat de SAML-single sign-on-verbinding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** > **Users** > **All users All users**.

    ![De opties voor gebruikers en alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **de> van brittasimon\@\<your-company-domain in.\< uitbreiding>**. Bijvoorbeeld, **brittasimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

    ![Het deelvenster Gebruiker](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toegang tot Kontiki, zodat ze Azure-enkele aanmelding kan gebruiken.

1. Selecteer in de Azure-portal **Enterprise-toepassingen** > **Alle toepassingen** > **Kontiki**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Kontiki**in de lijst met toepassingen .

    ![Kontiki in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer **Britta Simon** in het deelvenster **Gebruikers en groepen** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het **rolvenster Selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer **Toewijzing toewijzen** in het deelvenster Toewijzing **toevoegen**.

### <a name="create-a-kontiki-test-user"></a>Een Kontiki-testgebruiker maken

Er is geen actie-item voor u om gebruikersinrichting in Kontiki te configureren. Wanneer een toegewezen gebruiker zich probeert aan te melden bij Kontiki met behulp van de mijn apps-portal, controleert Kontiki of de gebruiker bestaat. Als er geen gebruikersaccount wordt gevonden, maakt Kontiki automatisch het gebruikersaccount aan.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Nadat u één aanmelding hebt ingesteld, wordt u automatisch aangemeld bij Kontiki wanneer u **Kontiki** selecteert in de mijn apps-portal. Zie Apps openen en gebruiken [in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over de portal Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Bekijk deze artikelen voor meer informatie:

- [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
