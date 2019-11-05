---
title: Azure Data Factory toewijzing van gegevens stroom Alter Row trans formatie
description: Data base-doel bijwerken met Azure Data Factory toewijzing gegevens stroom Alter Row trans formatie
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 7a782c62165aa6f2641c2ebe8e4600198ec373c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486214"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter Row Transform

Gebruik de Alter Row trans formatie om INSERT-, Delete-, update-en upsert-beleid in te stellen op rijen. U kunt één-op-veel-voor waarden toevoegen als expressies. Deze voor waarden moeten in volg orde van prioriteit worden opgegeven, aangezien elke rij wordt gemarkeerd met het beleid dat overeenkomt met de eerste-overeenkomende expressie. Elk van deze voor waarden kan resulteren in een rij (of rijen) die wordt ingevoegd, bijgewerkt, verwijderd of upserted. Alter Row kan zowel DDL-& DML-acties op uw data base produceren.



![Rij-instellingen wijzigen](media/data-flow/alter-row1.png "Rij-instellingen wijzigen")

> [!NOTE]
> Alter Row-trans formaties worden alleen toegepast op Data Base-of CosmosDB-sinks in uw gegevens stroom. De acties die u toewijst aan rijen (invoegen, bijwerken, verwijderen, upsert) worden niet uitgevoerd tijdens foutopsporingssessie. U moet een taak stroom voor het uitvoeren van gegevens toevoegen aan een pijp lijn en de fout opsporing of triggers voor pijp lijn gebruiken om de Alter Row-beleids regels in uw database tabellen te bepalen.

## <a name="indicate-a-default-row-policy"></a>Een standaard beleid voor rijen aangeven

Een alter Row trans formatie maken en een rij-beleid met een voor waarde van `true()` opgeven. Elke rij die niet aan een van de eerder gedefinieerde expressies voldoet, wordt gemarkeerd voor het opgegeven beleid voor de rij. Standaard wordt elke rij die niet aan een voorwaardelijke expressie voldoet, gemarkeerd voor `Insert`.

![Rij één beleids regel wijzigen](media/data-flow/alter-row4.png "Rij één beleids regel wijzigen")

> [!NOTE]
> Als u alle rijen met één beleid wilt markeren, kunt u een voor waarde voor dat beleid maken en de voor waarde als `true()` opgeven.

## <a name="view-policies"></a>Beleids regels weer geven

Schakel de modus voor fout opsporing van gegevens stromen in om de resultaten van de Alter Row-beleids regels weer te geven in het deel venster voor gegevens voorbeeld. Bij het uitvoeren van een alter Row in de modus voor fout opsporing van gegevens stromen worden geen DDL-of DML-acties op uw doel gemaakt. Als u deze acties wilt uitvoeren, voert u de gegevens stroom in een activiteit gegevens stroom uitvoeren binnen een pijp lijn uit.

![Alter Row policies](media/data-flow/alter-row3.png "Alter Row policies")

Hiermee kunt u de status van elke rij controleren en weer geven op basis van uw voor waarden. Er zijn pictogrammen voor elke insert-, update-, DELETE-en upsert-actie die wordt uitgevoerd in uw gegevens stroom, waarmee wordt aangegeven welke actie wordt uitgevoerd wanneer u de gegevens stroom in een pijp lijn uitvoert.

## <a name="sink-settings"></a>Sink-instellingen

U moet een type data base-Sink hebben voor Alter Row to work. In de Sink-instellingen moet u elke actie instellen die overeenkomt met uw Alter Row-voor waarden.

![Rij-Sink wijzigen](media/data-flow/alter-row2.png "Rij-Sink wijzigen")

Het standaard gedrag van de ADF-gegevens stroom met data base-sinks is het invoegen van rijen. Als u ook updates, upsert en verwijderingen wilt toestaan, moet u ook deze selectie vakjes in de Sink inschakelen om de acties toe te staan.

> [!NOTE]
> Als uw invoeg-, update-of upsert het schema van de doel tabel in de Sink wijzigt, mislukt de gegevens stroom. Als u het doel schema in uw Data Base wilt wijzigen, moet u de optie ' opnieuw maken ' in de Sink selecteren. Hiermee wordt de tabel verwijderd en opnieuw gemaakt met de nieuwe schema definitie.

## <a name="next-steps"></a>Volgende stappen

Na de Alter Row trans formatie wilt u mogelijk [uw gegevens in een doel gegevens archief opvangen](data-flow-sink.md).
