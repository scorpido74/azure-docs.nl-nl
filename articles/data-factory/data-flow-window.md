---
title: Trans formatie van gegevens stroom venster toewijzen
description: Trans formatie van gegevens stroom venster Azure Data Factory toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606228"
---
# <a name="azure-data-factory-window-transformation"></a>Trans formatie van Azure Data Factory-venster

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de venster transformatie definieert u op venster gebaseerde aggregaties van kolommen in uw gegevens stromen. In de opbouw functie voor expressies kunt u verschillende typen aggregaties definiëren die zijn gebaseerd op gegevens of tijd Vensters (SQL OVER component) zoals LEAD, LAG, NTILE, CUMEDIST, RANK, enzovoort. Er wordt een nieuw veld in uw uitvoer gegenereerd dat deze aggregaties bevat. U kunt ook optionele velden voor groeperen op toevoegen.

![Venster Opties](media/data-flow/windows1.png "Windows 1")

## <a name="over"></a>Verloop
De partitionering van kolom gegevens voor uw venster transformatie instellen. Het equivalent van SQL is de ```Partition By``` in de component over in SQL. Als u een berekening wilt maken of een expressie wilt maken die voor de partitionering moet worden gebruikt, kunt u dit doen door de muis aanwijzer over de kolom naam te bewegen en "berekende kolom" te selecteren.

![Venster Opties](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Sorteren
Een ander deel van de component over is het instellen van de ```Order By``` . Hiermee wordt de volg orde van de sortering van gegevens ingesteld. U kunt ook een expressie maken voor een waarde berekenen in dit kolom veld voor het sorteren.

![Venster Opties](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Bereik op
Stel vervolgens het venster kader in op niet-gebonden of gebonden. Als u een niet-gebonden venster kader wilt instellen, stelt u de schuif regelaar in op beide uiteinden. Als u een instelling selecteert tussen de niet-gebonden en de huidige rij, moet u de begin-en eind waarde van de offset instellen. Beide waarden moeten positieve gehele getallen zijn. U kunt de relatieve cijfers of waarden van uw gegevens gebruiken.

De schuif regelaar voor het venster heeft twee waarden die moeten worden ingesteld: de waarden vóór de huidige rij en de waarden na de huidige rij. De begin-en eind offset komen overeen met de twee selecters op de schuif regelaar.

![Venster Opties](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Venster kolommen
Gebruik ten slotte de opbouw functie voor expressies om de aggregaties te definiëren die u wilt gebruiken met de gegevens Vensters zoals positie, aantal, MIN, MAX, hoge rang orde, LEAD, vertraging enzovoort.

![Venster Opties](media/data-flow/windows7.png "Windows 7")

De volledige lijst met aggregatie-en analytische functies die u kunt gebruiken in de taal van de ADF-gegevens stroom expressie via de opbouw functie voor expressies vindt u hier: https://aka.ms/dataflowexpressions .

## <a name="next-steps"></a>Volgende stappen

Als u op zoek bent naar een eenvoudige groep-op aggregatie, gebruikt u de [cumulatieve trans formatie](data-flow-aggregate.md)
