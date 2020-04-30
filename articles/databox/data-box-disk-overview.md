---
title: Overzicht van Microsoft Azure Data Box Disk | Microsoft Docs in gegevens
description: Beschrijft Azure Data Box Disk, een cloudoplossing waarmee u grote hoeveelheden gegevens aan Azure kunt overdragen
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 067d818b7d23fc0b83cb1d4255bfbb8659149412
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240728"
---
# <a name="what-is-azure-data-box-disk"></a>Wat is Azure Data Box Disk?

Met de oplossing van Microsoft Azure Data Box Disk kunt u terabytes aan on-premises gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden. De beveiligde gegevensoverdracht wordt versneld door u één tot vijf SSD's (solid-state drives) te verzenden. Deze versleutelde schijven van 8 TB worden naar uw datacenter gestuurd via een regionale vervoerder. 

U kunt deze schijven snel configureren, verbinden en ontgrendelen via de Data Box-service in Azure Portal. Kopieer uw gegevens op schijven en stuur de schijven terug naar Azure. In het Azure-datacenter worden uw gegevens automatisch van de schijven naar de cloud geüpload met behulp van een snelle en persoonlijke uploadlink.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Gebruik Data Box Disk om terabytes aan gegevens over te dragen in scenario's met geen tot beperkte netwerkconnectiviteit. De gegevensverplaatsing kan eenmalig of periodiek zijn, of eerst een grote gegevensoverdracht, gevolgd door meerdere periodieke overdrachten. 

- **Eenmalige migratie**: wanneer grote aantallen on-premises gegevens worden verplaatst naar Azure. Bijvoorbeeld de gegevensoverdracht van offline tapes naar archiefgegevens in statische Azure-opslag.
- **Toenemende overdracht**: wanneer eerst een grote overdracht plaatsvindt met Data Box Disk (seed), gevolgd door toenemende overdrachten via het netwerk. Bijvoorbeeld wanneer Commvault en Data Box Disk worden gebruikt om back-ups te verplaatsen naar Azure. Deze migratie wordt gevolgd door het kopiëren van toenemende gegevens via het netwerk naar Azure-opslag. 
- **Periodieke uploads**: wanneer er periodiek grote aantallen gegevens worden gegenereerd en verplaatst moeten worden naar Azure. Bijvoorbeeld in de energie-exploitatie, waar er video's worden gegenereerd op olieplatforms en windmolenparken.

## <a name="the-workflow"></a>De werkstroom

Een typische stroom bestaat uit de volgende stappen:

1. **Bestellen**: plaats een bestelling in Azure Portal en geef de verzendingsgegevens en het Azure-doelopslagaccount voor uw gegevens op. Wanneer de schijven beschikbaar zijn, versleutelt Azure de schijven, bereidt Azure deze voor en verstuurt Azure deze met volgnummer.

2. **Ontvangen**: nadat de schijven zijn bezorgd, pakt u de schijven uit en koppelt u deze aan de computer die de te kopiëren gegevens bevat. Ontgrendel de schijven.
    
3. **Gegevens kopiëren**: sleep en zet de gegevens neer om deze naar de schijven te kopiëren.

4. **Terugsturen**: bereid de schijven voor en stuur deze terug naar het Azure-datacenter.

5. **Uploaden**: de gegevens worden automatisch van de schijf naar Azure gekopieerd. De schijven worden veilig gewist overeenkomstig de richtlijnen van het National Institute of Standards and Technology.

Tijdens dit proces wordt u via e-mail op de hoogte gesteld van alle statuswijzigingen. Ga naar [Data Box Disks in Azure Portal implementeren](data-box-disk-quickstart-portal.md) voor meer informatie over de gedetailleerde werkstroom.


## <a name="benefits"></a>Voordelen

Data Box Disk is ontworpen om grote aantallen gegevens naar Azure te verplaatsen, zonder impact op het netwerk. Deze oplossing biedt de volgende voordelen:

- **Snelheid**: Data Box Disk gebruikt een USB 3.0-verbinding om in minder dan een week tot 35 TB aan gegevens te verplaatsen.   

- **Gebruiksvriendelijk**: Data Box is een eenvoudig te gebruiken oplossing.

    - De schijven gebruiken een USB-verbinding met bijna geen insteltijd.
    - De schijven zijn klein en daardoor gemakkelijk te hanteren.
    - De schijven hebben geen externe stroomtoevoer nodig.
    - De schijven kunnen worden gebruikt met een datacenterserver, desktop of laptop.
    - De oplossing biedt via Azure Portal tracering van begin tot eind.    

- **Beveiligd**: Data Box Disk heeft ingebouwde beveiliging voor de schijven, gegevens en de service. 
    - Er kan niet met de schijven worden geknoeid en de schijven ondersteunen beveiligde update-functionaliteit. 
    - De gegevens op de schijven zijn altijd beveiligd met AES 128-bitsversleuteling. 
    - De schijven kunnen alleen worden ontgrendeld met een sleutel die via Azure Portal wordt doorgegeven. 
    - De service wordt beschermd door beveiligingsfuncties van Azure. 
    - Nadat uw gegevens zijn geüpload naar Azure, worden de schijven gewist overeenkomstig NIST 800-88r1-standaarden.  
    
Ga naar [Azure Data Box Disk-beveiliging en -gegevensbescherming](data-box-disk-security.md) voor meer informatie.


## <a name="features-and-specifications"></a>Functies en specificaties


| Specificaties                                          | Beschrijving              |
|---------------------------------------------------------|--------------------------|
| Gewicht                                                  | < 2 lbs. per doos. Maximaal 5 schijven in de doos                |
| Dimensies                                              | Schijf: 2,5-inch SSD |            
| Kabels                                                  | 1 USB 3.1-kabel per schijf|
| Opslagcapaciteit per bestelling                              | 40 TB (ongeveer 35 TB bruikbaar)|
| Opslagcapaciteit van de schijf                                   | 8 TB (ongeveer 7 TB bruikbaar)|
| Gegevensinterface                                          | USB   |
| Beveiliging                                                | Vooraf versleuteld met BitLocker en beveiligde updates <br> Met wachtwoordsleutels beschermde schijven <br> Gegevens altijd versleuteld  |
| Overdrachtssnelheid                                      | tot 430 MBps, afhankelijk van de bestandsgrootte      |
|Beheer                                               | Azure Portal |


## <a name="region-availability"></a>Beschikbaarheid in regio’s

Voor informatie over de beschik baarheid van regio's gaat u naar [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Disk kan ook in de Azure Government Cloud worden geïmplementeerd. Zie [Wat is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)voor meer informatie.


## <a name="pricing"></a>Prijzen

Ga naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/databox/disk/) voor informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [Vereisten voor Data Box](data-box-disk-system-requirements.md).
- De [Limieten voor Data Box Disk](data-box-disk-limits.md) begrijpen.
- Snel [Azure Data Box Disk](data-box-disk-quickstart-portal.md) in Azure Portal implementeren.
