---
title: Binnenkomend verkeer filteren met Azure Firewall DNAT via de Azure-portal
description: In deze zelfstudie leert u hoe u Azure Firewall DNAT kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f0a58382b9825a7b32aee69c00b9801d1c77251a
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114631"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Zelfstudie: Binnenkomend verkeer filteren met Azure Firewall DNAT via de Azure-portal

U kunt Azure Firewall Destination Network Address Translation (DNAT) configureren voor het omzetten en filteren van inkomend verkeer naar uw subnetten. Wanneer u DNAT configureert, wordt de actie Verzameling van NAT-regels ingesteld op **Dnat**. Elke regel in de verzameling van NAT-regels kan vervolgens worden gebruikt voor de omzetting van het openbare IP-adres en de openbare poort van uw firewall in een privé-IP-adres en -poort. Met DNAT-regels wordt er impliciet een overeenkomende netwerkregel toegevoegd om het omgezette verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Zie [Verwerkingslogica voor Azure Firewall-regels](rule-processing.md) voor meer informatie over de verwerkingslogica voor Azure Firewall-regels.

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
2. Klik op de startpagina van de Azure-portal op **Resourcegroepen** en klik vervolgens op **Toevoegen**.
3. Bij **Resourcegroepnaam** typt u **RG-DNAT-Test**.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Bij **Resourcegroeplocatie** selecteert u een locatie. Alle volgende resources die u maakt, moeten zich op dezelfde locatie bevinden.
6. Klik op **Create**.

## <a name="set-up-the-network-environment"></a>De netwerkomgeving instellen

Maak eerst de VNets en peer ze.

### <a name="create-the-hub-vnet"></a>Een hub-VNet maken

1. Klik op de startpagina van de Azure-portal op **Alle services**.
2. Klik onder **Netwerken** op **Virtuele netwerken**.
3. Klik op **Toevoegen**.
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
11. Gebruik de andere standaardinstellingen en klik vervolgens op **Maken**.

### <a name="create-a-spoke-vnet"></a>Een spoke-VNet maken

1. Klik op de startpagina van de Azure-portal op **Alle services**.
2. Klik onder **Netwerken** op **Virtuele netwerken**.
3. Klik op **Toevoegen**.
4. Bij **Naam** typt u **VN-Spoke**.
5. Bij **Adresruimte** typt u **192.168.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Workload** bij **Naam**.

    De server wordt in dit subnet geplaatst.
10. Bij **Adresbereik** typt u **192.168.1.0/24**.
11. Gebruik de andere standaardinstellingen en klik vervolgens op **Maken**.

### <a name="peer-the-vnets"></a>De VNets instellen als peers

Nu gaat u de twee VNets als peer van elkaar instellen.

#### <a name="hub-to-spoke"></a>Hub naar spoke

1. Klik op het virtuele netwerk **VN-Hub**.
2. Klik onder **Instellingen** op **Peerings**.
3. Klik op **Toevoegen**.
4. Typ **Peer-HubSpoke** als de naam.
5. Selecteer **VN-Spoke** voor het virtuele netwerk.
6. Klik op **OK**.

#### <a name="spoke-to-hub"></a>Spoke naar hub

1. Klik op het virtuele netwerk **VN-Spoke**.
2. Klik onder **Instellingen** op **Peerings**.
3. Klik op **Toevoegen**.
4. Typ **Peer-SpokeHub** als de naam.
5. Selecteer **VN-Hub** voor het virtuele netwerk.
6. Klik op **Doorgestuurd verkeer toestaan**.
7. Klik op **OK**.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele machine met de naam 'workload' en plaats deze in het subnet **SN-Workload**.

1. Klik op de startpagina van de Azure-portal op **Alle services**.
2. Klik onder **Compute** op **Virtuele machines**.
3. Klik achtereenvolgens op **Toevoegen**, **Windows Server**, **Windows Server 2016 Datacenter** en **Maken**.

**Basisinstellingen**

1. Bij **Naam** typt u **Srv-Workload**.
5. Typ een gebruikersnaam en wachtwoord.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Bij **Resourcegroep** klikt u op **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Klik op **OK**.

**Grootte**

1. Kies een geschikte grootte voor een virtuele testmachine waarop Windows Server wordt uitgevoerd, bijvoorbeeld **B2ms** (8 GB RAM, 16 GB opslag).
2. Klik op **Selecteren**.

**Instellingen**

1. Onder **Netwerk** selecteert u bij **Virtueel netwerk** de optie **VN-Spoke**.
2. Bij **Subnet** selecteert u **SN-Workload**.
3. Klik op **Openbaar IP-adres** en klik vervolgens op **Geen**.
4. Bij **Openbare binnenkomende poorten selecteren** selecteert u **Geen openbare binnenkomende poorten**. 
2. Behoud de andere standaardinstellingen en klik op **OK**.

**Samenvatting**

Controleer de samenvatting en klik vervolgens op **Maken**. Het duurt enkele minuten voordat dit is voltooid.

Als de implementatie is voltooid, ziet u het privé IP-adres voor de virtuele machine. Noteer dit voor later gebruik, wanneer u de firewall gaat configureren. Klik op de naam van de virtuele machine en klik onder **Instellingen** op **Netwerken** om het privé IP-adres te vinden.

## <a name="deploy-the-firewall"></a>De firewall implementeren

1. Klik op de startpagina van de portal op **Een resource maken**.
2. Klik op **Netwerken** en klik bij **Aanbevolen** op **Alles weergeven**.
3. Klik op **Firewall** en klik vervolgens op **Maken**. 
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Name     |FW-DNAT-test|
   |Subscription     |\<uw abonnement\>|
   |Resource group     |**Bestaande gebruiken**: RG-DNAT-test |
   |Location     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**: VN-Hub|
   |Openbaar IP-adres     |**Nieuwe maken**. Het openbare IP-adres moet van het type Standaard-SKU zijn.|

5. Klik op **Controleren + maken**.
6. Controleer de samenvatting en klik vervolgens op **Maken** om de firewall te maken.

   Het duurt enkele minuten voordat deze is geïmplementeerd.
7. Nadat de implementatie is voltooid, gaat u naar de resourcegroep **RG-DNAT-Test** en klikt u op de firewall **FW-DNAT-test**.
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-a-default-route"></a>Een standaardroute maken

Voor het subnet **SN-Workload** configureert u dat de standaardroute voor uitgaand verkeer via de firewall loopt.

1. Klik op de startpagina van de Azure-portal op **Alle services**.
2. Klik onder **Netwerken** op **Routetabellen**.
3. Klik op **Toevoegen**.
4. Bij **Naam** typt u **RT-FWroute**.
5. Bij **Abonnement** selecteert u uw abonnement.
6. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u **RG-DNAT-Test**.
7. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
8. Klik op **Create**.
9. Klik op **Vernieuwen** en klik vervolgens op de routetabel **RT-FWroute**.
10. Klik op **Subnetten** en klik vervolgens op **Koppelen**.
11. Klik op **Virtueel netwerk** en selecteer vervolgens **VN-Spoke**.
12. Klik bij **Subnet** op **SN-Workload**.
13. Klik op **OK**.
14. Klik op **Routes** en klik vervolgens op **Toevoegen**.
15. Bij **Routenaam** typt u **FW-DG**.
16. Bij **Adresvoorvoegsel** typt u **0.0.0.0/0**.
17. Bij **Volgend hoptype** selecteert u **Virtueel apparaat**.

    Azure Firewall is eigenlijk een beheerde service, maar Virtueel apparaat werkt in deze situatie.
18. Bij **Adres van de volgende hop** typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
19. Klik op **OK**.

## <a name="configure-a-nat-rule"></a>Een NAT-regel configureren

1. Open de groep **RG-DNAT-Test** en klik op de firewall **FW-DNAT-test**. 
2. Klik op de pagina **FW-DNAT-test** onder **Instellingen** op **Regels**. 
3. Klik op **Verzameling NAT-regels toevoegen**. 
4. Bij **Naam** typt u **RC-DNAT-01**. 
5. Bij **Prioriteit** typt u **200**. 
6. Onder **Regels** typt u bij **Naam** de naam **RL-01**.
7. Bij **Protocol** selecteert u **TCP**.
8. Bij **Bronadressen** typt u *. 
9. Bij **Doeladressen** typt u het openbare IP-adres van de firewall. 
10. Bij **Doelpoorten** typt u **3389**. 
11. Bij **Omgezet adres** typt u het privé-IP-adres voor de virtuele machine Srv-Workload. 
12. Bij **Vertaalde poort** typt u **3389**. 
13. Klik op **Toevoegen**. 

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
