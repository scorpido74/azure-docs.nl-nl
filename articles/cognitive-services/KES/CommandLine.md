---
title: Opdracht regel interface-Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Gebruik de opdracht regel interface om index-en grammatica bestanden te bouwen op basis van gestructureerde gegevens en implementeer deze vervolgens als webservices.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220103"
---
# <a name="command-line-interface"></a>Opdracht regel interface

De Knowledge Exploration Service-opdracht regel interface (KES) biedt de mogelijkheid om index-en grammatica bestanden te bouwen op basis van gestructureerde gegevens en deze te implementeren als webservices.  Hierbij wordt gebruikgemaakt van de algemene syntaxis: `kes.exe <command> <required_args> [<optional_args>]`.  U kunt `kes.exe` zonder argumenten uitvoeren om een lijst met opdrachten weer te geven of `kes.exe <command>` om een lijst met beschik bare argumenten weer te geven voor de opgegeven opdracht.  Hieronder vindt u een lijst met beschik bare opdrachten:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="build_index-command"></a>build_index opdracht

De **build_index** opdracht bouwt een binair index bestand van een schema definitie bestand en een gegevens bestand van objecten die moeten worden geïndexeerd.  Het resulterende index bestand kan worden gebruikt voor het evalueren van gestructureerde query-expressies of voor het genereren van interpretaties van query's in natuurlijke taal in combi natie met een gecompileerd grammatica bestand.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parameter      | Beschrijving               |
|----------------|---------------------------|
| `<schemaFile>` | Pad naar invoer schema |
| `<dataFile>`   | Pad naar invoer gegevens   |
| `<indexFile>`  | Pad naar uitvoer index |
| `--description <description>` | Beschrijvings teken reeks |
| `--remote <vmSize>`           | Grootte van de virtuele machine voor de externe build |

Deze bestanden kunnen worden opgegeven door lokale bestands paden of URL-paden naar Azure-blobs.  Het schema bestand beschrijft de structuur van de objecten die worden geïndexeerd en de bewerkingen die moeten worden ondersteund (Zie [schema-indeling](SchemaFormat.md)).  Het gegevens bestand inventariseert de objecten en kenmerk waarden die moeten worden geïndexeerd (Zie [gegevens indeling](DataFormat.md)).  Wanneer de build slaagt, bevat het uitvoer index bestand een gecomprimeerde weer gave van de invoer gegevens die de gewenste bewerkingen ondersteunen.  

Er kan optioneel een beschrijvings teken reeks worden opgegeven om een binaire index vervolgens te identificeren met behulp van de **describe_index** opdracht.  

De index is standaard gebaseerd op de lokale computer.  Buiten de Azure-omgeving zijn lokale builds beperkt tot gegevens bestanden met Maxi maal 10.000 objecten.  Wanneer de vlag--Remote is opgegeven, wordt de index gebaseerd op een tijdelijk gemaakte Azure-VM van de opgegeven grootte.  Hierdoor kunnen grote indexen efficiënt worden gebouwd met behulp van virtuele Azure-machines met meer geheugen.  Om paginering te voor komen waardoor het bouw proces wordt vertraagd, raden we u aan om een virtuele machine te gebruiken met 3 keer de hoeveelheid RAM-geheugen als de grootte van het invoer gegevens bestand.  Kijk voor een lijst met beschikbare VM-grootten op [Grootten voor virtuele machines](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Voor snellere builds kunt u de objecten in het gegevens bestand voorsorteren door de kans te verminderen.

<a name="build_grammar-command"></a>

## <a name="build_grammar-command"></a>build_grammar opdracht

De **build_grammar** opdracht compileert een grammatica die is opgegeven in XML naar een binair grammatica bestand.  Het resulterende grammatica bestand kan worden gebruikt in combi natie met een index bestand voor het genereren van interpretaties van query's in natuurlijke taal.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parameter       | Beschrijving               |
|-----------------|---------------------------|
| `<xmlFile>`     | Pad naar XML-specificatie voor invoer |
| `<grammarFile>` | Pad naar gecompileerde grammatica-uitvoer         |

Deze bestanden kunnen worden opgegeven door lokale bestands paden of URL-paden naar Azure-blobs.  De specificatie grammatica beschrijft de set met expressies met een gewogen natuurlijke taal en hun semantische interpretaties (Zie de [grammatica-indeling](GrammarFormat.md)).  Wanneer de build slaagt, bevat het bestand met de uitvoer een binaire weer gave van de grammatica specificatie om snelle decodering mogelijk te maken.

<a name="host_service-command"/>

## <a name="host_service-command"></a>host_service opdracht

De **host_service** -opdracht fungeert als host voor een exemplaar van de Kes-service op de lokale computer.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parameter       | Beschrijving                |
|-----------------|----------------------------|
| `<grammarFile>` | Binair pad van invoer         |
| `<indexFile>`   | Pad naar binaire index invoer           |
| `--port <port>` | Nummer van de lokale poort.  Standaard: 8000 |

Deze bestanden kunnen worden opgegeven door lokale bestands paden of URL-paden naar Azure-blobs.  Er wordt een webservice gehost op http://localhost:&lt;p sorteren&gt;/.  Bekijk [Web-api's](WebAPI.md) voor een lijst met ondersteunde bewerkingen.

Buiten de Azure-omgeving zijn lokale gehoste services beperkt tot het indexeren van bestanden met een grootte van Maxi maal 1 MB, 10 aanvragen per seconde en totaal aantal aanroepen van 1000.  Als u deze beperkingen wilt overwinnen, voert u **host_service** uit binnen een Azure-VM of implementeert u deze in een Azure-Cloud service met behulp van **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deploy_service-command"></a>deploy_service opdracht

De **deploy_service** -opdracht implementeert een exemplaar van de Kes-service naar een Azure-Cloud service.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parameter       | Beschrijving                  |
|-----------------|------------------------------|
| `<grammarFile>` | Binair pad van invoer           |
| `<indexFile>`   | Pad naar binaire index invoer             |
| `<serviceName>` | Naam van de doel-Cloud service |
| `<vmSize>`      | Grootte van Cloud service-VM     |
| `--slot <slot>` | Cloud service-sleuf: ' staging ' (standaard), ' productie ' |

Deze bestanden kunnen worden opgegeven door lokale bestands paden of URL-paden naar Azure-blobs.  Met Service naam wordt een vooraf geconfigureerde Azure-Cloud service opgegeven (Zie [een Cloud service maken en implementeren](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)) voor meer informatie.  Met de opdracht wordt de KES-service automatisch geïmplementeerd naar de opgegeven Azure-Cloud service, met behulp van Vm's van de opgegeven grootte.  We raden u aan een virtuele machine met 1 GB meer RAM-geheugen te gebruiken dan de grootte van het invoer index bestand om te voor komen dat de prestaties aanzienlijk afnemen.  Zie [grootten voor Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md)voor een lijst met beschik bare VM-grootten.

De service wordt standaard geïmplementeerd naar de faserings omgeving, eventueel overschreven via de para meter--sleuf.  Bekijk [Web-api's](WebAPI.md) voor een lijst met ondersteunde bewerkingen.

<a name="describe_index-command"/>

## <a name="describe_index-command"></a>describe_index opdracht

De **describe_index** opdracht voert informatie uit over een index bestand, met inbegrip van het schema en de beschrijving.

`kes.exe describe_index <indexFile>`

| Parameter     | Beschrijving      |
|---------------|------------------|
| `<indexFile>` | Pad van invoer index |

Dit bestand kan worden opgegeven door een lokaal bestandspad of een URL-pad naar een Azure-Blob.  De teken reeks voor de uitvoer beschrijving kan worden opgegeven met behulp van de para meter--Description van de opdracht **build_index** .

<a name="describe_grammar-command"/>

## <a name="describe_grammar-command"></a>describe_grammar opdracht

De **describe_grammar** -opdracht voert de oorspronkelijke grammatica specificatie uit die is gebruikt om de binaire grammatica te maken.

`kes.exe describe_grammar <grammarFile>`

| Parameter       | Beschrijving      |
|-----------------|------------------|
| `<grammarFile>` | Pad van invoer-grammatica |

Dit bestand kan worden opgegeven door een lokaal bestandspad of een URL-pad naar een Azure-Blob.

