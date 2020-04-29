---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Google Cloud (G suite)-connector | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de Google Cloud (G suite)-connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3282dd88b62a6811031e95672638d67702215a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80048451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Google Cloud (G suite)-connector

In deze zelf studie leert u hoe u de Google Cloud (G suite)-connector integreert met Azure Active Directory (Azure AD). Wanneer u de Google Cloud (G suite)-connector integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Google Cloud (G suite)-connector.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij de Google Cloud (G suite)-connector met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

- Een Azure AD-abonnement
- Het abonnement van de Google Cloud (G suite)-connector voor eenmalige aanmelding (SSO) is ingeschakeld.
- Een Google Apps-abonnement of Google Cloud Platform-abonnement.

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken. Dit document is gemaakt met behulp van de nieuwe ervaring voor eenmalige aanmelding. Als u nog steeds gebruikmaakt van de oude versie, ziet de installatie er anders uit. U kunt de nieuwe ervaring inschakelen in de instellingen voor eenmalige aanmelding van de G Suite-toepassing. Ga naar **Azure AD, zakelijke toepassingen**, selecteer **Google Cloud (G suite) connector**, selecteer **eenmalige aanmelding** en klik vervolgens op **onze nieuwe ervaring proberen**.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

1. **V: ondersteunt deze integratie Google Cloud Platform-SSO-integratie met Azure AD?**

    A: Ja. Google Cloud Platform en Google Apps delen hetzelfde verificatieplatform. Voor de GCP-integratie moet u dus de eenmalige aanmelding met Google Apps configureren.

2. **V: zijn Chromebooks en andere Chrome-apparaten die compatibel zijn met de eenmalige aanmelding van Azure AD?**
  
    A: Ja, gebruikers kunnen zich aanmelden bij hun Chromebook-apparaten met hun Azure AD-referenties. Zie dit [ondersteunings artikel voor de Google Cloud (G suite)-connector](https://support.google.com/chrome/a/answer/6060880) voor informatie over waarom gebruikers twee keer om referenties wordt gevraagd.

3. **V: als ik eenmalige aanmelding inschakel, kunnen gebruikers hun Azure AD-referenties gebruiken om zich aan te melden bij een Google-product, zoals Google Classroom, GMail, Google Drive, YouTube, enzovoort?**

    A: Ja, afhankelijk van de [Google Cloud (G suite)-connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) die u voor uw organisatie wilt in-of uitschakelen.

4. **V: kan ik eenmalige aanmelding inschakelen voor slechts een subset van de gebruikers van de Google Cloud (G suite)-connector?**

    A: Nee, wanneer u eenmalige aanmelding inschakelt, moeten alle gebruikers van de Google Cloud (G suite)-connector zich aanmelden met hun Azure AD-referenties. Omdat de Google Cloud (G suite)-connector geen ondersteuning biedt voor meerdere id-providers, kan de ID-provider voor uw Google Cloud (G suite)-connector omgeving zowel Azure AD als Google zijn, maar niet beide tegelijk.

5. **V: als een gebruiker is aangemeld via Windows, worden ze automatisch geverifieerd bij de Google Cloud (G suite)-connector zonder dat om een wacht woord wordt gevraagd?**

    A: er zijn twee opties voor het inschakelen van dit scenario. Ten eerste kan een gebruiker zich via [Deelnemen aan Azure Active Directory](../device-management-introduction.md) aanmelden bij een Windows 10-apparaat. Gebruikers kunnen zich ook aanmelden bij Windows-apparaten die zijn toegevoegd aan een domein in on-premises Active Directory waarvoor eenmalige aanmelding bij Azure AD is ingeschakeld via een [AD FS-implementatie (Active Directory Federation Services)](../hybrid/plan-connect-user-signin.md). Voor beide opties moet u de stappen in de volgende zelf studie uitvoeren om eenmalige aanmelding in te scha kelen tussen de Azure AD-en de Google Cloud-connector (G suite).

6. **V: wat moet ik doen als ik het fout bericht ' ongeldige e-mail ' Ontvang?**

    A: voor deze installatie is het e-mail kenmerk vereist zodat de gebruikers zich kunnen aanmelden. Dit kenmerk kan niet handmatig worden ingesteld.

    Het e-mailkenmerk wordt automatisch ingevuld voor elke gebruiker met een geldige Exchange-licentie. Als e-mail niet is ingeschakeld voor een gebruiker, wordt dit foutbericht ontvangen omdat de toepassing dit kenmerk moet ophalen om toegang te kunnen verlenen.

    U kunt met een beheerdersaccount naar portal.office.com gaan, in het beheercentrum klikken op Facturering > Abonnementen, uw Office 365-abonnement selecteren en vervolgens klikken op Toewijzen aan gebruikers. Selecteer de gebruikers van wie u het abonnement wilt controleren, en klik in het rechterdeelvenster op Licenties bewerken.

    Nadat de O365-licentie is toegewezen, kan het enkele minuten duren voordat deze is toegepast. Hierna wordt het kenmerk user.mail automatisch ingevuld en is het probleem zeer waarschijnlijk opgelost.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Google Cloud (G suite)-connector ondersteunt door **SP** GEÏNITIEERDe SSO

* Google Cloud (G suite)-connector ondersteunt [ **geautomatiseerde** gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Wanneer u de connector van de Google Cloud (G suite) hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>De Google Cloud (G suite)-connector toevoegen vanuit de galerie

Als u de integratie van de Google Cloud (G suite)-connector wilt configureren in azure AD, moet u de Google Cloud (G suite)-connector toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Google Cloud (G suite) connector** in het zoekvak.
1. Selecteer **Google Cloud (G suite)-connector** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor de Google Cloud (G suite)-connector

Azure AD SSO met de Google Cloud (G suite)-connector configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Google Cloud (G suite)-connector.

Als u Azure AD SSO wilt configureren en testen met de Google Cloud (G suite)-connector, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. De **[SSO-connector van Google Cloud (G suite) configureren](#configure-google-cloud-g-suite-connector-sso)** voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een **[test gebruiker voor de Google Cloud (g Suite)-connector maken](#create-google-cloud-g-suite-connector-test-user)** : als u een equivalent van B. Simon wilt hebben in de Google Cloud (g Suite)-connector die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina met de integratie van de app **-connector van Google Cloud (G suite)** , zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u wilt configureren voor **Gmail**:

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon:  

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u wilt configureren voor **Google Cloud Platform**:

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon:  
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en id. De Google Cloud (G suite)-connector biedt geen Entiteits-ID/ID-waarde voor de configuratie van eenmalige aanmelding, dus als u de optie voor de **specifieke uitgever** van het domein uitschakelt, is de id-waarde `google.com`. Als u de optie **domein specifieke uitgever** inschakelt, is `google.com/a/<yourdomainname.com>`dit. Als u de optie **specifieke certificaat verlener** wilt in-of uitschakelen, gaat u naar de sectie **SSO-connector van Google Cloud (G suite) configureren** die verderop in de zelf studie wordt beschreven. Neem contact op met het [ondersteunings team van de Google Cloud (G suite)-connector](https://www.google.com/contact/)voor meer informatie.

1. Uw Google Cloud (G suite)-connector toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermopname ziet u een voorbeeld hiervan. De standaard waarde van de **unieke gebruikers-id** is **User. userPrincipalName** , maar de Google Cloud (G suite)-connector verwacht dat dit moet worden toegewezen aan het e-mail adres van de gebruiker. Hiervoor kunt u het kenmerk **user.mail** in de lijst gebruiken of de juiste kenmerkwaarde op basis van uw organisatieconfiguratie.

    ![installatiekopie](common/default-attributes.png)


1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de sectie **Google Cloud (G suite)-connector instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de Google Cloud (G suite)-connector.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Google Cloud (G suite) connector**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-google-cloud-g-suite-connector-sso"></a>SSO-connector voor Google Cloud (G suite) configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij de [beheer console van de Google Cloud (G suite)-connector](https://admin.google.com/) met uw beheerders account.

2. Klik op **Beveiliging**. Als u de koppeling niet ziet, is deze mogelijk verborgen in het menu **Meer besturingselementen** onder aan het scherm.

    ![Klik op Beveiliging.][10]

3. Klik op de pagina **Beveiliging** op **Eenmalige aanmelding (SSO) instellen.**

    ![Klik op Eenmalige aanmelding.][11]

4. Voer de volgende configuratiewijzigingen uit:

    ![Eenmalige aanmelding configureren][12]

    a. Selecteer **Installatie van eenmalige aanmelding met id-provider van derden**.

    b. Plak de waarde van de **aanmeldings-URL** die u hebt gekopieerd van Azure Portal in het URL-veld voor de **aanmeldings pagina** in de Google Cloud (G suite)-connector.

    c. Plak de waarde van de **Afmeldings-URL** die u uit Azure Portal hebt gekopieerd in het URL-veld voor de **afmeldings pagina** in de Google Cloud (G suite)-connector.

    d. In het veld **wacht woord-URL wijzigen** in Google Cloud (G suite) connector, plakt u de waarde van de **wachtwoord-URL** die u hebt gekopieerd uit Azure Portal.

    e. Upload het certificaat dat u van Azure Portal hebt gedownload in de Google Cloud (G suite)-connector voor het **verificatie certificaat**.

    f. Schakel het selectie vakje **een specifieke certificaat Verlener gebruiken** in/uit, zoals vermeld in de bovenstaande **basis configuratie van SAML** in azure AD.

    g. Klik op **wijzigingen opslaan**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Test gebruiker van Google Cloud (G suite)-connector maken

Het doel van deze sectie is het [maken van een gebruiker in de Google Cloud-connector (G suite) met de](https://support.google.com/a/answer/33310?hl=en) naam B. Simon. Nadat de gebruiker hand matig is gemaakt in de Google Cloud (G suite)-connector, kan de gebruiker zich nu aanmelden met hun aanmeldings referenties voor Office 365.

De Google Cloud (G suite)-connector biedt ook ondersteuning voor automatische gebruikers inrichting. Als u het automatisch inrichten van gebruikers wilt configureren, moet u eerst de [Google Cloud (G suite)-connector configureren voor automatische gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Zorg ervoor dat uw gebruiker al bestaat in de Google Cloud (G suite)-connector als inrichten in azure AD niet is ingeschakeld voordat u eenmalige aanmelding test.

> [!NOTE]
> Neem contact op met het [ondersteuningsteam van Google](https://www.google.com/contact/) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel van de Google Cloud (G suite)-connector in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Google Cloud (G suite)-connector waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Gebruikers inrichten configureren](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [De Google Cloud (G suite)-connector uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [De Google Cloud (G suite)-connector beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png