---
title: Knoop punten Azure Data Factory gegevens stroom verplaatsen
description: Knoop punten verplaatsen in een diagram met Azure Data Factory toewijzings gegevens stroom
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2031820843342fb7e6b115865297e08cbee28a0a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387336"
---
# <a name="mapping-data-flow-move-nodes"></a>Gegevens stroom koppelen knoop punten verplaatsen



![Aggregatie opties voor trans formatie](media/data-flow/agghead.png "aggregator-header")

Het ontwerp oppervlak voor Azure Data Factory gegevens stroom is een constructie-Opper vlak waar u gegevens stromen van boven naar beneden bouwt, van links naar rechts. Er is een werkset aan elke trans formatie gekoppeld met een plus teken (+). Richt u op uw bedrijfs logica in plaats van knoop punten te verbinden via randen in een vrije-vorm DAG-omgeving.

Als u dus zonder een model voor slepen en neerzetten een trans formatie-knoop punt wilt verplaatsen, wijzigt u de inkomende stroom. In plaats daarvan gaat u trans formaties verplaatsen door de "binnenkomende stroom" te wijzigen.

## <a name="streams-of-data-inside-of-data-flow"></a>Gegevens stromen in gegevens stroom

In Azure Data Factory gegevens stroom vertegenwoordigen streams de stroom van gegevens. In het deel venster trans formatie-instellingen wordt het veld "binnenkomende stoom" weer geven. Zo weet u welke binnenkomende gegevens stroom de trans formatie verfeedt. U kunt de fysieke locatie van uw transformatie knooppunt in de grafiek wijzigen door te klikken op de naam van de inkomende stroom en een andere gegevens stroom te selecteren. De huidige trans formatie en alle daaropvolgende transformaties voor die stroom worden vervolgens verplaatst naar de nieuwe locatie.

Als u een trans formatie met een of meer transformaties verplaatst, wordt de nieuwe locatie in de gegevens stroom gekoppeld via een nieuwe vertakking.

Als u geen daaropvolgende trans formaties hebt nadat het knoop punt dat u hebt geselecteerd, wordt alleen dat transformatie verplaatst naar de nieuwe locatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u het ontwerp van de gegevens stroom hebt voltooid, schakelt u de knop fout opsporing in en test u deze in de foutopsporingsmodus rechtstreeks in de [Data flow Designer](concepts-data-flow-debug-mode.md) of de [fout opsporing voor de pijp lijn](control-flow-execute-data-flow-activity.md).
