---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met G suite | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en G Suite.
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
ms.openlocfilehash: d5ef5816759074073c57ef0f616ddea4a159956f
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370347"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met G suite

In deze zelf studie leert u hoe u G suite integreert met Azure Active Directory (Azure AD). Wanneer u G suite integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot G suite.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij G suite met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

- Een Azure AD-abonnement
- G suite-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.
- Een Google Apps-abonnement of Google Cloud Platform-abonnement.

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de  productieomgeving te gebruiken. Dit document is gemaakt met behulp van de nieuwe ervaring voor eenmalige aanmelding. Als u nog steeds gebruikmaakt van de oude versie, ziet de installatie er anders uit. U kunt de nieuwe ervaring inschakelen in de instellingen voor eenmalige aanmelding van de G Suite-toepassing. Ga naar **Azure AD, bedrijfstoepassingen**, selecteer **G Suite**, selecteer **Eenmalige aanmelding** en klik vervolgens op **Nieuwe ervaring uitproberen**.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

1. **V: ondersteunt deze integratie Google Cloud Platform-SSO-integratie met Azure AD?**

    A: Ja. Google Cloud Platform en Google Apps delen hetzelfde verificatieplatform. Voor de GCP-integratie moet u dus de eenmalige aanmelding met Google Apps configureren.

2. **V: zijn Chromebooks en andere Chrome-apparaten die compatibel zijn met de eenmalige aanmelding van Azure AD?**
  
    A: Ja, gebruikers kunnen zich aanmelden bij hun Chromebook-apparaten met hun Azure AD-referenties. Bekijk dit [G Suite-ondersteuningsartikel](https://support.google.com/chrome/a/answer/6060880) voor informatie over waarom gebruikers mogelijk tweemaal om hun referenties wordt gevraagd.

3. **V: als ik eenmalige aanmelding inschakel, kunnen gebruikers hun Azure AD-referenties gebruiken om zich aan te melden bij een Google-product, zoals Google Classroom, GMail, Google Drive, YouTube, enzovoort?**

    A: Ja, afhankelijk van de [G suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) die u voor uw organisatie wilt in-of uitschakelen.

4. **V: kan ik eenmalige aanmelding inschakelen voor slechts een subset van mijn G Suite-gebruikers?**

    A: Nee, wanneer u eenmalige aanmelding inschakelt, moeten alle gebruikers van G suite zich verifiëren met hun Azure AD-referenties. Omdat G Suite geen ondersteuning biedt voor meerdere id-providers, kan de id-provider voor uw G Suite-omgeving Azure AD of Google zijn, maar niet beide tegelijkertijd.

5. **V: als een gebruiker is aangemeld via Windows, worden ze automatisch geverifieerd bij G suite zonder dat om een wacht woord wordt gevraagd?**

    A: er zijn twee opties voor het inschakelen van dit scenario. Ten eerste kan een gebruiker zich via [Deelnemen aan Azure Active Directory](../device-management-introduction.md) aanmelden bij een Windows 10-apparaat. Gebruikers kunnen zich ook aanmelden bij Windows-apparaten die zijn toegevoegd aan een domein in on-premises Active Directory waarvoor eenmalige aanmelding bij Azure AD is ingeschakeld via een [AD FS-implementatie (Active Directory Federation Services)](../hybrid/plan-connect-user-signin.md). Voor beide opties moet u de stappen in de volgende zelfstudie uitvoeren om eenmalige aanmelding tussen Azure AD en G Suite in te schakelen.

6. **V: wat moet ik doen als ik het fout bericht ' ongeldige e-mail ' Ontvang?**

    A: voor deze installatie is het e-mail kenmerk vereist zodat de gebruikers zich kunnen aanmelden. Dit kenmerk kan niet handmatig worden ingesteld.

    Het e-mailkenmerk wordt automatisch ingevuld voor elke gebruiker met een geldige Exchange-licentie. Als e-mail niet is ingeschakeld voor een gebruiker, wordt dit foutbericht ontvangen omdat de toepassing dit kenmerk moet ophalen om toegang te kunnen verlenen.

    U kunt met een beheerdersaccount naar portal.office.com gaan, in het beheercentrum klikken op Facturering > Abonnementen, uw Office 365-abonnement selecteren en vervolgens klikken op Toewijzen aan gebruikers. Selecteer de gebruikers van wie u het abonnement wilt controleren, en klik in het rechterdeelvenster op Licenties bewerken.

    Nadat de O365-licentie is toegewezen, kan het enkele minuten duren voordat deze is toegepast. Hierna wordt het kenmerk user.mail automatisch ingevuld en is het probleem zeer waarschijnlijk opgelost.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* G Suite biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

* G suite ondersteunt [ **geautomatiseerde** gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Nadat u G Suite hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-g-suite-from-the-gallery"></a>G Suite toevoegen uit de galerie

Als u de integratie van G Suite in Azure AD wilt configureren, moet u G Suite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie de** tekst **G suite** in het zoekvak.
1. Selecteer **G suite** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor G suite

Azure AD SSO met G Suite configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in G suite.

Als u Azure AD SSO wilt configureren en testen met G suite, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[G suite SSO configureren](#configure-g-suite-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[G Suite-test gebruiker maken](#create-g-suite-test-user)** : als u een equivalent van B. Simon in G Suite wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **G suite** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u wilt configureren voor **Gmail**:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: 

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u wilt configureren voor **Google Cloud Platform**:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: 
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en id. G suite biedt geen entiteit-ID/ID-waarde voor de configuratie van eenmalige aanmelding, dus als u de optie **domein specifieke uitgever** uitschakelt, wordt de id-waarde `google.com`. Als u de optie **domein specifieke uitgever** inschakelt, wordt deze `google.com/a/<yourdomainname.com>`. Als u de optie **specifieke certificaat verlener** wilt in-of uitschakelen, gaat u naar de sectie **G suite SSO configureren** , die verderop in de zelf studie wordt beschreven. Neem contact op met het [ondersteunings team van client Suite](https://www.google.com/contact/)voor meer informatie.

1. In de G Suite-toepassing worden de SAML-beweringen in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermopname ziet u een voorbeeld hiervan. De standaardwaarde van **Unieke gebruikers-id** is **user.userprincipalname**, maar in G Suite wordt verwacht dat aan deze waarde het e-mailadres van de gebruiker is toegewezen. Hiervoor kunt u het kenmerk **user.mail** in de lijst gebruiken of de juiste kenmerkwaarde op basis van uw organisatieconfiguratie.

    ![image](common/default-attributes.png)


1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **G Suite instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door het verlenen van toegang tot G suite.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **G suite**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-g-suite-sso"></a>G suite SSO configureren

1. Open een nieuw tabblad in uw browser en meld u met uw beheerdersaccount aan bij de [G Suite-beheerconsole](https://admin.google.com/).

2. Klik op **Security** (Beveiliging). Als u de koppeling niet ziet, is deze mogelijk verborgen in het menu **Meer besturingselementen** onder aan het scherm.

    ![Klik op Beveiliging.][10]

3. Klik op de pagina **Beveiliging** op **Eenmalige aanmelding (SSO) instellen.**

    ![Klik op Eenmalige aanmelding.][11]

4. Voer de volgende configuratiewijzigingen uit:

    ![Eenmalige aanmelding configureren][12]

    a. Selecteer **Installatie van eenmalige aanmelding met id-provider van derden**.

    b. Plak in het URL-veld voor de **aanmeldings pagina** in G suite de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Plak in het URL-veld voor de afmeldings **pagina** in G suite de waarde van de **afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    d. Plak in het veld **wacht woord-URL wijzigen** in G suite de waarde van de **wachtwoord-URL wijzigen** die u hebt gekopieerd uit Azure Portal.

    e. Upload in G Suite voor **Verificatiecertificaat** het certificaat dat u hebt gedownload in de Azure-portal.

    f. Schakel het selectie vakje **een specifieke certificaat Verlener gebruiken** in/uit, zoals vermeld in de bovenstaande **basis configuratie van SAML** in azure AD.

    g. Klik op **Wijzigingen opslaan**.

### <a name="create-g-suite-test-user"></a>G Suite-testgebruiker maken

Het doel van deze sectie is het [maken van een gebruiker in G suite met de](https://support.google.com/a/answer/33310?hl=en) naam B. Simon. Nadat de gebruiker hand matig is gemaakt in G suite, kan de gebruiker zich nu aanmelden met hun aanmeldings referenties voor Office 365.

G suite ondersteunt ook automatische gebruikers inrichting. Als u het automatisch inrichten van gebruikers wilt configureren, moet u eerst [G Suite configureren voor automatische gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Zorg ervoor dat uw gebruiker al bestaat in G Suite, als inrichten in Azure AD niet is ingeschakeld vóór het testen van eenmalige aanmelding.

> [!NOTE]
> Neem contact op met het [ondersteuningsteam van Google](https://www.google.com/contact/) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel G Suite in het toegangsvenster klikt, wordt u automatisch aangemeld bij het G Suite-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Inrichten van gebruikers configureren](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [G suite proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [G suite beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png