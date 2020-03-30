---
title: VPN met Azure MFA met de NPS-extensie - Azure Active Directory
description: Integreer uw VPN-infrastructuur met Azure MFA met de extensie Netwerkbeleidsserver voor Microsoft Azure.
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
ms.openlocfilehash: f446f1549b3efcd5f27752fac972dfd80c8650d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425414"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Uw VPN-infrastructuur integreren met Azure MFA met de extensie Netwerkbeleidsserver voor Azure

Met de NPS-extensie (Network Policy Server) voor Azure kunnen organisaties clientverificatie (Remote Authentication Dial-In User Service) (RADIUS) beveiligen met behulp van [cloudgebaseerde Azure Multi-Factor Authentication (MFA),](howto-mfaserver-nps-rdg.md)die verificatie in twee stappen biedt.

In dit artikel vindt u instructies voor het integreren van de NPS-infrastructuur met MFA met behulp van de NPS-extensie voor Azure. Dit proces maakt veilige verificatie in twee stappen mogelijk voor gebruikers die via een VPN verbinding proberen te maken met uw netwerk.

Netwerkbeleid en toegangsservices bieden organisaties de mogelijkheid om:

* Wijs een centrale locatie toe voor het beheer en beheer van netwerkaanvragen om op te geven:

  * Wie kan verbinding maken

  * Op welke tijdstippen van de dag zijn verbindingen toegestaan

  * De duur van de verbindingen

  * Het beveiligingsniveau dat clients moeten gebruiken om verbinding te maken

    In plaats van beleid op te geven op elke VPN- of Extern bureaublad-gatewayserver, doet u dit nadat ze zich op een centrale locatie bevinden. Het RADIUS-protocol wordt gebruikt om gecentraliseerde verificatie, autorisatie en boekhouding (AAA) te bieden.

* Stel nap-clientstatusbeleid (Network Access Protection) op en af te dwingen om te bepalen of apparaten onbeperkte of beperkte toegang tot netwerkbronnen krijgen.

* Bied een manier om verificatie en autorisatie af te dwingen voor toegang tot 802.1x-compatibele draadloze toegangspunten en Ethernet-switches.
  Zie [Netwerkbeleidsserver](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)voor meer informatie .

Om de beveiliging te verbeteren en een hoog niveau van naleving te bieden, kunnen organisaties NPS integreren met Azure Multi-Factor Authentication om ervoor te zorgen dat gebruikers verificatie in twee stappen gebruiken om verbinding te maken met de virtuele poort op de VPN-server. Om gebruikers toegang te geven, moeten ze hun gebruikersnaam en wachtwoordcombinatie en andere informatie die ze beheren, opgeven. Deze informatie moet worden vertrouwd en niet gemakkelijk gedupliceerd. Het kan een mobiel telefoonnummer, een vast nummer of een toepassing op een mobiel apparaat bevatten.

Voorafgaand aan de beschikbaarheid van de NPS-extensie voor Azure, moesten klanten die tweestapsverificatie voor geïntegreerde NPS- en MFA-omgevingen wilden implementeren, een aparte MFA-server configureren en onderhouden in een on-premises omgeving. Dit type verificatie wordt aangeboden door Remote Desktop Gateway en Azure Multi-Factor Authentication Server met RADIUS.

Met de NPS-extensie voor Azure kunnen organisaties RADIUS-clientverificatie beveiligen door een on-premises MFA-oplossing of een cloudgebaseerde MFA-oplossing te implementeren.

## <a name="authentication-flow"></a>Verificatiestroom

Wanneer gebruikers verbinding maken met een virtuele poort op een VPN-server, moeten ze zich eerst verifiëren met behulp van verschillende protocollen. De protocollen maken het gebruik van een combinatie van gebruikersnaam en wachtwoord en op certificaten gebaseerde verificatiemethoden mogelijk.

Naast het verifiëren en verifiëren van hun identiteit, moeten gebruikers beschikken over de juiste inbelmachtigingen. In eenvoudige implementaties worden inbelmachtigingen die toegang bieden, rechtstreeks ingesteld op de Active Directory-gebruikersobjecten.

![Tabblad Inbelknopinen in de gebruikerseigenschappen van Active Directory: gebruikers en computers](./media/howto-mfa-nps-extension-vpn/image1.png)

In eenvoudige implementaties verleent of ontzegt elke VPN-server toegang op basis van beleid dat is gedefinieerd op elke lokale VPN-server.

In grotere en meer schaalbare implementaties worden het beleid dat VPN-toegang verleent of weigert, gecentraliseerd op RADIUS-servers. In deze gevallen fungeert de VPN-server als een access server (RADIUS-client) die verbindingsverzoeken en accountberichten doorstuurt naar een RADIUS-server. Om verbinding te maken met de virtuele poort op de VPN-server, moeten gebruikers worden geverifieerd en voldoen aan de voorwaarden die centraal zijn gedefinieerd op RADIUS-servers.

Wanneer de NPS-extensie voor Azure is geïntegreerd met de NPS, ontstaat als volgt een succesvolle verificatiestroom:

1. De VPN-server ontvangt een verificatieverzoek van een VPN-gebruiker met de gebruikersnaam en het wachtwoord voor verbinding maken met een bron, zoals een Extern bureaublad-sessie.
2. Als RADIUS-client zet de VPN-server het verzoek om naar een RADIUS *Access-Request-bericht* en stuurt het (met een versleuteld wachtwoord) naar de RADIUS-server waar de NPS-extensie is geïnstalleerd.
3. De combinatie gebruikersnaam en wachtwoord wordt geverifieerd in Active Directory. Als de gebruikersnaam of het wachtwoord onjuist is, stuurt de *RADIUS-server een bericht Access-Reject.*
4. Als aan alle voorwaarden is voldaan, zoals gespecificeerd in de NPS Connection Request en Network Policies, (bijvoorbeeld beperkingen voor het tijdstip van de dag of groepslidmaatschap), activeert de NPS-extensie een verzoek voor secundaire verificatie met Azure Multi-Factor Authentication.
5. Azure Multi-Factor Authentication communiceert met Azure Active Directory, haalt de gegevens van de gebruiker op en voert de secundaire verificatie uit met behulp van de methode die is geconfigureerd door de gebruiker (mobiele telefoon, sms of mobiele app).
6. Wanneer de MFA-uitdaging succesvol is, communiceert Azure Multi-Factor Authentication het resultaat naar de NPS-extensie.
7. Nadat de verbindingspoging zowel is geverifieerd als geautoriseerd, stuurt de NPS waar de extensie is geïnstalleerd een RADIUS *Access-Accept-bericht* naar de VPN-server (RADIUS-client).
8. De gebruiker krijgt toegang tot de virtuele poort op de VPN-server en stelt een versleutelde VPN-tunnel in.

## <a name="prerequisites"></a>Vereisten

In deze sectie worden de vereisten beschreven die moeten worden ingevuld voordat u MFA integreren met de VPN. Voordat u begint, moet u de volgende vereisten hebben:

* VPN-infrastructuur
* Functie Netwerkbeleid en Toegangsservices
* Azure Multi-Factor Authentication-licentie
* Windows Server-software
* Bibliotheken
* Azure Active Directory (Azure AD) gesynchroniseerd met on-premises Active Directory
* Azure Active Directory GUID-id

### <a name="vpn-infrastructure"></a>VPN-infrastructuur

In dit artikel wordt ervan uitgegaan dat u een werkende VPN-infrastructuur hebt die Microsoft Windows Server 2016 gebruikt en dat uw VPN-server momenteel niet is geconfigureerd om verbindingsaanvragen door te sturen naar een RADIUS-server. In het artikel configureert u de VPN-infrastructuur om een centrale RADIUS-server te gebruiken.

Als u niet beschikt over een werkende VPN-infrastructuur, u er snel een maken door de richtlijnen te volgen in tal van VPN-installatietutorials die u vinden op de sites van Microsoft en derden.

### <a name="the-network-policy-and-access-services-role"></a>De rol Netwerkbeleid en Toegangsdiensten

Netwerkbeleid en Toegangsservices bieden de RADIUS-server en clientfunctionaliteit. In dit artikel wordt ervan uitgegaan dat u de rol Netwerkbeleid en Toegangsservices hebt geïnstalleerd op een lidserver of domeincontroller in uw omgeving. In deze handleiding configureert u RADIUS voor een VPN-configuratie. Installeer de rol Netwerkbeleid en Toegangsservices op een andere server *dan* uw VPN-server.

Zie [Een NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx)installeren voor informatie over het installeren van de functieservice Netwerkbeleid en Access Services windows server 2012 of hoger. NAP is afgeschaft in Windows Server 2016. Zie [Best practices voor NPS](https://technet.microsoft.com/library/cc771746)voor een beschrijving van best practices voor NPS, inclusief de aanbeveling om NPS op een domeincontroller te installeren.

### <a name="azure-mfa-license"></a>Azure MFA-licentie

Er is een licentie vereist voor Azure Multi-Factor Authentication en is beschikbaar via een Azure AD Premium, Enterprise Mobility + Security of een stand-alone licentie voor multi-factorauthenticatie. Licenties op basis van verbruik voor Azure MFA, zoals per gebruiker of per verificatielicenties, zijn niet compatibel met de NPS-extensie. Zie [Azure Multi-Factor Authentication voor](concept-mfa-licensing.md)meer informatie. Voor testdoeleinden u een proefabonnement gebruiken.

### <a name="windows-server-software"></a>Windows Server-software

De NPS-extensie vereist Windows Server 2008 R2 SP1 of hoger, waarbij de rol Netwerkbeleid en Access Services is geïnstalleerd. Alle stappen in deze handleiding zijn uitgevoerd met Windows Server 2016.

### <a name="libraries"></a>Bibliotheken

De volgende bibliotheken worden automatisch geïnstalleerd met de NPS-extensie:

-   [Visual C++ Verredbare pakketten voor Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory Module voor Windows PowerShell versie 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Als de Microsoft Azure Active Directory PowerShell-module nog niet aanwezig is, wordt deze geïnstalleerd met een configuratiescript dat u uitvoert als onderdeel van het installatieproces. Het is niet nodig om de module van tevoren te installeren als deze nog niet is geïnstalleerd.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory gesynchroniseerd met on-premises Active Directory

Als u de NPS-extensie wilt gebruiken, moeten on-premises gebruikers worden gesynchroniseerd met Azure Active Directory en zijn ingeschakeld voor MFA. In deze handleiding wordt ervan uitgegaan dat on-premises gebruikers worden gesynchroniseerd met Azure Active Directory via Azure AD Connect. Instructies voor het inschakelen van gebruikers voor MFA zijn hieronder weergegeven.

Zie [Uw on-premises mappen integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor informatie over Azure AD Connect.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-id

Als u de NPS-extensie wilt installeren, moet u de GUID van de Azure Active Directory kennen. Instructies voor het vinden van de GUID van de Azure Active Directory worden in de volgende sectie gegeven.

## <a name="configure-radius-for-vpn-connections"></a>RADIUS configureren voor VPN-verbindingen

Als u de NPS-rol op een lidserver hebt geïnstalleerd, moet u deze configureren om de VPN-client die VPN-verbindingen aanvraagt, te verifiëren en te autoriseren. 

In deze sectie wordt ervan uitgegaan dat u de rol Netwerkbeleid en Toegangsservices hebt geïnstalleerd, maar deze niet hebt geconfigureerd voor gebruik in uw infrastructuur.

> [!NOTE]
> Als u al een werkende VPN-server hebt die een gecentraliseerde RADIUS-server gebruikt voor verificatie, u deze sectie overslaan.
>

### <a name="register-server-in-active-directory"></a>Server registreren in Active Directory

Om in dit scenario goed te kunnen functioneren, moet de NPS-server zijn geregistreerd in Active Directory.

1. Serverbeheer openen.

2. Selecteer in Serverbeheer **Extra**en selecteer **vervolgens Netwerkbeleidsserver**.

3. Klik in de console Netwerkbeleidsserver met de rechtermuisknop op **NPS (Lokaal)** en selecteer **Vervolgens Server registreren in Active Directory**. Selecteer **OK** twee keer.

    ![Server registreren in de menuoptie Active Directory](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Laat de console open voor de volgende procedure.

### <a name="use-wizard-to-configure-the-radius-server"></a>Wizard gebruiken om de RADIUS-server te configureren

U een standaardoptie (wizard gebaseerd) of geavanceerde configuratieoptie gebruiken om de RADIUS-server te configureren. In deze sectie wordt ervan uitgegaan dat u de op wizard gebaseerde standaardconfiguratieoptie gebruikt.

1. Selecteer **NPS (Lokaal)** in de console Netwerkbeleidsserver .

2. Selecteer **onder Standaardconfiguratie**DE OPTIE **RADIUS-server voor inbel- of VPN-verbindingen**en selecteer **vervolgens VPN of Inbelverbinding configureren.**

    ![RADIUS-server configureren voor inbel- of VPN-verbindingen](./media/howto-mfa-nps-extension-vpn/image3.png)

3. Selecteer in het venster **Inbel- of Type virtuele privénetwerkverbindingen** selecteren de optie **Virtuele privénetwerkverbindingen**en selecteer **Volgende**.

    ![Virtuele privénetwerkverbindingen configureren](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Selecteer In het venster **Inbel- of VPN-server opgeven** de optie **Toevoegen**.

5. Geef in het venster **Nieuwe RADIUS-client** een vriendelijke naam op, voer de oplosbare naam of het IP-adres van de VPN-server in en voer vervolgens een gedeeld geheim wachtwoord in. Maak het gedeelde geheime wachtwoord lang en complex. Neem het op, want je hebt het nodig in de volgende sectie.

    ![Een nieuw clientvenster RADIUS maken](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Selecteer **OK**en selecteer **Volgende**.

7. Accepteer in het venster **Verificatiemethoden configureren** de standaardselectie **(Microsoft Versleutelde Verificatie versie 2 [MS-CHAPv2])** of kies een andere optie en selecteer **Volgende**.

    > [!NOTE]
    > Als u Extensible Authentication Protocol (EAP) configureert, moet u het Microsoft Challenge-Handshake Authentication Protocol (CHAPv2) of Protected Extensible Authentication Protocol (PEAP) gebruiken. Geen enkel ander EAP wordt ondersteund.

8. Selecteer **Toevoegen**en selecteer vervolgens een geschikte groep in het venster **Gebruikersgroepen opgeven.** Als er geen groep bestaat, laat u de selectie leeg om toegang te verlenen aan alle gebruikers.

    ![Het venster Gebruikersgroepen opgeven om toegang toe te staan of te weigeren](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Selecteer **Volgende**.

10. Selecteer **Volgende**in het venster **IP-filters opgeven** .

11. Accepteer in het venster **Versleutelingsinstellingen opgeven** de standaardinstellingen en selecteer **Volgende**.

    ![Het venster Versleutelingsinstellingen opgeven](./media/howto-mfa-nps-extension-vpn/image8.png)

12. Laat in het venster **Realmname opgeven de** naam van de realm leeg, accepteer de standaardinstelling en selecteer **Volgende**.

    ![Het venster Een realmnaam opgeven](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Selecteer In het venster **Nieuwe inbelverbinding of Virtuele privénetwerkverbindingen en RADIUS-clients** voltooien de optie **Voltooien .**

    ![Voltooid configuratievenster](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>De RADIUS-configuratie controleren

In deze sectie wordt de configuratie beschreven die u hebt gemaakt met behulp van de wizard.

1. Vouw op de netwerkbeleidsserver in de NPS-console (lokale) **STRAAL-clients**uit en selecteer **vervolgens RADIUS-clients**.

2. Klik in het detailvenster met de rechtermuisknop op de RADIUS-client die u hebt gemaakt en selecteer **Eigenschappen**. De eigenschappen van uw RADIUS-client (de VPN-server) moeten zijn zoals die hier worden weergegeven:

    ![Controleer de VPN-eigenschappen en -configuratie](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Selecteer **Annuleren**.

4. Vouw op de netwerkbeleidsserver in de NPS-console (lokale) **beleid**uit en selecteer **vervolgens Beleid voor verbindingsaanvraag**. Het beleid vpn-verbindingen wordt weergegeven zoals weergegeven in de volgende afbeelding:

    ![Beleid voor verbindingsaanvraag met VPN-verbindingsbeleid](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Selecteer **netwerkbeleid**onder **Beleid**. U ziet een VPN-beleid (Virtual Private Network) dat lijkt op het beleid in de volgende afbeelding:

    ![Netwerkbeleid met beleid voor virtuele privénetwerkverbindingen](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Uw VPN-server configureren om RADIUS-verificatie te gebruiken

In deze sectie configureert u uw VPN-server om RADIUS-verificatie te gebruiken. De instructies gaan ervan uit dat u een werkende configuratie van een VPN-server hebt, maar deze niet hebt geconfigureerd om RADIUS-verificatie te gebruiken. Nadat u de VPN-server hebt geconfigureerd, bevestigt u dat uw configuratie werkt zoals verwacht.

> [!NOTE]
> Als u al een werkende VPN-serverconfiguratie hebt die RADIUS-verificatie gebruikt, u deze sectie overslaan.
>

### <a name="configure-authentication-provider"></a>Verificatieprovider configureren

1. Open Serverbeheer op de VPN-server.

2. Selecteer in Serverbeheer **Extra**en selecteer **Vervolgens Routering en Rastoegang**.

3. Klik in het venster **Routering en Rastoegang** met de rechtermuisknop op ** \<de servernaam> (lokaal)** en selecteer **Vervolgens Eigenschappen**.

4. Selecteer in het **Security** ** \<venster servernaam> (lokaal) eigenschappen,** het tabblad Beveiliging.

5. Selecteer op het tabblad **Beveiliging** onder **Verificatieprovider**de optie **RADIUS-verificatie**en selecteer **Vervolgens Configureren**.

    ![RADIUS-verificatieprovider configureren](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Selecteer in het venster **RADIUS-verificatie** de optie **Toevoegen**.

7. Ga als volgt te werk in het venster **RADIUS-server toevoegen:**

    a. Voer in het vak **Servernaam** de naam of het IP-adres in van de RADIUS-server die u in de vorige sectie hebt geconfigureerd.

    b. Selecteer **Wijzigen**voor het **gedeelde geheim**en voer het gedeelde geheime wachtwoord in dat u eerder hebt gemaakt en opgenomen.

    c. Voer in het vak **Time-out (seconden)** een waarde van **30**in .  
    De time-outwaarde is nodig om voldoende tijd te hebben om de tweede verificatiefactor te voltooien.

    ![Radiusservervenster toevoegen door de time-out te configureren](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Selecteer **OK**.

### <a name="test-vpn-connectivity"></a>VPN-connectiviteit testen

In deze sectie bevestigt u dat de VPN-client is geverifieerd en geautoriseerd door de RADIUS-server wanneer u probeert verbinding te maken met de virtuele VPN-poort. De instructies gaan ervan uit dat u Windows 10 gebruikt als VPN-client.

> [!NOTE]
> Als u al een VPN-client hebt geconfigureerd om verbinding te maken met de VPN-server en de instellingen hebt opgeslagen, u de stappen overslaan met betrekking tot het configureren en opslaan van een VPN-verbindingsobject.
>

1. Selecteer op uw VPN-clientcomputer de knop **Start** en selecteer de knop **Instellingen.**

2. Selecteer **Netwerk & internet**in het venster **Windows-instellingen** .

3. Selecteer **VPN**.

4. Selecteer **Een VPN-verbinding toevoegen**.

5. Selecteer in het venster **Een VPN-verbinding toevoegen** in het vak **VPN-provider** de optie **Windows (ingebouwd),** vul de overige velden in, naar gelang van het geval, en selecteer **Vervolgens Opslaan**.

    ![Het venster 'Een VPN-verbinding toevoegen'](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Ga naar **het Configuratiescherm**en selecteer **Netwerk- en Deelcentrum**.

7. Selecteer **Adapterinstellingen wijzigen**.

    ![Netwerk- en deelcentrum - Adapterinstellingen wijzigen](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Klik met de rechtermuisknop op de VPN-netwerkverbinding en selecteer **Eigenschappen**.

9. Selecteer in het venster VPN-eigenschappen het tabblad **Beveiliging.**

10. Controleer op het tabblad **Beveiliging** of alleen **Microsoft CHAP Versie 2 (MS-CHAP v2)** is geselecteerd en selecteer **OK**.

    ![De optie 'Deze protocollen toestaan'](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Klik met de rechtermuisknop op de VPN-verbinding en selecteer **Verbinding maken**.

12. Selecteer **Verbinding** **maken**in het venster Instellingen .  
    Er wordt een geslaagde verbinding weergegeven in het beveiligingslogboek op de RADIUS-server als gebeurtenis-id 6272, zoals hier wordt weergegeven:

    ![Venster Gebeurtenis-eigenschappen met een geslaagde verbinding](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Radius oplossen van problemen

Ga ervan uit dat uw VPN-configuratie werkte voordat u de VPN-server configureerde om een gecentraliseerde RADIUS-server te gebruiken voor verificatie en autorisatie. Als de configuratie werkte, is het waarschijnlijk dat het probleem wordt veroorzaakt door een verkeerde configuratie van de RADIUS-server of het gebruik van een ongeldige gebruikersnaam of wachtwoord. Als u bijvoorbeeld het alternatieve UPN-achtervoegsel in de gebruikersnaam gebruikt, kan de aanmeldingspoging mislukken. Gebruik dezelfde accountnaam voor de beste resultaten.

Om deze problemen op te lossen, is een ideale plek om te beginnen om de logboeken van beveiligingsgebeurtenissen op de RADIUS-server te onderzoeken. Als u tijd wilt besparen bij het zoeken naar gebeurtenissen, u de aangepaste weergave Netwerkbeleid en Access Server gebruiken in Logboeken, zoals hier wordt weergegeven. "Event ID 6273" geeft gebeurtenissen aan waarbij de NPS de toegang tot een gebruiker heeft geweigerd.

![Logboeken met NPAS-gebeurtenissen](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Meervoudige verificatie configureren

Zie de artikelen [Planning a cloud-based Azure Multi-Factor Authentication deployment](howto-mfa-getstarted.md#create-conditional-access-policy) and [Set up my account for Two-Step Authentication](../user-help/multi-factor-authentication-end-user-first-time.md) Voor het configureren van gebruikers voor multi-factor authenticatie.

## <a name="install-and-configure-the-nps-extension"></a>De NPS-extensie installeren en configureren

In deze sectie vindt u instructies voor het configureren van VPN om MFA te gebruiken voor clientverificatie met de VPN-server.

Nadat u de NPS-extensie hebt geïnstalleerd en geconfigureerd, is alle RADIUS-gebaseerde clientverificatie die door deze server wordt verwerkt, vereist om MFA te gebruiken. Als al uw VPN-gebruikers niet zijn ingeschreven voor Azure Multi-Factor Authentication, u een van de volgende handelingen doen:

* Stel een andere RADIUS-server in om gebruikers te verifiëren die niet zijn geconfigureerd om MFA te gebruiken.

* Maak een registervermelding waarmee uitgedaagde gebruikers een tweede verificatiefactor kunnen opgeven als ze zijn ingeschreven voor Azure Multi-Factor Authentication.

Maak een nieuwe tekenreekswaarde met de naam _REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa_en stel de waarde in op *Waar* of *Onwaar*.

![De instelling 'Gebruikersmatch vereisen'](./media/howto-mfa-nps-extension-vpn/image34.png)

Als de waarde is ingesteld op *True* of leeg is, worden alle verificatieaanvragen onderworpen aan een MFA-uitdaging. Als de waarde is ingesteld op *False,* worden MFA-uitdagingen alleen uitgegeven aan gebruikers die zijn ingeschreven voor Azure Multi-Factor Authentication. Gebruik de instelling *False* alleen in test- of productieomgevingen tijdens een onboarding-periode.

### <a name="obtain-the-azure-active-directory-guid-id"></a>De Azure Active Directory GUID-id verkrijgen

Als onderdeel van de configuratie van de NPS-extensie moet u beheerdersreferenties en de id van uw Azure AD-tenant leveren. Verkrijg de ID door het volgende te doen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als de globale beheerder van de Azure-tenant.

2. Selecteer azure active directory in het menu **Azure Active Directory**of zoek naar En selecteer Azure Active **Directory** op een pagina.

3. Selecteer **Eigenschappen**.

4. Als u uw Azure AD-id wilt kopiëren, selecteert u de knop **Kopiëren.**

    ![Azure AD Directory ID in de Azure-portal](./media/howto-mfa-nps-extension-vpn/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>De NPS-extensie installeren

De NPS-extensie moet worden geïnstalleerd op een server waarop de functie Netwerkbeleid en Access Services is geïnstalleerd en die functioneert als de RADIUS-server in uw ontwerp. Installeer de NPS-extensie *niet* op uw VPN-server.

1. Download de NPS-extensie van [Microsoft Download Center](https://aka.ms/npsmfa).

2. Kopieer het uitvoerbare bestand *(NpsExtnForAzureMfaInstaller.exe)* naar de NPS-server.

3. Dubbelklik op de NPS-server op **NpsExtnForAzureMfaInstaller.exe** en selecteer, als u daarom wordt gevraagd, **Uitvoeren**.

4. Bekijk in het venster **NPS Extension For Azure MFA Setup** de voorwaarden voor softwarelicenties, schakel het selectievakje Akkoord gaan met de **licentievoorwaarden in** en selecteer **Installeren.**

    ![Het venster 'NPS-extensie voor Azure MFA Setup'](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Selecteer In het venster **NPS-extensie voor Azure MFA-installatie** de optie **Sluiten**.  

    ![Het bevestigingsvenster 'Setup Succesvol'](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Certificaten configureren voor gebruik met de NPS-extensie met behulp van een PowerShell-script

Om veilige communicatie en zekerheid te garanderen, configureert u certificaten voor gebruik door de NPS-extensie. De NPS-componenten bevatten een Windows PowerShell-script dat een zelfondertekend certificaat configureert voor gebruik met NPS.

Het script voert de volgende acties uit:

* Hiermee maakt u een zelfondertekend certificaat.
* Koppelt de openbare sleutel van het certificaat aan de serviceprincipal op Azure AD.
* Slaat het certificaat op in de plaatselijke machinewinkel.
* Verleent de netwerkgebruiker toegang tot de privésleutel van het certificaat.
* Start de NPS-service opnieuw.

Als u uw eigen certificaten wilt gebruiken, moet u de openbare sleutel van uw certificaat koppelen aan de serviceprincipal op Azure AD, enzovoort.

Als u het script wilt gebruiken, geeft u de extensie uw Azure Active Directory-beheerdersreferenties en de Azure Active Directory-tenant-id die u eerder hebt gekopieerd. Voer het script uit op elke NPS-server waar u de NPS-extensie installeert.

1. Voer Windows PowerShell uit als beheerder.

2. Voer bij de PowerShell-opdrachtprompt **cd 'c:\Program Files\Microsoft\AzureMfa\Config'** in en selecteer Enter.

3. Voer bij de volgende opdrachtprompt **.\AzureMfaNpsExtnConfigSetup.ps1**in en selecteer Enter. Het script controleert of de Azure AD PowerShell-module is geïnstalleerd. Als deze niet is geïnstalleerd, installeert het script de module voor u.

    ![Het configuratiescript AzureMfsNpsExtnConfigSetup.ps1 uitvoeren](./media/howto-mfa-nps-extension-vpn/image38.png)

    Nadat het script de installatie van de PowerShell-module heeft geverifieerd, wordt het aanmeldingsvenster van de Azure Active Directory PowerShell-module weergegeven.

4. Voer de referenties en het wachtwoord van uw Azure AD-ad-beheerder in en selecteer **Aanmelden**.

    ![Verifiëren naar Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Plak bij de opdrachtprompt de tenant-id die u eerder hebt gekopieerd en selecteer Enter.

    ![Voer de eerder gekopieerde Azure AD Directory-id in](./media/howto-mfa-nps-extension-vpn/image40.png)

    Het script maakt een zelfondertekend certificaat en voert andere configuratiewijzigingen uit. De uitvoer is als die in de volgende afbeelding:

    ![PowerShell-venster met zelfondertekend certificaat](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Start de server opnieuw op.

### <a name="verify-the-configuration"></a>De configuratie controleren

Als u de configuratie wilt verifiëren, moet u een nieuwe VPN-verbinding met de VPN-server tot stand brengen. Nadat u uw referenties voor primaire verificatie hebt ingevoerd, wacht de VPN-verbinding tot de secundaire verificatie is geslaagd voordat de verbinding tot stand is gebracht, zoals hieronder wordt weergegeven.

![Het VPN-venster Windows-instellingen](./media/howto-mfa-nps-extension-vpn/image42.png)

Als u met de secundaire verificatiemethode die u eerder in Azure MFA hebt geconfigureerd, hebt geverifieerd, bent u verbonden met de bron. Als de secundaire verificatie echter niet succesvol is, wordt u de toegang tot de bron geweigerd.

In het volgende voorbeeld biedt de Microsoft Authenticator-app op een Windows Phone de secundaire verificatie:

![Voorbeeld MFA-prompt op Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

Nadat u met de secundaire methode hebt geverifieerd, krijgt u toegang tot de virtuele poort op de VPN-server. Omdat u een secundaire verificatiemethode moest gebruiken met behulp van een mobiele app op een vertrouwd apparaat, is het aanmeldingsproces veiliger dan wanneer het alleen een combinatie van gebruikersnaam en wachtwoord zou gebruiken.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Logboeklogboeken weergeven voor geslaagde aanmeldingsgebeurtenissen

Als u geslaagde aanmeldingsgebeurtenissen in de logboeken van Windows Event Viewer wilt weergeven, voert u het Windows Security-logboek op de NPS-server op door de volgende PowerShell-opdracht in te voeren:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![PowerShell-logboeklogboeken voor beveiliging](./media/howto-mfa-nps-extension-vpn/image44.png)

U ook het beveiligingslogboek of de aangepaste weergave Netwerkbeleid en Access Services bekijken, zoals hier wordt weergegeven:

![Voorbeeld logboek van netwerkbeleidsserver](./media/howto-mfa-nps-extension-vpn/image45.png)

Op de server waar u de NPS-extensie voor Azure Multi-Factor Authentication hebt geïnstalleerd, vindt u Logboeken die specifiek zijn voor de extensie bij *Toepassings- en Serviceslogboeken\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Voorbeeld logboeken van logboeken AuthZ](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Als de configuratie niet werkt zoals verwacht, begint u met het oplossen van problemen door te controleren of de gebruiker is geconfigureerd om MFA te gebruiken. Laat de gebruiker verbinding maken met de [Azure-portal.](https://portal.azure.com) Als de gebruiker wordt gevraagd om secundaire verificatie en met succes kan verifiëren, u een onjuiste configuratie van MFA als probleem elimineren.

Als MFA voor de gebruiker werkt, controleert u de relevante logboeken van logboeken. De logboeken bevatten de beveiligingsgebeurtenis, gatewayoperationele en Azure Multi-Factor Authentication-logboeken die in de vorige sectie worden besproken.

Hier wordt een voorbeeld van een beveiligingslogboek weergegeven met een mislukte aanmeldingsgebeurtenis (gebeurtenis-id 6273):

![Beveiligingslogboek met een mislukte aanmeldingsgebeurtenis](./media/howto-mfa-nps-extension-vpn/image47.png)

Een gerelateerde gebeurtenis uit het azure multi-factor authentication-logboek wordt hier weergegeven:

![Azure Multi-Factor Authentication-logboeken](./media/howto-mfa-nps-extension-vpn/image48.png)

Raadpleeg de LOGBOEKbestanden van de NPS-databaseindeling waar de NPS-service is geïnstalleerd voor geavanceerde probleemoplossing. De logboekbestanden worden gemaakt in de map _%SystemRoot%\System32\Logs_ als door komma's afgebakende tekstbestanden. Zie [NPS Database Format Log Files interpreteren voor](https://technet.microsoft.com/library/cc771748.aspx)een beschrijving van de logbestanden .

De vermeldingen in deze logboekbestanden zijn moeilijk te interpreteren, tenzij u ze exporteert naar een spreadsheet of een database. U veel IAS-ontweringtools (Internet Authentication Service) online vinden om u te helpen bij het interpreteren van de logbestanden. De output van een dergelijke downloadbare [shareware applicatie](https://www.deepsoftware.com/iasviewer) wordt hier weergegeven:

![Voorbeeld Shareware-app IAS-parser](./media/howto-mfa-nps-extension-vpn/image49.png)

Als u aanvullende probleemoplossing wilt oplossen, u een protocolanalyzer gebruiken, zoals Wireshark of [Microsoft Message Analyzer.](https://technet.microsoft.com/library/jj649776.aspx) De volgende afbeelding van Wireshark toont de RADIUS-berichten tussen de VPN-server en de NPS.

![Microsoft Message Analyzer met gefilterd verkeer](./media/howto-mfa-nps-extension-vpn/image50.png)

Zie [Uw bestaande NPS-infrastructuur integreren met Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Azure-verificatie voor meerdere factoren](concept-mfa-licensing.md)

[Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md)

[Uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
