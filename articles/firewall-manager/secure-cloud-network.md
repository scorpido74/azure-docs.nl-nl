---
title: 'Zelfstudie: Uw virtuele hub beveiligen met Azure Firewall Manager'
description: In deze zelfstudie leert u hoe u uw virtuele hub beveiligt met Azure Firewall Manager met behulp van Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c44daa67b4029c73c57ca82d72ee0a9759dd4c2d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563653"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Zelfstudie: Uw virtuele hub beveiligen met Azure Firewall Manager

Met behulp van Azure Firewall Manager kunt u beveiligde virtuele hubs maken om het cloudnetwerkverkeer te beveiligen dat bestemd is voor privé-IP-adressen, Azure PaaS en internet. Omleiding van het verkeer naar de firewall is geautomatiseerd, dus u hoeft geen door de gebruiker gedefinieerde routes (UDR's) te maken.

![Het cloudnetwerk beveiligen](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager biedt ook ondersteuning voor een virtuele-netwerkarchitectuur met hubs. Zie [Wat zijn de opties voor de Azure Firewall Manager-architectuur?](vhubs-and-vnets.md) voor een vergelijking van de architectuurtypen voor beveiligde virtuele hubs en virtuele hubnetwerken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het virtuele spoke-netwerk maken
> * Een beveiligde virtuele hub maken
> * De hub-en-spoke-VNets koppelen
> * Een firewallbeleid maken en uw hub beveiligen
> * Verkeer doorsturen naar uw hub
> * De firewall testen

## <a name="create-a-hub-and-spoke-architecture"></a>Een hub-en-spoke-architectuur maken

Maak eerst een virtueel spoke-netwerk waarin u uw servers kunt plaatsen.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Een virtueel spoke-netwerk en subnetten maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Virtueel netwerk** onder **Netwerken**.
2. Bij **Abonnement** selecteert u uw abonnement.
1. Als **Resourcegroep** selecteert u **Nieuwe maken**, typt u **FW-Manager** als naam en selecteert u **OK**.
2. Als **Naam** typt u **Spoke-01**.
3. Selecteer bij **Regio** **(VS) VS - oost**.
4. Selecteer **Volgende: IP-adressen**.
1. Als **Adresruimte** accepteert u de standaardinstelling **10.0.0.0/16**.
3. Onder **Subnetnaam** selecteert u **standaard**.
4. Wijzig de subnetnaam in **Workload-SN**.
5. Als **Subnetadresbereik** typt u **10.0.1.0/24**.
6. Selecteer **Opslaan**.

Maak hierna een subnet voor een jumpserver.

1. Selecteer **Subnet toevoegen**.
4. Als **Subnetnaam** typt u **Jump-SN**.
5. Als **Subnetadresbereik** typt u **10.0.2.0/24**.
6. Selecteer **Toevoegen**.

Maak nu het virtuele netwerk.

1. Selecteer **Controleren + maken**.
2. Selecteer **Maken**.

### <a name="create-the-secured-virtual-hub"></a>De beveiligde virtuele hub maken

Maak uw beveiligde virtuele hub met behulp van Firewall Manager.

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. In het zoekvak typt u **Firewall Manager** en selecteert u **Firewall Manager**.
3. Op de pagina **Firewall Manager** selecteert u **Beveiligde virtuele hubs weergeven**.
4. Op de pagina **Firewall Manager | Beveiligde virtuele hubs** selecteert u **Nieuwe beveiligde virtuele hub maken**.
5. Selecteer voor **Resourcegroep** de naam **FW-Manager**.
7. Selecteer bij **Regio** **VS - oost**.
1. Als de **Naam van beveiligde virtuele hub** typt u **Hub-01**.
2. Bij **Hubadresruimte** typt u **10.1.0.0/16**.
3. Als de nieuwe vWAN-naam typt u **Vwan-01**.
4. Laat het selectievakje **VPN-gateway opnemen om vertrouwde beveiligingspartners in te schakelen** leeg.
5. Selecteer **Volgende: Azure Firewall**.
6. Accepteer de standaardinstelling **Azure Firewall** **Ingeschakeld** en selecteer vervolgens **Volgende: Vertrouwde beveiligingspartner**.
7. Accepteer de standaardinstelling **Vertrouwde beveiligingspartner** **Uitgeschakeld** en selecteer **Volgende: Beoordelen en maken**.
8. Selecteer **Maken**. De implementatie duurt ongeveer 30 minuten.

### <a name="connect-the-hub-and-spoke-vnets"></a>De hub-en-spoke-VNets koppelen

U kunt nu de hub-en-spoke-VNets koppelen.

1. Selecteer de resourcegroep **FW-Manager** en selecteer vervolgens de virtuele WAN **Vwan-01**.
2. Onder **Connectiviteit** selecteert u **Virtuele netwerkverbindingen**.
3. Selecteer **Verbinding toevoegen**.
4. Als **Verbindingsnaam** typt u **hub-spoke**.
5. Als **Hubs** selecteert u **Hub-01**.
6. Selecteer voor **Resourcegroep** de naam **FW-Manager**.
7. Als **Virtueel netwerk**, selecteert u **Spoke-01**.
8. Selecteer **Maken**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Een firewallbeleid maken en uw hub beveiligen

Met een firewallbeleid worden verzamelingen regels gedefinieerd om verkeer om te leiden naar een of meer beveiligde virtuele hubs. U maakt uw firewallbeleid en vervolgens beveiligt u uw hub.

1. Bij Firewall Manager selecteert u **Azure Firewall-beleidsregels weergeven**.
2. Selecteer **Azure Firewall-beleid maken**.
3. Onder **Beleidsdetails** typt u voor **naam** **beleid-01** en voor **Regio** selecteert u **VS - oost**.
4. Selecteer **Volgende:Regels**.
5. Op het tabblad **Regels** selecteert u **Een regelverzameling toevoegen**.
6. Op de pagina **Een regelverzameling toevoegen** typt u **RC-01** voor de **Naam**.
7. Als **Type regelverzameling** selecteert u **Toepassing**.
8. Bij **Prioriteit** typt u **100**.
9. Controleer of **Type regelverzameling** is ingesteld op **Toestaan**.
10. Als de **Naam** van de regel typt u **Allow-msft**.
11. Selecteer **IP-adres** als het **Brontype**.
12. Typ bij **Bron** **\*** .
13. Als **Protocol** typt u **http,https**.
14. Controleer of **Doeltype** is ingesteld op **FQDN**.
15. Als **Doel** typt u **\*.microsoft.com**.
16. Selecteer **Toevoegen**.
17. Selecteer **Volgende : Bedreigingsinformatie**.
18. Selecteer **Volgende: Hubs**.
19. Op het tabblad **Hubs** selecteert u **Virtuele hubs koppelen**.
20. Selecteer **Hub-01** en selecteer vervolgens **Toevoegen**.
21. Selecteer **Controleren + maken**.
22. Selecteer **Maken**.

Dit duurt ongeveer vijf minuten voordat het is voltooid.

## <a name="route-traffic-to-your-hub"></a>Verkeer doorsturen naar uw hub

Nu moet u ervoor zorgen dat netwerkverkeer wordt omgeleid naar uw firewall.

1. Bij Firewall Manager selecteert u **Beveiligde virtuele hubs**.
2. Selecteer **Hub-01**.
3. Onder **Instellingen** selecteert u **Beveiligingsconfiguratie**.
4. Onder **Internetverkeer** selecteert u **Azure Firewall**.
5. Onder **Privéverkeer** selecteert u **Verzenden via Azure Firewall**.
10. Controleer of bij de **hub-spoke**-verbinding **Internetverkeer** als **Beveiligd** wordt weergegeven.
11. Selecteer **Opslaan**.


## <a name="test-your-firewall"></a>Uw firewall testen

Voor het testen van uw firewallregels moet u een aantal servers implementeren. Implementeer Workload-Srv in het subnet Workload-SN om de firewallregels te testen, en implementeer Jump-Srv zodat u extern bureaublad kunt gebruiken om via internet verbinding te maken. Maak vervolgens verbinding met Workload-Srv.

### <a name="deploy-the-servers"></a>De servers implementeren

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer in de lijst **Populair** de optie **Windows Server 2016-gegevenscentrum**.
3. Voer deze waarden in voor de virtuele machine:

   |Instelling  |Waarde  |
   |---------|---------|
   |Resourcegroep     |**FW-Manager**|
   |Naam van de virtuele machine     |**Jump-Srv**|
   |Regio     |**(VS) VS - oost**|
   |Beheerdersgebruikersnaam     |typ een gebruikersnaam|
   |Wachtwoord     |typ een wachtwoord|

4. Selecteer voor **Openbare binnenkomende poorten** onder **Regels voor binnenkomende poort** de optie **Geselecteerde poorten toestaan**.
5. Selecteer **RDP (3389)** voor **Binnenkomende poorten selecteren**.
6. Accepteer de overige standaardwaarden en selecteer **Volgende: Schijven**.
7. Accepteer de standaardwaarden voor schijven en selecteer **Volgende: Netwerken**.
8. Zorg ervoor dat **Spoke-01** is geselecteerd als virtuele netwerk en dat het subnet **Jump-SN** is.
9. Accepteer bij **Openbare IP** de standaardnaam van het nieuwe openbare IP-adres (Jump-Srv-ip).
11. Accepteer de overige standaardwaarden en selecteer **Volgende: Beheer**.
12. Selecteer **Uit** om diagnostische gegevens over opstarten uit te schakelen. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
13. Controleer de instellingen op de overzichtspagina en selecteer **Maken**.

Gebruik de informatie in de volgende tabel om een andere virtuele machine, **Workload-Srv**, te configureren. De rest van de configuratie is hetzelfde als voor de virtuele machine Srv-Jump.

|Instelling  |Waarde  |
|---------|---------|
|Subnet|**Workload-SN**|
|Openbare IP|**Geen**|
|Openbare poorten voor inkomend verkeer|**Geen**|

### <a name="add-a-route-table-and-default-route"></a>Een routetabel en standaardroute toevoegen

Wanneer u een internetverbinding met Jump-Srv tot stand wilt brengen, moet u een routetabel en een standaardgatewayroute naar internet maken vanaf het subnet **Jump-SN**.

1. Selecteer **Een resource maken** in de Azure-portal.
2. Typ **routetabel** in het zoekvak en selecteer vervolgens **Routetabel**.
3. Selecteer **Maken**.
4. Typ **RT-01** voor **Naam**.
5. Selecteer uw abonnement, **FW-Manager** voor de resourcegroep en **{VS} VS - oost** voor de regio.
6. Selecteer **Maken**.
7. Wanneer de implementatie is voltooid, selecteert u de routetabel **RT-01**.
8. Selecteer **Routes** en vervolgens **Toevoegen**.
9. Typ **jump-to-inet** voor de **Routenaam**.
10. Als **adresvoorvoegsel** typt u **0.0.0.0/0**.
11. Selecteer **Internet** als **Volgend hoptype**.
12. Selecteer **OK**.
13. Wanneer de implementatie is voltooid, selecteert u **Subnets** en vervolgens **Koppelen**.
14. Selecteer **Spoke-01** als **Virtueel netwerk**.
15. Selecteer **Jump-SN** als **Subnet**.
16. Selecteer **OK**.

### <a name="test-the-rules"></a>De regels testen

Test nu de firewallregels om te controleren of deze werkt zoals verwacht.

1. Controleer in Azure Portal de netwerkinstellingen voor de virtuele machine **Workload-Srv** en noteer het privé-IP-adres.
2. Verbind een extern bureaublad met de virtuele machine **Jump-Srv** en meld u aan. Open vervolgens een verbinding met een extern bureaublad met het privé-IP-adres **Workload-Srv**.

3. Open Internet Explorer en blader naar https://www.microsoft.com.
4. Selecteer **OK** > **Sluiten** in de beveiligingswaarschuwingen van Internet Explorer.

   Als het goed is, ziet u nu de startpagina van Microsoft.

5. Blader naar https://www.google.com.

   U zou nu door de firewall moeten worden geblokkeerd.

Nu u hebt geverifieerd dat de firewallregels werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over vertrouwde beveiligingspartners](trusted-security-partners.md)
