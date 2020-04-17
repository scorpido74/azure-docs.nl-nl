---
title: Een ILB-eindpunt (internal load balancer) configureren
titleSuffix: Azure Application Gateway
description: In dit artikel vindt u informatie over het configureren van Application Gateway met een privé-ip-adres aan de voorkant
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/16/2020
ms.author: victorh
ms.openlocfilehash: df21a2c40dd532ac1ff321638099ceee8a2b3e53
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535584"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Een toepassingsgateway configureren met een ILB-eindpunt (Internal Load Balancer)

Azure Application Gateway kan worden geconfigureerd met een VIP op internet of met een intern eindpunt dat niet is blootgesteld aan internet. Een intern eindpunt gebruikt een privé-IP-adres voor de frontend, dat ook wel bekend staat als een *ip-eindpunt (internal load balancer).*

Het configureren van de gateway met behulp van een frontend privé IP-adres is handig voor interne line-of-business-toepassingen die niet zijn blootgesteld aan internet. Het is ook handig voor services en lagen binnen een multi-tier applicatie die zich in een beveiligingsgrens bevindt die niet is blootgesteld aan het internet, maar nog steeds round-robin-belastingdistributie, sessiestickiness of Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), beëindiging vereist.

In dit artikel u een toepassingsgateway configureren met een privé-IP-adres met frontend met behulp van de Azure-portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U een nieuw virtueel netwerk maken of een bestaand netwerk gebruiken. In dit voorbeeld maakt u een nieuw virtueel netwerk. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt. Toepassingsgateway-exemplaren worden gemaakt in afzonderlijke subnetten. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

1. Vouw het portalmenu uit en selecteer **Een resource maken**.
2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst Aanbevolen.
3. Voer *myAppGateway* in voor de naam van de toepassingsgateway en *myResourceGroupAG* voor de nieuwe resourcegroep.
4. Selecteer **voor Regio** **(US) Central US**.
5. Selecteer **Standaard**selecteren **voor Laag**.
6. Selecteer **onder Virtueel netwerk configureren** nieuwe optie Nieuw **maken**en voer deze waarden in voor het virtuele netwerk:
   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myAGSubnet* als de naam van het subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.
   - *myBackendSubnet* - voor de backend subnet naam.
   - *10.0.1.0/24* - voor de backend subnet adresruimte.

    ![Virtueel netwerk maken](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selecteer **OK** om het virtuele netwerk en subnet te maken.
7. Selecteer **Volgende:Frontends**.
8. Selecteer **Privé**voor **IP-adrestype Frontend**.

   Standaard is het een dynamische IP-adrestoewijzing. Het eerste beschikbare adres van het geconfigureerde subnet wordt toegewezen als het frontend IP-adres.
   > [!NOTE]
   > Eenmaal toegewezen kan het IP-adrestype (statisch of dynamisch) later niet worden gewijzigd.
9. Selecteer **Volgende:Backends**.
10. Selecteer **Een backendpool toevoegen**.
11. Typ voor **Naam** *appGatewayBackendPool*.
12. Selecteer **Ja** **voor Backend pool toevoegen zonder doelen**. Je voegt de doelen later toe.
13. Selecteer **Toevoegen**.
14. Selecteer **Volgende:Configuratie**.
15. Selecteer **onder Routeringsregels**de optie **Een regel toevoegen**.
16. Typ *Rrule-01* **voor regelnaam**.
17. Typ *Listener-01*voor **listenernaam**.
18. Selecteer **Privé**voor **Frontend IP**.
19. Accepteer de resterende standaardinstellingen en selecteer het tabblad **Backenddoelen.**
20. Selecteer **voor Doeltype** **Backend-groep**en selecteer **vervolgens appGatewayBackendPool**.
21. Selecteer Nieuwe **optie Nieuwe optie voor** **HTTP-instelling**.
22. Typ *http-instelling-01*voor **http-instellingsnaam**.
23. Selecteer **HTTP**voor **backendprotocol**.
24. Voor **backendpoort**typt u *80*.
25. Accepteer de resterende standaardinstellingen en selecteer **Toevoegen**.
26. Selecteer op de pagina **Een routeringsregel** toevoegen de optie **Toevoegen**.
27. Selecteer **Volgende: Tags**.
28. Selecteer **Volgende: Controleren + maken**.
29. Controleer de instellingen op de overzichtspagina en selecteer **Maken** om de netwerkbronnen en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-pool"></a>Backendpool toevoegen

De backendpool wordt gebruikt om aanvragen te routeren naar de backendservers die het verzoek dienen. De backend kan bestaan uit NIC's, virtuele machineschaalsets, openbare IP-adressen, interne IP-adressen, volledig gekwalificeerde domeinnamen (FQDN) en back-ends met meerdere tenant's zoals Azure App Service. In dit voorbeeld gebruikt u virtuele machines als doelbackend. U bestaande virtuele machines gebruiken of nieuwe machines maken. In dit voorbeeld maakt u twee virtuele machines die in Azure worden gebruikt als back-endservers voor de toepassingsgateway.

Om dit te doen, u:

1. Maak twee nieuwe virtuele machines, *myVM* en *myVM2,* gebruikt als backend servers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de backendservers toe aan de backendpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken**.
2. Selecteer **Berekenen** en selecteer **Vervolgens Virtuele machine**.
4. Voer deze waarden in voor de virtuele machine:
   - selecteer *myResourceGroupAG* voor **resourcegroep**.
   - *myVM* - voor **virtuele machine naam**.
   - Selecteer **Windows Server 2019 Datacenter** voor **afbeelding**.
   - een geldige **gebruikersnaam**.
   - een geldig **wachtwoord**.
5. Accepteer de resterende standaardinstellingen en selecteer **Volgende: Schijven**.
6. Accepteer de standaardinstellingen en selecteer **Volgende: Netwerken**.
7. Zorg ervoor dat **myVNet** is geselecteerd voor het virtuele netwerk en dat het subnet **myBackendSubnet** is.
8. Accepteer de resterende standaardinstellingen en selecteer **Volgende : Beheer**.
9. Selecteer **Uit** om opstartdiagnoses uit te schakelen.
10. Accepteer de resterende standaardinstellingen en selecteer **Volgende: Geavanceerd**.
11. Selecteer **Volgende: Tags**.
12. Selecteer **Volgende : Controleren + maken**.
13. Controleer de instellingen op de overzichtspagina en selecteer **Maken**. Het kan enkele minuten duren voordat de VM is gemaakt. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

### <a name="install-iis"></a>IIS installeren

1. Open de Cloud Shell en zorg ervoor dat deze is ingesteld op **PowerShell.**
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
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



3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Voer myVM2 in voor de naam en voor VMName in Set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Backend-servers toevoegen aan backendpool

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.
2. Selecteer **Backend-groepen**. Selecteer **appGatewayBackendPool**.
3. Selecteer **onder Doeltype** **Virtuele machine** selecteren en selecteer onder **Doel**de vNIC die aan myVM is gekoppeld.
4. Herhaal dit om MyVM2 toe te voegen.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. selecteer **Opslaan.**

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Controleer uw frontend IP die is toegewezen door op de pagina **Frontend IP-configuraties** in de portal te klikken.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Kopieer het privé-IP-adres en plak het vervolgens in de adresbalk van de browser in een VM in dezelfde VNet- of on-premises met deze VNet-verbinding en probeer toegang te krijgen tot de Application Gateway.

## <a name="next-steps"></a>Volgende stappen

Zie [Back-endstatus en diagnostische logboeken voor Application Gateway](application-gateway-diagnostics.md)als u de status van uw back-end wilt controleren.
