---
title: Querytijd optimaliseren met de opslagstrategie van de TOAST-tabel in Azure Database voor PostgreSQL - Enkele server
description: In dit artikel wordt beschreven hoe u de querytijd optimaliseert met de opslagstrategie voor de OPSLAG van de TOAST-tabel op een Azure-database voor PostgreSQL - Enkele server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65066977"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Querytijd optimaliseren met de opslagstrategie voor POP-lijst 
In dit artikel wordt beschreven hoe u querytijden optimaliseren met de opslagstrategie (TOAST) (oversized-attribuutopslagstrategie).

## <a name="toast-table-storage-strategies"></a>OPSLAGstrategieën voor TOAST-tafel
Vier verschillende strategieën worden gebruikt om kolommen op schijf op te slaan die TOAST kunnen gebruiken. Ze vertegenwoordigen verschillende combinaties tussen compressie en out-of-line opslag. De strategie kan worden ingesteld op het niveau van het gegevenstype en op kolomniveau.
- **Plain** voorkomt compressie of out-of-line opslag. Het schakelt het gebruik van single-byte headers voor varlena types. Plain is de enige mogelijke strategie voor kolommen met gegevenstypen die TOAST niet kunnen gebruiken.
- **Extended** maakt zowel compressie als out-of-line opslag mogelijk. Extended is de standaardinstelling voor de meeste gegevenstypen die TOAST kunnen gebruiken. Compressie wordt eerst geprobeerd. Out-of-line opslag wordt geprobeerd als de rij nog steeds te groot is.
- **Extern** maakt out-of-line opslag mogelijk, maar geen compressie. Gebruik van Extern maakt substringbewerkingen op brede tekst- en byteakolommen sneller. Deze snelheid wordt geleverd met de straf van meer opslagruimte. Deze bewerkingen zijn geoptimaliseerd om alleen de vereiste delen van de out-of-line waarde op te halen wanneer deze niet wordt gecomprimeerd.
- **Main** maakt compressie mogelijk, maar niet out-of-line opslag. Out-of-line opslag wordt nog steeds uitgevoerd voor dergelijke kolommen, maar alleen als laatste redmiddel. Het gebeurt wanneer er geen andere manier is om de rij klein genoeg te maken om op een pagina te passen.

## <a name="use-toast-table-storage-strategies"></a>Opslagstrategieën voor POP-tafelopslag gebruiken
Als uw query's toegang hebben tot gegevenstypen die TOAST kunnen gebruiken, u overwegen de hoofdstrategie te gebruiken in plaats van de standaardoptie Uitgebreide opties om de querytijden te verkorten. Main sluit out-of-line opslag niet uit. Als uw query's geen toegang hebben tot gegevenstypen die TOAST kunnen gebruiken, kan het handig zijn om de optie Uitgebreid te behouden. Een groter deel van de rijen van de hoofdtabel past in de gedeelde buffercache, wat de prestaties helpt.

Als u een werkbelasting hebt die een schema met brede tabellen en hoge tekentellingen gebruikt, u overwegen PostgreSQL-POP-tabellen te gebruiken. Een voorbeeld van de klanttabel had meer dan 350 kolommen met verschillende kolommen die 255 tekens overspannen. Nadat deze was omgezet naar de hoofdstrategie van de TOAST-tabel, is de querytijd van de benchmark teruggebracht van 4203 seconden tot 467 seconden. Dat is een verbetering van 89 procent.

## <a name="next-steps"></a>Volgende stappen
Controleer uw werklast op de vorige kenmerken. 

Bekijk de volgende PostgreSQL-documentatie: 
- [Hoofdstuk 68, Fysieke opslag van gegevensbestanden](https://www.postgresql.org/docs/current/storage-toast.html) 