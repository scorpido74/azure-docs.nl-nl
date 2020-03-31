---
title: Overzicht van remote monitoring-oplossingsversnellers - Azure | Microsoft Documenten
description: In dit artikel vindt u een overzicht van enkele van de belangrijkste elementen van de oplossing voor externe bewaking, zodat u begrijpen hoe het werkt.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 33005bc286f7dc2c0ebed74bd9df0309e71346f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824837"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Overzicht van de verbetering voor de externe bewakingsoplossing

De Remote Monitoring [oplossingsversneller](../iot-accelerators/about-iot-accelerators.md) implementeert een end-to-end monitoring oplossing voor meerdere machines op afgelegen locaties. De oplossing combineert belangrijke Azure-services voor een algemene implementatie van het bedrijfsscenario. U kunt de oplossing gebruiken als uitgangspunt voor uw eigen implementatie en u kunt deze [aanpassen](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) aan uw eigen specifieke zakelijke vereisten.

In dit artikel vindt u enkele van de belangrijkste elementen van de oplossing voor externe bewaking, zodat u begrijpen hoe het werkt. Deze kennis helpt u bij:

* Het oplossen van problemen met de oplossing.
* Het plannen van een aanpassing van de oplossing zodat deze voldoet aan uw eigen specifieke vereisten.
* Het ontwerpen van uw eigen IoT-oplossing die gebruikmaakt van Azure-services.

De remote monitoring oplossingsversnellercode is beschikbaar op GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logische architectuur

In het volgende diagram worden de logische componenten beschreven van de versneller van de oplossing voor externe bewaking die is bedekt met de [IoT-architectuur:](../iot-fundamentals/iot-introduction.md)

![Logische architectuur](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Waarom microservices?

Cloudarchitectuur is geëvolueerd sinds Microsoft de eerste oplossingsversnellers uitbracht. [Microservices](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) zijn een bewezen praktijk gebleken om schaal en flexibiliteit te bereiken zonder in te boeten aan ontwikkelingssnelheid. Verschillende Microsoft-services gebruiken dit architecturale patroon intern met grote betrouwbaarheids- en schaalbaarheidsresultaten. De bijgewerkte oplossingsversnellers brengen deze learnings in de praktijk, zodat u er ook van profiteren.

> [!TIP]
> Zie [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-toepassingsarchitectuur) en [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: een toepassingsrevolutie aangedreven door de cloud) voor meer informatie over microservicearchitectuur.

## <a name="device-connectivity"></a>Connectiviteit van apparaten

De oplossing bevat de volgende componenten in het apparaatconnectiviteitsonderdeel van de logische architectuur:

### <a name="real-devices"></a>Echte apparaten

U echte apparaten aansluiten op de oplossing. U het gedrag van uw gesimuleerde apparaten implementeren met de SDK's van Azure IoT-apparaat.

U echte apparaten inrichten vanuit het dashboard in het oplossingsportaal.

### <a name="device-simulation-microservice"></a>Microservice voor apparaatsimulatie

De oplossing omvat de microservice voor [apparaatsimulatie](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) waarmee u een pool van gesimuleerde apparaten vanaf de oplossingsportal beheren om de end-to-end stroom in de oplossing te testen. De gesimuleerde apparaten:

* Genereer telemetrie tussen apparaat en cloud.
* Reageer op oproepen van de cloud-to-device-methode vanuit IoT Hub.

De microservice biedt een RESTful-eindpunt voor u om simulaties te maken, te starten en te stoppen. Elke simulatie bestaat uit een set virtuele apparaten van verschillende types, die telemetrie verzenden en reageren op methodeoproepen.

U gesimuleerde apparaten inrichten vanuit het dashboard in de oplossingsportal.

### <a name="iot-hub"></a>IoT Hub

De [IoT-hub](../iot-hub/index.yml) neemt telemetrie in die van zowel de echte als de gesimuleerde apparaten naar de cloud wordt verzonden. De IoT-hub maakt de telemetrie beschikbaar voor de services in de backend van de IoT-oplossing voor verwerking.

De IoT Hub in de oplossing doet ook het volgende:

* Houdt een identiteitsregister bij dat de id's en verificatiesleutels van alle apparaten opslaat die verbinding mogen maken met de portal.
* Roept methoden op uw apparaten in opdracht van de oplossingsversneller.
* Onderhoudt apparaatdubbels voor alle geregistreerde apparaten. Een apparaatdubbel slaat de eigenschapswaarden op die door een apparaat worden gerapporteerd. Een apparaatdubbel slaat ook gewenste eigenschappen op die in de oplossingsportal zijn ingesteld, zodat het apparaat deze kan ophalen wanneer opnieuw verbinding wordt maakt.
* Plant taken voor het instellen van eigenschappen voor meerdere apparaten of voor het aanroepen van methoden op meerdere apparaten.

## <a name="data-processing-and-analytics"></a>Gegevensverwerking en -analyse

De oplossing omvat de volgende componenten in het deel van de gegevensverwerking en analyse van de logische architectuur:

### <a name="iot-hub-manager-microservice"></a>IoT Hub manager microservice

De oplossing omvat de [IoT Hub manager microservice](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) voor het afhandelen van interacties met uw IoT-hub, zoals:

* IoT-apparaten maken en beheren.
* Het beheren van apparaat tweelingen.
* Het aanroepen van methoden op apparaten.
* IoT-referenties beheren.

Met deze service worden ook IoT Hub-query's uitgevoerd om apparaten op te halen die behoren tot door de gebruiker gedefinieerde groepen.

De microservice biedt een RESTful-eindpunt voor het beheren van apparaten en apparaattweelingen, het aanroepen van methoden en het uitvoeren van IoT Hub-query's.

### <a name="device-telemetry-microservice"></a>Microservice voor telemetrie van het apparaat

De [microservice voor telemetrie van het apparaat](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) biedt een RESTful-eindpunt voor leestoegang tot apparaattelemetrie die is opgeslagen in Time Series Insights. Het RESTful-eindpunt maakt ook CRUD-bewerkingen mogelijk op het beheer van regels en lees-/schrijftoegang voor alarmdefinities uit opslag.

### <a name="storage-adapter-microservice"></a>Microservice voor opslagadapter

De [microservice voor opslagadapter](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) beheert sleutelwaardeparen, abstraheert de semantiek van de opslagservice en presenteert een eenvoudige interface om gegevens van elke indeling op te slaan met Azure Cosmos DB.

Waarden worden georganiseerd in collecties. U werken aan afzonderlijke waarden of hele collecties ophalen. Complexe gegevensstructuren worden geserialiseerd door de clients en beheerd als eenvoudige tekst payload.

De service biedt een RESTful-eindpunt voor CRUD-bewerkingen op sleutelparen. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Implementaties van oplossingsversneller gebruiken [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) om regels, waarschuwingen, configuratie-instellingen en alle andere koudeopslag op te slaan.

### <a name="azure-stream-analytics-manager-microservice"></a>Microservice voor Azure Stream Analytics-manager

De [Azure Stream Analytics manager microservice](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) beheert Azure Stream Analytics (ASA)-taken, waaronder het instellen van hun configuratie, het starten en stoppen ervan en het bewaken van hun status.

De ASA-taak wordt ondersteund door twee referentiegegevenssets. Eén gegevensset definieert regels en één definieert apparaatgroepen. De referentiegegevens van de regels worden gegenereerd op basis van de informatie die wordt beheerd door de microservice voor telemetrie van het apparaat. De microservice azure stream analytics manager transformeert telemetrieregels in streamverwerkingslogica.

De referentiegegevens van het apparaat groepen worden gebruikt om te bepalen welke groep regels moet worden toegepast op een binnenkomend telemetriebericht. De apparaatgroepen worden beheerd door de configuratiemicroservice en gebruiken twee query's voor Azure IoT Hub-apparaten.

De ASA-taken leveren de telemetrie van de verbonden apparaten aan Time Series Insights voor opslag en analyse.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) is een gebeurtenisverwerkingsengine waarmee u grote hoeveelheden gegevensstreaming vanaf apparaten onderzoeken.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) slaat de telemetrie op vanaf de apparaten die zijn aangesloten op de oplossingsversneller. Het maakt het ook mogelijk om telemetrie van apparaten te visualiseren en op te vragen in de gebruikersinterface van het oplossingsweb.

> [!NOTE]
> Time Series Insights is momenteel niet beschikbaar in de Azure China-cloud. Nieuwe remote monitoring oplossing sversneller implementaties in de Azure China cloud gebruik Cosmos DB voor alle opslag.

### <a name="configuration-microservice"></a>Configuratiemicroservice

De [configuratiemicroservice](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) biedt een RESTful-eindpunt voor CRUD-bewerkingen op apparaatgroepen, oplossingsinstellingen en gebruikersinstellingen in de oplossingsversneller. Het werkt met de microservice van de opslagadapter om de configuratiegegevens voort te zetten.

### <a name="authentication-and-authorization-microservice"></a>Microservice voor verificatie en autorisatie

De [microservice voor verificatie en autorisatie](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) beheert de gebruikers die gemachtigd zijn om toegang te krijgen tot de oplossingsversneller. Gebruikersbeheer kan worden gedaan met behulp van elke identity service provider die [OpenId Connect](https://openid.net/connect/)ondersteunt.

### <a name="azure-active-directory"></a>Azure Active Directory

Implementaties van oplossingsversneller gebruiken [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) als OpenID Connect-provider. Azure Active Directory slaat gebruikersgegevens op en biedt certificaten om JWT-tokenhandtekeningen te valideren.

## <a name="presentation"></a>Stijl

De oplossing bevat de volgende componenten in het presentatiegedeelte van de logische architectuur:

De [webuser interface is een React Javascript applicatie.](https://github.com/Azure/pcs-remote-monitoring-webui) De aanvraag:

* Gebruikt alleen Javascript React en draait volledig in de browser.
* Is gestyled met CSS.
* Communiceert met publieke gerichte microservices via AJAX-gesprekken.

De gebruikersinterface presenteert alle functionaliteit voor oplossingsversneller en werkt samen met andere microservices zoals:

* De microservice voor verificatie en autorisatie om gebruikersgegevens te beschermen.
* De IoT Hub manager microservice om de IoT-apparaten op te sommen en te beheren.

De gebruikersinterface integreert de Azure Time Series Insights explorer om query's en analyse van apparaattelemetrie mogelijk te maken.

Met de configuratiemicroservice kan de gebruikersinterface configuratie-instellingen opslaan en ophalen.

## <a name="next-steps"></a>Volgende stappen

Als u de broncode en de documentatie van ontwikkelaars wilt verkennen, begint u met een van de twee GitHub-repositories:

* [Oplossingsversneller voor externe bewaking met Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Oplossingsversneller voor externe bewaking met Azure IoT (Java).](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Gedetailleerde oplossingenarchitectuurdiagrammen:
* [Oplossingsversneller voor remote monitoring-architectuur.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)

Zie [De oplossingsversneller aanpassen](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)voor meer conceptuele informatie over de accelerator voor externe bewaking.
