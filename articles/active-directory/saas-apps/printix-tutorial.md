---
title: 'Zelf studie: integratie Azure Active Directory met Printix | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Printix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: e9bc7969c5fee83944dab7cb35832f82b57c5595
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552340"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Zelf studie: integratie Azure Active Directory met Printix

In deze zelf studie leert u hoe u Printix integreert met Azure Active Directory (Azure AD).

Het integreren van Printix met Azure AD biedt de volgende voor delen:

- U kunt beheren in azure AD die toegang heeft tot Printix
- U kunt uw gebruikers in staat stellen om automatisch aangemeld te komen bij Printix (eenmalige aanmelding) met hun Azure AD-accounts
- U kunt uw accounts op één centrale locatie beheren: de Azure Portal

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md)als u meer wilt weten over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Printix wilt configureren, hebt u de volgende items nodig:

- Een Azure AD-abonnement
- Een abonnement met eenmalige aanmelding voor Printix

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proef omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een proef versie van één maand krijgen.

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelf studie test u eenmalige aanmelding voor Azure AD in een test omgeving. Het scenario dat in deze zelf studie wordt beschreven, bestaat uit twee belang rijke bouw stenen:

1. Printix toevoegen uit de galerie
1. Eenmalige aanmelding voor Azure AD configureren en testen

## <a name="adding-printix-from-the-gallery"></a>Printix toevoegen uit de galerie
Als u de integratie van Printix in azure AD wilt configureren, moet u Printix uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Printix toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram. 

    ![Active Directory][1]

1. Navigeer naar **bedrijfs toepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![Toepassingen][2]
    
1. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![Toepassingen][3]

1. Typ **Printix**in het zoekvak.

    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/tutorial_printix_search.png)

1. Selecteer in het deel venster resultaten **Printix**en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Eenmalige aanmelding voor Azure AD configureren en testen
In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Printix op basis van een test gebruiker met de naam ' Julia Simon '.

Voor gebruik van eenmalige aanmelding moet Azure AD weten wat de tegen gebruiker in Printix is voor een gebruiker in azure AD. Met andere woorden, een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Printix moet tot stand worden gebracht.

In Printix wijst u de waarde van de **gebruikers naam** in azure AD toe als de waarde van de **naam** van de gebruiker om de koppelings relatie tot stand te brengen.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Printix, moet u de volgende bouw stenen volt ooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configuring-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Een Azure AD-test gebruiker maken](#creating-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met Julia Simon.
1. Het **[maken van een Printix-test gebruiker](#creating-a-printix-test-user)** : als u een equivalent van Julia Simon in Printix wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[De Azure AD-test gebruiker toewijzen](#assigning-the-azure-ad-test-user)** : om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
1. **[Eenmalige aanmelding testen](#testing-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Eenmalige aanmelding voor Azure AD configureren

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal en configureert u eenmalige aanmelding in uw Printix-toepassing.

**Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Printix:**

1. Klik in het Azure Portal op de pagina **Printix** toepassings integratie op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren][4]

1. Selecteer in het dialoog venster **eenmalige aanmelding** de optie **modus** als op **SAML gebaseerde aanmelding** om eenmalige aanmelding in te scha kelen.
 
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Voer de volgende stappen uit in de sectie **Printix domein en url's** :

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_url.png)

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van Printix](mailto:support@printix.net) om de waarde op te halen. 
 
1. Klik in de sectie **SAML-handtekening certificaat** op **meta gegevens-XML** en sla het bestand met meta gegevens op uw computer op.

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Klik op de knop **Save**.

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_general_400.png)

1. Meld u aan bij uw Printix-Tenant als beheerder.

1. Klik in het menu aan de bovenkant op het pictogram in de rechter bovenhoek en selecteer '**verificatie**'.
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_06.png)

1. Selecteer op het tabblad **installatie** de optie **Azure/Office 365-verificatie inschakelen**
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_07.png)

1. Op het tabblad **Azure** voert u de URL voor de federatieve meta gegevens in op het tekstvak van het**federatieve meta gegevens document**. 

    Koppel het XML-bestand met meta gegevens dat u hebt gedownload van Azure AD naar het [Printix-ondersteunings team](mailto:support@printix.net). Vervolgens uploaden ze het XML-bestand en geven ze een URL voor federatieve meta gegevens.
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Klik op de knop**testen**en klik op de knop**OK**als de test is geslaagd.
   
     De pagina Azure Active Directory wordt weer gegeven nadat u op de knop **testen** hebt geklikt. ' De test is voltooid ' houdt in dat na het invoeren van de referenties van uw Azure-test account een bericht wordt weer gegeven met de tekst ' instellingen getest OK '. Klik vervolgens op de knop **OK** .
   
    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_09.png)

1. Klik op de knop **Opslaan** op de pagina**verificatie**.


> [!TIP]
> U kunt nu een beknopte versie van deze instructies in [Azure Portal](https://portal.azure.com) lezen terwijl u de app instelt!  Klik nadat u deze app onder **Active Directory > Bedrijfstoepassingen** hebt toegevoegd op het tabblad **Eenmalige aanmelding** en open de ingesloten documentatie via het gedeelte **Configuratie** onderaan. U vindt hier meer informatie over de Inge sloten documentatie functie: [documentatie voor Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

![Azure AD-gebruiker maken][100]

**Als u een test gebruiker in azure AD wilt maken, voert u de volgende stappen uit:**

1. Klik in het **Azure Portal**, in het navigatie deel venster aan de linkerkant op **Azure Active Directory** pictogram.

    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_01.png) 

1. Als u de lijst met gebruikers wilt weer geven, gaat u naar **gebruikers en groepen** en klikt u op **alle gebruikers**.
    
    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_02.png) 

1. Klik boven in het dialoog venster op **toevoegen** om het dialoog venster **gebruiker** te openen.
 
    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_03.png) 

1. Voer de volgende stappen uit op de pagina **gebruikers** dialoogvenster:
 
    ![Een Azure AD-testgebruiker maken](./media/printix-tutorial/create_aaduser_04.png) 

    a. Typ **BrittaSimon**in het tekstvak **naam** .

    b. Typ in het tekstvak **gebruikers naam** het **e-mail adres** van BrittaSimon.

    c. Selecteer **wacht woord weer geven** en noteer de waarde van het **wacht woord**.

    d. Klik op **Create**.
 
### <a name="creating-a-printix-test-user"></a>Een Printix-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in Printix. Printix biedt ondersteuning voor Just-in-time-inrichting, die standaard is ingeschakeld.

Er is geen actie-item voor u in deze sectie. Er wordt een nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen tot Printix als deze nog niet bestaat. 

> [!NOTE]
> Als u hand matig een gebruiker moet maken, moet u contact opnemen met het [ondersteunings team van Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>De Azure AD-test gebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Printix.

![Gebruiker toewijzen][200] 

**Voer de volgende stappen uit om Julia Simon toe te wijzen aan Printix:**

1. Open in de Azure Portal de weer gave toepassingen en navigeer vervolgens naar de mapweergave en ga naar **bedrijfs toepassingen** en klik vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

1. Selecteer in de lijst toepassingen de optie **Printix**.

    ![Eenmalige aanmelding configureren](./media/printix-tutorial/tutorial_printix_app.png) 

1. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

1. Klik op de knop **Add**. Selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![Gebruiker toewijzen][203]

1. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst gebruikers.

1. Klik op de knop **selecteren** in het dialoog venster **gebruikers en groepen** .

1. Klik op de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Printix in het toegangs venster klikt, wordt u automatisch aangemeld bij uw Printix-toepassing.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)



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

