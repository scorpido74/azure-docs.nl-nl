---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Cisco WebEx | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01fe4d06d5f73eacee1d1cdaf1963232b84daf05
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046788"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Cisco WebEx

In deze zelf studie leert u hoe u Cisco WebEx integreert met Azure Active Directory (Azure AD). Wanneer u Cisco WebEx integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Cisco WebEx.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Cisco WebEx met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Cisco WebEx-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Cisco WebEx ondersteunt door **SP** geïnitieerde SSO.
* Cisco WebEx ondersteunt **geautomatiseerde** gebruikers inrichting.
* Zodra u Cisco WebEx hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex toevoegen vanuit de galerie

Voor het configureren van de integratie van Cisco Webex in Azure AD moet u Cisco Webex vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Cisco WebEx** in het zoekvak.
1. Selecteer **Cisco WebEx** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Eenmalige aanmelding van Azure AD voor Cisco WebEx configureren en testen

Azure AD SSO met Cisco WebEx configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Cisco WebEx.

Als u Azure AD SSO wilt configureren en testen met Cisco WebEx, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
2. **[Configureer Cisco WebEx](#configure-cisco-webex)** voor het configureren van de SSO-instellingen aan de kant van de toepassing.
    1. **[Cisco web test-gebruiker maken](#create-cisco-webex-test-user)** voor een equivalent van B. Simon in Cisco WebEx dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de **Cisco Web** Application Integration-pagina de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** uploadt u het bestand met metagegevens dat u hebt gedownload van de **serviceprovider** en configureert u de toepassing door de volgende stappen uit te voeren:

    >[!Note]
    >U krijgt het meta gegevensbestand van de service provider via de sectie **Cisco WebEx configureren** . dit wordt verderop in de zelf studie beschreven. 

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Als het uploaden van het metagegevensbestand van de serviceprovider is geslaagd, worden de waarden voor de **id** en **Antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    Plak in het tekstvak **URL voor aanmelden** de waarde van de **antwoord-URL**, die automatisch wordt ingevuld door het uploaden van het meta gegevensbestand van het bestand.

1. In de Cisco Webex-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. In aanvulling op hierboven verwacht de Cisco WebEx-toepassing nog maar weinig kenmerken om te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
  
    | Name |  Bronkenmerk|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Cisco WebEx instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan Cisco WebEx.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Typ en selecteer **Cisco Webex** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cisco-webex"></a>Cisco WebEx configureren

1. Als u de configuratie binnen Cisco WebEx wilt automatiseren, moet u de **uitbrei ding mijn apps beveiligde aanmeldings browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **Cisco WebEx instellen** , wordt u doorgestuurd naar de Cisco WebEx-toepassing. Geef de beheerders referenties op om u aan te melden bij Cisco WebEx. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-8 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Cisco WebEx hand matig wilt instellen, meldt u zich aan bij het [samenwerkings beheer van Cisco-Cloud](https://admin.ciscospark.com/) met uw volledige beheerders referenties.

4. Selecteer **Settings** en klik in de sectie **Authentication** op **Modify**.

    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Selecteer **een id-provider van een derde partij integreren. (Geavanceerd)** en gaat u naar het volgende scherm.

6. Sleep het bestand met de Azure AD-metagegevens naar de pagina **Import Idp Metadata** of gebruik de optie 'file browser' om het bestand te zoeken en te uploaden. Selecteer vervolgens **Require certificate signed by a certificate authority in Metadata (more secure)** en klik op **Next**.

    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Selecteer **Test SSO Connection**. Wanneer er een nieuw browsertabblad wordt geopend, moet u zich verifiëren bij Azure AD door u aan te melden.

8. Ga terug naar het tabblad **Cisco Cloud Collaboration Management** browser. Als de test is geslaagd, selecteert u **deze test is geslaagd. Schakel de optie eenmalige aanmelding in** en klik op **volgende**.

### <a name="create-cisco-webex-test-user"></a>Testgebruiker voor Cisco Webex maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Cisco WebEx. In deze sectie maakt u een gebruiker met de naam B. Simon in Cisco WebEx.

1. Meld u aan bij [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) met uw volledige beheerdersreferenties.

2. Klik op **Users** en vervolgens op **Manage Users**.
   
    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. Selecteer **Manually Add or Modify Users** in het venster **Manage User** en klik vervolgens op **Next**.

4. Selecteer **Names and Email address**. Vul vervolgens de tekstvakken als volgt in:

    ![Eenmalige aanmelding configureren](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Typ in het tekstvak **voor de voor naam** de voor naam van de gebruiker zoals **B**.

    b. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **e-mail adres** het e-mail adres van de gebruiker, zoals b.simon@contoso.com.

5. Klik op het plus teken om B. Simon toe te voegen. Klik op **Volgende**.

6. Klik in het venster **Add Services for Users** op **Save** en daarna op **Finish**.

## <a name="test-sso"></a>SSO testen

Wanneer u de Cisco WebEx-tegel in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de Cisco WebEx waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Cisco WebEx proberen met Azure AD](https://aad.portal.azure.com)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Cisco WebEx beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/protect-webex)