---
title: Rijtransformatie wijzigen in de toewijzingsgegevensstroom
description: Databasedoel bijwerken met behulp van de transformatie van de gewijzigde rij in de toewijzingsgegevensstroom
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 77c58bb8dfa7d21b108d2aa63e90142f66877fb7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606513"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Rijtransformatie wijzigen in de toewijzingsgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de transformatie Rij wijzigen om het beleid voor invoegen, verwijderen, bijwerken en upsert in te stellen op rijen. U één op vele voorwaarden toevoegen als expressie. Deze voorwaarden moeten worden opgegeven in volgorde van prioriteit, omdat elke rij wordt gemarkeerd met het beleid dat overeenkomt met de eerste overeenkomende expressie. Elk van deze voorwaarden kan ertoe leiden dat een rij (of rijen) wordt ingevoegd, bijgewerkt, verwijderd of upserted. Alter Row kan beide DDL-& DML-acties tegen uw database produceren.

![Rijinstellingen wijzigen](media/data-flow/alter-row1.png "Rijinstellingen wijzigen")

Alter Row-transformaties werken alleen op database of CosmosDB-sinks in uw gegevensstroom. De acties die u aan rijen toewijst (invoegen, bijwerken, verwijderen, upsert) vinden niet plaats tijdens foutopsporingssessies. Voer een activiteit Gegevensstroom uitvoeren uit in een pijplijn om het beleid voor gewijzigde rij in uw databasetabellen uit te voeren.

## <a name="specify-a-default-row-policy"></a>Een standaardrijbeleid opgeven

Een transformatie Van een rij wijzigen maken `true()`en een rijbeleid opgeven met een voorwaarde van . Elke rij die niet overeenkomt met een van de eerder gedefinieerde expressies wordt gemarkeerd voor het opgegeven rijbeleid. Elke rij die niet overeenkomt met een voorwaardelijke expressie, wordt standaard gemarkeerd voor `Insert`.

![Rijbeleid wijzigen](media/data-flow/alter-row4.png "Rijbeleid wijzigen")

> [!NOTE]
> Als u alle rijen met één beleid wilt markeren, `true()`u een voorwaarde voor dat beleid maken en de voorwaarde opgeven als.

## <a name="view-policies-in-data-preview"></a>Beleid weergeven in een voorbeeld van gegevens

Gebruik [de foutopsporingsmodus](concepts-data-flow-debug-mode.md) om de resultaten van het beleid voor gewijzigde rij weer te geven in het voorbeeldvenster voor gegevens. Een voorbeeld van gegevens van een gewijzigde rijtransformatie levert geen DDL- of DML-acties op tegen uw doel.

![Rijbeleid wijzigen](media/data-flow/alter-row3.png "Rijbeleid wijzigen")

Elk beleid voor gewijzigde rij wordt weergegeven door een pictogram dat aangeeft of er een actie voor invoegen, bijwerken, upsert of verwijderd wordt. De bovenste koptekst geeft aan hoeveel rijen worden beïnvloed door elk beleid in de preview.

## <a name="allow-alter-row-policies-in-sink"></a>Wijzigen rijbeleid in gootsteen toestaan

Als het rijbeleid wijzigen werkt, moet de gegevensstroom naar een database of Cosmos-sink worden geschreven. Schakel op het tabblad **Instellingen** in uw gootsteen in welke rijbeleidsregels voor die gootsteen zijn toegestaan.

![Rijgootsteen wijzigen](media/data-flow/alter-row2.png "Rijgootsteen wijzigen")

 Het standaardgedrag is om alleen inserts toe te staan. Als u updates, upserts of verwijderingen wilt toestaan, schakelt u het selectievakje in de gootsteen in dat overeenkomt met die voorwaarde. Als updates, upserts of, deletes zijn ingeschakeld, moet u opgeven op welke belangrijke kolommen in de gootsteen moeten worden gekoppeld.

> [!NOTE]
> Als uw invoegsels, updates of upserts het schema van de doeltabel in de gootsteen wijzigen, mislukt de gegevensstroom. Als u het doelschema in uw database wilt wijzigen, kiest **u Tabel opnieuw maken** als tabelactie. Hiermee wordt uw tabel opnieuw weergegeven met de nieuwe schemadefinitie.

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

Het onderstaande voorbeeld is `CleanData` een transformatie met `SpecifyUpsertConditions` een alter row met de naam die een binnenkomende stream neemt en drie gewijzigde rijvoorwaarden maakt. In de vorige transformatie `alterRowCondition` wordt een kolom met de naam berekend die bepaalt of een rij al dan niet in de database wordt ingevoegd, bijgewerkt of verwijderd. Als de waarde van de kolom een tekenreekswaarde heeft die overeenkomt met de regel van de gewijzigde rij, wordt dat beleid toegewezen.

In de UX van de Data Factory lijkt deze transformatie op de onderstaande afbeelding:

![Voorbeeld van rij wijzigen](media/data-flow/alter-row4.png "Voorbeeld van rij wijzigen")

Het gegevensstroomscript voor deze transformatie bevindt zich in het onderstaande fragment:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Volgende stappen

Na de transformatie Rij wijzigen u [uw gegevens in een doelgegevensarchief laten zinken.](data-flow-sink.md)
