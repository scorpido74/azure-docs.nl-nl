---
title: Overzicht van moment opnamen van shares voor Azure Files | Microsoft Docs
description: Een moment opname van een share is een alleen-lezen versie van een Azure Files share die op een bepaald moment wordt gemaakt, als een manier om een back-up van de share te maken.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333182"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Overzicht van moment opnamen van shares voor Azure Files 
Azure Files biedt de mogelijkheid om moment opnamen van shares van bestands shares te maken. Met moment opnamen delen wordt de status van de share op dat moment vastgelegd. In dit artikel wordt beschreven welke mogelijkheden moment opnamen delen bieden en hoe u deze kunt gebruiken in uw aangepaste use-case.

## <a name="when-to-use-share-snapshots"></a>Wanneer moet u moment opnamen van shares gebruiken?

### <a name="protection-against-application-error-and-data-corruption"></a>Bescherming tegen toepassings fout en gegevens beschadiging
Toepassingen die gebruikmaken van bestands shares voeren bewerkingen uit, zoals schrijven, lezen, opslag, verzen ding en verwerking. Als een toepassing onjuist is geconfigureerd of als er een onbedoelde fout is geïntroduceerd, kunnen er onbedoelde overschrijvingen of schade optreden in enkele blokken. Om u te helpen bij het beveiligen van deze scenario's, kunt u een moment opname van een share maken voordat u nieuwe toepassings code implementeert. Als er een bug-of toepassings fout is geïntroduceerd in de nieuwe implementatie, kunt u teruggaan naar een eerdere versie van uw gegevens op die bestands share. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Bescherming tegen onbedoeld verwijderen of onbedoelde wijzigingen
Stel dat u werkt met een tekst bestand in een bestands share. Wanneer het tekst bestand is gesloten, verliest u de mogelijkheid om uw wijzigingen ongedaan te maken. In deze gevallen moet u een vorige versie van het bestand herstellen. U kunt moment opnamen van shares gebruiken om eerdere versies van het bestand te herstellen als de naam per ongeluk is gewijzigd of verwijderd.

### <a name="general-backup-purposes"></a>Algemene back-updoeleinden
Nadat u een bestands share hebt gemaakt, kunt u regel matig een moment opname van de share van de bestands share maken om deze te gebruiken voor gegevens back-up. Als u regel matig een moment opname van een share maakt, kunt u de vorige versies van de gegevens behouden die kunnen worden gebruikt voor toekomstige controle vereisten of herstel na nood gevallen.

## <a name="capabilities"></a>Mogelijkheden
Een moment opname van een share is een alleen-lezen kopie van uw gegevens. U kunt moment opnamen maken, verwijderen en beheren met behulp van de REST API. Dezelfde mogelijkheden zijn ook beschikbaar in de client bibliotheek, Azure CLI en Azure Portal. 

U kunt moment opnamen van een share weer geven door zowel de REST API als SMB te gebruiken. U kunt de lijst met versies van de map of het bestand ophalen en u kunt een specifieke versie rechtstreeks koppelen als een station (alleen beschikbaar voor Windows-Zie [limieten](#limits)). 

Nadat een moment opname van een share is gemaakt, kan deze worden gelezen, gekopieerd of verwijderd, maar niet worden gewijzigd. U kunt een volledige share-moment opname niet kopiëren naar een ander opslag account. U moet dat bestand doen op bestand met behulp van AzCopy of andere Kopieer mechanismen.

De functionaliteit van de moment opname van shares wordt op bestands share niveau gegeven. Ophalen wordt op afzonderlijke bestands niveau gegeven, zodat afzonderlijke bestanden kunnen worden teruggezet. U kunt een volledige bestands share herstellen met behulp van SMB, de REST API, de portal, de client bibliotheek of het Power shell/CLI-hulp programma.

Een moment opname van een share van een bestands share is identiek aan de basis bestands share. Het enige verschil is dat een **datum/tijd** -waarde wordt toegevoegd aan de share-URI om het tijdstip aan te geven waarop de moment opname van de share is gemaakt. Als een bestands share-URI bijvoorbeeld http://storagesample.core.file.windows.net/myshare is, is de URI van de moment opname van de share gelijk aan:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Moment opnamen delen die blijven bestaan totdat ze expliciet worden verwijderd. Een moment opname van de share kan de basis bestands share niet uitmaken. U kunt de moment opnamen die zijn gekoppeld aan de basis bestands share opsommen om uw huidige moment opnamen bij te houden. 

Wanneer u een moment opname van een share van een bestands share maakt, worden de bestanden in de systeem eigenschappen van de share gekopieerd naar de moment opname van de share met dezelfde waarden. De basis bestanden en de meta gegevens van de bestands share worden ook gekopieerd naar de moment opname van de share, tenzij u afzonderlijke meta gegevens voor de moment opname van de share opgeeft wanneer u deze maakt.

U kunt een share met share-moment opnamen delen alleen verwijderen als u eerst alle moment opnamen van shares verwijdert.

## <a name="space-usage"></a>Ruimte gebruik 
Moment opnamen van shares zijn incrementeel. Alleen de gegevens die zijn gewijzigd na de moment opname van de meest recente share, worden opgeslagen. Dit minimaliseert de tijd die nodig is om de moment opname van de share te maken en bespaart opslag kosten. Elke schrijf bewerking naar het object of de update bewerking van de meta gegevens wordt geteld bij ' gewijzigde inhoud ' en wordt opgeslagen in de moment opname van de share. 

Als u ruimte wilt besparen, kunt u de moment opname van de share verwijderen voor de periode waarin het verloop het hoogste is.

Hoewel share-moment opnamen incrementeel worden opgeslagen, moet u alleen de meest recente moment opname van de share behouden om de share te herstellen. Wanneer u een moment opname van een share verwijdert, worden alleen de gegevens verwijderd die uniek zijn voor die share-moment opname. Actieve moment opnamen bevatten alle informatie die u nodig hebt om uw gegevens te zoeken en te herstellen (vanaf het moment dat de moment opname van de share is gemaakt) naar de oorspronkelijke locatie of een alternatieve locatie. U kunt herstellen op item niveau.

Moment opnamen tellen niet mee voor uw share limiet van 5 TB. Er is geen limiet voor het totale aantal moment opnamen van de ruimte share. De limieten van het opslag account zijn nog steeds van toepassing.

## <a name="limits"></a>Beperkingen
Het maximum aantal moment opnamen van shares dat Azure Files vandaag toestaat, is 200. Na 200 moment opnamen delen, moet u oudere moment opnamen van shares verwijderen om nieuwe te kunnen maken. 

Er is geen limiet voor de gelijktijdige aanroepen voor het maken van moment opnamen van shares. Er is geen limiet voor de hoeveelheid ruimte die moment opnamen van een bepaalde bestands share delen kan gebruiken. 

Momenteel is het niet mogelijk om moment opnamen van shares te koppelen aan linux. Dit komt doordat de Linux SMB-client geen ondersteuning biedt voor het koppelen van moment opnamen zoals Windows wel.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>De gegevens worden terug gekopieerd naar een share vanuit een share-moment opname
Kopieer bewerkingen die betrekking hebben op bestanden en moment opnamen delen, gelden deze regels:

U kunt afzonderlijke bestanden in een moment opname van een bestands share kopiëren naar het basis share of een andere locatie. U kunt een eerdere versie van een bestand herstellen of de volledige bestands share herstellen door bestand te kopiëren op bestand van de moment opname van de share. De moment opname van de share wordt niet gepromoveerd naar een basis share. 

De moment opname van de share blijft intact na het kopiëren, maar de basis bestands share wordt overschreven met een kopie van de gegevens die beschikbaar waren in de moment opname van de share. Alle herstelde bestanden tellen mee naar gewijzigde inhoud.

U kunt een bestand in een moment opname van een share kopiëren naar een ander doel met een andere naam. Het resulterende doel bestand is een schrijfbaar bestand en geen moment opname van de share. In dit geval blijft uw basis bestands share intact.

Wanneer een doel bestand wordt overschreven door een kopie, blijven de moment opnamen van shares die zijn gekoppeld aan het oorspronkelijke doel bestand intact.

## <a name="general-best-practices"></a>Algemene best practices 
Wanneer u een infra structuur uitvoert op Azure, moet u, indien mogelijk, back-ups automatiseren voor gegevens herstel. Geautomatiseerde acties zijn betrouwbaarder dan hand matige processen, waardoor gegevens bescherming en herstel baarheid worden verbeterd. U kunt de REST API, de client-SDK of scripts gebruiken voor Automation.

Voordat u de planner voor delen van snap shots implementeert, moet u de instellingen voor moment opnamen van delen en de Bewaar periode zorgvuldig overwegen om onnodige kosten te vermijden.

Moment opnamen delen bieden alleen beveiliging op bestands niveau. Moment opnamen delen verhinderen niet dat FAT-Finger wordt verwijderd op een bestands share of opslag account. Als u een opslag account wilt helpen beveiligen tegen onbedoeld verwijderen, kunt u het opslag account of de resource groep vergren delen.

## <a name="next-steps"></a>Volgende stappen
- Werken met moment opnamen van shares in:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Veelgestelde vragen over delen van moment opnamen](storage-files-faq.md#share-snapshots)
