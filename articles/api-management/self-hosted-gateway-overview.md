---
title: Overzicht van de zelf-hostende Azure API Management Gateway | Microsoft Docs
description: Meer informatie over hoe zelf-hostende Azure API Management-Gateway-organisaties de Api's in de omgevingen hybride en multicloud kunnen beheren.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73513716"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Overzicht van zelf-hostende API Management Gateway

In dit artikel wordt uitgelegd hoe zelf-hostende gateway functies hybride en multi-Cloud API management mogelijk maken, de architectuur op hoog niveau biedt en de belangrijkste mogelijkheden van het onderdeel zijn.

> [!NOTE]
> De zelf-hostende gateway functie is beschikbaar als preview-versie. Tijdens de preview-periode is de zelf-hostende gateway alleen beschikbaar in de lagen ontwikkelaar en Premium zonder extra kosten. De Developer-laag is beperkt tot één zelf-hostende gateway-implementatie.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybride en API-beheer met meerdere clouds

De zelf-hostende gateway functie breidt API Management ondersteuning uit voor hybride en omgevingen met meerdere clouds en stelt organisaties in staat om op efficiënte en veilige wijze Api's te beheren die on-premises en tussen Clouds worden gehost vanuit één API Management service in Azure.

Met de zelf-hostende gateway hebben klanten de flexibiliteit om een container versie van het onderdeel API Management Gateway te implementeren in dezelfde omgevingen waar ze hun Api's hosten. Alle zelf-hostende gateways worden beheerd vanuit de API Management-service waarmee ze zijn federatief, waardoor klanten de zicht baarheid en Unified management-ervaring bieden voor alle interne en externe Api's. Als u de gateways dicht bij de Api's plaatst, kunnen klanten het API-verkeer optimaliseren en voldoen aan de vereisten voor beveiliging en naleving.

Elke API Management-service bestaat uit de volgende belang rijke onderdelen:

-   Beheer vlak, beschikbaar als een API, dat wordt gebruikt om de service te configureren via de Azure Portal, Power shell en andere ondersteunde mechanismen.
-   De gateway (of het gegevens vlak) is verantwoordelijk voor de API-aanvragen van de proxy, het Toep assen van beleid en het verzamelen van telemetrie
-   Ontwikkelaars portal die door ontwikkel aars wordt gebruikt voor het detecteren, leren en onboarden van de Api's

Standaard worden al deze onderdelen geïmplementeerd in azure, waardoor alle API-verkeer (weer gegeven als effen zwarte pijlen op de onderstaande afbeelding) wordt uitgevoerd om door Azure te stromen, ongeacht waar de back-ends die de Api's implementeren, worden gehost. De operationele eenvoud van dit model is de kosten van verhoogde latentie, nalevings problemen en in sommige gevallen extra kosten voor gegevens overdracht.

![API-verkeers stroom zonder zelf-hostende gateways](media/self-hosted-gateway-overview/without-gateways.png)

Door zelf-hostende gateways in dezelfde omgevingen te implementeren als back-end-API-implementaties en toe te voegen aan de API Management-service, kan API-verkeer rechtstreeks naar de back-end-Api's stromen, waardoor de latentie wordt verbeterd, de kosten voor gegevens overdracht worden geoptimaliseerd en de voor delen van het beheer van alle Api's binnen de organisatie worden beheerd.

![API-verkeer stroom met zelf-hostende gateways](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Verpakking en functies

De zelf-hostende gateway is een container, functioneel equivalente versie van de beheerde gateway die is geïmplementeerd in azure als onderdeel van elke API Management service. De zelf-hostende gateway is beschikbaar als een op Linux gebaseerde docker-container vanuit de micro soft-Container Registry. Het kan worden geïmplementeerd voor docker, Kubernetes of een andere oplossing voor container indeling die wordt uitgevoerd op een desktop, Server cluster of Cloud infrastructuur.

> [!IMPORTANT]
> Sommige functies die beschikbaar zijn in de beheerde gateway, zijn nog niet beschikbaar in de preview-versie. Met name: log to Event hub-beleid, Service Fabric Integration, downstream HTTP/2. Er is geen plan om een ingebouwde cache beschikbaar te maken op de zelf-hostende gateway.

## <a name="connectivity-to-azure"></a>Connectiviteit met Azure

De zelf-hostende gateway vereist uitgaande TCP/IP-verbinding met Azure op poort 443. Elke zelf-hostende gateway moet worden gekoppeld aan een enkele API Management-service en via het beheer vlak worden geconfigureerd. Zelf-hostende gateway maakt gebruik van connectiviteit met Azure voor:

-   De status rapporteren door per minuut heartbeat-berichten te verzenden
-   Regel matig controleren op (elke 10 seconden) en configuratie-updates Toep assen wanneer deze beschikbaar zijn
-   Het verzenden van Logboeken en metrische gegevens voor aanvragen naar Azure Monitor, indien geconfigureerd om dit te doen
-   Gebeurtenissen verzenden naar Application Insights, indien ingesteld op...

Wanneer de verbinding met Azure is verbroken, kunnen zelf-hostende gateways geen configuratie-updates ontvangen, de status van het rapport rapporteren of telemetrie uploaden.

De zelf-hostende gateway is ontworpen om statisch te mislukken en kan het tijdelijke verlies van connectiviteit met Azure overlaten. Het kan worden geïmplementeerd met of zonder dat de lokale configuratie back-up is ingeschakeld. In het eerste geval worden zelf-hostende gateways regel matig een back-upkopie van de configuratie opgeslagen op een permanent volume dat is gekoppeld aan de container of pod.

Wanneer de configuratie back-up is uitgeschakeld en de verbinding met Azure wordt onderbroken:

-   Zelf-hostende gateways die worden uitgevoerd, blijven functioneren met behulp van een in-Memory kopie van de configuratie
-   Gestopt zelf-hostende gateways kunnen niet worden gestart

Wanneer configuratie back-up is ingeschakeld en de verbinding met Azure wordt onderbroken:

-   Zelf-hostende gateways die worden uitgevoerd, blijven functioneren met behulp van een in-Memory kopie van de configuratie
-   Gestopt zelf-hostende gateways gaan gebruikmaken van een back-up van de configuratie

Wanneer de verbinding wordt hersteld, zal elke zelf-hostende gateway die wordt beïnvloed door de storing automatisch opnieuw verbinding maken met de bijbehorende API Management-service en alle configuratie-updates downloaden die zijn opgetreden terwijl de gateway ' offline ' was.

## <a name="next-steps"></a>Volgende stappen

-   [Lees een technisch document voor aanvullende achtergrond informatie over dit onderwerp](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Zelf-hostende Gateway implementeren naar docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Zelf-hostende Gateway implementeren op Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
