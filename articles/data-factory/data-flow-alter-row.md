---
title: Rij transformatie wijzigen in gegevens stroom toewijzen
description: Data base-doel bijwerken met behulp van de trans formatie Alter Row in data flow toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834541"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Rij transformatie wijzigen in gegevens stroom toewijzen

Gebruik de Alter Row trans formatie om INSERT-, Delete-, update-en upsert-beleid in te stellen op rijen. U kunt één-op-veel-voor waarden toevoegen als expressies. Deze voor waarden moeten in volg orde van prioriteit worden opgegeven, aangezien elke rij wordt gemarkeerd met het beleid dat overeenkomt met de eerste-overeenkomende expressie. Elk van deze voor waarden kan resulteren in een rij (of rijen) die wordt ingevoegd, bijgewerkt, verwijderd of upserted. Alter Row kan zowel DDL-& DML-acties op uw data base produceren.

![Rij-instellingen wijzigen](media/data-flow/alter-row1.png "Rij-instellingen wijzigen")

Alter Row-trans formaties worden alleen toegepast op Data Base-of CosmosDB-sinks in uw gegevens stroom. De acties die u toewijst aan rijen (invoegen, bijwerken, verwijderen, upsert) worden niet uitgevoerd tijdens foutopsporingssessie. Voer een activiteit gegevens stroom uitvoeren in een pijp lijn uit om het alter Row-beleid in uw database tabellen te bepalen.

## <a name="specify-a-default-row-policy"></a>Een standaard beleid voor rijen opgeven

Een alter Row trans formatie maken en een rij-beleid met een voor waarde van `true()`opgeven. Elke rij die niet overeenkomt met een van de eerder gedefinieerde expressies, wordt gemarkeerd voor het opgegeven Row-beleid. Standaard wordt elke rij die niet overeenkomt met een voorwaardelijke expressie gemarkeerd voor `Insert`.

![Alter Row Policy](media/data-flow/alter-row4.png "Alter Row Policy")

> [!NOTE]
> Als u alle rijen met één beleid wilt markeren, kunt u een voor waarde voor dat beleid maken en de voor waarde als `true()`opgeven.

## <a name="view-policies-in-data-preview"></a>Beleids regels weer geven in voor beeld van gegevens

Gebruik de [foutopsporingsmodus](concepts-data-flow-debug-mode.md) om de resultaten van de Alter Row-beleids regels weer te geven in het deel venster voor gegevens voorbeeld. Een voor beeld van een gegevens van een alter Row trans formatie produceert geen DDL-of DML-acties op uw doel.

![Alter Row policies](media/data-flow/alter-row3.png "Alter Row policies")

Elk Alter Row-beleid wordt vertegenwoordigd door een pictogram dat aangeeft of een insert-, update-, upsert-of verwijderde actie wordt uitgevoerd. In de bovenste koptekst ziet u hoeveel rijen worden beïnvloed door elk beleid in de preview-versie.

## <a name="allow-alter-row-policies-in-sink"></a>Alter Row Policies in Sink toestaan

De gegevens stroom moet worden geschreven naar een Data Base of Cosmos Sink om het beleid voor het wijzigen van de rij te kunnen gebruiken. Schakel op het tabblad **instellingen** in de sink in welke Alter Row-beleids regels zijn toegestaan voor die sink.

![Rij-Sink wijzigen](media/data-flow/alter-row2.png "Rij-Sink wijzigen")

 Het standaard gedrag is alleen invoegen toestaan. Als u updates, upsert of verwijderen wilt toestaan, schakelt u het selectie vakje in de sink in die overeenkomt met deze voor waarde. Als updates, upsert of, worden verwijderd, zijn ingeschakeld, moet u opgeven welke sleutel kolommen in de Sink moeten worden gevonden.

> [!NOTE]
> Als uw invoeg-, update-of upsert het schema van de doel tabel in de Sink wijzigt, mislukt de gegevens stroom. Als u het doel schema in uw Data Base wilt wijzigen, kiest u **tabel opnieuw maken** als tabel actie. Hiermee wordt de tabel verwijderd en opnieuw gemaakt met de nieuwe schema definitie.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voor beeld is een alter Row trans formatie met de naam `CleanData` die een binnenkomende stream `SpecifyUpsertConditions` en drie Alter Row-voor waarden maakt. In de vorige trans formatie wordt een kolom met de naam `alterRowCondition` berekend die bepaalt of een rij wordt ingevoegd, bijgewerkt of verwijderd in de data base. Als de waarde van de kolom een teken reeks waarde heeft die overeenkomt met de regel Alter Row, wordt dat beleid toegewezen.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld van rijen wijzigen](media/data-flow/alter-row4.png "Voor beeld van rijen wijzigen")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Volgende stappen

Na de Alter Row trans formatie wilt u mogelijk [uw gegevens in een doel gegevens archief opvangen](data-flow-sink.md).
