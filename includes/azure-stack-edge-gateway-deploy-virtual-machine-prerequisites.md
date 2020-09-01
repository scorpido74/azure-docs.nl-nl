---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7b98d3c1febd68a7ee73cf3064f4d8e108ea81fa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083845"
---
Voordat u Vm's op uw Azure Stack edge-apparaat kunt implementeren, moet u uw client configureren om verbinding te maken met het apparaat via Azure Resource Manager over Azure PowerShell. Ga voor gedetailleerde stappen naar [verbinding maken met Azure Resource Manager op uw Azure stack edge-apparaat](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Zorg ervoor dat de volgende stappen kunnen worden gebruikt om toegang te krijgen tot het apparaat vanaf uw client (u hebt deze configuratie uitgevoerd wanneer u verbinding maakt met Azure Resource Manager, controleert u gewoon of de configuratie is geslaagd.): 

1. Controleer of Azure Resource Manager communicatie werkt. Type:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>:30005/
    ```

1. De Api's van het lokale apparaat aanroepen om te verifiëren. Type: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Geef de gebruikers naam- *EdgeARMuser* en het wacht woord op om verbinding te maken via Azure Resource Manager.

1. Als u **Compute** voor Kubernetes hebt geconfigureerd, kunt u deze stap overs Laan. Ga door om te controleren of u een netwerk interface voor Compute hebt ingeschakeld. Ga in lokale gebruikers interface naar **computer** instellingen. Selecteer de netwerk interface die u gaat gebruiken om een virtuele switch te maken. De Vm's die u maakt, worden gekoppeld aan een virtuele switch die is gekoppeld aan deze poort en het bijbehorende netwerk. Zorg ervoor dat u een netwerk kiest dat overeenkomt met het statische IP-adres dat u voor de virtuele machine gaat gebruiken.  

    ![Reken instellingen inschakelen 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Schakel Compute in op de netwerk interface. Azure Stack Edge maakt en beheert een virtuele switch die overeenkomt met die netwerk interface. Voer op dit moment geen specifieke IP-adressen in voor Kubernetes. Het kan enkele minuten duren om de berekening in te scha kelen.

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

