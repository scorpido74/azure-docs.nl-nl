---
title: Beheerd virtueel netwerk in Azure Synapse Analytics
description: Een artikel waarin het beheerde virtuele netwerk in Azure Synapse Analytics wordt toegelicht
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b46ce6f6164479853bc762cb1ca45d67f7f80930
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194362"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Beheerd virtueel netwerk in Azure Synapse Analytics (preview)

In dit artikel wordt het beheerde virtuele netwerk in Azure Synapse Analytics toegelicht.

## <a name="managed-workspace-vnet"></a>VNet van beheerde werkruimte

Wanneer u een Azure Synapse-werkruimte maakt, kunt u deze koppelen aan een VNet. Het VNet dat is gekoppeld aan uw werkruimte wordt beheerd door Azure Synapse. Dit VNet heet een *VNet van een beheerde werkruimte*.

Het VNet van een beheerde werkruimte is handig om vier redenen:

- Met het VNet van een beheerde werkruimte wordt het beheren van het VNet in Azure Synapse eenvoudiger.
- U hoeft geen inkomende NSG-regels in te stellen op uw eigen VNets om Azure Synapse-beheerverkeer in uw VNet toe te staan. Een onjuiste configuratie van deze NSG-regels leidt ertoe dat de service wordt onderbroken voor klanten.
- U hoeft geen subnet voor uw Spark-clusters te maken op basis van de piekbelasting.
- Het VNet van een beheerde werkruimte met beheerde privé-eindpunten beschermt u tegen gegevensoverdracht. U kunt alleen beheerde privé-eindpunten maken in een werkruimte waaraan een VNet van een beheerde werkruimte is gekoppeld.

Als u een werkruimte maakt waaraan een VNet van een beheerde werkruimte is gekoppeld, zorgt u ervoor dat uw werkruimte in het netwerk is afgeschermd van andere werkruimten. Azure Synapse biedt verschillende analysemogelijkheden in een werkruimte: Gegevensintegratie, Apache Spark, SQL-pool en SQL on-demand.

Als uw werkruimte beschikt over een VNet van een beheerde werkruimte, worden hierin gegevensintegratie en Spark-resources geïmplementeerd. Een VNet van een beheerde werkruimte biedt ook afscherming op gebruikersniveau voor Spark-activiteiten, omdat elk Spark-cluster zich in een eigen subnet bevindt.

SQL-pool en SQL on-demand zijn functies met meerdere tenants en bevinden zich daarom buiten het VNet van de beheerde werkruimte. Communicatie tussen werkruimten naar SQL-pool en SQL on-demand gebruiken privélinks van Azure. Deze privélinks worden automatisch gemaakt wanneer u een werkruimte maakt waaraan een VNet van een beheerde werkruimte is gekoppeld.

>[!IMPORTANT]
>U kunt deze werkruimteconfiguratie niet wijzigen nadat de werkruimte is gemaakt. U kunt een werkruimte waaraan geen VNet van een beheerde werkruimte is gekoppeld bijvoorbeeld niet opnieuw configureren en er een VNet aan koppelen. Ook kunt u een werkruimte waaraan wel een VNet van een beheerde werkruimte is gekoppeld niet opnieuw configureren en de VNet verwijderen.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Een Azure Synapse-werkruimte maken met een VNet van een beheerde werkruimte

Registreer de resourceprovider van het netwerk, als u dit nog niet hebt gedaan. Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider. Kies *Microsoft.Network* in de lijst met resourceproviders tijdens het [registreren](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Als u een Azure Synapse-werkruimte wilt maken waaraan een VNet van een beheerde werkruimte is gekoppeld, selecteert u het tabblad **Beveiliging en netwerk** in de Azure-portal en schakelt u het selectievakje **Beheerd virtueel netwerk inschakelen** in.

Als u het selectievakje uitgeschakeld laat, wordt er geen VNet gekoppeld aan de werkruimte.

>[!IMPORTANT]
>U kunt alleen privélinks gebruiken in een werkruimte die een VNet van een beheerde werkruimte heeft.

![VNet van beheerde werkruimte inschakelen](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Al het uitgaande verkeer van het beheerde-werkruimte-VNet, met uitzondering van verkeer van beheerde privé-eindpunten, wordt in de toekomst geblokkeerd. Het wordt aanbevolen dat u beheerde privé-eindpunten maakt om verbinding te maken met al uw Azure-gegevensbronnen die zich buiten de werkruimte bevinden. 

U kunt controleren of uw Azure Synapse-werkruimte is gekoppeld aan een VNet van een beheerde werkruimte door in de Azure-portal **Overzicht** te selecteren.

![Overzicht van werkruimten in de Azure-portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werkruimte](../quickstart-create-workspace.md) maken

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)

[Create Managed private endpoint to your data sources](./how-to-create-managed-private-endpoints.md) (Beheerde privé-eindpunten naar uw gegevensbronnen maken)
