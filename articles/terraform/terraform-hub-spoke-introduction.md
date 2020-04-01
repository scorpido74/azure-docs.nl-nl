---
title: Zelfstudie - Een hub- en spoke hybride netwerktopologie maken in Azure met Terraform
description: Zelfstudie die illustreert hoe u een volledige hybride netwerkreferentiearchitectuur in Azure maakt met Terraform
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472176"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Zelfstudie: Een hub- en spoke hybrid network-topologie maken in Azure met Terraform

In deze zelfstudiereeks ziet u hoe u Terraform gebruiken om in Azure een [hub- en spoke-netwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)te implementeren. 

Een hub en spoke topologie is een manier om workloads te isoleren tijdens het delen van gemeenschappelijke services. Deze diensten omvatten identiteit en beveiliging. De hub is een virtueel netwerk (VNet) dat fungeert als een centraal verbindingspunt voor een on-premises netwerk. De punten zijn VNets die zijn gekoppeld aan de hub. Gedeelde services worden geïmplementeerd in de hub, terwijl afzonderlijke workloads worden geïmplementeerd in gesproken netwerken.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * HCL (HashiCorp Language) gebruiken om hub- en spoke hybride netwerkreferentiearchitectuurbronnen op te stellen
> * Terraform gebruiken om hubnetwerktoestelbronnen te maken
> * Terraform gebruiken om hubnetwerk in Azure te maken om als gemeenschappelijk punt voor alle resources op te treden
> * Terraform gebruiken om afzonderlijke workloads te maken zoals spoke VNets in Azure
> * Terraform gebruiken om gateways en verbindingen tot stand te brengen tussen on-premises en Azure-netwerken
> * Terraform gebruiken om VNet-peerings te maken naar gesproken netwerken

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: Als u nog geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

- **Terraform installeren en configureren: Terraform** [installeren en configureren](terraform-install-configure.md) voor het inrichten van VM's en andere infrastructuur in Azure

## <a name="hub-and-spoke-topology-architecture"></a>Hub en spaak topologie architectuur

In de hub en spoke topologie is de hub een VNet. Het VNet fungeert als centraal verbindingspunt voor uw on-premises netwerk. De spokes of spaken zijn VNets die via peering zijn verbonden met de hub, en die kunnen worden gebruikt om workloads te isoleren. Verkeer stroomt tussen het on-premises datacentrum en de hub via een ExpressRoute- of VPN-gateway-verbinding. De volgende afbeelding toont de componenten in een hub en spaaktopologie:

![Hub- en spoke-topologiearchitectuur in Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Voordelen van de hub en spaaktopologie

Een hub- en spoke-netwerktopologie is een manier om workloads te isoleren tijdens het delen van algemene services. Deze diensten omvatten identiteit en beveiliging. De hub is een VNet dat fungeert als een centraal verbindingspunt voor een on-premises netwerk. De punten zijn VNets die zijn gekoppeld aan de hub. Gedeelde services worden geïmplementeerd in de hub, terwijl afzonderlijke workloads worden geïmplementeerd in gesproken netwerken. Hier zijn enkele voordelen van de hub en spaak netwerk topologie:

- **Kostenbesparingen** door services te centraliseren op één locatie die door meerdere workloads kunnen worden gedeeld. Deze workloads omvatten virtuele netwerkapparaten en DNS-servers.
- **Limieten in abonnementen overwinnen** door peering van VNets uit verschillende abonnementen naar de centrale hub.
- **Scheiding van problemen** tussen centrale IT (SecOps, InfraOps) en workloads (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typische toepassingen voor de hub en spaakarchitectuur

Enkele van de typische toepassingen voor een hub en spaakarchitectuur zijn:

- Veel klanten hebben workloads die in verschillende omgevingen worden geïmplementeerd. Deze omgevingen omvatten ontwikkeling, testen en productie. Vaak moeten deze workloads services zoals DNS, IDS, NTP of AD DS delen. Deze gedeelde diensten kunnen worden geplaatst in de hub VNet. Op die manier wordt elke omgeving ingezet om te praten om isolatie te behouden.
- Workloads die geen verbinding met elkaar vereisen, maar toegang tot gedeelde services vereisen.
- Ondernemingen die centrale controle over beveiligingsaspecten nodig hebben.
- Ondernemingen die gescheiden beheer nodig hebben voor de workloads in elk gesprek.

## <a name="preview-the-demo-components"></a>Een voorbeeld van de democomponenten bekijken

Terwijl je elke tutorial in deze serie doorloopt, worden verschillende componenten gedefinieerd in verschillende Terraform-scripts. De demoarchitectuur die is gemaakt en geïmplementeerd, bestaat uit de volgende componenten:

- **On-premises netwerk**. Een privé netwerk in de omgeving dat wordt uitgevoerd met een organisatie. Voor hub- en spoke-referentiearchitectuur wordt een VNet in Azure gebruikt om een on-premises netwerk te simuleren.

- **VPN-apparaat**. Een VPN-apparaat of -service biedt externe connectiviteit met het on-premises netwerk. Het VPN-apparaat kan een hardwaretoestel of een softwareoplossing zijn. 

- **Hub VNet**. De hub is het centrale punt van connectiviteit met uw on-premises netwerk en een plek om diensten te hosten. Deze services kunnen worden verbruikt door de verschillende workloads die worden gehost in de spaakVNets.

- **Gateway subnet**. De VNet-gateways worden in hetzelfde subnet gehouden.

- **Knooppunt VNets**. Knooppunten kunnen worden gebruikt om werkbelastingen te isoleren in hun eigen VNets, afzonderlijk beheerd vanaf andere knooppunten. Elke workload kan meerdere lagen bevatten, met meerdere subnetten die zijn verbonden via Azure-load balancers. 

- **VNet-peering**. Twee VNets kunnen worden verbonden via een peering-verbinding. Peeringverbindingen zijn niet-transitieve verbindingen met lage latentie tussen VNets. Eenmaal peered wisselen de VNets verkeer uit met behulp van de Azure-backbone, zonder dat er een router nodig is. In een hub en spoke network topology wordt VNet peering gebruikt om de hub aan te sluiten op elke spaak. U vnets in dezelfde regio of verschillende regio's peeren.

## <a name="create-the-directory-structure"></a>De mapstructuur maken

Maak de map met uw Terraform-configuratiebestanden voor de demo.

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Azure [Cloud Shell openen](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een map met de naam `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Maak de nieuwe directory de actieve directory:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>De Azure-provider declareren

Maak het Terraform-configuratiebestand waarin de Azure-provider wordt gedeclareerd.

1. Open in Cloud Shell een `main.tf`nieuw bestand met de naam .

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

## <a name="create-the-variables-file"></a>Het variabelenbestand maken

Maak het terraform-configuratiebestand voor veelvoorkomende variabelen die worden gebruikt in verschillende scripts.

1. Open in Cloud Shell een `variables.tf`nieuw bestand met de naam .

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
> [On-premises virtueel netwerk maken met Terraform in Azure](./terraform-hub-spoke-on-prem.md)