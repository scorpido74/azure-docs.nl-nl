---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met NetDocuments | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71e5e13485c4a10664d98363e8e99bfd3b4f4bcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72035702"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met NetDocuments

In deze zelfstudie leert u hoe u NetDocuments integreren met Azure Active Directory (Azure AD). Wanneer u NetDocuments integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot NetDocuments.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij NetDocuments met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement op enkelaanmelding (SSO) van NetDocuments.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* NetDocuments ondersteunt **door SP** geïnitieerde SSO

## <a name="adding-netdocuments-from-the-gallery"></a>NetDocumenten toevoegen vanuit de galerie

Als u de integratie van NetDocuments in Azure AD wilt configureren, moet u NetDocuments uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **NetDocuments** in het zoekvak in de sectie **Toevoegen in de sectie Toevoegen in de galerie.**
1. Selecteer **NetDocuments** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Azure AD-aanmelding voor NetDocuments configureren en testen

Azure AD SSO configureren en testen met NetDocuments met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in NetDocuments.

Als u Azure AD SSO wilt configureren en testen met NetDocuments, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer NetDocuments SSO](#configure-netdocuments-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[NetDocuments-testgebruiker maken](#create-netdocuments-test-user)** - om een tegenhanger van B.Simon in NetDocuments te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **NetDocuments-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL aanbording en antwoord-URL. Repository ID is een waarde die begint met **CA,** gevolgd door 8 tekencode die is gekoppeld aan uw NetDocuments Repository. Voor meer informatie u het [ondersteuningsdocument van NetDocuments Federated Identity](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) controleren. U ook contact opnemen met [het ondersteuningsteam van NetDocuments Client](https://support.netdocuments.com/hc/) om deze waarden te verkrijgen als u problemen hebt met het configureren van de bovenstaande informatie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. NetDocuments-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. NetDocuments-toepassing verwacht dat **naam-id** wordt toegewezen met **employeeid** of een andere claim die van toepassing is op uw organisatie als **naam-id,** dus u moet de kenmerktoewijzing bewerken door op pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **NetDocumenten instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot NetDocuments.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **NetDocuments**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-netdocuments-sso"></a>NetDocuments SSO configureren

1. Meld u in een ander browservenster aan op uw NetDocuments-bedrijfssite als beheerder.

2. Ga naar **Beheerder**.

3. Klik **op Gebruikers en groepen toevoegen en verwijderen**.
   
    ![Opslagplaats](./media/netdocuments-tutorial/ic795047.png "Opslagplaats")

4. Klik **op Geavanceerde verificatieopties configureren**.
    
    ![Geavanceerde verificatieopties configureren](./media/netdocuments-tutorial/ic795048.png "Geavanceerde verificatieopties configureren")

5. Voer in het dialoogvenster **Federatieve identiteit** de volgende stappen uit:
   
    ![Federatieve identiteit](./media/netdocuments-tutorial/ic795049.png "Federatieve identiteit")
   
    a. Selecteer **Active Directory Federation Services**als **federatief identiteitsservertype**.
   
    b. Klik **op Bestand kiezen**om het gedownloade metagegevensbestand te uploaden dat u hebt gedownload van de Azure-portal.
   
    c. Klik op **OK**.

### <a name="create-netdocuments-test-user"></a>NetDocuments-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij NetDocuments, moeten ze worden ingericht in NetDocuments.  
In het geval van NetDocuments is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan op uw **netdocumenten** bedrijfssite als beheerder.

2. Klik in het menu bovenaan op **Admin**.
   
    ![Beheerder](./media/netdocuments-tutorial/ic795051.png "Beheerder")

3. Klik **op Gebruikers en groepen toevoegen en verwijderen**.
   
    ![Opslagplaats](./media/netdocuments-tutorial/ic795047.png "Opslagplaats")

4. Typ in het tekstvak **E-mailadres** het e-mailadres van een geldig Azure Active Directory-account dat u wilt inrichten en klik op **Gebruiker toevoegen.**
   
    ![E-mailadres](./media/netdocuments-tutorial/ic795053.png "E-mailadres")
   
    >[!NOTE]
    >De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt. U alle andere netdocuments-hulpprogramma's voor het maken van gebruikersaccounts of API's van NetDocuments gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel NetDocumenten in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de NetDocuments waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer NetDocuments met Azure AD](https://aad.portal.azure.com/)

