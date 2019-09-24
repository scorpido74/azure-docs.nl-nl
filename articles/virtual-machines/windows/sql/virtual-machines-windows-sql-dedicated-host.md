---
title: SQL Server VM op een door Azure toegewezen host
description: Meer informatie over de Details voor het uitvoeren van een SQL Server VM op een aan Azure toegewezen host.
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
ms.openlocfilehash: 8b29bbce1511b022def522d46c74b99967a76ea3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204521"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM op een door Azure toegewezen host 

In dit artikel worden de details van het gebruik van een SQL Server virtuele machine met een voor [Azure toegewezen host](/azure/virtual-machines/windows/dedicated-hosts)beschreven. Meer informatie over de specifieke Azure-host vindt u in het blog bericht [Inleiding tot de toegewezen Azure-host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Overzicht
De [toegewezen Azure-host](/azure/virtual-machines/windows/dedicated-hosts) is een service die fysieke servers biedt en een of meer virtuele machines kan hosten die zijn toegewezen aan één Azure-abonnement. Toegewezen hosts zijn dezelfde fysieke servers die worden gebruikt in Data Centers van micro soft, als een resource. U kunt toegewezen hosts inrichten binnen een regio, een beschikbaarheids zone en een fout domein. Vervolgens kunt u Vm's rechtstreeks op uw ingerichte hosts plaatsen, in welke configuratie het beste voldoet aan uw behoeften.


[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]


## <a name="licensing"></a>Licenties

U kunt kiezen tussen twee verschillende licentie opties bij het toevoegen van uw SQL Server-VM aan een toegewezen Azure-host. 

  - **SQL VM-licentie verlening**: Dit is de bestaande licentie optie, waar u voor elke SQL Server VM-licentie afzonderlijk betaalt. 
  - **Dedicated host Licensing**: Het nieuwe licentie model dat beschikbaar is voor de met Azure toegewezen host, waarbij SQL Server licenties worden gebundeld en betaald op hostniveau. 


Opties op hostniveau voor het gebruik van bestaande SQL Server licenties: 
  - SQL Server Enterprise Edition Azure Hybrid Benefit
    - Beschikbaar voor klanten met SA of een abonnement.
    - Licentie voor alle beschik bare fysieke kernen en geniet van onbeperkte virtualisatie (Maxi maal het maximum aantal Vcpu's dat door de host wordt ondersteund).
        - Zie de [Azure Hybrid Benefit Veelgestelde vragen](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)voor meer informatie over het Toep assen van de Azure Hybrid Benefit op een exclusieve Azure-host. 
  - SQL Server licenties die zijn verkregen vóór 1 oktober
      - SQL Server Enterprise Edition heeft zowel de licentie opties host-niveau als de virtuele machine. 
      - SQL Server Standard Edition heeft alleen de licentie optie-VM beschikbaar. 
          - Zie [de product voorwaarden van micro soft](https://www.microsoft.com/licensing/product-licensing/products)voor meer informatie. 
  - Als er geen SQL Server optie specifieke hostniveau is geselecteerd, kan SQL Server AHB worden geselecteerd op het niveau van afzonderlijke Vm's, net als met Vm's met meerdere tenants.



## <a name="provisioning"></a>Inrichting  
Het inrichten van een SQL Server-VM naar de toegewezen host is niet anders dan andere virtuele Azure-machines. U kunt dit doen met behulp van [Azure PowerShell](../dedicated-hosts-powershell.md), de [Azure Portal](../dedicated-hosts-portal.md)en [Azure cli](../../linux/dedicated-hosts-cli.md).

Het proces voor het toevoegen van een bestaande SQL Server VM aan de toegewezen host vereist downtime, maar heeft geen invloed op gegevens en heeft geen gegevens verlies. Voor alle data bases, inclusief systeem databases, moet eerst een back-up worden gemaakt voordat deze kan worden verplaatst.

## <a name="virtualization"></a>Virtualisatie 

Een van de voor delen van een speciale host is een onbeperkte virtualisatie. U kunt bijvoorbeeld licenties hebben voor 64 vCores, maar u kunt de host zo configureren dat deze 128 vCores heeft, zodat u dubbele vCores krijgt, maar u alleen betaalt voor de helft van de SQL Server-licenties. 

Omdat het de host is, komt u in aanmerking voor het instellen van virtualisatie met een verhouding van 1:2. 

## <a name="faq"></a>Veelgestelde vragen

**V: Hoe werkt de Azure Hybrid Benefit voor Windows Server/SQL Server-licenties op een toegewezen Azure-host?**

A: Klanten kunnen de waarde van hun bestaande Windows Server-en SQL Server-licenties met Software Assurance gebruiken, of abonnements licenties kwalificeren, om een gereduceerd tarief te betalen op een toegewezen Azure-host met behulp van Azure Hybrid Benefit. Klanten met Windows Server Data Center en SQL Server Enterprise Edition krijgen een onbeperkte virtualisatie (zo veel mogelijk virtuele Windows Server-machines implementeren op de host, afhankelijk van de fysieke capaciteit van de onderliggende server) wanneer ze een licentie voor de hele host hebben en gebruik Azure Hybrid Benefit.  Alle Windows Server-en SQL Server-workloads in de toegewezen Azure-host komen ook in aanmerking voor uitgebreide beveiligings updates voor Windows Server en SQL Server 2008/R2 zonder extra kosten. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


