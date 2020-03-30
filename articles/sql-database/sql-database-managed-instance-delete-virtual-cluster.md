---
title: Een subnet verwijderen na het verwijderen van een beheerde instantie
description: Meer informatie over het verwijderen van een virtueel Azure-netwerk na het verwijderen van een door Azure SQL Database beheerde instantie.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820470"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Een subnet verwijderen na het verwijderen van een beheerde Azure SQL Database-instantie

In dit artikel vindt u richtlijnen voor het handmatig verwijderen van een subnet na het verwijderen van de laatste Azure SQL Database beheerde instantie die erin zit.

Beheerde exemplaren worden geïmplementeerd in [virtuele clusters.](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) Elk virtueel cluster is gekoppeld aan een subnet. Het virtuele cluster blijft 12 uur na het verwijderen van de laatste instantie bij ontwerp overeind, zodat u sneller beheerde exemplaren in hetzelfde subnet maken. Er zijn geen kosten verbonden aan het bewaren van een leeg virtueel cluster. Gedurende deze periode kan het subnet dat is gekoppeld aan het virtuele-cluster, niet worden verwijderd.

Als u niet 12 uur wilt wachten en het virtuele cluster en het subnet liever eerder wilt verwijderen, u dit handmatig doen. Verwijder het virtuele cluster handmatig met behulp van de Azure-portal of de API voor virtuele clusters.

> [!IMPORTANT]
> - Het virtuele cluster mag geen beheerde exemplaren bevatten om de verwijdering te laten slagen. 
> - Verwijdering van een virtueel cluster is een langdurige bewerking die ongeveer 1,5 uur duurt (zie [Beheerde instantiebeheerbewerkingen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) voor up-to-date virtuele clusterverwijderingstijd) waarbij het virtuele cluster nog steeds zichtbaar is in de portal totdat dit proces is voltooid.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtueel cluster verwijderen uit de Azure-portal

Als u een virtueel cluster wilt verwijderen met behulp van de Azure-portal, zoekt u naar de virtuele clusterbronnen.

![Schermafbeelding van de Azure-portal, met het zoekvak gemarkeerd](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Nadat u het virtuele cluster hebt gevonden dat u wilt verwijderen, selecteert u deze bron en selecteert u **Verwijderen**. U wordt gevraagd de verwijdering van het virtuele cluster te bevestigen.

![Schermafbeelding van het dashboard virtuele clusters van Azure-portal, met de optie Verwijderen gemarkeerd](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure-portalmeldingen tonen u een bevestiging dat het verzoek om het virtuele cluster te verwijderen is ingediend. Verwijderingsbewerking zelf duurt ongeveer 1,5 uur waarin het virtuele cluster nog steeds zichtbaar is in portal. Zodra het proces is voltooid, is het virtuele cluster niet meer zichtbaar en wordt het subnet dat eraan is gekoppeld, vrijgegeven voor hergebruik.

> [!TIP]
> Als er geen beheerde exemplaren worden weergegeven in het virtuele cluster en u het virtuele cluster niet verwijderen, moet u ervoor zorgen dat er geen lopende instantie-implementatie wordt uitgevoerd. Dit omvat gestarte en geannuleerde implementaties die nog in uitvoering zijn. Dit komt omdat deze bewerkingen nog steeds gebruik maken van het virtuele cluster dat het vergrendelt van verwijdering. Als u het tabblad Implementaties bekijkt van de resourcegroep waaraan de instantie is geïmplementeerd, worden eventuele implementaties aangegeven die worden uitgevoerd. Wacht in dit geval tot de implementatie is voltooid, verwijder de beheerde instantie en vervolgens het virtuele cluster.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtueel cluster verwijderen met behulp van de API

Als u een virtueel cluster via de API wilt verwijderen, gebruikt u de URI-parameters die zijn opgegeven in de [methode voor het verwijderen van virtuele clusters](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerde instantie voor](sql-database-managed-instance.md)een overzicht.
- Meer informatie over [connectiviteitsarchitectuur in Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Meer informatie over het [wijzigen van een bestaand virtueel netwerk voor Beheerde instantie](sql-database-managed-instance-configure-vnet-subnet.md).
- Zie Een Azure SQL Database Managed Instance maken voor een zelfstudie die laat zien hoe u een virtueel netwerk maakt, een beheerde instantie maakt en een database herstelt van een databaseback.For a tutorial that shows how to create a virtual network, create a Managed Instance, create a Managed Instance and restore a database from a database backup, see [Create an Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Zie [Een aangepaste DNS configureren](sql-database-managed-instance-custom-dns.md)voor DNS-problemen.
