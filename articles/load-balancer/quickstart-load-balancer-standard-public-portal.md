---
title: 'Quickstart: Een openbare load balancer maken - Azure-portal'
titleSuffix: Azure Load Balancer
description: In deze quickstart vindt u informatie over het maken van een load balancer via de Azure-portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: eb23f1e703c2e447c484ccb366914cb4b23c5bf7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536540"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Quickstart: Een load balancer maken om taken van VM's te verdelen via de Azure-portal

Ga aan de slag met Azure Load Balancer via de Azure-portal om een openbare load balancer en drie virtuele machines te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[Optie 1 (standaard): Een load balancer maken (Standard SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen.  Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. 

U kunt een openbare load balancer of een interne load balancer maken. 

Wanneer u een openbare load balancer maakt, maakt u een nieuw openbaar IP-adres dat als de front-end (standaard **LoadBalancerFrontend** genoemd) voor de load balancer wordt geconfigureerd.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en voer **myResourceGroupLB** in het tekstvak in.|
    | Naam                   | Voer **myLoadBalancer** in                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standard** |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Als u een bestaand openbaar IP-adres hebt dat u wilt gebruiken, selecteert u **Bestaande gebruiken**. |
    | Naam openbaar IP-adres | Typ **myPublicIP** in het tekstvak.|
    | Beschikbaarheidszone | Selecteer **Zone-redundant** om een tolerante load balancer te maken. Als u een zonegebonden load balancer wilt maken, selecteert u een specifieke zone uit 1, 2 of 3 |
    | Een openbaar IPv6-adres toevoegen | Selecteer **Nee**. </br> Zie [Wat is IPv6 voor Azure Virtual Network?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview) voor meer informatie over IPv6-adressen en load balancer.  |

3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Een standaardversie van een load balancer maken" border="true":::
 
## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u het volgende:

* Load balancer-instellingen voor een back-endadresgroep.
* Een statustest.
* Een load balancer-regel en een automatische uitgaande regel.

### <a name="create-a-backend-pool"></a>Een back-endpool maken

De back-endadresgroep bevat de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. 

Maak de back-endadres groep **myBackendPool** om virtuele machines voor het verdelen van internetverkeer in te bewaren.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Typ op de pagina **Back-endpool toevoegen** **myBackendPool** als naam voor de back-endpool. Selecteer vervolgens **Toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

De load balancer controleert de status van uw app met een statustest. 

De statustest voegt VM's toe aan of verwijdert ze van de load balancer op basis van hun reactie op statuscontroles. 

Maak een statustest genaamd **myHealthProbe** om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHealthProbe** in. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer **80** in.|
    | Interval | Voer **15** in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer **2** als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|
    | | |

3. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen. De bron- en doelpoort worden in de regel gedefinieerd. 

In deze sectie maakt u een load balancer-regel:

* Naam: **myHTTPRule**.
* Naam in de front-end: **LoadBalancerFrontEnd**.
* Luisteren op **poort 80**.
* Hiermee wordt verkeer met gelijke taakverdeling naar de back-end met de naam **myBackendPool** op **poort 80** geleid.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.

3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHTTPRule** in. |
    | IP-versie | Selecteer **IPv4** |
    | IP-adres voor front-end | Selecteer **LoadBalancerFrontEnd** |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80** in.|
    | Back-endpoort | Voer **80** in. |
    | Back-end-pool | Selecteer **myBackendPool**.|
    | Statustest | Selecteer **myHealthProbe**. |
    | Impliciete uitgaande regels maken | Selecteer **Ja**. </br> Zie voor meer informatie en geavanceerde configuratie van uitgaande regels: </br> [Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md) </br> [Taakverdeling en uitgaande regels in Standard Load Balancer configureren via de Azure-portal](configure-load-balancer-outbound-portal.md)

4. Laat de overige standaardwaarden staan en selecteer **OK**.

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie doet u het volgende:

* Maak een virtueel netwerk.
* Maak drie virtuele machines voor de back-endpool van de load balancer.
* Installeer IIS op de virtuele machines om de load balancer te testen.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie gaat u de parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa -west      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuele machines maken

SKU's voor openbare IP-adressen en SKU's voor load balancers moeten overeenkomen. Gebruik voor standaard load balancer VM's met standaard IP-adressen in de back-endpool. 

In deze sectie maakt u drie VM's (**myVM1**, **myVM2** en **myVM3**) met een standaard openbaar IP-adres in drie verschillende zones (**Zone 1**, **Zone 2** en **Zone 3**). 

Deze VM's worden toegevoegd aan de back-endpool van de load balancer die eerder is gemaakt.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroupLB** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myVM1** in |
    | Regio | Selecteer **Europa - west** |
    | Beschikbaarheidsopties | Selecteer **Beschikbaarheidszones** |
    | Beschikbaarheidszone | Selecteer **1** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Openbare IP | Accepteer de standaardwaarde **myVM-ip**. </br> Het IP-adres wordt automatisch een IP-adres met standaard-SKU in Zone 1. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**|
    | Netwerkbeveiligingsgroep configureren | Selecteer **Nieuw maken**. </br> Voer in **Netwerkbeveiligingsgroep maken** bij **Naam** **myNSG** in. </br> Selecteer onder **Inkomende regels** de optie **+ Een inkomende regel toevoegen**. </br> Voer bij **Poortbereiken van doel** **80** in. </br> Voer bij **Prioriteit** **100** in. </br> Voer bij **Naam** **myHTTPRule** in. </br> Selecteer **Toevoegen** </br> Selecteer **OK** |
    | **Taakverdeling**  |
    | Wilt u deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen? | Selecteer **Ja** |
    | **Instellingen voor taakverdeling** |
    | Opties voor taakverdeling | Selecteer **Azure-taakverdeling** |
    | Een load balancer selecteren | Selecteer **myLoadBalancer**  |
    | Een back-endpool selecteren | Selecteer **myBackendPool** |

5. Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**.

6. Op het tabblad **Beheer** voert u het volgende in of selecteert u het volgende:
    
    | Instelling | Waarde |
    |-|-|
    | **Controle** |  |
    | Diagnostische gegevens over opstarten | Selecteer **Uit** |
   
7. Selecteer **Controleren + maken**. 
  
8. Controleer de instellingen en selecteer vervolgens **Maken**.

9. Volg de stappen 1 tot en met 8 om twee extra VM's te maken met de volgende waarden en alle andere instellingen hetzelfde als **myVM1**:

    | Instelling | VM 2| VM 3|
    | ------- | ----- |---|
    | Naam |  **myVM2** |**myVM3**|
    | Beschikbaarheidszone | **2** |**3**|
    | Netwerkbeveiligingsgroep | Het bestaande **myNSG** selecteren| Het bestaande **myNSG** selecteren|


# <a name="option-2-create-a-load-balancer-basic-sku"></a>[Optie 2: Een load balancer (Basic SKU) maken](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen.  Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. 

U kunt een openbare load balancer of een interne load balancer maken. 

Wanneer u een openbare load balancer maakt, maakt u een nieuw openbaar IP-adres dat als de front-end (standaard **LoadBalancerFrontend** genoemd) voor de load balancer wordt geconfigureerd.

1. Selecteer linksboven in het scherm de optie **Een resource maken** > **Netwerken** > **Load balancer**.

2. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**: 

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en typ **myResourceGroupLB** in het tekstvak.|
    | Naam                   | Voer **myLoadBalancer** in                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | Selecteer **Basic** |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Als u een bestaand openbaar IP-adres hebt dat u wilt gebruiken, selecteert u **Bestaande gebruiken**. |
    | Naam openbaar IP-adres | Typ **myPublicIP** in het tekstvak.|
    | Toewijzing | **Dynamisch** selecteren |
    | Een openbaar IPv6-adres toevoegen | Selecteer **Nee**. </br> Zie [Wat is IPv6 voor Azure Virtual Network?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview) voor meer informatie over IPv6-adressen en load balancer.  |

3. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Beoordelen en maken**.

4. Selecteer in het deelvenster **Beoordelen en maken** de optie **Maken**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Een Basic load balancer maken" border="true":::

## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u het volgende:

* Maak een virtueel netwerk.
* Load balancer-instellingen voor een back-endadresgroep.
* Een statustest.
* Een load balancer-regel.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie gaat u de parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa -west      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-backend-pool"></a>Een back-endpool maken

De back-endadresgroep bevat de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. 

Maak de back-endadres groep **myBackendPool** om virtuele machines voor het verdelen van internetverkeer in te bewaren.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Back-endpools** en selecteer vervolgens **Toevoegen**.

3. Op de pagina **Een back-endpool toevoegen** voert u het volgende in of selecteert u het volgende:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myBackendPool** in. |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Gekoppeld aan | **Virtuele machines** selecteren |

### <a name="create-a-health-probe"></a>Een statustest maken

De load balancer controleert de status van uw app met een statustest. 

De statustest voegt VM's toe aan of verwijdert ze van de load balancer op basis van hun reactie op statuscontroles. 

Maak een statustest genaamd **myHealthProbe** om de status van de VM's te bewaken.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHealthProbe** in. |
    | Protocol | Selecteer **HTTP**. |
    | Poort | Voer **80** in.|
    | Pad | **/** invoeren |
    | Interval | Voer **15** in als waarde voor het **Interval** in seconden tussen tests. |
    | Drempelwaarde voor beschadigde status | Selecteer **2** als het aantal voor de **Drempelwaarde voor beschadigde status** of het aantal opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een beschadigde status heeft.|

3. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen. De bron- en doelpoort worden in de regel gedefinieerd. 

In deze sectie maakt u een load balancer-regel:

* Naam: **myHTTPRule**.
* Naam in de front-end: **LoadBalancerFrontEnd**.
* Luisteren op **poort 80**.
* Hiermee wordt verkeer met gelijke taakverdeling naar de back-end met de naam **myBackendPool** op **poort 80** geleid.

1. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer** en in de lijst met resources.

2. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.

3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **myHTTPRule** in. |
    | IP-versie | Selecteer **IPv4** |
    | IP-adres voor front-end | Selecteer **LoadBalancerFrontEnd** |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer **80** in.|
    | Back-endpoort | Voer **80** in. |
    | Back-end-pool | Selecteer **myBackendPool**.|
    | Statustest | Selecteer **myHealthProbe**. |
 
4. Laat de overige standaardwaarden staan en selecteer **OK**.

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie doet u het volgende:

* Maak drie virtuele machines voor de back-endpool van de load balancer.
* Maak een beschikbaarheidsset voor de virtuele machines.
* Installeer IIS op de virtuele machines om de load balancer te testen.

### <a name="create-virtual-machines"></a>Virtuele machines maken

SKU's voor openbare IP-adressen en SKU's voor load balancers moeten overeenkomen. Gebruik voor Basic load balancer VM's met Basic IP-adressen in de back-endpool. 

In deze sectie maakt u drie VM's(**myVM1**, **myVM2** en **myVM3**) met een basic openbaar IP-adres.  

De drie VM's worden toegevoegd aan een beschikbaarheidsset met de naam **myAvailabilitySet**.

Deze VM's worden toegevoegd aan de back-endpool van de load balancer die eerder is gemaakt.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroupLB** |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myVM1** in |
    | Regio | Selecteer **Europa - west** |
    | Beschikbaarheidsopties | **Beschikbaarheidstest** selecteren |
    | Beschikbaarheidsset | Selecteer **Nieuw maken**. </br> Voer **myAvailabilitySet** in bij **Naam**. </br> Selecteer **OK** |
    | Installatiekopie | **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | **myVNet** selecteren |
    | Subnet | **myBackendSubnet** selecteren |
    | Openbare IP | **Nieuw maken** selecteren </br> Voer bij de naam **myVM-ip** in. </br> Selecteer **OK** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geavanceerd**|
    | Netwerkbeveiligingsgroep configureren | Selecteer **Nieuw maken**. </br> Voer in **Netwerkbeveiligingsgroep maken** bij **Naam** **myNSG** in. </br> Selecteer onder **Inkomende regels** de optie **+ Een inkomende regel toevoegen**. </br> Voer bij **Poortbereiken van doel** **80** in. </br> Voer bij **Prioriteit** **100** in. </br> Voer bij **Naam** **myHTTPRule** in. </br> Selecteer **Toevoegen** </br> Selecteer **OK** |
    | **Taakverdeling**  |
    | Wilt u deze virtuele machine achter een bestaande taakverdelingsoplossing plaatsen? | Selecteer **Nee** |
 
5. Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**.

6. Op het tabblad **Beheer** voert u het volgende in of selecteert u het volgende:
    | Instelling | Waarde |
    |-|-|
    | **Controle** | |
    | Diagnostische gegevens over opstarten | Selecteer **Uit** |

7. Selecteer **Controleren + maken**. 
  
8. Controleer de instellingen en selecteer vervolgens **Maken**.

9. Volg de stappen 1 tot en met 8 om twee extra VM's te maken met de volgende waarden en alle andere instellingen hetzelfde als **myVM1**:

    | Instelling | VM 2| VM 3|
    | ------- | ----- |---|
    | Naam |  **myVM2** |**myVM3**|
    | Beschikbaarheidsset| **myAvailabilitySet** selecteren | **myAvailabilitySet** selecteren|
    | Netwerkbeveiligingsgroep | Het bestaande **myNSG** selecteren| Het bestaande **myNSG** selecteren|
---

### <a name="install-iis"></a>IIS installeren

1. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myVM1**, die zich in de resourcegroep **myResourceGroupLB** bevindt.

2. Op de **overzichtspagina** selecteert u **Verbinding maken** om het RDP-bestand voor de VM te downloaden.

3. Open het RDP-bestand.

4. Meld u aan bij de virtuele machine met de referenties die u hebt opgegeven tijdens het maken van deze virtuele machine.

5. Ga op de serverdesktop naar **Windows Systeembeheer**>**Windows Powershell**.

6. In het venster PowerShell voert u de volgende opdrachten uit om het volgende te doen:

    * De IIS-server installeren
    * Het standaard iisstart.htm-bestand verwijderen
    * Een nieuw iisstart.htm-bestand toevoegen waarin de naam van de VM wordt weergegeven:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
7. Sluit de RDP-sessie met **myVM1**.

8. Herhaal stappen 1 tot en met 6 om IIS en het bijgewerkte bestand iisstart.htm te installeren op **myVM2** en **myVM3**.

## <a name="test-the-load-balancer"></a>Load balancer testen

1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer op. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

   ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

U kunt de standaardpagina van de IIS-webserver van elke virtuele machine aanpassen en vervolgens uw webbrowser geforceerd vernieuwen vanaf de clientcomputer om te zien hoe de load balancer verkeer verdeelt over alle drie de virtuele machines.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Als u dit wilt doen, selecteert u de resourcegroep **myResourceGroupLB** die de resources bevat en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart, gaat u het volgende doen:

* U hebt een Standard of Basic load balancer in Azure gemaakt
* U hebt drie VM's aan de load balancer gekoppeld.
* U hebt de verkeersregel en statustest van de load balancer geconfigureerd en vervolgens de load balancer getest. 

Zie [Wat is Azure Load Balancer?](load-balancer-overview.md) en de [veelgestelde vragen over Load Balancer](load-balancer-faqs.md) voor meer informatie over Azure Load Balancer.

Meer informatie over [Load Balancer en beschikbaarheidszones](load-balancer-standard-availability-zones.md).
