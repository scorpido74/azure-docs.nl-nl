---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Concur Travel and Expense | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Concur Travel and Expense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373419"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Concur Travel and Expense

In deze zelfstudie leert u hoe u Concur Travel and Expense integreert met Azure Active Directory (Azure AD). Wanneer u Concur Travel and Expense integreert met Azure AD, u het als nog niet doen:

* Beheer in Azure AD die toegang heeft tot Concur Travel and Expense.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Concur Travel and Expense met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Concur Travel and Expense abonnement.
* Een rol 'Bedrijfsbeheerder' onder uw Concur-gebruikersaccount. U testen of u de juiste toegang hebt door naar [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin)te gaan. Als u geen toegang hebt, neem dan contact op met de ondersteunings- of implementatieprojectmanager van Concur. 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO.

* Concur Travel and Expense ondersteunt **IDP** en **SP** geïnitieerd sso
* Concur Travel and Expense ondersteunt testen SSO in zowel productie- als implementatieomgeving 

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde voor elk van de drie regio's: de VS, EMEA en China. Er kan dus slechts één exemplaar voor elke regio in één tenant worden geconfigureerd. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Het toevoegen van Concur Travel and Expense vanuit de galerij

Als u de integratie van Concur Travel and Expense in Azure AD wilt configureren, moet u Concur Travel and Expense vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ In de sectie **Toevoegen in de galerie** het type Reis en **Onkosten in** het zoekvak.
1. Selecteer **Reis en onkosten overeenkomen** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Azure AD-aanmelding configureren en testen voor Reis en Kosten van Akkoord

Azure AD SSO configureren en testen met Concur Travel and Expense met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Concur Travel and Expense.

Als u Azure AD SSO wilt configureren en testen met Concur Travel and Expense, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Concur Travel and Expense SSO](#configure-concur-travel-and-expense-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak de gebruiker van de Concur Travel and Expense-test](#create-concur-travel-and-expense-test-user)** - om een tegenhanger van B.Simon te hebben in Concur Travel en Expense die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de integratiepagina van de **Klant- en onkostentoepassing een** smet te vinden in de sectie **Beheer** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

    > [!NOTE]
    > Id (Entity ID) en Reply URL (Assertion Consumer Service URL) zijn regiospecifiek. Selecteer op basis van het datacenter van uw Concur-entiteit. Als u het datacenter van uw Concur-entiteit niet kent, neemt u contact op met de ondersteuning van Concur. 

5. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **gebruikerskenmerk** om de instellingen te bewerken. De unieke gebruikers-id moet overeenkomen met de login_id van De login_id van Concur. Meestal moet u **user.userprincipalname** wijzigen in **user.mail**.

    ![Gebruikerskenmerk bewerken](common/edit-attribute.png)

6. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om de metagegevens te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Concur Travel and Expense.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer In de lijst met toepassingen de optie **Reis en Onkosten overeenkomen**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-concur-travel-and-expense-sso"></a>Concur Travel and Expense SSO configureren

1. Als u eenmalige aanmelding wilt configureren aan de kant **van Concur Travel en Onkosten,** moet u de gedownloade Federation Metadata **XML** uploaden naar [De SSO Selfservice Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin) en inloggen met een account met de rol 'Bedrijfsbeheerder'. 

1. Klik op**toevoegen**.
1. Voer een aangepaste naam in voor uw IdP, bijvoorbeeld 'Azure AD (US)". 
1. Klik **op XML-bestand uploaden** en **voeg federatiemetagegevens XML** toe die u eerder hebt gedownload.
1. Klik **op Metagegevens toevoegen** om de wijziging op te slaan.

    ![Concur SSO self-service tool screenshot](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Speler van De Reis en onkosten van Concur maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Concur Travel and Expense. Werk samen met het ondersteuningsteam van Concur om de gebruikers toe te voegen aan het Concur Travel and Expense-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

> [!NOTE]
> De inlog-id van B.Simon moet overeenkomen met de unieke id van B.Simon bij Azure AD. Als b.Simon's Azure AD-unieke identifer bijvoorbeeld is `B.Simon@contoso.com`. B.Simon's Concur login id `B.Simon@contoso.com` moet ook. 

## <a name="configure-concur-mobile-sso"></a>Concur Mobile SSO configureren
Om Concur mobile SSO in te schakelen, moet u De gebruiker van het Ondersteuningsteam van Concur **toegang geven tot URL.** Volg de onderstaande stappen om **URL van gebruikerstoegang** van Azure AD te krijgen:
1. Ga naar **Enterprise-toepassingen**
1. Klik **op Reis en Onkosten eens**
1. Klik **op Eigenschappen**
1. Url **van gebruikerstoegang** kopiëren en deze URL aan De Eens-ondersteuning geven

> [!NOTE]
> Self-Service-optie om SSO te configureren is niet beschikbaar, dus werk samen met het Ondersteuningsteam van Concur om mobiele SSO in te schakelen. 

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Reis en Onkosten akkoord in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Overeenkomst Reis en Onkosten waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Reis en kosten met Azure AD](https://aad.portal.azure.com/)

