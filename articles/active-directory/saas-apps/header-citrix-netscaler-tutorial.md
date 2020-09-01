---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Citrix NetScaler | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Citrix NetScaler door headergebaseerde verificatie te gebruiken.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.openlocfilehash: 48fedf7f3a73ce7fde60a1df80d971a5d7f88dd7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540577"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Citrix NetScaler (headergebaseerde verificatie)

In deze zelfstudie leert u hoe u Citrix NetScaler integreert met Azure Active Directory (Azure AD). Wanneer u Citrix NetScaler integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang heeft tot Citrix NetScaler.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Citrix NetScaler.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Citrix NetScaler waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. De zelfstudie omvat deze scenario’s:

* **Door SP geïnitieerde** eenmalige aanmelding voor Citrix NetScaler

* **Just-In-Time**-inrichting van gebruikers voor Citrix NetScaler

* [Headergebaseerde verificatie voor Citrix NetScaler](#publish-the-web-server)

* [Kerberos-gebaseerde verificatie voor Citrix NetScaler](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler toevoegen uit de galerie

Om Citrix NetScaler te integreren met Azure AD, voegt u Citrix NetScaler eerst uit de galerie toe aan uw lijst met beheerde SaaS-apps:

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer **Azure Active Directory** in het linkermenu.

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.

1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Citrix NetScaler**.

1. Selecteer **Citrix NetScaler** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Citrix NetScaler

Configureer en test eenmalige aanmelding van Azure AD met Citrix NetScaler met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Citrix NetScaler.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Citrix NetScaler te configureren en testen:

1. [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso) : zodat uw gebruikers deze functie kunnen gebruiken.

    1. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user): om eenmalige aanmelding van Azure AD te testen met B.Simon.

    1. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user): zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.

1. [Eenmalige aanmelding voor Citrix NetScaler configureren](#configure-citrix-netscaler-sso): als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.

    * [Een testgebruiker voor Citrix NetScaler maken](#create-a-citrix-netscaler-test-user): als u een tegenhanger van B.Simon in Citrix NetScaler wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.

1. [Eenmalige aanmelding testen](#test-sso) : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen via de Azure-portal:

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Citrix NetScaler** naar **Beheren** en selecteer **Eenmalige aanmelding**.

1. Selecteer in het deelvenster **Selecteer een methode voor eenmalige aanmelding** de optie **SAML**.

1. Selecteer in het deelvenster **Eenmalige aanmelding met SAML instellen** het penpictogram **Bewerken** voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** doet u het volgende om de toepassing in de **door IDP geïnitieerde** modus te configureren:

    1. Typ in het tekstvak **Id** een URL met het volgende patroon: `https://<Your FQDN>`

    1. Typ in het tekstvak **Antwoord-URL** een URL met het volgende patroon: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Als u de toepassing in de **door SP geïnitieerde** modus wilt configureren, selecteert u **Extra URL's instellen** en voert u de volgende stap uit:

    * Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * De URL’s die in deze sectie worden gebruikt, zijn geen echte waarden. Werk deze waarden bij met de werkelijke waarden voor Id, Antwoord-URL en Aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Citrix NetScaler](https://www.citrix.com/contact/technical-support.html) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.
    > * Om eenmalige aanmelding in te stellen, moeten de URL’s toegankelijk zijn vanaf openbare websites. U moet de firewall of andere beveiligingsinstellingen aan de zijde van Citrix NetScaler inschakelen, zodat Azure AD het token op de geconfigureerde URL kan posten.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** kopieert u de URL bij **App-URL voor federatieve metagegevens** en slaat u deze op in Kladblok.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de Citrix NetScaler-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer het pictogram **Bewerken** en wijzig de kenmerktoewijzingen.

    ![De SAML-kenmerktoewijzing bewerken](common/edit-attribute.png)

1. Bovendien worden in de Citrix NetScaler-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. In de sectie **Gebruikerskenmerken** onder **Gebruikersclaims** voert u de volgende stappen uit om de SAML-tokenkenmerken toe te voegen, zoals weergegeven in de tabel:

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Bij **Kenmerk** selecteert u **Bron**.

    1. Typ in de lijst **Bronkenmerk** de kenmerkwaarde die voor die rij wordt weergegeven.

    1. Selecteer **OK**.

    1. Selecteer **Opslaan**.

1. In de sectie **Citrix NetScaler instellen** kopieert u de juiste URL's op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkermenu van de Azure-portal achtereenvolgens **Azure Active Directory**, **Gebruikers** en **Alle gebruikers**.

1. Selecteer **Nieuwe gebruiker** boven aan het scherm.

1. Voltooi deze stappen in **Gebruikerseigenschappen**:

   1. Voer `B.Simon` in bij **Naam**.  

   1. Bij **Gebruikersnaam** voert u _username@companydomain.extension_ in. Bijvoorbeeld `B.Simon@contoso.com`.

   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer of kopieer de waarde die in **Wachtwoord** wordt weergegeven.

   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door de gebruiker toegang te verlenen tot Citrix NetScaler.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

1. Selecteer **Citrix NetScaler** in de lijst met toepassingen.

1. In het app-overzicht onder **Beheren** selecteert u **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. In het dialoogvenster **Gebruikers en groepen** selecteert u **B.Simon** in de lijst **Gebruikers**. Kies **Selecteren**.

1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de relevante rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren**.

1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-citrix-netscaler-sso"></a>Eenmalige aanmelding voor Citrix NetScaler configureren

Selecteer een koppeling voor stappen voor het soort verificatie dat u wilt configureren:

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor headergebaseerde verificatie](#publish-the-web-server)

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor Kerberos-gebaseerde verificatie](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>De webserver publiceren 

Ga als volgt te werk om een virtuele server te maken:

1. Selecteer **Verkeersbeheer** > **Taakverdeling** > **Services**.
    
1. Selecteer **Toevoegen**.

    ![Citrix NetScaler-configuratie - Deelvenster Services](./media/header-citrix-netscaler-tutorial/web01.png)

1. Stel de volgende waarden in voor de webserver die de toepassingen uitvoert:

   * **Servicenaam**
   * **IP-adres van de server/Bestaande server**
   * **Protocol**
   * **Poort**

     ![Deelvenster Citrix NetScaler-configuratie](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>De load balancer configureren

Ga als volgt te werk om de load balancer te configureren:

1. Ga naar **Verkeersbeheer** > **Taakverdeling** > **Virtuele servers**.

1. Selecteer **Toevoegen**.

1. Stel de volgende waarden in zoals beschreven in de volgende schermopname:

    * **Naam**
    * **Protocol**
    * **IP-adres**
    * **Poort**

1. Selecteer **OK**.

    ![Citrix NetScaler-configuratie - Deelvenster Basisinstellingen](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>De virtuele server binden

Ga als volgt te werk om de load balancer aan de virtuele server te binden:

1. Selecteer in het deelvenster **Services en servicegroepen** de optie **Geen servicebinding van taakverdelende virtuele server**.

   ![Citrix NetScaler-configuratie - Deelvenster Servicebinding van taakverdelende virtuele server](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Verifieer de instellingen zoals weergegeven in de volgende schermopname, en selecteer vervolgens **Sluiten**.

   ![Citrix NetScaler-configuratie - De servicebinding voor de virtuele server verifiëren](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Het certificaat binden

Om deze service als TLS te publiceren, bindt u het servercertificaat en test u uw toepassing:

1. Onder **Certificaat** selecteert u **Geen servercertificaat**.

   ![Citrix NetScaler-configuratie - Deelvenster Servercertificaat](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Verifieer de instellingen zoals weergegeven in de volgende schermopname, en selecteer vervolgens **Sluiten**.

   ![Citrix NetScaler-configuratie - Het certificaat verifiëren](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profiel

Voltooi de volgende secties om het Citrix ADC SAML-profiel te configureren:

### <a name="create-an-authentication-policy"></a>Een verificatiebeleid maken

Ga als volgt te werk om een verificatiebeleid te maken:

1. Ga naar **Beveiliging** > **AAA – Toepassingsverkeer** > **Beleidsregels** > **Verificatie** > **Verificatiebeleid**.

1. Selecteer **Toevoegen**.

1. Typ of selecteer in het deelvenster **Verificatiebeleid maken** de volgende waarden:

    * **Naam**: Voer een naam in voor uw verificatiebeleid.
    * **Actie**: Voer **SAML** in en selecteer **Toevoegen**.
    * **Expressie**:  Voer **true** in.     
    
    ![Citrix NetScaler-configuratie - Deelvenster Verificatiebeleid maken](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Selecteer **Maken**.

### <a name="create-an-authentication-saml-server"></a>Een verificatie-SAML-server maken

Om een verificatie-SAML-server te maken, gaat u naar het deelvenster **Verificatie-SAML-server maken** en voert u de volgende stappen uit:

1. Bij **Naam** voert u een naam in voor de verificatie-SAML-server.

1. Doe het volgende onder **SAML-metagegevens exporteren**:

   1. Schakel het selectievakje **Metagegevens importeren** in.

   1. Voer de URL voor federatieve metagegevens in die u eerder hebt gekopieerd uit de Azure SAML-gebruikersinterface.
    
1. Bij **Naam van uitgever** voert u de relevante URL in.

1. Selecteer **Maken**.

![Citrix NetScaler-configuratie - Deelvenster Verificatie-SAML-server maken](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Een virtuele verificatieserver maken

Ga als volgt te werk om een virtuele verificatieserver te maken:

1.  Ga naar **Beveiliging** > **AAA – Toepassingsverkeer** > **Beleidsregels** > **Verificatie** > **Virtuele verificatieservers**.

1.  Selecteer **Toevoegen** en voltooi de volgende stappen:

    1. Bij **Naam** voert u een naam in voor de virtuele verificatieserver.

    1. Schakel het selectievakje **Niet-adresseerbaar** in.

    1. Bij **Protocol** selecteert u **SSL**.

    1. Selecteer **OK**.

    ![Citrix NetScaler-configuratie - Deelvenster Virtuele verificatieserver maken](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>De virtuele verificatieserver configureren om Azure AD te gebruiken

Wijzig de twee secties voor de virtuele verificatieserver:

1.  In het deelvenster **Geavanceerd verificatiebeleid** selecteert u **Geen verificatiebeleid**.

    ![Citrix NetScaler-configuratie - Deelvenster Geavanceerd verificatiebeleid](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. In het deelvenster **Beleidsbinding** selecteert u het verificatiebeleid en selecteert u vervolgens **Binden**.

    ![Citrix NetScaler-configuratie - Deelvenster Beleidsbinding](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. In het deelvenster **Formuliergebaseerde virtuele servers** selecteert u **Geen taakverdelende virtuele server**.

    ![Citrix NetScaler-configuratie - Deelvenster Formuliergebaseerde virtuele servers](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. Bij **Verificatie-FQDN** voert u een Fully Qualified Domain Name (FQDN) in (vereist).

1. Selecteer de taakverdelende virtuele server die u wilt beveiligen met Azure AD-verificatie.

1. Selecteer **Binden**.

    ![Citrix NetScaler-configuratie - Deelvenster Binding van taakverdelende virtuele server](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Zorg ervoor dat u **Gereed** selecteert in het deelvenster **Configuratie van virtuele verificatieserver**.

1. Om uw wijzigingen te verifiëren, gaat u in een browser naar de toepassings-URL. U zou de aanmeldingspagina van uw tenant moeten zien in plaats van de niet-geverifieerde toegang die u eerder zou hebben gezien.

    ![Citrix NetScaler-configuratie - Een aanmeldingspagina in een webbrowser](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Eenmalige aanmelding voor Citrix NetScaler configureren voor headergebaseerde verificatie

### <a name="configure-citrix-adc"></a>Citrix ADC configureren

Voltooi de volgende secties om Citrix ADC te configureren voor headergebaseerde verificatie.

#### <a name="create-a-rewrite-action"></a>Een herschrijfactie maken

1. Ga naar **AppExpert** > **Herschrijven** > **Herschrijfacties**.
 
    ![Citrix NetScaler-configuratie - Deelvenster Herschrijfacties](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Selecteer **Toevoegen** en voltooi de volgende stappen:

    1. Bij **Naam** voert u een naam in voor de herschrijfactie.

    1. Bij **Type** voert u **INSERT_HTTP_HEADER** in.

    1. Bij **Headernaam** voert u een headernaam in (in dit voorbeeld gebruiken we _SecretID_).

    1. Bij **Expressie** voert u **aaa.USER.ATTRIBUTE("mySecretID")** in, waarbij **mySecretID** de Azure AD SAML-claim is die naar Citrix ADC werd verzonden.

    1. Selecteer **Maken**.

    ![Citrix NetScaler-configuratie - Deelvenster Herschrijfactie maken](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Een herschrijfbeleid maken

1.  Ga naar **AppExpert** > **Herschrijven** > **Herschrijfbeleid**.
 
    ![Citrix NetScaler-configuratie - Deelvenster Herschrijfbeleid](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Selecteer **Toevoegen** en voltooi de volgende stappen:

    1. Bij **Naam** voert u een naam in voor het herschrijfbeleid.

    1. Bij **Actie** selecteert u de herschrijfactie die u in de vorige sectie hebt gemaakt.

    1. Bij **Expressie** voert u **true** in.

    1. Selecteer **Maken**.

    ![Citrix NetScaler-configuratie - Deelvenster Herschrijfbeleid maken](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Een herschrijfbeleid binden aan een virtuele server

Ga als volgt te werk om een herschrijfbeleid te binden aan een virtuele server met behulp van de GUI:

1. Ga naar **Verkeersbeheer** > **Taakverdeling** > **Virtuele servers**.

1. In de lijst met virtuele servers selecteert u de virtuele server waaraan u het herschrijfbeleid wilt binden, en vervolgens selecteert u **Openen**.

1. In het deelvenster **Taakverdelende virtuele server** onder **Geavanceerde instellingen** selecteert u **Beleidsregels**. Alle beleidsregels die voor uw NetScaler-exemplaar zijn geconfigureerd, worden in de lijst weergegeven.
 
    ![Citrix NetScaler-configuratie - Deelvenster Taakverdelende virtuele server](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler-configuratie - Deelvenster Taakverdelende virtuele server](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Schakel het selectievakje in naast de naam van het beleid dat u aan deze virtuele server wilt binden.
 
    ![Citrix NetScaler-configuratie - Deelvenster Verkeersbeleidsbinding van taakverdelende virtuele server](./media/header-citrix-netscaler-tutorial/header08.png)

1. Doe het volgende in het dialoogvenster **Type kiezen**:

    1. Bij **Beleid kiezen** selecteert u **Verkeer**.

    1. Bij **Type kiezen** selecteert u **Aanvraag**.

    ![Citrix NetScaler-configuratie - Dialoogvenster Beleidsregels](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Selecteer **OK**. Een bericht in de statusbalk geeft aan dat het beleid is geconfigureerd.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>De SAML-server wijzigen om kenmerken uit een claim te extraheren

1.  Ga naar **Beveiliging** > **AAA – Toepassingsverkeer** > **Beleidsregels** > **Verificatie** > **Geavanceerde beleidsregels** > **Acties** > **Servers**.

1.  Selecteer de juiste verificatie-SAML-server voor de toepassing.
 
    ![Citrix NetScaler-configuratie - Deelvenster Verificatie-SAML-server configureren](./media/header-citrix-netscaler-tutorial/header09.png)

1. Voer in het deelvenster **Kenmerken** de SAML-kenmerken in die u wilt extraheren, gescheiden door komma’s. In ons voorbeeld voeren we het kenmerk `mySecretID` in.
 
    ![Citrix NetScaler-configuratie - Deelvenster Kenmerken](./media/header-citrix-netscaler-tutorial/header10.png)

1. Om toegang te verifiëren, zoekt u in de URL in een browser naar het SAML-kenmerk onder **Verzameling met headers**.

    ![Citrix NetScaler-configuratie - Verzameling met headers in de URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Een testgebruiker voor Citrix NetScaler maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Citrix NetScaler. Citrix NetScaler ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. U hoeft geen handelingen uit te voeren in deze sectie. Als er nog geen gebruiker in Citrix NetScaler bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, neemt u contact op met het [klantenondersteuningsteam van Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u met behulp van het toegangsvenster de configuratie voor eenmalige aanmelding van Azure AD.

Wanneer u in het toegangsvenster op de tegel Citrix NetScaler klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Citrix NetScaler waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Citrix NetScaler uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Eenmalige aanmelding voor Citrix NetScaler configureren voor Kerberos-gebaseerde verificatie](citrix-netscaler-tutorial.md)
