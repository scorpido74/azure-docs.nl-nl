---
title: Trans formatie Azure Data Factory toewijzing van gegevens stroom selecteren
description: Trans formatie Azure Data Factory toewijzing van gegevens stroom selecteren
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6ef9712dd2fd6b8d53fd4ad2c3e07e1d6c8f1aec
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72387205"
---
# <a name="mapping-data-flow-select-transformation"></a>Gegevens stroom koppelen selecteren trans formatie


Gebruik deze trans formatie voor kolom selectiviteit (kleiner aantal kolommen), alias kolommen en stroom namen en volg orde van kolommen.

## <a name="how-to-use-select-transformation"></a>Trans formatie selecteren gebruiken
Met de Select Transform kunt u een volledige stroom of kolommen in die stroom aliassen, verschillende namen toewijzen (aliassen) en vervolgens naar deze nieuwe namen verwijzen verderop in uw gegevens stroom. Deze trans formatie is handig voor Self-koppelings scenario's. De manier waarop u een self-samen voeging in de ADF-gegevens stroom implementeert, is door een stroom te maken, deze te vertakking met ' nieuwe vertakking ' en vervolgens onmiddellijk daarna een ' Select '-trans formatie toe te voegen. Deze stroom heeft nu een nieuwe naam die u kunt gebruiken om terug te gaan naar de oorspronkelijke stream, waarbij u een self-deelname maakt:

![Self-deelname](media/data-flow/selfjoin.png "Self-deelname")

In het bovenstaande diagram bevindt de geselecteerde trans formatie zich bovenaan. Dit is een alias voor de oorspronkelijke stroom naar ' OrigSourceBatting '. In de onderstaande gemarkeerde join-trans formatie ziet u dat we deze Select alias Stream gebruiken als de rechter join, zodat we naar dezelfde sleutel in de linker & rechter kant van de inner join kunnen verwijzen.

Select kan ook worden gebruikt als een manier om de kolommen uit uw gegevens stroom te deselecteren. Als u bijvoorbeeld 6 kolommen hebt gedefinieerd in uw Sink, maar u alleen een specifieke 3 wilt transformeren en vervolgens naar de Sink wilt gaan, kunt u alleen die drie selecteren met behulp van de trans formatie selecteren.

![Trans formatie selecteren](media/data-flow/newselect1.png "Alias selecteren")

## <a name="options"></a>Opties
* De standaard instelling voor ' Select ' is om alle binnenkomende kolommen op te slaan en de oorspronkelijke namen te blijven gebruiken. U kunt de stroom alias door de naam van de Select trans formatie in te stellen.
* Als u afzonderlijke kolommen als alias wilt gebruiken, schakelt u ' Alles selecteren ' uit en gebruikt u onderaan de kolom toewijzing.
* Kies dubbele items overs Laan om dubbele kolommen van invoer-of uitvoer meta gegevens te verwijderen.

![Dubbele items overs Laan](media/data-flow/select-skip-dup.png "Dubbele items overs Laan")

* Wanneer u ervoor kiest dubbele items over te slaan, worden de resultaten weer gegeven op het tabblad controleren. de eerste keer dat de kolom wordt ingecheckt, blijft de volgende keer dat dezelfde kolom van de stroom wordt verwijderd.

> [!NOTE]
> Als u de toewijzings regels wilt wissen, klikt u op de knop **Reset** .

## <a name="mapping"></a>Mapping
Standaard worden alle kolommen automatisch toegewezen door trans formatie selecteren, die alle binnenkomende kolommen doorgeeft aan dezelfde naam in de uitvoer. De naam van de uitvoer stroom die is ingesteld in instellingen selecteren, definieert een nieuwe alias naam voor de stroom. Als u de optie instellen voor automatisch toewijzen inschakelt, kunt u de hele stroom een alias met alle kolommen.

![Transformatie regels selecteren](media/data-flow/rule2.png "Toewijzing op basis van een regel")

Als u kolommen wilt aliassen, verwijderen, de naam ervan wilt wijzigen of opnieuw wilt rangschikken, moet u eerst de optie automatisch toewijzen uitschakelen. Standaard ziet u een standaard regel die is ingevoerd voor alle invoer kolommen. U kunt deze regel behouden als u van plan bent om altijd alle binnenkomende kolommen toe te wijzen aan dezelfde naam op de uitvoer.

Als u echter aangepaste regels wilt toevoegen, klikt u op toewijzing toevoegen. Veld toewijzing bevat een lijst met binnenkomende en uitgaande kolom namen voor toewijzing en alias. Kies regel toewijzing om regels voor het vergelijken van het patroon te maken.

## <a name="rule-based-mapping"></a>Toewijzing op basis van een regel
Wanneer u op regels gebaseerde toewijzing kiest, geeft u de ADF de opdracht om de overeenkomende expressie te evalueren zodat deze overeenkomt met de regels voor binnenkomende patronen en de uitgaande veld namen te definiëren. U kunt een combi natie van zowel veld-als op regels gebaseerde toewijzingen toevoegen. Veld namen worden vervolgens gegenereerd tijdens runtime via ADF op basis van binnenkomende meta gegevens van de bron. U kunt de namen van de gegenereerde velden weer geven tijdens fout opsporing en het deel venster gegevens voorbeeld gebruiken.

Meer informatie over het vergelijken van patronen vindt u in de documentatie van het [kolom patroon](concepts-data-flow-column-pattern.md).

## <a name="next-steps"></a>Volgende stappen
* Gebruik de [sink-trans formatie](data-flow-sink.md) om uw gegevens in een gegevens archief in te voeren nadat u selecteren voor naam wijzigen, volg orde en alias kolommen hebt gebruikt.
