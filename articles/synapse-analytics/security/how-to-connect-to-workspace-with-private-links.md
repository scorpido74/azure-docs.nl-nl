---
title: Verbinding maken met een Azure Synapse-werkruimte via privékoppelingen
description: In dit artikel leert u hoe u verbinding maken met uw Azure Synapse-werkruimte via privékoppelingen
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432188"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Verbinding maken met uw Azure Synapse-werkruimte via privékoppelingen (voorbeeld)

In dit artikel leert u hoe u een privéeindpunt maakt voor uw Azure Synapse-werkruimte. Bekijk [privélinks en privéeindpunten](https://docs.microsoft.com/azure/private-link/) voor meer informatie.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Stap 1: Uw Azure Synapse-werkruimte openen in Azure-portal

Selecteer **Privéeindpuntverbinding** onder **Beveiliging** en selecteer **+ Privéeindpunt**.
![Azure Synapse-werkruimte openen in Azure-portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Stap 2: Selecteer uw abonnement en regiogegevens

Kies onder het tabblad **Basisbeginselen** in het venster **Een privéeindpunt maken** de optie **Abonnement en** **Brongroep**. Geef een **naam** aan het privéeindpunt dat u wilt maken. Selecteer het **gebied** waar u het privéeindpunt wilt maken.

Privéeindpunten worden gemaakt in een subnet. De geselecteerde abonnements-, resourcegroep- en regiogroep filtert de subnetten voor privéeindpunten. Selecteer **Volgende: Resource >** wanneer u klaar bent.
![Abonnement- en regiogegevens selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Stap 3: De details van uw Azure Synapse-werkruimte selecteren

Selecteer **Verbinding maken met een Azure-bron in mijn map** op het tabblad **Resource.** Selecteer het **abonnement** dat uw Azure Synapse-werkruimte bevat. Het **resourcetype** voor het maken van privéeindpunten voor een Azure Synapse-werkruimte is *Microsoft.Synapse/werkruimtes*.

Selecteer uw Azure Synapse-werkruimte als **resource**. Elke Azure Synapse-werkruimte heeft drie **Target-subresources** waarmee u een privéeindpunt maken: Sql, SqlOnDemand en Dev.

Selecteer **Volgende: Configuratie>** om door te gaan naar het volgende deel van de installatie.
![Abonnement- en regiogegevens selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Selecteer **op** het tabblad Configuratie het **virtuele netwerk** en het **subnet** waarin het privéeindpunt moet worden gemaakt. U moet ook een DNS-record maken die wordt toegewezen aan het privéeindpunt.

Selecteer **Ja** voor **Integreren met een privé-DNS-zone** om uw privéeindpunt te integreren met een privé-DNS-zone. Als u geen privé-DNS-zone hebt die aan uw VNet is gekoppeld, wordt een nieuwe privé-DNS-zone gemaakt. Selecteer **Controleren + maken** wanneer u klaar bent.

![Abonnement- en regiogegevens selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Wanneer de implementatie is voltooid, opent u uw Azure Synapse-werkruimte in Azure-portal en selecteert u **Privé-eindpuntverbindingen**. De nieuwe naam van het privéeindpunt en de privé-eindpuntverbinding die aan het privéeindpunt is gekoppeld, wordt weergegeven.

![Abonnement- en regiogegevens selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Beheerde werkruimte VNet](./synapse-workspace-managed-vnet.md)

Meer informatie over [beheerde privéeindpunten](./synapse-workspace-managed-private-endpoints.md)

[Beheerde privéeindpunten maken voor uw gegevensbronnen](./how-to-create-managed-private-endpoints.md)
