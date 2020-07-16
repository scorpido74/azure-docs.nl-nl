---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor BitBucket | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en EasySSO voor BitBucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ed96e97-b590-4dca-8d00-36288444c641
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946785f02461706d1d3675d089c19d68816fb8ca
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077096"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor BitBucket

In deze zelfstudie leert u hoe u EasySSO voor BitBucket kunt integreren met Azure AD (Active Directory). Wanneer u EasySSO voor BitBucket integreert met Azure AD, kunt u:

* Bepalen in Azure AD wie toegang heeft tot EasySSO voor BitBucket.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij EasySSO voor BitBucket.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op EasySSO voor BitBucket dat voor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* EasySSO voor BitBucket ondersteunt door SP en IDP geïnitieerde eenmalige aanmelding.
* EasySSO voor BitBucket ondersteunt het Just-In-Time inrichten van gebruikers.
* Nadat u EasySSO voor BitBucket hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>EasySSO voor BitBucket toevoegen vanuit de galerie

Om de integratie van EasySSO voor BitBucket in Azure AD te configureren, moet u EasySSO voor BitBucket uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **EasySSO voor BitBucket**.
1. Selecteer **EasySSO voor BitBucket** in de resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Eenmalige aanmelding van Azure AD configureren en testen voor EasySSO voor BitBucket

Configureer en test eenmalige aanmelding van Azure AD met EasySSO voor BitBucket met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een gekoppelde relatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in EasySSO voor BitBucket.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met EasySSO voor BitBucket te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Configureer eenmalige aanmelding bij EasySSO voor BitBucket](#configure-easysso-for-bitbucket-sso) als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. [Maak een testgebruiker voor EasySSO voor BitBucket](#create-an-easysso-for-bitbucket-test-user) als u een tegenhanger van B. Simon in EasySSO voor BitBucket wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **EasySSO voor BitBucket** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IdP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    - In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem bij twijfel contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De EasySSO voor BitBucket-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van standaardkenmerken](common/default-attributes.png)

1. De toepassing EasySSO voor BitBucket verwacht ook dat er nog enkele kenmerken moeten worden teruggestuurd via de SAML-respons. Dit kunt u in de volgende tabel zien. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.
    
    | Naam | Bronkenmerk|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Als uw Azure AD-gebruikers **sAMAccountName** hebben geconfigureerd, moet u **urn:oid:0.9.2342.19200300.100.1.1** aan het **sAMAccountName**-kenmerk toewijzen.
    
1. Op de pagina **Eenmalige aanmelding met SAML** instellen in de sectie **SAML-handtekeningcertificaat** selecteert u de downloadkoppelingen voor de optie **Certificaat (Base64)** of **Federation Metadata-XML**. Sla een van beide of beide op uw computer op. U hebt deze later nodig om BitBucket EasySSO te configureren.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met de downloadkoppelingen gemarkeerd](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Als u van plan bent om EasySSO voor BitBucket handmatig met een certificaat te configureren, moet u ook de **aanmeldings-URL** en de **Azure AD-id** kopiëren en op uw computer opslaan.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B. Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer `B.Simon` in bij **Naam**.  
   1. Voer bij **Gebruikersnaam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot EasySSO voor BitBucket.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **EasySSO voor BitBucket** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in de lijst met gebruikers in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst **Gebruikers** en kies **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Eenmalige aanmelding bij EasySSO voor BitBucket configureren

1. Meld u bij uw Atlassian BitBucket-exemplaar aan met beheerdersmachtigingen en ga naar de sectie **Beheer**. 

    ![Schermopname van het BitBucket-exemplaar, met het tandwielpictogram gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Zoek en selecteer **EasySSO**.

    ![Schermopname van de optie EasySSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Selecteer **SAML**. U wordt naar de sectie SAML-configuratie geleid.

    ![Schermopname van de beheerpagina EasySSO, met SAML gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Selecteer het tabblad **Certificaten**. Het volgende scherm wordt geopend:

    ![Schermopname van het tabblad Certificaten, met diverse opties gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Zoek de optie **Certificaat (Base64)** of **Metagegevensbestand** dat u in de vorige sectie van deze zelfstudie hebt opgeslagen. U kunt op een van de volgende manieren doorgaan:

    - Gebruik het **Metagegevensbestand** van de app-federatie dat u naar een lokaal bestand op de computer hebt gedownload. Selecteer het keuzerondje **Uploaden** en volg het specifieke pad voor het besturingssysteem.

    - Open het **Metagegevensbestand** voor app-federatie om de inhoud van het bestand in een eenvoudige teksteditor te bekijken. Kopieer het naar het klembord. Selecteer **Invoer** en plak de inhoud van het klembord in het tekstveld.
 
    - Voer een volledig handmatige configuratie uit. Open het **certificaat (Base64)** voor app-federatie om de inhoud van het bestand in een eenvoudige teksteditor te bekijken. Kopieer het naar het klembord en plak het in het tekstveld **IdP-certificaat voor token-ondertekening**. Ga vervolgens naar het tabblad **Algemeen** en vul de velden **POST-binding-URL** en **Entiteits-id** met respectieve waarden voor **Aanmeldings-URL** en **Azure AD-id** die u eerder hebt opgeslagen.
 
1. Selecteer **Opslaan** onder aan de pagina. U ziet dat de inhoud van de metagegevens- of certificaatbestanden in de configuratievelden wordt geparseerd. De configuratie van EasySSO voor BitBucket is voltooid.

1. Als u de configuratie wilt testen, gaat u naar het tabblad **Uiterlijk** en selecteert u **SAML-aanmeldingsknop**. Hiermee wordt een aparte knop op het BitBucket-aanmeldingsscherm ingeschakeld die specifiek is bedoeld om uw Azure AD SAML-integratie end-to-end te testen. U kunt deze knop ingeschakeld laten en ook de positie, kleur en vertaling voor productiemodus configureren.

    ![Schermopname van het tabblad Uiterlijk, met SAML-aanmeldingsknop gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Als u problemen ondervindt, neemt u contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Testgebruiker voor EasySSO voor BitBucket maken

In deze sectie maakt u in BitBucket een gebruiker met de naam Britta Simon. EasySSO voor BitBucket ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is uitgeschakeld. Als u het wilt inschakelen, moet u **Gebruiker maken na geslaagde aanmelding** expliciet inschakelen in de sectie **Algemeen** van de configuratie voor de EasySSO-invoegtoepassing. Als er nog geen gebruiker in BitBucket bestaat, wordt er na authenticatie een nieuwe gemaakt.

Als u het automatisch inrichten van gebruikers echter niet wilt inschakelen wanneer de gebruiker zich voor het eerst aanmeldt, moeten gebruikers aanwezig zijn in de gebruikersmappen waarvan het exemplaar van BitBucket gebruikmaakt. Deze directory kan bijvoorbeeld LDAP of Atlassian Crowd zijn.

![Schermopname van de sectie Algemeen van de configuratie voor de EasySSO-invoegtoepassing, met Gebruiker maken na geslaagde aanmelding gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

### <a name="idp-initiated-workflow"></a>Door IdP geïnitieerde werkstroom

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel EasySSO voor BitBucket selecteert, wordt u automatisch aangemeld bij het exemplaar van BitBucket waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) voor meer informatie.

### <a name="sp-initiated-workflow"></a>Door SP geïnitieerde werkstroom

In deze sectie gaat u de configuratie van de eenmalige aanmelding voor Azure AD testen met behulp van de **SAML-aanmeldingsknop** van BitBucket.

![Schermopname van het aanmeldingsvenster, met SAML-aanmelding gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

In dit scenario wordt ervan uitgegaan dat u op de **SAML-aanmeldingsknop** hebt geklikt op het tabblad **Uiterlijk** op de configuratiepagina van de BitBucket EasySSO. Open de aanmeldings-URL voor BitBucket in de incognitomodus van de browser om hinder met uw bestaande sessies te voorkomen. Selecteer **SAML-aanmelding** om te worden omgeleid naar de Azure AD-gebruikersverificatiestroom. Nadat dit is voltooid, wordt u als geverifieerde gebruiker via SAML teruggeleid naar uw BitBucket-exemplaar.

Mogelijk ziet u het volgende scherm nadat u bent omgeleid vanuit Azure AD:

![Schermopname van het EasySSO-scherm met de fout, met het referentienummer gemarkeerd](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

Als dat zo is, volgt u de [instructies op deze pagina](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) om toegang te krijgen tot het bestand **atlassian-bitbucket.log**. Details van de fout zijn beschikbaar via de verwijzings-id op de foutpagina van EasySSO.

Als u problemen ondervindt, neemt u contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [EasySSO voor BitBucket uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [EasySSO voor BitBucket beveiligen met geavanceerde zichtbaarheid en beheeropties](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
