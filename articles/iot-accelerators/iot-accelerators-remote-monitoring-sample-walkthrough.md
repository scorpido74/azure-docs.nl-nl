---
title: Overzicht van oplossings versneller externe bewaking-Azure | Microsoft Docs
description: Dit artikel bevat een overzicht van een aantal belang rijke elementen van de oplossing voor controle op afstand, zodat u kunt begrijpen hoe het werkt.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f501eb55f72811063ddf1d8e02a0ce2137d598f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80546319"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Overzicht van de verbetering voor de externe bewakingsoplossing

De [oplossings versneller](../iot-accelerators/about-iot-accelerators.md) voor externe controle implementeert een end-to-end bewakings oplossing voor meerdere machines op externe locaties. De oplossing combineert belangrijke Azure-services voor een algemene implementatie van het bedrijfsscenario. U kunt de oplossing gebruiken als uitgangspunt voor uw eigen implementatie en u kunt deze [aanpassen](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) aan uw eigen specifieke zakelijke vereisten.

In dit artikel wordt stapsgewijs beschreven hoe u een aantal van de belangrijkste elementen van de oplossing voor externe controle kunt gebruiken om te begrijpen hoe het werkt. Deze kennis helpt u bij:

* Het oplossen van problemen met de oplossing.
* Het plannen van een aanpassing van de oplossing zodat deze voldoet aan uw eigen specifieke vereisten.
* Het ontwerpen van uw eigen IoT-oplossing die gebruikmaakt van Azure-services.

De code voor de oplossing voor externe controle is beschikbaar op GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logische architectuur

In het volgende diagram ziet u een overzicht van de logische onderdelen van de oplossings versneller voor externe controle in de [IOT-architectuur](../iot-fundamentals/iot-introduction.md):

![Logische architectuur](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Waarom micro Services?

De Cloud architectuur is ontwikkeld sinds micro soft de eerste oplossings Accelerators heeft uitgebracht. Micro [Services](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) zijn opgehouden als bewezen prak tijken voor schaal baarheid en flexibiliteit zonder dat dit tot een betere ontwikkelings snelheid kan leiden. Diverse micro soft-Services gebruiken dit architectuur patroon intern met uitstekende betrouw baarheid en schaal baarheid. Met de bijgewerkte oplossings versnellers worden deze informatie in de praktijk opgenomen, zodat u er ook van kunt profiteren.

> [!TIP]
> Zie [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-toepassingsarchitectuur) en [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: een toepassingsrevolutie aangedreven door de cloud) voor meer informatie over microservicearchitectuur.

## <a name="device-connectivity"></a>Connectiviteit van apparaten

De oplossing bevat de volgende onderdelen in het apparaat connectiviteits onderdeel van de logische architectuur:

### <a name="real-devices"></a>Echte apparaten

U kunt echte apparaten verbinden met de oplossing. U kunt het gedrag van uw gesimuleerde apparaten implementeren met behulp van de Azure IoT-apparaat-Sdk's.

U kunt echte apparaten inrichten vanuit het dash board in de oplossings Portal.

### <a name="device-simulation-microservice"></a>Device simulatie micro service

De oplossing omvat de [device simulatie micro service](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) waarmee u een groep gesimuleerde apparaten kunt beheren vanuit de oplossings Portal om de end-to-end-stroom in de oplossing te testen. De gesimuleerde apparaten:

* Apparaat-naar-Cloud-telemetrie genereren.
* Reageren op aanroepen van de methode Cloud-naar-apparaat van IoT Hub.

De micro service biedt een REST-eind punt voor het maken, starten en stoppen van simulaties. Elke simulatie bestaat uit een set virtuele apparaten van verschillende typen die telemetrie verzenden en reageren op methode aanroepen.

U kunt gesimuleerde apparaten vanuit het dash board inrichten in de oplossings Portal.

### <a name="iot-hub"></a>IoT Hub

De [IOT-hub](../iot-hub/index.yml) verzendt telemetrie van de echte en gesimuleerde apparaten naar de Cloud. De IoT-hub maakt de telemetrie beschikbaar voor de services in de back-end van de IoT-oplossing voor verwerking.

De IoT Hub in de oplossing doet ook het volgende:

* Onderhoudt een id-REGI ster waarin de Id's en verificatie sleutels worden opgeslagen van alle apparaten die verbinding mogen maken met de portal.
* Roept namens de oplossings versneller methoden op uw apparaten aan.
* Onderhoudt apparaatdubbels voor alle geregistreerde apparaten. Een apparaatdubbel slaat de eigenschapswaarden op die door een apparaat worden gerapporteerd. Een apparaatdubbel slaat ook gewenste eigenschappen op die in de oplossingsportal zijn ingesteld, zodat het apparaat deze kan ophalen wanneer opnieuw verbinding wordt maakt.
* Plant taken voor het instellen van eigenschappen voor meerdere apparaten of voor het aanroepen van methoden op meerdere apparaten.

## <a name="data-processing-and-analytics"></a>Gegevensverwerking en -analyse

De oplossing bevat de volgende onderdelen van het onderdeel voor gegevens verwerking en-analyse van de logische architectuur:

### <a name="iot-hub-manager-microservice"></a>Micro service IoT Hub Manager

De oplossing bevat de [IOT hub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) -micro service voor het verwerken van interacties met uw IOT-hub, zoals:

* IoT-apparaten maken en beheren.
* Apparaatdubbels beheren.
* Methoden aanroepen op apparaten.
* IoT-referenties beheren.

Deze service voert ook IoT Hub query's uit om apparaten op te halen die horen bij door de gebruiker gedefinieerde groepen.

De micro service biedt een REST-eind punt voor het beheren van apparaten en apparaatdubbels, het aanroepen van methoden en het uitvoeren van IoT Hub query's.

### <a name="device-telemetry-microservice"></a>Telemetrie-service van apparaat

De [telemetrie-service](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) van het apparaat biedt een rest-eind punt voor lees toegang tot telemetrie van apparaten die zijn opgeslagen in time series Insights. Met het REST-eind punt kunnen ook ruwe bewerkingen worden uitgevoerd op regels en lees-/schrijftoegang tot alarm definities uit de opslag.

### <a name="storage-adapter-microservice"></a>Micro service Storage Adapter

De micro [service Storage-Adapter](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) beheert sleutel-waardeparen, de semantiek van de opslag service wordt abstract en een eenvoudige interface voor het opslaan van gegevens van elke indeling met behulp van Azure Cosmos db.

De waarden zijn ingedeeld in verzamelingen. U kunt aan afzonderlijke waarden werken of volledige verzamelingen ophalen. Complexe gegevens structuren worden geserialiseerd door de clients en worden beheerd als een eenvoudige tekst payload.

De service biedt een REST-eind punt voor ruwe bewerkingen op sleutel-waardeparen. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Implementaties van oplossings versneller gebruiken [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) om regels, waarschuwingen, configuratie-instellingen en alle andere koude opslag op te slaan.

### <a name="azure-stream-analytics-manager-microservice"></a>Micro service Azure Stream Analytics Manager

De micro [service Azure stream Analytics Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) beheert Azure stream Analytics (ASA)-taken, zoals het instellen van hun configuratie, starten en stoppen en de status ervan controleren.

De ASA-taak wordt ondersteund door twee referentie gegevens sets. Eén gegevens groep definieert regels en één definieert apparaatgroepen. De referentie gegevens van de regel worden gegenereerd op basis van de informatie die wordt beheerd door de telemetrie van het apparaat. De micro service Azure Stream Analytics Manager transformeert regels voor het verwerken van de telemetrie.

De verwijzings gegevens voor apparaatgroepen worden gebruikt om te bepalen welke groeps regels moeten worden toegepast op een binnenkomend telemetrie-bericht. De apparaatgroepen worden beheerd door de configuratie micro service en gebruiken dubbele query's van Azure IoT Hub device.

De ASA-taken leveren de telemetrie van de verbonden apparaten om te Time Series Insights voor opslag en analyse.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) is een engine voor gebeurtenis verwerking waarmee u grote hoeveel heden gegevens stromen van apparaten kunt controleren.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure time series Insights](https://docs.microsoft.com/azure/time-series-insights/) slaat de telemetrie op van de apparaten die zijn verbonden met de oplossings versneller. Daarnaast kunt u met deze functie telemetrie van apparaten visualiseren en query's uitvoeren in de webgebruikersinterface van de oplossing.

### <a name="configuration-microservice"></a>Micro service configureren

De [configuratie](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) -micro service biedt een rest-eind punt voor ruwe bewerkingen op apparaatgroepen, oplossings instellingen en gebruikers instellingen in de oplossings versneller. Het werkt met de micro service Storage Adapter om de configuratie gegevens te behouden.

### <a name="authentication-and-authorization-microservice"></a>Micro service voor verificatie en autorisatie

De micro [service verificatie en autorisatie](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) beheert de gebruikers die gemachtigd zijn voor toegang tot de oplossings versneller. Gebruikers beheer kan worden uitgevoerd met behulp van een ID-service provider die ondersteuning biedt voor [OpenID Connect Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Implementaties van oplossings versnelling gebruiken [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) als een OpenID Connect Connect-provider. Azure Active Directory gebruikers gegevens opslaat en certificaten biedt om JWT-token handtekeningen te valideren.

## <a name="presentation"></a>Stijl

De oplossing bevat de volgende onderdelen in het presentatie deel van de logische architectuur:

De [Web-gebruikers interface is een reagerende java script-toepassing](https://github.com/Azure/pcs-remote-monitoring-webui). De toepassing:

* Maakt gebruik van Java script reageert alleen en wordt volledig uitgevoerd in de browser.
* Is opgemaakt met CSS.
* Communiceert met open bare micro Services met behulp van AJAX-aanroepen.

De gebruikers interface bevat alle functionaliteit van de oplossings versnelling en communiceert met andere micro Services, zoals:

* De verificatie en autorisatie micro service voor het beveiligen van gebruikers gegevens.
* De micro service IoT Hub Manager om IoT-apparaten weer te geven en te beheren.

De gebruikers interface integreert de Azure Time Series Insights Explorer voor het inschakelen van query's en het analyseren van telemetrie van apparaten.

Met de configuratie-micro service kan de gebruikers interface configuratie-instellingen opslaan en ophalen.

## <a name="next-steps"></a>Volgende stappen

Als u de documentatie voor de bron code en de ontwikkel aars wilt verkennen, begint u met een van de twee GitHub-opslag plaatsen:

* [Oplossings versneller voor externe controle met Azure IOT (.net)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Oplossings versneller voor externe controle met Azure IOT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Gedetailleerde diagrammen voor oplossings architectuur:
* [Oplossings versneller voor architectuur van bewaking op afstand](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Zie [de oplossings versneller aanpassen](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)voor meer conceptuele informatie over de oplossings versneller voor externe controle.
