---
title: Beheerd virtueel netwerk
description: Een artikel waarin het beheerde virtuele netwerk in Azure Synapse Analytics wordt toegelicht
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 00920f30061832bd1d685f04113a63781df718b4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313692"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Beheerd virtueel netwerk in Azure Synapse Analytics (preview)

In dit artikel wordt het beheerde virtuele netwerk in Azure Synapse Analytics toegelicht.

## <a name="managed-workspace-virtual-network"></a>Een beheerd, aan werkruimte gekoppeld virtueel netwerk

Wanneer u een Azure Synapse-werkruimte maakt, kunt u ervoor kiezen om deze te koppelen aan een Microsoft Azure Virtual Network. Het virtuele netwerk dat is gekoppeld aan uw werkruimte wordt beheerd door Azure Synapse. Dit virtuele netwerk wordt een *beheerd, aan werkruimte gekoppeld virtueel netwerk* genoemd.

Een beheerd, aan werkruimte gekoppeld virtueel netwerk is handig om vier redenen:

- Met een beheerd, aan werkruimte gekoppeld virtueel netwerk kunt u de beheertaak voor het virtuele netwerk overlaten aan Azure Synapse.
- U hoeft geen inkomende NSG-regels in te stellen op uw eigen virtuele netwerken om Azure Synapse-beheerverkeer in uw virtuele netwerk toe te staan. Een onjuiste configuratie van deze NSG-regels leidt ertoe dat de service wordt onderbroken voor klanten.
- U hoeft geen subnet voor uw Spark-clusters te maken op basis van de piekbelasting.
- Met een beheerd, aan werkruimte gekoppeld virtueel netwerk in combinatie met beheerde privé-eindpunten wordt u beschermd tegen gegevensexfiltratie. U kunt alleen beheerde privé-eindpunten maken in een werkruimte waaraan een beheerd virtueel netwerk is gekoppeld.

Als u een werkruimte maakt waaraan een beheerd virtueel netwerk is gekoppeld, zorgt u ervoor dat uw werkruimte binnen het netwerk is afgeschermd van andere werkruimten. Azure Synapse biedt verschillende analysemogelijkheden in een werkruimte: Gegevensintegratie, serverloze Apache Spark-pool, toegewezen SQL-pool, en serverloze SQL-pool.

Als uw werkruimte beschikt over een beheerd virtueel netwerk, worden hierin gegevensintegratie en Spark-resources geïmplementeerd. Een beheerd, aan werkruimte gekoppeld virtueel netwerk biedt ook afscherming op gebruikersniveau voor Spark-activiteiten, omdat elk Spark-cluster zich in een eigen subnet bevindt.

Toegewezen SQL-pools en serverloze SQL-pools bieden mogelijkheden voor meerdere tenants, en bevinden zich daarom buiten het beheerde virtuele werkruimtenetwerk. Voor communicatie tussen werkruimten naar een toegewezen SQL-pool en een serverloze SQL-pool wordt gebruikgemaakt van Azure Private Links. Deze privélinks worden automatisch gemaakt wanneer u een werkruimte maakt waaraan een beheerd virtueel netwerk is gekoppeld.

>[!IMPORTANT]
>U kunt deze werkruimteconfiguratie niet wijzigen nadat de werkruimte is gemaakt. U kunt een werkruimte waaraan geen beheerd virtueel netwerk is gekoppeld bijvoorbeeld niet opnieuw configureren en er een virtueel netwerk aan koppelen. Evenzo kunt u een werkruimte waaraan geen beheerd virtueel netwerk is gekoppeld niet opnieuw configureren en het virtueel netwerk ervan ontkoppelen.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Een Azure Synapse-werkruimte maken met een beheerd, aan de werkruimte gekoppeld virtueel netwerk

Registreer de resourceprovider van het netwerk, als u dit nog niet hebt gedaan. Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider. Kies *Microsoft.Network* in de lijst met resourceproviders tijdens het [registreren](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Als u een Azure Synapse-werkruimte wilt maken waaraan een beheerd virtueel werkruimtenetwerk is gekoppeld, selecteert u het tabblad **Beveiliging** in Azure Portal, en schakelt u het selectievakje **Beheerd virtueel netwerk inschakelen** in.

Als u het selectievakje uitgeschakeld laat, wordt er geen virtueel netwerk gekoppeld aan de werkruimte.

>[!IMPORTANT]
>U kunt alleen privélinks gebruiken in een werkruimte die een beheerd, aan deze werkruimte gekoppeld virtueel netwerk heeft.

![Een beheerd, aan werkruimte gekoppeld virtueel netwerk inschakelen](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Al het uitgaande verkeer van het beheerde, aan uw werkruimte gekoppelde virtuele netwerk, met uitzondering van verkeer van beheerde privé-eindpunten, wordt in de toekomst geblokkeerd. Het wordt aanbevolen dat u beheerde privé-eindpunten maakt om verbinding te maken met al uw Azure-gegevensbronnen die zich buiten de werkruimte bevinden. 

U kunt controleren of uw Azure Synapse-werkruimte is gekoppeld aan een beheerd virtueel netwerk door in Azure Portal **Overzicht** te selecteren.

![Overzicht van werkruimten in de Azure-portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werkruimte](../quickstart-create-workspace.md) maken

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)

[Create Managed private endpoint to your data sources](./how-to-create-managed-private-endpoints.md) (Beheerde privé-eindpunten naar uw gegevensbronnen maken)
