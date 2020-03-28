---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Otsuka Shokai | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Otsuka Shokai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c04bb636-a3c7-4940-9b36-810425b855d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af9acde38df91f2505a85119fb83b88cf4879df9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75658692"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-otsuka-shokai"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Otsuka Shokai

In deze zelfstudie leert u hoe u Otsuka Shokai integreren met Azure Active Directory (Azure AD). Wanneer u Otsuka Shokai integreert met Azure AD, u het als:

* Controle in Azure AD die toegang heeft tot Otsuka Shokai.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Otsuka Shokai met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Otsuka Shokai single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Otsuka Shokai ondersteunt **IDP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-otsuka-shokai-from-the-gallery"></a>Otsuka Shokai toevoegen vanuit de galerie

Als u de integratie van Otsuka Shokai in Azure AD wilt configureren, moet u Otsuka Shokai uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Otsuka Shokai** in het zoekvak in de sectie **Toevoegen van de galerie.**
1. Selecteer **Otsuka Shokai** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-otsuka-shokai"></a>Azure AD-aanmelding configureren en testen voor Otsuka Shokai

Azure AD SSO configureren en testen met Otsuka Shokai met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppeling smaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Otsuka Shokai.

Als u Azure AD SSO wilt configureren en testen met Otsuka Shokai, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Otsuka Shokai SSO](#configure-otsuka-shokai-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Otsuka Shokai-testgebruiker](#create-otsuka-shokai-test-user)** - om een tegenhanger van B.Simon in Otsuka Shokai te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Otsuka Shokai-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Otsuka Shokai-toepassing verwacht de SAML-beweringen in een specifiek formaat, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. Otsuka Shokai-toepassing verwacht **dat naam-id** wordt toegewezen met **user.objectid,** dus u moet de toewijzing van het kenmerk bewerken door op **pictogram Bewerken** te klikken en de toewijzing van het kenmerk wijzigen.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, PureCloud door Genesys applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | Appid | `<Application ID>` |

    >[!NOTE]
    >`<Application ID>`is de waarde die u hebt gekopieerd van het tabblad **Eigenschappen** van azure-portal.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Otsuka Shokai.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Otsuka Shokai**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-otsuka-shokai-sso"></a>Otsuka Shokai SSO configureren

1. Wanneer u verbinding maakt met de Mijn pagina van de klant vanuit de SSO-app, wordt de wizard van de SSO-instelling gestart.

2. Als Otsuka ID niet is geregistreerd, ga dan naar Otsuka-ID nieuwe registratie.   Als u Otsuka-ID hebt geregistreerd, gaat u verder met de koppelingsinstelling.

3. Ga door naar het einde en wanneer het bovenste scherm wordt weergegeven nadat u zich hebt ingevonden bij Mijn pagina van de klant, zijn de SSO-instellingen voltooid.

4. De volgende keer dat u verbinding maakt met Mijn pagina van de klant vanuit de SSO-app, wordt het bovenste scherm weergegeven nadat u zich hebt ingevonden bij Mijn pagina van de klant.

### <a name="create-otsuka-shokai-test-user"></a>Otsuka Shokai-testgebruiker maken

Nieuwe registratie van SaaS account zal worden uitgevoerd bij de eerste toegang tot Otsuka Shokai. Daarnaast zullen we ook Azure AD-account en SaaS-account koppelen op het moment van nieuwe creatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Otsuka Shokai-tegel in het Access-paneel klikt, moet u automatisch worden aangemeld bij de Otsuka Shokai waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Otsuka Shokai met Azure AD](https://aad.portal.azure.com/)

