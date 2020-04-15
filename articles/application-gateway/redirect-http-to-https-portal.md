---
title: HTTP naar HTTPS-omleiding in portal - Azure Application Gateway
description: Meer informatie over het maken van een toepassingsgateway met omgeleid verkeer van HTTP naar HTTPS met behulp van de Azure-portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: cd33d23a506bd86b9651af3d4c3bbca01673a7a4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312102"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Een toepassingsgateway maken met HTTP naar HTTPS-omleiding met behulp van de Azure-portal

U de Azure-portal gebruiken om een [toepassingsgateway](overview.md) te maken met een certificaat voor TLS-beëindiging. Er wordt een routeringsregel gebruikt om HTTP-verkeer om te leiden naar de HTTPS-poort in uw toepassingsgateway. In dit voorbeeld maakt u ook een [virtuele machineschaalset](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) voor de backendpool van de toepassingsgateway die twee virtuele machine-exemplaren bevat.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een netwerk instellen
> * Een toepassingsgateway maken met behulp van het certificaat
> * Een listener- en omleidingsregel toevoegen
> * Een virtuele-machineschaalset maken met de standaard back-endgroep

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor deze zelfstudie moet de Azure PowerShell-module versie 1.0.0 of hoger een certificaat maken en IIS installeren. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u de opdrachten in deze zelfstudie `Login-AzAccount` wilt uitvoeren, moet u ook worden uitgevoerd om een verbinding met Azure te maken.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor productiegebruik moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider. Voor deze zelfstudie maakt u een zelfondertekend certificaat met behulp van [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). U kunt [Export-PfxCertificate ](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) gebruiken met de Thumbprint die is geretourneerd om een ​​PFX-bestand uit het certificaat te exporteren.

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

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U hebt een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. In dit voorbeeld worden twee subnetten gemaakt: één voor de toepassingsgateway en de andere voor de back-endservers. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt.

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
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
8. Controleer **onder Frontend IP-configuratie**of **ip-adrestype** **Openbaar**is en als **nieuw maken** is geselecteerd. Voer *myAGPublicIPAddress* in voor de naam. Accepteer de standaardwaarden voor de overige instellingen en klik op **OK**.
9. Selecteer **onder Listenerconfiguratie** **HTTPS**, selecteer Vervolgens Selecteer een **bestand selecteren** en navigeer naar het bestand *c:\appgwcert.pfx* en selecteer **Openen**.
10. Typ *appgwcert* voor de cert-naam en *Azure123456!* als het wachtwoord.
11. Laat de firewall van de webtoepassing uitgeschakeld en selecteer **OK**.
12. Controleer de instellingen op de overzichtspagina en selecteer **OK** om de netwerkbronnen en de toepassingsgateway te maken. Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt, wacht tot de implementatie is voltooid voordat u doorgaat naar de volgende sectie.

### <a name="add-a-subnet"></a>Een subnet toevoegen

1. Selecteer **Alle bronnen** in het linkermenu en selecteer vervolgens **myVNet** in de lijst resources.
2. Selecteer **Subnetten**en klik op **Subnet**.

    ![Subnet maken](./media/create-url-route-portal/application-gateway-subnet.png)

3. Typ *myBackendSubnet* voor de naam van het subnet.
4. Typ *10.0.2.0/24* voor het adresbereik en selecteer **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Een listener- en omleidingsregel toevoegen

### <a name="add-the-listener"></a>De listener toevoegen

Voeg eerst de luisteraar met de naam *myListener* toe voor poort 80.

1. Open de **resourcegroep myResourceGroupAG** en selecteer **myAppGateway**.
2. Selecteer **Luisteraars** en selecteer **+ Basic**.
3. Typ *MyListener* voor de naam.
4. Typ *httpPort* voor de nieuwe frontend poortnaam en *80* voor de poort.
5. Controleer of het protocol is ingesteld op **HTTP**en selecteer **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Een routeringsregel toevoegen met een omleidingsconfiguratie

1. Selecteer op **myAppGateway** **Regels** en selecteer **vervolgens de routeringsregel +Verzoek**.
2. Typ *Regel2*voor de **regelnaam**.
3. Zorg ervoor dat **MyListener** is geselecteerd voor de luisteraar.
4. Klik op het tabblad **Backend-doelen** en selecteer **Doeltype** als *Omleiding*.
5. Selecteer **Permanent**voor **type omleiding**.
6. Selecteer **Listener**voor **doel om te leiden**.
7. Controleer of de **doellistener** is ingesteld op **appGatewayHttpListener**.
8. Selecteer *Ja*voor de **querytekenreeks Opnemen** en **Pad opnemen** .
9. Selecteer **Toevoegen**.

## <a name="create-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset om servers op te geven voor de back-endpool in de toepassingsgateway.

1. Selecteer in de linkerbovenhoek van de portal de optie **+Een resource maken**.
2. Selecteer **Compute**.
3. Typ in het zoekvak *de schaalset* en druk op Enter.
4. Selecteer **Virtuele machineschaalset**en selecteer **Vervolgens Maken**.
5. Typ *myvmss*voor de naam van de virtuele **machineschaalset**.
6. Controleer voor de schijfafbeelding van het besturingssysteem** dat **Windows Server 2016-datacenter** is geselecteerd.
7. Selecteer **myResourceGroupAG**voor **resourcegroep**.
8. Typ *Azureuser*voor **gebruikersnaam**.
9. Typ *Azure123456* voor **Wachtwoord!** en bevestig het wachtwoord.
10. Bijvoorbeeld **tellen**, zorg ervoor dat de waarde **2**is .
11. Selecteer **bijvoorbeeld de grootte**van **D2s_v3**.
12. Controleer **onder Netwerken**ervoor dat Opties voor **taakverdeling** kiezen is ingesteld op **Application Gateway**.
13. Ervoor zorgen **dat de toepassingsgateway** is ingesteld op **myAppGateway**.
14. Zorg ervoor dat **Subnet** is ingesteld op **myBackendSubnet**.
15. Selecteer **Maken**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>De schaalset koppelen aan de juiste backendpool

De gebruikersinterface voor de virtuele machineschaalsetset maakt een nieuwe backendpool voor de schaalset, maar u wilt deze koppelen aan uw bestaande appGatewayBackendPool.

1. Open de **resourcegroep myResourceGroupAg.**
2. Selecteer **myAppGateway**.
3. Selecteer **Backend-groepen**.
4. Selecteer **myAppGatewaymyvmss**.
5. Selecteer **Alle doelen uit de backendpool verwijderen**.
6. Selecteer **Opslaan**.
7. Nadat dit proces is voltooid, selecteert u de backendpool **myAppGatewaymyvmss,** selecteert u **Verwijderen** en **vervolgens OK** om te bevestigen.
8. Selecteer **appGatewayBackendPool**.
9. Selecteer **onder Doelen**de optie **VMSS**.
10. Selecteer **myvmss**onder **VMSS**.
11. Selecteer **myvmssNic**onder **Netwerkinterfaceconfiguraties**.
12. Selecteer **Opslaan**.

### <a name="upgrade-the-scale-set"></a>De schaalset upgraden

Ten slotte moet u de schaalset met deze wijzigingen upgraden.

1. Selecteer de **myvms-schaalset.**
2. Selecteer onder **Instellingen** de optie **Instanties**.
3. Selecteer beide instanties en selecteer **Vervolgens Bijwerken**.
4. Selecteer **Ja** om te bevestigen.
5. Ga daarna terug naar de **myAppGateway** en selecteer **Backend-zwembaden.** U moet nu zien dat de **appGatewayBackendPool** twee doelen heeft en **myAppGatewaymyvmss** nul doelen heeft.
6. Selecteer **myAppGatewaymyvmss**en selecteer **Verwijderen**.
7. Selecteer **OK** om te bevestigen.

### <a name="install-iis"></a>IIS installeren

Een eenvoudige manier om IIS op de weegschaal set te installeren is het gebruik van PowerShell. Klik in de portal op het pictogram Cloud Shell en zorg ervoor dat **PowerShell** is geselecteerd.

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

### <a name="upgrade-the-scale-set"></a>De schaalset upgraden

Na het wijzigen van de exemplaren met IIS, moet u opnieuw upgraden van de schaal set met deze wijziging.

1. Selecteer de **myvms-schaalset.**
2. Selecteer onder **Instellingen** de optie **Instanties**.
3. Selecteer beide instanties en selecteer **Vervolgens Bijwerken**.
4. Selecteer **Ja** om te bevestigen.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

U het openbare IP-adres van de toepassing ophalen via de pagina Overzicht van de toepassingsgateway.

1. Selecteer **myAppGateway**.
2. Noteer op de **pagina Overzicht** het IP-adres onder **Frontend public IP-adres**.

3. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Bijvoorbeeld: http://52.170.203.149

   ![Beveiligingswaarschuwing](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Als u de beveiligingswaarschuwing wilt accepteren als u een zelfondertekend certificaat hebt gebruikt, selecteert u **Details** en **gaat u vervolgens naar de webpagina**. Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

   ![Basis-URL testen in de toepassingsgateway](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een toepassingsgateway met interne omleiding](redirect-internal-site-powershell.md).
