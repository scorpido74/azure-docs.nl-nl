---
title: 'Zelfstudie: Azure Active Directory-integratie met Rackspace SSO | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Rackspace SSO.
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870307"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Zelfstudie: Azure Active Directory-integratie met Rackspace SSO

In deze zelfstudie leert u hoe U Rackspace SSO integreren met Azure Active Directory (Azure AD).
De integratie van Rackspace SSO met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Rackspace SSO.
* U uw gebruikers automatisch laten aanmelden bij Rackspace SSO (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Rackspace SSO, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Rackspace SSO-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Rackspace SSO ondersteunt **door SP** geïnitieerde SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Rackspace SSO toevoegen vanuit de galerie

Als u de integratie van Rackspace SSO in Azure AD wilt configureren, moet u Rackspace SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Rackspace SSO uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Rackspace SSO**in het zoekvak , selecteer **Rackspace SSO** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Rackspace SSO in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Rackspace SSO op basis van een testgebruiker genaamd **Britta Simon**.
Wanneer u eenmalige aanmelding met Rackspace gebruikt, worden de Rackspace-gebruikers automatisch gemaakt wanneer ze zich voor het eerst aanmelden bij de Rackspace-portal. 

Als u Azure AD-single sign-on wilt configureren en testen met Rackspace SSO, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Rackspace SSO Single Sign-On](#configure-rackspace-sso-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Attribuuttoewijzing instellen in het Configuratiescherm rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - om Rackspace-rollen toe te wijzen aan Azure AD-gebruikers.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Rackspace SSO te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Rackspace SSO-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Upload in de sectie **BasisSAML-configuratie** het **metagegevensbestand van** de serviceprovider dat u downloaden van de [URL](https://login.rackspace.com/federate/sp.xml) en voer de volgende stappen uit:

    a. Klik op **Metagegevensbestand uploaden**.

    ![installatiekopie](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![installatiekopie](common/browse-upload-metadata.png)

    c. Zodra het metagegevensbestand is geüpload, worden de benodigde url's automatisch ingevuld.

    d. Typ een URL in het tekstvak **AANmeldings-URL:**`https://login.rackspace.com/federate/`

    ![Rackspace SSO-domein en URL's met eenmalige aanmeldingsgegevens](common/sp-signonurl.png)   

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

Dit bestand wordt geüpload naar Rackspace om de vereiste configuratie-instellingen van de Identity Federation in te vullen.

### <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace SSO-aanmelding configureren

Ga als het gaat om eenmalige aanmelding aan **de SSO-kant van Rackspace:**

1. Zie de documentatie bij [Een identiteitsprovider toevoegen aan het Configuratiescherm](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Het zal u door de stappen leiden om:
    1. Een nieuwe identiteitsprovider maken
    1. Geef een e-maildomein op dat gebruikers zullen gebruiken om uw bedrijf te identificeren wanneer ze zich aanmelden.
    1. Upload de **XML met federatiemetagegevens** die eerder zijn gedownload vanuit het Azure-configuratiescherm.

Hiermee worden de basisinstellingen voor SSO correct geconfigureerd die nodig zijn om Verbinding te maken met Azure en Rackspace.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Rackspace SSO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Rackspace SSO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Rackspace SSO**in de lijst met toepassingen .

    ![De Rackspace SSO-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Kenmerktoewijzing instellen in het bedieningspaneel Rackspace

Rackspace gebruikt een **beleid voor toewijzing van kenmerken** om Rackspace-rollen en -groepen toe te wijzen aan uw gebruikers met een malige aanmelding. Het **beleid voor toewijzing** van kenmerken vertaalt Azure AD SAML-claims naar de gebruikersconfiguratievelden die Rackspace nodig heeft. Meer documentatie is te vinden in de [basisdocumentatie voor het toewijzen van rackspace-kenmerken](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/). Enkele overwegingen:

* Als u verschillende niveaus van Rackspace-toegang wilt toewijzen met Azure AD-groepen, moet u de claim Groepen inschakelen in de instellingen voor eenmalig aanmelden van Azure **Rackspace SSO.** Het **kenmerktoewijzingsbeleid** wordt vervolgens gebruikt om deze groepen af te koppelen aan gewenste Rackspace-rollen en -groepen:

    ![De claiminstellingen Groepen](common/sso-groups-claim.png)

* Azure AD verzendt standaard de UID van Azure AD-groepen in de SAML-claim, versus de naam van de groep. Als u echter uw on-premises Active Directory synchroniseert met Azure AD, hebt u de optie om de werkelijke namen van de groepen te verzenden:

    ![De instellingen voor naamvan groepen claimen](common/sso-groups-claims-names.png)

In het volgende **voorbeeld wordt kenmerktoewijzingsbeleid** gedemonstreerd:
1. De naam van de Rackspace-gebruiker instellen op de `user.name` SAML-claim. Elke claim kan worden gebruikt, maar het is het meest gebruikelijk om dit in te stellen op een veld met het e-mailadres van de gebruiker.
1. De `admin` Rackspace-rollen `billing:admin` en op een gebruiker instellen door een Azure AD-groep te matchen op groepsnaam of Groep UID. Er wordt `"{0}"` een `roles` *vervanging* van in het veld gebruikt `remote` en wordt vervangen door de resultaten van de regelexpressies.
1. Met `"{D}"` behulp van de *standaardsubstitutie* moet Rackspace extra SAML-velden ophalen door te zoeken naar standaard en bekende SAML-claims in de SAML-uitwisseling.

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
> Zorg ervoor dat u een teksteditor gebruikt die de syntaxis van YAML valideert bij het bewerken van uw beleidsbestand.

Zie de [basisdocumentatie voor basisbeginselen voor het toewijzen van rackspace-kenmerken](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) voor meer voorbeelden.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de SSO-tegel Rackspace klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de Rackspace SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

U de knop **Valideren** ook gebruiken in de instellingen voor één malige aanmelding **van Rackspace SSO:**

   ![Knop SSO-valideren](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

