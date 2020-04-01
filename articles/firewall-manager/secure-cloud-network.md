---
title: 'Zelfstudie: Uw virtuele WAN beveiligen met de preview-proefversie van Azure Firewall Manager'
description: In deze zelfstudie leert u hoe u uw virtuele WAN beveiligen met Azure Firewall Manager met behulp van de Azure-portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77443054"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Zelfstudie: Uw virtuele WAN beveiligen met de preview-proefversie van Azure Firewall Manager 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Met Azure Firewall Manager Preview u beveiligde virtuele hubs maken om uw cloudnetwerkverkeer te beveiligen dat is bestemd voor privé-IP-adressen, Azure PaaS en internet. Verkeer routering naar de firewall is geautomatiseerd, dus er is geen noodzaak om door de gebruiker gedefinieerde routes (UDR's) te maken.

![het cloudnetwerk beveiligen](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager ondersteunt ook een virtuele hub-netwerkarchitectuur. Zie [Wat zijn de azure Firewall Manager-architectuuropties voor](vhubs-and-vnets.md) een vergelijking van de beveiligde virtuele hub- en hubtypen voor virtuele netwerkarchitectuur?

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het virtuele spoke-netwerk maken
> * Een beveiligde virtuele hub maken
> * Sluit de hub en spaak VNets
> * Een firewallbeleid maken en uw hub beveiligen
> * Verkeer naar uw hub leiden
> * De firewall testen

## <a name="create-a-hub-and-spoke-architecture"></a>Een hub en spaakarchitectuur maken

Maak eerst een gesproken VNet waar u uw servers plaatsen.

### <a name="create-a-spoke-vnet-and-subnets"></a>Een gesproken VNet en subnetten maken

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Selecteer **Onder Netwerken**de optie Virtueel **netwerk**.
4. Typ **Spoke-01**voor **naam**.
5. Bij **Adresruimte** typt u **10.0.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Selecteer **voor resourcegroep** **Nieuw maken**en typ **FW-Manager** voor de naam en selecteer **OK**.
8. Selecteer **voor Locatie** **(US) East US**.
9. Onder **Subnet**voor **Naamtype** **Workload-SN**.
10. Bij **Adresbereik** typt u **10.0.1.0/24**.
11. Accepteer de andere standaardinstellingen en selecteer **Vervolgens Maken**.

Maak vervolgens een subnet voor een jump server.

1. Selecteer op de startpagina van de Azure-portal **resourcegroepen** > **FW-Manager**.
2. Selecteer het virtuele netwerk **Spoke-01.**
3. Selecteer **Subnetten** > **+Subnet**.
4. Typ Voor **Naam** **Jump-SN**.
5. Bij **Adresbereik** typt u **10.0.2.0/24**.
6. Selecteer **OK**.

### <a name="create-the-secured-virtual-hub"></a>De beveiligde virtuele hub maken

Maak uw beveiligde virtuele hub met Firewall Manager.

1. Selecteer **alle services**op de startpagina van de Azure-portal .
2. Typ **Firewallbeheer** in het zoekvak en selecteer **Firewallbeheer**.
3. Selecteer **op** de pagina Firewall Manager de optie **Een beveiligde virtuele hub maken**.
4. Selecteer **op de** pagina Nieuwe beveiligde virtuele hub maken uw abonnement en de **brongroep FW-Manager.**
5. Typ **Hub-01**voor de **naam Beveiligde virtuele hub**.
6. Selecteer **Oost-VS**voor **Locatie**.
7. Typ **10.1.0.0/16**voor **hubadresruimte**.
8. Voor de nieuwe vWAN naam, type **vwan-01**.
9. Laat de **VPN-gateway opnemen om vertrouwde beveiligingspartners in te schakelen.**
10. Selecteer **Volgende:Azure Firewall**.
11. Accepteer de standaardinstelling **Azure Firewall** **Ingeschakeld** en selecteer **Volgende: Vertrouwde beveiligingspartner**.
12. Accepteer de standaard instelling **Uitgeschakelde** **vertrouwde beveiligingspartner** en selecteer **Volgende: Controleren + maken**.
13. Selecteer **Maken**. Het duurt ongeveer 30 minuten om te implementeren.

### <a name="connect-the-hub-and-spoke-vnets"></a>Sluit de hub en spaak VNets

Nu u peer de hub en sprak VNets.

1. Selecteer de **brongroep FW-Manager** en selecteer vervolgens het virtuele WAN **vwan-01.**
2. Selecteer **onder Connectiviteit**virtuele **netwerkverbindingen**.
3. Selecteer **Verbinding toevoegen**.
4. Typ **hub-spoke**voor **verbindingsnaam**.
5. Selecteer **Hub-01**voor **Hubs**.
6. Selecteer **Spoke-01**voor **virtueel netwerk**.
7. Selecteer **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Een firewallbeleid maken en uw hub beveiligen

Een firewallbeleid definieert verzamelingen van regels om verkeer op een of meer beveiligde virtuele hubs te leiden. U maakt uw firewallbeleid en beveiligt vervolgens uw hub.

1. Selecteer in Firewallbeheer de optie **Een Azure Firewall-beleid maken**.
2. Selecteer uw abonnement en selecteer vervolgens de **brongroep FW-Manager.**
3. Selecteer Oost-VS onder **Beleidsdetails**voor het **type Naam** **beleid-01** en voor **Regio** **Selecteer Oost-VS**.
4. Selecteer **Volgende:Regels**.
5. Selecteer op het tabblad **Regels** de optie **Een regelverzameling toevoegen**.
6. Typ **RC-01** op de pagina **Een regelverzameling toevoegen** voor de **naam**.
7. Selecteer **Toepassing**voor **regelverzamelingstype**.
8. Voor **prioriteit**, type **100**.
9. **Regelverzamelingsactie** is **Toestaan**.
10. Ga voor **regelnaamtype** **Toestaan-msft**.
11. Typ **bronadres voor bronadres** **\***.
12. Typ **http, https**voor **Protocol**.
13. Zorg ervoor dat **Doeltype **FQDN is**.
14. Typ **voor Bestemming** ** \*.microsoft.com**.
15. Selecteer **Toevoegen**.
16. Selecteer **Volgende: Beveiligde virtuele hubs**.
17. Selecteer **Hub-01**op het tabblad **Beveiligde virtuele hubs** .
19. Selecteer **Controleren + maken**.
20. Selecteer **Maken**.

Dit kan ongeveer vijf minuten of langer duren.

## <a name="route-traffic-to-your-hub"></a>Verkeer naar uw hub leiden

Nu moet u ervoor zorgen dat het netwerkverkeer wordt doorgestuurd naar via uw firewall.

1. Selecteer **beveiligde virtuele hubs in**Firewall Manager.
2. Selecteer **Hub-01**.
3. Selecteer **Route-instellingen**onder **Instellingen**.
4. Selecteer Onder **Internetverkeer,** **Verkeer van virtuele netwerken**de optie Verzenden via Azure **Firewall**.
5. Selecteer Onder **Azure privéverkeer** **, Verkeer naar virtuele netwerken,** de optie **Verzenden via Azure Firewall**.
6. Selecteer **IP-adresvoorvoegsel(en) bewerken.**
7. Selecteer **Een IP-adresvoorvoegsel toevoegen**.
8. Typ **10.0.1.0/24** als het adres van het subnet Werkbelasting en selecteer **Opslaan**.
9. Selecteer **Onder Instellingen**de optie **Verbindingen**.
10. Selecteer de **hub-spoke-verbinding** en selecteer **Vervolgens Internetverkeer beveiligen** en selecteer **OK**.


## <a name="test-your-firewall"></a>Uw firewall testen

Als u uw firewallregels wilt testen, moet u een aantal servers implementeren. U implementeert Workload-Srv in het Subnet Workload-SN om de firewallregels te testen, en Jump-Srv, zodat u Extern bureaublad gebruiken om verbinding te maken via internet en vervolgens verbinding te maken met Workload-Srv.

### <a name="deploy-the-servers"></a>De servers implementeren

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer **Windows Server 2016 Datacenter** in de lijst **Populair.**
3. Voer deze waarden in voor de virtuele machine:

   |Instelling  |Waarde  |
   |---------|---------|
   |Resourcegroep     |**FW-Manager**|
   |Naam van de virtuele machine     |**Jump-Srv**|
   |Regio     |**(VS) Oost-VS)**|
   |Beheerdersgebruikersnaam     |**azureuser**|
   |Wachtwoord     |uw wachtwoord typen|

4. Selecteer **Geselecteerde poorten toestaan**onder **inkomende poortregels**voor **openbare binnenkomende poorten**.
5. Selecteer **RDP (3389)** voor **Binnenkomende poorten selecteren**.

6. Accepteer de andere standaardinstellingen en selecteer **Volgende: schijven**.
7. Accepteer de standaardinstellingen voor de schijf en selecteer **Volgende: Netwerken**.
8. Zorg ervoor dat **Spoke-01** is geselecteerd voor het virtuele netwerk en het subnet **is Jump-SN**.
9. Accepteer voor **openbare IP**de standaardnaam van het nieuwe openbare IP-adres (Jump-Srv-ip).
11. Accepteer de andere standaardinstellingen en selecteer **Volgende: Beheer**.
12. Selecteer **Uit** om opstartdiagnoses uit te schakelen. Accepteer de andere standaardinstellingen en selecteer **Controleren + maken**.
13. Controleer de instellingen op de overzichtspagina en selecteer **Maken**.

Gebruik de informatie in de volgende tabel om een andere virtuele machine met de naam **Workload-Srv**te configureren. De rest van de configuratie is hetzelfde als voor de virtuele machine Srv-Jump.

|Instelling  |Waarde  |
|---------|---------|
|Subnet|**Workload-SN**|
|Openbare IP|**Geen**|
|Openbare poorten voor inkomend verkeer|**Geen**|

### <a name="add-a-route-table-and-default-route"></a>Een routetabel en standaardroute toevoegen

Als u een internetverbinding met Jump-Srv wilt toestaan, moet u een routetabel en een standaardgatewayroute naar het internet maken via het **Jump-SN-subnet.**

1. Selecteer **Een resource maken** in de Azure-portal.
2. Typ **routetabel** in het zoekvak en selecteer **routetabel**.
3. Selecteer **Maken**.
4. Typ **RT-01** voor **Naam**.
5. Selecteer uw abonnement, **FW-Manager** voor de resourcegroep en **(US) East US** voor de regio.
6. Selecteer **Maken**.
7. Wanneer de implementatie is voltooid, selecteert u de **routetabel RT-01.**
8. Selecteer **Routes** en selecteer **Vervolgens Toevoegen**.
9. Typ **jump-to-inet** voor de **naam Route**.
10. Typ **0.0.0.0/0** voor het **adresvoorvoegsel**.
11. Selecteer **Internet** voor het **hoptype Volgende**.
12. Selecteer **OK**.
13. Wanneer de implementatie is voltooid, selecteert u **Subnetten**en selecteert u **Associate**.
14. Selecteer **Spoke-01** voor **virtueel netwerk**.
15. Selecteer **Jump-SN** voor **subnet**.
16. Selecteer **OK**.

### <a name="test-the-rules"></a>Test de regels

Test nu de firewallregels om te bevestigen dat het werkt zoals verwacht.

1. Bekijk in de Azure-portal de netwerkinstellingen voor de virtuele **workload-Srv en** noteer het privé-IP-adres.
2. Sluit een extern bureaublad aan op de virtuele machine **jump-Srv** en meld u aan. Open van daaruit een externe desktopverbinding met het **privé-IP-adres Workload-Srv.**

3. Open Internet Explorer en blader naar https://www.microsoft.com.
4. Selecteer **OK** > **Sluiten** op de beveiligingswaarschuwingen van Internet Explorer.

   U ziet de startpagina van Microsoft.

5. Blader naar https://www.google.com.

   U zou nu door de firewall moeten worden geblokkeerd.

Dus nu heb je geverifieerd dat de firewall regels werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over vertrouwde beveiligingspartners](trusted-security-partners.md)
