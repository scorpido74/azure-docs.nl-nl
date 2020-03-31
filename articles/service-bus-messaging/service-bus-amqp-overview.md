---
title: Overzicht van AMQP 1.0 in Azure Service Bus
description: Lees hoe Azure Service Bus Advanced Message Queuing Protocol (AMQP) ondersteunt, een open standaardprotocol.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50d21cfe8136b9c794eae5104bbb34e28f7c1661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759309"
---
# <a name="amqp-10-support-in-service-bus"></a>AMQP 1.0-ondersteuning in Service Bus
Zowel de Azure Service Bus-cloudservice als de on-premises [Service Bus voor Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) ondersteunen het Advanced Message Queueing Protocol (AMQP) 1.0. AMQP stelt u in staat om cross-platform, hybride applicaties te bouwen met behulp van een open standaard protocol. U toepassingen samenstellen met behulp van componenten die zijn gebouwd met behulp van verschillende talen en frameworks en die op verschillende besturingssystemen worden uitgevoerd. Al deze componenten kunnen verbinding maken met Service Bus en gestructureerde bedrijfsberichten efficiënt en op volledige trouw uitwisselen.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Inleiding: Wat is AMQP 1.0 en waarom is het belangrijk?
Traditioneel, bericht-georiënteerde middleware producten hebben gebruik gemaakt van eigen protocollen voor de communicatie tussen client applicaties en makelaars. Dit betekent dat zodra u de berichtenmakelaar van een bepaalde leverancier hebt geselecteerd, u de bibliotheken van die leverancier moet gebruiken om uw clienttoepassingen aan die broker te koppelen. Dit resulteert in een zekere afhankelijkheid van die leverancier, omdat het overzetten van een toepassing naar een ander product codewijzigingen vereist in alle aangesloten toepassingen. 

Bovendien is het lastig om berichtenmakelaars van verschillende leveranciers met elkaar te verbinden. Dit vereist meestal overbrugging op toepassingsniveau om berichten van het ene systeem naar het andere te verplaatsen en te vertalen tussen hun eigen berichtindelingen. Dit is een gemeenschappelijke eis; bijvoorbeeld wanneer u een nieuwe uniforme interface moet bieden aan oudere verschillende systemen of IT-systemen moet integreren na een fusie.

De software-industrie is een snel evoluerend bedrijf; nieuwe programmeertalen en toepassingskaders worden in een soms verbijsterend tempo geïntroduceerd. Op dezelfde manier evolueren de vereisten van IT-systemen in de loop van de tijd en willen ontwikkelaars profiteren van de nieuwste platformfuncties. Soms ondersteunt de geselecteerde berichtenleverancier deze platforms echter niet. Omdat messaging-protocollen eigendom zijn, is het niet mogelijk voor anderen om bibliotheken te bieden voor deze nieuwe platforms. Daarom moet u benaderingen gebruiken, zoals het bouwen van gateways of bruggen waarmee u het berichtenproduct blijven gebruiken.

De ontwikkeling van het Advanced Message Queuing Protocol (AMQP) 1.0 werd ingegeven door deze problemen. Het is ontstaan bij JP Morgan Chase, die, net als de meeste financiële dienstverleners, zijn zware gebruikers van message-georiënteerde middleware. Het doel was eenvoudig: het creëren van een open-standaard messaging protocol dat het mogelijk maakte om op berichten gebaseerde applicaties te bouwen met behulp van componenten gebouwd met behulp van verschillende talen, frameworks en besturingssystemen, allemaal met behulp van best-of-breed componenten uit een reeks van Leveranciers.

## <a name="amqp-10-technical-features"></a>AMQP 1.0 technische kenmerken
AMQP 1.0 is een efficiënt, betrouwbaar messaging-protocol op draadniveau dat u gebruiken om robuuste, cross-platform, messaging-toepassingen te bouwen. Het protocol heeft een eenvoudig doel: het definiëren van de mechanica van de veilige, betrouwbare en efficiënte overdracht van berichten tussen twee partijen. De berichten zelf zijn gecodeerd met behulp van een draagbare gegevensvertegenwoordiging waarmee heterogene afzenders en ontvangers gestructureerde zakelijke berichten kunnen uitwisselen op volledige trouw. Het volgende is een samenvatting van de belangrijkste kenmerken:

* **Efficiënt**: AMQP 1.0 is een verbindingsgeoriënteerd protocol dat een binaire codering gebruikt voor de protocolinstructies en de zakelijke berichten die erover worden overgedragen. Het bevat geavanceerde stroomregelingsschema's om het gebruik van het netwerk en de aangesloten componenten te maximaliseren. Dat gezegd hebbende, is het protocol ontworpen om een evenwicht te vinden tussen efficiëntie, flexibiliteit en interoperabiliteit.
* **Betrouwbaar**: Het AMQP 1.0 protocol maakt het mogelijk om berichten uit te wisselen met een reeks betrouwbaarheidsgaranties, van brand-and-forget tot betrouwbare, precies-eens erkende levering.
* **Flexibel**: AMQP 1.0 is een flexibel protocol dat kan worden gebruikt om verschillende topologieën te ondersteunen. Hetzelfde protocol kan worden gebruikt voor client-to-client, client-to-broker, en broker-to-broker communicatie.
* **Broker-model onafhankelijk**: De AMQP 1.0 specificatie stelt geen eisen aan het berichtenmodel dat door een makelaar wordt gebruikt. Dit betekent dat het mogelijk is om eenvoudig AMQP 1.0-ondersteuning toe te voegen aan bestaande berichtenmakelaars.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 is een standaard (met een hoofdletter 'S')
AMQP 1.0 is een internationale standaard, goedgekeurd door ISO en IEC als ISO/IEC 19464:2014.

AMQP 1.0 is sinds 2008 in ontwikkeling door een kerngroep van meer dan 20 bedrijven, zowel technologieleveranciers als eindgebruikersbedrijven. Gedurende die tijd hebben gebruikersbedrijven hun real-world business vereisten bijgedragen en de technologieleveranciers hebben het protocol ontwikkeld om aan deze vereisten te voldoen. Gedurende het hele proces hebben leveranciers deelgenomen aan workshops waarin ze hebben samengewerkt om de interoperabiliteit tussen hun implementaties te valideren.

In oktober 2011 werd het ontwikkelingswerk overgezet naar een technische commissie binnen de Organisatie voor de Bevordering van Gestructureerde Informatiestandaarden (OASIS) en werd in oktober 2012 de OASIS AMQP 1.0 Standard uitgebracht. De volgende ondernemingen namen aan het technisch comité deel tijdens de ontwikkeling van de norm:

* **Technologieleveranciers**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Gebruikersfirma's**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Enkele van de algemeen genoemde voordelen van open standaarden zijn:

* Minder kans op vendor lock-in
* Interoperabiliteit
* Brede beschikbaarheid van bibliotheken en tooling
* Bescherming tegen veroudering
* Beschikbaarheid van deskundig personeel
* Lager en beheersbaar risico

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 en Service Bus
AMQP 1.0-ondersteuning in Azure Service Bus betekent dat u nu gebruik maken van de servicebus die in de rij staat en brokered messaging-functies van een reeks platforms publiceert/abonneert met behulp van een efficiënt binair protocol. Bovendien u toepassingen bouwen die bestaan uit componenten die zijn gebouwd met behulp van een mix van talen, frameworks en besturingssystemen.

De volgende figuur illustreert een voorbeeldimplementatie waarin Java-clients die op Linux draaien, geschreven met behulp van de standaard Java Message Service (JMS) API en .NET-clients die op Windows draaien, berichten uitwisselen via Service Bus met AMQP 1.0.

![][0]

**Figuur 1: Voorbeeldimplementatiescenario met cross-platform berichten met Service Bus en AMQP 1.0**

Op dit moment is bekend dat de volgende clientbibliotheken werken met Service Bus:

| Taal | Bibliotheek |
| --- | --- |
| Java |Apache Qpid Java Message Service (JMS) client<br/>IIT Software SwiftMQ Java client |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .NET Lite |

**Figuur 2: Tabel van AMQP 1.0-clientbibliotheken**

## <a name="summary"></a>Samenvatting
* AMQP 1.0 is een open, betrouwbaar berichtenprotocol dat u gebruiken om cross-platform, hybride applicaties te bouwen. AMQP 1.0 is een OASIS standaard.
* AMQP 1.0-ondersteuning is nu beschikbaar in Azure Service Bus en Service Bus voor Windows Server (Service Bus 1.1). Prijzen zijn hetzelfde als voor de bestaande protocollen.

## <a name="next-steps"></a>Volgende stappen
Wilt u meer weten? Ga naar de volgende links:

* [Servicebus van .NET gebruiken met AMQP]
* [Servicebus uit Java gebruiken met AMQP]
* [Apache Qpid Proton-C installeren op een Azure Linux VM]
* [AMQP in servicebus voor Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Servicebus van .NET gebruiken met AMQP]: service-bus-amqp-dotnet.md
[Servicebus uit Java gebruiken met AMQP]: service-bus-amqp-java.md
[Apache Qpid Proton-C installeren op een Azure Linux VM]: service-bus-amqp-apache.md
[AMQP in servicebus voor Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
