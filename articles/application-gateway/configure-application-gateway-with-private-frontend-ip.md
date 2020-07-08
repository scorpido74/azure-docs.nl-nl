---
title: Een intern load balancer-eind punt (ILB) configureren
titleSuffix: Azure Application Gateway
description: Dit artikel bevat informatie over het configureren van Application Gateway met een privé-frontend-IP-adres
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/16/2020
ms.author: victorh
ms.openlocfilehash: c7a0022c5cff405a993f30cdf2ab5900485c84a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808115"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Een toepassings gateway met een ILB-eind punt (interne load balancer) configureren

Azure-toepassing gateway kan worden geconfigureerd met een Internet gerichte VIP of met een intern eind punt dat niet beschikbaar is op internet. Een intern eind punt maakt gebruik van een privé-IP-adres voor de frontend. dit wordt ook wel een *intern Load Balancer-eind punt (ILB)* genoemd.

Het configureren van de gateway met behulp van een privé-frontend-IP-adres is handig voor interne line-of-business-toepassingen die niet worden blootgesteld aan Internet. Het is ook nuttig voor services en lagen in een toepassing met meerdere lagen die zich in een beveiligings grens bevinden die niet beschikbaar is op internet, maar wel een Round Robin-taak verdeling, sessie persistentie of Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), is beëindigd.

Dit artikel begeleidt u bij de stappen voor het configureren van een toepassings gateway met een privé-IP-adres met een frontend met behulp van de Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaand gebruiken. In dit voor beeld maakt u een nieuw virtueel netwerk. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt. Instanties van toepassingsgateways worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

1. Vouw het menu Portal uit en selecteer **een resource maken**.
2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst Aanbevolen.
3. Voer *myAppGateway* in als de naam van de toepassings gateway en *myResourceGroupAG* voor de nieuwe resource groep.
4. Voor **regio**selecteert u **(Verenigde Staten) centraal**.
5. Selecteer bij **laag**de optie **standaard**.
6. Onder **virtuele netwerk configureren** selecteert u **nieuwe maken**en voert u vervolgens deze waarden in voor het virtuele netwerk:
   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myAGSubnet* als de naam van het subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.
   - *myBackendSubnet* : de naam van het back-end-subnet.
   - *10.0.1.0/24* : voor de adres ruimte van het back-end-subnet.

    ![Virtueel netwerk maken](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selecteer **OK** om het virtuele netwerk en subnet te maken.
7. Selecteer **volgende:** front-end.
8. Selecteer **privé**bij **IP-adres type voor frontend**.

   Het is standaard een dynamische IP-adres toewijzing. Het eerste beschik bare adres van het geconfigureerde subnet wordt toegewezen als het frontend-IP-adres.
   > [!NOTE]
   > Zodra het IP-adres type is toegewezen, kan het niet later worden gewijzigd.
9. Selecteer **volgende: back-end**.
10. Selecteer **een back-end-pool toevoegen**.
11. Typ *appGatewayBackendPool*voor **naam**.
12. Selecteer **Ja**als u de **back-end-pool wilt toevoegen zonder doelen**. U voegt de doelen later toe.
13. Selecteer **Toevoegen**.
14. Selecteer **volgende: Configuratie**.
15. Selecteer **een regel toevoegen**onder **routerings regels**.
16. Typ *Rrule-01*bij **regel naam**.
17. Voor de naam van de **listener**typt u *listener-01*.
18. Selecteer **privé**voor **frontend-IP**.
19. Accepteer de resterende standaard waarden en selecteer het tabblad **backend-doelen** .
20. Selecteer in **doel type** **back-end-pool**en selecteer vervolgens **appGatewayBackendPool**.
21. Voor **http-instelling**selecteert u **nieuwe maken**.
22. Typ *http-setting-01*voor de naam van de **http-instelling**.
23. Selecteer voor back-end **-** **protocol**http.
24. Typ *80*voor de **back-end-poort**.
25. Accepteer de resterende standaard waarden en selecteer **toevoegen**.
26. Selecteer op de pagina **een routerings regel toevoegen** de optie **toevoegen**.
27. Selecteer **volgende: Tags**.
28. Selecteer **Volgende: Controleren en maken**.
29. Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken** om de netwerk resources en de toepassings gateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-pool"></a>Back-endgroep toevoegen

De back-endpool word gebruikt om aanvragen te routeren naar de back-endservers die de aanvraag verwerken. De back-end kan bestaan uit Nic's, virtuele-machine schaal sets, open bare IP-adressen, interne IP-adressen, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voor beeld gebruikt u virtuele machines als doel-back-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. In dit voorbeeld maakt u twee virtuele machines die in Azure worden gebruikt als back-endservers voor de toepassingsgateway.

Hiervoor gaat u als volgt te werk:

1. Maak twee nieuwe virtuele machines, *myVM* en *myVM2*, die worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de back-endservers toe aan de back-endpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken**.
2. Selecteer **Compute** en selecteer vervolgens **Virtual Machine**.
4. Voer deze waarden in voor de virtuele machine:
   - Selecteer *myResourceGroupAG* voor **resource groep**.
   - *myVM* : voor de naam van de **virtuele machine**.
   - Selecteer **Windows Server 2019 Data Center** voor **installatie kopie**.
   - een geldige **gebruikers naam**.
   - een geldig **wacht woord**.
5. Accepteer de resterende standaard waarden en selecteer **volgende: schijven**.
6. Accepteer de standaard instellingen en selecteer **volgende: netwerken**.
7. Zorg ervoor dat **myVNet** is geselecteerd voor het virtuele netwerk en dat het subnet **myBackendSubnet** is.
8. Accepteer de resterende standaard waarden en selecteer **volgende: beheer**.
9. Selecteer **Uit** om diagnostische gegevens over opstarten uit te schakelen.
10. Accepteer de resterende standaard waarden en selecteer **volgende: Geavanceerd**.
11. Selecteer **volgende: Tags**.
12. Selecteer **volgende: controleren + maken**.
13. Controleer de instellingen op de overzichtspagina en selecteer **Maken**. Het maken van de virtuele machine kan enkele minuten duren. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

### <a name="install-iis"></a>IIS installeren

1. Open de Cloud Shell en zorg ervoor dat deze is ingesteld op **Power shell**.
    ![privé-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Voer myVM2 in als naam en voor VMName in set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Back-endservers toevoegen aan de back-endpool

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.
2. Selecteer **back-endservers**. Selecteer **appGatewayBackendPool**.
3. Onder **doel type** **virtuele machine** selecteren en onder **doel**selecteert u de vNIC die is gekoppeld aan myVM.
4. Herhaal deze stap om MyVM2 toe te voegen.
   ![privé-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Selecteer **opslaan.**

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Controleer het frontend-IP-adres dat is toegewezen door te klikken op de pagina **frontend IP-configuraties** in de portal.
    ![privé-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Kopieer het privé-IP-adres en plak het in de adres balk van de browser in een virtuele machine in hetzelfde VNet of op locatie die verbinding heeft met dit VNet en probeer toegang te krijgen tot de Application Gateway.

## <a name="next-steps"></a>Volgende stappen

Als u de status van uw back-end wilt bewaken, raadpleegt u [back-end status-en Diagnostische logboeken voor Application Gateway](application-gateway-diagnostics.md).
