---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FortiGate SSL VPN | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met FortiGate SSL VPN

In deze zelfstudie leert u hoe u FortiGate SSL VPN integreert met Azure Active Directory (Azure AD). Wanneer u FortiGate SSL VPN integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot FortiGate SSL VPN.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij FortiGate SSL VPN.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op FortiGate SSL VPN waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FortiGate SSL VPN biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding
* Zodra u FortiGate SSL VPN hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>FortiGate SSL VPN toevoegen vanuit de galerie

Voor het configureren van de integratie van FortiGate SSL VPN met Azure AD moet u FortiGate SSL VPN uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **FortiGate SSL VPN** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **FortiGate SSL VPN** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Eenmalige aanmelding van Azure AD voor FortiGate SSL VPN configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met FortiGate SSL VPN met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FortiGate SSL VPN.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met FortiGate SSL VPN te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor FortiGate SSL VPN configureren](#configure-fortigate-ssl-vpn-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **Een FortiGate SSL VPN-testgebruiker maken**: als u een equivalent van B.Simon in FortiGate SSL VPN wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **FortiGate SSL VPN** het gedeelte **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<FQDN>/remote/login`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<FQDN>/remote/saml/metadata`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://><FQDN/remote/saml/login`

    d. Typ in het tekstvak **Afmeldings-URL** een URL met het volgende patroon: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id, antwoord-URL en afmeldings-URL. Neem contact op met het [ondersteuningsteam van FortiGate SSL VPN](mailto:tac_amer@fortinet.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de FortiGate SSL VPN-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de FortiGate SSL VPN-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | ------------ | --------- |
    | gebruikersnaam | user.userprincipalname |
    | group | user.groups |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In het gedeelte **FortiGate SSL VPN instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot FortiGate SSL VPN.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **FortiGate SSL VPN**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-a-security-group-for-the-test-user"></a>Maak een beveiligingsgroep voor de testgebruiker

In dit gedeelte maakt u een beveiligingsgroep in Azure Active Directory voor de testgebruiker. Deze beveiligingsgroep zal door FortiGate worden gebruikt om de gebruiker netwerktoegang te verlenen via de VPN.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory** en selecteer vervolgens **Groepen**.
1. Selecteer bovenaan het scherm **Nieuwe groep**.
1. Volg in **Eigenschappen van nieuwe groep** de volgende stappen:
   1. Selecteer in het veld **Groepstype** de optie **Beveiliging**.
   1. Voer in het veld **Naam**`FortiGateAccess` in.
   1. In het veld **Groepsbeschrijving** voer `Group for granting FortiGate VPN access` in.
   1. Selecteer **Nee** voor de **Azure AD-rollen kunnen worden toegewezen aan de groep (Preview)** -instellingen.
   1. Selecteer in het veld **Lidmaatschapstype** de optie **Toegewezen**.
   1. Selecteer onder **Leden** de optie **Geen leden geselecteerd**.
   1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
   1. Selecteer **Maken**.
1. Als u eenmaal bent teruggekeerd naar de blade **Groups** in Azure Active Directory, zoekt u de **FortiGate Access**-groep en noteert u het **Object-ID** voor later gebruik.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Eenmalige aanmelding voor FortiGate SSL VPN configureren

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Upload het Base64 SAML-certificaat naar het FortiGate-apparaat

Nadat u de SAML-configuratie van de FortiGate-app in uw tenant heeft voltooid, heeft u het met Base64 gecodeerde SAML-certificaat gedownload. Dit moet worden geüpload naar het FortiGate-apparaat:

1. Log in op het beheerportaal van uw FortiGate-apparaat.
1. Klik op **System** in het menu aan de linkerkant.
1. Klik onder **System** op **Certificates**.
1. Klik op **Importeer** -> **extern certificaat**.
1. Blader naar het certificaat dat u hebt gedownload uit de implementatie van de FortiGate-app in de Azure-tenant, selecteer het certificaat en klik op **OK**.

Nadat het certificaat is geüpload, noteert u de naam onder **Systeem** > **Certificaten** > **Extern certificaat**. Het wordt standaard REMOTE_Cert_ **N** genoemd, waarbij **N** een geheel getal is.

### <a name="perform-fortigate-command-line-configuration"></a>Voer de FortiGate-opdrachtregelconfiguratie uit

Voor de volgende stappen moet de uitlog-URL van Azure zijn geconfigureerd. Deze URL bevat een vraagteken (?). Er zijn speciale stappen nodig om dit teken met succes in te dienen. De stappen kunnen niet worden uitgevoerd vanuit de FortiGate CLI Console. Breng in plaats daarvan een SSH-sessie tot stand met het FortiGate-apparaat met behulp van een tool zoals PuTTY. Als uw FortiGate-apparaat een virtuele machine van Azure is, kunt u de volgende stappen uitvoeren vanaf de seriële console van de virtuele Azure-machine.

Om deze stappen uit te voeren, heeft u de eerder geregistreerde waarden nodig:

- Entiteits-id
- Antwoord-URL
- Afmeldings-URL
- URL voor aanmelden bij Azure
- Azure AD-id
- URL voor afmelden bij Azure
- Base64 SAML-certificaatnaam (REMOTE_Cert_N)

1. Breng een SSH-sessie tot stand met uw FortiGate-apparaat en log in met een FortiGate-beheerdersaccount.
1. Voer de volgende opdrachten uit:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > De **Azure-afmeldings-URL** bevat een `?`-teken. U moet een speciale toetsenreeks invoeren om de URL correct op te geven naar de seriële FortiGate-console. De URL is meestal `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Voer `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` in om de Azure-afmeldings-URL in de seriële console in te voeren
   > 
   > PlSelecteer vervolgens CTRL + V en plak de rest van de URL om de regel te voltooien: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

### <a name="configure-fortigate-for-group-matching"></a>Configureer FortiGate voor het matchen van groepen

In dit gedeelte configureert u FortiGate om de object-id te herkennen van de beveiligingsgroep waarin de testgebruiker zich bevindt. Hierdoor kan FortiGate toegangsbeslissingen nemen op basis van dit groepslidmaatschap.

Om deze stappen uit te voeren, heeft u de object-id nodig van de eerder gemaakte **FortiGateAccess**-beveiligingsgroep

1. Breng een SSH-sessie tot stand op uw FortiGate Appliance en log in met een FortiGate Administrator-account.
1. Voer de volgende opdrachten uit:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Maak FortiGate VPN-portals en firewallbeleid

In dit gedeelte configureert u FortiGate VPN-portals en firewallbeleid die toegang verlenen tot de beveiligingsgroep, **FortiGateAccess** die hierboven is gemaakt.

Werk samen met het  [FortiGate-ondersteuningsteam](mailto:tac_amer@fortinet.com) om de VPN-portals en het firewallbeleid toe te voegen aan het FortiGate VPN-platform. Deze stappen moeten zijn voltooid voordat u eenmalige aanmelding gebruikt.

## <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel FortiGate SSL VPN klikt, wordt u als het goed is automatisch aangemeld bij de instantie van FortiGate SSL VPN waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

Microsoft en FortiGate raden u aan de Fortinet VPN-client, FortiClient, te gebruiken voor de beste eindgebruikerservaring.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [FortiGate SSL VPN proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
