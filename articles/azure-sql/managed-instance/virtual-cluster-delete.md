---
title: Een subnet verwijderen na het verwijderen van een door Azure SQL beheerd exemplaar
description: Meer informatie over het verwijderen van een virtueel Azure-netwerk na het verwijderen van een Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 73150ee893ca23ed7996b001bd02acaabbf89dce
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116678"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Een subnet verwijderen na het verwijderen van een door Azure SQL beheerd exemplaar
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat richt lijnen voor het hand matig verwijderen van een subnet na het verwijderen van het laatste beheerde exemplaar van Azure SQL.

Beheerde exemplaren worden geïmplementeerd in [virtuele clusters](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Elk virtueel cluster is gekoppeld aan een subnet. Het virtuele cluster wordt gedurende 12 uur na het verwijderen van de laatste instantie bewaard, zodat u sneller beheerde instanties kunt maken in hetzelfde subnet. Er worden geen kosten in rekening gebracht voor het bewaren van een leeg virtueel cluster. Gedurende deze periode kan het subnet dat is gekoppeld aan het virtuele-cluster, niet worden verwijderd.

Als u 12 uur niet wilt wachten en de voor keur geeft om het virtuele cluster en het subnet eerder te verwijderen, kunt u dit hand matig doen. Verwijder het virtuele cluster hand matig met behulp van de Azure Portal of de API voor virtuele clusters.

> [!IMPORTANT]
> - Het virtuele cluster mag geen beheerde instanties bevatten zodat de verwijdering kan worden voltooid. 
> - Het verwijderen van een virtueel cluster is ongeveer 1,5 uur lang operationeel (Zie [bewerkingen voor beheer van SQL Managed instances](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) voor actuele virtuele cluster tijd voor het verwijderen), waarbij het virtuele cluster nog steeds zichtbaar is in de portal totdat dit proces is voltooid.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtueel cluster verwijderen uit de Azure Portal

Als u een virtueel cluster wilt verwijderen met behulp van de Azure Portal, zoekt u de virtuele cluster resources.

![Scherm afbeelding van de Azure Portal, waarbij het zoekvak is gemarkeerd](./media/virtual-cluster-delete/virtual-clusters-search.png)

Nadat u het virtuele cluster dat u wilt verwijderen hebt gevonden, selecteert u deze resource en selecteert u **verwijderen**. U wordt gevraagd om het verwijderen van het virtuele cluster te bevestigen.

![Scherm afbeelding van het dash board Azure Portal virtuele clusters, met de optie verwijderen gemarkeerd](./media/virtual-cluster-delete/virtual-clusters-delete.png)

In Azure Portal meldingen wordt een bevestiging weer gegeven dat de aanvraag voor het verwijderen van het virtuele cluster is verzonden. De verwijderings bewerking zelf duurt ongeveer 1,5 uur gedurende welke het virtuele cluster nog steeds zichtbaar is in de portal. Zodra het proces is voltooid, is het virtuele cluster niet meer zichtbaar en wordt het bijbehorende subnet vrijgegeven voor hergebruik.

> [!TIP]
> Als er geen beheerde exemplaren worden weer gegeven in het virtuele cluster en u het virtuele cluster niet kunt verwijderen, moet u ervoor zorgen dat er geen doorlopende implementatie van de instantie wordt uitgevoerd. Dit omvat gestarte en geannuleerde implementaties die nog worden uitgevoerd. Dit komt doordat deze bewerkingen het virtuele cluster nog steeds gebruiken om het uit de verwijdering te vergren delen. Het tabblad implementaties controleren van de resource groep waarop het exemplaar is geïmplementeerd, geeft aan dat er implementaties worden uitgevoerd. Wacht in dit geval tot de implementatie is voltooid, verwijder het beheerde exemplaar en vervolgens het virtuele cluster.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtueel cluster verwijderen met de API

Als u een virtueel cluster wilt verwijderen via de API, gebruikt u de URI-para meters die zijn opgegeven in de methode voor het [verwijderen van virtuele clusters](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Meer informatie over [connectiviteits architectuur in het beheerde exemplaar](connectivity-architecture-overview.md).
- Meer informatie over het [wijzigen van een bestaand virtueel netwerk voor een beheerd exemplaar](vnet-existing-add-subnet.md).
- Zie [een Azure SQL Managed instance maken](instance-create-quickstart.md)voor een zelf studie waarin wordt getoond hoe u een virtueel netwerk maakt, een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-up van een Data Base.
- Zie [Configuring a Custom DNS](custom-dns-configure.md)(Engelstalig) voor DNS-problemen.
