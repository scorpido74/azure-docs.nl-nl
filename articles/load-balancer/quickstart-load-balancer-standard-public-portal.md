---
title: 'Snelstartgids: een open bare Load Balancer maken-Azure Portal'
titleSuffix: Azure Load Balancer
description: In deze Quick start ziet u hoe u een Load Balancer maakt met behulp van de Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78898832"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snelstartgids: een Load Balancer maken om taken te verdelen over Vm's met behulp van de Azure Portal

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. U kunt in Azure Portal een load balancer maken om taken van virtuele machines (VM's) te verdelen. In deze Quick start ziet u hoe u virtuele machines kunt verdelen met behulp van een open bare Load Balancer.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Een load balancer maken

In deze sectie maakt u een Load Balancer die de taak verdeling van virtuele machines ondersteunt. U kunt een open bare Load Balancer of een interne Load Balancer maken. Wanneer u een open bare Load Balancer maakt, moet u ook een nieuw openbaar IP-adres maken dat is geconfigureerd als de front-end (standaard de naam *LoadBalancerFrontend* ) voor de Load Balancer.

1. Selecteer linksboven in het scherm de optie **een resource** > **maken netwerk** > **Load Balancer**.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **nieuwe maken** en typ *myResourceGroupSLB* in het tekstvak.|
    | Naam                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Select **Openbaar**.                                        |
    | SKU           | Selecteer **Standard** of **Basic**. Micro soft adviseert standaard voor productie werkbelastingen. |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. Als u een bestaand openbaar IP-adres hebt dat u wilt gebruiken, selecteert u **bestaande gebruiken** |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   Gebruiken ```-SKU Basic``` voor het maken van een open bare basis-IP. Algemene open bare Ip's zijn niet compatibel met **standaard** Load Balancer. Micro soft raadt aan om **standaard** te gebruiken voor werk belastingen voor de productie.|
    | Beschikbaarheidszone | Typ een *zone-redundante* voor het maken van een flexibele Load Balancer. Als u een zonegebonden Load Balancer wilt maken, selecteert u een specifieke zone van 1, 2 of 3 |

> [!IMPORTANT]
> In de rest van deze Snelstartgids wordt ervan uitgegaan dat **standaard** -SKU is gekozen tijdens het bovenstaande SKU-selectie proces.


3. Selecteer in het tabblad **controleren en maken** de optie **maken**.   

    ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer-resources maken

In deze sectie configureert u Load Balancer instellingen voor een back-end-adres groep, een status test en een Balancer-regel opgeven.

### <a name="create-a-backend-pool"></a>Een back-end-groep maken

Als u verkeer naar de Vm's wilt distribueren, bevat een back-endadresgroep de IP-adressen van de virtuele (Nic's) die zijn verbonden met de Load Balancer. Maak de back- *myBackendPool* voor het toevoegen van virtuele machines voor het Internet verkeer voor taak verdeling.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens **myLoadBalancer** in de lijst met resources.
2. Selecteer **Back-upgroepen**onder **instellingen**en selecteer vervolgens **toevoegen**.
3. Op de pagina **een back-endserver toevoegen** typt u *myBackendPool*, als naam voor uw back-end-pool en selecteert u vervolgens **toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

Als u wilt dat de Load Balancer de status van uw app bewaken, gebruikt u een status test. De status test voegt dynamische Vm's toe aan of verwijdert uit de Load Balancer draaiing op basis van hun reactie op status controles. Maak een statustest (*myHealthProbe*) om de status van de VM's te bewaken.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens **myLoadBalancer** in de lijst met resources.
2. Selecteer onder **instellingen**de optie **status controles**en selecteer vervolgens **toevoegen**.
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myHealthProbe*in. |
    | Protocol | Selecteer **http**. |
    | Poort | Voer *80*in.|
    | Interval | Geef *15* **op voor** het aantal seconden tussen de test pogingen. |
    | Drempel waarde voor onjuiste status | Selecteer **2** voor het aantal **foutieve drempel waarden** of opeenvolgende test fouten die moeten optreden voordat een VM wordt beschouwd als beschadigd.|
    | | |
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken
Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak een Load Balancer regel *myLoadBalancerRuleWeb* voor het Luis teren naar poort 80 in de front-end *FrontendLoadBalancer* en verzend netwerk verkeer met gelijke taak verdeling naar de back-end-adres groep *myBackEndPool* ook via poort 80. 

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens **myLoadBalancer** in de lijst met resources.
2. Klik onder **instellingen**op **taakverdelings regels**en selecteer vervolgens **toevoegen**.
3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    
    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myhttprule als*in. |
    | Protocol | selecteer **TCP**. |
    | Poort | Voer *80*in.|
    | Backend-poort | Voer *80*in. |
    | Back-endpool | Selecteer *myBackendPool*.|
    | Statustest | Selecteer *myHealthProbe*. |
4. Laat de overige standaard waarden ongewijzigd en selecteer **OK**.


## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u een virtueel netwerk, maakt u drie virtuele machines voor de back-end-pool van de Load Balancer en installeert u IIS op de virtuele machines om de Load Balancer te testen.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en para meters

In deze sectie moet u de volgende para meters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB |
| **\<de naam van het virtuele netwerk>** | myVNet          |
| **\<regio-naam>**          | Europa -west      |
| **\<IPv4-adres ruimte>**   | 10.1.0.0 \ 16          |
| **\<>van subnet naam**          | myBackendSubnet        |
| **\<>van het subnet-adres bereik** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuele machines maken
Open bare IP-Sku's en Load Balancer Sku's moeten overeenkomen. Gebruik voor Standard Load Balancer Vm's met standaard-IP-adressen in de back-end-pool. In deze sectie maakt u drie virtuele machines (*myVM1*, *myVM2* en *myVM3*) met een standaard openbaar IP-adres in drie verschillende zones (*zone 1*, *zone 2*en *zone 3*) die later worden toegevoegd aan de back-end-groep van de Load Balancer die eerder is gemaakt. Als u basis hebt geselecteerd, gebruikt u Vm's met basis-IP-adressen.

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource** > **maken Compute** > **Windows Server 2019 Data Center**. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnements** > **resource groep**: Selecteer **myResourceGroupSLB**.
   - **Details** > van de naam van de**virtuele machine**: Typ *myVM1*.
   - **Exemplaar Details** > **regio** > Selecteer **Europa-West**.
   - **Instance Details** > **Opties voor de beschik baarheid** van instantie Details > **beschikbaarheids zones**selecteren. 
   - **Instance Details** > **Beschik baarheid** van instantie Details zone > Selecteer **1**.
   - **Beheerders account**> Geef de **gebruikers naam**, het **wacht woord** en het **wacht woord** op.
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
1. Controleer op het tabblad **netwerken** of het volgende is geselecteerd:
   - **Virtueel netwerk**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **Open bare IP-** > Selecteer **nieuwe maken**en selecteer in het venster **openbaar IP-adres maken** voor **SKU**de optie **standaard**en selecteer **zone-redundante**in het **gebied beschikbaarheids zone**en selecteer vervolgens **OK**. Als u een basis Load Balancer hebt gemaakt, selecteert u basis. Micro soft raadt aan om standaard-SKU te gebruiken voor werk belastingen voor productie.
   - Als u een nieuwe netwerkbeveiligingsgroep (NSG) wilt maken, een soort firewall, selecteert u onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd**. 
       1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
       1. Typ *myNetworkSecurityGroup*en selecteer **OK**.
   - Voer de volgende stappen uit om de VM deel te laten uitmaken van de back-end-pool van de Load Balancer:
        - In **taak verdeling**, voor **het plaatsen van deze virtuele machine achter een bestaande oplossing voor taak verdeling?**, selecteert u **Ja**.
        - Selecteer in de **instellingen**voor taak verdeling voor **Opties voor taak verdeling** **Azure Load Balancer**.
        - Voor **Select a load balancer**, *myLoadBalancer*.
        - Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**.
2. Stel op het tabblad **beheer** , onder **bewaking**, **Diagnostische gegevens over opstarten** in op **uit**. 
1. Selecteer **controleren + maken**.   
1. Controleer de instellingen en selecteer vervolgens **Maken**.
1. Volg de stappen 2 tot en met 6 om twee extra Vm's te maken met de volgende waarden en alle andere instellingen op dezelfde manier als *myVM1*:

    | Instelling | VM 2| VM 3|
    | ------- | ----- |---|
    | Naam |  *myVM2* |*myVM3*|
    | Beschikbaarheidszone | 2 |3|
    |Openbare IP| **Standaard** SKU|**Standaard** SKU|
    | Zone voor open bare IP-Beschik baarheid| **Zone redundant** |**Zone redundant**|
    | Netwerkbeveiligingsgroep | De bestaande *myNetworkSecurity-groep* selecteren| De bestaande *myNetworkSecurity-groep* selecteren|

 ### <a name="create-nsg-rule"></a>Een NSG-regel maken

In deze sectie maakt u een regel voor de netwerk beveiligings groep om binnenkomende verbindingen via HTTP toe te staan.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens in de lijst met resources **myNetworkSecurityGroup** die zich in de resource groep **myResourceGroupSLB** bevindt.
2. Selecteer onder **Instellingen****Inkomende beveiligingsregels** en selecteer vervolgens **Toevoegen**.
3. Voer deze waarden in voor de inkomende beveiligingsregel met de naam *myHTTPRule* om een binnenkomende HTTP-verbinding via poort 80 toe te staan:
    - **Bron**: *servicetag*
    -  **Bron servicetag**: *Internet*
    - **Poort bereik van doel**: *80*
    - **Protocol**: *TCP*
    - **Actie**: *toestaan*
    - **Prioriteit**: *100*
    - **Naam**: *myhttprule als* 
    - **Beschrijving**: '*http toestaan* 
4. Selecteer **Toevoegen**.
5. Herhaal de stappen voor de inkomende RDP-regel, indien nodig, met de volgende verschillende waarden:
   - **Poortbereiken van doel**: typ *3389*.
   - **Prioriteit**: typ *200*. 
   - **Naam**: typ *MyRDPRule*. 
   - **Beschrijving**: typ *RDP toestaan*. 
 
### <a name="install-iis"></a>IIS installeren

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens in de lijst met resources **myVM1** die zich in de resource groep *myResourceGroupSLB* bevindt.
2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** om extern verbinding te maken met de VM.
5. Meld u aan bij de virtuele machine met de referenties die u hebt opgegeven tijdens het maken van deze virtuele machine. Hiermee wordt een sessie met extern bureaublad met virtuele machine *myVM1* gestart.
6. Navigeer op het bureau blad van de server naar **Windows-beheer Programma's**>**Windows Power shell**.
7. Voer in het venster PowerShell de volgende opdrachten uit om de IIS-server te installeren, het standaardbestand iisstart.htm te verwijderen en een nieuw bestand iisstart.htm toe te voegen dat de naam van de VM weergeeft:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Sluit de RDP-sessie met *myVM1*.
7. Herhaal stappen 1 tot en met 6 om IIS en het bijgewerkte bestand iisstart.htm te installeren op *myVM2* en *myVM3*.

## <a name="test-the-load-balancer"></a>De Load Balancer testen
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens **myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

   ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

U kunt de standaard pagina van de IIS-webserver van elke virtuele machine aanpassen en vervolgens uw webbrowser geforceerd vernieuwen vanaf de client computer om te zien hoe de Load Balancer verkeer distribueren over alle drie de virtuele machines.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, Load Balancer en alle gerelateerde resources. Als u dit wilt doen, selecteert u de resource groep (*myResourceGroupSLB*) die de Load Balancer bevat en selecteert u vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Standard Load Balancer, gekoppelde Vm's aan de app gemaakt, de regel voor het Load Balancer verkeer geconfigureerd, de status test en vervolgens de Load Balancer getest. Ga door naar [Azure Load Balancer zelf studies](tutorial-load-balancer-standard-public-zone-redundant-portal.md)voor meer informatie over Azure Load Balancer.

Meer informatie over [Load Balancer-en beschikbaarheids zones](load-balancer-standard-availability-zones.md).
