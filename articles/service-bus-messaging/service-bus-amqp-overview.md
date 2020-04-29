---
title: Overzicht van AMQP 1,0 in Azure Service Bus
description: Lees hoe Azure Service Bus Advanced Message Queueing Protocol (AMQP) ondersteunt, een open standaard protocol.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759309"
---
# <a name="amqp-10-support-in-service-bus"></a>Ondersteuning voor AMQP 1,0 in Service Bus
Zowel de Azure Service Bus Cloud service als on-premises [service bus voor Windows Server (Service Bus 1,1)](https://msdn.microsoft.com/library/dn282144.aspx) ondersteunen het AMQP (Advanced Message queueing Protocol) 1,0. Met AMQP kunt u platform onafhankelijke, hybride toepassingen bouwen met behulp van een open standaard protocol. U kunt toepassingen bouwen met behulp van onderdelen die zijn gebouwd met behulp van verschillende talen en frameworks, en die worden uitgevoerd op verschillende besturings systemen. Al deze onderdelen kunnen verbinding maken met Service Bus en gestructureerde bedrijfs berichten efficiënt en met volledige betrouw baarheid uitwisselen.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Inleiding: wat is AMQP 1,0 en waarom is het belang rijk?
Normaal gesp roken hebben op berichten georiënteerde middlewareproducten een eigen protocol gebruikt voor communicatie tussen client toepassingen en makelaars. Dit betekent dat als u de Messa ging-Broker van een bepaalde leverancier hebt geselecteerd, u de bibliotheken van die leverancier moet gebruiken om uw client toepassingen te verbinden met die Broker. Dit leidt tot een zekere mate van afhankelijkheid van die leverancier, omdat het voor de poort van een toepassing naar een ander product nood zakelijk is dat code wijzigingen in alle verbonden toepassingen worden aangebracht. 

Bovendien is het maken van de communicatie van Messa ging-Brokers van verschillende leveranciers lastig. Dit vereist meestal bridging op toepassings niveau om berichten van het ene naar het andere systeem te verplaatsen en te vertalen tussen hun eigen bericht indelingen. Dit is een algemene vereiste. bijvoorbeeld wanneer u een nieuwe, geïntegreerde interface moet bieden voor oudere systemen of als u de IT-systemen wilt integreren na een fusie.

De software-industrie is een bedrijf dat snel kan worden verplaatst. nieuwe programmeer talen en toepassings raamwerken worden in een soms bewildering tempo geïntroduceerd. Op dezelfde manier kunnen de vereisten van IT-systemen in de loop van tijd en ontwikkel aars profiteren van de nieuwste platform functies. Soms wordt deze platformen echter niet ondersteund door de leverancier van de geselecteerde berichten. Omdat berichten protocollen eigendom zijn, is het niet mogelijk dat anderen bibliotheken voor deze nieuwe platforms bieden. Daarom moet u benaderingen gebruiken zoals het maken van gateways of bruggen waarmee u het bericht product kunt blijven gebruiken.

De ontwikkeling van de Advanced Message Queueing Protocol (AMQP) 1,0 is gemotiveerd door deze problemen. Het is afkomstig van de Morgan-oplossing van JP, wie, zoals de meeste ondernemingen van financiële dienst verlening, zware gebruikers van de bericht georiënteerde middleware. Het doel is eenvoudig: om een open-standaard berichten protocol te maken dat het mogelijk maakt om op berichten gebaseerde toepassingen te bouwen met behulp van onderdelen die zijn gemaakt met verschillende talen, frameworks en besturings systemen, waarbij alles wordt gebruikt voor het gebruik van de beste onderdelen van een assortiment leveranciers.

## <a name="amqp-10-technical-features"></a>Technische functies van AMQP 1,0
AMQP 1,0 is een efficiënt, betrouwbaar, Wire-level berichten protocol dat u kunt gebruiken voor het bouwen van robuuste, platform onafhankelijke toepassingen voor bericht verwerking. Het protocol heeft een eenvoudig doel: voor het definiëren van de mechanismen van de veilige, betrouw bare en efficiënte overdracht van berichten tussen twee partijen. De berichten zelf worden gecodeerd met behulp van een draag bare gegevens weergave waarmee heterogene afzenders en ontvangers gestructureerde zakelijke berichten kunnen uitwisselen met volledige betrouw baarheid. Hier volgt een overzicht van de belangrijkste functies:

* **Efficiënt**: AMQP 1,0 is een verbindingsgeoriënteerd protocol dat gebruikmaakt van een binaire code ring voor de instructies van het protocol en de bedrijfs berichten die worden overgedragen. Het bevat geavanceerde schema's voor stroom beheer om het gebruik van het netwerk en de verbonden onderdelen te maximaliseren. Het protocol is zo ontworpen dat het een evenwicht heeft tussen efficiëntie, flexibiliteit en interoperabiliteit.
* **Betrouw bare**: met het AMQP 1,0-protocol kunnen berichten worden uitgewisseld met een bereik van betrouwbaarheids garanties, van brand en voor verg eten tot betrouw bare, precies eenmaal bevestigde levering.
* **Flexibel**: AMQP 1,0 is een flexibel protocol dat kan worden gebruikt om verschillende topologieën te ondersteunen. Hetzelfde protocol kan worden gebruikt voor communicatie van client naar client, client naar Broker en Broker-naar-Broker.
* **Broker-model onafhankelijk**: de AMQP 1,0-specificatie maakt geen vereisten voor het berichten model dat wordt gebruikt door een Broker. Dit betekent dat het mogelijk is om eenvoudig AMQP 1,0-ondersteuning toe te voegen aan bestaande Messa ging-brokers.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1,0 is een standaard (met een kapitaal)
AMQP 1,0 is een internationale standaard, goedgekeurd door ISO en IEC als ISO/IEC 19464:2014.

AMQP 1,0 is in ontwikkeling sinds 2008 door een kern groep van meer dan 20 bedrijven, zowel technologie leveranciers als eind gebruikers. Gedurende die tijd hebben gebruikers bedrijven hun eigen bedrijfs vereisten bijgedragen en hebben de technologie leveranciers het protocol ontwikkeld om aan deze vereisten te voldoen. Tijdens het hele proces hebben leveranciers gedeeld in workshops waarin ze zijn gewerkt om de interoperabiliteit tussen hun implementaties te valideren.

In oktober 2011 werd het ontwikkelings werk overgegaan naar een technisch comité binnen de organisatie voor de vervroeging van Structured Information Standards (OASIS) en de OASIS AMQP 1,0 Standard is uitgebracht in oktober 2012. De volgende ondernemingen hebben in het technisch comité gedeeld tijdens de ontwikkeling van de standaard:

* **Technologie leveranciers**: Axway software, Huawei Technologies, IIT software, INETCO Systems, Kaazing, micro soft, Mitre Corporation, Primeton Technologies, progressief software, Red Hat, Sita, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Gebruikers bedrijven**: Bank of America, Credit Suisse, Deutsche boerse, Goldman Sachs, JPMorgan-nadoende.

Enkele van de meest voorkomende voor delen van open standaarden zijn:

* Minder kans op het vergren delen van een leverancier
* Interoperabiliteit
* Brede Beschik baarheid van bibliotheken en hulpprogram ma's
* Bescherming tegen economische veroudering
* Beschik baarheid van deskundig personeel
* Lager en beheersbaar risico

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 en Service Bus
AMQP 1,0-ondersteuning in Azure Service Bus houdt in dat u nu kunt gebruikmaken van de Service Bus Queuing en Brokered Messaging functies publiceert/abonneert vanuit diverse platformen met behulp van een efficiënt binair protocol. Daarnaast kunt u toepassingen bouwen die bestaan uit onderdelen die zijn gebouwd met behulp van een combi natie van talen, frameworks en besturings systemen.

In de volgende afbeelding ziet u een voorbeeld implementatie waarbij Java-clients die worden uitgevoerd op Linux, worden geschreven met behulp van de standaard JMS-API (Java Message Service) en .NET-clients die worden uitgevoerd op Windows, Exchange-berichten via Service Bus met AMQP 1,0.

![][0]

**Afbeelding 1: voor beeld van implementatie scenario met verschillende platform berichten met behulp van Service Bus en AMQP 1,0**

Op dit moment zijn de volgende client bibliotheken bekend om samen te werken met Service Bus:

| Taal | Bibliotheek |
| --- | --- |
| Java |JMS-client (Apache Qpid Java Message Service)<br/>IIT software SwiftMQ Java-client |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-python |
| C# |AMQP .NET Lite |

**Afbeelding 2: tabel met AMQP 1,0-client bibliotheken**

## <a name="summary"></a>Samenvatting
* AMQP 1,0 is een open, betrouw bare berichten protocol dat u kunt gebruiken voor het bouwen van platformoverschrijdende, hybride toepassingen. AMQP 1,0 is een OASIS-standaard.
* AMQP 1,0-ondersteuning is nu beschikbaar in Azure Service Bus en Service Bus voor Windows Server (Service Bus 1,1). De prijs is hetzelfde als voor de bestaande protocollen.

## <a name="next-steps"></a>Volgende stappen
Wilt u meer weten? Ga naar de volgende koppelingen:

* [Service Bus van .NET gebruiken met AMQP]
* [Service Bus van Java gebruiken met AMQP]
* [Apache Qpid Proton-C op een Azure Linux-VM installeren]
* [AMQP in Service Bus voor Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Service Bus van .NET gebruiken met AMQP]: service-bus-amqp-dotnet.md
[Service Bus van Java gebruiken met AMQP]: service-bus-amqp-java.md
[Apache Qpid Proton-C op een Azure Linux-VM installeren]: service-bus-amqp-apache.md
[AMQP in Service Bus voor Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
