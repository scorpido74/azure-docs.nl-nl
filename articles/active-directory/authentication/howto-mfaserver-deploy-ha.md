---
title: Hoge beschikbaarheid voor Azure MFA Server - Azure Active Directory
description: Implementeer meerdere exemplaren van Azure Multi-Factor Authentication Server in configuraties die een hoge beschikbaarheid bieden.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7b5f6bef5358acf0709f994b85215e505fa4db
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653370"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Azure Multi-Factor Authentication Server configureren voor hoge beschikbaarheid

Als u met uw Azure Server MFA-implementatie een hoge beschikbaarheid wilt bereiken, moet u meerdere MFA-servers implementeren. In deze sectie vindt u informatie over een ontwerp voor een belastingom uw doelen met hoge beschikbaarheid in uw Azure MFS Server-implementatie te bereiken.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt Microsoft geen MFA Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die MFA Server vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.

## <a name="mfa-server-overview"></a>Overzicht van MFA Server

De Azure MFA Server-servicearchitectuur bestaat uit verschillende componenten zoals weergegeven in het volgende diagram:

 ![MFA-serverarchitectuurcomponenten](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Een MFA Server is een Windows Server waarop de Azure Multi-Factor Authentication-software is geïnstalleerd. De MFA-server-instantie moet worden geactiveerd door de MFA-service in Azure om te kunnen functioneren. Er kunnen meerdere MFA-servers on-premises worden geïnstalleerd.

De eerste MFA-server die is geïnstalleerd, is de hoofdMFA-server bij activering door de Azure MFA-service. De master MFA server heeft een schrijfbare kopie van de PhoneFactor.pfdata database. Latere installaties van exemplaren van MFA Server staan bekend als ondergeschikten. De MFA-ondergeschikten hebben een gerepliceerde alleen-lezen kopie van de PhoneFactor.pfdata-database. MFA-servers repliceren informatie met behulp van RPC (Remote Procedure Call). Alle MFA Severs moeten collectief domein worden samengevoegd of zelfstandig zijn om informatie te repliceren.

Zowel MFA-master als ondergeschikte MFA-servers communiceren met de MFA-service wanneer tweestapsverificatie vereist is. Wanneer een gebruiker bijvoorbeeld probeert toegang te krijgen tot een toepassing waarvoor tweestapsverificatie vereist is, wordt de gebruiker eerst geverifieerd door een identiteitsprovider, zoals Active Directory (AD).

Na succesvolle verificatie met AD communiceert de MFA-server met de MFA-service. De MFA-server wacht op een melding van de MFA-service om de gebruiker toegang tot de toepassing toe te staan of te weigeren.

Als de MFA-hoofdserver offline gaat, kunnen verificaties nog steeds worden verwerkt, maar bewerkingen waarvoor wijzigingen in de MFA-database nodig zijn, kunnen niet worden verwerkt. (Voorbeelden hiervan zijn: de toevoeging van gebruikers, selfservice-pincodewijzigingen, het wijzigen van gebruikersgegevens of toegang tot de gebruikersportal)

## <a name="deployment"></a>Implementatie

Houd rekening met de volgende belangrijke punten voor het balanceren van Azure MFA Server en de bijbehorende onderdelen.

* **Radius-standaard gebruiken om een hoge beschikbaarheid te bereiken.** Als u Azure MFA-servers als RADIUS-servers gebruikt, u mogelijk één MFA-server configureren als een primair RADIUS-verificatiedoel en andere Azure MFA-servers als secundaire verificatiedoelen. Deze methode om een hoge beschikbaarheid te bereiken is echter mogelijk niet praktisch omdat u moet wachten tot er een time-outperiode optreedt wanneer verificatie mislukt op het primaire verificatiedoel voordat u worden geverifieerd tegen het secundaire verificatiedoel. Het is efficiënter om de RADIUS-doorvoer tussen de RADIUS-client en de RADIUS-servers te laden (in dit geval de Azure MFA-servers die fungeren als RADIUS-servers), zodat u de RADIUS-clients configureren met één URL die ze kunnen aanwijzen.
* **Noodzaak om mfa ondergeschikten handmatig te promoten.** Als de hoofdAzure MFA-server offline gaat, blijven de secundaire Azure MFA-servers MFA-aanvragen verwerken. Totdat er echter een hoofdMFA-server beschikbaar is, kunnen beheerders geen gebruikers toevoegen of MFA-instellingen wijzigen en kunnen gebruikers geen wijzigingen aanbrengen via de gebruikersportal. Het promoten van een MFA die ondergeschikt is aan de hoofdrol is altijd een handmatig proces.
* **Onafscheidelijkheid van componenten**. De Azure MFA Server bestaat uit verschillende onderdelen die op dezelfde Windows Server-instantie of op verschillende instanties kunnen worden geïnstalleerd. Deze onderdelen omvatten de gebruikersportal, de mobiele app-webservice en de ADFS-adapter (agent). Deze separabiliteit maakt het mogelijk om de webtoepassingsproxy te gebruiken om de gebruikersportal en de mobiele app-webserver te publiceren vanuit het perimeternetwerk. Een dergelijke configuratie draagt bij aan de algehele beveiliging van uw ontwerp, zoals weergegeven in het volgende diagram. De MFA-gebruikersportal en mobiele app-webserver kunnen ook worden geïmplementeerd in HA-load-balanced-configuraties.

   ![MFA-server met een perimeternetwerk](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **One-time password (OTP) via SMS (aka one-way SMS) vereist het gebruik van kleverige sessies als het verkeer is load-balanced**. One-way SMS is een verificatieoptie die ervoor zorgt dat de MFA-server de gebruikers een sms-bericht stuurt met een OTP. De gebruiker voert de OTP in een snel venster om de MFA-uitdaging te voltooien. Als u Azure MFA-servers met saldo laadt, moet dezelfde server die het oorspronkelijke verificatieverzoek heeft ingediend, de server zijn die het OTP-bericht van de gebruiker ontvangt. als een andere MFA-server het OTP-antwoord ontvangt, mislukt de verificatieuitdaging. Zie [Eenmalig wachtwoord via SMS toegevoegd aan Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server)voor meer informatie.
* **Load-Balanced implementaties van de User Portal en Mobile App Web Service vereisen plakkerige sessies.** Als u de MFA-gebruikersportal en de mobiele app-webservice in evenwicht brengt, moet elke sessie op dezelfde server blijven.

## <a name="high-availability-deployment"></a>Implementatie met hoge beschikbaarheid

In het volgende diagram wordt een volledige HA-load-balanced implementatie van Azure MFA en de onderdelen daarvan weergegeven, samen met ADFS ter referentie.

 ![Azure MFA Server HA-implementatie](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Let op de volgende items voor het overeenkomstig genummerde gebied van het voorgaande diagram.

1. De twee Azure MFA-servers (MFA1 en MFA2) zijn load balanced (mfaapp.contoso.com) en zijn geconfigureerd om een statische poort (4443) te gebruiken om de PhoneFactor.pfdata-database te repliceren. De Web Service SDK is geïnstalleerd op elk van de MFA-server om communicatie via TCP-poort 443 met de ADFS-servers mogelijk te maken. De MFA-servers worden geïmplementeerd in een stateless load-balanced configuratie. Als u OTP echter via SMS wilt gebruiken, moet u stateful load balancing gebruiken.
   ![Azure MFA Server - App-server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Omdat RPC dynamische poorten gebruikt, is het niet aan te raden om firewalls te openen tot het bereik van dynamische poorten die RPC mogelijk kan gebruiken. Als u een firewall **hebt tussen** uw MFA-toepassingsservers, moet u de MFA-server configureren om te communiceren op een statische poort voor het replicatieverkeer tussen ondergeschikte en hoofdservers en die poort op uw firewall te openen. U de statische poort forceren door ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` ```Pfsvc_ncan_ip_tcp_port``` een DWORD-registerwaarde te maken bij aangeroepen en de waarde in te stellen op een beschikbare statische poort. Verbindingen worden altijd geïnitieerd door de ondergeschikte MFA-servers naar de master, de statische poort is alleen vereist op de master, maar omdat u een ondergeschikte promoten om de master te zijn op elk gewenst moment, moet u de statische poort instellen op alle MFA-servers.

2. De twee User Portal/MFA Mobile App servers (MFA-UP-MAS1 en MFA-UP-MAS2) worden gebalanceerd in een **stateful** configuratie (mfa.contoso.com). Bedenk dat plaksessies een vereiste zijn voor het balanceren van de MFA-gebruikersportal en mobiele appservice.
   ![Azure MFA Server - Gebruikersportal en HA van mobiele appservice](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. De ADFS-serverfarm is load balanced en gepubliceerd op het internet via load-balanced ADFS-proxy's in het perimeternetwerk. Elke ADFS-server gebruikt de ADFS-agent om te communiceren met de Azure MFA-servers met behulp van één url (mfaapp.contoso.com) via TCP-poort 443.

## <a name="next-steps"></a>Volgende stappen

* [Azure MFA Server installeren en configureren](howto-mfaserver-deploy.md)
