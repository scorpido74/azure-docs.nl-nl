---
title: LDAP-verificatie en Azure MFA Server - Azure Active Directory
description: LDAP-verificatie en Azure Multi-Factor Authentication Server implementeren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7a5400fce2fce1b75cbd579830f929fcc0d0dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454462"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-verificatie en Azure Multi-Factor Authentication Server

Standaard is de Azure Multi-Factor Authentication-server geconfigureerd om de gebruikers van Active Directory te importeren of te synchroniseren. Deze kan echter worden geconfigureerd om verbinding te maken met verschillende LDAP-adreslijsten, zoals een ADAM-adreslijst of een specifieke Active Directory-domeincontroller. Wanneer de Azure Multi-Factor Authentication Server via LDAP met een directory is verbonden, kan deze fungeren als een LDAP-proxy om verificaties uit te voeren. Daarnaast kunt u de LDAP-binding gebruiken als een RADIUS-doel voor het uitvoeren van pre-verificaties van gebruikers met IIS-verificatie, of voor het uitvoeren van primaire verificaties in de Azure MFA-gebruikersportal.

Als u Azure Multi-Factor Authentication als LDAP-proxy wilt gebruiken, voegt u de Azure Multi-Factor Authentication Server in tussen de LDAP-client (bijvoorbeeld VPN-toestel, -toepassing) en de LDAP-mapserver. De Azure Multi-Factor Authentication-server moet worden geconfigureerd om te kunnen communiceren met zowel de clientservers als de LDAP-directory. In deze configuratie accepteert de Azure Multi-Factor Authentication-server LDAP-aanvragen van clientservers en toepassingen, en stuurt deze door naar de doel-LDAP-adreslijstserver om de primaire referenties te valideren. Als de LDAP-map de primaire referenties valideert, voert Azure Multi-Factor Authentication een tweede identiteitsverificatie uit en stuurt het een antwoord terug naar de LDAP-client. De volledige verificatie slaagt alleen als de LDAP-serververificatie en de verificatie in de tweede stap zijn gelukt.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt Microsoft geen MFA Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die MFA Server vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.

## <a name="configure-ldap-authentication"></a>LDAP-verificatie configureren

Als u LDAP-verificatie wilt configureren, moet u de Azure Multi-Factor Authentication-server op een Windows-server installeren. Gebruik de volgende procedure:

### <a name="add-an-ldap-client"></a>Een LDAP-client toevoegen

1. Selecteer in de Azure Multi-Factor Authentication Server het pictogram LDAP-verificatie in het linkermenu.
2. Schakel het selectievakje **LDAP-verificatie inschakelen** in.

   ![LDAP-verificatie in MFA-server](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Wijzig op het tabblad Clients de TCP-poort en SSL-poort (TLS) als de LDAP-service azure multi-factor authentication moet worden gekoppeld aan niet-standaardpoorten om naar LDAP-aanvragen te luisteren.
4. Als u VAN plan bent LDAPS van de client te gebruiken naar de Azure Multi-Factor Authentication Server, moet een TLS/SSL-certificaat worden geïnstalleerd op dezelfde server als MFA Server. Klik **op Bladeren** naast het SSL-certificaatvak (SSL) en selecteer een certificaat dat u wilt gebruiken voor de beveiligde verbinding.
5. Klik op**toevoegen**.
6. Voer in het dialoogvenster LDAP-client toevoegen het IP-adres van het toestel, de server of de toepassing in die is geverifieerd naar de server en een toepassingsnaam (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
7. Schakel het selectievakje **Overeenkomende Azure Multi-Factor Authentication-gebruiker** vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan verificatie in twee stappen onderworpen zijn. Als een aanzienlijk aantal gebruikers nog niet in de server is geïmporteerd en/of is vrijgesteld van verificatie in twee stappen, laat u het vakje onaangevinkt. Zie het helpbestand van MFA Server voor meer informatie over deze functie.

Herhaal deze stappen om extra LDAP-clients toe te voegen.

### <a name="configure-the-ldap-directory-connection"></a>De LDAP-directoryverbinding configureren

Wanneer Azure Multi-Factor Authentication is geconfigureerd om LDAP-verificaties te kunnen ontvangen, moet deze als proxy optreden voor verificaties bij de LDAP-adreslijst. Dat is de reden waarom op het tabblad Doel een enkele niet beschikbare optie voor het gebruik van een LDAP-doel wordt weergegeven.

> [!NOTE]
> Directory-integratie werkt niet gegarandeerd met andere mappen dan Active Directory Domain Services.

1. Als u de LDAP-mapverbinding wilt configureren, klikt u op het pictogram **Directory-integratie.**
2. Selecteer op het tabblad Instellingen de knop **Specifieke LDAP-configuratiekeuzeknop gebruiken.**
3. Selecteer **Bewerken...**
4. Vul in de velden van het dialoogvenster LDAP-configuratie bewerken de benodigde informatie in om verbinding te maken met de LDAP-adreslijst. Beschrijvingen van de velden zijn ook beschikbaar in het Help-bestand voor de Azure Multi-Factor Authentication-server.

    ![LdAP-config voor directory-integratie](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Test de LDAP-verbinding door op de knop **Testen** te klikken.
6. Als de LDAP-verbindingstest is geslaagd, klikt u op de knop **OK.**
7. Klik op het tabblad **Filters.** De server is vooraf geconfigureerd voor het laden van containers, beveiligingsgroepen en gebruikers uit Active Directory. Als u verbinding maakt met een andere LDAP-directory, moet u waarschijnlijk de weergegeven filters bewerken. Klik op de koppeling **Help** voor meer informatie over filters.
8. Klik op het tabblad **Kenmerken.** De server is vooraf geconfigureerd om kenmerken uit Active Directory toe te kaarten.
9. Als u verbinding maakt met een andere LDAP-directory of als u de vooraf geconfigureerde kenmerktoewijzingen wilt wijzigen, klikt u op **Bewerken...**
10. Wijzig in het dialoogvenster Kenmerken bewerken de toewijzingen van de LDAP-kenmerktoewijzingen voor uw adreslijst. Kenmerkennamen kunnen worden getypt in of geselecteerd door op de **...** -knop naast elk veld. Klik op de koppeling **Help** voor meer informatie over kenmerken.
11. Klik op de knop **OK**.
12. Klik op het pictogram **Bedrijfsinstellingen** en selecteer het tabblad **Gebruikersnaamresolutie.**
13. Als u een verbinding maakt met Active Directory vanaf een server die is gekoppeld aan een domein, laat u het keuzerondje **Windows-beveiligings-id's (SID's) gebruiken voor overeenkomende gebruikersnamen** ingeschakeld. Selecteer anders het **kenmerk LDAP-kenmerk gebruiken voor het afstemmen van de keuzerondjes voor gebruikersnamen.**

Wanneer het keuzerondje **Het kenmerk Unieke LDAP-id gebruiken voor overeenkomende gebruikersnamen** is geselecteerd, probeert de Azure Multi-Factor Authentication-server elke gebruikersnaam om te zetten in een unieke id in de LDAP-directory. Er wordt een LDAP-zoekopdracht uitgevoerd op de kenmerken gebruikersnaam die zijn gedefinieerd in het tabblad Directory-integratie -> kenmerken. Wanneer een gebruiker zich verifieert, wordt de gebruikersnaam opgelost naar de unieke id in de LDAP-map. De unieke id wordt gebruikt voor het matchen van de gebruiker in het Azure Multi-Factor Authentication-gegevensbestand. Dit zorgt voor case-ongevoelige vergelijkingen, en lange en korte gebruikersnaam formaten.

Nadat u deze stappen hebt voltooid, luistert de MFA-server op de geconfigureerde poorten voor LDAP-toegangsaanvragen van de geconfigureerde clients en fungeert deze als een proxy voor deze aanvragen naar de LDAP-map voor verificatie.

## <a name="configure-ldap-client"></a>LDAP-client configureren

Als u de LDAP-client wilt configureren, gebruikt u de volgende richtlijnen:

* Configureer uw apparaat, server of toepassing als u wilt dat deze via LDAP bij de Azure Multi-Factor Authentication-server wordt geverifieerd alsof het uw LDAP was. Gebruik dezelfde instellingen die u normaal gesproken gebruikt om rechtstreeks verbinding te maken met de LDAP-directory, met uitzondering van de servernaam of het IP-adres omdat deze toebehoren aan de Azure Multi-Factor Authentication-server.
* Configureer de LDAP-time-out op 30-60 seconden, zodat er tijd is om de referenties van de gebruiker te valideren met de LDAP-map, de verificatie in de tweede stap uit te voeren, hun antwoord te ontvangen en te reageren op het LDAP-toegangsverzoek.
* Als u LDAPS gebruikt, moet het toestel of de server die de LDAP-query's maakt, het TLS/SSL-certificaat vertrouwen dat is geïnstalleerd op de Azure Multi-Factor Authentication Server.
