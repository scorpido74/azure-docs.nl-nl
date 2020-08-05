---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zscaler Private Access Administrator (ZPA) | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler Private Access (ZPA).
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
ms.openlocfilehash: 07607d6695d1010e5a704ed05608e9ea2587a64b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016153"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Zelfstudie: Zscaler Private Access (ZPA) integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Zscaler Private Access (ZPA) integreert met Azure Active Directory (Azure AD). Wanneer u Zscaler Private Access (ZPA) integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Zscaler Private Access (ZPA).
* Uw gebruikers zich automatisch laten aanmelden bij Zscaler Private Access (ZPA) met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Zscaler Private Access-abonnement (ZPA) waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Zscaler private Access (ZPA) ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler Private Access (ZPA) toevoegen vanuit de galerie

Als u de integratie van Zscaler Private Access (ZPA) met Azure AD wilt configureren, dient u Zscaler Private Access (ZPA) vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen vanuit de galerie** typt u **Zscaler Private Access (ZPA)** in het zoekvak.
1. Selecteer **Zscaler Private Access (ZPA)** in het deelvenster met resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met Zscaler Private Access (ZPA) met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler Private Access (ZPA).

Als u eenmalige aanmelding van Azure AD met Zscaler Private Access (ZPA) wilt configureren en testen, moet u de volgende bouwblokken voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Zscaler Private Access (ZPA) configureren](#configure-zscaler-private-access-zpa)** om de instellingen voor eenmalige aanmelding aan de zijde van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. **[Testgebruiker van Zscaler Private Access (ZPA) maken](#create-zscaler-private-access-zpa-test-user)** : als u een equivalent van Britta Simon in Zscaler Private Access (ZPA) wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Zscaler Private Access (ZPA)** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Typ een URL in het vak **Id (Entiteits-id)** : `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > De waarde voor **Aanmeldings-URL** is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [Ondersteuningsteam van Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden en vervolgens op te slaan op de computer.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Zscaler Private Access (ZPA) instellen** de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Zscaler Private Access (ZPA) configureren

1. Als u de configuratie in Zscaler Private Access (ZPA) wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbreiding aan de browser hebt toegevoegd, klikt u op **Zscaler Private Access (ZPA) instellen** zodat u wordt omgeleid naar naar de toepassing Zscaler Private Access (ZPA). Geef hier de Administrator-referenties op om u aan te melden bij Zscaler Private Access (ZPA). In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Zscaler Private Access (ZPA) handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Zscaler Private Access-bedrijfssite (ZPA). Voer daarna de volgende stappen uit:

4. Klik aan de linkerkant van het menu op **Beheer**, navigeer naar de sectie **VERIFICATIE** en klik op **IdP-configuratie**.

    ![Zscaler Private Access Administrator-beheer](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Klik in de rechterbovenhoek op **IdP-configuratie toevoegen**. 

    ![Zscaler Private Access Administrator-IdP](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Voer op de pagina **IdP-configuratie toevoegen** de volgende stappen uit:
 
    ![Zscaler Private Access Administrator selecteren](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klik op **Bestand selecteren** om het gedownloade metagegevensbestand uit Azure AD te uploaden in het veld **IdP-metagegevensbestand uploaden**.

    b. De **IdP-metagegevens** uit Azure AD worden gelezen en de overeenkomende velden worden ingevuld, zoals hieronder weergegeven.

    ![Zscaler Private Access Administrator configureren](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selecteer in het veld **Domeinen** uw domein.
    
    d. Klik op **Opslaan**.

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

In deze sectie hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Zscaler Private Access (ZPA).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Zscaler Private Access (ZPA)** .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Testgebruiker van Zscaler Private Access (ZPA) maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Zscaler Private Access (ZPA). Ga naar het ondersteuningsteam van [Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) om de gebruikers toe te voegen aan het ZPA-platform (Zscaler Private Access).

### <a name="test-sso"></a>Eenmalige aanmelding testen

Als u in het toegangsvenster op de tegel Zscaler Private Access (ZPA) klikt, wordt u automatisch aangemeld bij de Zscaler Private Access (ZPA) waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)