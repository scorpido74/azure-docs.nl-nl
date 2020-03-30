---
title: Een Azure-serviceonderbreking verwerken die gevolgen heeft voor Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Meer informatie over wat u moet doen in het geval van een Azure-serviceonderbreking die gevolgen heeft voor Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: e3f0fd88eb302dac208f43d0622ae28b31dcddc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157503"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Wat te doen wanneer een Azure-service wordt onderbroken, wat gevolgen heeft voor Azure Cloud Services
Bij Microsoft werken we er hard aan om ervoor te zorgen dat onze services altijd voor u beschikbaar zijn wanneer u ze nodig hebt. Krachten buiten onze controle beïnvloeden ons soms op manieren die ongeplande serviceonderbrekingen veroorzaken.

Microsoft biedt een SLA (Service Level Agreement) voor zijn services als een verbintenis voor uptime en connectiviteit. De SLA voor afzonderlijke Azure-services is te vinden bij [Azure Service Level Agreements.](https://azure.microsoft.com/support/legal/sla/)

Azure heeft al veel ingebouwde platformfuncties die zeer beschikbare toepassingen ondersteunen. Lees Voor meer informatie over deze services [Disaster recovery en hoge beschikbaarheid voor Azure-toepassingen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Dit artikel behandelt een echt scenario voor noodherstel, wanneer een hele regio een storing ondervindt als gevolg van een grote natuurramp of wijdverspreide onderbreking van de service. Dit zijn zeldzame gebeurtenissen, maar u moet zich voorbereiden op de mogelijkheid dat er een storing van een hele regio is. Als een hele regio een onderbreking van de service ondervindt, zijn de lokaal overbodige kopieën van uw gegevens tijdelijk niet beschikbaar. Als u georeplicatie hebt ingeschakeld, worden drie extra exemplaren van uw Azure Storage-blobs en -tabellen in een andere regio opgeslagen. In het geval van een volledige regionale storing of een ramp waarbij de primaire regio niet kan worden hersteld, brengt Azure alle DNS-vermeldingen opnieuw in het geo-gerepliceerde gebied.

> [!NOTE]
> Houd er rekening mee dat u geen controle hebt over dit proces en dat dit alleen geldt voor serviceonderbrekingen in het hele datacenter. Hierdoor moet u ook vertrouwen op andere toepassingsspecifieke back-upstrategieën om het hoogste niveau van beschikbaarheid te bereiken. Zie [Herstel na noodgevallen en hoge beschikbaarheid voor toepassingen die zijn gebouwd op Microsoft Azure voor](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)meer informatie. Als u uw eigen failover wilt kunnen beïnvloeden, u overwegen om het gebruik van [read-access geo-redundante opslag (RA-GRS)](../storage/common/storage-redundancy.md)te overwegen, waardoor een alleen-lezen kopie van uw gegevens in een andere regio wordt gemaakt.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Optie 1: Een back-upimplementatie gebruiken via Azure Traffic Manager
De meest robuuste oplossing voor noodherstel omvat het onderhouden van meerdere implementaties van uw toepassing in verschillende regio's en vervolgens [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) gebruiken om verkeer tussen deze toepassingen te leiden. Azure Traffic Manager biedt meerdere [routeringsmethoden,](../traffic-manager/traffic-manager-routing-methods.md)zodat u kiezen of u uw implementaties wilt beheren met behulp van een primair/back-upmodel of om verkeer tussen deze methoden te splitsen.

![Azure Cloud Services in verschillende regio's balanceren met Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Voor de snelste reactie op het verlies van een regio is het belangrijk dat u de eindpuntbewaking van Traffic Manager [configureert.](../traffic-manager/traffic-manager-monitoring.md)

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Optie 2: Uw toepassing implementeren in een nieuwe regio
Het onderhouden van meerdere actieve implementaties zoals beschreven in de vorige optie brengt extra lopende kosten met zich mee. Als uw hersteltijddoelstelling (RTO) flexibel genoeg is en u de oorspronkelijke code of het gecompileerde Cloud Services-pakket hebt, u een nieuw exemplaar van uw toepassing in een andere regio maken en uw DNS-records bijwerken om naar de nieuwe implementatie te wijzen.

Zie [Een cloudservice maken en implementeren](cloud-services-how-to-create-deploy-portal.md)voor meer informatie over het maken en implementeren van een cloudservicetoepassing.

Afhankelijk van uw toepassingsgegevensbronnen moet u mogelijk de herstelprocedures voor uw toepassingsgegevensbron controleren.

* Zie Redundantie van Azure Storage voor Azure [Storage-redundantie](../storage/common/storage-redundancy.md) om te controleren welke opties beschikbaar zijn op basis van het gekozen redundantiemodel voor uw toepassing.
* Lees voor SQL Database-bronnen [Overzicht: Cloud business continuity en database disaster recovery with SQL Database](../sql-database/sql-database-business-continuity.md) to check on the options that are available based on the chosen replication model for your application.


## <a name="option-3-wait-for-recovery"></a>Optie 3: Wachten op herstel
In dit geval is er geen actie van uw kant vereist, maar uw service is niet beschikbaar totdat de regio is hersteld. U de huidige servicestatus zien op het [Azure Service Health Dashboard](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Volgende stappen
Zie [Disaster recovery en hoge beschikbaarheid voor Azure-toepassingen voor](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)meer informatie over het implementeren van een strategie voor noodherstel en hoge beschikbaarheid.

Zie [technische richtlijnen](/azure/architecture/checklist/resiliency-per-service)voor azure tolerantie om een gedetailleerd technisch inzicht in de mogelijkheden van een cloudplatform te ontwikkelen.