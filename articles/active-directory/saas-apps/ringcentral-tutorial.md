---
title: 'Zelfstudie: Azure Active Directory-integratie met RingCentral | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: de7cf57d177902efdbb44524703481e8c65c75c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72991475"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Zelfstudie: RingCentral integreren met Azure Active Directory

In deze zelfstudie leert u hoe u RingCentral integreren met Azure Active Directory (Azure AD). Wanneer u RingCentral integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot RingCentral.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij RingCentral met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* RingCentral single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* RingCentral ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-ringcentral-from-the-gallery"></a>RingCentral toevoegen vanuit de galerie

Als u de integratie van RingCentral in Azure AD wilt configureren, moet u RingCentral vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **RingCentral** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **RingCentral** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met RingCentral met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in RingCentral.

Als u Azure AD SSO wilt configureren en testen met RingCentral, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[RingCentral SSO configureren](#configure-ringcentral-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[RingCentral-testgebruiker maken](#create-ringcentral-test-user)** - om een tegenhanger van B.Simon in RingCentral te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **RingCentral-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    1. Klik op **Metagegevensbestand uploaden**.
    1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.
    1. Nadat het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in de sectie **BasisSAML-configuratie.**

    > [!Note]
    > U krijgt het **metadatabestand van** de serviceprovider op de pagina RingCentral SSO-configuratie, dat later in de zelfstudie wordt uitgelegd.

1. Als u geen **metagegevensbestand van serviceprovider hebt,** voert u de waarden in voor de volgende velden:

    a. Typ een URL in het tekstvak **Identifier:**

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Typ een URL in het tekstvak **Antwoord-URL**:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd Britta Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot RingCentral.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **RingCentral**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-ringcentral-sso"></a>RingCentral SSO configureren

1. Als u de configuratie binnen RingCentral wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **RingCentral instellen** en stuurt u door naar de RingCentral-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij RingCentral. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u RingCentral handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich als beheerder aan bij uw RingCentral-bedrijfssite en voert u de volgende stappen uit:

1. Klik bovenaan op **Extra .**

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral1.png)

1. Navigeer naar **Eenmalige aanmelding**.

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral2.png)

1. Klik op de pagina **Eén aanmelding** onder **sectie SSO-configuratie** in **stap 1** op **Bewerken** en voer de volgende stappen uit:

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral3.png)

1. Voer op de pagina **Eén aanmelding instellen** de volgende stappen uit:

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klik **op Bladeren** om het metagegevensbestand te uploaden dat u hebt gedownload van de Azure-portal.

    b. Na het uploaden van metadata worden de waarden automatisch ingevuld in de sectie **Algemene Informatie van SSO.**

    c. Selecteer onder de sectie **Kenmerktoewijzing** toewijzen de optie **E-mailkenmerk toewijzen aan** als`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klik op **Opslaan**.

    e. Klik **in stap 2** op **Downloaden** om het **metagegevensbestand van** de serviceprovider te downloaden en te uploaden in de sectie **BasisSAML-configuratie** om de **URL-waarden-id** en **antwoord** automatisch in te vullen in Azure-portal.

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Navigeer op dezelfde pagina naar de sectie **SSO inschakelen** en voer de volgende stappen uit:

    ![installatiekopie](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecteer **SSO-service inschakelen**.

    * Selecteer **Gebruikers toestaan om in te loggen met SSO- of RingCentral-referenties**.

    * Klik op **Opslaan**.

### <a name="create-ringcentral-test-user"></a>RingCentral-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in RingCentral. Werk samen met [ringcentral client ondersteuningsteam](https://success.ringcentral.com/RCContactSupp) om de gebruikers toe te voegen in het RingCentral-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Test SSO

Wanneer u de Tegel RingCentral selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de RingCentral waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [RingCentral uitproberen met Azure AD](https://aad.portal.azure.com/)