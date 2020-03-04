---
title: Inkomend Internet verkeer filteren met Azure Firewall DNAT met behulp van de portal
description: In deze zelfstudie leert u hoe u Azure Firewall DNAT kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/02/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7220e48c6103352108bdb89e107bb862ee194040
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251493"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Zelf studie: inkomend Internet verkeer filteren met Azure Firewall DNAT met behulp van de Azure Portal

U kunt Azure Firewall (DNAT) van het Doelnet doel configureren voor het vertalen en filteren van inkomend Internet verkeer naar uw subnetten. Wanneer u DNAT configureert, wordt de actie Verzameling van NAT-regels ingesteld op **Dnat**. Elke regel in de verzameling van NAT-regels kan vervolgens worden gebruikt voor de omzetting van het openbare IP-adres en de openbare poort van uw firewall in een privé-IP-adres en -poort. Met DNAT-regels wordt er impliciet een overeenkomende netwerkregel toegevoegd om het omgezette verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Zie [Verwerkingslogica voor Azure Firewall-regels](rule-processing.md) voor meer informatie over de verwerkingslogica voor Azure Firewall-regels.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een DNAT-regel configureren
> * De firewall testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

In deze zelfstudie maakt u twee VNets die peer zijn van elkaar:

- **VN-Hub**: de firewall bevindt zich in dit VNet.
- **VN-Spoke**: de workloadserver bevindt zich in dit VNet.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer op de start pagina van Azure Portal de optie **resource groepen**en selecteer vervolgens **toevoegen**.
3. Bij **Resourcegroepnaam** typt u **RG-DNAT-Test**.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Bij **Resourcegroeplocatie** selecteert u een locatie. Alle volgende resources die u maakt, moeten zich op dezelfde locatie bevinden.
6. Selecteer **Maken**.

## <a name="set-up-the-network-environment"></a>De netwerkomgeving instellen

Maak eerst de VNets en peer ze.

### <a name="create-the-hub-vnet"></a>Een hub-VNet maken

1. Selecteer op de start pagina van Azure Portal **alle services**.
2. Onder **netwerken**selecteert u **virtuele netwerken**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **VN-Hub**.
5. Bij **Adresruimte** typt u **10.0.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**.

     De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
     > [!NOTE]
     > De grootte van het AzureFirewallSubnet-subnet is/26. Zie [Azure firewall FAQ (Engelstalig](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)) voor meer informatie over de grootte van het subnet.

10. Typ **10.0.1.0/26**voor het **adres bereik**.
11. Gebruik de andere standaard instellingen en selecteer vervolgens **maken**.

### <a name="create-a-spoke-vnet"></a>Een spoke-VNet maken

1. Selecteer op de start pagina van Azure Portal **alle services**.
2. Onder **netwerken**selecteert u **virtuele netwerken**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **VN-Spoke**.
5. Bij **Adresruimte** typt u **192.168.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Workload** bij **Naam**.

    De server wordt in dit subnet geplaatst.
10. Bij **Adresbereik** typt u **192.168.1.0/24**.
11. Gebruik de andere standaard instellingen en selecteer vervolgens **maken**.

### <a name="peer-the-vnets"></a>De VNets instellen als peers

Nu gaat u de twee VNets als peer van elkaar instellen.

1. Selecteer het virtuele netwerk **VN-hub** .
2. Selecteer onder **instellingen**de optie **peerings**.
3. Selecteer **Toevoegen**.
4. Typ **peer-HubSpoke** voor de **naam van de peering van VN-hub naar VN Spaak**.
5. Selecteer **VN-Spoke** voor het virtuele netwerk.
6. Typ **peer-SpokeHub** voor de **naam van peering van VN-spoke naar VN-hub**.
7. Selecteer **ingeschakeld**om **doorgestuurd verkeer van VN-spoke naar VN-hub toe te staan** .
8. Selecteer **OK**.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele machine met de naam 'workload' en plaats deze in het subnet **SN-Workload**.

1. Selecteer in het menu Azure Portal de optie **een resource maken**.
2. Onder **populair**selecteert u **Windows Server 2016 Data Center**.

**Basisinstellingen**

1. Bij **Abonnement** selecteert u uw abonnement.
1. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
1. Typ **SRV-workload**voor de naam van de **virtuele machine**.
1. Selecteer voor **regio**dezelfde locatie die u eerder hebt gebruikt.
1. Typ een gebruikersnaam en wachtwoord.
1. Selecteer **volgende: schijven**.

**Schijven**
1. Selecteer **volgende: netwerken**.

**Netwerken**

1. Selecteer voor **virtueel netwerk** **VN-spoke**.
2. Bij **Subnet** selecteert u **SN-Workload**.
3. Selecteer **geen**voor **openbaar IP-adres** .
4. Selecteer **geen**bij **open bare binnenkomende poorten**. 
2. Wijzig de overige standaard instellingen en selecteer **volgende: beheer**.

**Beheer**

1. Selecteer **uit**voor **Diagnostische gegevens over opstarten**.
1. Selecteer **Controleren + maken**.

**Controleren en maken**

Controleer de samen vatting en selecteer vervolgens **maken**. Het duurt enkele minuten voordat dit is voltooid.

Als de implementatie is voltooid, ziet u het privé IP-adres voor de virtuele machine. Noteer dit voor later gebruik, wanneer u de firewall gaat configureren. Selecteer de naam van de virtuele machine en selecteer onder **instellingen**de optie **netwerken** om het privé-IP-adres te zoeken.

## <a name="deploy-the-firewall"></a>De firewall implementeren

1. Selecteer op de start pagina van de Portal de optie **een resource maken**.
2. Selecteer **netwerken**en selecteer na **Aanbevolen**de optie **alles weer geven**.
3. Selecteer **firewall**en selecteer vervolgens **maken**. 
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Naam     |FW-DNAT-test|
   |Abonnement     |\<uw abonnement\>|
   |Resourcegroep     |**Bestaande gebruiken**: RG-DNAT-Test |
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**: VN-Hub|
   |Openbaar IP-adres     |**Nieuwe maken**. Het openbare IP-adres moet van het type Standaard-SKU zijn.|

5. Selecteer **Controleren + maken**.
6. Bekijk de samen vatting en selecteer vervolgens **maken** om de firewall te maken.

   Het duurt enkele minuten voordat deze is geïmplementeerd.
7. Nadat de implementatie is voltooid, gaat u naar de resource groep **RG-DNAT-test** en selecteert u de ' **FW-DNAT-test-** firewall.
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-a-default-route"></a>Een standaardroute maken

Voor het subnet **SN-Workload** configureert u dat de standaardroute voor uitgaand verkeer via de firewall loopt.

1. Selecteer op de start pagina van Azure Portal **alle services**.
2. Selecteer in **netwerken** **route tabellen**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **RT-FWroute**.
5. Bij **Abonnement** selecteert u uw abonnement.
6. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u **RG-DNAT-Test**.
7. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
8. Selecteer **Maken**.
9. Selecteer **vernieuwen**en selecteer vervolgens de **RT-FWroute-** route tabel.
10. Selecteer **subnetten**en selecteer vervolgens **koppelen**.
11. Selecteer **virtueel netwerk**en selecteer vervolgens **VN-Spaak**.
12. Bij **Subnet** selecteert u **SN-Workload**.
13. Selecteer **OK**.
14. Selecteer **routes**en selecteer vervolgens **toevoegen**.
15. Bij **Routenaam** typt u **FW-DG**.
16. Bij **Adresvoorvoegsel** typt u **0.0.0.0/0**.
17. Bij **Volgend hoptype** selecteert u **Virtueel apparaat**.

    Azure Firewall is eigenlijk een beheerde service, maar Virtueel apparaat werkt in deze situatie.
18. Bij **Adres van de volgende hop** typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
19. Selecteer **OK**.

## <a name="configure-a-nat-rule"></a>Een NAT-regel configureren

1. Open de **RG-DNAT-test**en selecteer de **FW-DNAT-test-** firewall. 
2. Selecteer op de pagina **FW-DNAT-test** onder **instellingen**de optie **regels**. 
3. Selecteer **verzameling van NAT-regels toevoegen**. 
4. Bij **Naam** typt u **RC-DNAT-01**. 
5. Bij **Prioriteit** typt u **200**. 
6. Onder **Regels** typt u bij **Naam** de naam **RL-01**.
7. Bij **Protocol** selecteert u **TCP**.
8. Bij **Bronadressen** typt u *. 
9. Bij **Doeladressen** typt u het openbare IP-adres van de firewall. 
10. Bij **Doelpoorten** typt u **3389**. 
11. Bij **Omgezet adres** typt u het privé-IP-adres voor de virtuele machine Srv-Workload. 
12. Bij **Vertaalde poort** typt u **3389**. 
13. Selecteer **Toevoegen**. 

## <a name="test-the-firewall"></a>De firewall testen

1. Verbind een extern-bureaubladsessie met het openbare IP-adres van de firewall. U wordt als het goed is verbonden met de virtuele machine **Srv-Workload**.
2. Sluit de sessie van Extern bureaublad.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources behouden voor de volgende zelfstudie. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **RG-DNAT-Test** om alle aan de firewall gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een DNAT-regel configureren
> * De firewall testen

Als volgende kunt u de Azure Firewall-logboeken bewaken.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
