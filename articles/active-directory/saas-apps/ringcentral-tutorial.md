---
title: 'Zelfstudie: Azure Active Directory-integratie met RingCentral | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en RingCentral.
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
ms.openlocfilehash: 15dc4ee8a5c1021587bb5c37e634070a76b3f05d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058234"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Zelfstudie: RingCentral integreren met Azure Active Directory

In deze zelfstudie leert u hoe u RingCentral integreert met Azure Active Directory (Azure AD). Wanneer u RingCentral integreert met Azure AD, kunt u:

* Beheer in Azure AD wie toegang heeft tot RingCentral.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij RingCentral.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* RingCentral-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RingCentral ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-ringcentral-from-the-gallery"></a>RingCentral toevoegen uit de galerie

Voor het configureren van de integratie van RingCentral met Azure AD moet u RingCentral uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **RingCentral**.
1. Selecteer **RingCentral** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met RingCentral met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RingCentral.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met RingCentral te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor RingCentral configureren](#configure-ringcentral-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een RingCentral-testgebruiker maken](#create-ringcentral-test-user)** : als u een equivalent van B.Simon in RingCentral wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **RingCentral** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    1. Klik op **Metagegevensbestand uploaden**.
    1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.
    1. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**.

    > [!Note]
    > U krijgt het **metagegevensbestand van de service provider** op de pagina RingCentral SSO-configuratie die verderop in de zelfstudie wordt beschreven.

1. Als u geen **metagegevensbestand van service provider hebt**, voert u de waarden voor de volgende velden in:

    a. Typ een URL in het tekstvak **Id**:
  
    | Id |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Typ een URL in het tekstvak **Antwoord-URL**:

    | Antwoord-URL |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte hebt u Britta Simon in staat gesteld gebruik te maken van Azure-eenmalige aanmelding door haar toegang te geven tot RingCentral.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **RingCentral** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ringcentral-sso"></a>RingCentral met eenmalige aanmelding configureren

1. Als u de configuratie in RingCentral wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **RingCentral instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de RingCentral-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij RingCentral. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u RingCentral handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de RingCentral-bedrijfssite. Voer daarna de volgende stappen uit:

1. Klik bovenaan op **Hulpprogramma's**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Ga naar **eenmalige aanmelding**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Klik op de pagina **eenmalige aanmelding** onder **configuratie van de SSO** in **Stap 1** op **Bewerken** en voer de volgende stappen uit:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Voer op de pagina **Eenmalige aanmelding instellen** de volgende stappen uit:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klik op **Browse** om het bestand met metagegevens dat u hebt gedownload van de Azure-portal te uploaden.

    b. Na het uploaden van metagegevens worden de waarden automatisch ingevuld in de sectie **Algemene informatie over eenmalige aanmelding**.

    c. Selecteer in sectie **Kenmerktoewijzing** **E-mailkenmerk toewijzen** als `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klik op **Opslaan**.

    e. Vanaf **Stap 2** klikt u op **Downloaden** om de **metagegevens van de serviceprovider** te downloaden en deze te uploaden naar de sectie **Basic SAML Configuration** om de waarden **Identifier** en **Reply URL** automatisch in de Azure-portal in te vullen.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Ga op dezelfde pagina naar de sectie **SSO inschakelen** en voer de volgende stappen uit:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecteer **SSO-service inschakelen**.

    * Selecteer **Gebruikers toestaan zich aan te melden met een SSO-of RingCentral-referentie**.

    * Klik op **Opslaan**.

### <a name="create-ringcentral-test-user"></a>RingCentral-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in RingCentral. Werk samen met het [ondersteuningsteam van RingCentral](https://success.ringcentral.com/RCContactSupp) om de gebruikers toe te voegen in het RingCentral-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel RingCentral selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij het exemplaar van RingCentral toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer RingCentral met Azure AD](https://aad.portal.azure.com/)
