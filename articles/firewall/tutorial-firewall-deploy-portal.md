---
title: 'Zelfstudie: Azure Firewall & configureren met de Azure-portal'
description: In deze zelfstudie leert u hoe u Azure Firewall kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239572"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Zelfstudie: Azure Firewall implementeren en configureren met de Azure-portal

Het beheren van toegang tot uitgaande netwerken is een belangrijk onderdeel van een algemeen netwerkbeveiligingsabonnement. U bijvoorbeeld de toegang tot websites beperken. U ook de uitgaande IP-adressen en poorten beperken die toegankelijk zijn.

Een van de manieren waarop u de toegang tot uitgaande netwerken kunt beheren vanaf een Azure-subnet is met Azure Firewall. Met Azure Firewall kunt u het volgende configureren:

* Toepassingsregels die volledig gekwalificeerde domeinnamen (FQDN's) definiëren waartoe toegang kan worden verkregen via een subnet.
* Netwerkregels die een bronadres, protocol, doelpoort en doeladres definiëren.

Netwerkverkeer is onderhevig aan de geconfigureerde firewallregels wanneer u het routeert naar de firewall als standaardgateway van het subnet.

Voor deze zelfstudie maakt u één vereenvoudigd VNet met drie subnetten voor eenvoudige implementatie. Voor productie-implementaties wordt een [hub- en spaakmodel](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) aanbevolen. De firewall bevindt zich in zijn eigen VNet. De workloadservers bevinden zich in peered VNets in dezelfde regio met een of meer subnetten.

* **AzureFirewallSubnet** – De firewall bevindt zich in dit subnet.
* **Workload-SN** – De workloadserver bevindt zich in dit subnet. Het netwerkverkeer van dit subnet gaat via de firewall.
* **Jump-SN** – De "jumpserver" bevindt zich in dit subnet. De jumpserver heeft een openbaar IP-adres waarmee u verbinding kunt maken via een extern bureaublad. Van daaruit kunt u vervolgens (via een ander extern bureaublad) verbinding maken met de workloadserver.

![Netwerkinfrastructuur van zelfstudie](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een toepassingsregel configureren om toegang te krijgen tot www.google.com
> * Een netwerkregel configureren om toegang tot externe DNS-servers toe te staan
> * De firewall testen

U kunt deze zelfstudie desgewenst volgen met behulp van [Azure PowerShell](deploy-ps.md).

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="set-up-the-network"></a>Netwerk instellen

Maak eerst een resourcegroep met de resources die nodig zijn om de firewall te implementeren. Maak vervolgens een VNet, subnetten en testservers.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De resourcegroep bevat alle resources voor de zelfstudie.

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
2. Selecteer **resourcegroepen** in het menu Van de Azure-portal of zoek naar resourcegroepen op een pagina en selecteer *deze.* Selecteer vervolgens **Toevoegen**.
3. Voer *test-FW-RG*in voor **de naam van de resourcegroep**.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Bij **Resourcegroeplocatie** selecteert u een locatie. Alle andere resources die u maakt, moeten zich op dezelfde locatie bevinden.
6. Selecteer **Maken**.

### <a name="create-a-vnet"></a>Een VNet maken

Dit VNet bevat drie subnetten.

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)over azure firewall voor meer informatie over de subnetgrootte.

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
1. Selecteer **Netwerkvirtueel** > **netwerk**.
1. Bij **Naam** typt u **Test-FW-VN**.
1. Bij **Adresruimte** typt u **10.0.0.0/16**.
1. Bij **Abonnement** selecteert u uw abonnement.
1. Selecteer **Test-FW-RG**voor **resourcegroep**.
1. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
1. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**. De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
1. Voor **adresbereik**typt u **10.0.1.0/26**.
1. Accepteer de andere standaardinstellingen en selecteer **Vervolgens Maken**.

### <a name="create-additional-subnets"></a>Extra subnetten maken

Maak vervolgens subnetten voor de jumpserver en een subnet voor de workloadservers.

1. Selecteer **resourcegroepen** in het menu Van de Azure-portal of zoek naar resourcegroepen op een pagina en selecteer *deze.* Selecteer vervolgens **Test-FW-RG**.
2. Selecteer het virtuele **netwerk Test-FW-VN.**
3. Selecteer **Subnetten** > **+Subnet**.
4. Bij **Naam** typt u **Workload-SN**.
5. Bij **Adresbereik** typt u **10.0.2.0/24**.
6. Selecteer **OK**.

Maak nog een subnet met **Jump-SN** als naam en **10.0.3.0/24** als adresbereik.

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de virtuele jump- en workloadmachines en plaats ze in de toepasselijke subnetten.

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
2. Selecteer **Compute** en selecteer vervolgens **Windows Server 2016 Datacenter** in de lijst Aanbevolen.
3. Voer deze waarden in voor de virtuele machine:

   |Instelling  |Waarde  |
   |---------|---------|
   |Resourcegroep     |**Test-FW-RG**|
   |Naam van de virtuele machine     |**Srv-Sprong**|
   |Regio     |Hetzelfde als vorige|
   |Beheerdersgebruikersnaam     |**azureuser**|
   |Wachtwoord     |**Azure123456!**|

4. Selecteer **Geselecteerde poorten toestaan**onder **inkomende poortregels**voor **openbare binnenkomende poorten**.
5. Selecteer **RDP (3389)** voor **Binnenkomende poorten selecteren**.

6. Accepteer de andere standaardinstellingen en selecteer **Volgende: schijven**.
7. Accepteer de standaardinstellingen voor de schijf en selecteer **Volgende: Netwerken**.
8. Zorg ervoor dat **Test-FW-VN** is geselecteerd voor het virtuele netwerk en dat het subnet **Jump-SN** is.
9. Accepteer voor **openbare IP**de standaardnaam van het nieuwe openbare IP-adres (Srv-Jump-ip).
11. Accepteer de andere standaardinstellingen en selecteer **Volgende: Beheer**.
12. Selecteer **Uit** om opstartdiagnoses uit te schakelen. Accepteer de andere standaardinstellingen en selecteer **Controleren + maken**.
13. Controleer de instellingen op de overzichtspagina en selecteer **Maken**.

Gebruik de informatie in de volgende tabel om een andere virtuele machine met de naam **Srv-Work**te configureren. De rest van de configuratie is hetzelfde als voor de virtuele machine Srv-Jump.

|Instelling  |Waarde  |
|---------|---------|
|Subnet|**Workload-SN**|
|Openbare IP|**Geen**|
|Openbare poorten voor inkomend verkeer|**Geen**|

## <a name="deploy-the-firewall"></a>De firewall implementeren

Implementeer de firewall in het VNet.

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
2. Typ **firewall** in het zoekvak en druk op **Enter**.
3. Selecteer **Firewall** en selecteer **Vervolgens Maken**.
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Abonnement     |\<uw abonnement\>|
   |Resourcegroep     |**Test-FW-RG** |
   |Name     |**Test-FW01**|
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**: **Test-FW-VN**|
   |Openbaar IP-adres     |**Voeg nieuwe .** Het openbare IP-adres moet van het type Standaard-SKU zijn.|

5. Selecteer **Controleren + maken**.
6. Bekijk het overzicht en selecteer **Maken** om de firewall te maken.

   Het duurt enkele minuten voordat deze is geïmplementeerd.
7. Nadat de implementatie is voltooid, gaat u naar de brongroep **Test-FW-RG** en selecteert u de **firewall Test-FW01.**
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-a-default-route"></a>Een standaardroute maken

Voor het subnet **Workload-SN** configureert u de standaardroute voor uitgaand verkeer om via de firewall te gaan.

1. Selecteer in het menu van de Azure-portal **Alle services** of zoek naar alle services op een pagina en selecteer *deze.*
2. Selecteer **routetabellen**onder **Netwerken**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **Firewall-route**.
5. Bij **Abonnement** selecteert u uw abonnement.
6. Selecteer **Test-FW-RG**voor **resourcegroep**.
7. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
8. Selecteer **Maken**.
9. Selecteer **Vernieuwen**en selecteer vervolgens de routetabel **Firewall-route.**
10. Selecteer **Subnetten** en selecteer **Vervolgens Associate**.
11. Selecteer **Virtueel netwerk** > **Test-FW-VN**.
12. Selecteer **Workload-SN**voor **Subnet**. Zorg ervoor dat u alleen het **Subnet Workload-SN** voor deze route selecteert, anders werkt uw firewall niet goed.

13. Selecteer **OK**.
14. Selecteer **Routes** en selecteer **Vervolgens Toevoegen**.
15. Typ **fw-dg** **voor routenaam**.
16. Bij **Adresvoorvoegsel** typt u **0.0.0.0/0**.
17. Bij **Volgend hoptype** selecteert u **Virtueel apparaat**.

    Azure Firewall is eigenlijk een beheerde service, maar Virtueel apparaat werkt in deze situatie.
18. Bij **Adres van de volgende hop** typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
19. Selecteer **OK**.

## <a name="configure-an-application-rule"></a>Een toepassingsregel configureren

Dit is de toepassingsregel die uitgaande toegang tot www.google.com mogelijk maakt.

1. Open de **Test-FW-RG**en selecteer de **Firewall Test-FW01.**
2. Selecteer op de pagina **Test-FW01** onder **Instellingen**de optie **Regels**.
3. Selecteer het tabblad **Toepassingsregelverzameling.**
4. Selecteer **Verzameling toepassingsregel toevoegen**.
5. Bij **Naam** typt u **App-Coll01**.
6. Bij **Prioriteit** typt u **200**.
7. Bij **Actie** selecteert u **Toestaan**.
8. Onder **Regels** **, Target FQDN's**, voor **Naam**, typ **Allow-Google**.
9. Selecteer **IP-adres**voor **Brontype**.
10. Voor **Bron**typt u **10.0.2.0/24**.
11. Bij **Protocol:poort** typt u **http, https**.
12. Typ **www.google.com** **voor DoelFQDNS**
13. Selecteer **Toevoegen**.

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. Zie [FQDN's voor infrastructuur](infrastructure-fqdns.md) voor meer informatie.

## <a name="configure-a-network-rule"></a>Een netwerkregel configureren

Dit is de netwerkregel waarmee uitgaande toegang tot twee IP-adressen op poort 53 (DNS) wordt toegestaan.

1. Selecteer het tabblad **Netwerkregelverzameling.**
2. Selecteer **Netwerkregelverzameling toevoegen**.
3. Bij **Naam** typt u **Net-Coll01**.
4. Bij **Prioriteit** typt u **200**.
5. Bij **Actie** selecteert u **Toestaan**.
6. Typ **Toestaan-DNS**onder **Regels** **, IP-adressen**voor **Naam**.
7. Bij **Protocol** selecteert u **UDP**.
9. Selecteer **IP-adres**voor **Brontype**.
1. Voor **Bron**typt u **10.0.2.0/24**.
2. Voor **bestemmingsadres**, type **209.244.0.3.209.244.0.4**

   Dit zijn openbare DNS-servers die worden beheerd door CenturyLink.
1. Bij **Doelpoorten** typt u **53**.
2. Selecteer **Toevoegen**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Het primaire en secundaire DNS-adres voor de netwerkinterface **Srv-Work** wijzigen

Configureer de primaire en secundaire DNS-adressen van de server voor testdoeleinden in deze zelfstudie. Dit is geen algemene Azure Firewall-vereiste.

1. Selecteer **resourcegroepen** in het menu Van de Azure-portal of zoek naar resourcegroepen op een pagina en selecteer *deze.* Selecteer de brongroep **Test-FW-RG.**
2. Selecteer de netwerkinterface voor de virtuele **srv-work-machine.**
3. Selecteer **onder Instellingen** **DNS-servers**.
4. Selecteer **Onder DNS-servers**de optie **Aangepast**.
5. Typ **209.244.0.3** in het tekstvak **DNS-server toevoegen** en **209.244.0.4** in het volgende tekstvak.
6. Selecteer **Opslaan**.
7. Start de virtuele machine **Srv-Work** opnieuw.

## <a name="test-the-firewall"></a>De firewall testen

Test nu de firewall om te bevestigen dat het werkt zoals verwacht.

1. Controleer in de Azure-portal de netwerkinstellingen voor de virtuele machine **Srv-Work** en noteer het privé-IP-adres.
2. Sluit een extern bureaublad aan op de virtuele machine **van Srv-Jump** en meld u aan. Open van daaruit een externe desktopverbinding met het **privé-IP-adres van Srv-Work.**
3. Open Internet Explorer en blader naar https://www.google.com.
4. Selecteer **OK** > **Sluiten** op de beveiligingswaarschuwingen van Internet Explorer.

   U ziet de startpagina van Google.

5. Blader naar https://www.microsoft.com.

   U zou nu door de firewall moeten worden geblokkeerd.

Dus nu heb je geverifieerd dat de firewall regels werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.
* Kunt u DNS-namen omzetten met behulp van de geconfigureerde externe DNS-server.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **Test-FW-RG** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
