---
title: 'Zelfstudie: Azure Active Directory-integratie met Projectplace | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093526"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Zelfstudie: Projectplace integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Projectplace integreren met Azure Active Directory (Azure AD). Wanneer u Projectplace integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Projectplace.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Projectplace met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.
* Gebruikers kunnen automatisch worden ingericht in Projectplace.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Projectplace single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Projectplace ondersteunt **SP en IDP** geïnitieerd SSO en ondersteunt **Just In Time** gebruikersinrichting.

## <a name="adding-projectplace-from-the-gallery"></a>Projectplace toevoegen vanuit de galerie

Als u de integratie van Projectplace in Azure AD wilt configureren, moet u Projectplace vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Projectplace** in het zoekvak in de sectie Toevoegen in de sectie Toevoegen **in de galerie.**
1. Selecteer **Projectplace** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Projectplace met behulp van een testgebruiker genaamd **B. Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Projectplace.

Als u Azure AD SSO met Projectplace wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Projectplace configureren](#configure-projectplace)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-eenmaligaanmelding met B. Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak de testgebruiker](#create-projectplace-test-user)** van Projectplace om een tegenhanger van B. Simon in Projectplace te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Projectplace-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u **de** toepassing in de **idp-modus** wilt configureren, is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://service.projectplace.com`

1. Klik op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op het **pictogram** kopiëren om de url van de **app-federatie metagegevens**te kopiëren, volgens uw vereiste en deze op te slaan in kladblok.

   ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

1. Kopieer in de sectie **Projectplace instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Projectplaats configureren

Als u eenmalige aanmelding wilt configureren aan de **projectplaatszijde,** moet u de gekopieerde url van **de appfederatie-metagegevens** van de Azure-portal naar het [ondersteuningsteam van Projectplace](https://success.planview.com/Projectplace/Support)verzenden. Dit team zorgt ervoor dat de SAML SSO-verbinding aan beide kanten goed is ingesteld.

>[!NOTE]
>De enkele aanmeldingsconfiguratie moet worden uitgevoerd door het [projectplace-ondersteuningsteam.](https://success.planview.com/Projectplace/Support) U ontvangt een melding zodra de configuratie is voltooid. 

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B. Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B. Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Projectplace.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Projectplace**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen **B. Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-projectplace-test-user"></a>Testgebruiker Projectplace maken

>[!NOTE]
>U deze stap overslaan als u de inrichting hebt ingeschakeld in Projectplace. U het [ondersteuningsteam van Projectplace](https://success.planview.com/Projectplace/Support) vragen om het mogelijk te maken, zodra gebruikers tijdens de eerste aanmelding in Projectplace worden gemaakt.

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Projectplace, moet u deze toevoegen aan Projectplace. Je moet ze handmatig toevoegen.

**Als u een gebruikersaccount wilt maken, neemt u de volgende stappen:**

1. Meld u aan bij uw **projectplace-bedrijfssite** als beheerder.

2. Ga naar **Personen**en selecteer **vervolgens Leden:**
   
    ![Ga naar Personen en selecteer vervolgens Leden](./media/projectplace-tutorial/ic790228.png "People")

3. Selecteer **Lid toevoegen:**
   
    ![Lid toevoegen selecteren](./media/projectplace-tutorial/ic790232.png "Leden toevoegen")

4. Neem **in** de sectie Lid toevoegen de volgende stappen.
   
    ![Sectie Lid toevoegen](./media/projectplace-tutorial/ic790233.png "Nieuwe leden")
   
    1. Voer in het vak **Nieuwe leden** het e-mailadres in van een geldig Azure AD-account dat u wilt toevoegen.
   
    1. Selecteer **Verzenden**.

   Een e-mail met een koppeling om het account te bevestigen voordat het actief wordt, wordt verzonden naar de houder van het Azure AD-account.

>[!NOTE]
>U ook elk ander hulpprogramma voor het maken van gebruikersaccounts of API's van Projectplace gebruiken om Azure AD-gebruikersaccounts toe te voegen.


### <a name="test-sso"></a>Test SSO

Wanneer u de tegel Projectplace selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Projectplace waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)