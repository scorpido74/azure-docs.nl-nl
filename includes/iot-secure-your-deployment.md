---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748907"
---
Dit artikel biedt het volgende detailniveau voor het beveiligen van de IoT-infrastructuur (Internet of Things) (Azure IoT). Het koppelt aan details op implementatieniveau voor het configureren en implementeren van elk onderdeel. Het biedt ook vergelijkingen en keuzes tussen verschillende concurrerende methoden.

Het beveiligen van de Azure IoT-implementatie kan worden onderverdeeld in de volgende drie beveiligingsgebieden:

* **Apparaatbeveiliging:** het beveiligen van het IoT-apparaat terwijl het in het wild wordt geïmplementeerd.

* **Verbindingsbeveiliging:** ervoor zorgen dat alle gegevens die tussen het IoT-apparaat en de IoT-hub worden verzonden, vertrouwelijk en sabotagebestendig zijn.

* **Cloudbeveiliging:** een middel om gegevens te beveiligen terwijl deze worden verplaatst en wordt opgeslagen in de cloud.

![Drie veiligheidszones](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Veilige inrichting van apparaten en verificatie

De IoT-oplossingsversnellers beveiligen IoT-apparaten met de volgende twee methoden:

* Door het verstrekken van een unieke identiteitssleutel (beveiligingstokens) voor elk apparaat, die kan worden gebruikt door het apparaat om te communiceren met de IoT Hub.

* Door een [X.509-certificaat](https://www.itu.int/rec/T-REC-X.509-201210-S) op het apparaat en een privésleutel te gebruiken als een middel om het apparaat te verifiëren naar de IoT Hub. Deze verificatiemethode zorgt ervoor dat de privésleutel op het apparaat op geen enkel moment buiten het apparaat bekend is, wat een hoger beveiligingsniveau biedt.

De beveiligingstokenmethode biedt verificatie voor elk gesprek dat door het apparaat naar IoT Hub wordt gemaakt door de symmetrische sleutel aan elke oproep te koppelen. X.509-gebaseerde verificatie maakt verificatie van een IoT-apparaat op de fysieke laag mogelijk als onderdeel van de TLS-verbindingsinstelling. De op beveiliging token gebaseerde methode kan worden gebruikt zonder de X.509-verificatie, wat een minder veilig patroon is. De keuze tussen de twee methoden wordt voornamelijk bepaald door hoe veilig de apparaatverificatie moet zijn en de beschikbaarheid van veilige opslag op het apparaat (om de privésleutel veilig op te slaan).

## <a name="iot-hub-security-tokens"></a>IoT Hub-beveiligingstokens

IoT Hub gebruikt beveiligingstokens om apparaten en services te verifiëren om te voorkomen dat sleutels op het netwerk worden verzonden. Bovendien zijn beveiligingstokens beperkt in tijdgeldigheid en reikwijdte. Azure IoT SDKs genereren automatisch tokens zonder speciale configuratie. Sommige scenario's vereisen echter dat de gebruiker beveiligingstokens rechtstreeks genereert en gebruikt. Deze scenario's omvatten het directe gebruik van de MQTT-, AMQP- of HTTP-oppervlakken of de implementatie van het tokenservicepatroon.

Meer details over de structuur van het beveiligingstoken en het gebruik ervan vindt u in de volgende artikelen:

* [Structuur voor beveiligingstoken](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [SAS-tokens als apparaat gebruiken](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Elke IoT-hub heeft een [identiteitsregister](../articles/iot-hub/iot-hub-devguide-identity-registry.md) dat kan worden gebruikt om bronnen per apparaat in de service te maken, zoals een wachtrij met in-flight cloud-to-device-berichten en om toegang te geven tot de apparaatgerichte eindpunten. Het IoT Hub-identiteitsregister biedt veilige opslag van apparaatidentiteiten en beveiligingssleutels voor een oplossing. Individuele of groepen apparaatidentiteiten kunnen worden toegevoegd aan een lijst met toegestane personen of een bloklijst, zodat volledige controle over de toegang tot het apparaat mogelijk is. De volgende artikelen geven meer details over de structuur van het identiteitsregister en ondersteunde bewerkingen.

[IoT Hub ondersteunt protocollen zoals MQTT, AMQP en HTTP.](../articles//iot-hub/iot-hub-devguide-security.md) Elk van deze protocollen gebruikt beveiligingstokens van het IoT-apparaat naar IoT Hub op een andere manier:

* AMQP: SASL PLAIN en AMQP`{policyName}@sas.root.{iothubName}` Claims-gebaseerde beveiliging (met IoT-hub-level tokens; `{deviceId}` met device-scoped tokens).

* MQTT: CONNECT-pakket `{deviceId}` `{ClientId}`wordt `{IoThubhostname}/{deviceId}` gebruikt als het veld **Gebruikersnaam** en een SAS-token in het veld **Wachtwoord.**

* HTTP: Geldig token bevindt zich in de koptekst autorisatieaanvraag.

Het iot-hub-identiteitsregister kan worden gebruikt om beveiligingsreferenties per apparaat en toegangscontrole te configureren. Als een IoT-oplossing echter al een aanzienlijke investering heeft in een [aangepast apparaatidentiteitsregister en/of verificatieschema,](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)kan deze worden geïntegreerd in een bestaande infrastructuur met IoT Hub door een tokenservice te maken.

### <a name="x509-certificate-based-device-authentication"></a>Verificatie op basis van x.509-certificaten

Het gebruik van een [x.509-certificaat op basis](../articles/iot-hub/iot-hub-devguide-security.md) van een apparaat en het bijbehorende privé- en openbare sleutelpaar maakt extra verificatie op de fysieke laag mogelijk. De privésleutel wordt veilig in het apparaat opgeslagen en is niet buiten het apparaat te ontdekken. Het X.509-certificaat bevat informatie over het apparaat, zoals apparaat-id, en andere organisatorische gegevens. Een handtekening van het certificaat wordt gegenereerd met behulp van de privésleutel.

Inrichten van apparaten op hoog niveau:

* Een id koppelen aan een fysiek apparaat – apparaatidentiteit en/of X.509-certificaat dat aan het apparaat is gekoppeld tijdens de productie of inbedrijfstelling van het apparaat.

* Maak een overeenkomstige identiteitsvermelding in IoT Hub – apparaatidentiteit en bijbehorende apparaatgegevens in het IoT Hub-identiteitsregister.

* Bewaar de duimafdruk van het X.509-certificaat veilig in het identiteitsregister van IoT Hub.

### <a name="root-certificate-on-device"></a>Rootcertificaat op apparaat

Terwijl het IoT-apparaat een veilige TLS-verbinding met IoT Hub tot stand heeft gebracht, verifieert het IoT-hub met behulp van een rootcertificaat dat deel uitmaakt van de SDK van het apparaat. Voor de C-client SDK bevindt het\\\\certificaat zich onder de map "c certs" onder de wortel van de repo. Hoewel deze rootcertificaten van lange duur zijn, kunnen ze nog steeds verlopen of worden ingetrokken. Als er geen manier is om het certificaat op het apparaat bij te werken, kan het apparaat mogelijk geen verbinding maken met de IoT Hub (of een andere cloudservice). Het hebben van een middel om het hoofdcertificaat bij te werken zodra het IoT-apparaat is geïmplementeerd, vermindert dit risico effectief.

## <a name="securing-the-connection"></a>De verbinding beveiligen

De internetverbinding tussen het IoT-apparaat en de IoT Hub is beveiligd met behulp van de TLS-standaard (Transport Layer Security). Azure IoT ondersteunt [TLS 1.2,](https://tools.ietf.org/html/rfc5246)TLS 1.1 en TLS 1.0 in deze volgorde. Ondersteuning voor TLS 1.0 is alleen beschikbaar voor achterwaartse compatibiliteit. Controleer [TLS-ondersteuning in IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) om te zien hoe u uw hub configureert om TLS 1.2 te gebruiken, omdat deze de meeste beveiliging biedt.

## <a name="securing-the-cloud"></a>De cloud beveiligen

Azure IoT Hub maakt de definitie van [toegangsbeheerbeleid](../articles/iot-hub/iot-hub-devguide-security.md) voor elke beveiligingssleutel mogelijk. Het maakt gebruik van de volgende set machtigingen om toegang te verlenen tot elk van de eindpunten van IoT Hub. Met behulp van bevoegdheden kunt u toegang tot een IoT Hub beperken op basis van de functionaliteit.

* **RegisterLees**. Verleent leestoegang tot het identiteitsregister. Zie [identiteitsregister](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie .

* **RegisterReadWrite**. Subsidies lezen en schrijven toegang tot het identiteitsregister. Zie [identiteitsregister](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie .

* **ServiceConnect**. Verleent toegang tot cloudservicegerichte communicatie- en monitoringeindpunten. Het verleent bijvoorbeeld toestemming voor back-end cloudservices om device-to-cloud-berichten te ontvangen, cloud-to-device-berichten te verzenden en de bijbehorende leveringsbevestigingen op te halen.

* **DeviceConnect**. Verleent toegang tot apparaatgerichte eindpunten. Het verleent bijvoorbeeld toestemming om device-to-cloud-berichten te verzenden en berichten van cloud naar apparaat te ontvangen. Deze toestemming wordt gebruikt door apparaten.

Er zijn twee manieren om **DeviceConnect-machtigingen** te verkrijgen met IoT Hub met [beveiligingstokens:](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)met behulp van een apparaatidentiteitssleutel of een gedeelde toegangssleutel. Bovendien is het belangrijk op te merken dat alle functionaliteit toegankelijk vanaf apparaten `/devices/{deviceId}`wordt blootgesteld door het ontwerp op eindpunten met voorvoegsel .

[Serviceonderdelen kunnen alleen beveiligingstokens genereren](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) met behulp van beleid voor gedeelde toegang waarbij de juiste machtigingen worden verleend.

Azure IoT Hub en andere services die deel uitmaken van de oplossing, maken het mogelijk om gebruikers te beheren die de Azure Active Directory gebruiken.

Gegevens die door Azure IoT Hub worden ingenomen, kunnen worden verbruikt door verschillende services, zoals Azure Stream Analytics en Azure blob-opslag. Deze services bieden toegang tot het beheer. Lees meer over deze services en beschikbare opties:

* [Azure Cosmos DB:](https://azure.microsoft.com/services/cosmos-db/)een schaalbare, volledig geïndexeerde databaseservice voor semi-gestructureerde gegevens die metagegevens beheert voor de apparaten die u indient, zoals kenmerken, configuratie en beveiligingseigenschappen. Azure Cosmos DB biedt krachtige en krachtige verwerking, schema-agnostische indexering van gegevens en een uitgebreide SQL-queryinterface.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Real-time streamprocessing in de cloud waarmee u snel een low-cost analyseoplossing ontwikkelen en implementeren om realtime inzichten van apparaten, sensoren, infrastructuur en toepassingen te ontdekken. De gegevens van deze volledig beheerde service kunnen worden geschaald naar elk volume terwijl ze nog steeds een hoge doorvoer, lage latentie en tolerantie bereiken.

* [Azure App Services:](https://azure.microsoft.com/services/app-service/)een cloudplatform om krachtige web- en mobiele apps te bouwen die overal verbinding maken met gegevens; in de cloud of on-premises. Bouw aantrekkelijke mobiele apps voor iOS, Android en Windows. Integreer met uw Software as a Service (SaaS) en bedrijfsapplicaties met out-of-the-box connectiviteit met tientallen cloudservices en bedrijfsapplicaties. Code in uw favoriete taal en IDE (.NET, Node.js, PHP, Python of Java) om web-apps en API's sneller dan ooit te bouwen.

* [Logic Apps:](https://azure.microsoft.com/services/app-service/logic/)De logic apps-functie van Azure App Service helpt uw IoT-oplossing te integreren in uw bestaande bedrijfssystemen en workflowprocessen te automatiseren. Logic Apps stelt ontwikkelaars in staat om workflows te ontwerpen die uitgaan van een trigger en vervolgens een reeks stappen uit te voeren: regels en acties die krachtige connectors gebruiken om te integreren met uw bedrijfsprocessen. Logic Apps biedt kant-en-klare connectiviteit met een uitgebreid ecosysteem van SaaS- en cloudtoepassingen.

* [Azure Blob-opslag:](https://azure.microsoft.com/services/storage/)betrouwbare, economische cloudopslag voor de gegevens die uw apparaten naar de cloud verzenden.

## <a name="conclusion"></a>Conclusie

In dit artikel vindt u een overzicht van de details op implementatieniveau voor het ontwerpen en implementeren van een IoT-infrastructuur met Azure IoT. Het configureren van elk onderdeel om veilig te zijn is essentieel voor het beveiligen van de algehele IoT-infrastructuur. De ontwerpkeuzes die beschikbaar zijn in Azure IoT bieden enige mate van flexibiliteit en keuze; Elke keuze kan echter gevolgen hebben voor de veiligheid. Het wordt aanbevolen om elk van deze keuzes te evalueren door middel van een risico/kostenbeoordeling.
