---
title: 'Zelfstudie: Azure Active Directory-integratie met Keeper Password Manager & Digital Vault | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Keeper Password Manager & Digital Vault configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: c6bd0c130e860a5700256a54c081cc046219b41a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546743"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Zelfstudie: Azure Active Directory-integratie met Keeper Password Manager & Digital Vault

In deze zelfstudie leert u hoe u Keeper Password Manager & Digital Vault kunt integreren met Azure Active Directory (Azure AD).
De integratie van Keeper Password Manager & Digital Vault met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Keeper Password Manager & Digital Vault.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Keeper Password Manager & Digital Vault (eenmalige aanmelding of SSO).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Keeper Password Manager & Digital Vault hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Keeper Password Manager & Digital Vault waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Keeper Password Manager & Digital Vault ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Keeper Password Manager & Digital Vault ondersteunt **just in time** inrichten van gebruikers

* Zodra u Keeper Password Manager & Digital Vault hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Keeper Password Manager & Digital Vault toevoegen vanuit de galerie

Om de integratie van Keeper Password Manager & Digital Vault te configureren in Azure AD, moet u Keeper Password Manager & Digital Vault vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In het gedeelte **Toevoegen uit de galerie** typt u **Keeper Password Manager & Digital Vault** in het zoekvak.
1. Selecteer **Keeper Password Manager & Digital Vault** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Azure AD SSO voor Keeper Password Manager & Digital Vault configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Keeper Password Manager & Digital Vault met behulp van een testgebruiker, **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Keeper Password Manager & Digital Vault.

Om eenmalige aanmelding van Azure AD met Keeper Password Manager & Digital Vault te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.

    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    * **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.

1. **[Eenmalige aanmelding voor Keeper Password Manager & Digital Vault configureren](#configure-keeper-password-manager--digital-vault-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor Keeper Password Manager & Digital Vault maken](#create-keeper-password-manager--digital-vault-test-user)**: als u een tegenhanger van Britta Simon in Keeper Password Manager & Digital Vault wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **Keeper Password Manager & Digital Vault**, ga naar het gedeelte **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:
    * Voor **eenmalige aanmelding in de cloud**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Voor **eenmalige aanmelding on-premises**: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:
    * Voor **eenmalige aanmelding in de cloud**: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Voor **eenmalige aanmelding on-premises**: `https://<KEEPER_FQDN>/sso-connect`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie:
    * Voor **eenmalige aanmelding in de cloud**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Voor **eenmalige aanmelding on-premises**: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteuningsteam van Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **Keeper Password Manager & Digital Vault instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u B.Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Keeper Password Manager & Digital Vault.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Keeper Password Manager & Digital Vault** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Eenmalige aanmelding configureren voor Keeper Password Manager & Digital Vault

Als u eenmalige aanmelding wilt configureren in **Keeper Password Manager & Digital Vault Configuration**, volgt u de richtlijnen in de [Setup Guide van Keeper SSO Connect](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Testgebruiker maken voor Keeper Password Manager & Digital Vault

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Keeper Password Manager & Digital Vault, moeten ze worden ingericht bij Keeper Password Manager & Digital Vault. De toepassing biedt ondersteuning voor just in time-gebruikersinrichting. Dit betekent dat gebruikers na verificatie automatisch worden aangemaakt in de toepassing. Neem contact op met [Keeper Support](https://keepersecurity.com/contact.html) als u de gebruikers handmatig wilt instellen.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Keeper Password Manager & Digital Vault klikt, wordt u automatisch aangemeld bij de instantie van Keeper Password Manager & Digital Vault waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Keeper Password Manager & Digital Vault met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
