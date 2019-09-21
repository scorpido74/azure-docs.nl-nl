---
title: Een hub-en-spoke hybride netwerk topologie maken met terraform in azure
description: Zelf studie waarin wordt uitgelegd hoe u een volledige referentie architectuur van een hybride netwerk maakt in azure met behulp van terraform
services: terraform
ms.service: azure
keywords: terraform, hub en spoke, netwerken, hybride netwerken, devops, virtuele machine, azure, vnet-peering, virtueel netwerk apparaat
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 5c2a61dd9da6d233a4b1410042f2125a1c300758
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173452"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Zelfstudie: Een hub-en-spoke hybride netwerk topologie maken met terraform in azure

In deze reeks zelf studies ziet u hoe u terraform kunt gebruiken om in azure een [hub-en spoke-netwerk topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)te implementeren. 

Een hub-en-spoke-topologie is een manier om workloads te isoleren terwijl algemene services worden gedeeld. Deze services omvatten identiteit en beveiliging. De hub is een virtueel netwerk (VNet) dat fungeert als een centraal verbindings punt voor een on-premises netwerk. De punten zijn VNets die zijn gekoppeld aan de hub. Gedeelde services worden geïmplementeerd in de hub, terwijl afzonderlijke workloads binnen spoke-netwerken worden geïmplementeerd.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gebruik HCL (HashiCorp Language) voor het indelen van hub-en spoke hybride netwerk referentie architectuur bronnen
> * Terraform gebruiken om resources van het netwerk apparaat te maken
> * Gebruik terraform voor het maken van een hub-netwerk in azure om te fungeren als een gemeen schappelijk punt voor alle resources
> * Terraform gebruiken om afzonderlijke werk belastingen te maken als spoke VNets in azure
> * Terraform gebruiken om gateways en verbindingen te maken tussen on-premises en Azure-netwerken
> * Terraform gebruiken om VNet-peerings te maken voor spoke-netwerken

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: Als u nog geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

- **Terraform installeren en configureren**: Voor het inrichten van Vm's en andere infra structuur in azure, [installeert en configureert u terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Architectuur hub en spoke-topologie

In de hub-en spoke-topologie is de hub een VNet. Het VNet fungeert als een centraal punt van connectiviteit met uw on-premises netwerk. De spokes of spaken zijn VNets die via peering zijn verbonden met de hub, en die kunnen worden gebruikt om workloads te isoleren. Verkeer stroomt tussen het on-premises datacentrum en de hub via een ExpressRoute- of VPN-gateway-verbinding. In de volgende afbeelding ziet u de onderdelen van een hub-en spoke-topologie:

![Architectuur van hub-en spoke-topologie in azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Voor delen van de hub-en spoke-topologie

Een hub-en-spoke-netwerk topologie is een manier om workloads te isoleren terwijl algemene services worden gedeeld. Deze services omvatten identiteit en beveiliging. De hub is een VNet dat fungeert als een centraal verbindings punt voor een on-premises netwerk. De punten zijn VNets die zijn gekoppeld aan de hub. Gedeelde services worden geïmplementeerd in de hub, terwijl afzonderlijke workloads binnen spoke-netwerken worden geïmplementeerd. Hier volgen enkele voor delen van de hub-en spoke-netwerk topologie:

- **Kosten besparingen** door de services op één locatie te centraliseren die kunnen worden gedeeld door meerdere werk belastingen. Tot deze werk belastingen behoren virtuele netwerk apparaten en DNS-servers.
- **Limieten in abonnementen overwinnen** door peering van VNets uit verschillende abonnementen naar de centrale hub.
- **Scheiding van problemen** tussen centrale IT (SecOps, InfraOps) en workloads (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typische toepassingen voor de hub-en spoke-architectuur

Enkele van de typische toepassingen voor een hub-en spoke-architectuur zijn:

- Veel klanten hebben werk belastingen die in verschillende omgevingen worden geïmplementeerd. Deze omgevingen omvatten ontwikkeling, testen en productie. Vaak moeten deze werk belastingen Services delen, zoals DNS, ID'S, NTP of AD DS. Deze gedeelde services kunnen in het hub-VNet worden geplaatst. Op die manier wordt elke omgeving geïmplementeerd naar een spoke om isolatie te hand haven.
- Werk belastingen waarvoor geen verbinding met elkaar is vereist, maar die toegang moet hebben tot gedeelde services.
- Ondernemingen die centraal toezicht op beveiligings aspecten vereisen.
- Ondernemingen waarvoor gescheiden beheer is vereist voor de werk belastingen in elke spoke.

## <a name="preview-the-demo-components"></a>De demo onderdelen bekijken

Tijdens het uitvoeren van elke zelf studie in deze reeks worden verschillende onderdelen gedefinieerd in afzonderlijke terraform-scripts. De demo architectuur die is gemaakt en geïmplementeerd, bestaat uit de volgende onderdelen:

- **On-premises netwerk**. Een particulier lokaal netwerk dat wordt uitgevoerd met een organisatie. Voor hub-en spoke-referentie architectuur wordt een VNet in azure gebruikt voor het simuleren van een on-premises netwerk.

- **VPN-apparaat**. Een VPN-apparaat of service biedt externe verbinding met het on-premises netwerk. Het VPN-apparaat kan een hardwareapparaat of een software oplossing zijn. 

- **Hub VNet**. De hub is het centrale punt van connectiviteit met uw on-premises netwerk en een locatie voor het hosten van services. Deze services kunnen worden gebruikt door de verschillende workloads die worden gehost in de spoke-VNets.

- **Gatewaysubnet**. De VNet-gateways worden in hetzelfde subnet bewaard.

- **Knooppunt VNets**. Knooppunten kunnen worden gebruikt om werkbelastingen te isoleren in hun eigen VNets, afzonderlijk beheerd vanaf andere knooppunten. Elke workload kan meerdere lagen bevatten, met meerdere subnetten die zijn verbonden via Azure-load balancers. 

- **VNet-peering**. Twee VNets kunnen worden verbonden met behulp van een peering-verbinding. Peeringverbindingen zijn niet-transitieve verbindingen met lage latentie tussen VNets. Wanneer het VNets Exchange-verkeer wordt gebruikt met de Azure-backbone, zonder dat hiervoor een router nodig is. In een hub-en-spoke-netwerk topologie wordt VNet-peering gebruikt om de hub op elke spoke te verbinden. U kunt VNets in dezelfde regio of in verschillende regio's.

## <a name="create-the-directory-structure"></a>De directorystructuur maken

Maak de map die uw terraform-configuratie bestanden bevat voor de demo.

1. Blader naar de [Azure-portal](https://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een directory met de naam `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Ga naar de nieuwe map:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>De Azure-provider declareren

Maak het Terraform-configuratiebestand waarin de Azure-provider wordt gedeclareerd.

1. Open in Cloud Shell een nieuw bestand met de `main.tf`naam.

    ```bash
    code main.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Sla het bestand op en sluit de editor af.

## <a name="create-the-variables-file"></a>Het variabelen bestand maken

Maak het terraform-configuratie bestand voor algemene variabelen die worden gebruikt in verschillende scripts.

1. Open in Cloud Shell een nieuw bestand met de `variables.tf`naam.

    ```bash
    code variables.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Sla het bestand op en sluit de editor af.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [On-premises virtueel netwerk maken met terraform in azure](./terraform-hub-spoke-on-prem.md)
