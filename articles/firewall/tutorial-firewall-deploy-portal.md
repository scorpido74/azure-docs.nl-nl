---
title: 'Zelf studie: Azure Firewall implementeren met behulp van de Azure Portal & configureren'
description: In deze zelfstudie leert u hoe u Azure Firewall kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359198"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Zelfstudie: Azure Firewall implementeren en configureren met de Azure-portal

Het beheren van toegang tot uitgaande netwerken is een belangrijk onderdeel van een algemeen netwerkbeveiligingsabonnement. U kunt bijvoorbeeld de toegang tot websites beperken. Het is ook mogelijk dat u de uitgaande IP-adressen en poorten wilt beperken waartoe toegang kan worden verkregen.

Een van de manieren waarop u de toegang tot uitgaande netwerken kunt beheren vanaf een Azure-subnet is met Azure Firewall. Met Azure Firewall kunt u het volgende configureren:

* Toepassingsregels die volledig gekwalificeerde domeinnamen (FQDN's) definiëren waartoe toegang kan worden verkregen via een subnet.
* Netwerkregels die een bronadres, protocol, doelpoort en doeladres definiëren.

Netwerkverkeer is onderhevig aan de geconfigureerde firewallregels wanneer u het routeert naar de firewall als standaardgateway van het subnet.

Voor deze zelfstudie maakt u één vereenvoudigd VNet met drie subnetten voor eenvoudige implementatie. Voor productie-implementaties wordt een [hub-en spoke-model](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) aanbevolen. De firewall bevindt zich in een eigen VNet. De werkbelasting servers bevinden zich in een gepeerd VNets in dezelfde regio met een of meer subnetten.

* **AzureFirewallSubnet** – De firewall bevindt zich in dit subnet.
* **Workload-SN** – De workloadserver bevindt zich in dit subnet. Het netwerkverkeer van dit subnet gaat via de firewall.
* **Jump-SN** – De "jumpserver" bevindt zich in dit subnet. De jumpserver heeft een openbaar IP-adres waarmee u verbinding kunt maken via een extern bureaublad. Van daaruit kunt u vervolgens (via een ander extern bureaublad) verbinding maken met de workloadserver.

![Netwerkinfrastructuur van zelfstudie](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een toepassings regel configureren om toegang tot www.google.com toe te staan
> * Een netwerkregel configureren om toegang tot externe DNS-servers toe te staan
> * De firewall testen

U kunt deze zelfstudie desgewenst volgen met behulp van [Azure PowerShell](deploy-ps.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="set-up-the-network"></a>Het netwerk instellen

Maak eerst een resourcegroep met de resources die nodig zijn om de firewall te implementeren. Maak vervolgens een VNet, subnetten en testservers.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De resourcegroep bevat alle resources voor de zelfstudie.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **resource groepen** in het menu Azure portal of zoek naar een wille keurige pagina en selecteer *resource groepen* . Selecteer vervolgens **Toevoegen**.
3. Voer voor de naam van de **resource groep** *test-FW-RG*in.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Bij **Resourcegroeplocatie** selecteert u een locatie. Alle andere resources die u maakt, moeten zich op dezelfde locatie behoeven.
6. Selecteer **Maken**.

### <a name="create-a-vnet"></a>Een VNet maken

Dit VNet bevat drie subnetten.

> [!NOTE]
> De grootte van het AzureFirewallSubnet-subnet is/26. Zie [Azure firewall FAQ (Engelstalig](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)) voor meer informatie over de grootte van het subnet.

1. Selecteer in het menu Azure Portal of op de **Start** pagina de optie **een resource maken**.
1. Selecteer **netwerk** > **virtueel netwerk**.
1. Bij **Naam** typt u **Test-FW-VN**.
1. Bij **Adresruimte** typt u **10.0.0.0/16**.
1. Bij **Abonnement** selecteert u uw abonnement.
1. Voor **resource groep**selecteert u **test-FW-RG**.
1. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
1. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**. De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
1. Typ **10.0.1.0/26**voor het **adres bereik**.
1. Accepteer de andere standaard instellingen en selecteer vervolgens **maken**.

### <a name="create-additional-subnets"></a>Extra subnetten maken

Maak vervolgens subnetten voor de jumpserver en een subnet voor de workloadservers.

1. Selecteer **resource groepen** in het menu Azure portal of zoek naar een wille keurige pagina en selecteer *resource groepen* . Selecteer vervolgens **test-FW-RG**.
2. Selecteer het virtuele netwerk **test-FW-VN** .
3. Selecteer **subnetten** >  **+ subnet**.
4. Bij **Naam** typt u **Workload-SN**.
5. Bij **Adresbereik** typt u **10.0.2.0/24**.
6. Selecteer **OK**.

Maak nog een subnet met **Jump-SN** als naam en **10.0.3.0/24** als adresbereik.

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de virtuele jump- en workloadmachines en plaats ze in de toepasselijke subnetten.

1. Selecteer in het menu Azure Portal of op de **Start** pagina de optie **een resource maken**.
2. Selecteer **Compute** en selecteer vervolgens **Windows Server 2016 Datacenter** in de lijst Aanbevolen.
3. Voer deze waarden in voor de virtuele machine:

   |Instelling  |Waarde  |
   |---------|---------|
   |Resourcegroep     |**Test-FW-RG**|
   |Naam van de virtuele machine     |**SRV-Jump**|
   |Regio     |Hetzelfde als vorige|
   |Gebruikers naam van beheerder     |**azureuser**|
   |Wachtwoord     |**Azure123456!**|

4. Onder **Binnenkomende poort regels**, voor **open bare binnenkomende poorten**, selecteert u **geselecteerde poorten toestaan**.
5. Selecteer **RDP (3389)** voor **Binnenkomende poorten selecteren**.

6. Accepteer de andere standaard waarden en selecteer **volgende: schijven**.
7. Accepteer de standaard waarden voor de schijf en selecteer **volgende: netwerken**.
8. Zorg ervoor dat **Test-FW-VN** is geselecteerd voor het virtuele netwerk en dat het subnet **Jump-SN** is.
9. Accepteer voor **openbaar IP**de standaard naam van het nieuwe open bare IP-adres (SRV-Jump-IP).
11. Accepteer de andere standaard waarden en selecteer **volgende: beheer**.
12. Selecteer **uitschakelen** om diagnostische gegevens over opstarten uit te scha kelen. Accepteer de andere standaard waarden en selecteer **controleren + maken**.
13. Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.

Gebruik de informatie in de volgende tabel om een andere virtuele machine met de naam **SRV-work**te configureren. De rest van de configuratie is hetzelfde als voor de virtuele machine Srv-Jump.

|Instelling  |Waarde  |
|---------|---------|
|Subnet|**Workload-SN**|
|Openbare IP|**Geen**|
|Openbare poorten voor inkomend verkeer|**Geen**|

## <a name="deploy-the-firewall"></a>De firewall implementeren

Implementeer de firewall in het VNet.

1. Selecteer in het menu Azure Portal of op de **Start** pagina de optie **een resource maken**.
2. Typ **firewall** in het zoekvak en druk op **Enter**.
3. Selecteer **firewall** en selecteer vervolgens **maken**.
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Abonnement     |\<uw abonnement\>|
   |Resourcegroep     |**Test-FW-RG** |
   |Naam     |**Test-FW01**|
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**: **test-FW-VN**|
   |Openbaar IP-adres     |**Nieuwe toevoegen**. Het openbare IP-adres moet van het type Standaard-SKU zijn.|

5. Selecteer **Controleren + maken**.
6. Bekijk de samen vatting en selecteer vervolgens **maken** om de firewall te maken.

   Het duurt enkele minuten voordat deze is geïmplementeerd.
7. Nadat de implementatie is voltooid, gaat u naar de resource groep **test-FW-RG** en selecteert u de **test-FW01-** firewall.
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-a-default-route"></a>Een standaardroute maken

Voor het subnet **Workload-SN** configureert u de standaardroute voor uitgaand verkeer om via de firewall te gaan.

1. Selecteer in het menu Azure Portal **alle services** of zoek naar *alle services* op een wille keurige pagina en selecteer deze.
2. Selecteer in **netwerken** **route tabellen**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **Firewall-route**.
5. Bij **Abonnement** selecteert u uw abonnement.
6. Voor **resource groep**selecteert u **test-FW-RG**.
7. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
8. Selecteer **Maken**.
9. Selecteer **vernieuwen**en selecteer vervolgens de route tabel **firewall-route** .
10. Selecteer **subnetten** en selecteer vervolgens **koppelen**.
11. Selecteer **virtual network** > **test-FW-VN**.
12. Selecteer voor **subnet**de optie **workload-SN**. Zorg ervoor dat u alleen het **workload-SN-** subnet voor deze route selecteert, anders werkt uw firewall niet correct.

13. Selecteer **OK**.
14. Selecteer **routes** en selecteer vervolgens **toevoegen**.
15. Typ voor **route naam** **FW-DG**.
16. Bij **Adresvoorvoegsel** typt u **0.0.0.0/0**.
17. Bij **Volgend hoptype** selecteert u **Virtueel apparaat**.

    Azure Firewall is eigenlijk een beheerde service, maar Virtueel apparaat werkt in deze situatie.
18. Bij **Adres van de volgende hop** typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
19. Selecteer **OK**.

## <a name="configure-an-application-rule"></a>Een toepassingsregel configureren

Dit is de toepassings regel waarmee uitgaande toegang tot www.google.com wordt toegestaan.

1. Open de **test-FW-RG**en selecteer de **FW01-** firewall.
2. Selecteer op de pagina **test-FW01** onder **instellingen**de optie **regels**.
3. Selecteer het tabblad **toepassings regel verzameling** .
4. Selecteer **verzameling toepassings regels toevoegen**.
5. Bij **Naam** typt u **App-Coll01**.
6. Bij **Prioriteit** typt u **200**.
7. Bij **Actie** selecteert u **Toestaan**.
8. Onder **regels**, **doel-FQDN**- **namen**, type **toestaan-Google**.
9. Selecteer **IP-adres**bij **bron type**.
10. Typ **10.0.2.0/24**voor **bron**.
11. Bij **Protocol:poort** typt u **http, https**.
12. Voor **doel-FQDN**-typen, typt u **www.Google.com**
13. Selecteer **Toevoegen**.

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. Zie [FQDN's voor infrastructuur](infrastructure-fqdns.md) voor meer informatie.

## <a name="configure-a-network-rule"></a>Een netwerkregel configureren

Dit is de netwerkregel waarmee uitgaande toegang tot twee IP-adressen op poort 53 (DNS) wordt toegestaan.

1. Selecteer het tabblad verzameling van de **netwerk regel** .
2. Selecteer **verzameling netwerk regel toevoegen**.
3. Bij **Naam** typt u **Net-Coll01**.
4. Bij **Prioriteit** typt u **200**.
5. Bij **Actie** selecteert u **Toestaan**.
6. Onder **regels**, **IP-adressen**, typt u de **naam** **Allow-DNS**.
7. Bij **Protocol** selecteert u **UDP**.
9. Selecteer **IP-adres**bij **bron type**.
1. Typ **10.0.2.0/24**voor **bron**.
2. Voor het **doel adres**typt u **209.244.0.3, 209.244.0.4**

   Dit zijn open bare DNS-servers die worden beheerd door CenturyLink.
1. Bij **Doelpoorten** typt u **53**.
2. Selecteer **Toevoegen**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Het primaire en secundaire DNS-adres voor de netwerkinterface **Srv-Work** wijzigen

Voor test doeleinden in deze zelf studie configureert u de primaire en secundaire DNS-adressen van de server. Dit is geen algemene Azure Firewall vereiste.

1. Selecteer **resource groepen** in het menu Azure portal of zoek naar een wille keurige pagina en selecteer *resource groepen* . Selecteer de resource groep **test-FW-RG** .
2. Selecteer de netwerk interface voor de virtuele machine **met SRV-werk** .
3. Selecteer bij **instellingen**de optie **DNS-servers**.
4. Onder **DNS-servers**selecteert u **aangepast**.
5. Typ **209.244.0.3** in het tekstvak **DNS-server toevoegen** en **209.244.0.4** in het volgende tekstvak.
6. Selecteer **Opslaan**.
7. Start de virtuele machine **Srv-Work** opnieuw.

## <a name="test-the-firewall"></a>De firewall testen

Test nu de firewall om te controleren of deze werkt zoals verwacht.

1. Controleer in de Azure-portal de netwerkinstellingen voor de virtuele machine **Srv-Work** en noteer het privé-IP-adres.
2. Verbind een extern bureau blad met **SRV-Jump** virtuele machine en meld u aan. Open vanaf daar een verbinding met een extern bureau blad met het particuliere IP-adres van **SRV-werk** .
3. Open Internet Explorer en blader naar https://www.google.com.
4. Selecteer **OK** > **sluiten** op de beveiligings waarschuwingen van Internet Explorer.

   U ziet nu de Google-start pagina.

5. Blader naar https://www.microsoft.com.

   U zou nu door de firewall moeten worden geblokkeerd.

Nu hebt u gecontroleerd of de firewall regels werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.
* Kunt u DNS-namen omzetten met behulp van de geconfigureerde externe DNS-server.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **Test-FW-RG** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
