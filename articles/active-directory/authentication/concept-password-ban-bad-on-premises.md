---
title: Azure AD-wachtwoord beveiliging-Azure Active Directory
description: Zwakke wacht woorden in on-premises Active Directory met Azure AD-wachtwoord beveiliging verbieden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74848643"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Microsoft Azure Active Directory-wachtwoordbeveiliging afdwingen voor Windows Server Active Directory

Azure AD-wachtwoord beveiliging is een functie waarmee wachtwoord beleid in een organisatie wordt verbeterd. Bij een on-premises implementatie van wachtwoord beveiliging worden zowel de globale als aangepaste lijst met verboden wacht woorden gebruikt die zijn opgeslagen in azure AD. Het doet hetzelfde als Azure AD voor wijzigingen in de Cloud. Deze controles worden uitgevoerd tijdens wachtwoord wijzigingen en scenario's voor het opnieuw instellen van wacht woorden.

## <a name="design-principles"></a>Ontwerpprincipes

Azure AD-wachtwoord beveiliging is ontworpen met het oog op deze principes:

* Domein controllers hoeven nooit rechtstreeks met Internet te communiceren.
* Er zijn geen nieuwe netwerk poorten geopend op domein controllers.
* Er zijn geen wijzigingen in het Active Directory schema vereist. De software maakt gebruik van de bestaande Active Directory- **container** en de schema objecten van **serviceConnectionPoint** .
* Er is geen minimum Active Directory domein of forest-functionaliteits niveau (DFL/FFL) vereist.
* De software maakt of vereist geen accounts in de Active Directory domeinen die deze beveiligt.
* Wacht woorden voor ongecodeerde tekst van de gebruiker houden nooit de domein controller, hetzij tijdens wachtwoord validatie bewerkingen of op een ander tijdstip.
* De software is niet afhankelijk van andere Azure AD-functies. een voor beeld van een Azure AD-wachtwoord hash-synchronisatie is niet gerelateerd en is niet vereist om Azure AD-wachtwoord beveiliging te laten functioneren.
* Incrementele implementatie wordt ondersteund, maar het wachtwoord beleid wordt alleen afgedwongen wanneer de domein controller agent (DC-agent) is geïnstalleerd. Zie het volgende onderwerp voor meer informatie.

## <a name="incremental-deployment"></a>Incrementele implementatie

Azure AD-wachtwoord beveiliging biedt ondersteuning voor incrementele implementatie op alle domein controllers in een Active Directory domein, maar het is belang rijk om te begrijpen wat dit echt betekent en wat de voor afwegingen zijn.

De Azure AD-agent software voor wachtwoord beveiliging kan alleen wacht woorden valideren wanneer deze is geïnstalleerd op een domein controller en alleen voor wachtwoord wijzigingen die worden verzonden naar die domein controller. Het is niet mogelijk om te bepalen welke domein controllers worden gekozen door Windows-client machines voor het verwerken van wijzigingen in het gebruikers wachtwoord. Om het consistente gedrag en de beveiliging van Universal-wachtwoord beveiliging te garanderen, moet de DC-agent software worden geïnstalleerd op alle domein controllers in een domein.

Veel organisaties willen een zorgvuldige test uitvoeren van Azure AD-wachtwoord beveiliging op een subset van hun domein controllers voordat een volledige implementatie wordt uitgevoerd. Azure AD-wachtwoord beveiliging biedt ondersteuning voor gedeeltelijke implementatie. Internet Explorer wordt door de DC-agent software op een bepaalde domein controller actief gevalideerd, zelfs wanneer andere domein controllers in het domein niet de DC-agent software hebben geïnstalleerd. Gedeeltelijke implementaties van dit type zijn niet beveiligd en worden niet aanbevolen voor test doeleinden.

## <a name="architectural-diagram"></a>Architectuur diagram

Het is belang rijk om inzicht te krijgen in het onderliggende ontwerp en functionele concepten voordat u Azure AD-wachtwoord beveiliging in een on-premises Active Directory omgeving implementeert. In het volgende diagram ziet u hoe de onderdelen van wachtwoord beveiliging samen werken:

![Hoe de Azure AD-onderdelen voor wachtwoord beveiliging samen werken](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* De Azure AD-proxy service voor wachtwoord beveiliging wordt uitgevoerd op een computer die lid is van een domein in het huidige Active Directory-forest. Het belangrijkste doel is om aanvragen voor het downloaden van wachtwoord beleid van domein controllers door te sturen naar Azure AD. Vervolgens worden de antwoorden van Azure AD naar de domein controller geretourneerd.
* De wachtwoord filter-DLL van de DC-agent ontvangt aanvragen voor validatie van gebruikers wachtwoorden van het besturings systeem. Ze worden doorgestuurd naar de DC-Agent service die lokaal op de domein controller wordt uitgevoerd.
* De DC-Agent service van wachtwoord beveiliging ontvangt aanvragen voor wachtwoord validatie van de wachtwoord filter-DLL van de DC-agent. Ze worden verwerkt met behulp van het huidige (lokaal beschik bare) wachtwoord beleid en retourneert het resultaat: *slagen* of *mislukken*.

## <a name="how-password-protection-works"></a>Hoe wachtwoord beveiliging werkt

Elk Azure AD-service-exemplaar van het wacht woord voor wachtwoord beveiliging adverteert zichzelf aan de domein controllers in het forest door een **serviceConnectionPoint** -object te maken in Active Directory.

Elke DC-Agent service voor wachtwoord beveiliging maakt ook een **serviceConnectionPoint** -object in Active Directory. Dit object wordt hoofd zakelijk gebruikt voor rapportage en diagnose.

De DC-Agent service is verantwoordelijk voor het initiëren van het downloaden van een nieuw wachtwoord beleid vanuit Azure AD. De eerste stap is het vinden van een Azure AD-proxy service voor wachtwoord beveiliging door het uitvoeren van een query op het forest voor proxy **serviceConnectionPoint** -objecten. Wanneer een beschik bare proxy service wordt gevonden, verzendt de DC-agent een aanvraag voor wachtwoord beleid downloaden naar de proxy service. De proxy service op zijn beurt verzendt de aanvraag naar Azure AD. De proxy service retourneert vervolgens de reactie van de DC-Agent service.

Nadat de DC-Agent service een nieuw wachtwoord beleid van Azure AD heeft ontvangen, slaat de service het beleid op in een speciale map in de hoofdmap van de map *SYSVOL* van het domein. De DC-Agent service bewaakt deze map ook voor het geval nieuwere beleids regels worden gerepliceerd vanuit andere services van de DC-agent in het domein.

De DC-Agent service vraagt altijd een nieuw beleid op bij het starten van de service. Nadat de service DC-agent is gestart, wordt de leeftijd van het huidige lokaal beschik bare beleid per uur gecontroleerd. Als het beleid ouder is dan één uur, vraagt de DC-agent een nieuw beleid aan bij Azure AD via de proxy service, zoals eerder is beschreven. Als het huidige beleid niet ouder is dan één uur, blijft de DC-agent dit beleid gebruiken.

Wanneer een Azure AD-wachtwoord beleid voor wachtwoord beveiliging wordt gedownload, is dat beleid specifiek voor een Tenant. Met andere woorden, wachtwoord beleid is altijd een combi natie van de aangepaste lijst met verboden wacht woorden van micro soft en de lijst met geblokkeerde wacht woorden per Tenant.

De DC-Agent communiceert met de proxy service via RPC via TCP. De proxy service luistert naar deze aanroepen op een dynamische of statische RPC-poort, afhankelijk van de configuratie.

De DC-agent luistert nooit naar een poort die beschikbaar is via het netwerk.

De proxy service roept de DC-Agent service nooit aan.

De proxy service is stateless. Het beleid of een andere status die is gedownload van Azure, wordt nooit in de cache opgeslagen.

De DC Agent-service maakt altijd gebruik van het meest recente, lokaal beschik bare wachtwoord beleid om het wacht woord van een gebruiker te evalueren. Als er geen wachtwoord beleid beschikbaar is op de lokale domein controller, wordt het wacht woord automatisch geaccepteerd. Als dat gebeurt, wordt een gebeurtenis bericht in het logboek geregistreerd om de beheerder te waarschuwen.

Azure AD-wachtwoord beveiliging is geen real-time beleids toepassings engine. Er kan een vertraging optreden tussen het moment dat een wachtwoord beleid configuratie wijziging wordt aangebracht in azure AD en wanneer deze wijziging wordt doorgevoerd en op alle domein controllers wordt afgedwongen.

Azure AD-wachtwoord beveiliging fungeert als aanvulling op de bestaande beleids regels voor Active Directory wachtwoord, niet als vervanging. Dit omvat alle andere dll-bestanden voor wachtwoord filter van derden die kunnen worden geïnstalleerd. Active Directory is altijd vereist dat alle onderdelen van wachtwoord validatie overeenkomen voordat een wacht woord wordt geaccepteerd.

## <a name="foresttenant-binding-for-password-protection"></a>Forest/Tenant-binding voor wachtwoord beveiliging

Voor de implementatie van Azure AD-wachtwoord beveiliging in een Active Directory-forest is registratie van dat forest met Azure AD vereist. Elke proxy service die is geïmplementeerd, moet ook worden geregistreerd bij Azure AD. Deze forest-en proxy registraties zijn gekoppeld aan een specifieke Azure AD-Tenant, die impliciet wordt geïdentificeerd door de referenties die worden gebruikt tijdens de registratie.

Het Active Directory-forest en alle geïmplementeerde proxy services binnen een forest moeten zijn geregistreerd bij dezelfde Tenant. Het is niet mogelijk om een Active Directory-forest of proxy services in dat forest te registreren bij verschillende Azure AD-tenants. Symptomen van een dergelijke configuratie met een onjuiste configuratie zijn het niet mogelijk om wachtwoord beleid te downloaden.

## <a name="download"></a>Download

De twee vereiste agent installatie Programma's voor Azure AD-wachtwoord beveiliging zijn beschikbaar via het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Volgende stappen
[Wachtwoordbeveiliging in Azure AD implementeren](howto-password-ban-bad-on-premises-deploy.md)
