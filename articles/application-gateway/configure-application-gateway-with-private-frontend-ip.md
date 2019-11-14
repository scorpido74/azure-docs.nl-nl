---
title: Een intern load balancer-eind punt (ILB) configureren
titleSuffix: Azure Application Gateway
description: Dit artikel bevat informatie over het configureren van Application Gateway met een privé-frontend-IP-adres
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075224"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Een toepassings gateway met een ILB-eind punt (interne load balancer) configureren

Azure-toepassing gateway kan worden geconfigureerd met een Internet gerichte VIP of met een intern eind punt dat niet beschikbaar is op internet (met behulp van een privé-IP voor het frontend-IP-adres), ook wel een intern load balancer (ILB)-eind punt genoemd. Het configureren van de gateway met behulp van een privé-frontend-IP-adres is handig voor interne line-of-business-toepassingen die niet toegankelijk zijn via internet. Ook is dit handig als u services en lagen gebruikt in een toepassing met meerdere lagen die zich binnen een beveiligingsgrens bevinden, en als deze toepassing geen toegang heeft tot het internet, maar er wel round-robinbelastingverdeling, sessiepersistentie of SSL-beëindiging (Secure Sockets Layer) vereist is.

In dit artikel worden de stappen beschreven voor het configureren van een toepassings gateway met een privé-IP-adres van de frontend van Azure Portal.

In dit artikel leert u hoe u:

- Een persoonlijke frontend-IP-configuratie maken voor een Application Gateway
- Een toepassings gateway met een persoonlijke frontend-IP-configuratie maken


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Aanmelden bij Azure Portal op <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaande gebruiken. In dit voor beeld maken we een nieuw virtueel netwerk. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt. Application Gateway exemplaren worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

1. Klik op **Nieuw** gevonden in de linkerbovenhoek van de Azure Portal.
2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst Aanbevolen.
3. Voer *myAppGateway* in als de naam van de toepassings gateway en *myResourceGroupAG* voor de nieuwe resource groep.
4. Accepteer de standaardwaarden voor de overige instellingen en klik op **OK**.
5. Klik op **Een virtueel netwerk kiezen**, klik op **Nieuw maken** en voer deze waarden in voor het virtuele netwerk:
   - myVNet *: voor de naam van het virtuele netwerk.
   - 10.0.0.0/16 *: voor de adres ruimte van het virtuele netwerk.
   - *myAGSubnet* als de naam van het subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.  
     ![privé-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Klik op **OK** om het virtuele netwerk en subnet te maken.
7. Kies de frontend-IP-configuratie als privé en standaard een dynamische IP-adres toewijzing. Het eerste beschik bare adres van het gekozen subnet wordt toegewezen als frontend-IP-adres.
8. Als u een privé-IP-adres wilt kiezen in het subnetadres (statische toewijzing), klikt u op het vak **Kies een specifiek privé-IP-adres** en geeft u het IP-adres op.
   > [!NOTE]
   > Zodra het IP-adres type is toegewezen, kan het niet later worden gewijzigd.
9. Kies de configuratie van de listener voor het protocol en de poort, WAF-configuratie (indien nodig) en klik op OK.
    ![privé-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Controleer de instellingen op de pagina samen vatting en klik vervolgens op **OK** om de netwerk resources en de toepassings gateway te maken. Het duurt enkele minuten voor de toepassingsgateway worden gemaakt, wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-pool"></a>Back-end-pool toevoegen

De back-end-groep wordt gebruikt voor het routeren van aanvragen naar de back-endservers die de aanvraag zullen verwerken. Back-end kan bestaan uit Nic's, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voor beeld gebruiken we virtuele machines als doel-back-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. In dit voor beeld maken we twee virtuele machines die door Azure worden gebruikt als back-endservers voor de toepassings gateway. Om dit te doen, zullen we het volgende doen:

1. Maak 2 nieuwe VM'S, *myVM* en *myVM2*, die moeten worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassings gateway is gemaakt.
3. Voeg de back-endservers toe aan de back-end-groep.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Klik op **Nieuw**.
2. Klik op **Compute** en selecteer vervolgens **Windows Server 2016 Datacenter** in de lijst Aanbevolen.
3. Voer deze waarden in voor de virtuele machine:
   - *myVM* als naam van de virtuele machine.
   - *azureuser* als gebruikersnaam van de beheerder.
   - *Azure123456!* als het wachtwoord.
   - Selecteer **Bestaande gebruiken** en selecteer *myResourceGroupAG*.
4. Klik op **OK**.
5. Selecteer **DS1_V2** voor de grootte van de virtuele machine en klik op **selecteren**.
6. Zorg ervoor dat **myVNet** is geselecteerd voor het virtuele netwerk en dat het subnet **myBackendSubnet** is.
7. Klik op **Uitgeschakeld** om diagnostische gegevens over opstarten uit te schakelen.
8. Klik op **OK**, controleer de instellingen op de overzichtspagina en klik op **Maken**.

### <a name="install-iis"></a>IIS installeren

1. Open de interactieve shell en zorg ervoor dat deze is ingesteld op **PowerShell**.
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
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
