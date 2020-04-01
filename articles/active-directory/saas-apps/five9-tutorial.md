---
title: 'Zelfstudie: Azure Active Directory-integratie met Five9 Plus-adapter (CTI, Contact Center Agents) | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Five9 Plus Adapter (CTI, Contact Center Agents).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: dd4eab2d8e877cac468097c0a6ed6aa3cfb750b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102456"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Zelfstudie: Azure Active Directory-integratie met Five9 Plus-adapter (CTI, Contact Center Agents)

In deze zelfstudie leert u hoe u Five9 Plus Adapter (CTI, Contact Center Agents) integreert met Azure Active Directory (Azure AD).
De integratie van Five9 Plus Adapter (CTI, Contact Center Agents) met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Five9 Plus Adapter (CTI, Contact Center Agents).
* U uw gebruikers automatisch laten aanmelden bij Five9 Plus Adapter (CTI, Contact Center Agents) (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Five9 Plus-adapter (CTI, Contact Center Agents), hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Eenmalige aanmeldingsabonnement voor Five9 Plus Adapter (CTI, Contact Center Agents)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Five9 Plus Adapter (CTI, Contact Center Agents) ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Toevoegen Five9 Plus Adapter (CTI, Contact Center Agents) uit de galerie

Als u de integratie van Five9 Plus Adapter (CTI, Contact Center Agents) wilt configureren in Azure AD, moet u Five9 Plus Adapter (CTI, Contact Center Agents) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om de Five9 Plus-adapter (CTI, Contact Center Agents) toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Five9 Plus Adapter (CTI, Contact Center Agents)** en selecteer **Five9 Plus Adapter (CTI, Contact Center Agents)** in het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Five9 Plus Adapter (CTI, Contact Center Agents) in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Five9 Plus Adapter (CTI, Contact Center Agents) op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Five9 Plus Adapter (CTI, Contact Center Agents).

Als u Azure AD-singlesign-aan wilt configureren en testen met Five9 Plus Adapter (CTI, Contact Center Agents), moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer de single sign-on -aan -instellingen voor één aanmelding voor five9 plus-adapter (CTI, Contact Center Agents)](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak de testgebruiker Five9 Plus Adapter (CTI, Contact Center Agents)](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** om een tegenhanger van Britta Simon in Five9 Plus Adapter (CTI, Contact Center Agents) te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD te configureren met De Vijf9 Plus-adapter (CTI, Contact Center Agents):

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Five9 Plus Adapter (CTI, Contact Center Agents)** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL's van 59 Plus-adapter (CTI, Contact Center Agents)](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 
    
    |    Omgeving      |       URL      |
    | :-- | :-- |
    | Voor "Five9 Plus-adapter voor Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | For "Five9 Plus Adapter for Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Voor "Five9 Plus-adapter voor Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    |      Omgeving     |      URL      |
    | :--                  | :--           |
    | Voor "Five9 Plus-adapter voor Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | For "Five9 Plus Adapter for Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Voor "Five9 Plus-adapter voor Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Five9 Plus Adapter (CTI, Contact Center Agents)** de juiste URL(s) naar uw behoefte.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Eenmalige aanmelding voor Five9 Plus-adapter (CTI, Contact Center Agents) configureren

1. Als u de kant van de single sign-on op **de Five9 Plus-adapter (CTI, Contact Center Agents)** wilt configureren, moet u het gedownloade **certificaat(Base64)** en de juiste gekopieerde URL(s) naar [het ondersteuningsteam van Five9 Plus-adapters (CTI, Contact Center Agents)](https://www.five9.com/about/contact)verzenden. Ook voor het configureren van SSO verder volg dan de onderstaande stappen volgens de adapter:

    a. Admin Guide voor "Five9 Plus-adapter voor Agent Desktop Toolkit":[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Beheerhandleiding voor "Five9 Plus-adapter voor Microsoft Dynamics CRM":[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Admin Guide voor "Five9 Plus-adapter voor Zendesk":[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Five9 Plus Adapter (CTI, Contact Center Agents).

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens Five9 Plus Adapter (CTI, Contact Center Agents)**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Five9 Plus Adapter (CTI, Contact Center Agents)** in de lijst met toepassingen.

    ![De koppeling Five9 Plus-adapter (CTI, Contact Center Agents) in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Testgebruiker Five9 Plus-adapter (CTI, Contact Center Agents)

In deze sectie maakt u een gebruiker genaamd Britta Simon in Five9 Plus Adapter (CTI, Contact Center Agents). Werk met [het ondersteuningsteam van Five9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact) om de gebruikers toe te voegen in het Five9 Plus Adapter (CTI, Contact Center Agents) platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Five9 Plus-adapter (CTI, contactcenteragents-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Five9 Plus-adapter (CTI, Contact Center Agents) waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

