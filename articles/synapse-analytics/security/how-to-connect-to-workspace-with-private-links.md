---
title: Verbinding maken met een Azure Synapse-werk ruimte met behulp van persoonlijke koppelingen
description: In dit artikel leert u hoe u verbinding kunt maken met uw Azure Synapse-werk ruimte met behulp van persoonlijke koppelingen
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432188"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Verbinding maken met uw Azure Synapse-werk ruimte met behulp van persoonlijke koppelingen (preview-versie)

In dit artikel leert u hoe u een persoonlijk eind punt maakt in de Azure Synapse-werk ruimte. Zie [persoonlijke koppelingen en persoonlijke eind punten](https://docs.microsoft.com/azure/private-link/) voor meer informatie.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Stap 1: Open de Azure Synapse-werk ruimte in Azure Portal

Selecteer **particuliere endpoint-verbinding** onder **beveiliging** en selecteer vervolgens **+ persoonlijk eind punt**.
![Open de Azure Synapse-werk ruimte in Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Stap 2: Selecteer uw abonnement en de regio Details

Kies op het tabblad **basis beginselen** in het venster **een persoonlijk eind punt maken** de optie uw **abonnement** en **resource groep**. Geef een **naam** op voor het persoonlijke eind punt dat u wilt maken. Selecteer de **regio** waar u het persoonlijke eind punt wilt maken.

Privé-eind punten worden in een subnet gemaakt. Het abonnement, de resource groep en de geselecteerde regio filteren de subnetten met het persoonlijke eind punt. Selecteer **volgende: Resource >** wanneer dit is voltooid.
![Details van abonnement en regio selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Stap 3: de details van uw Azure Synapse-werk ruimte selecteren

Selecteer **verbinding maken met een Azure-resource in mijn Directory** op het tabblad **resource** . Selecteer het **abonnement** met de Azure Synapse-werk ruimte. Het **resource type** voor het maken van persoonlijke eind punten in een Azure Synapse-werk ruimte is *micro soft. Synapse/werk ruimten*.

Selecteer uw Azure Synapse-werk ruimte als de **resource**. Elke Azure Synapse-werk ruimte heeft drie **doel-subbronnen** waarmee u een persoonlijk eind punt kunt maken: SQL, SqlOnDemand en dev.

Selecteer **volgende: configuratie>** om door te gaan naar het volgende deel van de installatie.
![Details van abonnement en regio selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Selecteer op het tabblad **configuratie** het **virtuele netwerk** en het **subnet** waarin het persoonlijke eind punt moet worden gemaakt. U moet ook een DNS-record maken die is toegewezen aan het persoonlijke eind punt.

Selecteer **Ja** om te **integreren met een particuliere DNS-zone** om uw persoonlijke eind punt te integreren met een privé-DNS-zone. Als u geen privé-DNS-zone hebt die aan uw VNet is gekoppeld, wordt een nieuwe privé-DNS-zone gemaakt. Selecteer **controleren + maken** wanneer u klaar bent.

![Details van abonnement en regio selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Wanneer de implementatie is voltooid, opent u de Azure Synapse-werk ruimte in Azure Portal en selecteert u verbindingen met een **privé-eind punt**. Het nieuwe persoonlijke eind punt en de verbindings naam van het persoonlijke eind punt die aan het persoonlijke eind punt zijn gekoppeld, worden weer gegeven.

![Details van abonnement en regio selecteren](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Managed Workspace VNet](./synapse-workspace-managed-vnet.md)

Meer informatie over [beheerde privé-eind punten](./synapse-workspace-managed-private-endpoints.md)

[Beheerde persoonlijke eind punten maken voor uw gegevens bronnen](./how-to-create-managed-private-endpoints.md)
