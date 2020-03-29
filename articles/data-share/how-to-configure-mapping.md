---
title: Een toewijzing van gegevenssets configureren in Azure-gegevensshare
description: Meer informatie over het configureren van een gegevenssettoewijzing voor een ontvangen share met Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964240"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Een toewijzing van gegevenssets configureren voor een ontvangen share in Azure Data Share

In dit artikel wordt uitgelegd hoe u een toewijzing van gegevenssets configureert voor een ontvangen share met Azure-gegevensshare. U wilt dit doen als u een uitnodiging voor gegevensshare hebt geaccepteerd, maar ervoor hebt gekozen om later accepteren en configureren of als gegevens op hun plaats worden gedeeld. U een toewijzing van gegevenssets configureren als u de bestemming moet wijzigen voor gegevens die met u worden gedeeld of als u gegevens wilt ontvangen in een SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navigeren naar een ontvangen gegevensshare

Navigeer in de Azure Data Share-service naar uw ontvangen deel en selecteer het tabblad **Details.** 

![Toewijzing van gegevenssets](./media/dataset-mapping.png "Toewijzing van gegevenssets") 

Schakel het selectievakje in naast de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **De toewijzing opheffen** om de bestaande toewijzing ongedaan te maken. Selecteer **+ Map om te targeten** om een nieuwe bestemmingswinkel te kiezen. 

![Kaart naar doel](./media/dataset-map-target.png "Kaart naar doel") 

## <a name="select-a-new-target-store"></a>Een nieuwe doelwinkel selecteren

Selecteer een doelgegevenstype waarin de gegevens moeten worden verwerkt. Voor delen op basis van momentopnamen worden alle gegevens die al bestaan in eerder toegewezen opslagaccounts niet automatisch verplaatst naar het nieuwe doelarchief. Selecteer een gegevensarchief in de opgegeven locatie voor het delen op de plaats. De locatie is het Azure-datacenter waar het brongegevensarchief van de gegevensprovider zich bevindt.

![Doelopslagaccount](./media/dataset-map-target-sql.png "Doelopslag") 

## <a name="select-a-file-format-sql-sources-only"></a>Een bestandsindeling selecteren (alleen SQL-bronnen)

Als de brongegevens afkomstig zijn van een SQL-bron, u kiezen in welke indeling deze is ontvangen. 

![Indeling kiezen](./media/sql-file-formats.png "SQL-bestandsindelingen")

## <a name="next-steps"></a>Volgende stappen

Ga door met het [delen van uw gegevenszelfstudie](share-your-data.md) om te leren hoe u gegevens delen.



