---
title: HTTP-naar-HTTPS-omleiding in portal-Azure-toepassing gateway
description: Informatie over het maken van een toepassingsgateway met omgeleide verkeer van HTTP naar HTTPS met behulp van de Azure-portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 51c191a7815bb64243e2324e150c00c2dcb7ec4c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705323"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Een toepassingsgateway maken met HTTP naar HTTPS-omleiding via Azure portal

U kunt de Azure-portal gebruiken om te maken een [toepassingsgateway](overview.md) met een certificaat voor SSL-beëindiging. Een regel voor doorsturen wordt gebruikt om HTTP-verkeer omleiden naar de HTTPS-poort in uw application gateway. In dit voorbeeld maakt u ook een [virtuele-machineschaalset](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) voor de back-endpool van de toepassingsgateway met twee exemplaren van de virtuele machine.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een netwerk instellen
> * Een toepassingsgateway maken met behulp van het certificaat
> * Een listener en omleiding van-regel toevoegen
> * Een virtuele-machineschaalset maken met de standaard back-endpool

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist voor het maken van een certificaat en het installeren van IIS. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). De opdrachten in deze zelfstudie, hebt u ook nodig om uit te voeren `Login-AzAccount` voor het maken van een verbinding met Azure.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor gebruik in productieomgevingen, moet u een geldig certificaat dat is ondertekend door een vertrouwde provider importeren. Voor deze zelfstudie maakt u een zelfondertekend certificaat met behulp van [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). U kunt [Export-PfxCertificate ](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) gebruiken met de Thumbprint die is geretourneerd om een ​​PFX-bestand uit het certificaat te exporteren.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

U zou iets moeten zien dat lijkt op dit resultaat:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Gebruik de vingerafdruk om het PFX-bestand te maken:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Een Application Gateway maken

U hebt een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. In dit voorbeeld worden twee subnetten gemaakt: één voor de toepassingsgateway en de andere voor de back-endservers. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.
3. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst Aanbevolen.
4. Voer deze waarden in voor de toepassingsgateway:

   - *myAppGateway* als de naam van de toepassingsgateway.
   - *myResourceGroupAG* als de nieuwe resourcegroep.

     ![Nieuwe toepassingsgateway maken](./media/create-url-route-portal/application-gateway-create.png)

5. Accepteer de standaardwaarden voor de overige instellingen en klik op **OK**.
6. Klik op **Een virtueel netwerk kiezen**, klik op **Nieuw maken** en voer deze waarden in voor het virtuele netwerk:

   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myAGSubnet* als de naam van het subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.

     ![Virtueel netwerk maken](./media/create-url-route-portal/application-gateway-vnet.png)

7. Klik op **OK** om het virtuele netwerk en subnet te maken.
8. Onder **Frontend-IP-configuratie**, zorg ervoor dat **type IP-adres** is **openbare**, en **nieuw** is geselecteerd. Voer *myAGPublicIPAddress* voor de naam. Accepteer de standaardwaarden voor de overige instellingen en klik op **OK**.
9. Onder **listenerconfiguratie**, selecteer **HTTPS**en selecteer vervolgens **selecteert u een bestand** en navigeer naar de *c:\appgwcert.pfx* bestand en Selecteer **Open**.
10. Type *appgwcert* voor de naam van het certificaat en *Azure123456!* als het wachtwoord.
11. Laat u de Web application firewall is uitgeschakeld, en selecteer vervolgens **OK**.
12. Controleer de instellingen op de pagina Samenvatting en selecteer vervolgens **OK** om de netwerkbronnen en de application gateway te maken. Het duurt enkele minuten voor de toepassingsgateway worden gemaakt, wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

### <a name="add-a-subnet"></a>Een subnet toevoegen

1. Selecteer **alle resources** in het menu links, en selecteer vervolgens **myVNet** in de lijst met resources.
2. Selecteer **subnetten**, en klik vervolgens op **Subnet**.

    ![Subnet maken](./media/create-url-route-portal/application-gateway-subnet.png)

3. Type *myBackendSubnet* voor de naam van het subnet.
4. Type *10.0.2.0/24* voor het adresbereik, en selecteer vervolgens **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Een listener en omleiding van-regel toevoegen

### <a name="add-the-listener"></a>Voeg de listener toe

Voeg eerst de listener met de naam toe *myListener* voor poort 80.

1. Open de **myResourceGroupAG** resource groep en selecteert u een **myAppGateway**.
2. Selecteer **Listeners** en selecteer vervolgens **+ Basic**.
3. Type *MyListener* voor de naam.
4. Type *httpPort* voor de nieuwe naam van de frontend-poort en *80* voor de poort.
5. Zorg ervoor dat het protocol is ingesteld op **HTTP**, en selecteer vervolgens **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Toevoegen van een regel voor doorsturen met de configuratie van een omleiding

1. Selecteer op **myAppGateway**de optie **regels** en selecteer vervolgens **routerings regel voor aanvraag**.
2. Typ *firewallregel2*voor de naam van de **regel**.
3. Zorg ervoor dat **MyListener** is geselecteerd voor de listener.
4. Klik op het tabblad **backend-doelen** en selecteer **doel type** als *omleiding*.
5. Voor **type omleiding**, selecteer **permanente**.
6. Voor **doel van omleiding**, selecteer **Listener**.
7. Zorg ervoor dat de **doel-listener** is ingesteld op **appGatewayHttpListener**.
8. Selecteer *Ja*in het deel **reeks query toevoegen** en het **pad include** .
9. Selecteer **Toevoegen**.

## <a name="create-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset om servers op te geven voor de back-endpool in de toepassingsgateway.

1. Selecteer op de portal linkerbovenhoek, **+ een resource maken**.
2. Selecteer **Compute**.
3. Typ in het zoekvak *schaalset* en druk op Enter.
4. Selecteer **virtuele-machineschaalset**, en selecteer vervolgens **maken**.
5. Voor **virtuele-machineschaalset naam**, type *myvmss*.
6. Voor de installatiekopie van besturingssysteemschijf, ** Controleer **Windows Server 2016 Datacenter** is geselecteerd.
7. Voor **resourcegroep**, selecteer **myResourceGroupAG**.
8. Voor **gebruikersnaam**, type *azureuser*.
9. Voor **wachtwoord**, type *Azure123456!* en Bevestig het wachtwoord.
10. Voor **aantal instanties**, zorg ervoor dat de waarde is **2**.
11. Voor **Exemplaargrootte**, selecteer **D2s_v3**.
12. Onder **netwerken**, zorg ervoor dat **kiest u opties voor taakverdeling** is ingesteld op **Application Gateway**.
13. Zorg ervoor dat **Application gateway** is ingesteld op **myAppGateway**.
14. Zorg ervoor dat **Subnet** is ingesteld op **myBackendSubnet**.
15. Selecteer **Maken**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Koppelen van de schaalset met de juiste back-endpool

Gebruikersinterface van de virtuele machine scale set portal maakt een nieuwe back endpool voor de schaalset, maar u wilt koppelen aan uw bestaande appGatewayBackendPool.

1. Open de **myResourceGroupAg** resourcegroep.
2. Selecteer **myAppGateway**.
3. Selecteer **back-endpools**.
4. Selecteer **myAppGatewaymyvmss**.
5. Selecteer **alle doelen verwijderen uit de back-endpool**.
6. Selecteer **Opslaan**.
7. Nadat dit proces is voltooid, selecteert u de **myAppGatewaymyvmss** back-endpool, selecteer **verwijderen** en vervolgens **OK** om te bevestigen.
8. Selecteer **appGatewayBackendPool**.
9. Onder **doelen**, selecteer **VMSS**.
10. Onder **VMSS**, selecteer **myvmss**.
11. Onder **Netwerkinterfaceconfiguraties**, selecteer **myvmssNic**.
12. Selecteer **Opslaan**.

### <a name="upgrade-the-scale-set"></a>Upgrade van de schaalset

Tot slot moet u de schaalset met deze wijzigingen upgraden.

1. Selecteer de **myvmss** schaalset.
2. Onder **instellingen**, selecteer **exemplaren**.
3. Selecteert u beide exemplaren en selecteer vervolgens **Upgrade**.
4. Selecteer **Ja** om te bevestigen.
5. Nadat deze is voltooid, gaat u terug naar de **myAppGateway** en selecteer **back-endpools**. U ziet nu dat de **appGatewayBackendPool** heeft twee doelen en **myAppGatewaymyvmss** heeft nul doelen.
6. Selecteer **myAppGatewaymyvmss**, en selecteer vervolgens **verwijderen**.
7. Selecteer **OK** om te bevestigen.

### <a name="install-iis"></a>IIS installeren

Er is een eenvoudige manier om IIS te installeren op de schaalset met PowerShell. Vanuit de portal, klik op het pictogram van de Cloud Shell en zorg ervoor dat **PowerShell** is geselecteerd.

Plak de volgende code in het PowerShell-venster en druk op Enter.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Upgrade van de schaalset

Na het wijzigen van de exemplaren met IIS, moet u de schaalset met deze wijziging nog een keer te upgraden.

1. Selecteer de **myvmss** schaalset.
2. Onder **instellingen**, selecteer **exemplaren**.
3. Selecteert u beide exemplaren en selecteer vervolgens **Upgrade**.
4. Selecteer **Ja** om te bevestigen.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

De overzichtspagina van application gateway krijgt u het openbare IP-adres van toepassing.

1. Selecteer **myAppGateway**.
2. Op de **overzicht** pagina, houd er rekening mee de IP-adres onder **openbare IP-adres van front-end**.

3. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Bijvoorbeeld: http://52.170.203.149

   ![Beveiligingswaarschuwing](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Voor het accepteren van de beveiligingswaarschuwing als u een zelfondertekend certificaat hebt gebruikt, selecteert u **Details** en vervolgens **Ga verder naar de webpagina**. Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

   ![Basis-URL testen in de toepassingsgateway](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [een toepassingsgateway maken met de interne omleiding](redirect-internal-site-powershell.md).
