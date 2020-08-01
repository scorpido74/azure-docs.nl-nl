---
title: VRealize-bewerkingen instellen voor Azure VMware-oplossing (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476006"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>VRealize-bewerkingen instellen voor Azure VMware-oplossing (AVS)


vRealize Operations Manager is een operations management-platform waarmee VMware-infrastructuur beheerders systeem bronnen kunnen bewaken. Deze systeem bronnen kunnen op toepassings niveau of op infrastructuur niveau (zowel fysieke als virtuele) objecten zijn. In het verleden hebben de meeste VMware-beheerders vRealize-bewerkingen gebruikt voor het bewaken en beheren van de onderdelen van de VMware Private Cloud – vCenter, ESXi, NSX-T, vSAN en Hybrid Cloud extension (HCX). Elke AVS-privécloud is ingericht met een speciale vCenter-, NSX-T-, vSAN-en HCX-implementatie. 

Controleer eerst [voordat u](#before-you-begin) aan de slag gaat en voordat u aan de [vereisten](#prerequisites) voldoet. Vervolgens wordt u stapsgewijs begeleid bij de twee typische implementatie topologieën voor vRealize Operations Manager met AVS:

> [!div class="checklist"]
> * [On-premises vRealize-bewerkingen AVS-implementatie beheren](#on-premises-vrealize-operations-managing-avs-deployment)
> * [vRealize-bewerkingen die worden uitgevoerd op de AVS-implementatie](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Voordat u begint
* Raadpleeg de [product documentatie voor vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor meer informatie over het implementeren van VRealize-bewerkingen. 
* Bekijk de [zelf studie reeks](tutorial-network-checklist.md)Basic AVS Software Defined Data Center (SDDC).
* Raadpleeg desgewenst de product documentatie voor [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) voor de implementatie optie voor het beheer van de AVS voor on-premises VRealize-bewerkingen. 



## <a name="prerequisites"></a>Vereisten
* VPN-of Azure-ExpressRoute moet worden geconfigureerd tussen on-premises en AVS SDDC.
* Er is een AVS-privécloud geïmplementeerd in Azure.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>On-premises vRealize-bewerkingen AVS-implementatie beheren
De meeste klanten hebben een bestaande on-premises implementatie van vRealize-bewerkingen die worden gebruikt voor het beheren van een of meer on-premises vCenter-domeinen. Wanneer klanten een nieuwe automatische AVS-Cloud in azure inrichten, verbinden ze meestal hun lokale omgeving met AVS met behulp van een Azure ExpressRoute of een laag 3 VPN-oplossing, zoals hieronder wordt weer gegeven.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="On-premises vRealize-bewerkingen AVS-implementatie beheren"  border="false":::

Als u de vRealize-bewerkingen wilt uitbreiden naar de cloud van de AVS, maakt u een[ instantie van een adapter voor de cloud resources](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) van de AVS, om gegevens te verzamelen van de automatische AVS-Cloud en deze naar on-premises VRealize-bewerkingen te brengen. De on-premises vRealize Operations Manager-exemplaar kan rechtstreeks verbinding maken met de vCenter-en NSX-T-Manager op AVS of u kunt optioneel een vRealize-bewerkingen externe Collector op de AVS-privécloud implementeren. Een externe Collector van vRealize comprimeert en versleutelt de gegevens die zijn verzameld uit de AVS-privécloud voordat deze wordt verzonden via het ExpressRoute-of VPN-netwerk naar de vRealize Operations Manager op locatie worden uitgevoerd. 

> [!TIP]
> Raadpleeg de [documentatie van VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor stapsgewijze hand leiding voor het installeren van vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-avs-deployment"></a>vRealize-bewerkingen die worden uitgevoerd op de AVS-implementatie

Een andere implementatie optie is het implementeren van een exemplaar van vRealize Operations Manager op een van de vSphere-clusters in de privécloud van de AVS, zoals hieronder wordt weer gegeven. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize-bewerkingen die worden uitgevoerd op AVS" border="false":::

Nadat u het AVS-exemplaar van vRealize-bewerkingen hebt geïmplementeerd, kunt u vRealize-bewerkingen configureren voor het verzamelen van gegevens van vCenter, ESXi, NSX-T, vSAN en HCX. 

> [!TIP]
> Raadpleeg de [documentatie van VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) voor stapsgewijze hand leiding voor het installeren van vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




