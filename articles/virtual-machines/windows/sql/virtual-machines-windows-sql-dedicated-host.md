---
title: SQL Server VM op een Azure Dedicated Host
description: Meer informatie over de details voor het uitvoeren van een SQL Server VM op een Azure Dedicated Host.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834354"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM op een Azure Dedicated Host 

In dit artikel worden de specifieke kenmerken van het gebruik van een SQL Server VM met een [Azure Dedicated Host beschreven.](/azure/virtual-machines/windows/dedicated-hosts) Aanvullende informatie over de azure dedicated host is te vinden in het blogbericht [Introducing Azure Dedicated Host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Overzicht
[Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) is een service die fysieke servers biedt - die een of meer virtuele machines kunnen hosten - die zijn toegewezen aan één Azure-abonnement. Dedicated hosts zijn dezelfde fysieke servers die worden gebruikt in de datacenters van Microsoft, die als bron worden geleverd. U speciale hosts inrichten binnen een regio, beschikbaarheidszone en foutdomein. Vervolgens u VM's rechtstreeks in uw ingerichte hosts plaatsen, in elke configuratie die het beste aan uw behoeften voldoet.

## <a name="limitations"></a>Beperkingen

- Virtuele machineschaalsets worden momenteel niet ondersteund op speciale hosts.
- De volgende VM-series worden ondersteund: DSv3 en ESv3. 

## <a name="licensing"></a>Licentieverlening

U kiezen uit twee verschillende licentieopties wanneer u uw SQL Server VM toevoegt aan een Azure Dedicated Host. 

  - **SQL VM-licenties**: dit is de bestaande licentieoptie, waarbij u voor elke SQL Server VM-licentie afzonderlijk betaalt. 
  - **Speciale hostlicenties:** het nieuwe licentiemodel dat beschikbaar is voor de Azure Dedicated Host, waarbij SQL Server-licenties worden gebundeld en betaald op hostniveau. 


Opties op hostniveau voor het gebruik van bestaande SQL Server-licenties: 
  - SQL Server Enterprise Edition Azure Hybrid-voordeel
    - Beschikbaar voor klanten met SA of abonnement.
    - Licentie alle beschikbare fysieke kernen en geniet van onbeperkte virtualisatie (tot de max vCPU's ondersteund door de host).
        - Zie de [veelgestelde vragen](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)over Azure Hybrid Benefit voor meer informatie over het toepassen van de Azure Hybrid Benefit op Azure Dedicated-host. 
  - SQL Server-licenties aangeschaft vóór 1 oktober
      - SQL Server Enterprise edition heeft zowel host-level als by-VM licentieopties. 
      - SQL Server Standard-editie heeft alleen de standaard-VM-licentieoptie beschikbaar. 
          - Zie de [productvoorwaarden van Microsoft](https://www.microsoft.com/licensing/product-licensing/products)voor meer informatie. 
  - Als er geen SQL Server-optie voor dedicated host-level is geselecteerd, kan SQL Server AHB worden geselecteerd op het niveau van afzonderlijke VM's, net als bij vm's met meerdere tenant's.



## <a name="provisioning"></a>Inrichten  
Het inrichten van een SQL Server VM aan de dedicated host is niet anders dan elke andere Azure Virtual Machine. U dit doen met [Azure PowerShell,](../dedicated-hosts-powershell.md)de [Azure-portal](../dedicated-hosts-portal.md)en [Azure CLI](../../linux/dedicated-hosts-cli.md).

Het proces van het toevoegen van een bestaande SQL Server VM aan de dedicated host vereist downtime, maar heeft geen invloed op gegevens en heeft geen gegevensverlies. Niettemin moeten alle databases, inclusief systeemdatabases, voorafgaand aan de verhuizing worden geback-upt.

## <a name="virtualization"></a>Virtualisatie 

Een van de voordelen van een dedicated host is onbeperkte virtualisatie. U bijvoorbeeld licenties voor 64 vCores hebben, maar u de host configureren met 128 vCores, zodat u het dubbele van de vCores krijgt, maar slechts de helft van de SQL Server-licenties betaalt. 

Aangezien het uw host is, komt u in aanmerking om de virtualisatie in te stellen met een verhouding van 1:2. 

## <a name="faq"></a>Veelgestelde vragen

**V: Hoe werkt de Azure Hybrid Benefit voor Windows Server/SQL Server-licenties op Azure Dedicated Host?**

A: Klanten kunnen de waarde van hun bestaande Windows Server- en SQL Server-licenties met Software Assurance of in aanmerking komende abonnementslicenties gebruiken om een verlaagd tarief te betalen op Azure Dedicated Host met Azure Hybrid Benefit. Klanten van Windows Server Datacenter en SQL Server Enterprise Edition krijgen onbeperkte virtualisatie (implementeer zoveel mogelijk Virtuele Windows Server-machines op de host onder voorbehoud van de fysieke capaciteit van de onderliggende server) wanneer ze de volledige host in licentie geven en gebruik Azure Hybrid Benefit.  Alle Windows Server- en SQL Server-workloads in Azure Dedicated Host komen ook in aanmerking voor Extended Security Updates voor Windows Server en SQL Server 2008/R2 zonder extra kosten. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-vm](virtual-machines-windows-sql-server-iaas-faq.md)
* [Richtlijnen voor prijzen voor SQL Server op een Windows-vm](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notities voor SQL Server vrijgeven op een Windows-vm](virtual-machines-windows-sql-server-iaas-release-notes.md)


