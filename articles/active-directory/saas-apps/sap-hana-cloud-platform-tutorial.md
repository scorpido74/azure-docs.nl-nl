---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform | Microsoft Documenten'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89ea2c45e16dfeb63801f70fa4480c0d865a890f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160086"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform

In deze zelfstudie leert u hoe u SAP Cloud Platform met Azure Active Directory (Azure AD) kunt integreren.
De integratie van SAP Cloud Platform met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot SAP Cloud Platform.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAP Cloud Platform (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van de Azure AD-integratie met SAP Cloud Platform hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op SAP Cloud Platform waarvoor eenmalige aanmelding is ingeschakeld

Na het voltooien van deze zelfstudie kunnen de Azure AD-gebruikers die u hebt toegewezen aan SAP Cloud Platform zich aanmelden bij de toepassing met behulp van [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster).

>[!IMPORTANT]
>U moet uw eigen toepassing implementeren of zich abonneren op een toepassing van uw SAP Cloud Platform-account voor het testen van de eenmalige aanmelding. In deze zelfstudie wordt een toepassing geïmplementeerd in het account.
> 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Cloud Platform ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP Cloud Platform toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP Cloud Platform met Azure AD moet u SAP Cloud Platform uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u SAP Cloud Platform uit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SAP Cloud Platform**, selecteer **SAP Cloud Platform** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![SAP Cloud Platform in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met SAP Cloud Platform op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAP Cloud Platform tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij SAP Cloud Platform moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SAP Cloud Platform-eenmalige aanmelding configureren](#configure-sap-cloud-platform-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor SAP Cloud Platform maken](#create-sap-cloud-platform-test-user)**: als u een equivalent van Britta Simon in SAP Cloud Platform wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met SAP Cloud Platform moet u de volgende stappen uitvoeren:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de pagina voor integratie van de **SAP Cloud Platform**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het SAP Cloud Platform-domein en URL's](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **Aanmeldings-URL** de URL die wordt gebruikt door uw gebruikers om zich aan te melden bij uw **SAP Cloud Platform**-toepassing. Dit is de accountspecifieke URL van een beveiligde resource in uw SAP Cloud Platform-toepassing. De URL is gebaseerd op de volgende notatie: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Dit is de URL in uw SAP Cloud Platform-toepassing die vereist is voor verificatie van de gebruiker.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. In het tekstvak **Id** voert u in uw SAP Cloud Platform een URL in met een van de volgende notaties: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [SAP Cloud Platform-clientondersteuningsteam](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) om de aanmeldings-URL en id te verkrijgen. De antwoord-URL kunt u ophalen in de sectie vertrouwensbeheer die verderop in de zelfstudie wordt uitgelegd.
    > 
4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>SAP Cloud Platform-eenmalige aanmelding configureren

1. Meld u in een ander browservenster aan bij SAP Cloud Platform Cockpit op `https://account.<landscape host>.ondemand.com/cockpit`(bijvoorbeeld: https://account.hanatrial.ondemand.com/cockpit).

2. Klik op het tabblad **Vertrouwen**.
   
    ![Vertrouwen](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Vertrouwen")

3. Voer in de sectie Vertrouwensbeheer, onder **Lokale serviceprovider**, de volgende stappen uit:

    ![Vertrouwensbeheer](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Vertrouwensbeheer")
   
    a. Klik op **Bewerken**.

    b. Selecteer als **configuratietype** de optie **Aangepast**.

    c. Laat bij **Lokale providernaam** de standaardwaarde staan. Kopieer de waarde en plak deze in het **id**-veld in de Azure AD-configuratie voor SAP Cloud Platform.

    d. Klik voor het genereren van een sleutelpaar voor een **ondertekeningssleutel** en **ondertekeningscertificaat** op **Sleutelpaar genereren**.

    e. Selecteer bij **Principal-doorgifte** de optie **Uitgeschakeld**.

    f. Selecteer bij **Geforceerde verificatie** de optie **Uitgeschakeld**.

    g. Klik op **Opslaan**.

4. Nadat de **Lokale serviceprovider**-instellingen zijn opgeslagen, kunt u de antwoord-URL op de volgende manier verkrijgen:
   
    ![Metagegevens opmaken](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Metagegevens opmaken")

    a. Download het metagegevensbestand van SAP Cloud Platform door te klikken op **Metagegevens ophalen**.

    b. Open het gedownloade SAP Cloud Platform metagegevens-XML-bestand en zoek vervolgens de tag **ns3:AssertionConsumerService**.
 
    c. Kopieer de waarde van het kenmerk **Locatie** en plak deze in het veld **Antwoord-URL** in de Azure AD-configuratie voor SAP Cloud Platform.

5. Klik op het tabblad **Vertrouwde id-provider** en klik vervolgens op **Vertrouwde id-provider toevoegen**.
   
    ![Vertrouwensbeheer](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Vertrouwensbeheer")
   
    >[!NOTE]
    >Voor het beheren van de lijst met vertrouwde id-providers moet u het type Aangepaste configuratie hebben gekozen in de sectie Lokale serviceprovider. Voor het standaard configuratietype hebt u een niet-bewerkbare en impliciete vertrouwensrelatie met de SAP-id-service. Voor Geen hebt u geen vertrouwensrelatie-instellingen.
    > 
    > 

6. Klik op het tabblad **Algemeen** en vervolgens op **Bladeren** om het gedownloade metagegevensbestand te uploaden.
    
    ![Vertrouwensbeheer](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Vertrouwensbeheer")
    
    >[!NOTE]
    >Na het uploaden van het metagegevensbestand worden de waarden voor **Eenmalige aanmeldings-URL**, **Eenmalige afmeldings-URL** en **Ondertekeningscertificaat** automatisch ingevuld.
    > 
     
7. Klik op het tabblad **Kenmerken**.

8. Voer op het tabblad **Kenmerken** de volgende stap uit:
    
    ![Kenmerken](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Kenmerken") 

    a. Klik op **Op bevestiging gebaseerd kenmerk toevoegen** en voeg de volgende kenmerken toe op basis van een bevestiging:
       
    | Bevestigingskenmerk | Principal-kenmerk |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >De configuratie van de kenmerken is afhankelijk van hoe de toepassingen op SCP worden ontwikkeld. Dat wil zeggen, welke kenmerken in het SAML-antwoord worden verwacht en onder welke naam (Principal-kenmerk) toegang wordt verleend tot dit kenmerk in de code.
     > 
    
    b. Het **Standaardkenmerk** in de schermafbeelding dient slechts ter illustratie. Het is niet vereist om het scenario te laten werken.  
 
    c. De weergegeven namen en waarden voor **Principal-kenmerk** in de schermafbeelding zijn afhankelijk van hoe de toepassing is ontwikkeld. Het is mogelijk dat uw toepassing is vereist voor andere toewijzingen.

### <a name="assertion-based-groups"></a>Op bevestiging gebaseerde groepen

Als optionele stap kunt u groepen op basis van een bevestiging configureren voor uw identiteitsprovider Azure Active Directory.

Met behulp van groepen op SAP Cloud Platform kunt u een of meer gebruikers dynamisch toewijzen aan een of meer rollen in uw SAP Cloud Platform-toepassingen, die worden bepaald door de waarden van kenmerken in de SAML 2.0-verklaring. 

Als de bevestiging bijvoorbeeld het kenmerk *contract=tijdelijk* bevat, kunt u alle betrokken gebruikers toevoegen aan de groep *TIJDELIJK*. De groep *TIJDELIJK* kan een of meer rollen bevatten uit een of meer toepassingen die zijn geïmplementeerd in uw SAP Cloud Platform-account.
 
Gebruik op bevestiging gebaseerde groepen wanneer u veel gebruikers gelijktijdig wilt toewijzen aan een of meer rollen van toepassingen in uw SAP Cloud Platform-account. Als u slechts één of een klein aantal gebruikers aan specifieke rollen wilt toewijzen, dan kunt u deze het beste rechtstreeks in het tabblad **Autorisaties** van de SAP Cloud Platform-cockpit toewijzen.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie zorgt u ervoor dat Britta Simon van eenmalige aanmelding met Azure gebruik kan maken door haar toegang te verlenen tot SAP Cloud Platform.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **SAP Cloud Platform**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **SAP Cloud Platform** in de lijst met toepassingen.

    ![De SAP Cloud Platform-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sap-cloud-platform-test-user"></a>Testgebruiker voor SAP Cloud Platform maken

Als u wilt dat Azure AD-gebruikers zich aanmelden bij SAP Cloud Platform moet u de rollen in SAP Cloud Platform aan hen toewijzen.

**Als u een rol wilt toewijzen aan een gebruiker, moet u de volgende stappen uitvoeren:**

1. Meld u aan bij uw **SAP Cloud Platform**-cockpit.

2. Voer het volgende uit:
   
    ![Autorisaties](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorisaties")
   
    a. Klik op **Autorisatie**.

    b. Klik op het tabblad **Gebruikers.**

    c. Typ in het tekstvak **Gebruiker** het e-mailadres van de gebruiker.

    d. Klik op **Toewijzen** om de gebruiker toe te wijzen aan een rol.

    e. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Cloud Platform klikt in het toegangsvenster, wordt u automatisch aangemeld bij de instantie van SAP Cloud Platform waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

