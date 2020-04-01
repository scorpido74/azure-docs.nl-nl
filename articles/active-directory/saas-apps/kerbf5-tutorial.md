---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met F5 | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f06b0b5aa59328d2fe39d501cfdf3ad7524427
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75431457"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met F5

In deze zelfstudie leert u hoe u F5 integreert met Azure Active Directory (Azure AD). Wanneer u F5 integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot F5.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij F5 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)

* F5 single sign-on (SSO) ingeschakeld abonnement.

* Voor het implementeren van de gezamenlijke oplossing is de volgende licentie vereist:
    * F5 BIG-IP® Beste bundel (of)

    * Zelfstandige licentie F5 BIG-IP Access Policy Manager™ (APM)

    * F5 BIG-IP Access Policy Manager™ (APM) add-on licentie op een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).

    * Naast de bovenstaande licentie kan het F5-systeem ook worden gelicentieerd met:

        * Een URL-filteringsabonnement om de URL-categoriedatabase te gebruiken

        * Een F5 IP Intelligence-abonnement om bekende aanvallers en kwaadaardig verkeer te detecteren en te blokkeren

        * Een netwerkhardwarebeveiligingsmodule (HSM) om digitale sleutels te beveiligen en te beheren voor sterke verificatie

* F5 BIG-IP-systeem is ingericht met APM-modules (LTM is optioneel)

* Hoewel optioneel, is het ten zeerste aanbevolen om de F5-systemen te implementeren in een [synchronisatie/failover-apparaatgroep](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F-installatie), die het actieve stand-by-paar omvat, met een zwevend IP-adres voor hoge beschikbaarheid (HA). Verdere interfaceredundantie kan worden bereikt met behulp van het Link Aggregation Control Protocol (LACP). LACP beheert de verbonden fysieke interfaces als één virtuele interface (geaggregeerde groep) en detecteert eventuele interfacefouten binnen de groep.

* Voor Kerberos-toepassingen is een on-premises AD-serviceaccount voor beperkte delegatie.  Raadpleeg [F5-documentatie](https://support.f5.com/csp/article/K43063049) voor het maken van een AD-delegatieaccount.

## <a name="access-guided-configuration"></a>Toegang tot begeleide configuratie

* Access guided configuration' wordt ondersteund op F5 TMOS versie 13.1.0.8 en hoger. Als uw BIG-IP-systeem een versie onder 13.1.0.8 uitvoert, raadpleegt u de sectie **Geavanceerde configuratie.**

* Access guided configuration biedt een compleet nieuwe en gestroomlijnde gebruikerservaring. Deze workflowgebaseerde architectuur biedt intuïtieve, opnieuw toetrederconfiguratiestappen die zijn afgestemd op de geselecteerde topologie.

* Voordat u overgaat tot de configuratie, upgradet u de beheerde configuratie door het nieuwste use case pack van [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748)te downloaden. Om te upgraden, volg de onderstaande procedure.

    >[!NOTE]
    >De screenshots hieronder zijn voor de nieuwste versie (BIG-IP 15.0 met AGC versie 5.0). De onderstaande configuratiestappen zijn geldig voor deze use case van 13.1.0.8 tot de nieuwste BIG-IP-versie.

1. Klik op de F5 BIG-IP Web UI op **Access >> Guide Configuration**.

2. Klik op de pagina **Begeleide configuratie** op **Begeleide configuratie** op de linkerbovenhoek.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. Selecteer op het popscherm Configuratie van de upgradegids de optie **Bestand kiezen** om het gedownloade use casepack te uploaden en klik op de knop **Uploaden en installeren.**

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. Wanneer de upgrade is voltooid, klikt u op **Doorgaan.**

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* F5 ondersteunt **SP en IDP** gestart met SSO
* F5 SSO kan op drie verschillende manieren worden geconfigureerd.

- [F5-aanmelding configureren voor Kerberos-toepassing](#configure-f5-single-sign-on-for-kerberos-application)

- [F5-aanmelding configureren voor toepassing op basis van kopteksten](headerf5-tutorial.md)

- [F5-aanmelding configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Sleutelverificatiescenario's

Naast Azure Active Directory native integration support voor moderne authenticatieprotocollen zoals Open ID Connect, SAML en WS-Fed, breidt F5 veilige toegang uit voor legacy-gebaseerde authenticatie-apps voor zowel interne als externe toegang met Azure AD, waardoor moderne scenario's (bijvoorbeeld toegang zonder wachtwoord) tot deze toepassingen mogelijk zijn. Dit omvat:

* Verificatie-apps op basis van kopteksten

* Kerberos-verificatie-apps

* Anonieme verificatie of geen ingebouwde verificatie-apps

* NTLM-verificatie-apps (beveiliging met dubbele prompts voor de gebruiker)

* Formulieren gebaseerde toepassing (bescherming met dubbele prompts voor de gebruiker)

## <a name="adding-f5-from-the-gallery"></a>F5 toevoegen vanuit de galerie

Als u de integratie van F5 in Azure AD wilt configureren, moet u F5 uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **F5** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **F5** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Azure AD-aanmelding voor F5 configureren en testen

Azure AD SSO configureren en testen met F5 met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in F5.

Als u Azure AD SSO met F5 wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[F5 SSO configureren](#configure-f5-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[F5-testgebruiker maken](#create-f5-test-user)** - om een tegenhanger van B.Simon in F5 te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **F5-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<YourCustomFQDN>.f5.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourCustomFQDN>.f5.com/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [f5 client support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** and Certificate **(Base64)** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **F5 instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot F5.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **F5**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**
1. Klik op **voorwaardelijke toegang** .
1. Klik op **Nieuw beleid**.
1. U uw F5-app nu zien als een bron voor CA-beleid en voorwaardelijke toegang toepassen, waaronder Multifactor Auth, apparaatgebaseerd toegangscontrole of identiteitsbeveiligingsbeleid.

## <a name="configure-f5-sso"></a>F5 SSO configureren

- [F5-aanmelding configureren voor toepassing op basis van kopteksten](headerf5-tutorial.md)

- [F5-aanmelding configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>F5-aanmelding configureren voor Kerberos-toepassing

### <a name="guided-configuration"></a>Begeleide configuratie

1. Open een nieuw webbrowservenster en meld u aan op uw F5-bedrijfssite (Kerberos) als beheerder en voer de volgende stappen uit:

1. U moet het metagegevenscertificaat importeren in de F5, dat later in het installatieproces wordt gebruikt.

1. Navigeer naar **systeembeheer > certificaatbeheer > verkeerscertificaatbeheer > SSL-certificaatlijst**. Selecteer **Importeren** vanuit de rechterhoek. Geef een **certificaatnaam op** (wordt later in de config genoemd). Selecteer In de **certificaatbron**de optie Bestand uploaden geef het certificaat op dat is gedownload van Azure terwijl u SAML-eenmalig aanmelding configureert. Klik **op Importeren**.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Daarnaast hebt u **SSL-certificaat nodig voor de hostnaam van toepassing. Navigeer naar de lijst van systeembeheer >-certificaatbeheer > verkeerscertificaatbeheer > SSL-certificaat**. Selecteer **Importeren** vanuit de rechterhoek. **Importtype** is **PKCS 12(IIS)**. Geef een **sleutelnaam** op (wordt later in de config verwezen) en geef het PFX-bestand op. Geef het **wachtwoord** voor de PFX op. Klik **op Importeren**.

    >[!NOTE]
    >In het voorbeeld dat `Kerbapp.superdemo.live`onze app naam is, gebruiken we een Wild Card Certificate onze naam is`WildCard-SuperDemo.live`

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. We gebruiken de begeleide ervaring om de Azure AD Federation en Application Access in te stellen. Ga naar – F5 BIG-IP **Main** en selecteer **Access > Guided Configuration > Federation > SAML Service Provider**. Klik **op Volgende** en klik op **Volgende** om de configuratie te starten.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Geef een **configuratienaam op**. Geef de **entiteits-id** op (hetzelfde als wat u hebt geconfigureerd in de configuratie van de Azure AD-toepassingsconfiguratie). Geef de **hostnaam**op . Voeg een **beschrijving toe** voor referentie. Accepteer de resterende standaarditems en selecteer en klik op **Opslaan & Volgende**.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. In dit voorbeeld maken we een nieuwe Virtuele Server als 192.168.30.200 met poort 443. Geef het IP-adres van de virtuele server op in het **doeladres**. Selecteer het **SSL-profiel**van client , selecteer Nieuw maken. Geef eerder geüpload aanvraagcertificaat (het wildcardcertificaat in dit voorbeeld) en de bijbehorende sleutel op en klik vervolgens op **Opslaan & Volgende**.

    >[!NOTE]
    >in dit voorbeeld draait onze Interne webserver op poort 80 en we willen het publiceren met 443.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. Klik onder **Methode Selecteren om uw IdP-connector te configureren**, Geef Metagegevens op, klik op Bestand kiezen en upload het XML-bestand met metagegevens dat eerder is gedownload van Azure AD. Geef een unieke **naam** op voor saml IDP-connector. Kies het **certificaat voor het ondertekenen met met gegevens** dat eerder is geüpload. Klik **op Opslaan & volgende**.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. Geef **onder Een groep**selecteren op Nieuw **maken** op (selecteer ook een groep die al bestaat). Laat andere waarde standaard zijn. Typ onder Groepservers het IP-adres onder **IP-adres/knooppuntnaam**. Geef de **poort op**. Klik **op Opslaan & volgende**.
 
    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. Selecteer op het scherm Instellingen voor eenmalig aanmelden de optie **Eenmalig aanmelden inschakelen**. Kies **Kerberos**onder **Geselecteerd eenmalig aanmeldingstype** . Plaats **session.saml.last.Identity** door **session.saml.last.attr.name.Identity** onder **Gebruikersnaambron** (deze variabele die is ingesteld met behulp van claimtoewijzing in het Azure AD ). Selecteer **Geavanceerde instelling weergeven**. Typ **onder Kerberos Realm** de domeinnaam. Onder **Accountnaam/ Accountwachtwoord** Geef het APM-delegatieaccount en -wachtwoord op. Geef het IP-adres van domeincontroller op in het **KDC-veld.** Klik **op Opslaan & volgende**.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. Voor deze richtlijnen slaan we eindpuntcontroles over.  Raadpleeg F5-documentatie voor meer informatie.  Selecteer op het scherm **& Volgende opslaan**.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Accepteer de standaardinstellingen en klik op **Volgende opslaan &**. Raadpleeg F5-documentatie voor meer informatie over de instellingen voor saml-sessiebeheer.


    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Bekijk het overzichtsscherm en selecteer **Implementeren** om het BIG-IP te configureren.
 
    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. Zodra de toepassing is geconfigureerd klik op **Voltooien**.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Geavanceerde configuratie

>[!NOTE]
>Klik [hier](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) voor meer informatie

### <a name="configuring-an-active-directory-aaa-server"></a>Een Active Directory AAA-server configureren

U configureert een Active Directory AAA-server in Access Policy Manager (APM) om domeincontrollers en referenties op te geven die APM kan gebruiken voor het verifiëren van gebruikers.

1.  Klik op het tabblad Hoofd op **Toegangsbeleid > AAA-servers > Active Directory**. Het lijstscherm active directory-servers wordt geopend.

2.  Klik **op Maken**. Het scherm Eigenschappen van nieuwe server wordt geopend.

3.  Typ **in** het veld Naam een unieke naam voor de verificatieserver.

4.  Typ in het veld **Domeinnaam** de naam van het Windows-domein.

5.  Selecteer een van de volgende opties voor de instelling **Serververbinding:**

    * Selecteer **Groep gebruiken** om hoge beschikbaarheid voor de AAA-server in te stellen.

    * Selecteer **Direct** om de AAA-server in te stellen voor zelfstandige functionaliteit.

6.  Als u **Direct**hebt geselecteerd, typt u een naam in het veld **Domeincontroller.**

7.  Als u **Groep**gebruiken hebt geselecteerd, configureert u de groep:

    * Typ een naam in het veld **Domeincontrollerpoolnaam.**

    * Geef de **domeincontrollers** in de groep op door het IP-adres en de hostnaam voor elk te typen en op de knop **Toevoegen te** klikken.

    * Als u de status van de AAA-server wilt controleren, u een statusmonitor selecteren: alleen de **gateway_icmp** monitor is in dit geval geschikt; u deze selecteren in de lijst **Servergroepmonitor.**

8.  Typ in het veld **Beheerdersnaam** een hoofdlettergevoelige naam voor een beheerder die beheerdersmachtigingen voor Active Directory heeft. APM gebruikt de gegevens in de velden **Beheerdersnaam** en **beheerderswachtwoord** voor AD-query. Als Active Directory is geconfigureerd voor anonieme query's, hoeft u geen beheerdersnaam op te geven. Anders heeft APM een account nodig met voldoende bevoegdheden om te binden aan een Active Directory-server, gebruikersgroepsgegevens op te halen en Active Directory-wachtwoordbeleid op te halen om wachtwoordgerelateerde functionaliteit te ondersteunen. (APM moet bijvoorbeeld wachtwoordbeleid ophalen als u de gebruiker Vragen selecteert om het wachtwoord te wijzigen voordat de optie afloopt in een actie AD Query.) Als u geen beheerdersaccountgegevens in deze configuratie opgeeft, gebruikt APM het gebruikersaccount om informatie op te halen. Dit werkt als het gebruikersaccount voldoende bevoegdheden heeft.

9.  Typ in het veld **Beheerderswachtwoord** het beheerderswachtwoord dat is gekoppeld aan de domeinnaam.

10. Typ in het veld **Beheerderswachtwoord verifiëren** het beheerderswachtwoord dat is gekoppeld aan de instelling **Domeinnaam** opnieuw.

11. Typ in het veld Levensduur van de **groepcache** het aantal dagen. De standaardlevensduur is 30 dagen.

12. Typ het aantal dagen in het veld Levensduur van de cache van **het wachtwoordobject.** De standaardlevensduur is 30 dagen.

13. Selecteer in de lijst **Kerberos Preauthentication Encryption Type** een versleutelingstype. De standaardinstelling is **Geen**. Als u een versleutelingstype opgeeft, bevat het BIG-IP-systeem Kerberos-preverificatiegegevens in het eerste AS-REQ-pakket (authentication service request).

14. Typ in het veld **Time-out** een time-outinterval (in seconden) voor de AAA-server. (Deze instelling is optioneel.)

15. Klik **op Voltooid**. De nieuwe server wordt weergegeven in de lijst. Hiermee wordt de nieuwe Active Directory-server toegevoegd aan de lijst Active Directory-servers.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML-configuratie

1. U moet het metagegevenscertificaat importeren in de F5, dat later in het installatieproces wordt gebruikt. Navigeer naar **systeembeheer > certificaatbeheer > verkeerscertificaatbeheer > SSL-certificaatlijst**. Selecteer **Importeren** vanuit de rechterhoek.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. Als u de SAML IDP wilt instellen, **navigeert u naar Access > Federation > SAML: Service Provider > externe Idp-connectors**en klikt u op **> maken van metagegevens**.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. Als u de SAML SP wilt instellen, navigeert u naar **Access > Federation > SAML-serviceprovider > lokale SP-services** en klikt u op **Maken**. Vul de volgende informatie in en klik op **OK**.

    * Typenaam: KerbApp200SAML
    * Entiteits-id*:https://kerbapp200.superdemo.live
    * SP-naaminstellingen
    * Schema: https
    * Gastheer: kerbapp200.superdemo.live
    * Beschrijving: kerbapp200.superdemo.live

     ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Selecteer de SP-configuratie, KerbApp200SAML en klik op **IdP-connectors binden/binden**.

     ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     c. Klik op **Nieuwe rij toevoegen** en selecteer de externe **IdP-connector** die in de vorige stap is gemaakt, klik op **Bijwerken**en klik vervolgens op **OK**.

     ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. Als u Kerberos SSO wilt configureren, navigeert u naar **Access > Single Sign-on > Kerberos,** volledige informatie en klikt u op **Voltooid**.

    >[!Note]
    > Je hebt het Kerberos-delegatieaccount nodig om te worden gemaakt en opgegeven. Sectie KCD verwijzen (Bijlage voor variabele verwijzingen verwijzen)

    * **Gebruikersnaam Bron**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **User Realm Bron**: session.logon.last.domain

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. Als u Toegangsprofiel wilt configureren, navigeert u naar **Access > Profiel/Beleid > Toegangsprofiel (per sessiebeleid),** klikt u op **Maken,** de volgende informatie invullen en klikt u op **Voltooid**.

    * Naam: KerbApp200
    * Profieltype: alles
    * Profielbereik: profiel
    * Talen: Engels

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Klik op de naam KerbApp200, vul de volgende informatie in en klik op **Bijwerken.**

    * Domain Cookie: superdemo.live
    * SSO-configuratie: KerAppSSO_sso

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Klik **op Toegangsbeleid** en klik vervolgens op **Toegangsbeleid bewerken** voor profiel 'KerbApp200'.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain TEXT superdemo.live**

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Als u Nieuw knooppunt wilt toevoegen, navigeert u naar **Lokaal verkeer > knooppunten > knooppuntlijst, klikt u op Maken,** de volgende informatie invullen en klikt u vervolgens op **Voltooid**.

    * Naam: KerbApp200
    * Beschrijving: KerbApp200
    * Adres: 192.168.20.200

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. Als u een nieuwe groep wilt maken, navigeert u naar **Lokaal verkeer > groepen > groeplijst, klikt u op Maken,** de volgende informatie invullen en klikt u op **Voltooid**.

    * Naam: KerbApp200-Pool
    * Beschrijving: KerbApp200-Pool
    * Statusmonitors: http
    * Adres: 192.168.20.200
    * Servicepoort: 81

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. Als u virtuele server wilt maken, navigeert u naar **Lokaal verkeer > virtuele servers > virtuele serverlijst > +**, vult u de volgende informatie in en klikt u op **Voltooid**.

    * Naam: KerbApp200
    * Bestemmingsadres/masker: host 192.168.30.200
    * Servicepoort: Poort 443 HTTPS
    * Toegangsprofiel: KerbApp200
    * Het toegangsprofiel opgeven dat in vorige stap is gemaakt

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Kerberos-delegatie instellen 

>[!NOTE]
>Klik [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) voor meer informatie

*  **Stap 1:** Een delegatieaccount maken

    **Voorbeeld:**
    * Domeinnaam: **superdemo.live**

    * Sam Account Naam: **big-ipuser**

    * Nieuw-ADUser -Naam "APM Delegatie Account" host/big-ipuser.superdemo.live@superdemo.live -UserPrincipalName -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Ingeschakeld $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Stap 2:** SPN instellen (op het APM-delegatieaccount)

    **Voorbeeld:**
    * setspn -Een **host /big-ipuser.superdemo.live** big-ipuser

* **Stap 3:** SPN-delegatie (voor het App-serviceaccount) Stel de juiste delegatie voor het F5-delegatieaccount op.
    In het onderstaande voorbeeld wordt het APM-delegatieaccount geconfigureerd voor KCD voor FRP-App1.superdemo. live app.

    ![F5 -configuratie (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Geef de details zoals vermeld in het bovenstaande referentiedocument onder [deze](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

### <a name="create-f5-test-user"></a>F5-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in F5. Werk samen met [f5-ondersteuningsteam](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) om de gebruikers toe te voegen aan het F5-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de F5-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de F5 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer F5 met Azure AD](https://aad.portal.azure.com/)

- [F5-aanmelding configureren voor toepassing op basis van kopteksten](headerf5-tutorial.md)

- [F5-aanmelding configureren voor geavanceerde Kerberos-toepassing](advance-kerbf5-tutorial.md)

