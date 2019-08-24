---
title: OPC-kluis architectuur-Azure | Microsoft Docs
description: OPC kluis certificaat beheer service-architectuur
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9331473402ddd22180df3b404824969360d48164
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995819"
---
# <a name="opc-vault-architecture"></a>OPC-kluis architectuur

Dit artikel bevat een overzicht van de **OPC kluis micro service** en de **OPC kluis IOT Edge-module**.

## <a name="overview"></a>Overzicht

OPC UA-toepassingen gebruiken toepassings exemplaar certificaten om beveiliging op toepassings niveau te bieden. Er wordt een beveiligde verbinding tot stand gebracht met behulp van asymmetrische crypto grafie, waarvoor de toepassings certificaten het combi natie van open bare en persoonlijke sleutels bevatten. De certificaten kunnen zelf worden ondertekend of kunnen worden ondertekend door een certificerings instantie (CA).

Een OPC UA-toepassing heeft een lijst met vertrouwde certificaten die de vertrouw bare toepassingen vertegenwoordigen. Deze certificaten kunnen zelfondertekend zijn, ondertekend door een certificerings instantie of kunnen een basis-CA of een subca zijn. Als een vertrouwd certificaat deel uitmaakt van een grotere certificaat keten, vertrouwt de toepassing alle certificaten die zijn gekoppeld aan het certificaat in de lijst met vertrouwens relaties, zolang de volledige certificaat keten kan worden gevalideerd.

Het belangrijkste verschil tussen het vertrouwen van zelfondertekende certificaten en het vertrouwen van een CA-certificaat is de installatie-inspanning die nodig is voor het implementeren en onderhouden van vertrouwen en de extra inspanningen om een bedrijfsspecifieke CA te hosten. 

Als u het vertrouwen wilt distribueren voor zelfondertekende certificaten voor n servers met één client toepassing, moeten alle n server toepassings certificaten worden geïnstalleerd in de lijst met vertrouwens relaties van de client en moet het client toepassings certificaat op alle zijn geïnstalleerd Lijsten met vertrouwde server toepassingen. Deze administratieve inspanning is een zware belasting en voegt nog meer toe wanneer de levens duur van het certificaat moet worden overwogen en de certificaten moeten worden vernieuwd.

Het gebruik van een bedrijfsspecifieke CA kan het beheer van vertrouwen met meerdere-servers en-clients aanzienlijk vereenvoudigen. In dit geval genereert de beheerder een ondertekend exemplaar van het certificaat van de CERTIFICERINGs instantie voor elke client en server die wordt gebruikt. Daarnaast wordt het CA-certificaat geïnstalleerd in elke lijst met vertrouwde toepassingen, op alle servers en clients. Met deze aanpak moeten alleen verlopen certificaten worden vernieuwd en vervangen voor de betrokken toepassingen.

De Azure Industrial IoT OPC-service voor het beheer van certificaten is onze oplossing voor het beheren van een bedrijfsspecifieke CA voor OPC UA-toepassingen die zijn gebaseerd op de OPC-kluis micro service.

OPC-kluis biedt een micro service voor het hosten van een bedrijfsspecifieke CA in een beveiligde Cloud, die wordt ondersteund door door Azure AD beveiligde services met Azure Key Vault met Hardware Security modules, Cosmos DB en optioneel ook IoT Hub als toepassings archief.

De OPC kluis micro service is ontworpen ter ondersteuning van op rollen gebaseerde werk stroom waarbij geen persoonlijke aanvragen zijn ondertekend, en waarbij beveiligings beheerders en goed keurders met aanmeldings rechten in Azure Key Vault deze aanvragen goed keuren of afwijzen.

Voor compatibiliteit met bestaande OPC UA-oplossingen op basis van GDS, omvat de Services ondersteuning voor een met de module OPC kluis micro service ondersteunde Edge, waarmee de *OPC UA Global Discovery-server en de certificaat beheer* interface worden geïmplementeerd die moeten worden gedistribueerd certificaten en vertrouwens lijsten volgens het deel 12 van de specificatie. Op basis van onze kennis wordt deze GDS-Server interface echter nog veel gebruikt en heeft deze nog beperkte functionaliteit (rol van lezer). [Op aanvraag zullen we de ervaring op het verzoek van de klant verbeteren (*)](#yet-unsupported-features).

## <a name="architecture"></a>Architectuur

De architectuur is gebaseerd op de OPC kluis micro service met een OPC kluis IoT Edge module voor het fabrieks netwerk en een web-voor beeld-UX om de werk stroom te beheren:

![OPCVault-architectuur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC-kluis micro service

De OPC kluis micro service bestaat uit de volgende interfaces voor het implementeren van de werk stroom voor het distribueren en beheren van een bedrijfsspecifieke CA voor OPC UA-toepassingen:

### <a name="application"></a>Toepassing 
- Een ' OPC UA-toepassing ' kan een server of een client zijn of beide. OPC-kluis fungeert in dit geval als een registratie-instantie voor toepassingen. 
- Naast de basis bewerkingen voor het registreren, bijwerken en ongedaan maken van de registratie van toepassingen, zijn er ook interfaces om toepassingen met zoek expressies te vinden en er query's op uit te zoeken. 
- De certificaat aanvragen moeten verwijzen naar een geldige toepassing om een aanvraag te kunnen verwerken en een ondertekend certificaat met alle OPC UA-specifieke uitbrei dingen uit te geven. 
- De toepassings service wordt ondersteund door een CosmosDB-data base of het [OpcTwin-apparaatregistratie (*)](#yet-unsupported-features), afhankelijk van de configuratie van de klant.

### <a name="certificate-group"></a>Certificaat groep
- Een certificaat groep is een entiteit, waarin een basis-CA of een subca-certificaat wordt opgeslagen, inclusief de persoonlijke sleutel voor het ondertekenen van certificaten. 
- De lengte van de RSA-sleutel, de SHA-2-hash-lengte en de levens duur kunnen worden geconfigureerd voor zowel de verlenende CA als de ondertekende toepassings certificaten. 
- Meerdere groepen kunnen in één service worden gehost om toekomstige uitbrei dingen met certificaat groepen van HTTPS, gebruikers of ECC-algoritmen [(*)](#yet-unsupported-features)toe te staan. 
- De CA-certificaten worden opgeslagen in Azure Key Vault ondersteund met een HSM (Hardware Security modules) van FIPS 140-2 level 2. De persoonlijke sleutel verlaat nooit de beveiligde opslag omdat het ondertekenen wordt uitgevoerd door een AzureAD-beveiligde Key Vault bewerking. 
- De CA-certificaten kunnen na verloop van tijd worden verlengd en blijven aanwezig in veilige opslag vanwege Key Vault geschiedenis. 
- De intrekkings lijst voor elk CA-certificaat wordt ook opgeslagen in Key Vault als een geheim. Zodra de registratie van een toepassing ongedaan is gemaakt, wordt het toepassings certificaat ook ingetrokken in de CRL door een beheerder.
- Het uitvoeren van batches en het intrekken van één certificaat wordt ondersteund.

### <a name="certificate-request"></a>Certificaat aanvraag
Een certificaat aanvraag implementeert de werk stroom voor het genereren van een nieuw sleutel paar of een ondertekend certificaat met behulp van een certificaat handtekening aanvraag (CSR) voor een OPC UA-toepassing. 
- De aanvraag wordt opgeslagen in een Data Base met bijbehorende informatie zoals het onderwerp of een CSR en een verwijzing naar de OPC UA-toepassing. 
- De bedrijfs logica in de service valideert de aanvraag met de informatie die is opgeslagen in de toepassings database. De URI van de toepassing in de data base moet bijvoorbeeld overeenkomen met de toepassings-URI in de CSR.
- Een beveiligings beheerder met de machtiging ondertekenen (rol van goed keurder) keurt de aanvraag goed of weigert deze. Als de aanvraag is goedgekeurd, worden er een nieuw sleutel paar en/of een ondertekend certificaat gegenereerd. De nieuwe persoonlijke sleutel wordt in de sleutel kluis opgeslagen wanneer het nieuwe ondertekende open bare certificaat wordt opgeslagen in de data base van de certificaat aanvraag.
- De aanvrager kan de aanvraag status controleren totdat deze is goedgekeurd of ingetrokken. Als de aanvraag is goedgekeurd, kunnen de persoonlijke sleutel en het certificaat worden gedownload en geïnstalleerd in het certificaat archief van de OPC UA-toepassing.
- De aanvrager kan nu de aanvraag accepteren om overbodige gegevens uit de aanvraag database te verwijderen. 

Gedurende de levens duur van een ondertekend certificaat kan een toepassing worden verwijderd of kan een sleutel worden aangetast. In een dergelijk geval kan een CA-beheerder:
- Een toepassing verwijderen die tegelijkertijd alle in behandeling zijnde en goedgekeurde certificaat aanvragen van de app verwijdert. 
- Een andere mogelijkheid is om slechts één certificaat aanvraag te verwijderen als alleen een sleutel wordt vernieuwd of is aangetast.
- Er zijn nu geïnfecteerde goedgekeurde en geaccepteerde certificaat aanvragen gemarkeerd als verwijderd.
- Een manager kan regel matig een verlenging van de CA-CRL van de verlener uitvoeren. Op het moment dat het vernieuwen van alle verwijderde certificaat aanvragen wordt ingetrokken en de serie nummers van het certificaat worden toegevoegd aan de intrekkings lijst van de CRL. Ingetrokken certificaat aanvragen worden als ingetrokken gemarkeerd.
- In dringende gebeurtenissen kunnen ook enkele certificaat aanvragen worden ingetrokken.
- Ten slotte zijn de bijgewerkte Crl's beschikbaar voor distributie naar de deelnemende OPC UA-clients en-servers.

Zie Swagger documentation of the micro service (Engelstalig) voor meer informatie over de OPC kluis micro Service-API.

## <a name="opc-vault-iot-edge-module"></a>OPC-kluis IoT Edge module
Ter ondersteuning van een Factory Network Global Discovery-server kan de OPC kluis module aan de rand worden geïmplementeerd, worden uitgevoerd als een lokale .net core-toepassing of kan worden gestart in een docker-container. Vanwege een gebrek aan ondersteuning voor Auth2-verificatie in de huidige OPC UA .NET Standard-stack is de functionaliteit van de module OPC kluis Edge beperkt tot een rol van lezer, omdat een gebruiker niet kan worden geïmiteerd vanuit de module Edge naar de micro service met behulp van de OPC UA GDS Standard  Interface. Alleen bewerkingen, waarvoor de rol schrijver, beheerder of fiatteur niet vereist zijn, zijn op dit moment[(*)](#yet-unsupported-features)toegestaan. 

## <a name="yet-unsupported-features"></a>Nog niet-ondersteunde functies

**(*)** nog niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Nu u over de OPC-kluis architectuur hebt geleerd, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [OPC-kluis bouwen en implementeren](howto-opc-vault-deploy.md)
