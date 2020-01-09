---
title: RDG integreren met de Azure MFA NPS-extensie-Azure Active Directory
description: Uw extern bureaublad-Gateway-infrastructuur integreren met Azure MFA met behulp van de Network Policy Server-extensie voor Microsoft Azure
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425450"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>De infrastructuur van uw extern bureaublad-Gateway met behulp van de extensie voor Network Policy Server (NPS) en Azure AD integreren

Dit artikel bevat informatie voor het integreren van uw infrastructuur voor extern bureaublad-Gateway met Azure multi-factor Authentication (MFA) met behulp van de Network Policy Server (NPS)-extensie voor Microsoft Azure.

De Network Policy Server (NPS)-extensie voor Azure klanten kunnen ter bescherming van de Remote Authentication Dial-In User Service (RADIUS)-clientauthenticatie met behulp van Azure cloud-gebaseerde [multi-factor Authentication (MFA)](multi-factor-authentication.md). Deze oplossing biedt verificatie in twee stappen voor het toevoegen van een tweede beveiligingslaag aan gebruikersaanmeldingen en transacties.

In dit artikel bevat stapsgewijze instructies voor het integreren van de NPS-infrastructuur met Azure MFA met behulp van de NPS-extensie voor Azure. Hierdoor kunnen beveiligde verificatie voor gebruikers die zich aanmelden bij een extern bureaublad-Gateway.

> [!NOTE]
> Dit artikel mag niet worden gebruikt met MFA-server implementaties en mag alleen worden gebruikt met Azure MFA-implementaties (op basis van de Cloud).

De Network Policy and Access Services (NPS) biedt organisaties de mogelijkheid om het volgende te doen:

* Definieer centrale locatie voor het beheer en controle van netwerkaanvragen door op te geven die verbinding kunnen maken, welke tijdstippen van de dag verbindingen zijn toegestaan, de duur van verbindingen en het niveau van beveiliging die clients moeten gebruiken om verbinding te maken, enzovoort. In plaats van op te geven deze beleidsregels op elke server VPN of extern bureaublad (RD)-Gateway, kunnen deze beleidsregels één keer worden opgegeven in een centrale locatie. Het RADIUS-protocol biedt het centrale verificatie, autorisatie en Accounting (AAA).
* Instellen en afdwingen van statusbeleid voor Network Access Protection (NAP) client om te bepalen of apparaten onbeperkte of beperkte toegang krijgen tot netwerkbronnen.
* Een methode om af te dwingen de verificatie en autorisatie voor toegang met 802. 1 x-compatibele draadloze toegangspunten en Ethernet-switches.

Organisaties gebruiken meestal NPS (RADIUS) om het beheer van VPN-beleid te vereenvoudigen en te centraliseren. Veel organisaties worden echter ook de NPS gebruiken om te vereenvoudigen en centraliseer het beheer van extern bureaublad-Sessiehost Desktop Verbindingsautorisatiebeleid (RD CAP's).

Organisaties kunnen ook NPS integreren met Azure MFA voor betere beveiliging en bieden een hoge mate van naleving. Dit zorgt ervoor dat gebruikers verificatie in twee stappen om aan te melden bij de extern bureaublad-Gateway tot stand brengen. Voor gebruikers toegang te krijgen, moet deze de combinatie van gebruikersnaam en wachtwoord, samen met informatie die de gebruiker in hun beheer heeft opgeven. Deze informatie moet worden vertrouwd en worden niet gedupliceerd, zoals een mobiele telefoonnummer, een vast, een toepassing op een mobiel apparaat, enzovoort. RDG ondersteunt momenteel telefoon gesprekken en push meldingen van micro soft Authenticator-app-methoden voor twee ledige. Zie de sectie voor meer informatie over ondersteunde verificatiemethoden [bepalen welke verificatiemethoden die uw gebruikers kunnen gebruiken](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Vóór de beschikbaarheid van de NPS-extensie voor Azure moest de klanten die willen verificatie in twee stappen voor geïntegreerde NPS en Azure MFA-omgevingen implementeren om te configureren en onderhouden van een afzonderlijke MFA-Server in de on-premises-omgeving, zoals beschreven in [ Extern bureaublad-Gateway en Azure multi-factor Authentication-Server met behulp van RADIUS](howto-mfaserver-nps-rdg.md).

De beschikbaarheid van de NPS-extensie voor Azure biedt nu organisaties de keuze om te implementeren op een on-premises gebaseerde MFA-oplossing of een MFA-cloud-gebaseerde oplossing voor veilige verificatie van de RADIUS-client.

## <a name="authentication-flow"></a>Verificatie-stroom

Voor gebruikers toegang tot netwerkbronnen via een extern bureaublad-Gateway worden verleend, moeten ze voldoen aan de voorwaarden zijn opgegeven in een extern bureaublad-Verbindingsverificatiebeleid (RD CAP's) en een extern bureaublad-bronautorisatiebeleid (RD RAP). RD CAP's opgeven die is geautoriseerd voor verbinding met extern bureaublad-Gateways. RD RAP's geven de netwerkbronnen, zoals externe bureaubladen of externe apps, die de gebruiker is toegestaan verbinding maken met via de RD-Gateway.

Een extern bureaublad-Gateway kan worden geconfigureerd voor het gebruik van een centrale beleidsarchief voor RD CAP's. RD RAP's niet een centraal beleid gebruiken als ze worden verwerkt op de RD-Gateway. Een voorbeeld van een extern bureaublad-Gateway geconfigureerd voor het gebruik van een centrale beleidsarchief voor RD CAP's is een RADIUS-client naar een andere NPS-server die als de centrale beleid-store fungeert.

Wanneer de NPS-extensie voor Azure is geïntegreerd met de NPS- en extern bureaublad-Gateway, is de stroom is geverifieerd als volgt:

1. De server extern bureaublad-Gateway ontvangt een verificatieaanvraag van een extern bureaublad-gebruiker verbinding maken met een resource, zoals een extern bureaublad-sessie. De extern bureaublad-Gateway-server fungeert als een RADIUS-client, de aanvraag wordt omgezet in een bericht van de RADIUS-aanvraag voor toegang tot en het bericht verzonden naar de RADIUS-(NPS)-server waarop de NPS-extensie is geïnstalleerd.
1. De combinatie van gebruikersnaam en wachtwoord is geverifieerd in Active Directory en de gebruiker wordt geverifieerd.
1. Als voldaan is aan de voorwaarden die zijn opgegeven in de NPS-verbindingsaanvraag en het netwerkbeleid (bijvoorbeeld, de tijd van de dag of groep lidmaatschapsbeperkingen), de NPS-extensie wordt geactiveerd voor een aanvraag voor secundaire verificatie met Azure MFA.
1. Azure MFA communiceert met Azure AD, haalt gegevens op van de gebruiker en de secundaire authenticatie met behulp van ondersteunde methoden wordt uitgevoerd.
1. Azure MFA communiceert bij voltooiing van de MFA-controle, het resultaat dat de NPS-extensie.
1. De NPS-server waarop de extensie is geïnstalleerd, verzendt een bericht RADIUS toegang accepteren voor de RD CAP-beleid met de server extern bureaublad-Gateway.
1. De gebruiker is toegang verleend tot de aangevraagde-resource die via de RD-Gateway.

## <a name="prerequisites"></a>Vereisten

In deze sectie worden de vereisten die nodig zijn voordat de integratie van Azure MFA met extern bureaublad-Gateway. Voordat u begint, moet u de volgende vereisten hebben voldaan.  

* Extern bureaublad-Services (RDS)-infrastructuur
* Azure MFA-licentie
* Windows Server-software
* Rol voor netwerkbeleid en -toegang tot Services (NPS)
* Azure Active Directory worden gesynchroniseerd met on-premises Active Directory
* Azure Active Directory-GUID-ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Extern bureaublad-Services (RDS)-infrastructuur

U moet beschikken over een werkende Remote Desktop Services (RDS)-infrastructuur op locatie. Als dat niet het geval is, kunt u deze infra structuur snel maken in azure met behulp van de volgende Quick Start-sjabloon: [create extern bureaublad sessie verzamelings implementatie](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Als u Maak handmatig een extern bureaublad-services on-premises infrastructuur snel voor testdoeleinden wenst, volg de stappen voor het implementeren van een.
**Meer informatie**: [RDS implementeren met Azure Quick](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) start en [eenvoudige implementatie van RDS-infra structuur](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Azure MFA-licentie

Vereiste bevindt zich een licentie voor Azure MFA, beschikbaar via Azure AD Premium of andere pakketten die deze bevatten. Licenties op basis van verbruik voor Azure MFA, die aan de hand van gebruiker of per verificatie-licenties zijn niet compatibel met de NPS-extensie. Zie voor meer informatie, [over het verkrijgen van Azure multi-factor Authentication](concept-mfa-licensing.md). Voor testdoeleinden kunt u een proefabonnement.

### <a name="windows-server-software"></a>Windows Server-software

De NPS-extensie is vereist voor Windows Server 2008 R2 SP1 of hoger met de NPS-functieservice die is geïnstalleerd. De stappen in deze sectie zijn uitgevoerd met behulp van Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rol voor netwerkbeleid en -toegang tot Services (NPS)

De functieservice NPS biedt de RADIUS-server en client-functionaliteit, evenals de health-service voor beleid voor netwerktoegang. Deze functie moet worden geïnstalleerd op ten minste twee computers in uw infrastructuur: de extern bureaublad-Gateway en een lidserver of domeincontroller. De rol is standaard al aanwezig op de computer is geconfigureerd als extern bureaublad-Gateway.  U moet ook installeren de functie NPS op ten minste op een andere computer, zoals een lidserver of domeincontroller.

Voor informatie over het installeren van de NPS-rolservice service WindowsServer 2012 of ouder, Zie [installeren van een NAP-statusbeleidsserver](https://technet.microsoft.com/library/dd296890.aspx). Zie voor een beschrijving van aanbevolen procedures voor NPS, met inbegrip van de aanbeveling voor het installeren van NPS op een domeincontroller [aanbevolen procedures voor NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory worden gesynchroniseerd met on-premises Active Directory

Voor het gebruik van de NPS-extensie, moeten on-premises gebruikers worden gesynchroniseerd met Azure AD en ingeschakeld voor MFA. In deze sectie wordt ervan uitgegaan dat on-premises gebruikers zijn gesynchroniseerd met Azure AD met behulp van AD Connect. Voor meer informatie over Azure AD connect, Zie [uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory-GUID-ID

Voor het installeren van NPS-extensie, moet u weten de GUID van de Azure AD. Hieronder vindt u instructies voor het vinden van de GUID van de Azure AD.

## <a name="configure-multi-factor-authentication"></a>Multi-factor Authentication configureren

Deze sectie vindt u instructies voor het integreren van Azure MFA met extern bureaublad-Gateway. Als beheerder, moet u de Azure MFA-service configureren voordat gebruikers zelf hun basis van meerdere factoren apparaten of toepassingen registreren kunnen.

Volg de stappen in [aan de slag met Azure multi-factor Authentication in de cloud](howto-mfa-getstarted.md) MFA inschakelen voor uw Azure AD-gebruikers.

### <a name="configure-accounts-for-two-step-verification"></a>Accounts configureren voor verificatie in twee stappen

Wanneer een account voor MFA is ingeschakeld, kunt u zich niet aanmelden tot resources beheerst door het MFA-beleid, totdat u een vertrouwd apparaat wilt gebruiken voor de tweede verificatiefactor en hebben geverifieerd met behulp van verificatie in twee stappen hebt geconfigureerd.

Volg de stappen in [wat Azure multi-factor Authentication betekent voor mij?](../user-help/multi-factor-authentication-end-user.md) om te begrijpen en uw apparaten correct configureren voor MFA met uw gebruikersaccount.

## <a name="install-and-configure-nps-extension"></a>Installeren en configureren van NPS-extensie

Deze sectie vindt u instructies voor het configureren van extern bureaublad-services-infrastructuur naar Azure MFA gebruiken voor clientverificatie met extern bureaublad-Gateway.

### <a name="acquire-azure-active-directory-guid-id"></a>ID van Azure Active Directory-GUID verkrijgen

Als onderdeel van de configuratie van de NPS-extensie moet u beheerdersreferenties en de Azure AD-ID opgeven voor uw Azure AD-tenant. De volgende stappen ziet u hoe u aan de tenant-ID.

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) als globale beheerder van de Azure-tenant.
1. Selecteer in het menu Azure Portal **Azure Active Directory**of zoek en selecteer **Azure Active Directory** op een wille keurige pagina.
1. Selecteer **Eigenschappen**.
1. Klik op de blade eigenschappen naast de map-ID, de **kopie** pictogram, zoals hieronder aangegeven, de ID naar Klembord kopiëren.

   ![De Directory-ID ophalen uit de Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>De NPS-extensie installeren

Installeer de NPS-extensie op een server waarop de rol Network Policy and Access Services (NPS) is geïnstalleerd. Dit fungeert als de RADIUS-server voor het ontwerp.

> [!Important]
> Zorg ervoor dat u de NPS-extensie niet installeert op uw server extern bureaublad-Gateway.
>

1. Download de [NPS-extensie](https://aka.ms/npsmfa).
1. Kopieer het uitvoerbare bestand voor installatie (NpsExtnForAzureMfaInstaller.exe) naar de NPS-server.
1. Dubbelklik op de NPS-server op **NpsExtnForAzureMfaInstaller.exe**. Als u hierom wordt gevraagd, klikt u op **uitvoeren**.
1. In de NPS-extensie voor Azure MFA instellen in het dialoogvenster, controleert u de licentievoorwaarden voor software controleren **ik ga akkoord met de licentievoorwaarden en voorwaarden**, en klikt u op **installeren**.
1. Klik in het dialoogvenster NPS-extensie voor Azure MFA instellen op **sluiten**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configureren van certificaten voor gebruik met de NPS-extensie met een PowerShell-script

Vervolgens moet u certificaten voor gebruik door de NPS-extensie om ervoor te zorgen voor veilige communicatie en controle configureren. De NPS-onderdelen zijn een Windows PowerShell-script dat een zelfondertekend certificaat voor gebruik met NPS configureert.

Het script voert de volgende handelingen uit:

* Hiermee maakt u een zelfondertekend certificaat
* Koppelt de openbare sleutel van het certificaat aan service-principal in Azure AD
* Het certificaat opgeslagen in het archief van de lokale computer
* Verleent toegang tot de persoonlijke sleutel van het certificaat voor de netwerkgebruiker
* Network Policy Server-service wordt opnieuw gestart

Als u wilt dat uw eigen certificaten te gebruiken, moet u het koppelen van de openbare sleutel van uw certificaat om de service-principal in Azure AD, enzovoort.

Voor het gebruik van het script, voorzien van de extensie Azure AD-beheerdersreferenties en de Azure AD-tenant-ID die u eerder hebt gekopieerd. Het script uitvoeren op elke NPS-server waarop u de NPS-extensie geïnstalleerd. Ga daarna als volgt te werk:

1. Open een beheeropdrachtprompt van Windows PowerShell.
1. Typ het volgende achter de PowerShell-prompt `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`, en druk op **ENTER**.
1. Type `.\AzureMfaNpsExtnConfigSetup.ps1`, en druk op **ENTER**. Het script wordt gecontroleerd of de Azure Active Directory PowerShell-module is geïnstalleerd. Als niet is geïnstalleerd, installeert u de module met het script voor u.

   ![AzureMfaNpsExtnConfigSetup. ps1 uitvoeren in azure AD Power shell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Nadat het script wordt gecontroleerd door de installatie van de PowerShell-module, wordt deze weergegeven in het dialoogvenster voor Azure Active Directory PowerShell-module. Voer in het dialoogvenster uw referenties voor Azure AD-beheerder en het wachtwoord in en klikt u op **aanmelden**.

   ![Verifiëren met Azure AD in Power shell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Plak, wanneer u hierom wordt gevraagd, de Directory-ID die u eerder hebt gekopieerd naar het klem bord en druk op **Enter**.

   ![De Directory-ID in Power shell invoeren](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Het script wordt een zelfondertekend certificaat gemaakt en andere wijzigingen in de configuratie wordt uitgevoerd. De uitvoer moet zoals in de afbeelding hieronder wordt weergegeven.

   ![Uitvoer van Power shell met zelfondertekend certificaat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>NPS-onderdelen op de extern bureaublad-Gateway configureren

In deze sectie configureert u de extern bureaublad-Gateway-Verbindingsautorisatiebeleid en andere RADIUS-instellingen.

De verificatie stroom vereist dat RADIUS-berichten worden uitgewisseld tussen de Extern bureaublad-gateway en de NPS-server waarop de NPS-extensie is geïnstalleerd. Dit betekent dat, moet u instellingen voor RADIUS-clients configureren voor zowel extern bureaublad-Gateway en de NPS-server waarop de NPS-extensie is geïnstalleerd.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Extern bureaublad-Gateway-Verbindingsautorisatiebeleid voor het gebruik van centrale opslag configureren

Verbinding met extern bureaublad-autorisatiebeleid (RD CAP's) worden de vereisten voor het verbinden met een extern bureaublad-Gateway-server opgeven. RD CAP's lokaal kunnen worden opgeslagen (standaard) of ze kunnen worden opgeslagen in een centraal RD CAP-archief waarop NPS wordt uitgevoerd. Voor het configureren van integratie van Azure MFA met extern bureaublad-services, moet u het gebruik van een centrale opslag opgeven.

1. Open op de server extern bureaublad-Gateway **Serverbeheer**.
1. In het menu, klikt u op **extra**, wijst u **extern bureaublad-Services**, en klik vervolgens op **extern bureaublad-gatewaybeheer**.
1. In de RD-gatewaybeheer met de rechtermuisknop op  **\[servernaam\] (lokaal)** , en klikt u op **eigenschappen**.
1. Selecteer in het dialoogvenster Eigenschappen van de **RD CAP Store** tabblad.
1. Selecteer op het tabblad RD CAP Store **centrale server waarop NPS wordt uitgevoerd**. 
1. In de **Voer een naam of IP-adres voor de server met NPS** veld, typt u het IP-adres of de naam van de server waarop u de NPS-extensie geïnstalleerd.

   ![Voer de naam of het IP-adres van de NPS-server in](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klik op **Add**.
1. In de **gedeeld geheim** in het dialoogvenster, Geef een gedeeld geheim, en klik vervolgens op **OK**. Zorg ervoor dat u dit gedeelde geheim en het veilig opslaan van de record.

   >[!NOTE]
   >Gedeeld geheim wordt gebruikt om de vertrouwensrelatie tussen de RADIUS-servers en clients. Een lange en complexe geheim maken.
   >

   ![Een gedeeld geheim maken om een vertrouwens relatie tot stand te brengen](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Klik op **OK** om het dialoogvenster te sluiten.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>RADIUS-time-outwaarde op extern bureaublad-Gateway NPS configureren

Verificatie in twee stappen uitvoeren om te controleren of er voldoende tijd voor het valideren van referenties van gebruikers is, antwoorden ontvangen en reageren op RADIUS-berichten, is het nodig zijn om aan te passen van de RADIUS-time-outwaarde.

1. Open Serverbeheer op de RD-Gateway-server. In het menu, klikt u op **extra**, en klik vervolgens op **Network Policy Server**.
1. In de **NPS (lokaal)** console, vouw **RADIUS-Clients en Servers**, en selecteer **externe RADIUS-Server**.

   ![Network Policy Server-beheer console met externe RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Dubbelklik in het deelvenster met details op **TS-GATEWAYSERVERGROEP**.

   >[!NOTE]
   >Deze RADIUS-Server-groep is gemaakt tijdens het configureren van de centrale server voor NPS-beleid. De RD-Gateway stuurt de RADIUS-berichten op deze server of de groep servers, als meer dan één in de groep.
   >

1. In de **groepseigenschappen van TS GATEWAY SERVER** dialoogvenster vak, selecteert u het IP-adres of de naam van de NPS-server die u hebt geconfigureerd voor het opslaan van RD CAP's, en klik vervolgens op **bewerken**.

   ![Selecteer het IP-adres of de naam van de NPS-server die u eerder hebt geconfigureerd](./media/howto-mfa-nps-extension-rdg/image13.png)

1. In de **RADIUS-Server bewerken** in het dialoogvenster, selecteer de **Load Balancing** tabblad.
1. In de **Load Balancing** tabblad, in de **aantal seconden zonder reactie voordat een aanvraag wordt beschouwd als een genegeerde** veld, wijzigt u de standaardwaarde van 3 op een waarde tussen 30 en 60 seconden.
1. In de **aantal seconden tussen aanvragen wanneer de server is geïdentificeerd als niet beschikbaar** veld, de standaardwaarde van 30 seconden wijzigen op een waarde die gelijk is aan of groter is dan de waarde die u hebt opgegeven in de vorige stap.

   ![Instellingen voor de time-out van de RADIUS-server bewerken op het tabblad taak verdeling](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klik op **OK** twee keer om de dialoogvensters te sluiten.

### <a name="verify-connection-request-policies"></a>Beleid voor verbindingsaanvragen controleren

Standaard, dat bij het configureren van de RD-Gateway met een centrale beleidsarchief voor Verbindingsautorisatiebeleid, de RD-Gateway is geconfigureerd voor het doorsturen van KAPJE aanvragen naar de NPS-server. De NPS-server met de Azure MFA-extensie is geïnstalleerd, verwerkt de RADIUS-toegangsaanvraag. De volgende stappen laten zien hoe om te controleren of het standaardbeleid voor verbinding.  

1. Op de RD-Gateway in de NPS (lokaal)-console, vouw **beleid**, en selecteer **beleid voor verbindingsaanvragen**.
1. Dubbelklik op **TS GATEWAY-AUTORISATIEBELEID**.
1. In de **TS GATEWAY-AUTORISATIEBELEID eigenschappen** in het dialoogvenster, klikt u op de **instellingen** tabblad.
1. Op **instellingen** klikt u op tabblad onder de verbindingsaanvraag **verificatie**. RADIUS-client is geconfigureerd om aanvragen doorsturen voor verificatie.

   ![Verificatie-instellingen configureren die de Server groep opgeven](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klik op **annuleren**.

>[!NOTE]
> Voor meer informatie over het maken van een beleid voor verbindings aanvragen raadpleegt u het artikel, Configureer de documentatie voor [beleid voor verbindings aanvragen](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) voor dezelfde. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS configureren op de server waarop de NPS-extensie is geïnstalleerd

De NPS-server waarop de NPS-extensie is geïnstalleerd moet kunnen zijn voor het uitwisselen van RADIUS-berichten met de NPS-server op de extern bureaublad-Gateway. Om in te schakelen deze uitwisseling van berichten, moet u de NPS-onderdelen configureren op de server waarop de NPS-extensie-service is geïnstalleerd.

### <a name="register-server-in-active-directory"></a>Server registreren in Active Directory

Werkt alleen goed in dit scenario, als moet de NPS-server worden geregistreerd in Active Directory.

1. Open op de NPS-server **Serverbeheer**.
1. Klik in Serverbeheer op **extra**, en klik vervolgens op **Network Policy Server**.
1. In de console van Network Policy Server met de rechtermuisknop op **NPS (lokaal)** , en klik vervolgens op **server registreren in Active Directory**.
1. Klik op **OK** twee keer.

   ![De NPS-server registreren in Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Laat de console geopend voor de volgende procedure.

### <a name="create-and-configure-radius-client"></a>Maken en configureren van RADIUS-client

Extern bureaublad-Gateway moet worden geconfigureerd als een RADIUS-client naar de NPS-server.

1. Op de NPS-server waarop de NPS-extensie is geïnstalleerd, in de **NPS (lokaal)** console, met de rechtermuisknop op **RADIUS-Clients** en klikt u op **nieuw**.

   ![Een nieuwe RADIUS-client maken in de NPS-console](./media/howto-mfa-nps-extension-rdg/image17.png)

1. In de **nieuwe RADIUS-Client** dialoogvenster vak, Geef een beschrijvende naam, zoals _Gateway_, en het IP-adres of de DNS-naam van de extern bureaublad-Gateway-server.
1. In de **gedeeld geheim** en de **Bevestig het gedeelde geheim** velden, Voer hetzelfde geheim dat u eerder hebt gebruikt.

   ![Een beschrijvende naam en IP-of DNS-adres configureren](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klik op **OK** te sluiten van het dialoogvenster Nieuwe RADIUS-Client.

### <a name="configure-network-policy"></a>Beleid voor netwerken configureren

Intrekken dat de NPS-server met de Azure MFA-extensie het aangewezen centrale beleidsarchief voor de verbinding autorisatie beleid (CAP is). Daarom moet u voor het implementeren van een LIMIET op de NPS-server om geldige verbindingen aanvragen te autoriseren.  

1. Open de console NPS (lokaal) op de NPS-Server uit, vouw **beleid**, en klikt u op **netwerkbeleid**.
1. Met de rechtermuisknop op **verbindingen met andere servers voor clienttoegang**, en klikt u op **beleid dupliceren**.

   ![De verbinding met andere toegangs servers beleid dupliceren](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Met de rechtermuisknop op **kopie van verbindingen met andere servers voor clienttoegang**, en klikt u op **eigenschappen**.
1. In de **kopie van verbindingen met andere servers voor clienttoegang** in het dialoogvenster **beleidsnaam**, voer een passende naam, zoals _RDG_CAP_. Controleer **beleid is ingeschakeld**, en selecteer **toegang verlenen**. (Optioneel) in **Type netwerktoegangsserver**, selecteer **extern bureaublad-Gateway**, of u kunt dit laten staan als **niet opgegeven**.

   ![Het beleid een naam geven, toegang inschakelen en verlenen](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klik op de **beperkingen** tabblad en Controleer **toestaan dat clients verbinding kunnen maken zonder een verificatiemethode te onderhandelen**.

   ![Verificatie methoden wijzigen zodat clients verbinding kunnen maken](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Klik desgewenst op de **voorwaarden** tabblad en voorwaarden waaraan moeten worden voldaan voor de verbinding moet worden gemachtigd, bijvoorbeeld, het lidmaatschap van een specifieke Windows-groep toevoegen.

   ![Geef desgewenst verbindings voorwaarden op](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klik op **OK**. Wanneer u hierom wordt gevraagd om de bijbehorende Help-onderwerp weer te geven, klikt u op **Nee**.
1. Zorg ervoor dat het nieuwe beleid boven aan de lijst, dat het beleid is ingeschakeld, en dat deze toegang wordt verleend.

   ![Verplaats uw beleid naar de bovenkant van de lijst](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Configuratie controleren

Als u wilt controleren of de configuratie, moet u aanmelden bij de extern bureaublad-Gateway met een geschikt RDP-client. Zorg ervoor dat een account dat is toegestaan door uw Verbindingsautorisatiebeleid en is ingeschakeld voor Azure MFA gebruiken.

Als de weergeven in de onderstaande afbeelding, kunt u de **Remote Desktop Web Access** pagina.

![Testen in Extern bureaublad Web Access](./media/howto-mfa-nps-extension-rdg/image25.png)

In het dialoogvenster Extern bureaublad-verbinding bevat de status van de externe verbinding tot stand brengen, bij is uw referenties invoeren voor primaire verificatie, zoals hieronder wordt weergegeven. 

Als u met de secundaire verificatiemethode die u eerder hebt geconfigureerd in de Azure MFA verifiëren, kunt u bent verbonden met de resource. Als de secundaire verificatie mislukt is, wordt u toegang tot de resource geweigerd. 

![Verbinding met extern bureaublad het starten van een externe verbinding](./media/howto-mfa-nps-extension-rdg/image26.png)

In het onderstaande voorbeeld wordt de Authenticator-app op een Windows Phone-apparaat gebruikt om de secundaire verificatie te bieden.

![Voor beeld Windows Phone Authenticator-app met verificatie](./media/howto-mfa-nps-extension-rdg/image27.png)

Nadat u bent geverifieerd met behulp van de secundaire verificatiemethode, kunt u bent aangemeld bij de extern bureaublad-Gateway als normaal. Omdat u echter een secundaire verificatie methode moet gebruiken met behulp van een mobiele app op een vertrouwd apparaat, is het aanmeldings proces veiliger dan het geval zou zijn.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Logboeken voor geslaagde aanmeldingsgebeurtenissen weergeven

Als u wilt weergeven van de geslaagde aanmeldingsgebeurtenissen in de Windows-Logboeken, kunt u de volgende Windows PowerShell-opdracht om op te vragen de logboeken van Windows Terminal Services en Windows-beveiliging uitgeven.

Query uitvoeren op gebeurtenissen voor geslaagde aanmelding in de operationele logboeken van de Gateway _(gebeurtenis Viewer\Applications en Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ , gebruikt u de volgende PowerShell-opdrachten:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Met deze opdracht bevat de Windows-gebeurtenissen waarin de gebruiker aan de resource autorisatie beleid vereisten voldoet (RD RAP) en heeft toegang gekregen.

![Gebeurtenissen weer geven met Power shell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Deze opdracht worden de gebeurtenissen die worden weergegeven wanneer de gebruiker verbinding autorisatievereisten voldaan.

![het verbindings autorisatie beleid weer geven met behulp van Power shell](./media/howto-mfa-nps-extension-rdg/image29.png)

U kunt ook dit logboek en filter op gebeurtenis-id's, 300 en 200 weergeven. Om te vragen van geslaagde aanmeldingsgebeurtenissen in de logboeken voor beveiliging, gebruik de volgende opdracht:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Met deze opdracht kan worden uitgevoerd op de centrale NPS of de extern bureaublad-gatewayserver.

![Voor beeld van geslaagde aanmeldings gebeurtenissen](./media/howto-mfa-nps-extension-rdg/image30.png)

U kunt ook het beveiligingslogboek of de Network Policy and Access Services aangepaste weergave, bekijken, zoals hieronder wordt weergegeven:

![Services voor netwerk beleid en-toegang Logboeken](./media/howto-mfa-nps-extension-rdg/image31.png)

Op de server waarop u de NPS-extensie voor Azure MFA hebt geïnstalleerd, vindt u logboeken van de toepassing Logboeken specifiek zijn voor de extensie op _toepassingen en Services Logs\Microsoft\AzureMfa_.

![AuthZ-toepassings Logboeken Logboeken](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Problemen oplossen handleiding

Als de configuratie niet werkt zoals verwacht, is de eerste plaats om te starten om op te lossen om te controleren of de gebruiker is geconfigureerd voor het gebruik van Azure MFA. Laat de gebruiker die verbinding maken met de [Azure-portal](https://portal.azure.com). Als gebruikers zijn gevraagd voor secundaire verificatie en kunnen worden geverifieerd, kunt u een onjuiste configuratie van Azure MFA elimineren.

Als u Azure MFA is voor de gebruiker (s) werkt, moet u de relevante gebeurtenislogboeken controleren. Hieronder vallen de beveiligingsgebeurtenis, operationele-Gateway en Azure MFA-logboeken die worden beschreven in de vorige sectie.

Hieronder volgt een voorbeeld van uitvoer van het beveiligingslogboek van een mislukte gebeurtenis (gebeurtenis-ID 6273).

![Voor beeld van een mislukte aanmeldings gebeurtenis](./media/howto-mfa-nps-extension-rdg/image33.png)

Hieronder volgt een gerelateerde gebeurtenis vanuit de AzureMFA Logboeken:

![Voor beeld van Azure MFA-logboek in Logboeken](./media/howto-mfa-nps-extension-rdg/image34.png)

Om uit te voeren geavanceerde opties voor problemen, raadpleegt u de NPS-database-indeling logboekbestanden waarop de NPS-service is geïnstalleerd. Deze logboekbestanden worden gemaakt in _%SystemRoot%\System32\Logs_ map als bestanden met door komma's gescheiden tekst.

Voor een beschrijving van deze logboekbestanden, Zie [interpreteren NPS indeling databaselogboekbestanden](https://technet.microsoft.com/library/cc771748.aspx). De vermeldingen in deze logboekbestanden kunnen lastig zijn om te interpreteren zonder ze te importeren in een werkblad of een database. U kunt online verschillende IAS-parsers vinden helpt u bij het interpreteren van de logboekbestanden.

De onderstaande afbeelding ziet u de uitvoer van een dergelijke downloadbare [shareware toepassing](https://www.deepsoftware.com/iasviewer).

![Voor beeld van shareware-app IAS-parser](./media/howto-mfa-nps-extension-rdg/image35.png)

Ten slotte voor extra opties oplossen, kunt u een programma voor protocolanalyse, dergelijke [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

In de afbeelding hieronder van Microsoft Message Analyzer netwerkverkeer gefilterd op RADIUS-protocol met de naam van de gebruiker ziet **CONTOSO\AliceC**.

![Micro soft Message Analyzer met gefilterd verkeer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Volgende stappen

[Azure Multi-Factor Authentication gebruiken](concept-mfa-licensing.md)

[Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md)

[Uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
