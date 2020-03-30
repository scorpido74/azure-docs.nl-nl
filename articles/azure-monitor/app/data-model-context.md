---
title: Azure Application Insights Telemetry Data Model - Telemetriecontext | Microsoft Documenten
description: Contextgegevensmodel voor telemetrie van Application Insights
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8a2e3296643b13a54c4fceb11f044a2808cf2877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671860"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetriecontext: gegevensmodel Application Insights

Elk telemetrieitem kan een sterk getypte contextvelden hebben. Elk veld maakt een specifiek monitoringscenario mogelijk. Gebruik de verzameling aangepaste eigenschappen om aangepaste of toepassingsspecifieke contextuele informatie op te slaan.


## <a name="application-version"></a>Toepassingsversie

Informatie in de contextvelden van toepassingen gaat altijd over de toepassing die de telemetrie verzendt. Toepassingsversie wordt gebruikt om trendwijzigingen in het toepassingsgedrag en de correlatie met de implementaties te analyseren.

Maximale lengte: 1024


## <a name="client-ip-address"></a>IP-adres van client

Het IP-adres van het clientapparaat. IPv4 en IPv6 worden ondersteund. Wanneer telemetrie wordt verzonden vanuit een service, gaat de locatiecontext over de gebruiker die de bewerking in de service heeft gestart. Application Insights haalt de geolocatie-informatie uit het IP-adres van de client en truncate deze vervolgens af. Client IP kan dus niet alleen worden gebruikt als identificeerbare informatie van de eindgebruiker. 

Maximale lengte: 46


## <a name="device-type"></a>Apparaattype

Oorspronkelijk werd dit veld gebruikt om aan te geven welk type apparaat de eindgebruiker van de toepassing gebruikt. Tegenwoordig voornamelijk gebruikt om JavaScript-telemetrie te onderscheiden met het apparaattype 'Browser' van servertelemetrie met het apparaattype 'PC'.

Maximale lengte: 64


## <a name="operation-id"></a>Bedrijfs-id

Een unieke id van de hoofdbewerking. Met deze id u telemetrie groeperen over meerdere componenten. Zie [telemetriecorrelatie](../../azure-monitor/app/correlation.md) voor details. De bewerkings-id wordt gemaakt door een aanvraag of een paginaweergave. Alle andere telemetrie stelt dit veld in op de waarde voor de weergave met gegevens of paginaweergave. 

Maximale lengte: 128


## <a name="parent-operation-id"></a>Bovenliggende bewerking-id

De unieke id van de directe bovenliggende ouder van het telemetrieitem. Zie [telemetriecorrelatie](../../azure-monitor/app/correlation.md) voor details.

Maximale lengte: 128


## <a name="operation-name"></a>Naam van bewerking

De naam (groep) van de bewerking. De naam van de bewerking wordt gemaakt door een aanvraag of een paginaweergave. Alle andere telemetrie-items stellen dit veld in op de waarde voor de weergave met gegevens of paginaweergave. De naam van de bewerking wordt gebruikt voor het vinden van alle telemetrie-items voor een groep bewerkingen (bijvoorbeeld 'GET Home/Index'). Deze contexteigenschap wordt gebruikt om vragen te beantwoorden als 'wat zijn de typische uitzonderingen die op deze pagina worden gegooid'.

Maximale lengte: 1024


## <a name="synthetic-source-of-the-operation"></a>Synthetische bron van de operatie

Naam van synthetische bron. Sommige telemetrie van de toepassing kan synthetisch verkeer vertegenwoordigen. Het kan webcrawler zijn die de website indexeert, tests voor sitebeschikbaarheid of traces van diagnostische bibliotheken zoals Application Insights SDK zelf.

Maximale lengte: 1024


## <a name="session-id"></a>Sessie-id

Sessie-ID - het voorbeeld van de interactie van de gebruiker met de app. Informatie in de contextvelden van de sessie gaat altijd over de eindgebruiker. Wanneer telemetrie wordt verzonden vanuit een service, gaat de sessiecontext over de gebruiker die de bewerking in de service heeft gestart.

Maximale lengte: 64


## <a name="anonymous-user-id"></a>Anonieme gebruikersnaam

Anonieme gebruikersnaam. Vertegenwoordigt de eindgebruiker van de toepassing. Wanneer telemetrie wordt verzonden vanuit een service, gaat de gebruikerscontext over de gebruiker die de bewerking in de service heeft gestart.

[Sampling](../../azure-monitor/app/sampling.md) is een van de technieken om de hoeveelheid verzamelde telemetrie te minimaliseren. Sampling algoritme probeert te monsterin- of out alle gecorreleerde telemetrie. Anonieme gebruikersnaam wordt gebruikt voor het genereren van samplingscore. Dus anonieme gebruikersnaam moet een willekeurige waarde genoeg. 

Het gebruik van anonieme gebruikersnaam om de gebruikersnaam op te slaan is een misbruik van het veld. Gebruik geverifieerde gebruikersnaam.

Maximale lengte: 128


## <a name="authenticated-user-id"></a>Geverifieerde gebruikersnaam

Geverifieerde gebruikersnaam. Het tegenovergestelde van anonieme gebruikersnaam, dit veld vertegenwoordigt de gebruiker met een vriendelijke naam. Sinds de PII-informatie wordt het niet standaard verzameld door de meeste SDK.

Maximale lengte: 1024


## <a name="account-id"></a>Account-id

In multi-tenant toepassingen is dit de account-ID of naam, waarmee de gebruiker handelt. Voorbeelden hiervan zijn abonnements-ID voor Azure-portal of blognaam voor een blogplatform.

Maximale lengte: 1024


## <a name="cloud-role"></a>Cloudrol

Naam van de rol waar de toepassing deel van uitmaakt. Hiermee wordt rechtstreeks toegewezen aan de rolnaam in azure. Kan ook worden gebruikt om micro-diensten te onderscheiden, die deel uitmaken van een enkele toepassing.

Maximale lengte: 256


## <a name="cloud-role-instance"></a>Cloudrolinstantie

Naam van de instantie waarin de toepassing wordt uitgevoerd. Computernaam voor on-premises, instantienaam voor Azure.

Maximale lengte: 256


## <a name="internal-sdk-version"></a>Intern: SDK-versie

SDK-versie. Zie [dit artikel](https://github.com/microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) voor informatie.

Maximale lengte: 64


## <a name="internal-node-name"></a>Intern: naam knooppunt

Dit veld vertegenwoordigt de naam van het knooppunt dat wordt gebruikt voor factureringsdoeleinden. Gebruik het om de standaarddetectie van knooppunten te overschrijven.

Maximale lengte: 256


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uitbreiden en filteren van telemetrie](../../azure-monitor/app/api-filtering-sampling.md).
- Zie [gegevensmodel](data-model.md) voor toepassingsinzichten en gegevensmodel.
- Bekijk de configuratie van de [verzameling](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)van standaardeigenschappen .
