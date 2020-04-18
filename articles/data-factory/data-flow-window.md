---
title: Venstertransformatie van gegevensstroom toewijzen
description: Venstertransformatie van azure-gegevensfabriek
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606228"
---
# <a name="azure-data-factory-window-transformation"></a>Transformatie van azure-gegevensfabrieksvenster

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de venstertransformatie definieert u op venster gebaseerde aggregaties van kolommen in uw gegevensstromen. In de Opbouwfunctie voor expressies u verschillende typen aggregaties definiëren die zijn gebaseerd op gegevens of tijdvensters (SQL OVER-clausule), zoals LEAD, LAG, NTILE, CUMEDIST, RANK, enz.). Er wordt een nieuw veld gegenereerd in uw uitvoer met deze aggregaties. U ook optionele groeps-door-velden opnemen.

![Vensteropties](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Meer dan
Stel de verdeling van kolomgegevens in voor uw venstertransformatie. Het SQL-equivalent ```Partition By``` is de in de Over-clausule in SQL. Als u een berekening wilt maken of een expressie wilt maken die u wilt gebruiken voor de partitionering, u dat doen door over de kolomnaam te zweven en 'berekende kolom' te selecteren.

![Vensteropties](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Sorteren
Een ander deel van de ```Order By```Over-clausule is het instellen van de . Hiermee wordt de volgorde van de gegevenssortvolgorde ingesteld. U ook een expressie maken voor een berekeningswaarde in dit kolomveld voor sorteren.

![Vensteropties](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Bereik op
Stel vervolgens het raamkozijn in als Niet-begrensd of begrensd. Als u een niet-begrensd vensterframe wilt instellen, stelt u de schuifregelaar in op Niet-begrensd aan beide uiteinden. Als u een instelling kiest tussen Niet-begrensde en huidige rij, moet u de begin- en eindwaarden voor verschuiving instellen. Beide waarden zijn positieve gehele getallen. U relatieve getallen of waarden uit uw gegevens gebruiken.

De vensterschuifregelaar heeft twee waarden in te stellen: de waarden vóór de huidige rij en de waarden na de huidige rij. De verschuiving Begin en Eind komt overeen met de twee selectors op de schuifregelaar.

![Vensteropties](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Vensterkolommen
Tot slot, gebruik de Expression Builder om de aggregaties die u wilt gebruiken met de gegevens vensters zoals RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, enz.

![Vensteropties](media/data-flow/windows7.png "windows 7")

De volledige lijst met aggregatie- en analytische functies die u gebruiken in de https://aka.ms/dataflowexpressionsADF Data Flow Expression Language via de Expression Builder vindt u hier:.

## <a name="next-steps"></a>Volgende stappen

Als u op zoek bent naar een eenvoudige groeps-door-aggregatie, gebruikt u [de](data-flow-aggregate.md)
