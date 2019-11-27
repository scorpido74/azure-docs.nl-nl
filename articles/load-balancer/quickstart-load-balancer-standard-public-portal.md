---
title: 'Quickstart: Een standaard load balancer maken - Azure Portal'
titleSuffix: Azure Load Balancer
description: In deze Quick start ziet u hoe u een Standard Load Balancer maakt met behulp van de Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: d15223dfe6d9ce710f2a3d402a49203ef169132e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225194"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snelstart: Een standaard load balancer maken om taken van VM's te verdelen via Azure Portal

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. U kunt in Azure Portal een load balancer maken om taken van virtuele machines (VM's) te verdelen. In deze snelstart wordt getoond hoe u taken van VM's kunt verdelen met een Standard Load Balancer.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Een Load Balancer van het type Standard maken

In deze sectie maakt u een Standard Load Balancer die de taak verdeling van virtuele machines ondersteunt. Standard Load Balancer biedt alleen ondersteuning voor een standaard, openbaar IP-adres. Als u een Standard Load Balancer maakt, moet u ook een nieuw, standaard, openbaar IP-adres maken dat als de front-end (standaard *LoadBalancerFrontend* genoemd) wordt geconfigureerd voor de Standard Load Balancer. 

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken** > **netwerk** > **Load Balancer**.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **nieuwe maken** en typ *myResourceGroupSLB* in het tekstvak.|
    | Naam                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standaard**.                          |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbare IP-adres              | Typ *myPublicIP* in het tekstvak.   |
    |Beschikbaarheidszone| Selecteer **Zone-redundant**.    |
3. Selecteer in het tabblad **controleren en maken** de optie **maken**.   

    ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer-resources maken

In deze sectie configureert u Load Balancer instellingen voor een back-end-adres groep, een status test en een Balancer-regel opgeven.

### <a name="create-a-backend-address-pool"></a>Een back-endadresgroep maken

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
    | Back-end-groep | Selecteer *myBackendPool*.|
    | Statustest | Selecteer *myHealthProbe*. |
4. Laat de overige standaard waarden ongewijzigd en selecteer **OK**.


## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u een virtueel netwerk, maakt u drie virtuele machines voor de back-end-pool van de Load Balancer en installeert u IIS op de virtuele machines om de Load Balancer te testen.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myVNet* in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer bestaande resource- *myResourceGroupSLB*. |
    | Locatie | Selecteer **Europa - west**.|
    | Subnet - naam | Voer *myBackendSubnet* in. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
1. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-virtual-machines"></a>Virtuele machines maken
Standard Load Balancer ondersteunt alleen Vm's met standaard-IP-adressen in de back-end-pool. In deze sectie maakt u drie virtuele machines (*myVM1*, *myVM2* en *myVM3*) met een standaard openbaar IP-adres in drie verschillende zones (*zone 1*, *zone 2*en *zone 3*) die later worden toegevoegd aan de back-end-groep van de Standard Load Balancer die eerder is gemaakt.

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **reken** > **Windows Server 2019 Data Center**. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupSLB**.
   - **Exemplaar Details** > **naam van de virtuele machine**: Typ *myVM1*.
   - **Exemplaar Details** > **regio** > **Europa-West**selecteren.
   - **Details van instantie** > **beschikbaarheids opties** > Selecteer **beschikbaarheids zones**. 
   - **Exemplaar Details** > **beschikbaarheids zone** > Selecteer **1**.
   - **Beheerders account**> Geef de **gebruikers naam**, het **wacht woord** en het **wacht woord** op.
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
1. Controleer op het tabblad **netwerken** of het volgende is geselecteerd:
   - **Virtueel netwerk**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **Open bare IP-** > Selecteer **nieuwe maken**en selecteer in het venster **openbaar IP-adres maken** voor **SKU**de optie **standaard**en selecteer **zone-redundante**in het **gebied beschikbaarheids zone**en selecteer vervolgens **OK**.
   - Als u een nieuwe netwerkbeveiligingsgroep (NSG) wilt maken, een soort firewall, selecteert u onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd**. 
       1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
       1. Typ *myNetworkSecurityGroup*en selecteer **OK**.
   - Voer de volgende stappen uit om de VM deel te laten uitmaken van de back-end-pool van de Load Balancer:
        - In **taak verdeling**, voor **het plaatsen van deze virtuele machine achter een bestaande oplossing voor taak verdeling?** , selecteert u **Ja**.
        - Selecteer in de **instellingen**voor taak verdeling voor **Opties voor taak verdeling** **Azure Load Balancer**.
        - Voor **Select a load balancer**, *myLoadBalancer*.
        - Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**.
2. Stel op het tabblad **beheer** , onder **bewaking**, **Diagnostische gegevens over opstarten** in op **uit**. 
1. Selecteer **Controleren + maken**.   
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
2. Selecteer onder **Instellingen** **Inkomende beveiligingsregels** en selecteer vervolgens **Toevoegen**.
3. Voer deze waarden in voor de inkomende beveiligingsregel met de naam *myHTTPRule* om een binnenkomende HTTP-verbinding via poort 80 toe te staan:
    - *Service Tag* bij **Bron**.
    - *Internet* bij **Bronservicetag**
    - *80* bij **Poortbereiken van doel**
    - *TCP* bij **Protocol**
    - *Allow* bij **Actie**
    - *100* bij **Prioriteit**
    - *myHTTPRule* als naam
    - *Allow HTTP* als beschrijving
4. Selecteer **Toevoegen**.
 
### <a name="install-iis"></a>IIS installeren

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens in de lijst met resources **myVM1** die zich in de resource groep *myResourceGroupSLB* bevindt.
2. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** om extern verbinding te maken met de VM.
5. Meld u aan bij de virtuele machine met de referenties die u hebt opgegeven tijdens het maken van deze virtuele machine. Hiermee wordt een sessie met extern bureaublad met virtuele machine *myVM1* gestart.
6. Ga op de serverdesktop naar **Windows Systeembeheer**>**Windows Powershell**.
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

In deze Quick Start hebt u een Standard Load Balancer, gekoppelde Vm's aan de app gemaakt, de regel voor het Load Balancer verkeer geconfigureerd, de status test en vervolgens de Load Balancer getest. Voor meer informatie over Azure Load Balancer gaat u verder met de zelfstudies voor Azure Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
