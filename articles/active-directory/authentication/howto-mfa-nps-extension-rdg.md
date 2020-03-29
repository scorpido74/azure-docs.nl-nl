---
title: RDG integreren met Azure MFA NPS-extensie - Azure Active Directory
description: Uw Extern bureaublad-gateway-infrastructuur integreren met Azure MFA met de extensie Netwerkbeleidsserver voor Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d9b2332d6d78e7bde63d0fa3f5b64b588e576b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425450"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Uw Extern bureaublad-gateway-infrastructuur integreren met de NPS-extensie (Network Policy Server) en Azure AD

In dit artikel vindt u informatie over de integratie van uw Extern bureaublad-gateway-infrastructuur met Azure Multi-Factor Authentication (MFA) met behulp van de NPS-extensie (Network Policy Server) voor Microsoft Azure.

Met de NPS-extensie (Network Policy Server) voor Azure kunnen klanten de RADIUS-clientverificatie (Remote Authentication Dial-In User Service) beveiligen met behulp van Azure's [multifactorauthenticatie (MFA) in](multi-factor-authentication.md)de cloud. Deze oplossing biedt verificatie in twee stappen voor het toevoegen van een tweede beveiligingslaag aan aanmeldingen en transacties van gebruikers.

In dit artikel worden stapsgewijze instructies gegeven voor de integratie van de NPS-infrastructuur met Azure MFA met behulp van de NPS-extensie voor Azure. Hierdoor kunnen gebruikers die zich proberen aan te melden bij een Extern bureaublad-gateway veilig verifiëren.

> [!NOTE]
> Dit artikel mag niet worden gebruikt met MFA Server-implementaties en mag alleen worden gebruikt met Azure MFA-implementaties (Cloud-implementaties).

De Network Policy and Access Services (NPS) geeft organisaties de mogelijkheid om het volgende te doen:

* Definieer centrale locaties voor het beheer en de controle van netwerkaanvragen door aan te geven wie verbinding kan maken, welke tijden van dagverbindingen zijn toegestaan, de duur van verbindingen en het beveiligingsniveau dat clients moeten gebruiken om verbinding te maken, enzovoort. In plaats van dit beleid op te geven op elke VPN- of Extern bureaublad-gatewayserver, kunnen deze beleidsregels eenmaal op een centrale locatie worden opgegeven. Het RADIUS-protocol biedt de gecentraliseerde Verificatie, Autorisatie en Boekhouding (AAA).
* Stel nap-clientstatusbeleid (Network Access Protection) op en af te dwingen om te bepalen of apparaten onbeperkte of beperkte toegang tot netwerkbronnen krijgen.
* Bied een middel om verificatie en autorisatie af te dwingen voor toegang tot 802.1x-compatibele draadloze toegangspunten en Ethernet-switches.

Doorgaans gebruiken organisaties NPS (RADIUS) om het beheer van VPN-beleid te vereenvoudigen en te centraliseren. Veel organisaties gebruiken NPS echter ook om het beheer van Rd Desktop Connection Authorization Policies (RD CAPs) te vereenvoudigen en te centraliseren.

Organisaties kunnen NPS ook integreren met Azure MFA om de beveiliging te verbeteren en een hoog niveau van naleving te bieden. Dit helpt ervoor te zorgen dat gebruikers tweestapsverificatie instellen om zich aan te melden bij de Extern bureaublad-gateway. Om gebruikers toegang te geven, moeten ze hun gebruikersnaam/wachtwoordcombinatie opgeven, samen met informatie die de gebruiker in hun controle heeft. Deze informatie moet worden vertrouwd en niet gemakkelijk gedupliceerd, zoals een mobiel telefoonnummer, vast nummer, toepassing op een mobiel apparaat, enzovoort. RDG ondersteunt momenteel telefoongesprekken en pushmeldingen van Microsoft authenticator-appmethoden voor 2FA. Zie de sectie [Bepalen welke verificatiemethoden uw gebruikers kunnen gebruiken](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)voor meer informatie over ondersteunde verificatiemethoden.

Voorafgaand aan de beschikbaarheid van de NPS-extensie voor Azure moesten klanten die tweestapsverificatie voor geïntegreerde NPS- en Azure MFA-omgevingen wilden implementeren, een afzonderlijke MFA-server configureren en onderhouden in de on-premises omgeving zoals gedocumenteerd in [Remote Desktop Gateway en Azure Multi-Factor Authentication Server met RADIUS](howto-mfaserver-nps-rdg.md).

De beschikbaarheid van de NPS-extensie voor Azure geeft organisaties nu de keuze om een on-premises MFA-oplossing of een cloudgebaseerde MFA-oplossing te implementeren om RADIUS-clientverificatie te beveiligen.

## <a name="authentication-flow"></a>Verificatiestroom

Als gebruikers toegang kunnen krijgen tot netwerkbronnen via een Extern bureaublad-gateway, moeten ze voldoen aan de voorwaarden die zijn opgegeven in één Rd-cap (Rd-cap) (Rd-connectieautorisatiebeleid) en één Rd Resource Authorization Policy (Rd RAP). RD CAPs geven aan wie is gemachtigd om verbinding te maken met Extern bureaublad-gateways. RD RAPs geven de netwerkbronnen op, zoals externe bureaubladen of externe apps, waarmee de gebruiker verbinding mag maken via de Extern bureaublad-gateway.

Een Extern bureaublad-gateway kan worden geconfigureerd om een centraal beleidsarchief voor RD CAPs te gebruiken. RD RAPs kunnen geen centraal beleid gebruiken, omdat ze worden verwerkt op de Extern bureaublad-gateway. Een voorbeeld van een Extern bureaublad-gateway die is geconfigureerd om een centraal beleidsarchief voor RD CAPs te gebruiken, is een RADIUS-client naar een andere NPS-server die fungeert als de centrale beleidsopslag.

Wanneer de NPS-extensie voor Azure is geïntegreerd met de NPS en Remote Desktop Gateway, is de succesvolle verificatiestroom als volgt:

1. De Extern bureaublad-gatewayserver ontvangt een verificatieverzoek van een externe desktopgebruiker om verbinding te maken met een bron, zoals een Extern bureaublad-sessie. Als RADIUS-client converteert de Extern bureaublad-gatewayserver het verzoek naar een RADIUS Access-Request-bericht en stuurt het bericht naar de RADIUS-server (NPS) waar de NPS-extensie is geïnstalleerd.
1. De combinatie gebruikersnaam en wachtwoord wordt geverifieerd in Active Directory en de gebruiker wordt geverifieerd.
1. Als aan alle voorwaarden is voldaan zoals opgegeven in de NPS Connection Request en het netwerkbeleid (bijvoorbeeld beperkingen op het tijdstip van de dag of groepslidmaatschap), activeert de NPS-extensie een verzoek voor secundaire verificatie met Azure MFA.
1. Azure MFA communiceert met Azure AD, haalt de gegevens van de gebruiker op en voert de secundaire verificatie uit met ondersteunde methoden.
1. Na het succes van de MFA-uitdaging, communiceert Azure MFA het resultaat naar de NPS-extensie.
1. De NPS-server, waar de extensie is geïnstalleerd, stuurt een RADIUS Access-Accept-bericht voor het RD CAP-beleid naar de Extern bureaublad-gatewayserver.
1. De gebruiker krijgt toegang tot de gevraagde netwerkbron via de Extern bureaublad-gateway.

## <a name="prerequisites"></a>Vereisten

In deze sectie worden de vereiste vereisten beschreven voordat Azure MFA wordt geïntegreerd met de Extern bureaublad-gateway. Voordat u begint, moet u de volgende vereisten hebben.  

* RDS-infrastructuur (Remote Desktop Services)
* Azure MFA-licentie
* Windows Server-software
* Functie Netwerkbeleid en Toegangsdiensten (NPS)
* Azure Active Directory gesynchroniseerd met on-premises Active Directory
* Azure Active Directory GUID-id

### <a name="remote-desktop-services-rds-infrastructure"></a>RDS-infrastructuur (Remote Desktop Services)

U moet over een werkende RDS-infrastructuur (Remote Desktop Services) beschikken. Als u dit niet doet, u deze infrastructuur snel maken in Azure met behulp van de volgende snelstartsjabloon: [Implementatie van extern bureaublad-sessieverzameling maken.](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)

Als u snel een on-premises RDS-infrastructuur wilt maken voor testdoeleinden, voert u de stappen uit om deze te implementeren.
**Meer informatie**: [RdS implementeren met Azure quickstart](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) en [Basic RDS-infrastructuurimplementatie](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Azure MFA-licentie

Vereist is een licentie voor Azure MFA, die beschikbaar is via Azure AD Premium of andere bundels die deze bevatten. Licenties op basis van verbruik voor Azure MFA, zoals per gebruiker of per verificatielicenties, zijn niet compatibel met de NPS-extensie. Zie [Azure Multi-Factor Authentication voor](concept-mfa-licensing.md)meer informatie. Voor testdoeleinden u een proefabonnement gebruiken.

### <a name="windows-server-software"></a>Windows Server-software

De NPS-extensie vereist Windows Server 2008 R2 SP1 of hoger met de NPS-functieservice geïnstalleerd. Alle stappen in deze sectie zijn uitgevoerd met Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Functie Netwerkbeleid en Toegangsdiensten (NPS)

De FUNCTIESERVICE NPS biedt de RADIUS-server- en clientfunctionaliteit en de statusservice Netwerktoegangsbeleid. Deze rol moet worden geïnstalleerd op ten minste twee computers in uw infrastructuur: de Extern bureaublad-gateway en een andere lidserver of domeincontroller. Standaard is de rol al aanwezig op de computer die is geconfigureerd als de Extern bureaublad-gateway.  U moet de NPS-rol ook op ten minste op een andere computer installeren, zoals een domeincontroller of lidserver.

Zie [Een NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx)installeren voor informatie over het installeren van de NPS-functieservice Windows Server 2012 of ouder. Zie [Best Practices voor NPS](https://technet.microsoft.com/library/cc771746)voor een beschrijving van best practices voor NPS, inclusief de aanbeveling om NPS op een domeincontroller te installeren.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory gesynchroniseerd met on-premises Active Directory

Als u de NPS-extensie wilt gebruiken, moeten on-premises gebruikers worden gesynchroniseerd met Azure AD en zijn ingeschakeld voor MFA. In deze sectie wordt ervan uitgegaan dat on-premises gebruikers worden gesynchroniseerd met Azure AD met AD Connect. Zie [Uw on-premises mappen integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor informatie over Azure AD Connect.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-id

Als u de NPS-extensie wilt installeren, moet u de GUID van het Azure AD kennen. Instructies voor het vinden van de GUID van het Azure AD worden hieronder weergegeven.

## <a name="configure-multi-factor-authentication"></a>Meervoudige verificatie configureren

In deze sectie vindt u instructies voor de integratie van Azure MFA met de Extern bureaublad-gateway. Als beheerder moet u de Azure MFA-service configureren voordat gebruikers hun multi-factor apparaten of toepassingen zelf kunnen registreren.

Volg de stappen in [Aan de slag met Azure Multi-Factor Authentication in de cloud](howto-mfa-getstarted.md) om MFA in te schakelen voor uw Azure AD-gebruikers.

### <a name="configure-accounts-for-two-step-verification"></a>Accounts configureren voor verificatie in twee stappen

Zodra een account is ingeschakeld voor MFA, u zich niet aanmelden bij bronnen die onder het MFA-beleid vallen totdat u een vertrouwd apparaat hebt geconfigureerd dat u voor de tweede verificatiefactor gebruiken en geverifieerd met verificatie in twee stappen.

Volg de stappen in [Wat betekent Azure Multi-Factor Authentication voor mij?](../user-help/multi-factor-authentication-end-user.md)

## <a name="install-and-configure-nps-extension"></a>NPS-extensie installeren en configureren

In deze sectie vindt u instructies voor het configureren van de RDS-infrastructuur om Azure MFA te gebruiken voor clientverificatie met de Extern bureaublad-gateway.

### <a name="acquire-azure-active-directory-guid-id"></a>Azure Active Directory GUID-id aanschaffen

Als onderdeel van de configuratie van de NPS-extensie moet u beheerdersreferenties en de Azure AD-id voor uw Azure AD-tenant leveren. In de volgende stappen ziet u hoe u de tenant-id krijgen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als de globale beheerder van de Azure-tenant.
1. Selecteer azure active directory in het menu **Azure Active Directory**of zoek naar En selecteer Azure Active **Directory** op een pagina.
1. Selecteer **Eigenschappen**.
1. Klik in het blad Eigenschappen naast de map-id op het pictogram **Kopiëren,** zoals hieronder wordt weergegeven, om de id naar het klembord te kopiëren.

   ![De Directory-id ophalen via de Azure-portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>De NPS-extensie installeren

Installeer de NPS-extensie op een server waarop de NPS-rol (Network Policy and Access Services) is geïnstalleerd. Dit fungeert als de RADIUS-server voor uw ontwerp.

> [!Important]
> Zorg ervoor dat u de NPS-extensie niet op uw Extern bureaublad-gatewayserver installeert.
>

1. Download de [NPS-extensie.](https://aka.ms/npsmfa)
1. Kopieer het uitvoerbare bestand (NpsExtnForAzureMfaInstaller.exe) naar de NPS-server.
1. Dubbelklik op de NPS-server op **NpsExtnForAzureMfaInstaller.exe**. Klik op **Uitvoeren**als u daarom wordt gevraagd.
1. Bekijk in het dialoogvenster NPS-extensie voor Azure MFA Setup de voorwaarden van de softwarelicentie, controleer of **ik akkoord ga met de licentievoorwaarden**en klik op **Installeren.**
1. Klik in het dialoogvenster NPS-extensie voor Azure MFA Setup op **Sluiten**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Certificaten configureren voor gebruik met de NPS-extensie met behulp van een PowerShell-script

Vervolgens moet u certificaten configureren voor gebruik door de NPS-extensie om veilige communicatie en zekerheid te garanderen. De NPS-componenten bevatten een Windows PowerShell-script dat een zelfondertekend certificaat configureert voor gebruik met NPS.

Het script voert de volgende acties uit:

* Een zelfondertekend certificaat maken
* Koppelt openbare sleutel van certificaat aan serviceprincipal op Azure AD
* Slaat het cert op in de plaatselijke machinewinkel
* Verleent toegang tot de privésleutel van het certificaat aan de netwerkgebruiker
* Opnieuw opstarten van de serverservice netwerkbeleid

Als u uw eigen certificaten wilt gebruiken, moet u de openbare sleutel van uw certificaat koppelen aan de serviceprincipal op Azure AD, enzovoort.

Als u het script wilt gebruiken, geeft u de extensie uw Azure AD-beheerdersreferenties en de Azure AD-tenant-id die u eerder hebt gekopieerd. Voer het script uit op elke NPS-server waar u de NPS-extensie hebt geïnstalleerd. Ga daarna als volgt te werk:

1. Open een systeemvan Windows PowerShell.
1. Typ en druk op `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`ENTER op de **PowerShell-prompt**.
1. Typ `.\AzureMfaNpsExtnConfigSetup.ps1`en druk op **ENTER**. Het script controleert of de Azure Active Directory PowerShell-module is geïnstalleerd. Als deze niet is geïnstalleerd, installeert het script de module voor u.

   ![AzureMfaNpsExtnConfigSetup.ps1 uitvoeren in Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Nadat het script de installatie van de PowerShell-module heeft geverifieerd, wordt het dialoogvenster Azure Active Directory PowerShell-module weergegeven. Voer in het dialoogvenster uw Azure AD-beheerdersreferenties en -wachtwoord in en klik op **Aanmelden**.

   ![Authenticeren naar Azure AD in PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Plak de map-id die u eerder naar het klembord hebt gekopieerd, wanneer u daarom wordt gevraagd en drukt op **ENTER**.

   ![De Directory-id invoeren in PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Het script maakt een zelfondertekend certificaat en voert andere configuratiewijzigingen uit. De uitvoer moet zijn zoals de afbeelding hieronder.

   ![Uitvoer van PowerShell met zelfondertekend certificaat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>NPS-componenten configureren op Extern bureaublad-gateway

In deze sectie configureert u het autorisatiebeleid voor verbinding met extern bureaublad-gateway en andere RADIUS-instellingen.

De verificatiestroom vereist dat RADIUS-berichten worden uitgewisseld tussen de Extern bureaublad-gateway en de NPS-server waar de NPS-extensie is geïnstalleerd. Dit betekent dat u RADIUS-clientinstellingen moet configureren op zowel de Extern bureaublad-gateway als de NPS-server waar de NPS-extensie is geïnstalleerd.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Verbindingsautorisatiebeleid voor Extern bureaublad-gateway configureren om het centrale archief te gebruiken

Extern bureaublad-verbindingsautorisatiebeleid (RD CAPs) geeft de vereisten op voor verbinding maken met een Extern bureaublad-gatewayserver. RD CAPs kunnen lokaal worden opgeslagen (standaard) of ze kunnen worden opgeslagen in een centrale RD CAP-winkel waarop NPS wordt uitgevoerd. Als u de integratie van Azure MFA met RDS wilt configureren, moet u het gebruik van een centrale opslag opgeven.

1. Open **Serverbeheer**op de Extern bureaublad-gatewayserver .
1. Klik in het menu op **Extra,** wijs **Extern bureaublad-services**aan en klik vervolgens op **Extern bureaublad-gatewaybeheer**.
1. Klik in Extern bureaublad-gatewaybeheer met de rechtermuisknop op ** \[Servernaam\] (Lokaal)** en klik op **Eigenschappen**.
1. Selecteer in het dialoogvenster Eigenschappen het tabblad **RD CAP Store.**
1. Selecteer op het tabblad RD CAP Store de optie **Centrale server met NPS**. 
1. Typ **in het veld Een naam of IP-adres invoeren voor de server met NPS** het IP-adres of de servernaam van de server waar u de NPS-extensie hebt geïnstalleerd.

   ![Voer de naam of het IP-adres van uw NPS-server in](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klik op**toevoegen**.
1. Voer in het dialoogvenster **Gedeeld geheim** een gedeeld geheim in en klik op **OK**. Zorg ervoor dat u dit gedeelde geheim opneemt en de record veilig opslaat.

   >[!NOTE]
   >Gedeeld geheim wordt gebruikt om vertrouwen te stellen tussen de RADIUS-servers en -clients. Maak een lang en complex geheim.
   >

   ![Een gedeeld geheim maken om vertrouwen te herstellen](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Klik op **OK** om het dialoogvenster te sluiten.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>RADIUS-time-outwaarde configureren op NPS voor Extern bureaublad-gateway

Om ervoor te zorgen dat er tijd is om de referenties van gebruikers te valideren, verificatie in twee stappen uit te voeren, reacties te ontvangen en te reageren op RADIUS-berichten, is het noodzakelijk om de TIME-outwaarde RADIUS aan te passen.

1. Open Serverbeheer op de Extern bureaublad-gatewayserver. Klik in het menu op **Extra**en klik vervolgens op **Netwerkbeleidsserver**.
1. Vouw **RADIUS-clients en -servers (Local) uit in**de **NPS-console** en selecteer **Extern RADIUS-server**.

   ![Beheerconsole van netwerkbeleidsserver met externe RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Dubbelklik in het detailvenster op **TS-gatewayservergroep**.

   >[!NOTE]
   >Deze RADIUS-servergroep is gemaakt toen u de centrale server voor NPS-beleid configureerde. De Extern bureaublad-gateway stuurt RADIUS-berichten door naar deze server of groep servers, als er meer dan één in de groep is.
   >

1. Selecteer in het dialoogvenster **Eigenschappen van TS-gatewayservergroep** het IP-adres of de naam van de NPS-server die u hebt geconfigureerd om RD CAPs op te slaan en klik vervolgens op **Bewerken**.

   ![Selecteer het IP- of de naam van de eerder geconfigureerde NPS-server](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Selecteer in het dialoogvenster **RADIUS-server bewerken** het tabblad **Taakverdeling.**
1. Wijzig op het tabblad **Taakverdeling** in het **aantal seconden zonder antwoord voordat de aanvraag wordt beschouwd als een veld met een afname,** de standaardwaarde van 3 naar een waarde tussen 30 en 60 seconden.
1. Wijzig in het **aantal seconden tussen aanvragen wanneer de server wordt geïdentificeerd als niet-beschikbaar** veld, de standaardwaarde van 30 seconden in een waarde die gelijk is aan of groter is dan de waarde die u in de vorige stap hebt opgegeven.

   ![Time-outinstellingen van RadiusServer bewerken op het tabblad taakverdeling](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klik twee keer op **OK** om de dialoogvensters te sluiten.

### <a name="verify-connection-request-policies"></a>Beleid voor verbindingsaanvragen verifiëren

Wanneer u de Extern bureaublad-gateway configureert om een centraal beleidsarchief te gebruiken voor verbindingsautorisatiebeleid, is de Extern bureaublad-gateway geconfigureerd om CAP-aanvragen door te sturen naar de NPS-server. De NPS-server met de Azure MFA-extensie is geïnstalleerd en verwerkt de RADIUS-toegangsaanvraag. In de volgende stappen ziet u hoe u het standaardbeleid voor verbindingsaanvragen verifiëren.  

1. Vouw op de Extern bureaublad-gateway in de console NPS (Lokaal) **beleid**uit en selecteer **Beleid voor verbindingsaanvraag**.
1. Dubbelklik op **TS-gatewayautorisatiebeleid**.
1. Klik in het dialoogvenster **Beleidseigenschappen van TS-gatewayautorisatie** beleid op het tabblad **Instellingen.**
1. Klik op het tabblad **Instellingen** onder Verbindingsaanvraag doorsturen op **Verificatie**. RADIUS-client is geconfigureerd om aanvragen voor verificatie door te sturen.

   ![Verificatie-instellingen configureren voor de servergroep](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klik op **Annuleren**.

>[!NOTE]
> Zie het artikel [Verbindingsaanvraagbeleidsdocumentatie configureren](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) voor meer informatie over het maken van een verbindingsaanvraagbeleid. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS configureren op de server waar de NPS-extensie is geïnstalleerd

De NPS-server waar de NPS-extensie is geïnstalleerd, moet RADIUS-berichten kunnen uitwisselen met de NPS-server op de Extern bureaublad-gateway. Als u deze berichtuitwisseling wilt inschakelen, moet u de NPS-componenten configureren op de server waar de NPS-extensieservice is geïnstalleerd.

### <a name="register-server-in-active-directory"></a>Server registreren in Active Directory

Om in dit scenario goed te kunnen functioneren, moet de NPS-server worden geregistreerd in Active Directory.

1. Open **Serverbeheer**op de NPS-server .
1. Klik in Serverbeheer op **Extra**en klik vervolgens op **Netwerkbeleidsserver**.
1. Klik in de console Netwerkbeleidsserver met de rechtermuisknop op **NPS (Lokaal)** en klik vervolgens op **Server registreren in Active Directory**.
1. Klik twee keer op **OK.**

   ![De NPS-server registreren in Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Laat de console open voor de volgende procedure.

### <a name="create-and-configure-radius-client"></a>RADIUS-client maken en configureren

De Extern bureaublad-gateway moet worden geconfigureerd als EEN RADIUS-client voor de NPS-server.

1. Klik op de NPS-server waar de NPS-extensie is geïnstalleerd in de **NPS -console (Local)** met de rechtermuisknop op **RADIUS-clients** en klik op **Nieuw**.

   ![Een nieuwe RADIUS-client maken in de NPS-console](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Geef in het dialoogvenster **Nieuwe RADIUS-client** een vriendelijke naam op, zoals _Gateway_en het IP-adres of de DNS-naam van de Extern bureaublad-gatewayserver.
1. Voer in de velden **Gedeeld geheim** en de **gedeelde geheime** velden hetzelfde geheim in als dat u eerder hebt gebruikt.

   ![Een vriendelijke naam en het IP- of DNS-adres configureren](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klik op **OK** om het dialoogvenster Nieuwe RADIUS-client te sluiten.

### <a name="configure-network-policy"></a>Netwerkbeleid configureren

Bedenk dat de NPS-server met de Azure MFA-extensie het aangewezen centrale beleidsarchief is voor het Beleid voor verbindingsautorisatie (CAP). Daarom moet u een GLB implementeren op de NPS-server om geldige verbindingsaanvragen te autoriseren.  

1. Open op de NPS-server de NPS-console (Lokaal), vouw **Beleid**uit en klik op **Netwerkbeleid**.
1. Klik met de rechtermuisknop op **Verbindingen met andere toegangsservers**en klik op **Beleid dupliceren**.

   ![De verbinding met ander beleid voor toegangsservers dupliceren](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klik met de rechtermuisknop op **Verbindingen kopiëren naar andere toegangsservers**en klik op **Eigenschappen**.
1. Voer in het dialoogvenster **Kopiëren van verbindingen naar andere toegangsservers** in **Beleidsnaam**een geschikte naam in, zoals _RDG_CAP_. Controleer **Beleid ingeschakeld**en selecteer Toegang **verlenen**. Selecteer optioneel in **Type netwerktoegangsserver**de optie **Extern bureaublad-gateway**of u deze als **niet-opgegeven**laten.

   ![Geef het beleid een naam, inschakel en geef toegang](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klik op het tabblad **Beperkingen** en schakel **Clients toestaan verbinding te maken zonder te onderhandelen over een verificatiemethode.**

   ![Verificatiemethoden wijzigen zodat clients verbinding kunnen maken](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Klik eventueel op het tabblad **Voorwaarden** en voeg voorwaarden toe waaraan moet worden voldaan om de verbinding te kunnen toestaan, bijvoorbeeld lidmaatschap van een specifieke Windows-groep.

   ![Optioneel verbindingsvoorwaarden opgeven](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klik op **OK**. Klik op **Nee**wanneer u wordt gevraagd het bijbehorende Help-onderwerp te bekijken.
1. Zorg ervoor dat uw nieuwe beleid bovenaan de lijst staat, dat het beleid is ingeschakeld en dat het toegang verleent.

   ![Uw beleid naar de top van de lijst verplaatsen](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Configuratie verifiëren

Als u de configuratie wilt verifiëren, moet u zich aanmelden bij de Extern bureaublad-gateway met een geschikte RDP-client. Zorg ervoor dat u een account gebruikt dat is toegestaan door uw beleid voor verbindingsautorisatie en is ingeschakeld voor Azure MFA.

Zoals in de onderstaande afbeelding wordt weergegeven, u de pagina **Extern bureaublad-webtoegang** gebruiken.

![Testen in Extern bureaublad-webtoegang](./media/howto-mfa-nps-extension-rdg/image25.png)

Wanneer u uw referenties voor primaire verificatie hebt ingevoerd, wordt in het dialoogvenster Verbinding met extern bureaublad een status weergegeven van Het starten van een externe verbinding, zoals hieronder wordt weergegeven. 

Als u zich met de secundaire verificatiemethode die u eerder in Azure MFA hebt geconfigureerd, hebt geverifieerd, bent u verbonden met de bron. Als de secundaire verificatie echter niet succesvol is, wordt u de toegang tot de bron geweigerd. 

![Verbinding met extern bureaublad die een externe verbinding initieert](./media/howto-mfa-nps-extension-rdg/image26.png)

In het onderstaande voorbeeld wordt de Authenticator-app op een Windows-telefoon gebruikt om de secundaire verificatie te bieden.

![Voorbeeld van de Windows Phone Authenticator-app met verificatie](./media/howto-mfa-nps-extension-rdg/image27.png)

Nadat u met de secundaire verificatiemethode bent geverifieerd, bent u als normaal aangemeld bij de Extern bureaublad-gateway. Omdat u echter een secundaire verificatiemethode moet gebruiken met een mobiele app op een vertrouwd apparaat, is het aanmeldingsproces veiliger dan anders.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Logboeklogboeken weergeven voor geslaagde aanmeldingsgebeurtenissen

Als u de succesvolle aanmeldingsgebeurtenissen in de Logboeken van Windows Logboeken wilt weergeven, u de volgende Opdracht Windows PowerShell uitvoeren om de logboeken Windows Terminal Services en Windows Security op te vragen.

Als u succesvolle aanmeldingsgebeurtenissen wilt opvragen in de operationele logboeken _gateway (Logboeken van logboeken\Logboeken en services\Microsoft\Windows\TerminalServices-Gateway\Operation)_ gebruikt u de volgende PowerShell-opdrachten:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Met deze opdracht worden Windows-gebeurtenissen weergegeven die de gebruiker aande vereisten voor resourceautorisatiebeleid (RD RAP) weergeven en toegang hebben gekregen.

![Gebeurtenissen weergeven met PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Met deze opdracht worden de gebeurtenissen weergegeven die worden weergegeven wanneer de gebruiker voldoet aan de vereisten voor verbindingsautorisatie.

![het verbindingsautorisatiebeleid weergeven met PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

U dit logboek en filter ook bekijken op gebeurtenis-id's, 300 en 200. Als u geslaagde aanmeldingsgebeurtenissen wilt opvragen in de logboeken van de beveiligingsgebeurtenisviewer, gebruikt u de volgende opdracht:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Deze opdracht kan worden uitgevoerd op de centrale NPS of de Extern bureaublad-gatewayserver.

![Voorbeeld van geslaagde aanmeldingsgebeurtenissen](./media/howto-mfa-nps-extension-rdg/image30.png)

U ook het beveiligingslogboek of de aangepaste weergave Netwerkbeleid en Access Services bekijken, zoals hieronder wordt weergegeven:

![Logboeken voor netwerkbeleid en toegangsservices](./media/howto-mfa-nps-extension-rdg/image31.png)

Op de server waar u de NPS-extensie voor Azure MFA hebt geïnstalleerd, vindt u Logboeken die specifiek zijn voor de extensie bij _Toepassings- en serviceslogboeken\Microsoft\AzureMfa_.

![Logboeken AuthZ-toepassingslogboeken](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Problemen met de handleiding oplossen

Als de configuratie niet werkt zoals verwacht, is de eerste plaats om problemen op te lossen om te controleren of de gebruiker is geconfigureerd om Azure MFA te gebruiken. Laat de gebruiker verbinding maken met de [Azure-portal.](https://portal.azure.com) Als gebruikers worden gevraagd om secundaire verificatie en met succes kunnen verifiëren, u een onjuiste configuratie van Azure MFA elimineren.

Als Azure MFA voor de gebruiker(s) werkt, moet u de relevante gebeurtenislogboeken bekijken. Deze omvatten de beveiligingsgebeurtenis, gatewayoperationele en Azure MFA-logboeken die in de vorige sectie worden besproken.

Hieronder vindt u een voorbeeld uitvoer van beveiligingslogboek met een mislukte aanmeldingsgebeurtenis (gebeurtenis-id 6273).

![Voorbeeld van een mislukte aanmeldingsgebeurtenis](./media/howto-mfa-nps-extension-rdg/image33.png)

Hieronder vindt u een gerelateerde gebeurtenis uit de AzureMFA-logboeken:

![Voorbeeld van Azure MFA-logboek in Logboeken](./media/howto-mfa-nps-extension-rdg/image34.png)

Als u geavanceerde probleemoplossingsopties wilt uitvoeren, raadpleegt u de logboekbestanden van de NPS-databaseindeling waarin de NPS-service is geïnstalleerd. Deze logboekbestanden worden gemaakt in de map _%SystemRoot%\System32\Logs_ als door komma's afgebakende tekstbestanden.

Zie [NPS Database Format Log Files interpreteren voor](https://technet.microsoft.com/library/cc771748.aspx)een beschrijving van deze logbestanden. De vermeldingen in deze logboekbestanden kunnen moeilijk te interpreteren zijn zonder ze te importeren in een spreadsheet of database. U verschillende IAS parsers online vinden om u te helpen bij het interpreteren van de logbestanden.

De afbeelding hieronder toont de output van een dergelijke downloadbare [shareware applicatie](https://www.deepsoftware.com/iasviewer).

![Voorbeeld Shareware-app IAS-parser](./media/howto-mfa-nps-extension-rdg/image35.png)

Ten slotte u voor aanvullende probleemoplossingsopties een protocolanalyzer gebruiken, zoals [Microsoft Message Analyzer.](https://technet.microsoft.com/library/jj649776.aspx)

De onderstaande afbeelding van Microsoft Message Analyzer toont netwerkverkeer gefilterd op RADIUS-protocol met de gebruikersnaam **CONTOSO\AliceC**.

![Microsoft Message Analyzer met gefilterd verkeer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Volgende stappen

[Azure Multi-Factor Authentication gebruiken](concept-mfa-licensing.md)

[Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md)

[Uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
