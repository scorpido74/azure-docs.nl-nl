---
title: Azure AD-wachtwoordbeveiliging - Azure Active Directory
description: Zwakke wachtwoorden in on-premises Active Directory verbieden met azure AD-wachtwoordbeveiliging
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848643"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Microsoft Azure Active Directory-wachtwoordbeveiliging afdwingen voor Windows Server Active Directory

Azure AD-wachtwoordbeveiliging is een functie die het wachtwoordbeleid in een organisatie verbetert. On-premises implementatie van wachtwoordbeveiliging maakt gebruik van zowel de algemene als aangepaste lijsten met verboden wachtwoorden die zijn opgeslagen in Azure AD. Het doet dezelfde controles on-premises als Azure AD doet voor cloud-gebaseerde wijzigingen. Deze controles worden uitgevoerd tijdens wachtwoordwijzigingen en wachtwoordresetscenario's.

## <a name="design-principles"></a>Ontwerpprincipes

Azure AD-wachtwoordbeveiliging is ontworpen met de volgende principes:

* Domeincontrollers hoeven nooit rechtstreeks met het internet te communiceren.
* Er worden geen nieuwe netwerkpoorten geopend op domeincontrollers.
* Er zijn geen wijzigingen in het Active Directory-schema vereist. De software maakt gebruik van de bestaande Active **Directory-container-** en **serviceConnectionPoint-schemaobjecten.**
* Er is geen minimum Active Directory-domein of forestfunctionaliteitsniveau (DFL/FFL) vereist.
* De software maakt of vereist geen accounts in de Active Directory-domeinen die worden beschermd.
* Gebruikerswachtwoorden met duidelijke tekst verlaten de domeincontroller nooit, tijdens wachtwoordvalidatiebewerkingen of op een ander tijdstip.
* De software is niet afhankelijk van andere Azure AD-functies; Azure AD-wachtwoordhashsynchronisatie is bijvoorbeeld niet gerelateerd en is niet vereist om azure AD-wachtwoordbeveiliging te laten functioneren.
* Incrementele implementatie wordt ondersteund, maar het wachtwoordbeleid wordt alleen afgedwongen wanneer de Dc-agent (Domain Controller Agent) is geïnstalleerd. Zie het volgende onderwerp voor meer details.

## <a name="incremental-deployment"></a>Incrementele implementatie

Azure AD-wachtwoordbeveiliging ondersteunt incrementele implementatie voor domeincontrollers in een Active Directory-domein, maar het is belangrijk om te begrijpen wat dit werkelijk betekent en wat de afwegingen zijn.

De DC-agentsoftware voor Azure AD-wachtwoordbeveiliging kan alleen wachtwoorden valideren wanneer deze op een domeincontroller zijn geïnstalleerd, en alleen voor wachtwoordwijzigingen die naar die domeincontroller worden verzonden. Het is niet mogelijk om te bepalen welke domeincontrollers worden gekozen door Windows-clientmachines voor het verwerken van wijzigingen in gebruikerswachtwoorden. Om consistent gedrag en universele beveiliging van wachtwoorden te garanderen, moet de DC-agentsoftware op alle domeincontrollers in een domein worden geïnstalleerd.

Veel organisaties zullen zorgvuldig willen testen van Azure AD-wachtwoordbeveiliging op een subset van hun domeincontrollers voordat ze een volledige implementatie uitvoeren. Azure AD-wachtwoordbeveiliging ondersteunt gedeeltelijke implementatie, dat wil zeggen dat de DC-agentsoftware op een bepaalde DC wachtwoorden actief valideert, zelfs wanneer andere DC's in het domein de DC-agentsoftware niet hebben geïnstalleerd. Gedeeltelijke implementaties van dit type zijn NIET veilig en worden NIET aanbevolen, behalve voor testdoeleinden.

## <a name="architectural-diagram"></a>Architecturaal diagram

Het is belangrijk om de onderliggende ontwerp- en functieconcepten te begrijpen voordat u Azure AD-wachtwoordbeveiliging implementeert in een on-premises Active Directory-omgeving. In het volgende diagram ziet u hoe de onderdelen van wachtwoordbeveiliging samenwerken:

![Hoe azure AD-wachtwoordbeveiligingscomponenten samenwerken](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* De Azure AD Password Protection Proxy-service wordt uitgevoerd op elke door een domein verbonden machine in het huidige Active Directory-forest. Het primaire doel is het doorsturen van wachtwoordbeleidsdownloadaanvragen van domeincontrollers naar Azure AD. Vervolgens worden de antwoorden van Azure AD teruggegeven aan de domeincontroller.
* Het wachtwoordfilter DLL van de DC-agent ontvangt verzoeken om wachtwoordvalidatie van gebruikers van het besturingssysteem. Het stuurt ze door naar de DC Agent-service die lokaal wordt uitgevoerd op de domeincontroller.
* De DC Agent-service voor wachtwoordbeveiliging ontvangt aanvragen voor wachtwoordvalidatie van het wachtwoordfilter DLL van de DC-agent. Het verwerkt ze met behulp van het huidige (lokaal beschikbare) wachtwoordbeleid en retourneert het resultaat: *doorgeven* of *mislukken.*

## <a name="how-password-protection-works"></a>Hoe wachtwoordbeveiliging werkt

Elke Azure AD Password Protection Proxy-serviceinstance adverteert zichzelf bij de domeincontrollers in het forest door een **serviceConnectionPoint-object** te maken in Active Directory.

Elke DC Agent-service voor wachtwoordbeveiliging maakt ook een **serviceConnectionPoint-object** in Active Directory. Dit object wordt voornamelijk gebruikt voor rapportage en diagnostiek.

De DC Agent-service is verantwoordelijk voor het starten van het downloaden van een nieuw wachtwoordbeleid van Azure AD. De eerste stap is het zoeken naar een Azure AD Password Protection Proxy-service door het forest op te vragen voor **proxyserviceConnectionPoint-objecten.** Wanneer een beschikbare proxyservice wordt gevonden, stuurt de DC-agent een downloadverzoek voor wachtwoordbeleid naar de proxyservice. De proxyservice stuurt het verzoek op zijn beurt naar Azure AD. De proxyservice retourneert vervolgens het antwoord op de DC-agentservice.

Nadat de DC Agent-service een nieuw wachtwoordbeleid van Azure AD heeft ontvangen, slaat de service het beleid op in een speciale map aan de basis van het delen van de *domeinsysvol-map.* De DC Agent-service controleert deze map ook voor het geval nieuwere beleidsregels worden gerepliceerd vanuit andere DC-agentservices in het domein.

De DC Agent-service vraagt altijd een nieuw beleid aan bij het opstarten van de service. Nadat de DC Agent-service is gestart, controleert deze de leeftijd van het huidige lokaal beschikbare beleid per uur. Als het beleid ouder is dan een uur, vraagt de DC-agent een nieuw beleid van Azure AD aan via de proxyservice, zoals eerder beschreven. Als het huidige beleid niet ouder is dan een uur, blijft de DC-agent dat beleid gebruiken.

Wanneer een Azure AD-wachtwoordbeveiligingswachtwoordbeleid wordt gedownload, is dat beleid specifiek voor een tenant. Met andere woorden, wachtwoordbeleid is altijd een combinatie van de lijst met algemene verboden wachtwoorden van Microsoft en de lijst met aangepaste verboden wachtwoorden per tenant.

De DC-agent communiceert met de proxyservice via RPC via TCP. De proxyservice luistert naar deze aanroepen op een dynamische of statische RPC-poort, afhankelijk van de configuratie.

De DC-agent luistert nooit op een netwerk-beschikbare poort.

De proxyservice belt nooit de DC Agent-service.

De proxyservice is stateloos. Het caches nooit beleid of een andere staat gedownload van Azure.

De DC Agent-service gebruikt altijd het meest recente lokaal beschikbare wachtwoordbeleid om het wachtwoord van een gebruiker te evalueren. Als er geen wachtwoordbeleid beschikbaar is op de lokale DC, wordt het wachtwoord automatisch geaccepteerd. Wanneer dat gebeurt, wordt een gebeurtenisbericht geregistreerd om de beheerder te waarschuwen.

Azure AD-wachtwoordbeveiliging is geen realtime beleidstoepassingsengine. Er kan een vertraging optreden tussen het moment waarop een wijziging van de wachtwoordbeleidsconfiguratie wordt aangebracht in Azure AD en wanneer die wijziging alle domeincontrollers bereikt en afgedwongen.

Azure AD-wachtwoordbeveiliging fungeert als aanvulling op het bestaande Active Directory-wachtwoordbeleid, niet als vervanging. Dit geldt ook voor alle andere 3rd-party wachtwoord filter dlls die kunnen worden geïnstalleerd. Active Directory vereist altijd dat alle wachtwoordvalidatiecomponenten akkoord gaan voordat ze een wachtwoord accepteren.

## <a name="foresttenant-binding-for-password-protection"></a>Forest/tenant binding voor wachtwoordbeveiliging

Voor de implementatie van Azure AD-wachtwoordbeveiliging in een Active Directory-forest is registratie van dat forest met Azure AD vereist. Elke proxyservice die wordt geïmplementeerd, moet ook worden geregistreerd bij Azure AD. Deze forest- en proxyregistraties zijn gekoppeld aan een specifieke Azure AD-tenant, die impliciet wordt geïdentificeerd door de referenties die worden gebruikt tijdens de registratie.

Het Active Directory-forest en alle geïmplementeerde proxyservices binnen een forest moeten bij dezelfde tenant zijn geregistreerd. Er wordt geen ondersteuning voor dat een Active Directory-forest of proxyservices in dat forest is geregistreerd bij verschillende Azure AD-tenants. Symptomen van een dergelijke verkeerd geconfigureerde implementatie zijn het onvermogen om wachtwoordbeleid te downloaden.

## <a name="download"></a>Download

De twee vereiste agentinstallateurs voor Azure AD-wachtwoordbeveiliging zijn beschikbaar in het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Volgende stappen
[Wachtwoordbeveiliging in Azure AD implementeren](howto-password-ban-bad-on-premises-deploy.md)
