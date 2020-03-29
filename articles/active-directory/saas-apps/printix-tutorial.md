---
title: 'Zelfstudie: Azure Active Directory-integratie met Printix | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62111446"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Zelfstudie: Azure Active Directory-integratie met Printix

In deze zelfstudie leert u hoe u Printix integreert met Azure Active Directory (Azure AD).

De integratie van Printix met Azure AD biedt u de volgende voordelen:

- U in Azure AD bepalen wie toegang heeft tot Printix
- U uw gebruikers in staat stellen om automatisch aangemeld te worden bij Printix (Single Sign-On) met hun Azure AD-accounts
- U uw accounts beheren op één centrale locatie: de Azure-portal

Als u meer informatie wilt over de integratie van De SaaS-app met Azure AD, raadpleegt u [wat toepassingstoegang en eenmalige aanmelding is met Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie van Azure met Printix wilt configureren, hebt u de volgende items nodig:

- Een Azure AD-abonnement
- Een abonnement met eenmalige aanmelding in Printix

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proefomgeving hebt, u [hier](https://azure.microsoft.com/pricing/free-trial/)een proefperiode van één maand krijgen.

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u Azure AD single sign-on in een testomgeving. Het scenario dat in deze zelfstudie wordt beschreven, bestaat uit twee belangrijke bouwstenen:

1. Printix toevoegen vanuit de galerie
1. Azure AD-aanmelding configureren en testen

## <a name="adding-printix-from-the-gallery"></a>Printix toevoegen vanuit de galerie
Als u de integratie van Printix in Azure AD wilt configureren, moet u Printix vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Printix vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram. 

    ![Active Directory][1]

1. Navigeer naar **Enterprise-toepassingen**. Ga dan naar **Alle toepassingen.**

    ![Toepassingen][2]
    
1. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![Toepassingen][3]

1. Typ **Printix**in het zoekvak .

    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/tutorial_printix_search.png)

1. Selecteer In het deelvenster Resultaten de optie **Printix**en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD-aanmelding configureren en testen
In deze sectie configureert en test u Azure AD single sign-on met Printix op basis van een testgebruiker genaamd "Britta Simon".

Voor eenmalige aanmelding aan het werk moet Azure AD weten wat de tegenhangergebruiker in Printix is voor een gebruiker in Azure AD. Met andere woorden, er moet een koppeling worden gemaakt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Printix.

Wijs in Printix de waarde van de **gebruikersnaam** in Azure AD toe als de waarde van de **gebruikersnaam** om de koppelingsrelatie vast te stellen.

Als u Azure AD-singlesign-aan met Printix wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD Single Sign-On configureren](#configuring-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Een Azure AD-testgebruiker maken](#creating-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding met Britta Simon te testen.
1. **[Een Printix-testgebruiker maken](#creating-a-printix-test-user)** - om een tegenhanger van Britta Simon in Printix te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[De Azure AD-testgebruiker](#assigning-the-azure-ad-test-user)** toewijzen - zodat Britta Simon azure AD-aanmelding kan gebruiken.
1. **[Eén aanmelding testen](#testing-single-sign-on)** - om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD-aanmelding configureren

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in en configureert u eenmalige aanmelding in uw Printix-toepassing.

**Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Printix te configureren:**

1. Klik in de **Azure-portal**op de pagina **Printix-toepassingsintegratie** op Eén aanmelding .

    ![Eenmalige aanmelding configureren][4]

1. Selecteer in het dialoogvenster **Eén aanmelding** **modus** als **op SAML gebaseerde aanmelding** om eenmalige aanmelding in te schakelen.
 
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Voer in de sectie **Printix Domain en URL's** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_url.png)

    Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [printix client support team](mailto:support@printix.net) om de waarde te krijgen. 
 
1. Klik in de sectie **SAML Signing Certificate** op **Metagegevens XML** en sla het metagegevensbestand op uw computer op.

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klik op de knop **Save**.

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_general_400.png)

1. Meld u aan bij uw Printix-tenant als beheerder.

1. Klik in het menu bovenaan op het pictogram rechtsboven en selecteer "**Verificatie**".
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_06.png)

1. Selecteer op het tabblad **Setup** de optie **Azure/Office 365-verificatie inschakelen**
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_07.png)

1. Op het tabblad **Azure** u de URL van de invoerfederatie metagegevens invoeren in het tekstvak van het**document metagegevens**van de Federatie. 

    Voeg het xml-bestand met metagegevens dat u hebt gedownload van Azure AD toe aan [het ondersteuningsteam van Printix.](mailto:support@printix.net) Vervolgens uploaden ze het xml-bestand en geven ze een URL met aalmetagegevens van de federatie.
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klik op de knop "**Testen**" en klik op "**OK**" als de test succesvol is.
   
     De active directory-pagina van Azure wordt weergegeven nadat u op de **testknop** hebt geklikt. "De test is geslaagd" betekent hier dat na het invoeren van de referenties van uw Azure-testaccount het een bericht "Instellingen getest OK" zal verschijnen. Klik vervolgens op de knop **OK.**
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_09.png)

1. Klik **op** de knop Opslaan op de pagina**Verificatie.**


> [!TIP]
> U kunt nu een beknopte versie van deze instructies in [Azure Portal](https://portal.azure.com) lezen terwijl u de app instelt!  Klik nadat u deze app onder **Active Directory > Bedrijfstoepassingen** hebt toegevoegd op het tabblad **Eenmalige aanmelding** en open de ingesloten documentatie via het gedeelte **Configuratie** onderaan. U hier meer lezen over de ingesloten documentatiefunctie: [Azure AD-ingesloten documentatie]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

![Azure AD-gebruiker maken][100]

**Voer de volgende stappen uit om een testgebruiker in Azure AD te maken:**

1. Klik in de **Azure-portal**in het linkernavigatiedeelvenster op **azure Active Directory-pictogram.**

    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_01.png) 

1. Als u de lijst met gebruikers wilt weergeven, gaat u naar **Gebruikers en groepen** en klikt u op Alle **gebruikers**.
    
    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_02.png) 

1. Als u het dialoogvenster **Gebruiker** wilt openen, **klikt** u boven aan het dialoogvenster toevoegen.
 
    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_03.png) 

1. Voer op de pagina **Gebruiker** de volgende stappen uit:
 
    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_04.png) 

    a. Typ **BrittaSimon**in het tekstvak **Naam** .

    b. Typ in het tekstvak **Gebruikersnaam** het **e-mailadres** van BrittaSimon.

    c. Selecteer **Wachtwoord weergeven** en noteer de waarde van het **wachtwoord**.

    d. Klik **op Maken**.
 
### <a name="creating-a-printix-test-user"></a>Een Afdrukix-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in Printix. Printix ondersteunt just-in-time provisioning, die standaard is ingeschakeld.

Er is geen actie-item voor u in deze sectie. Er wordt een nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen tot Printix als deze nog niet bestaat. 

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, moet u contact opnemen met het [ondersteuningsteam van Printix.](mailto:support@printix.net)
> 

### <a name="assigning-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Printix.

![Gebruiker toewijzen][200] 

**Als u Britta Simon aan Printix wilt toewijzen, voert u de volgende stappen uit:**

1. Open in de Azure-portal de weergave toepassingen en navigeer vervolgens naar de mapweergave en ga naar **Enterprise-toepassingen** en klik vervolgens op **Alle toepassingen**.

    ![Gebruiker toewijzen][201] 

1. Selecteer **Printix**in de lijst met toepassingen .

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_app.png) 

1. Klik in het menu aan de linkerkant op **Gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

1. Klik op de knop **Add**. Selecteer **vervolgens Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![Gebruiker toewijzen][203]

1. Selecteer **Britta Simon** in het dialoogvenster Gebruikers **en groepen** in de lijst Gebruikers.

1. Klik **op Knop Selecteren** in het dialoogvenster Gebruikers en **groepen.**

1. Klik **op De** knop Toewijzen in het dialoogvenster Toewijzing **toevoegen.**
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Printix in het toegangspaneel klikt, wordt u automatisch aangemeld bij uw Printix-toepassing.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

