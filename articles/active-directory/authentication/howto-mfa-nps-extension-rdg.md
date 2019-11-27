---
title: RDG integreren met de Azure MFA NPS-extensie-Azure Active Directory
description: Uw extern bureaublad-Gateway-infrastructuur integreren met Azure MFA met behulp van de Network Policy Server-extensie voor Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec402cf2c741d88d230e5734485bf9eb0dd1b03
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381820"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>De infrastructuur van uw extern bureaublad-Gateway met behulp van de extensie voor Network Policy Server (NPS) en Azure AD integreren

Dit artikel bevat informatie voor het integreren van uw infrastructuur voor extern bureaublad-Gateway met Azure multi-factor Authentication (MFA) met behulp van de Network Policy Server (NPS)-extensie voor Microsoft Azure.

Met de uitbrei ding van de Network Policy Server (NPS) voor Azure kunnen klanten de verificatie van Remote Authentication Dial-In User Service (RADIUS)-client beveiligen met behulp van de Cloud [multi-factor Authentication (MFA)](multi-factor-authentication.md)van Azure. Deze oplossing biedt verificatie in twee stappen voor het toevoegen van een tweede beveiligingslaag aan gebruikersaanmeldingen en transacties.

In dit artikel bevat stapsgewijze instructies voor het integreren van de NPS-infrastructuur met Azure MFA met behulp van de NPS-extensie voor Azure. Hierdoor kunnen beveiligde verificatie voor gebruikers die zich aanmelden bij een extern bureaublad-Gateway.

> [!NOTE]
> Dit artikel mag niet worden gebruikt met MFA-server implementaties en mag alleen worden gebruikt met Azure MFA-implementaties (op basis van de Cloud).

De Network Policy and Access Services (NPS) biedt organisaties de mogelijkheid om het volgende te doen:

* Definieer centrale locatie voor het beheer en controle van netwerkaanvragen door op te geven die verbinding kunnen maken, welke tijdstippen van de dag verbindingen zijn toegestaan, de duur van verbindingen en het niveau van beveiliging die clients moeten gebruiken om verbinding te maken, enzovoort. In plaats van op te geven deze beleidsregels op elke server VPN of extern bureaublad (RD)-Gateway, kunnen deze beleidsregels één keer worden opgegeven in een centrale locatie. Het RADIUS-protocol biedt het centrale verificatie, autorisatie en Accounting (AAA).
* Instellen en afdwingen van statusbeleid voor Network Access Protection (NAP) client om te bepalen of apparaten onbeperkte of beperkte toegang krijgen tot netwerkbronnen.
* Een methode om af te dwingen de verificatie en autorisatie voor toegang met 802. 1 x-compatibele draadloze toegangspunten en Ethernet-switches.

Organisaties gebruiken meestal NPS (RADIUS) om het beheer van VPN-beleid te vereenvoudigen en te centraliseren. Veel organisaties worden echter ook de NPS gebruiken om te vereenvoudigen en centraliseer het beheer van extern bureaublad-Sessiehost Desktop Verbindingsautorisatiebeleid (RD CAP's).

Organisaties kunnen ook NPS integreren met Azure MFA voor betere beveiliging en bieden een hoge mate van naleving. Dit zorgt ervoor dat gebruikers verificatie in twee stappen om aan te melden bij de extern bureaublad-Gateway tot stand brengen. Voor gebruikers toegang te krijgen, moet deze de combinatie van gebruikersnaam en wachtwoord, samen met informatie die de gebruiker in hun beheer heeft opgeven. Deze informatie moet worden vertrouwd en worden niet gedupliceerd, zoals een mobiele telefoonnummer, een vast, een toepassing op een mobiel apparaat, enzovoort. RDG ondersteunt momenteel telefoon gesprekken en push meldingen van micro soft Authenticator-app-methoden voor twee ledige. Zie de sectie [bepalen welke verificatie methoden uw gebruikers kunnen gebruiken](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)voor meer informatie over ondersteunde verificatie methoden.

Vóór de beschik baarheid van de NPS-extensie voor Azure moesten klanten die verificatie in twee stappen willen implementeren voor geïntegreerde NPS-en Azure MFA-omgevingen, een afzonderlijke MFA-server configureren en onderhouden in de on-premises omgeving, zoals beschreven in [extern bureaublad-gateway en Azure multi-factor Authentication-server met RADIUS](howto-mfaserver-nps-rdg.md).

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

Vereiste bevindt zich een licentie voor Azure MFA, beschikbaar via Azure AD Premium of andere pakketten die deze bevatten. Licenties op basis van verbruik voor Azure MFA, die aan de hand van gebruiker of per verificatie-licenties zijn niet compatibel met de NPS-extensie. Zie [Azure multi-factor Authentication ophalen](concept-mfa-licensing.md)voor meer informatie. Voor testdoeleinden kunt u een proefabonnement.

### <a name="windows-server-software"></a>Windows Server-software

De NPS-extensie is vereist voor Windows Server 2008 R2 SP1 of hoger met de NPS-functieservice die is geïnstalleerd. De stappen in deze sectie zijn uitgevoerd met behulp van Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rol voor netwerkbeleid en -toegang tot Services (NPS)

De functieservice NPS biedt de RADIUS-server en client-functionaliteit, evenals de health-service voor beleid voor netwerktoegang. Deze functie moet worden geïnstalleerd op ten minste twee computers in uw infrastructuur: de extern bureaublad-Gateway en een lidserver of domeincontroller. De rol is standaard al aanwezig op de computer is geconfigureerd als extern bureaublad-Gateway.  U moet ook installeren de functie NPS op ten minste op een andere computer, zoals een lidserver of domeincontroller.

Zie [een NAP-status beleids server installeren](https://technet.microsoft.com/library/dd296890.aspx)voor meer informatie over het installeren van de NPS-functie service Windows Server 2012 of ouder. Zie [Aanbevolen procedures voor NPS](https://technet.microsoft.com/library/cc771746)voor een beschrijving van best practices voor NPS, met inbegrip van de aanbeveling voor het installeren van NPS op een domein controller.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory worden gesynchroniseerd met on-premises Active Directory

Voor het gebruik van de NPS-extensie, moeten on-premises gebruikers worden gesynchroniseerd met Azure AD en ingeschakeld voor MFA. In deze sectie wordt ervan uitgegaan dat on-premises gebruikers zijn gesynchroniseerd met Azure AD met behulp van AD Connect. Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor meer informatie over Azure AD Connect.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory-GUID-ID

Voor het installeren van NPS-extensie, moet u weten de GUID van de Azure AD. Hieronder vindt u instructies voor het vinden van de GUID van de Azure AD.

## <a name="configure-multi-factor-authentication"></a>Multi-factor Authentication configureren

Deze sectie vindt u instructies voor het integreren van Azure MFA met extern bureaublad-Gateway. Als beheerder, moet u de Azure MFA-service configureren voordat gebruikers zelf hun basis van meerdere factoren apparaten of toepassingen registreren kunnen.

Volg de stappen in aan [de slag met Azure multi-factor Authentication in de Cloud](howto-mfa-getstarted.md) om MFA in te scha kelen voor uw Azure AD-gebruikers.

### <a name="configure-accounts-for-two-step-verification"></a>Accounts configureren voor verificatie in twee stappen

Wanneer een account voor MFA is ingeschakeld, kunt u zich niet aanmelden tot resources beheerst door het MFA-beleid, totdat u een vertrouwd apparaat wilt gebruiken voor de tweede verificatiefactor en hebben geverifieerd met behulp van verificatie in twee stappen hebt geconfigureerd.

Volg de stappen in [Wat heeft Azure multi-factor Authentication voor mij?](../user-help/multi-factor-authentication-end-user.md) om uw apparaten te begrijpen en goed te configureren voor MFA met uw gebruikers account.

## <a name="install-and-configure-nps-extension"></a>Installeren en configureren van NPS-extensie

Deze sectie vindt u instructies voor het configureren van extern bureaublad-services-infrastructuur naar Azure MFA gebruiken voor clientverificatie met extern bureaublad-Gateway.

### <a name="acquire-azure-active-directory-guid-id"></a>ID van Azure Active Directory-GUID verkrijgen

Als onderdeel van de configuratie van de NPS-extensie moet u beheerdersreferenties en de Azure AD-ID opgeven voor uw Azure AD-tenant. De volgende stappen ziet u hoe u aan de tenant-ID.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder van de Azure-Tenant.
1. Selecteer in de linkernavigatiebalk het **Azure Active Directory** pictogram.
1. Selecteer **Eigenschappen**.
1. Klik op de Blade eigenschappen, naast de Directory-ID, op het **kopie** pictogram, zoals hieronder wordt weer gegeven, om de id naar het klem bord te kopiëren.

   ![De Directory-ID ophalen uit de Azure Portal](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>De NPS-extensie installeren

Installeer de NPS-extensie op een server waarop de rol Network Policy and Access Services (NPS) is geïnstalleerd. Dit fungeert als de RADIUS-server voor het ontwerp.

> [!Important]
> Zorg ervoor dat u de NPS-extensie niet installeert op uw server extern bureaublad-Gateway.
>

1. Down load de [NPS-extensie](https://aka.ms/npsmfa).
1. Kopieer het uitvoerbare bestand voor installatie (NpsExtnForAzureMfaInstaller.exe) naar de NPS-server.
1. Dubbel klik op **NpsExtnForAzureMfaInstaller. exe**op de NPS-server. Klik op **uitvoeren**als u hierom wordt gevraagd.
1. Bekijk in het dialoog venster NPS-extensie voor Azure MFA Setup de licentie voorwaarden van de software, Controleer **Ik ga akkoord met de licentie voorwaarden**en klik op **installeren**.
1. Klik in het dialoog venster NPS-extensie voor Azure MFA Setup op **sluiten**.

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
1. Typ `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`bij de Power shell-prompt en druk op **Enter**.
1. Typ `.\AzureMfaNpsExtnConfigSetup.ps1`en druk op **Enter**. Het script wordt gecontroleerd of de Azure Active Directory PowerShell-module is geïnstalleerd. Als niet is geïnstalleerd, installeert u de module met het script voor u.

   ![AzureMfaNpsExtnConfigSetup. ps1 uitvoeren in azure AD Power shell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Nadat het script wordt gecontroleerd door de installatie van de PowerShell-module, wordt deze weergegeven in het dialoogvenster voor Azure Active Directory PowerShell-module. Voer in het dialoog venster uw Azure AD-beheerders referenties en-wacht woord in en klik op **Aanmelden**.

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

1. Open **Serverbeheer**op de RD-gateway-server.
1. Klik in het menu op **extra**, wijs **extern bureaublad-services**aan en klik vervolgens op **extern bureaublad-gatewaybeheer**.
1. Klik in de RD-gatewaybeheer met de rechter muisknop op **\[server naam\] (lokaal)** en klik op **Eigenschappen**.
1. Selecteer in het dialoog venster Eigenschappen het tabblad **RD CAP archief** .
1. Op het tabblad RD CAP archief selecteert u **centrale server waarop NPS wordt uitgevoerd**. 
1. In het veld **Geef een naam of IP-adres op voor de server waarop NPS wordt uitgevoerd** , typt u het IP-adres of de server naam van de server waarop u de NPS-extensie hebt geïnstalleerd.

   ![Voer de naam of het IP-adres van de NPS-server in](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klik op **Toevoegen**.
1. Voer in het dialoog venster **gedeeld geheim** een gedeeld geheim in en klik vervolgens op **OK**. Zorg ervoor dat u dit gedeelde geheim en het veilig opslaan van de record.

   >[!NOTE]
   >Gedeeld geheim wordt gebruikt om de vertrouwensrelatie tussen de RADIUS-servers en clients. Een lange en complexe geheim maken.
   >

   ![Een gedeeld geheim maken om een vertrouwens relatie tot stand te brengen](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Klik op **OK** om het dialoogvenster te sluiten.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>RADIUS-time-outwaarde op extern bureaublad-Gateway NPS configureren

Verificatie in twee stappen uitvoeren om te controleren of er voldoende tijd voor het valideren van referenties van gebruikers is, antwoorden ontvangen en reageren op RADIUS-berichten, is het nodig zijn om aan te passen van de RADIUS-time-outwaarde.

1. Open Serverbeheer op de RD-Gateway-server. Klik in het menu op **extra**en klik vervolgens op **Network Policy Server**.
1. Vouw **RADIUS-clients en-servers**uit in de NPS-console **(lokaal)** en selecteer **externe RADIUS-server**.

   ![Network Policy Server-beheer console met externe RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Dubbel klik in het detail venster op **TS-Gateway server groep**.

   >[!NOTE]
   >Deze RADIUS-Server-groep is gemaakt tijdens het configureren van de centrale server voor NPS-beleid. De RD-Gateway stuurt de RADIUS-berichten op deze server of de groep servers, als meer dan één in de groep.
   >

1. Selecteer in het dialoog venster **Eigenschappen van TS-Gateway server groep** het IP-adres of de naam van de NPS-server die u hebt geconfigureerd voor het opslaan van RD cap's en klik vervolgens op **bewerken**.

   ![Selecteer het IP-adres of de naam van de NPS-server die u eerder hebt geconfigureerd](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Selecteer in het dialoog venster **RADIUS-server bewerken** het tabblad **taak verdeling** .
1. Op het tabblad **taak verdeling** , in het **aantal seconden zonder reactie voordat een aanvraag als een neergezet veld wordt beschouwd** , wijzigt u de standaard waarde van 3 in een waarde tussen 30 en 60 seconden.
1. Wijzig in het **aantal seconden tussen aanvragen wanneer de server wordt geïdentificeerd als niet-beschikbaar** veld de standaard waarde van 30 seconden op een waarde die gelijk is aan of groter is dan de waarde die u in de vorige stap hebt opgegeven.

   ![Instellingen voor de time-out van de RADIUS-server bewerken op het tabblad taak verdeling](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klik twee keer op **OK** om de dialoog vensters te sluiten.

### <a name="verify-connection-request-policies"></a>Beleid voor verbindingsaanvragen controleren

Standaard, dat bij het configureren van de RD-Gateway met een centrale beleidsarchief voor Verbindingsautorisatiebeleid, de RD-Gateway is geconfigureerd voor het doorsturen van KAPJE aanvragen naar de NPS-server. De NPS-server met de Azure MFA-extensie is geïnstalleerd, verwerkt de RADIUS-toegangsaanvraag. De volgende stappen laten zien hoe om te controleren of het standaardbeleid voor verbinding.

1. Vouw op het RD-gateway in de NPS-console (lokaal) **beleid**uit en selecteer **beleid voor verbindings aanvragen**.
1. Dubbel klik op **TS-gateway autorisatie beleid**.
1. Klik in het dialoog venster **Eigenschappen van TS-gateway autorisatie beleid** op het tabblad **instellingen** .
1. Klik op het tabblad **instellingen** onder verbindings aanvraag door sturen op **verificatie**. RADIUS-client is geconfigureerd om aanvragen doorsturen voor verificatie.

   ![Verificatie-instellingen configureren die de Server groep opgeven](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klik op **Annuleren**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS configureren op de server waarop de NPS-extensie is geïnstalleerd

De NPS-server waarop de NPS-extensie is geïnstalleerd moet kunnen zijn voor het uitwisselen van RADIUS-berichten met de NPS-server op de extern bureaublad-Gateway. Om in te schakelen deze uitwisseling van berichten, moet u de NPS-onderdelen configureren op de server waarop de NPS-extensie-service is geïnstalleerd.

### <a name="register-server-in-active-directory"></a>Server registreren in Active Directory

Werkt alleen goed in dit scenario, als moet de NPS-server worden geregistreerd in Active Directory.

1. Open **Serverbeheer**op de NPS-server.
1. Klik in Serverbeheer op **extra**en klik vervolgens op **Network Policy Server**.
1. Klik in de Network Policy Server-console met de rechter muisknop op **NPS (lokaal)** en klik vervolgens op **Server registreren in Active Directory**.
1. Klik twee keer op **OK** .

   ![De NPS-server registreren in Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Laat de console geopend voor de volgende procedure.

### <a name="create-and-configure-radius-client"></a>Maken en configureren van RADIUS-client

Extern bureaublad-Gateway moet worden geconfigureerd als een RADIUS-client naar de NPS-server.

1. Klik met de rechter muisknop op **RADIUS-clients** op de NPS-server waarop de NPS-extensie is geïnstalleerd, in de NPS-console **(lokale)** .

   ![Een nieuwe RADIUS-client maken in de NPS-console](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Geef in het dialoog venster **nieuwe RADIUS-client** een beschrijvende naam op, zoals _Gateway_, en het IP-adres of de DNS-naam van de Extern bureaublad-gateway-server.
1. In het **gedeelde geheim** en de velden **Bevestig het gedeelde geheim** voert u hetzelfde geheim in dat u eerder hebt gebruikt.

   ![Een beschrijvende naam en IP-of DNS-adres configureren](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klik op **OK** om het dialoog venster nieuwe RADIUS-client te sluiten.

### <a name="configure-network-policy"></a>Beleid voor netwerken configureren

Intrekken dat de NPS-server met de Azure MFA-extensie het aangewezen centrale beleidsarchief voor de verbinding autorisatie beleid (CAP is). Daarom moet u voor het implementeren van een LIMIET op de NPS-server om geldige verbindingen aanvragen te autoriseren.  

1. Open op de NPS-server de NPS-console (lokaal), vouw **beleid**uit en klik op **netwerk beleidsregels**.
1. Klik met de rechter muisknop op **verbindingen met andere toegangs servers**en klik op **beleid dupliceren**.

   ![De verbinding met andere toegangs servers beleid dupliceren](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klik met de rechter muisknop op **kopie van verbindingen met andere toegangs servers**en klik op **Eigenschappen**.
1. Voer in het dialoog venster **kopie van verbindingen naar andere toegangs servers** in het vak **beleids naam**een geschikte naam in, zoals _RDG_CAP_. Controleer of **beleid is ingeschakeld**en selecteer **toegang verlenen**. Selecteer eventueel in het **type netwerk toegangs server** **extern bureaublad-gateway**, of u kunt het wijzigen als niet **opgegeven**.

   ![Het beleid een naam geven, toegang inschakelen en verlenen](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klik op het tabblad **beperkingen** en controleer **clients toestaan verbinding te maken zonder te onderhandelen over een verificatie methode**.

   ![Verificatie methoden wijzigen zodat clients verbinding kunnen maken](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Klik eventueel op het tabblad **voor waarden** en voeg voor waarden toe waaraan moet worden voldaan om de verbinding te machtigen, bijvoorbeeld voor lidmaatschap van een specifieke Windows-groep.

   ![Geef desgewenst verbindings voorwaarden op](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klik op **OK**. Wanneer u wordt gevraagd om het bijbehorende Help-onderwerp weer te geven, klikt u op **Nee**.
1. Zorg ervoor dat het nieuwe beleid boven aan de lijst, dat het beleid is ingeschakeld, en dat deze toegang wordt verleend.

   ![Verplaats uw beleid naar de bovenkant van de lijst](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Configuratie controleren

Als u wilt controleren of de configuratie, moet u aanmelden bij de extern bureaublad-Gateway met een geschikt RDP-client. Zorg ervoor dat een account dat is toegestaan door uw Verbindingsautorisatiebeleid en is ingeschakeld voor Azure MFA gebruiken.

Zoals in de onderstaande afbeelding wordt weer gegeven, kunt u de pagina **webtoegang extern bureaublad** gebruiken.

![Testen in Extern bureaublad Web Access](./media/howto-mfa-nps-extension-rdg/image25.png)

In het dialoogvenster Extern bureaublad-verbinding bevat de status van de externe verbinding tot stand brengen, bij is uw referenties invoeren voor primaire verificatie, zoals hieronder wordt weergegeven. 

Als u met de secundaire verificatiemethode die u eerder hebt geconfigureerd in de Azure MFA verifiëren, kunt u bent verbonden met de resource. Als de secundaire verificatie mislukt is, wordt u toegang tot de resource geweigerd. 

![Verbinding met extern bureaublad het starten van een externe verbinding](./media/howto-mfa-nps-extension-rdg/image26.png)

In het onderstaande voorbeeld wordt de Authenticator-app op een Windows Phone-apparaat gebruikt om de secundaire verificatie te bieden.

![Voor beeld Windows Phone Authenticator-app met verificatie](./media/howto-mfa-nps-extension-rdg/image27.png)

Nadat u bent geverifieerd met behulp van de secundaire verificatiemethode, kunt u bent aangemeld bij de extern bureaublad-Gateway als normaal. Omdat u echter een secundaire verificatie methode moet gebruiken met behulp van een mobiele app op een vertrouwd apparaat, is het aanmeldings proces veiliger dan het geval zou zijn.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Logboeken voor geslaagde aanmeldingsgebeurtenissen weergeven

Als u wilt weergeven van de geslaagde aanmeldingsgebeurtenissen in de Windows-Logboeken, kunt u de volgende Windows PowerShell-opdracht om op te vragen de logboeken van Windows Terminal Services en Windows-beveiliging uitgeven.

Gebruik de volgende Power shell-opdrachten voor het uitvoeren van een query op geslaagde aanmeldings gebeurtenissen in de gateway-operationele logboeken _(gebeurtenis Viewer\Applications en services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ :

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

Op de server waarop u de NPS-extensie voor Azure MFA hebt geïnstalleerd, vindt u Logboeken toepassings logboeken die specifiek zijn voor de uitbrei ding op het _Logs\Microsoft\AzureMfa van toepassingen en services_.

![AuthZ-toepassings Logboeken Logboeken](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Problemen oplossen handleiding

Als de configuratie niet werkt zoals verwacht, is de eerste plaats om te starten om op te lossen om te controleren of de gebruiker is geconfigureerd voor het gebruik van Azure MFA. Laat de gebruiker verbinding maken met de [Azure Portal](https://portal.azure.com). Als gebruikers zijn gevraagd voor secundaire verificatie en kunnen worden geverifieerd, kunt u een onjuiste configuratie van Azure MFA elimineren.

Als u Azure MFA is voor de gebruiker (s) werkt, moet u de relevante gebeurtenislogboeken controleren. Hieronder vallen de beveiligingsgebeurtenis, operationele-Gateway en Azure MFA-logboeken die worden beschreven in de vorige sectie.

Hieronder volgt een voorbeeld van uitvoer van het beveiligingslogboek van een mislukte gebeurtenis (gebeurtenis-ID 6273).

![Voor beeld van een mislukte aanmeldings gebeurtenis](./media/howto-mfa-nps-extension-rdg/image33.png)

Hieronder volgt een gerelateerde gebeurtenis vanuit de AzureMFA Logboeken:

![Voor beeld van Azure MFA-logboek in Logboeken](./media/howto-mfa-nps-extension-rdg/image34.png)

Om uit te voeren geavanceerde opties voor problemen, raadpleegt u de NPS-database-indeling logboekbestanden waarop de NPS-service is geïnstalleerd. Deze logboek bestanden worden gemaakt in de map _%systemroot%\System32\Logs_ als tekst bestanden met door komma's gescheiden waarden.

Zie [NPS data base Format-logboek bestanden interpreteren](https://technet.microsoft.com/library/cc771748.aspx)voor een beschrijving van deze logboek bestanden. De vermeldingen in deze logboekbestanden kunnen lastig zijn om te interpreteren zonder ze te importeren in een werkblad of een database. U kunt online verschillende IAS-parsers vinden helpt u bij het interpreteren van de logboekbestanden.

In de onderstaande afbeelding ziet u de uitvoer van een gedownloade [shareware-toepassing](https://www.deepsoftware.com/iasviewer).

![Voor beeld van shareware-app IAS-parser](./media/howto-mfa-nps-extension-rdg/image35.png)

Ten slotte kunt u een protocol analyse gebruiken, zoals [micro soft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx), voor extra probleemoplossings opties.

In de onderstaande afbeelding van micro soft Message Analyzer wordt het netwerk verkeer weer gegeven dat is gefilterd op het RADIUS-protocol dat de gebruikers naam **CONTOSO\AliceC**bevat.

![Micro soft Message Analyzer met gefilterd verkeer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Volgende stappen

[Azure Multi-Factor Authentication gebruiken](concept-mfa-licensing.md)

[Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md)

[Uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
