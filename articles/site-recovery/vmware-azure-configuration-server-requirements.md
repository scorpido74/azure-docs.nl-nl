---
title: VMware-serververeisten voor noodherstel herstel in Azure Site Recovery
description: In dit artikel worden ondersteuning en vereisten beschreven bij het implementeren van de configuratieserver voor VMware-noodherstel naar Azure met Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257405"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Configuratieserververeisten voor VMware-noodherstel naar Azure

U implementeert een on-premises configuratieserver wanneer u [Azure Site Recovery](site-recovery-overview.md) gebruikt voor noodherstel van VMware VM's en fysieke servers naar Azure.

- De configuratieserver coördineert de communicatie tussen on-premises VMware en Azure. Het beheert ook gegevensreplicatie.
- [Meer informatie](vmware-azure-architecture.md) over de onderdelen en processen van de configuratieserver.

## <a name="configuration-server-deployment"></a>Configuratieserverimplementatie

Voor noodherstel van VMware VM's naar Azure implementeert u de configuratieserver als VMware VM.

- Siteherstel biedt een OVA-sjabloon die u downloadt van de Azure-portal en importeert in vCenter Server om de vm van de configuratieserver in te stellen.
- Wanneer u de configuratieserver implementeert met de OVA-sjabloon, voldoet de VM automatisch aan de vereisten in dit artikel.
- We raden u ten zeerste aan de configuratieserver in te stellen met behulp van de OVA-sjabloon. Als u echter noodherstel voor VMware-VM's instelt en de OVA-sjabloon niet gebruiken, u de configuratieserver implementeren met behulp van [de meegeleverde instructies.](physical-azure-set-up-source.md)
- Als u de configuratieserver implementeert voor noodherstel van on-premises fysieke machines in Azure, volgt u de instructies in [dit artikel](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Volgende stappen
Stel disaster recovery van [VMware VM's](vmware-azure-tutorial.md) in op Azure.
