---
title: VRealize-bewerkingen voor Azure VMware-oplossing instellen
description: Meer informatie over het instellen van vRealize-bewerkingen voor uw persoonlijke cloud van Azure VMware-oplossingen.
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 729ee5c64776d7d04f702af62451175f7c53421b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750397"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>VRealize-bewerkingen voor Azure VMware-oplossing instellen


vRealize Operations Manager is een operations management-platform waarmee VMware-infrastructuur beheerders systeem bronnen kunnen bewaken. Deze systeem bronnen kunnen op toepassings niveau of op infrastructuur niveau (zowel fysieke als virtuele) objecten zijn. In het verleden hebben de meeste VMware-beheerders vRealize-bewerkingen gebruikt voor het bewaken en beheren van de onderdelen van de VMware Private Cloud – vCenter, ESXi, NSX-T, vSAN en Hybrid Cloud extension (HCX). Elke persoonlijke cloud van Azure VMware-oplossingen is ingericht met een speciale vCenter-, NSX-T-, vSAN-en HCX-implementatie. 

Controleer eerst [voordat u](#before-you-begin) aan de slag gaat en voordat u aan de [vereisten](#prerequisites) voldoet. Vervolgens begeleiden u de twee typische implementatie topologieën voor vRealize Operations Manager met de Azure VMware-oplossing:

> [!div class="checklist"]
> * [On-premises vRealize-bewerkingen Azure VMware-oplossings implementatie beheren](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [vRealize-bewerkingen die worden uitgevoerd op de implementatie van de Azure VMware-oplossing](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Voordat u begint
* Raadpleeg de [product documentatie voor vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor meer informatie over het implementeren van VRealize-bewerkingen. 
* Bekijk de Basic- [zelf studie reeks zelf](tutorial-network-checklist.md)studies over Azure VMware Solution Software Defined Data Center (SDDC).
* Bekijk eventueel de product documentatie voor [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) voor de on-premises VRealize-bewerkingen de implementatie optie Azure VMware-oplossing beheren. 



## <a name="prerequisites"></a>Vereisten
* VPN-of Azure-ExpressRoute moet worden geconfigureerd tussen on-premises en Azure VMware Solution SDDC.
* Er is een Azure VMware-oplossings privécloud geïmplementeerd in Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>On-premises vRealize-bewerkingen Azure VMware-oplossings implementatie beheren
De meeste klanten hebben een bestaande on-premises implementatie van vRealize-bewerkingen die worden gebruikt voor het beheren van een of meer on-premises vCenter-domeinen. Wanneer klanten een nieuwe persoonlijke cloud van Azure VMware-oplossingen in azure inrichten, verbinden ze meestal hun on-premise omgeving met de Azure VMware-oplossing met behulp van een Azure ExpressRoute of met een Layer 3 VPN-oplossing, zoals hieronder wordt weer gegeven.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="On-premises vRealize-bewerkingen Azure VMware-oplossings implementatie beheren"  border="false":::

Als u de vRealize-mogelijkheden wilt uitbreiden naar de privécloud van Azure VMware, maakt u een [instantie van de Azure VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) -oplossing, voor het verzamelen van gegevens uit de privécloud van de Azure VMware-oplossing en om deze in on-premises VRealize-bewerkingen te brengen. De on-premises vRealize Operations Manager-exemplaar kan rechtstreeks verbinding maken met de vCenter-en NSX-T-manager in de Azure VMware-oplossing, of u kunt eventueel een vRealize Operations Remote Collector implementeren in de privécloud van de Azure VMware-oplossing. Een externe Collector van vRealize comprimeert en versleutelt de gegevens die zijn verzameld uit de privécloud van de Azure VMware-oplossing voordat deze via het ExpressRoute-of VPN-netwerk wordt verzonden naar de Operations Manager vRealize die on-premises wordt uitgevoerd. 

> [!TIP]
> Raadpleeg de [documentatie van VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor stapsgewijze hand leiding voor het installeren van vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>vRealize-bewerkingen die worden uitgevoerd op de implementatie van de Azure VMware-oplossing

Een andere implementatie optie is het implementeren van een exemplaar van vRealize Operations Manager op een van de vSphere-clusters in de privécloud van de Azure VMware-oplossing, zoals hieronder wordt weer gegeven. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize-bewerkingen die worden uitgevoerd op de Azure VMware-oplossing" border="false":::

Na de implementatie van het Azure VMware-oplossings exemplaar van vRealize-bewerkingen kunt u vRealize-bewerkingen configureren voor het verzamelen van gegevens van vCenter, ESXi, NSX-T, vSAN en HCX. 

> [!TIP]
> Raadpleeg de [documentatie van VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor stapsgewijze hand leiding voor het installeren van vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




