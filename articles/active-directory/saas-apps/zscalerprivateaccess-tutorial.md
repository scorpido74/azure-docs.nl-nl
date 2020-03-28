---
title: 'Zelfstudie: Azure Active Directory-integratie met Zscaler Private Access (ZPA) | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler Private Access (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085734"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Zelfstudie: Zscaler Private Access (ZPA) integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Zscaler Private Access (ZPA) integreren met Azure Active Directory (Azure AD). Wanneer u Zscaler Private Access (ZPA) integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Zscaler Private Access (ZPA).
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Zscaler Private Access (ZPA) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Zscaler Private Access (ZPA) single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Zscaler Private Access (ZPA) ondersteunt **SP** geïnitieerde SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler Private Access (ZPA) toevoegen vanuit de galerie

Als u de integratie van Zscaler Private Access (ZPA) in Azure AD wilt configureren, moet u Zscaler Private Access (ZPA) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Zscaler Private Access (ZPA)** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **Zscaler Private Access (ZPA)** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Zscaler Private Access (ZPA) met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Zscaler Private Access (ZPA).

Als u Azure AD SSO wilt configureren en testen met Zscaler Private Access (ZPA), voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met Britta Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om Britta Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Zscaler Private Access (ZPA)-testgebruiker](#create-zscaler-private-access-zpa-test-user)** maken om een tegenhanger van Britta Simon in Zscaler Private Access (ZPA) te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de **zpa-integratiepagina (Zscaler Private Access)** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Typ een URL in het vak **Id (Entiteits-id)**: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > De **waarde van de URL-waarde aanbording** is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [Zscaler Private Access (ZPA) Client support team](https://help.zscaler.com/zpa-submit-ticket) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Zscaler Private Access (ZPA)** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Zscaler-privétoegang configureren (ZPA)

1. Als u de configuratie binnen Zscaler Private Access (ZPA) wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure Sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Zscaler Private Access (ZPA)** en verwijst u naar de ZPA-toepassing (Zscaler Private Access). Geef van daaruit de beheerdersreferenties op om u aan te melden bij Zscaler Private Access (ZPA). De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Zscaler Private Access (ZPA) handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich aan op uw Zscaler Private Access -bedrijfssite als beheerder en voert u de volgende stappen uit:

4. Klik aan de linkerkant van het menu op **Beheer** en navigeer naar **de** sectie VERIFICATIE op **IdP-configuratie**.

    ![Beheer van de beheerder van de beheerder van de privétoegang van Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Klik in de rechterbovenhoek op **IdP-configuratie toevoegen**. 

    ![Zscaler Private Access Administrator idp](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Voer op de pagina **IdP-configuratie toevoegen** de volgende stappen uit:
 
    ![Zscaler Private Access Administrator selecteren](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klik **op Bestand selecteren** om het gedownloade bestand met ametagegevens van Azure AD te uploaden in het veld **Metagegevensbestand uploaden van idp.**

    b. Het leest de **IdP metadata** van Azure AD en vult alle velden informatie zoals hieronder weergegeven.

    ![Zscaler Private Access Administrator config](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selecteer uw domein in het veld **Domeinen.**
    
    d. Klik op **Opslaan**.

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Zscaler Private Access (ZPA).

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Zscaler Private Access (ZPA)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-zscaler-private-access-zpa-test-user"></a>Zscaler-testgebruiker Voor privétoegang maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Zscaler Private Access (ZPA). Werk samen met [zscaler Private Access (ZPA) support team](https://help.zscaler.com/zpa-submit-ticket) om de gebruikers toe te voegen in het Zscaler Private Access (ZPA) platform.

### <a name="test-sso"></a>Test SSO

Wanneer u de ZPA-tegel (Zscaler Private Access) selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Zscaler Private Access (ZPA) waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)