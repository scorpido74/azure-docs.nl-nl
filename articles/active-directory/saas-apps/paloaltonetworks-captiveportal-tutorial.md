---
title: 'Zelf studie: integratie Azure Active Directory met Palo Alto Networks-Portal | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks Captive Portal configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160161"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Zelf studie: integratie Azure Active Directory met Palo Alto Networks-Portal

In deze zelfstudie leert u hoe u Palo Alto Networks Captive Portal integreert met Azure Active Directory (Azure AD).

De integratie van Palo Alto Networks Captive Portal met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Palo Alto Networks Captive Portal.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Palo Alto Networks Captive Portal (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts beheren op één centrale locatie, de Azure-portal.

Zie voor meer informatie over de integratie van SaaS-apps (software als een service) met Azure AD [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD te integreren met Palo Alto Networks Captive Portal:

* Een Azure Active Directory-abonnement. Als u geen Azure AD hebt, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).
* Een abonnement op Palo Alto Networks Captive Portal waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

Palo Alto Networks Captive Portal ondersteunt de volgende scenario's:

* **Door IDP geïnitieerde eenmalige aanmelding**
* **Just In Time inrichten van gebruikers**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Palo Alto Networks Captive Portal toevoegen vanuit de galerie

Voeg om te beginnen in de galerie Palo Alto Networks Captive Portal toe aan uw lijst met beheerde SaaS-apps:

1. Selecteer in de [Azure-portal](https://portal.azure.com) in het linkermenu **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![De optie Bedrijfstoepassingen in het menu](common/enterprise-applications.png)

3. Selecteer **Nieuwe toepassing**.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Palo Alto Networks Captive Portal** in. Selecteer in de lijst met zoekresultaten **Palo Alto Networks - Captive Portal** en selecteer **Toevoegen**.

     ![Palo Alto Networks - Captive Portal toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

U configureert en test eenmalige aanmelding van Azure AD met Palo Alto Networks Captive Portal op basis van een testgebruiker met de naam *Britta Simon*. Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Palo Alto Networks Captive Portal tot stand is gebracht. 

Om eenmalige aanmelding van Azure AD met Palo Alto Networks Captive Portal te configureren en testen, moet u de volgende taken uitvoeren:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** : Hiermee stelt u de gebruiker in staat om deze functie te gebruiken.
2. **[Palo Alto Networks-eenmalige aanmelding voor de portal configureren](#configure-palo-alto-networks-captive-portal-single-sign-on)** : Configureer de instellingen voor eenmalige aanmelding in de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : de eenmalige aanmelding van Azure AD testen met de gebruiker *Julia Simon*.
4. **[De Azure AD-test gebruiker toewijzen](#assign-the-azure-ad-test-user)** : Stel Julia Simon in op het gebruik van eenmalige aanmelding voor Azure AD.
5. **Een Palo Alto Networks-gebruiker**voor de gebruikers van de portal testen: Maak een vergelijk gebruiker *Julia Simon* in Palo Alto Networks portal die is gekoppeld aan de Azure AD-gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : Controleer of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

Schakel eerst in de Azure-portal Azure AD-eenmalige aanmelding in:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **Palo Alto Networks - Captive Portal** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer **SAML** in het deelvenster **Selecteer een methode voor eenmalige aanmelding**.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** het potloodpictogram **Bewerken**.

    ![Het potloodpictogram Bewerken](common/edit-urls.png)

4. Voer in het deelvenster **Standaard SAML-configuratie** de volgende stappen uit:

    ![Deelvenster Palo Alto Networks Captive Portal Standaard SAML-configuratie](common/idp-intiated.png)

   1. Voer bij **Id** een URL in met het patroon `https://<customer_firewall_host_name>/SAML20/SP`.

   2. Voer bij **Antwoord-URL** een URL in met het patroon `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Werk de plaatsaanduidingen in deze stap bij met de werkelijke warden voor de id en antwoord-URL's. Neem contact op met het [ondersteuningsteam van Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support) om de werkelijke waarden te verkrijgen.

5. Selecteer **Downloaden** in de sectie **SAML-handtekeningcertificaat** naast **XML-bestand met federatieve metagegevens**. Sla het gedownloade bestand op uw computer op.

    ![De koppeling voor het downloaden van het XML-bestand met federatieve metagegevens](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Eenmalige aanmelding configureren voor Palo Alto Networks Captive Portal

Stel vervolgens eenmalige aanmelding in voor Palo Alto Networks Captive Portal:

1. Meld u in een ander browservenster aan bij de Palo Alto Networks-website als beheerder.

2. Selecteer het tabblad **Device**.

    ![Het tabblad Device van de Palo Alto Networks-website](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecteer in het menu **SAML Identity Provider** en vervolgens **Import**.

    ![De knop Import](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Voer in het dialoogvenster **SAML Identity Provider Server Profile Import** de volgende stappen uit:

    ![Eenmalige aanmelding voor Palo Alto Networks configureren](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Voer bij **Profile Name** een profielnaam in, bijvoorbeeld **AzureAD-CaptivePortal**.
    
    2. Selecteer **Browse** bij **Identity Provider Metadata**. Selecteer het metadata.xml-bestand dat u hebt gedownload van de Azure-portal.
    
    3. Selecteer **OK**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Maak vervolgens een testgebruiker met de naam *Britta Simon* in de Azure-portal:

1. Selecteer in de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker**.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer **BrittaSimon** in bij **Naam**.
  
    2. Voer voor **gebruikers naam** **BrittaSimon\@\<your_company_domain\>** . Bijvoorbeeld **BrittaSimon\@contoso.com**.

    3. Voer bij **Wachtwoord** een wachtwoord in. Wij raden u aan het wachtwoord dat u invoert te noteren. U kunt het selectievakje **Wachtwoord weergeven** inschakelen om het wachtwoord weer te geven.

    4. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Verleen vervolgens Britta Simon toegang tot Palo Alto Networks Captive Portal zodat ze eenmalige aanmelding met Azure kan gebruiken:

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

2. Voer in de lijst met toepassingen **Palo Alto Networks - Captive Portal** in en selecteer de toepassing.

    ![De koppeling naar Palo Alto Networks - Captive Portal in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **Britta Simon** in de lijst **Gebruikers** in het deelvenster **Gebruikers en groepen**. Kies **Selecteren**.

6. Selecteer in het deelvenster **Rol selecteren** de relevante rol voor de gebruiker om een rolwaarde aan de SAML-assertie toe te voegen. Kies **Selecteren**.

7. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Een testgebruiker maken voor Palo Alto Networks Captive Portal

Maak vervolgens een gebruiker met de naam *Britta Simon* in Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal ondersteunt Just In Time-gebruikersinrichting, wat standaard is ingeschakeld. U hoeft geen taken uit te voeren in deze sectie. Als er nog geen gebruiker in Palo Alto Networks Captive Portal bestaat, wordt er na verificatie een nieuwe gemaakt.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met het [klantondersteuningsteam van Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

Palo Alto Networks Captive Portal wordt achter de firewall op een Windows-VM geïnstalleerd. Als u eenmalige aanmelding in Palo Alto Networks Captive Portal wilt testen, meldt u zich bij de Windows-VM aan met behulp van Remote Desktop Protocol (RDP). Open in de RDP-sessie een browser en ga naar een willekeurige website. De URL voor eenmalige aanmelding wordt geopend en u wordt gevraagd om u te verifiëren. Wanneer de verificatie is voltooid, kunt u websites bezoeken.

## <a name="additional-resources"></a>Aanvullende bronnen

Zie voor meer informatie de volgende artikelen:

- [Zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Voorwaardelijke toegang in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

