---
title: Overzicht van zelfgehoste Azure API Management-gateway | Microsoft Documenten
description: Ontdek hoe de zelfgehoste Azure API Management-gateway organisaties helpt api's te beheren in de hybride en multicloudomgevingen.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513716"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Overzicht van zelfgehoste API Management-gateway

In dit artikel wordt uitgelegd hoe de zelfgehoste gatewayfunctie hybride en multi-cloud API-beheer mogelijk maakt, de architectuur op hoog niveau presenteert en de fundamentele mogelijkheden ervan belicht.

> [!NOTE]
> Zelf gehoste gateway-functie is in preview. Tijdens de preview is de zelf gehoste gateway alleen beschikbaar in de lagen Developer en Premium zonder extra kosten. De ontwikkelaarslaag is beperkt tot één zelf gehoste gateway-implementatie.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybride en multi-cloud API-beheer

De zelfgehoste gatewayfunctie breidt api-beheerondersteuning voor hybride en multi-cloudomgevingen uit en stelt organisaties in staat om API's die on-premises en over clouds worden gehost, efficiënt en veilig te beheren vanuit één API-beheerservice in Azure.

Met de zelfgehoste gateway hebben klanten de flexibiliteit om een gecontaineriseerde versie van de API Management-gatewaycomponent te implementeren naar dezelfde omgevingen waar ze hun API's hosten. Alle zelf gehoste gateways worden beheerd vanuit de API Management-service waarmee ze worden gefedereerd, waardoor klanten de zichtbaarheid en uniforme beheerervaring krijgen voor alle interne en externe API's. Door de gateways dicht bij de API's te plaatsen, kunnen klanten API-verkeersstromen optimaliseren en beveiligings- en nalevingsvereisten aanpakken.

Elke API Management-service bestaat uit de volgende belangrijke onderdelen:

-   Beheervlak, dat wordt weergegeven als een API, wordt gebruikt om de service te configureren via de Azure-portal, PowerShell en andere ondersteunde mechanismen.
-   Gateway (of gegevensvlak) is verantwoordelijk voor het proxyn van API-aanvragen, het toepassen van beleid en het verzamelen van telemetrie
-   Ontwikkelaarsportal die door ontwikkelaars wordt gebruikt om de API's te ontdekken, te leren en aan boord te gebruiken

Standaard worden al deze componenten geïmplementeerd in Azure, waardoor al het API-verkeer (weergegeven als effen zwarte pijlen op de afbeelding hieronder) door Azure stroomt, ongeacht waar backends die de API's implementeren, worden gehost. De operationele eenvoud van dit model gaat ten koste van verhoogde latentie, nalevingsproblemen en in sommige gevallen extra kosten voor gegevensoverdracht.

![API-verkeersstroom zonder zelfgehoste gateways](media/self-hosted-gateway-overview/without-gateways.png)

Door zelfgehoste gateways te implementeren in dezelfde omgevingen als backend API-implementaties en deze toe te voegen aan de API-beheerservice, kan API-verkeer rechtstreeks naar de backend-API's stromen, wat de latentie verbetert, de kosten voor gegevensoverdracht optimaliseert en naleving met behoud van de voordelen van het hebben van één enkel punt van beheer en het ontdekken van alle API's binnen de organisatie, ongeacht waar de implementaties worden gehost.

![API-verkeersstroom met zelfgehoste gateways](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Verpakking en functies

De zelfgehoste gateway is een gecontaineriseerde, functioneel gelijkwaardige versie van de beheerde gateway die is geïmplementeerd in Azure als onderdeel van elke API-beheerservice. De zelfgehoste gateway is beschikbaar als een Docker-container op Basis van Linux uit het Microsoft Container Registry. Het kan worden geïmplementeerd in Docker, Kubernetes of een andere containerorchestration-oplossing die wordt uitgevoerd op een desktop- of servercluster of cloudinfrastructuur.

> [!IMPORTANT]
> Sommige functionaliteit die beschikbaar is in de beheerde gateway is nog niet beschikbaar in preview. Het meest in het bijzonder: Log to Event Hub beleid, Service Fabric integratie, downstream HTTP/2. Er is geen plan om een ingebouwde cache beschikbaar te maken in de zelfgehoste gateway.

## <a name="connectivity-to-azure"></a>Connectiviteit met Azure

De zelfgehoste gateway vereist uitgaande TCP/IP-connectiviteit met Azure op poort 443. Elke zelfgehoste gateway moet worden gekoppeld aan één API Management-service en wordt geconfigureerd via het beheervlak. Zelf gehoste gateway maakt gebruik van connectiviteit met Azure voor:

-   De status rapporteren door elke minuut hartslagberichten te verzenden
-   Regelmatig controleren op (elke 10 seconden) en het toepassen van configuratie-updates wanneer ze beschikbaar zijn
-   Het verzenden van aanvraaglogboeken en metrische gegevens naar Azure Monitor, indien geconfigureerd om dit te doen
-   Gebeurtenissen verzenden naar Application Insights, indien ingesteld om dit te doen

Wanneer de verbinding met Azure verloren gaat, kan de zelfgehoste gateway geen configuratie-updates ontvangen, de status ervan rapporteren of telemetrie uploaden.

De zelfgehoste gateway is ontworpen om "statisch te mislukken" en kan het tijdelijke verlies van connectiviteit met Azure overleven. Het kan worden geïmplementeerd met of zonder lokale configuratie back-up ingeschakeld. In het eerste geval worden zelf gehoste gateways regelmatig een back-up van de configuratie opgeslagen op een blijvend volume dat aan de container of pod is gekoppeld.

Wanneer de configuratieback-up is uitgeschakeld en de verbinding met Azure wordt onderbroken:

-   Zelf gehoste gateways die worden uitgevoerd, blijven functioneren met behulp van een in-memory kopie van de configuratie
-   Gestopte zelfgehoste gateways kunnen niet worden gestart

Wanneer de configuratieback-up is ingeschakeld en de verbinding met Azure wordt onderbroken:

-   Zelf gehoste gateways die worden uitgevoerd, blijven functioneren met behulp van een in-memory kopie van de configuratie
-   Gestopte zelfgehoste gateways gebruiken een back-up van de configuratie

Wanneer de verbinding is hersteld, maakt elke door de storing getroffen zelfgehoste gateway automatisch opnieuw verbinding met de bijbehorende API Management-service en downloadt u alle configuratie-updates die plaatsvonden terwijl de gateway 'offline' was.

## <a name="next-steps"></a>Volgende stappen

-   [Lees een whitepaper voor extra achtergrondinformatie over dit onderwerp](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Zelf gehoste gateway implementeren voor Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Zelf gehoste gateway naar Kubernetes implementeren](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
