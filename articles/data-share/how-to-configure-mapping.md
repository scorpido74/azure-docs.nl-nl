---
title: Een toewijzing van een gegevensset configureren in een Azure-gegevens share
description: Meer informatie over het configureren van een toewijzing van een gegevensset voor een ontvangen share met behulp van de Azure-gegevens share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490651"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Een toewijzing van een gegevensset configureren voor een ontvangen share in de Azure-gegevens share

In dit artikel wordt uitgelegd hoe u een toewijzing van een gegevensset configureert voor een ontvangen share met behulp van de Azure-gegevens share. U kunt dit doen als u een uitnodiging voor gegevens delen hebt geaccepteerd, maar u ervoor hebt gekozen om deze later te accepteren en te configureren. U kunt een toewijzing van een gegevensset configureren als u de bestemming wilt wijzigen voor gegevens die met u worden gedeeld, of als u gegevens wilt ontvangen in een SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navigeer naar een ontvangen gegevens share

Navigeer in de Azure data share-service naar de share die u hebt ontvangen en selecteer het tabblad **Details** . 

![Toewijzing van gegevensset](./media/dataset-mapping.png "Toewijzing van gegevensset") 

Schakel het selectie vakje in naast de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **toewijzing** opheffen om de bestaande toewijzing te ontkoppelen. Selecteer **+ toewijzen aan doel** om een nieuw doel archief te kiezen. 

![Toewijzen aan doel](./media/dataset-map-target.png "Toewijzen aan doel") 

## <a name="select-a-new-destination-store"></a>Een nieuw doel archief selecteren

Selecteer een doel gegevens type waarvan u wilt dat de gegevens binnenkomen. Alle gegevens die al in een eerder toegewezen opslag account bestaan, worden niet automatisch verplaatst naar de nieuwe bestemming.

![Doel opslag account](./media/dataset-map-target-sql.png "Doel opslag") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecteer een bestands indeling (alleen SQL-bronnen)

Als de bron gegevens afkomstig zijn uit een bron op basis van SQL, kunt u kiezen in welke indeling deze wordt ontvangen. 

![Indeling kiezen](./media/sql-file-formats.png "SQL-bestands indelingen")

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.



