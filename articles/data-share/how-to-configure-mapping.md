---
title: Een toewijzing van een gegevensset configureren in een Azure-gegevens share
description: Meer informatie over het configureren van een toewijzing van een gegevensset voor een ontvangen share met behulp van de Azure-gegevens share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76964240"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Een toewijzing van een gegevensset configureren voor een ontvangen share in de Azure-gegevens share

In dit artikel wordt uitgelegd hoe u een toewijzing van een gegevensset configureert voor een ontvangen share met behulp van de Azure-gegevens share. U kunt dit doen als u een uitnodiging voor gegevens delen hebt geaccepteerd, maar u hebt gekozen voor accepteren en configureren, of als de gegevens in-place worden gedeeld. U kunt een toewijzing van een gegevensset configureren als u de bestemming wilt wijzigen voor gegevens die met u worden gedeeld, of als u gegevens wilt ontvangen in een SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navigeer naar een ontvangen gegevens share

Navigeer in de Azure data share-service naar de share die u hebt ontvangen en selecteer het tabblad **Details** . 

![Toewijzing van gegevensset](./media/dataset-mapping.png "Toewijzing van gegevensset") 

Schakel het selectie vakje in naast de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **toewijzing** opheffen om de bestaande toewijzing te ontkoppelen. Selecteer **+ toewijzen aan doel** om een nieuw doel archief te kiezen. 

![Toewijzen aan doel](./media/dataset-map-target.png "Toewijzen aan doel") 

## <a name="select-a-new-target-store"></a>Een nieuw doel archief selecteren

Selecteer een doel gegevens type waarvan u wilt dat de gegevens binnenkomen. Voor het delen op basis van moment opnamen worden alle gegevens die al in een eerder toegewezen opslag account bestaan, niet automatisch verplaatst naar het nieuwe doel archief. Selecteer voor in-place delen een gegevens archief op de opgegeven locatie. De locatie is het Azure Data Center waar de bron gegevens opslag van de gegevens provider zich bevindt.

![Doel opslag account](./media/dataset-map-target-sql.png "Doel opslag") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecteer een bestands indeling (alleen SQL-bronnen)

Als de bron gegevens afkomstig zijn uit een bron op basis van SQL, kunt u kiezen in welke indeling deze wordt ontvangen. 

![Indeling kiezen](./media/sql-file-formats.png "SQL-bestands indelingen")

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).



