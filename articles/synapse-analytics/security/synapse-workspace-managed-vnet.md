---
title: Beheerd virtueel netwerk in Azure Synapse Analytics
description: Een artikel waarin beheerd virtueel netwerk in Azure Synapse Analytics wordt uitgelegd
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423633"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics Managed Virtual Network (preview)

In dit artikel wordt Managed Virtual Network in Azure Synapse Analytics uitgelegd.

## <a name="managed-workspace-vnet"></a>Beheerde werkruimte VNet

Wanneer u uw Azure Synapse-werkruimte maakt, u ervoor kiezen deze aan een VNet te koppelen. De VNet die aan uw werkruimte is gekoppeld, wordt beheerd door Azure Synapse. Deze VNet wordt een *Beheerde werkruimte VNet*genoemd.

Beheerde werkruimte VNet biedt u waarde op vier manieren:

- Met een Managed workspace VNet u de last van het beheer van de VNet naar Azure Synapse ontlasten.
- U hoeft inkomende NSG-regels niet op uw eigen VNets te configureren, zodat Azure Synapse-beheerverkeer uw VNet kan invoeren. Verkeerde configuratie van deze NSG-regels veroorzaakt serviceonderbrekingen voor klanten.
- U hoeft geen subnet te maken voor uw Spark-clusters op basis van piekbelasting.
- Beheerde werkruimte VNet en beheerde privéeindpunten beschermt tegen gegevensexfiltratie. U alleen beheerde privéeindpunten maken in een werkruimte waar een Beheerde werkruimte VNet aan is gekoppeld.

Het maken van een werkruimte met een VNet voor beheerde werkruimte die eraan is gekoppeld, zorgt ervoor dat uw werkruimte is geïsoleerd van andere werkruimten. Azure Synapse biedt verschillende analytische mogelijkheden in een werkruimte: gegevensintegratie, Apache Spark, SQL-pool en SQL on-demand.

Als uw werkruimte een VNet voor beheerde werkruimte heeft, worden gegevensintegratie en Spark-resources daarin geïmplementeerd. Een Beheerde werkruimte VNet biedt ook isolatie op gebruikersniveau voor Spark-activiteiten omdat elk Spark-cluster zich in zijn eigen subnet bevindt.

SQL-pool en SQL on-demand zijn multi-tenantmogelijkheden en bevinden zich daarom buiten de Managed workspace VNet. Intra-workspace communicatie naar SQL pool en SQL on-demand gebruiken Azure private links. Deze privékoppelingen worden automatisch voor u gemaakt wanneer u een werkruimte maakt waaraan vnet voor beheerde werkruimte is gekoppeld.

>[!IMPORTANT]
>U deze werkruimteconfiguratie niet wijzigen nadat de werkruimte is gemaakt. U bijvoorbeeld geen werkruimte opnieuw configureren waaraan vnet van beheerde werkruimte is gekoppeld en waaraan een VNet is gekoppeld. U ook een werkruimte niet opnieuw configureren met een VNet voor beheerde werkruimte en het VNet ervan loskoppelen.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Een Azure Synapse-werkruimte maken met een VNet voor beheerde werkruimte

Als u een Azure Synapse-werkruimte wilt maken waaraan een VNet voor beheerde werkruimte is gekoppeld, schakelt u het tabblad **Beveiliging + netwerken** in Azure-portal in en schakelt u het selectievakje **Beheerd virtueel netwerk inschakelen** in.

Als u het selectievakje niet hebt ingeschakeld, is er geen VNet aan uw werkruimte gekoppeld.

>[!IMPORTANT]
>U alleen privékoppelingen gebruiken in een werkruimte met een VNet voor beheerde werkruimte.

![Beheerde werkruimte VNet inschakelen](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Al het uitgaande verkeer van de Beheerde werkruimte VNet wordt in de toekomst geblokkeerd. Het wordt aanbevolen om verbinding te maken met al uw gegevensbronnen met beheerde privéeindpunten.

U controleren of uw Azure Synapse-werkruimte is gekoppeld aan een VNet voor beheerde werkruimte door **Overzicht** te selecteren in Azure-portal.

![Overzicht van werkruimtein Azure-portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse Workspace maken](../quickstart-create-workspace.md)

Meer informatie over [beheerde privéeindpunten](./synapse-workspace-managed-private-endpoints.md)

[Beheerde privéeindpunten maken voor uw gegevensbronnen](./how-to-create-managed-private-endpoints.md)
