---
title: Big data catalogiseren in Azure Data Catalog
description: How-to-artikel markeringspatronen voor het gebruik van Azure Data Catalog met 'big data' gegevensbronnen, waaronder Azure Blob Storage, Azure Data Lake en Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 88dc85003fa2a3e41d8a31055ff8ba9b0fcc7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300574"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Big data catalogiseren in Azure Data Catalog

## <a name="introduction"></a>Inleiding

**Microsoft Azure Data Catalog** is een volledig beheerde cloudservice die dient als een systeem van registratie en detectiesysteem voor bedrijfsgegevensbronnen. Het gaat erom mensen te helpen gegevensbronnen te ontdekken, te begrijpen en te gebruiken en organisaties te helpen meer waarde te halen uit hun bestaande gegevensbronnen, waaronder big data.

**Azure Data Catalog** ondersteunt de registratie van Azure Blog Storage blobs en mappen, evenals Hadoop HDFS-bestanden en mappen. Het semi-gestructureerde karakter van deze gegevensbronnen biedt grote flexibiliteit. Om de meeste waarde te halen uit het registreren ervan met **Azure Data Catalog,** moeten gebruikers echter rekening houden met de manier waarop de gegevensbronnen zijn georganiseerd.

## <a name="directories-as-logical-data-sets"></a>Mappen als logische datasets

Een gemeenschappelijk patroon voor het organiseren van big data bronnen is om mappen te behandelen als logische gegevenssets. Mappen op het hoogste niveau worden gebruikt om een gegevensset te definiëren, terwijl submappen partities definiëren en de bestanden die ze bevatten, de gegevens zelf opslaan.

Een voorbeeld van dit patroon kan zijn:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

In dit voorbeeld vertegenwoordigen vehicle_maintenance_events en location_tracking_events logische gegevenssets. Elk van deze mappen bevat gegevensbestanden die per jaar en maand zijn ingedeeld in submappen. Elk van deze mappen kan mogelijk honderden of duizenden bestanden bevatten.

In dit patroon heeft het registreren van afzonderlijke bestanden met **Azure Data Catalog** waarschijnlijk geen zin. In plaats daarvan registreert u de mappen die de gegevenssets vertegenwoordigen die zinvol zijn voor de gebruikers die met de gegevens werken.

## <a name="reference-data-files"></a>Referentiegegevensbestanden

Een aanvullend patroon is het opslaan van referentiegegevenssets als afzonderlijke bestanden. Deze datasets kunnen worden gezien als de 'kleine' kant van big data en zijn vaak vergelijkbaar met dimensies in een analytisch datamodel. Referentiegegevensbestanden bevatten records die worden gebruikt om context te bieden voor het grootste deel van de gegevensbestanden die elders in het big data-archief zijn opgeslagen.

Een voorbeeld van dit patroon kan zijn:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Wanneer een analist of gegevenswetenschapper werkt met de gegevens in de grotere directorystructuren, kunnen de gegevens in deze referentiebestanden worden gebruikt om meer gedetailleerde informatie te verstrekken voor entiteiten die alleen met naam of id worden aangeduid in de grotere gegevensset.

In dit patroon is het zinvol om de afzonderlijke referentiegegevensbestanden te registreren bij **Azure Data Catalog.** Elk bestand vertegenwoordigt een gegevensset en elk bestand kan afzonderlijk worden geannoteerd en ontdekt.

## <a name="alternate-patterns"></a>Alternatieve patronen

De patronen beschreven in de vorige sectie zijn slechts twee mogelijke manieren waarop een big data-winkel kan worden georganiseerd, maar elke implementatie is anders. Ongeacht hoe uw gegevensbronnen zijn gestructureerd, richt u zich bij het registreren van big data-bronnen met **Azure Data Catalog**op het registreren van de bestanden en mappen die de gegevenssets vertegenwoordigen die van waarde zijn voor anderen binnen uw organisatie. Het registreren van alle bestanden en mappen kan de catalogus verrommelen, waardoor het moeilijker is voor gebruikers om te vinden wat ze nodig hebben.

## <a name="summary"></a>Samenvatting

Als u gegevensbronnen registreert met **Azure Data Catalog,** kunnen ze gemakkelijker worden ontdekt en begrepen. Door het registreren en annoteren van de big data-bestanden en mappen die logische gegevenssets vertegenwoordigen, u gebruikers helpen bij het vinden en gebruiken van de big data-bronnen die ze nodig hebben.
