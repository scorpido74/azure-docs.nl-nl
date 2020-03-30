---
title: Overzicht van momentopnamen voor delen voor Azure-bestanden | Microsoft Documenten
description: Een momentopname voor delen is een alleen-lezen versie van een Azure-bestandenaandeel dat op een bepaald moment is genomen, als een manier om een back-up van het aandeel te maken.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333182"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Overzicht van share-momentopnamen voor Azure Files 
Azure Files biedt de mogelijkheid om momentopnamen van bestandsshares te delen. Momentopnamen delen leggen op dat moment de status van het aandeel vast. In dit artikel beschrijven we welke mogelijkheden momentopnamen bieden en hoe u er gebruik van maken in uw aangepaste use case.

## <a name="when-to-use-share-snapshots"></a>Wanneer deelmomentopnamen gebruiken

### <a name="protection-against-application-error-and-data-corruption"></a>Bescherming tegen toepassingsfouten en gegevensbeschadiging
Toepassingen die bestandsshares gebruiken, voeren bewerkingen uit zoals schrijven, lezen, opslaan, verzenden en verwerken. Als een toepassing verkeerd is geconfigureerd of als een onbedoelde bug wordt geïntroduceerd, kan per ongeluk overschrijven of schade gebeuren met een paar blokken. Om te helpen beschermen tegen deze scenario's, u een momentopname voor delen maken voordat u nieuwe toepassingscode implementeert. Als er een fout met bug of toepassing wordt geïntroduceerd bij de nieuwe implementatie, u teruggaan naar een vorige versie van uw gegevens in die bestandsshare. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Bescherming tegen onbedoelde verwijderingen of onbedoelde wijzigingen
Stel dat u werkt aan een tekstbestand in een bestandsshare. Nadat het tekstbestand is gesloten, u uw wijzigingen niet meer ongedaan maken. In deze gevallen moet u vervolgens een eerdere versie van het bestand herstellen. U momentopnamen delen gebruiken om eerdere versies van het bestand te herstellen als het per ongeluk wordt hernoemd of verwijderd.

### <a name="general-backup-purposes"></a>Algemene back-updoeleinden
Nadat u een bestandsshare hebt gemaakt, u periodiek een momentopname van het delen van het bestandsaandeel maken om deze te gebruiken voor een back-up van gegevens. Een momentopname van delen, wanneer deze periodiek wordt uitgevoerd, helpt bij het onderhouden van eerdere versies van gegevens die kunnen worden gebruikt voor toekomstige controlevereisten of herstel na noodgevallen.

## <a name="capabilities"></a>Functionaliteit
Een momentopname voor delen is een point-in-time, alleen-lezen kopie van uw gegevens. U momentopnamen maken, verwijderen en beheren met behulp van de REST API. Dezelfde mogelijkheden zijn ook beschikbaar in de clientbibliotheek, Azure CLI en Azure-portal. 

U snapshots van een aandeel bekijken met behulp van zowel de REST API als smb. U de lijst met versies van de map of het bestand ophalen en u een specifieke versie rechtstreeks als station monteren (alleen beschikbaar op Windows - zie [Limieten).](#limits) 

Nadat een momentopname voor delen is gemaakt, kan deze worden gelezen, gekopieerd of verwijderd, maar niet gewijzigd. U een momentopname van een hele share niet kopiëren naar een ander opslagaccount. Je moet dat bestand per bestand doen, met behulp van AzCopy of andere kopieermechanismen.

De mogelijkheid voor momentopnamen voor delen wordt geleverd op het niveau van de bestandsshare. Ophalen wordt op individueel bestandsniveau aangeboden, zodat afzonderlijke bestanden kunnen worden hersteld. U een volledige bestandsshare herstellen met behulp van SMB, de REST API, de portal, de clientbibliotheek of PowerShell/CLI-tooling.

Een momentopname van een share van een bestandsshare is identiek aan het basisbestandaandeel. Het enige verschil is dat een **DateTime-waarde** wordt toegevoegd aan de share URI om aan te geven op welk moment de momentopname van het aandeel is gemaakt. Als een uri voor bestandsshare bijvoorbeeld is, http://storagesample.core.file.windows.net/myshareis de share snapshot URI vergelijkbaar met:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Momentopnamen voor delen blijven bestaan totdat ze expliciet worden verwijderd. Een momentopname voor delen kan het basisbestandaandeel niet overleven. U de momentopnamen die zijn gekoppeld aan het basisbestanddelen opsommen om uw huidige momentopnamen bij te houden. 

Wanneer u een momentopname van een bestandsshare maakt, worden de bestanden in de systeemeigenschappen van het aandeel gekopieerd naar de momentopname van delen met dezelfde waarden. De basisbestanden en de metagegevens van het bestandsaandeel worden ook gekopieerd naar de momentopname van het delen, tenzij u afzonderlijke metagegevens opgeeft voor de momentopname van het delen wanneer u deze maakt.

U een share met momentopnamen voor delen niet verwijderen, tenzij u eerst alle momentopnamen voor delen verwijdert.

## <a name="space-usage"></a>Ruimtegebruik 
Momentopnamen voor delen zijn incrementeel van aard. Alleen de gegevens die zijn gewijzigd na uw meest recente momentopname van delen, worden opgeslagen. Dit minimaliseert de tijd die nodig is om de momentopname van het aandeel te maken en bespaart op opslagkosten. Elke schrijfbewerking naar de object- of eigenschap- of metagegevensupdatebewerking wordt meegeteld bij 'gewijzigde inhoud' en wordt opgeslagen in de momentopname van het delen. 

Als u ruimte wilt besparen, u de momentopname van de share verwijderen voor de periode waarin de verloop het hoogst was.

Hoewel momentopnamen voor delen stapsgewijs worden opgeslagen, moet u alleen de meest recente momentopname van delen behouden om het aandeel te herstellen. Wanneer u een momentopname van een share verwijdert, worden alleen de gegevens die uniek zijn voor die momentopname voor delen verwijderd. Actieve momentopnamen bevatten alle informatie die u nodig hebt om door uw gegevens te bladeren en uw gegevens te herstellen (vanaf het moment dat de momentopname van het aandeel is gemaakt) naar de oorspronkelijke locatie of een alternatieve locatie. U herstellen op itemniveau.

Momentopnamen tellen niet mee voor uw aandeellimiet van 5 TB. Er is geen limiet aan hoeveel ruimtedelen momentopnamen in totaal bezetten. Opslagaccountlimieten zijn nog steeds van toepassing.

## <a name="limits"></a>Limieten
Het maximum aantal momentopnamen voor delen dat Azure Files vandaag toestaat, is 200. Na 200 momentopnamen voor delen moet u oudere momentopnamen voor delen verwijderen om nieuwe momentopnamen te maken. 

Er is geen limiet aan de gelijktijdige oproepen voor het maken van momentopnamen voor delen. Er is geen limiet aan de hoeveelheid ruimte die momentopnamen van een bepaald bestandsaandeel kan verbruiken. 

Tegenwoordig is het niet mogelijk om share snapshots op Linux te monteren. Dit komt omdat de Linux SMB client geen ondersteuning biedt voor het monteren van snapshots zoals Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Gegevens terugkopiëren naar een share vanuit momentopname delen
Kopieer bewerkingen die bestanden bevatten en momentopnamen delen volg de volgende regels:

U afzonderlijke bestanden in een momentopname voor bestandsshare kopiëren naar het basisaandeel of een andere locatie. U een eerdere versie van een bestand herstellen of de volledige bestandsshare herstellen door bestand per bestand te kopiëren vanuit de momentopname van delen. De momentopname van het aandeel wordt niet gepromoot naar basisaandeel. 

De momentopname van het aandeel blijft intact na het kopiëren, maar het basisbestandaandeel wordt overschreven met een kopie van de gegevens die beschikbaar waren in de momentopname van het aandeel. Alle herstelde bestanden tellen mee voor 'gewijzigde inhoud'.

U een bestand in een momentopname voor delen kopiëren naar een andere bestemming met een andere naam. Het resulterende doelbestand is een beschrijfbaar bestand en geen momentopname voor delen. In dit geval blijft uw basisbestandsaandeel intact.

Wanneer een doelbestand wordt overschreven met een kopie, blijven alle momentopnamen die aan het oorspronkelijke doelbestand zijn gekoppeld, intact.

## <a name="general-best-practices"></a>Algemene best practices 
Wanneer u infrastructuur op Azure uitvoert, automatiseer dan waar mogelijk back-ups voor gegevensherstel. Geautomatiseerde acties zijn betrouwbaarder dan handmatige processen, waardoor de gegevensbescherming en herstelbaarheid worden verbeterd. U de REST API, de Client SDK of scripting gebruiken voor automatisering.

Voordat u de momentopnameplanner voor delen implementeert, moet u zorgvuldig rekening houden met de frequentie en bewaarinstellingen voor momentopnamen voor delen om onnodige kosten te voorkomen.

Momentopnamen voor delen bieden alleen bescherming op bestandsniveau. Deel momentopnamen voor voorkomen dat de vetvinger wordt verwijderd op een bestandsshare- of opslagaccount. Als u een opslagaccount wilt beschermen tegen onbedoelde verwijderingen, u het opslagaccount of de brongroep vergrendelen.

## <a name="next-steps"></a>Volgende stappen
- Werken met momentopnamen voor delen in:
    - [Powershell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Veelgestelde vragen over momentopnamedelen](storage-files-faq.md#share-snapshots)
