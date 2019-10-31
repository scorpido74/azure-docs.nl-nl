---
title: 'Zelf studie: integratie Azure Active Directory met Qlik Sense Enter prise | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7886c863dd50fd3f09d782f892bdebabceb73546
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160093"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Zelf studie: Qlik Sense Enter prise integreren met Azure Active Directory

In deze zelf studie leert u hoe u Qlik Sense Enter prise integreert met Azure Active Directory (Azure AD). Wanneer u Qlik Sense Enter prise integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Qlik Sense-onderneming.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Qlik Sense Enter prise met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Eenmalige aanmelding (SSO) voor Qlik Sense-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Qlik Sense Enter prise ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Qlik Sense Enterprise uit de galerie toevoegen

Als u de integratie van Qlik Sense Enterprise in Azure AD wilt configureren, moet u Qlik Sense Enterprise vanuit de galerie toevoegen aan uw lijst beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Qlik Sense Enter prise** in het zoekvak.
1. Selecteer **Qlik Sense Enter prise** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met Qlik Sense Enter prise configureren en testen met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Qlik Sense Enter prise.

Als u Azure AD SSO wilt configureren en testen met Qlik Sense Enter prise, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[Qlik Sense Enter PRISE SSO configureren](#configure-qlik-sense-enterprise-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een Qlik Sense Enterprise-testgebruiker maken](#create-qlik-sense-enterprise-test-user)** : als u een equivalent van Britta Simon wilt maken in Qlik Sense Enterprise dat is gekoppeld aan de Azure AD-versie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Qlik Sense Enter prise** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL die verderop in deze zelfstudie worden beschreven of neem contact op met het [Qlik Sense Enterprise-ondersteuningsteam](https://www.qlik.com/us/services/support) om deze waarden te verkrijgen. De standaard poort voor de Url's is 443, maar u kunt deze aanpassen op basis van de behoeften van uw organisatie.

1. Op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , vindt u de **federatieve meta gegevens-XML** van de opgegeven opties volgens uw vereiste en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-sso"></a>Qlik Sense Enter prise SSO configureren

1. Bereid het XML-bestand met federatieve metagegevens voor zodat u die naar de Qlik Sense-server kunt uploaden.

    > [!NOTE]
    > Voordat u de metagegevens van de IdP naar de Qlik Sense-server uploadt, moet u het bestand bewerken om gegevens te verwijderen voor een goede werking tussen Azure AD en de Qlik Sense-server.

    ![QlikSense][qs24]

    a. Open het bestand FederationMetaData.xml dat u vanuit Azure Portal naar een teksteditor hebt gedownload.

    b. Zoek de waarde **RoleDescriptor**.  Er zijn vier items (twee paren openings- en afsluitelementlabels).

    c. Verwijder de RoleDescriptor-labels en alle gegevens ertussen uit het bestand.

    d. Sla het bestand op en houd deze bij de hand voor later gebruik in dit document.

2. Navigeer naar de Qlik Management Console (QMC) van Qlik Sense als een gebruiker die de configuraties van virtuele webproxy's kan maken.

3. Klik in de QMC op het menu-item **Virtuele proxy's**.

    ![QlikSense][qs6]

4. Klik aan de onderkant van het scherm op de knop **Nieuw**.

    ![QlikSense][qs7]

5. Het bewerkingsscherm voor de virtuele proxy wordt weergegeven.  Aan de rechterkant van het scherm ziet u een menu voor het maken van configuratie-opties.

    ![QlikSense][qs9]

6. Voer, terwijl de id-menuoptie is ingeschakeld, de identificatiegegevens in voor de configuratie van de virtuele proxy in Azure.

    ![QlikSense][qs8]  

    a. In het veld **Beschrijving** vindt u een beschrijvende naam voor de configuratie van de virtuele proxy.  Voer een waarde voor een beschrijving in.

    b. In het veld **Voorvoegsel** wordt het eindpunt van de virtuele proxy bepaald voor het verbinding maken met Qlik Sense via Azure AD-eenmalige aanmelding.  Voer een unieke voorvoegselnaam in voor deze virtuele proxy.

    c. **Sessietime-out bij inactiviteit (minuten)** is de time-out voor verbindingen via deze virtuele proxy.

    d. De **Sessienaam cookieheader** is de naam van de cookie waarin de sessie-id van de Qlik Sense-sessie wordt opgeslagen welke een gebruiker na een geslaagde verificatie ontvangt.  Deze naam moet uniek zijn.

7. Klik op de menuoptie Verificatie om deze zichtbaar te maken.  Het scherm Verificatie wordt weergegeven.

    ![QlikSense][qs10]

    a. In de vervolgkeuzelijst **anonieme toegangsmodus** bepaalt u of anonieme gebruikers toegang kunnen hebben tot Qlik Sense via de virtuele proxy.  De standaardoptie is Geen anonieme gebruiker.

    b. In de vervolgkeuzelijst **verificatiemethode** bepaalt u het verificatieschema dat door de virtuele proxy wordt gebruikt.  Selecteer SAML in de vervolgkeuzelijst.  Hierdoor worden meer opties weergegeven.

    c. Geef in het veld **Host-URI SAML** de hostnaam op die gebruikers invoeren om Qlik Sense via deze virtuele SAML-proxy te openen.  De hostnaam is de URI van de Qlik Sense-server.

    d. Geef in de **Entiteit-ID SAML** dezelfde waarde op die u hebt ingevoerd voor het veld Host-URI SAML.

    e. De **IdP-metagegevens SAML** is het bestand dat eerder in de sectie **Federatieve metagegevens bewerken van Azure AD-configuratie** is bewerkt.  **Voordat u de metagegevens van de IdP uploadt, moet u het bestand bewerken** om gegevens te verwijderen voor een goede werking tussen Azure AD en de Qlik Sense-server.  **Raadpleeg de bovenstaande instructies als het bestand nog moet worden bewerkt.**  Klik, als het bestand is bewerkt, op de knop Bladeren en selecteer het bewerkte metagegevensbestand om dit naar de virtuele-proxyconfiguratie te uploaden.

    f. Voer de kenmerknaam of schemareferentie in voor het SAML-kenmerk waarin de **UserID** wordt weergegeven die Azure AD naar de Qlik Sense-server verzendt.  Schemareferentiegegevens zijn beschikbaar in de Azure-appschermen na configuratie.  Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in om gebruik te maken van het naamkenmerk.

    g. Voer de waarde in voor de **gebruikerslijst** die zal worden gekoppeld aan gebruikers wanneer deze worden geverifieerd met de Qlik Sense-server via Azure AD.  Vastgelegde waarden moeten tussen **vierkante haken []** staan.  Als u een kenmerk wilt gebruiken dat in de Azure AD SAML-assertie is verzonden, voert u in dit tekstvak de naam van het kenmerk in **zonder** vierkante haken.

    h. Met het **algoritme voor SAML-ondertekening** stelt u de serviceprovider (in dit geval de Qlik Sense-server) in voor certificaatondertekening voor de virtuele-proxyconfiguratie.  Als de Qlik Sense-server gebruikmaakt van een vertrouwd certificaat dat is gegenereerd met behulp van de Microsoft Enhanced RSA en AES Cryptographic Provider, wijzigt u het SAML-ondertekeningsalgoritme in **SHA-256**.

    i. In de sectie voor het toewijzen van SAML-kenmerken kunt u aanvullende kenmerken toewijzen, zoals groepen die naar Qlik Sense moeten worden verzonden voor gebruik in regels.

8. Klik op de menuoptie **TAAKVERDELING** om deze zichtbaar te maken.  Het scherm Taakverdeling wordt weergegeven.

    ![QlikSense][qs11]

9. Klik op de knop **Nieuw serverknooppunt toevoegen**, selecteer een of meer engineknooppunten waarnaar Qlik Sense voor taakverdelingsdoeleinden sessies verzendt, en klik op de knop **Toevoegen**.

    ![QlikSense][qs12]

10. Klik op de menuoptie Geavanceerd om deze zichtbaar te maken. Het scherm Geavanceerd wordt weergegeven.

    ![QlikSense][qs13]

    De acceptatie lijst van de host identificeert hostnamen die worden geaccepteerd bij het maken van verbinding met de Qlik Sense-server.  **Voer de hostnaam in die gebruikers opgeven wanneer deze verbinding maken met de Qlik Sense-server.** De hostnaam heeft dezelfde waarde als de host-URI van SAML zonder de https://.

11. Klik op de knop **Toepassen**.

    ![QlikSense][qs14]

12. Klik op OK om de waarschuwing te accepteren waarin wordt gemeld dat proxy's die zijn gekoppeld aan de virtuele proxy opnieuw worden gestart.

    ![QlikSense][qs15]

13. Aan de rechterkant van het scherm wordt het menu Gekoppelde items weergegeven.  Klik op de menu-optie **Proxy's**.

    ![QlikSense][qs16]

14. Het proxy-scherm wordt weergegeven.  Klik op de knop **Koppeling** onderaan om een proxy aan de virtuele proxy te koppelen.

    ![QlikSense][qs17]

15. Selecteer het proxyknooppunt dat deze virtuele proxyverbinding ondersteunt en klik op de knop **Koppeling**.  Na de koppeling wordt de proxy vermeld bij Gekoppelde proxy's.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Na ongeveer vijf tot tien seconden verschijnt het bericht QMC vernieuwen.  Klik op de knop **QMC vernieuwen**.

    ![QlikSense][qs20]

17. Klik, wanneer de QMC is vernieuwd, op het menu-item **Virtuele proxy's**. De vermelding van de nieuwe virtuele SAML-proxy wordt in de tabel op het scherm weergegeven.  Klik eenmaal op de vermelding van de virtuele proxy.

    ![QlikSense][qs51]

18. Aan de onderkant van het scherm wordt de knop SP-metagegevens downloaden geactiveerd.  Klik op de knop **SP-metagegevens downloaden** om de metagegevens in een bestand op te slaan.

    ![QlikSense][qs52]

19. Open het bestand met SP-metagegevens.  Bekijk de vermelding **entityID** en de vermelding **AssertionConsumerService**.  Deze waarden zijn gelijk aan de **Id**, **Aanmeldings-URL** en de **Antwoord-URL** in de configuratie van de Azure AD-toepassing. Plak deze waarden in de sectie **Qlik Sense Enterprise-domein en -URL's** van de configuratie van de Azure AD-toepassing als deze niet overeenkomen. Vervolgens dient u deze in de wizard Azure AD-app configureren te vervangen.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extension in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Qlik Sense Enter prise.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Qlik Sense Enter prise**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-qlik-sense-enterprise-test-user"></a>Een Qlik Sense Enterprise-testgebruiker maken

In deze sectie maakt u de gebruiker Britta Simon in Qlik Sense Enterprise. Voeg in samenwerking met het  [Qlik Sense Enterprise-ondersteuningsteam](https://www.qlik.com/us/services/support) de gebruikers toe in het Qlik Sense Enterprise-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Qlik Sense ENTER selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de Qlik Sense-onderneming waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
