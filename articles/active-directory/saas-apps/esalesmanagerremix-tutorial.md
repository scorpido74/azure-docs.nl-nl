---
title: 'Zelfstudie: Azure Active Directory-integratie met E Sales Manager Remix | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65406959"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Azure Active Directory integreren met E Sales Manager Remix

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD) integreert met E Sales Manager Remix.

Door Azure AD te integreren met E Sales Manager Remix, krijgt u de volgende voordelen:

- U in Azure AD bepalen wie toegang heeft tot E Sales Manager Remix.
- U uw gebruikers automatisch laten aanmelden bij E Sales Manager Remix (single sign-on of SSO) met hun Azure AD-accounts.
- U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](../manage-apps/what-is-single-sign-on.md)meer informatie over de integratie van de SaaS-app met Azure AD. .

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met E Sales Manager Remix, hebt u de volgende items nodig:

- Een Azure AD-abonnement
- Een E Sales Manager Remix SSO-abonnement

> [!NOTE]
> Wanneer u de stappen in deze zelfstudie test, raden we u aan *geen* productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u Azure AD single sign-on in een testomgeving. 

Het scenario dat in deze zelfstudie wordt beschreven, bestaat uit twee belangrijke bouwstenen:

* E Sales Manager Remix toevoegen vanuit de galerie
* Azure AD-aanmelding configureren en testen

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>E Sales Manager Remix toevoegen vanuit de galerie
Als u de integratie van Azure AD met E Sales Manager Remix wilt configureren, voegt u E Sales Manager Remix vanuit de galerie toe aan uw lijst met beheerde SaaS-apps:

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**. 

    ![De knop Azure Active Directory][1]

1. Selecteer **Enterprise-toepassingen** > **Alle toepassingen**.

    ![Het venster 'Enterprise-toepassingen'][2]
    
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster.

    ![De knop Nieuwe toepassing][3]

1. Typ E Sales **Manager Remix**in het zoekvak, selecteer **E Sales Manager Remix** in de resultatenlijst en selecteer **Vervolgens Toevoegen**.

    ![E Sales Manager Remix in de resultatenlijst](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met E Sales Manager Remix, op basis van een testgebruiker genaamd 'Britta Simon'.

Azure AD moet de E Sales Manager Remix-gebruiker en zijn tegenhanger in Azure AD identificeren om eenmalige aanmelding te laten werken. Met andere woorden, er moet een koppeling slinken tussen een Azure AD-gebruiker en dezelfde gebruiker in E Sales Manager Remix.

Als u Azure AD-singlesign-aan wilt configureren en testen met E Sales Manager Remix, vult u de bouwstenen in de volgende vijf secties in:

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

Schakel Azure AD single sign-on in de Azure-portal in en configureer eenmalige aanmelding in uw E Sales Manager Remix-toepassing door het volgende te doen:

1. Selecteer in de **Azure-portal**op de pagina **E Sales Manager Remix-toepassingsintegratie** de optie Eén aanmelding .

    ![De link 'Eenmalig aanmelden'][4]

1. Selecteer in het venster **Eén aanmelding** in het vak **Inderetekenmodus** de optie **OP SAML-gebaseerde aanmelding**.
 
    ![Het venster "Eenmalig aanmeldingsvenster"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Ga als volgt te werk onder **E Sales Manager Remix Domain en URL's:**

    ![E Sales Manager Remix Domein en URL's single sign-on informatie](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Typ in het vak **AANmeldingsURL** een URL in de volgende indeling: *\<https:// Server-URL>/\<subdomein>/esales-pc*.

    b. Typ in het vak **Id** een URL in de volgende indeling: *https://\<server-URL>/\<subdomein>/*.

    c. Let in deze zelfstudie op de **waarde-id** voor later gebruik.
    
    > [!NOTE] 
    > De bovenstaande waarden zijn niet echt. Werk ze bij met de werkelijke aanmeldings-URL en id. Neem contact op met [E Sales Manager Remix Client support team](mailto:esupport@softbrain.co.jp)om de waarden te verkrijgen.

1. Selecteer **onder SAML-ondertekeningscertificaat** **Certificaat (Base64)** en sla het certificaatbestand op uw computer op.

    ![De downloadkoppeling Certificate (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Schakel het selectievakje **Alle andere gebruikerskenmerken weergeven en bewerken** in en schakel het kenmerk **e-mailadres** in.
    
    ![Het venster Gebruikerskenmerken](./media/esalesmanagerremix-tutorial/configure1.png)

    Het venster **Kenmerk bewerken** wordt geopend.

1. Kopieer de **waarden Naamruimte** en **Naam.** Genereer de waarde in het patroon * \<Namespace>/\<Name>* en bewaar deze voor later gebruik in deze zelfstudie.

    ![Het venster Kenmerk bewerken](./media/esalesmanagerremix-tutorial/configure2.png)

1. Selecteer **Onder E Sales Manager Remix-configuratie**de optie E Sales Manager Remix **configureren**.

    ![E Sales Manager Remix-configuratie](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Het **aanmeldingsvenster configureren** wordt geopend.

1. Kopieer in de sectie **Snel nagaan** de aanmeldings-URL en de URL van de SAML-aanmeldingsservice.

1. Selecteer **Opslaan**.

    ![De knop Opslaan](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Meld u aan bij uw E Sales Manager Remix-toepassing als beheerder.

1. Selecteer rechtsboven **het menu Administrator .**

    ![De opdracht 'Naar administratormenu',](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecteer in het linkerdeelvenster de optie **Systeeminstellingen** > **Samenwerking met het externe systeem**.

    ![De koppelingen "Systeeminstellingen" en "Samenwerking met extern systeem"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Selecteer **SAML**in **het venster Samenwerking met extern systeem** .

    ![Het venster "Samenwerking met extern systeem"](./media/esalesmanagerremix-tutorial/configure6.png)

1. Ga als volgt te werk onder **de saml-verificatieinstelling:**

    ![De sectie SAML-verificatie](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Schakel het selectievakje **PC-versie** in.
    
    b. Selecteer in de sectie **Samenwerkingsitem** in de vervolgkeuzelijst **e-mail**.

    c. Plak in het vak **Samenwerkingsitem** de claimwaarde die u eerder hebt **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**gekopieerd uit de Azure-portal (dat wil zeggen ).

    d. Plak in het vak **Issuer (entity ID)** de id-waarde die u eerder hebt gekopieerd uit het gedeelte **E Sales Manager Remix-domein en URL's** van de Azure-portal.

    e. Als u uw gedownloade certificaat wilt uploaden vanuit de Azure-portal, selecteert u **Bestandsselectie**.

    f. Plak in het **inlog-URL-vak van de ID-provider** de URL van de SAML-aanmeldingsservice die u eerder in de Azure-portal hebt gekopieerd.

    g. Plak in **het vak URL-instantie Van** de identiteitsprovider de afmeldings-URL-waarde die u eerder in de Azure-portal hebt gekopieerd.

    h. Selecteer **Instelling voltooid**.

> [!TIP]
> Terwijl u de app instelt, u een beknopte versie van de voorgaande instructies lezen in de [Azure-portal.](https://portal.azure.com) Nadat u de app hebt toegevoegd in de sectie **Active Directory** > **Enterprise Applications,** selecteert u het tabblad **Eén aanmelding** en opent u de ingesloten documentatie in de sectie **Configuratie** onderaan. Zie [Azure AD-ingesloten documentatie]( https://go.microsoft.com/fwlink/?linkid=845985)voor meer informatie over de ingesloten documentatie.
> 

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u testgebruiker Britta Simon in de Azure-portal als volgt:

![Een Azure AD-testgebruiker maken][100]

1. Selecteer **Azure Active Directory**in de Azure-portal in het linkerdeelvenster .

    ![De Azure Active Directory-koppeling](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Als u een lijst met huidige gebruikers wilt weergeven, selecteert u **Gebruikers en groepen** > **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Selecteer Boven aan het venster **Alle gebruikers** de optie **Toevoegen**.

    ![De knop Toevoegen](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Het venster **Gebruiker** wordt geopend.

1. Ga als volgt te werk in het venster **Gebruiker:**

    ![Het venster Gebruiker](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Typ **BrittaSimon**in het vak **Naam** .

    b. Typ in het vak **Gebruikersnaam** het e-mailadres van gebruiker Britta Simon.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    d. Selecteer **Maken**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Een E Sales Manager Remix-testgebruiker maken

1. Meld u aan bij uw E Sales Manager Remix-toepassing als beheerder.

1. Selecteer **Het menu Beheerder in** het menu rechtsboven.

    ![E Sales Manager Remix-configuratie](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selecteer **de instellingen** > van uw bedrijf**Onderhoud van afdelingen en werknemers**en selecteer vervolgens Geregistreerde **werknemers.**

    ![Het tabblad 'Werknemers geregistreerd'](./media/esalesmanagerremix-tutorial/user1.png)

1. Ga in de sectie **Nieuwe werknemersregistratie** als volgt te werk:
    
    ![De sectie "Nieuwe werknemersregistratie"](./media/esalesmanagerremix-tutorial/user2.png)

    a. Typ **in** het vak Naam van werknemer de naam van de gebruiker (bijvoorbeeld **Britta**).

    b. Vul de resterende vereiste velden in.
    
    c. Als u SAML inschakelt, kan de beheerder zich niet aanmelden vanaf de aanmeldingspagina. Geef beheerdersaanmeldingsrechten toe aan de gebruiker door het selectievakje **Aanmelding voor beheerders in** te schakelen.

    d. Selecteer **Registratie**.

1. Als u zich in de toekomst wilt aanmelden als beheerder, meldt u zich aan als de gebruiker die beheerdersmachtigingen heeft en selecteert u vervolgens rechtsboven **het beheerdersmenu**.

    ![De opdracht 'Naar administratormenu',](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u gebruiker Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot E Sales Manager Remix. Ga als volgt te werk: 

![De gebruikersrol toewijzen][200] 

1. Open in de Azure-portal de weergave **Toepassingen,** ga naar de **directoryweergave** en selecteer Vervolgens Alle**toepassingen** **voor Bedrijven** > selecteren.

    ![De koppelingen "Enterprise-toepassingen" en "Alle toepassingen"][201] 

1. Selecteer E Sales **Manager Remix**in de lijst **Toepassingen** .

    ![De e Sales Manager Remix link](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. Selecteer gebruikers en **groepen**in het linkerdeelvenster .

    ![De koppeling Gebruikers en groepen][202]

1. Selecteer **Toevoegen** en selecteer vervolgens in het deelvenster **Toewijzing toevoegen** de optie Gebruikers en **groepen**.

    ![Het deelvenster Toewijzing toevoegen][203]

1. Selecteer **Britta Simon**in het venster **Gebruikers en groepen** in de lijst **Gebruikers** .

1. Selecteer de knop **Selecteren.**

1. Selecteer **Toewijzing toewijzen** in het venster Toewijzing **toevoegen**.
    
### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel Remix e Sales Manager selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij uw E Sales Manager Remix-toepassing.

Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster. 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

