---
title: 'Zelfstudie: Taakverdeling voor internetverkeer naar virtuele machines instellen - Azure Portal'
titlesuffix: Azure Load Balancer
description: In deze zelfstudie vindt u informatie over het maken en beheren van een Standard Load Balancer via Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: a0887a66b6cb886419f86d93973b991151141e0a
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327208"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Zelfstudie: Taakverdeling voor internetverkeer naar virtuele machines instellen met behulp van Azure Portal

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. In deze zelfstudie leert u meer over de verschillende onderdelen van Azure Standard Load Balancer die internetverkeer verdelen naar VM's en zorgen voor hoge beschikbaarheid. In deze zelfstudie leert u procedures om het volgende te doen:


> [!div class="checklist"]
> * Een Azure Load Balancer maken
> * Load Balancer-resources maken
> * Virtuele machines maken en IIS-server installeren
> * Load Balancer in actie weer geven
> * Vm's toevoegen aan en verwijderen uit een Load Balancer

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Een Load Balancer van het type Standard maken

In deze sectie maakt u een Standard Load Balancer die de taak verdeling van virtuele machines ondersteunt. Standard Load Balancer biedt alleen ondersteuning voor een standaard, openbaar IP-adres. Wanneer u een Standard Load Balancer maakt, moet u ook een nieuw, standaard, openbaar IP-adres maken dat als de front-end (standaard *LoadBalancerFrontend* genoemd) wordt geconfigureerd voor de Standard Load Balancer. 

1. Klik linksboven in het scherm op **Een resource maken** > **Netwerken** > **Load balancer**.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecteer uw abonnement.    |    
    | Resource group         | Selecteer **nieuwe maken** en typ *myResourceGroupSLB* in het tekstvak.|
    | Name                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standaard**.                          |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   |
    |Beschikbaarheidszone| Selecteer **Zone-redundant**.    |

3. Klik op het tabblad **Beoordelen en maken** op **Maken**.

   ![Een Load Balancer van het type Standard maken](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer-resources maken

In deze sectie configureert u Load Balancer instellingen voor een back-end-adres groep, een status test en een Balancer-regel opgeven.

### <a name="create-a-backend-address-pool"></a>Een back-endadresgroep maken

Als u verkeer naar de Vm's wilt distribueren, bevat een back-endadresgroep de IP-adressen van de virtuele (Nic's) die zijn verbonden met de Load Balancer. Maak de back- *myBackendPool* voor het toevoegen van virtuele machines voor het Internet verkeer voor taak verdeling.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en klik vervolgens op **myLoadBalancer** in de lijst met resources.
2. Klik onder **Instellingen** op **Back-endpools** en vervolgens op **Toevoegen**.
3. Op de pagina **een back-endserver toevoegen** typt u *myBackendPool*, als naam voor uw back-end-pool en selecteert u vervolgens **toevoegen**.

### <a name="create-a-health-probe"></a>Een statustest maken

Als u wilt dat de Load Balancer de status van uw app bewaken, gebruikt u een status test. De status test voegt dynamische Vm's toe aan of verwijdert uit de Load Balancer draaiing op basis van hun reactie op status controles. Maak een statustest (*myHealthProbe*) om de status van de VM's te bewaken.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en klik vervolgens op **myLoadBalancer** in de lijst met resources.
2. Klik onder **Instellingen** op **Tests** en klik op **Toevoegen**.
3. Gebruik deze waarden om de statustest te maken:
     
    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Enter *myHealthProbe*. |
    | Protocol | Selecteer **http**. |
    | Port | Voer *80*in.|
    | Interval | Geef *15* op voor het aantal seconden tussen de test pogingen. |
    | Drempelwaarde voor onjuiste status | Selecteer *2* voor het aantal **foutieve drempel waarden** of opeenvolgende test fouten die moeten optreden voordat een VM wordt beschouwd als beschadigd.|
    
4. Selecteer **OK**.

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak een Load Balancer regel *myLoadBalancerRuleWeb* voor het Luis teren naar poort 80 in de front-end *FrontendLoadBalancer* en verzend netwerk verkeer met gelijke taak verdeling naar de back-end-adres groep *myBackEndPool* ook via poort 80.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en klik vervolgens op **myLoadBalancer** in de lijst met resources.
2. Klik onder **Instellingen** op **Taakverdelingsregels** en vervolgens op **Toevoegen**.
3. Gebruik deze waarden om de taakverdelings regel te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *myhttprule als*in. |
    | Protocol | selecteer **TCP**. |
    | Port | Voer *80*in.|
    | Back-endpoort | Voer *80*in. |
    | Back-endpool | Selecteer *myBackendPool*.|
    | Test | Selecteer *myHealthProbe*. |
    
4. Laat de overige standaardwaarden staan en selecteer **OK**.

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u een virtueel netwerk, maakt u drie virtuele machines voor de back-end-pool van de Load Balancer en installeert u IIS op de virtuele machines om de Load Balancer te testen.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *myVNet* in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Subscription | Selecteer uw abonnement.|
    | Resource group | Selecteer bestaande resource- *myResourceGroupSLB*. |
    | Location | Selecteer **Europa - west**.|
    | Subnet - Naam | Voer *myBackendSubnet* in. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |
    
3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-virtual-machines"></a>Virtuele machines maken

Standard Load Balancer ondersteunt alleen Vm's met standaard-IP-adressen in de back-end-pool. In deze sectie maakt u drie Vm's (*myVM1*, *myVM2*en *myVM3*) met een standaard openbaar IP-adres in drie verschillende zones (*zone 1*, *zone 2*en *zone 3*) die worden toegevoegd aan de back-end-groep van de standaard Load Balancer dat eerder is gemaakt.

1. Selecteer **Een resource maken** > **Compute** > **Windows Server 2016 Datacenter** linksboven in de portal. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: Selecteer **myResourceGroupSLB**.
   - **Instantiedetails** > **Naam van virtuele machine**: Typ *myVM1*.
   - **Exemplaar Details** > **regio** > Selecteer **Europa-West**.
   -  > Opties voor de**Beschik baarheid** van instantie Details > **beschikbaarheids zones**selecteren. 
   -  > **Beschik baarheid** van instantie Details zone > Selecteer **1**.
  
1. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**. 
   
   - Zorg ervoor dat de volgende opties zijn geselecteerd:
       - **Virtueel netwerk**: **myVnet**
       - **Subnet**: **myBackendSubnet**
       - **Open bare IP-** > Selecteer **nieuwe maken**en selecteer in het venster **openbaar IP-adres maken** voor **SKU**de optie **standaard**, en voor de **beschikbaarheids zone**de optie **zone-redundant**
      
   - Als u een nieuwe netwerkbeveiligingsgroep (NSG) wilt maken, een soort firewall, selecteert u onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd**. 
       1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
       1. Typ *myNetworkSecurityGroup*en selecteer **OK**.

   - Voer de volgende stappen uit om de VM deel te laten uitmaken van de back-end-pool van de Load Balancer:
        - In **taak verdeling**, voor **het plaatsen van deze virtuele machine achter een bestaande oplossing voor taak verdeling?** , selecteert u **Ja**.
        - Selecteer in de **instellingen**voor taak verdeling voor **Opties voor taak verdeling** **Azure Load Balancer**.
        - Voor **Select a load balancer**, *myLoadBalancer*. 
1. Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**. 
1. Selecteer **Controleren + maken**.   
1. Controleer de instellingen en selecteer vervolgens **Maken**.
1. Volg de stappen voor het maken van twee extra virtuele machines- *myVM2* en *myVM3*, met een openbaar IP-adres voor de standaard-SKU in de **beschikbaarheids zone** **2** en **3** en alle andere instellingen hetzelfde als *myVM1*.  

### <a name="create-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

In deze sectie maakt u een regel voor de netwerk beveiligings groep om binnenkomende verbindingen via HTTP toe te staan.

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en klik vervolgens in de lijst met resources op **myNetworkSecurityGroup** die zich in de resource groep **myResourceGroupSLB** bevindt.
2. Klik onder **Instellingen** op **Inkomende beveiligingsregels** en vervolgens op **Toevoegen**.
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

### <a name="install-iis-on-vms"></a>IIS installeren op VM's

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en klik vervolgens in de lijst met resources op **myVM1** die zich in de resource groep *myResourceGroupSLB* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** om extern verbinding te maken met de VM.
3. Selecteer in het pop-upvenster **Verbinding maken met virtuele machine** **RDP-bestand downloaden**, en open vervolgens het gedownloade RDP-bestand.
4. In het venster **Verbinding met extern bureaublad** klikt u op **Verbinden**.
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
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en klik vervolgens op **myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

      ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Als u de Load Balancer het distribueren van verkeer over de drie Vm's die uw app uitvoeren, wilt zien, kunt u uw webbrowser geforceerd vernieuwen.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Virtuele machines toevoegen aan of verwijderen uit de back-endpool
Het is mogelijk dat u onderhoud moet uitvoeren op de VM's waarop uw app wordt uitgevoerd, zoals het installeren van besturingssysteemupdates. U moet mogelijk extra VM's toevoegen vanwege toegenomen verkeer naar uw app. In deze sectie wordt beschreven hoe u een virtuele machine (*myVM1*) uit het Load Balancer verwijdert of toevoegt.

### <a name="remove-vm-from-a-backend-pool"></a>Virtuele machine uit een back-end-pool verwijderen
Voer de volgende stappen uit om *myVM1* uit de back-end-pool te verwijderen:

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en klik vervolgens op **myLoadBalancer** in de lijst met resources.
2. Klik onder **Instellingen** op **Back-endpools**. Klik vervolgens in de lijst van de back-endpool op **myBackendPool**.
3. Selecteer op de pagina **myBackendPool** het pictogram *verwijderen aan* het einde van de rij met *MyVM1*en klik vervolgens op **Opslaan**.

Nu *myVM1* niet meer in de back-endadresgroep zit, kunt u onderhoudstaken uitvoeren op *myVM1*, zoals het installeren van software-updates. Als er geen *VM1*is, wordt de belasting nu verdeeld over *myVM2* en *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Een virtuele machine toevoegen aan een back-end-groep
Voer de volgende stappen uit om *myVM1* weer toe te voegen aan de back-end-groep:

1. Selecteer **alle services** in het linkermenu, selecteer **alle resources**en selecteer vervolgens **myVM1** in de lijst met resources.
2. Selecteer op de pagina **VM1** onder **instellingen**de optie **netwerken**.
3. Selecteer op de pagina **netwerken** het tabblad **taak verdeling** en selecteer vervolgens **taak verdeling toevoegen**.
4. Ga als volgt te werk op de pagina **taak verdeling toevoegen** :
   1. Selecteer **Azure Load Balancer**voor **Opties voor taak verdeling**.
   2. Selecteer *myLoadBalancer*voor **een Load Balancer selecteren**.
   3. Selecteer *myBackendPool*voor **een back-end-pool selecteren**. 

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de resource groep, Load Balancer en alle gerelateerde resources. Als u dit wilt doen, selecteert u de resource groep *myResouceGroupSLB* die de Load Balancer bevat en selecteert u vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Standard Load Balancer, gekoppelde virtuele machines gemaakt, de regel voor het Load Balancer verkeer geconfigureerd, de status test en vervolgens de Load Balancer getest. U hebt ook een VM verwijderd uit de set load balancers en deze weer toegevoegd aan de back-endadresgroep. Voor meer informatie over Azure Load Balancer gaat u verder met de zelfstudies voor Azure Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
