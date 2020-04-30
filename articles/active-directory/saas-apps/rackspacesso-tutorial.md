---
title: 'Zelf studie: integratie Azure Active Directory met racks Pace SSO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en racks Pace SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 98a160b361e316c87e61855825c2cc36fdbb06d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81870307"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Zelf studie: integratie Azure Active Directory met racks Pace SSO

In deze zelf studie leert u hoe u racks Pace SSO integreert met Azure Active Directory (Azure AD).
Het integreren van racks Pace SSO met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot racks Pace SSO.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij racks Pace SSO (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met racks Pace SSO wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding van racks Pace SSO

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Racks Pace SSO ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Racks Pace SSO toevoegen vanuit de galerie

Als u de integratie van racks Pace SSO wilt configureren in azure AD, moet u racks Pace SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om racks Pace SSO vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **racks Pace SSO**in het zoekvak, selecteer **racks Pace SSO** van result panel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Racks Pace SSO in de resultaten lijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met racks Pace SSO op basis van een test gebruiker met de naam **Julia Simon**.
Wanneer u eenmalige aanmelding met racks Pace gebruikt, worden de racks Pace-gebruikers automatisch gemaakt wanneer ze zich voor de eerste keer aanmelden bij de racks Pace-Portal. 

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met racks Pace SSO, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor racks Pace SSO configureren](#configure-rackspace-sso-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Stel kenmerk toewijzing in het onderdeel racks Pace van het configuratie scherm in](#set-up-attribute-mapping-in-the-rackspace-control-panel)** om racks Pace-rollen toe te wijzen aan Azure AD-gebruikers.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met racks Pace SSO te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **racks Pace SSO** -toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Upload het **META gegevensbestand van de service provider** [in het gedeelte](https://login.rackspace.com/federate/sp.xml) **basis configuratie van SAML** en voer de volgende stappen uit:

    a. Klik op **Metagegevensbestand uploaden**.

    ![installatiekopie](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![installatiekopie](common/browse-upload-metadata.png)

    c. Zodra het meta gegevensbestand is geüpload, worden de benodigde url's automatisch gevuld.

    d. Typ een URL in het tekstvak **URL voor aanmelding** :`https://login.rackspace.com/federate/`

    ![Informatie over eenmalige aanmelding voor racks Pace SSO-domein en Url's](common/sp-signonurl.png)   

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

Dit bestand wordt geüpload naar racks Pace om de vereiste identiteits Federatie configuratie-instellingen op te vullen.

### <a name="configure-rackspace-sso-single-sign-on"></a>Eenmalige aanmelding voor racks Pace SSO configureren

Eenmalige aanmelding configureren op **racks Pace SSO** -zijde:

1. Raadpleeg de documentatie op [een id-provider toevoegen aan het configuratie scherm](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Dit leidt u stapsgewijs door de volgende stappen:
    1. Een nieuwe ID-provider maken
    1. Geef een e-mail domein op dat door gebruikers wordt gebruikt om uw bedrijf te identificeren bij het aanmelden.
    1. Upload de **federatieve meta gegevens-XML** die u eerder hebt gedownload via het configuratie scherm van Azure.

Hiermee worden de basis instellingen voor SSO die nodig zijn voor Azure en racks Pace, correct geconfigureerd om verbinding te maken.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan racks Pace SSO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **racks Pace SSO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **racks Pace SSO**.

    ![De SSO-koppeling racks Pace in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Kenmerk toewijzing instellen in het configuratie scherm van racks Pace

Racks Pace maakt gebruik van een **kenmerk toewijzings beleid** om racks Pace-rollen en-groepen toe te wijzen aan uw gebruikers met eenmalige aanmelding. Het **kenmerk toewijzings beleid** zet Azure AD SAML-claims om in de gebruikers configuratie velden racks Pace vereist. Meer documentatie vindt u in de [basis documentatie voor kenmerk toewijzing](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)van racks Pace. Enkele overwegingen:

* Als u verschillende niveaus van racks Pace toegang wilt toewijzen met behulp van Azure AD-groepen, moet u de groeps claim inschakelen in de instellingen voor eenmalige aanmelding voor Azure **racks Pace SSO** . Het **kenmerk toewijzings beleid** wordt vervolgens gebruikt om die groepen te vergelijken met de gewenste racks Pace-rollen en-groepen:

    ![De claim instellingen voor groepen](common/sso-groups-claim.png)

* Standaard verzendt Azure AD de UID van Azure AD-groepen in de SAML-claim, vergeleken met de naam van de groep. Als u uw on-premises Active Directory echter synchroniseert met Azure AD, hebt u de mogelijkheid om de werkelijke namen van de groepen te verzenden:

    ![De claim naam instellingen voor groepen](common/sso-groups-claims-names.png)

In het volgende voor beeld wordt het **beleid voor kenmerk toewijzing** gedemonstreerd:
1. De naam van de racks Pace-gebruiker wordt `user.name` ingesteld op de SAML-claim. Elke claim kan worden gebruikt, maar dit is het meest gebruikelijk om dit in te stellen op een veld met het e-mail adres van de gebruiker.
1. Het instellen van de `admin` racks Pace `billing:admin` -rollen en op een gebruiker door te voldoen aan een Azure AD-groep, op groeps naam of groep-UID. Een *vervanging* van `"{0}"` in het `roles` veld wordt gebruikt en wordt vervangen door de resultaten van de `remote` regel expressies.
1. Met behulp van de `"{D}"` *standaard vervanging* kunt racks Pace extra SAML-velden ophalen door te zoeken naar standaard-en bekende SAML-claims in de SAML-uitwisseling.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Zorg ervoor dat u een tekst editor gebruikt die de syntaxis van YAML valideert bij het bewerken van uw beleids bestand.

Zie de [basis documentatie voor racks Pace kenmerk toewijzing](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) voor meer voor beelden.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel racks Pace SSO in het toegangs venster klikt, moet u automatisch worden aangemeld bij de racks Pace SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

U kunt ook de knop **valideren** gebruiken in de instellingen voor eenmalige aanmelding van **racks Pace SSO** :

   ![Knop SSO valideren](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

