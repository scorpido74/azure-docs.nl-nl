---
title: Beheerd virtueel netwerk in azure Synapse Analytics
description: Een artikel waarin het beheerde virtuele netwerk in azure Synapse Analytics wordt uitgelegd
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423633"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Beheerde Virtual Network Azure Synapse Analytics (preview)

In dit artikel worden beheerde Virtual Network in azure Synapse Analytics uitgelegd.

## <a name="managed-workspace-vnet"></a>VNet beheerde werk ruimte

Wanneer u de Azure Synapse-werk ruimte maakt, kunt u deze koppelen aan een VNet. Het VNet dat is gekoppeld aan uw werk ruimte wordt beheerd door Azure Synapse. Dit VNet wordt een *beheerde werk ruimte-vnet*genoemd.

Beheer van beheerde werk ruimten biedt u op vier manieren een waarde:

- Met een beheerde werk ruimte VNet kunt u de last van het beheer van VNet naar Azure Synapse.
- U hoeft geen inkomende NSG-regels in te stellen op uw eigen VNets, zodat Azure Synapse Management-verkeer uw VNet kan invoeren. Een onjuiste configuratie van deze NSG-regels leidt ertoe dat de service wordt onderbroken door klanten.
- U hoeft geen subnet voor uw Spark-clusters te maken op basis van de piek belasting.
- Beheer van beheerde werk ruimte samen met beheerde persoonlijke eind punten beschermt tegen gegevens exfiltration. U kunt alleen beheerde persoonlijke eind punten maken in een werk ruimte waaraan een beheerde werk ruimte-VNet is gekoppeld.

Als u een werk ruimte maakt waaraan een beheerde werk ruimte-VNet is gekoppeld, zorgt u ervoor dat uw werk ruimte in een netwerk is geïsoleerd van andere werk ruimten. Azure Synapse biedt verschillende analyse mogelijkheden in een werk ruimte: gegevens integratie, Apache Spark, SQL-pool en SQL op aanvraag.

Als uw werk ruimte beschikt over een beheerde werk ruimte VNet, worden gegevens integratie en Spark-resources geïmplementeerd. Een beheerde werk ruimte VNet biedt ook isolatie op gebruikers niveau voor Spark-activiteiten, omdat elk Spark-cluster zich in een eigen subnet bevindt.

SQL-groep en SQL on-demand zijn mogelijkheden voor meerdere tenants en zijn daarom buiten het VNet van de beheerde werk ruimte. Communicatie tussen de werk ruimte en SQL-groep en SQL op aanvraag gebruiken persoonlijke koppelingen van Azure. Deze persoonlijke koppelingen worden automatisch voor u gemaakt wanneer u een werk ruimte maakt waaraan een Managed Workspace VNet is gekoppeld.

>[!IMPORTANT]
>U kunt deze werkruimte configuratie niet wijzigen nadat de werk ruimte is gemaakt. U kunt bijvoorbeeld een werk ruimte waaraan geen Managed Workspace VNet is gekoppeld, opnieuw configureren en er een VNet aan koppelen. Op dezelfde manier kunt u een werk ruimte die is gekoppeld aan het beheerde werk ruimte VNet niet opnieuw configureren en de VNet loskoppelen.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Een Azure Synapse-werk ruimte maken met een beheerde werk ruimte VNet

Als u een Azure Synapse-werk ruimte wilt maken waaraan een beheerde werk ruimte VNet is gekoppeld, selecteert u het tabblad **beveiliging en netwerk** in azure Portal en schakelt u het selectie vakje **beheerd virtueel netwerk inschakelen** in.

Als u het selectie vakje uitschakelt, is er geen VNet gekoppeld aan de werk ruimte.

>[!IMPORTANT]
>U kunt alleen persoonlijke koppelingen gebruiken in een werk ruimte die een beheerde werk ruimte VNet heeft.

![Beheer van beheerde werk ruimte inschakelen](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Al het uitgaande verkeer van het beheerde werk ruimte VNet wordt in de toekomst geblokkeerd. Het is raadzaam om verbinding te maken met al uw gegevens bronnen met behulp van beheerde privé-eind punten.

U kunt controleren of uw Azure Synapse-werk ruimte is gekoppeld aan een beheerde werk ruimte VNet door **overzicht** te selecteren in azure Portal.

![Overzicht van de werk ruimte in Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werk ruimte](../quickstart-create-workspace.md) maken

Meer informatie over [beheerde privé-eind punten](./synapse-workspace-managed-private-endpoints.md)

[Beheerde persoonlijke eind punten maken voor uw gegevens bronnen](./how-to-create-managed-private-endpoints.md)
