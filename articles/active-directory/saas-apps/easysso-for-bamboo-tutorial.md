---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor Bamboo | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en EasySSO voor Bamboo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f594613d-0cd8-4046-a7c5-2da2971afecd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c37850d2188f560b8eb8d0b16f5a1b2880a8b32e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83740567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bamboo"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met EasySSO voor Bamboo

In deze zelfstudie leert u hoe u EasySSO voor Bamboo kunt integreren met Azure AD (Active Directory). Wanneer u EasySSO voor Bamboo integreert met Azure AD, kunt u:

* Bepalen in Azure AD wie toegang heeft tot EasySSO voor Bamboo.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij EasySSO voor Bamboo.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op EasySSO voor Bamboo waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* EasySSO voor Bamboo ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* EasySSO voor Bamboo ondersteunt het **Just-In-Time** inrichten van gebruikers
* Zodra u EasySSO voor Bamboo hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-bamboo-from-the-gallery"></a>EasySSO voor Bamboo toevoegen vanuit de galerie

Om de integratie van EasySSO voor Bamboo in Azure AD te configureren, moet u EasySSO voor Bamboo uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **EasySSO voor Bamboo**.
1. Selecteer **EasySSO voor Bamboo** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bamboo"></a>Eenmalige aanmelding van Azure AD configureren en testen voor EasySSO voor Bamboo

Configureer en test eenmalige aanmelding van Azure AD met EasySSO voor Bamboo met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in EasySSO voor Bamboo.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met EasySSO voor Bamboo te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij EasySSO voor Jira configureren](#configure-easysso-for-bamboo-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor EasySSO voor Jira maken](#create-easysso-for-bamboo-test-user)** : als u een tegenhanger van B.Simon in EasySSO voor Bamboo wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **EasySSO voor Bamboo** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van EasySSO voor Bamboo](mailto:support@techtime.co.nz) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw EasySSO voor Bamboo-toepassing verwacht de SAML-asserties in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing EasySSO voor Bamboo nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk |
    | ---------------|  --------- |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.42 | user.givenname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.userprincipalname |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot EasySSO voor Bamboo.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **EasySSO voor Bamboo** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-easysso-for-bamboo-sso"></a>Eenmalige aanmelding bij EasySSO voor Bamboo configureren

1. Meld u bij uw EasySSO voor Bamboo-instantie aan met beheerdersmachtigingen en navigeer naar de sectie **Apps beheren**.

    ![EasySSO voor Bamboo configureren](./media/easysso-for-bamboo-tutorial/jira-admin-1.png)

1. Klik op **EasySSO**.

    ![EasySSO voor Bamboo configureren](./media/easysso-for-bamboo-tutorial/jira-admin-2.png)

1. Selecteer de optie **SAML**. U wordt vervolgens naar de sectie SAML-configuratie geleid.

    ![EasySSO voor Bamboo configureren](./media/easysso-for-bamboo-tutorial/jira-admin-3.png)

1. Selecteer het tabblad **Certificaten** bovenaan, waarna het volgende scherm wordt weergegeven. Zoek het **Certificaat (Base64)** of **Metagegevensbestand** dat u hebt opgeslagen in de vorige stappen van **Eenmalige aanmelding bij Azure AD configureren**. U hebt de volgende opties om verder te gaan:

    ![EasySSO voor Bamboo configureren](./media/easysso-for-bamboo-tutorial/jira-admin-4.png)

    a. Gebruik het **Metagegevensbestand** voor app-federatie dat u hebt gedownload naar het lokale bestand op uw computer. Selecteer het keuzerondje **Uploaden** en volg het specifieke dialoogvenster Bestand uploaden voor uw besturingssysteem

    **OF**

    b. Open het **Metagegevensbestand** voor app-federatie om de inhoud van het bestand (in een eenvoudige teksteditor) te bekijken en kopieer deze naar het klembord. Selecteer de optie **Invoer** en plak de klembordinhoud in het tekstveld.

    **OF**

    c. Volledig handmatige configuratie. Open het **Certificaat (Base64)** voor app-federatie om de inhoud van het bestand (in een eenvoudige teksteditor) te bekijken en kopieer deze naar het klembord. Plak de klembordinhoud in het tekstveld **Certificaten voor IdP-tokenondertekening**. Navigeer vervolgens naar het tabblad **Algemeen** en vul de velden **POST-binding-URL** en **Entiteits-id** met respectieve waarden voor **Aanmeldings-URL** en **Azure AD-id** die u eerder hebt opgeslagen.

1. Klik op de knop **Opslaan** onderaan de pagina. U ziet dat de inhoud van het bestand Metagegevens of Certificaat wordt geparseerd in de configuratievelden. De configuratie van EasySSO voor Bamboo is voltooid.

1. Voor de beste testervaring navigeert u naar het tabblad **Weergave** en schakelt u de optie **SAML-aanmelding** in. Er wordt dan een aparte knop op het EasySSO for Bamboo-aanmeldingsscherm ingeschakeld die specifiek is bedoeld om uw Azure AD SAML-integratie end-to-end te testen. U kunt deze knop ingeschakeld laten en ook de positie, kleur en vertaling voor productiemodus configureren.

    ![EasySSO voor Bamboo configureren](./media/easysso-for-bamboo-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Neem contact op met het [ondersteuningsteam van EasySSO](mailto:support@techtime.co.nz) als u problemen ondervindt.

### <a name="create-easysso-for-bamboo-test-user"></a>Testgebruiker voor EasySSO voor Bamboo maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in EasySSO voor Bamboo. EasySSO voor Bamboo ondersteunt het Just-In-Time inrichten van gebruikers, wat standaard is **uitgeschakeld**. Als u het inrichten van gebruikers wilt inschakelen, moet u de optie **Gebruiker maken na geslaagde aanmelding** expliciet inschakelen in de sectie Algemeen van de configuratie van de EasySSO-invoegtoepassing. Als er nog geen gebruiker in EasySSO voor Bamboo bestaat, wordt er een nieuwe gemaakt na verificatie.

Als u het automatisch inrichten van gebruikers na hun eerste aanmelding echter niet wilt inschakelen, moeten gebruikers bestaan in back-end gebruikerslijsten waarvan de EasySSO voor Bamboo-instantie gebruikmaakt, zoals LDAP of Atlassian Crowd.

![Inrichten van gebruikers](./media/easysso-for-bamboo-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel EasySSO voor Bamboo in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van EasySSO voor Bamboo waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [EasySSO voor Bamboo uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [EasySSO voor Bamboo beveiligen met geavanceerde zichtbaarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)