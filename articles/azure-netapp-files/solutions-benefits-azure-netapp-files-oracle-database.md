---
title: Voordelen van het gebruik van Azure NetApp-bestanden met Oracle Database | Microsoft Documenten
description: Beschrijft de technologie en biedt een prestatievergelijking tussen Oracle Direct NFS (dNFS) en de traditionele NFS-client. Toont de voordelen van het gebruik van dNFS met Azure NetApp-bestanden.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: d28f5469174a2659869ebb01ef01653b7190e30e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772078"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Voordelen van het gebruik van Azure NetApp-bestanden met Oracle Database

Oracle Direct NFS (dNFS) maakt het mogelijk om hogere prestaties te leveren dan het eigen NFS-stuurprogramma van het besturingssysteem. In dit artikel wordt de technologie uitgelegd en wordt een prestatievergelijking gemaakt tussen dNFS en de traditionele NFS-client (Kernel NFS). Het toont ook de voordelen en het gemak van het gebruik van dNFS met Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Hoe Oracle Direct NFS werkt

Oracle Direct NFS (dNFS) omzeilt de buffercache van het besturingssysteem. Gegevens worden slechts eenmaal in de gebruikersruimte opgeslagen, waardoor de overhead van geheugenkopieën wordt geëlimineerd.  

De traditionele NFS-client gebruikt één netwerkstroom, zoals in het volgende voorbeeld wordt weergegeven: 

![Traditionele NFS-client met één netwerkstroom](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

Oracle dNFS verbetert daarentegen de prestaties door netwerkverkeer over meerdere netwerkstromen te balanceren. Met deze mogelijkheid kan de Oracle-database dynamisch een aanzienlijk aantal 650 verschillende netwerkverbindingen tot stand brengen, zoals in het onderstaande voorbeeld wordt weergegeven:  

![Oracle Direct NFS verbetert de prestaties](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

Uit [de Oracle FAQ voor Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) blijkt dat Oracle dNFS een geoptimaliseerde NFS-client is. Het biedt snelle en schaalbare toegang tot NFS-opslag die zich op NAS-opslagapparaten bevindt (toegankelijk via TCP/IP). dNFS is ingebouwd in de databasekernel, net als ASM, die voornamelijk wordt gebruikt met DAS- of SAN-opslag. Als zodanig *is de richtlijn om dNFS te gebruiken bij het implementeren van NAS-opslag en ASM te gebruiken bij de implementatie van SAN-opslag.*

dNFS is de standaardoptie in Oracle 18c en de standaardoptie voor RAC.

dNFS is beschikbaar vanaf Oracle Database 11g. Het onderstaande diagram vergelijkt dNFS met native NFS. Wanneer u dNFS gebruikt, kan een Oracle-database die op een virtuele Azure-machine wordt uitgevoerd, meer I/O aansturen dan de oorspronkelijke NFS-client.

![Oracle en Azure NetApp Files vergelijking van dNFS met native NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

U dNFS in- of uitschakelen door twee opdrachten uit te voeren en de database opnieuw op te starten.

Inschakelen:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Uitschakelen:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp-bestanden in combinatie met Oracle Direct NFS

U de prestaties van Oracle dNFS verbeteren met de Azure NetApp Files-service. De service geeft u volledige controle over de prestaties van uw toepassing. Het kan voldoen aan zeer veeleisende toepassingen. De combinatie van Oracle dNFS met Azure NetApp Files biedt een groot voordeel voor uw workloads.

## <a name="next-steps"></a>Volgende stappen

- [Oplossingsarchitecturen op basis van Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Overzicht van Oracle-toepassingen en -oplossingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)