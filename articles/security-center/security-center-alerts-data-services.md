---
title: Detectie van bedreigingen voor gegevens Services in Azure Security Center | Microsoft Docs
description: Dit onderwerp bevat de beschik bare Data Services-waarschuwingen in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: c69352b8ff11edfce8bd800a0d3aad4584557572
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621350"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detectie van bedreigingen voor gegevens Services in Azure Security Center

 Azure Security Center analyseert de logboeken van de services voor gegevens opslag en activeert waarschuwingen wanneer een bedreiging voor uw gegevens bronnen wordt gedetecteerd. In dit onderwerp vindt u de waarschuwingen die Security Center gegenereerd voor de volgende services:

* [Azure SQL Database en Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database en SQL Data Warehouse<a name="data-sql"></a>

SQL-bedreigings detectie identificeert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te gebruiken 

|Waarschuwing|Beschrijving|
|---|---|
|**Beveiligings probleem met SQL-injectie**|Een toepassing heeft een mislukte SQL-instructie gegenereerd in de data base. Dit kan duiden op een mogelijke kwets baarheid voor SQL-injectie aanvallen. Er zijn twee mogelijke redenen voor een defecte instructie. Een fout in de toepassings code kan de beschadigde SQL-instructie hebben opgebouwd. Het is ook mogelijk dat toepassings code of opgeslagen procedures geen gebruikers invoer opschonen bij het samen stellen van de foutieve SQL-instructie, die kan worden misbruikt voor SQL-injectie.|
|**Mogelijke SQL-injectie**|Er is een actieve crack opgetreden op basis van een geïdentificeerde toepassing die kwetsbaar is voor SQL-injectie. Dit betekent dat een aanvaller schadelijke SQL-instructies wil injecteren door gebruik te maken van de code of opgeslagen procedures voor een kwets bare toepassing.|
|**Toegang vanaf ongebruikelijke locatie**|Er is een wijziging in het toegangs patroon SQL Server, waarbij iemand zich vanaf een ongebruikelijke geografische locatie heeft aangemeld bij de server. In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (een voormalige werk nemer of externe aanvaller).|
|**Toegang vanaf onbekende Principal**|Het toegangs patroon is gewijzigd in SQL Server. Iemand heeft zich aangemeld bij de server met behulp van een ongewone principal (gebruiker). In sommige gevallen detecteert de waarschuwing een legitieme actie (een nieuwe toepassing of onderhoud door ontwikkelaars). In andere gevallen detecteert de waarschuwing een schadelijke actie (een voormalige werk nemer of externe aanvaller).|
|**Toegang via een mogelijk schadelijke toepassing**|Er is een mogelijk schadelijke toepassing gebruikt om toegang te krijgen tot de data base. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een aanval die gebruikmaakt van algemene hulpprogram ma's.|
|**SQL-referenties voor brute kracht**|Een abnormaal groot aantal mislukte aanmeldingen met andere referenties heeft plaatsgevonden. In sommige gevallen detecteert de waarschuwing het uitvoeren van testen om binnen te dringen. In andere gevallen detecteert de waarschuwing een beveiligings aanval.|

Zie [Azure SQL database Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)(Engelstalig) voor meer informatie over waarschuwingen voor detectie van SQL-bedreigingen. Bekijk met name de sectie bedreigingen detectie waarschuwingen. Kijk ook [hoe Azure Security Center een cyberattack kunt onthullen](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) om een voor beeld te zien van hoe Security Center schadelijke SQL-activiteit detectie gebruikt om een aanval te detecteren.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Geavanceerde bedreigings beveiliging voor opslag is momenteel alleen beschikbaar voor Blob-opslag.

Advanced Threat Protection voor Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen verhelpen zonder dat u een beveiligings expert hoeft te zijn en om beveiligings bewakings systemen te beheren.

Security Center analyseert Diagnostische logboeken met lees-, schrijf-en verwijder aanvragen naar Blob-opslag om bedreigingen te detecteren en genereert een waarschuwing wanneer afwijkingen in de activiteit optreden. Zie [Opslaganalyse logboek registratie configureren](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)voor meer informatie.

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**Afwijking van ongebruikelijke locatie toegang**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande Remote Desktop Protocol (RDP)-communicatie gedetecteerd, afkomstig van een bron in uw implementatie. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop duiden dat de bron is aangetast en wordt nu gebruikt om een extern RDP-eind punt aanval te forceren. Dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Afwijking van toepassings toegang**|Geeft aan dat een ongebruikelijke toepassing toegang heeft gekregen tot dit opslag account. Een mogelijke oorzaak is dat een aanvaller met een nieuwe toepassing toegang heeft tot uw opslag account.|
|**Afwijking anonieme toegang**|Geeft aan dat er een wijziging is in het toegangs patroon voor een opslag account. Zo is bijvoorbeeld anoniem toegang gekregen tot het account (zonder enige verificatie). Dit is onverwacht vergeleken met het recente toegangs patroon voor dit account. Een mogelijke oorzaak is dat een aanvaller open bare Lees toegang heeft voor een container die Blob-opslag bevat.|
|**Tor-afwijkingen**|Geeft aan dat de toegang tot dit account is verkregen vanaf een IP-adres dat een actief eind knooppunt van Tor (een anoniem-proxy) wordt genoemd. Bij de ernst van deze waarschuwing wordt rekening gehouden met het gebruikte verificatie type (indien van toepassing) en of dit het eerste geval van dergelijke toegang is. Mogelijke oorzaken kunnen een aanvaller zijn die toegang heeft gehad tot uw opslag account via Tor of een rechtmatige gebruiker die toegang heeft gehad tot uw opslag account met behulp van Tor.|
|**Afwijkingen van gegevens exfiltration**|Geeft aan dat een ongebruikelijk grote hoeveelheid gegevens is geëxtraheerd vergeleken met recente activiteiten op deze opslag container. Een mogelijke oorzaak is dat een aanvaller een grote hoeveelheid gegevens heeft geëxtraheerd uit een container met Blob Storage.|
|**Onverwachte afwijkings verwijdering**|Hiermee wordt aangegeven dat er een of meer onverwachte verwijderings bewerkingen zijn uitgevoerd in een opslag account, vergeleken met recente activiteiten voor dit account. Een mogelijke oorzaak is dat een aanvaller gegevens uit uw opslag account heeft verwijderd.|
|**Azure Cloud Services-pakket uploaden**|Geeft aan dat een Azure Cloud Services-pakket (. cspkg-bestand) op een ongebruikelijke manier is geüpload naar een opslag account, vergeleken met de recente activiteit voor dit account. Een mogelijke oorzaak is dat een aanvaller bereid is om schadelijke code vanuit uw opslag account te implementeren naar een Azure-Cloud service.|
|**Afwijkings machtiging voor toegang**|Geeft aan dat de toegangs machtigingen van deze opslag container op een ongebruikelijke manier zijn gewijzigd. Een mogelijke oorzaak is dat een aanvaller container machtigingen heeft gewijzigd om de beveiligings postuur te verzwakken of om persistentie te verkrijgen.|
|**Afwijkings toegang voor inspectie**|Geeft aan dat de toegangs machtigingen van een opslag account op een ongebruikelijke manier zijn geïnspecteerd, vergeleken met de recente activiteit voor dit account. Een mogelijke oorzaak is dat een aanvaller Reconnaissance heeft uitgevoerd voor een toekomstige aanval.|
|**Anomalie voor gegevens onderzoek**|Geeft aan dat blobs of containers in een opslag account op een abnormale manier zijn geïnventariseerd, vergeleken met de recente activiteit van dit account. Een mogelijke oorzaak is dat een aanvaller Reconnaissance heeft uitgevoerd voor een toekomstige aanval.|
|**Mogelijke malware uploaden**|Geeft aan dat een blob met mogelijke schadelijke software is geüpload naar een opslag account. Mogelijke oorzaken zijn het uploaden van opzettelijke schadelijke software door een aanvaller of een onbedoelde upload van een mogelijk schadelijke BLOB door een rechtmatige gebruiker.|

>[!NOTE]
>Advanced Threat Protection voor opslag is momenteel niet beschikbaar in azure Government-en soevereine Cloud regio's.

Ga voor meer informatie over de waarschuwingen voor opslag naar [Advanced Threat Protection voor Azure Storage](../storage/common/storage-advanced-threat-protection.md). Bekijk in het bijzonder de sectie ' beveiligings waarschuwingen '.

## Azure Cosmos DB<a name="cosmos-db"></a>

De volgende waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om Azure Cosmos DB accounts te openen of misbruik te maken:

|Waarschuwing|Beschrijving|
|---|---|
|**Toegang vanaf ongebruikelijke locatie**|Geeft aan dat er een wijziging is in het toegangs patroon voor een Azure Cosmos DB-account. Iemand heeft dit account geopend vanuit een onbekend IP-adres, vergeleken met de recente activiteit. Een aanvaller heeft toegang tot het account of een rechtmatige gebruiker heeft dit geopend vanaf een nieuwe en ongebruikelijke geografische locatie. Een voor beeld van de laatste is extern onderhoud van een nieuwe toepassing of ontwikkelaar.|
|**Ongebruikelijke gegevens exfiltration**|Geeft aan dat er een wijziging is opgetreden in het patroon voor gegevens extractie van een Azure Cosmos DB-account. Iemand heeft een ongebruikelijke hoeveelheid gegevens geëxtraheerd in vergelijking met de recente activiteit. Een aanvaller kan een grote hoeveelheid gegevens uit een Azure Cosmos DB-Data Base (bijvoorbeeld gegevens exfiltration of lekkage of een niet-geautoriseerde overdracht van gegevens) hebben geëxtraheerd. Het is ook mogelijk dat een rechtmatige gebruiker of toepassing een ongebruikelijke hoeveelheid gegevens uit een container heeft geëxtraheerd (bijvoorbeeld voor onderhoud back-upactiviteit).|

Zie [Advanced Threat Protection voor Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md)voor meer informatie.
