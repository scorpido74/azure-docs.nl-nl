---
title: 'Zelf studie: TLS-beëindiging configureren in portal-Azure-toepassing gateway'
description: In deze zelf studie leert u hoe u een toepassings gateway kunt configureren en een certificaat voor het beëindigen van TLS kunt toevoegen met behulp van de Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: victorh
ms.openlocfilehash: 62f5375a0d468f5b137c4628c89c802d83dee102
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82024488"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Zelf studie: een toepassings gateway met TLS-beëindiging configureren met behulp van de Azure Portal

U kunt de Azure Portal gebruiken om een [toepassings gateway](overview.md) te configureren met een certificaat voor het beëindigen van TLS waarbij virtuele machines worden gebruikt voor back-endservers.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een toepassingsgateway maken met behulp van het certificaat
> * De virtuele machines maken die als back-endservers worden gebruikt
> * De toepassingsgateway testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op[https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

In deze sectie gebruikt u [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) om een zelfondertekend certificaat te maken. U uploadt het certificaat naar de Azure Portal wanneer u de listener voor de toepassings gateway maakt.

Open een Windows Power shell-venster als beheerder op uw lokale computer. Voer de volgende opdracht uit om het certificaat te maken:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

U ziet iets als dit antwoord:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Gebruik [export-pfx](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) met de vinger afdruk die is geretourneerd voor het exporteren van een pfx-bestand uit het certificaat. Zorg ervoor dat uw wacht woord 4-12 tekens lang is:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **netwerken** en selecteer in de lijst met **Aanbevolen** **Application Gateway** .

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **basis beginselen** deze waarden in voor de volgende instellingen voor de toepassings gateway:

   - **Resource groep**: Selecteer **myResourceGroupAG** voor de resource groep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam van de toepassings gateway**: Voer *myAppGateway* in als de naam van de toepassings gateway.

        ![Nieuwe toepassings gateway maken: basis beginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaande gebruiken. In dit voor beeld maakt u een nieuw virtueel netwerk op het moment dat u de toepassings gateway maakt. Application Gateway exemplaren worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    Maak onder **virtueel netwerk configureren**een nieuw virtueel netwerk door **Nieuw maken**te selecteren. In het venster **virtueel netwerk maken** dat wordt geopend, voert u de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

    - **Naam**: Voer *myVNet* in als de naam van het virtuele netwerk.

    - **Subnetnaam** (Application Gateway subnet): in het raster **subnetten** wordt een subnet weer gegeven met de naam *standaard*. Wijzig de naam van dit subnet in *myAGSubnet*.<br>Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.

    - **Subnetnaam** (subnet van de back-endserver): in de tweede rij van het raster **subnetten** voert u *myBackendSubnet* in de kolom **subnet name** in.

    - **Adres bereik** (subnet van de back-endserver): Voer in de tweede rij van het raster **subnetten** een adres bereik in dat niet overlapt met het adres bereik van *myAGSubnet*. Als het adres bereik van *myAGSubnet* bijvoorbeeld 10.0.0.0/24 is, voert u *10.0.1.0/24* in voor het adres bereik van *myBackendSubnet*.

    Selecteer **OK** om het venster **virtueel netwerk maken** te sluiten en de instellingen voor het virtuele netwerk op te slaan.

    ![Nieuwe toepassings gateway maken: virtueel netwerk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Accepteer op het tabblad **basis beginselen** de standaard waarden voor de overige instellingen en selecteer vervolgens **volgende:** front-ends.

### <a name="frontends-tab"></a>Tabblad front-ends

1. Controleer op het tabblad **frontends** of het **frontend-IP-adres type** is ingesteld op **openbaar**. <br>U kunt de frontend-IP zo configureren dat deze openbaar of privé is volgens uw use-case. In dit voor beeld kiest u een openbaar frontend-IP.
   > [!NOTE]
   > Voor de SKU van Application Gateway v2 kunt u alleen de **open bare** frontend-IP-configuratie kiezen. De persoonlijke frontend-IP-configuratie is op dit moment niet ingeschakeld voor deze v2-SKU.

2. Kies **Nieuw maken** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in als naam voor het open bare IP-adres en selecteer vervolgens **OK**. 

   ![Nieuwe toepassings gateway maken: front-end](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecteer **volgende: back-end**.

### <a name="backends-tab"></a>Tabblad back-ends

De back-end-groep wordt gebruikt voor het routeren van aanvragen naar de back-endservers die de aanvraag behandelen. Back-endservers kunnen bestaan uit Nic's, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voor beeld maakt u een lege back-end-pool met uw toepassings gateway en voegt u vervolgens de back-endservers toe aan de back-end-groep.

1. Selecteer op het tabblad **back** -end **+ een back-end-groep toevoegen**.

2. In het venster **een back-Endadresgroep toevoegen** dat wordt geopend, voert u de volgende waarden in om een lege back-end-groep te maken:

    - **Naam**: Voer *myBackendPool* in als de naam van de back-end-groep.
    - **Back-end-pool toevoegen zonder doelen**: Selecteer **Ja** om een back-end-groep met geen doelen te maken. U voegt back-endservers toe nadat u de toepassings gateway hebt gemaakt.

3. Selecteer in het venster **een back-Endadresgroep toevoegen** de optie **toevoegen** om de back-endadresgroep op te slaan en terug te keren naar het tabblad **back-end** .

   ![Nieuwe toepassings gateway maken: back-end](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Op het tabblad **back-end** selecteert u **volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **configuratie** verbindt u de front-end-en back-end-groep die u hebt gemaakt met behulp van een routerings regel.

1. Selecteer **een regel toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **een regel voor de route ring toevoegen** die wordt geopend, *myRoutingRule* in als naam van de **regel**.

3. Een routerings regel vereist een listener. Voer op het tabblad **listener** in het venster **een regel voor de route ring toevoegen** de volgende waarden in voor de listener:

    - **Naam van listener**: Voer *myListener* in als de naam van de listener.
    - **Frontend-IP**: Selecteer **openbaar** om het open bare IP-adres te kiezen dat u hebt gemaakt voor de front-end.
    - **Protocol**: Selecteer **https**.
    - **Poort**: controleer of 443 is opgegeven voor de poort.

   Onder **HTTPS-certificaat**:

   - **PFX-certificaat bestand** : Blader naar en selecteer het c:\appgwcert.pfx-bestand dat u eerder hebt gemaakt.
   - **Certificaat naam** : Typ *mycert1* voor de naam van het certificaat.
   - **Wacht woord** : Typ uw wacht woord.
  
        Accepteer de standaard waarden voor de overige instellingen op het tabblad **listener** en selecteer vervolgens het tabblad **backend-doelen** om de rest van de routerings regel te configureren.

   ![Nieuwe toepassings gateway maken: listener](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Op het tabblad **backend-doelen** selecteert u **MyBackendPool** voor het back- **end-doel**.

5. Voor de **http-instelling**selecteert u **Nieuw maken** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routerings regel. In het venster **een HTTP-instelling toevoegen** dat wordt geopend, voert u *myHTTPSetting* in voor de naam van de **http-instelling**. Accepteer de standaard waarden voor de overige instellingen in het venster **een HTTP-instelling toevoegen** en selecteer vervolgens **toevoegen** om terug te gaan naar het venster een regel voor het **routeren van een route ring toevoegen** . 

   ![Nieuwe toepassings gateway maken: HTTP-instelling](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. Selecteer in het venster **een routerings regel toevoegen** de optie **toevoegen** om de routerings regel op te slaan en terug te keren naar het tabblad **configuratie** .

   ![Nieuwe toepassings gateway maken: routerings regel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecteer **volgende: Tags** en vervolgens **volgende: controleren + maken**.

### <a name="review--create-tab"></a>Tabblad controleren en maken

Controleer de instellingen op het tabblad **beoordelen en maken** en selecteer vervolgens **maken** om het virtuele netwerk, het open bare IP-adres en de toepassings gateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-end doelen toevoegen

In dit voor beeld gebruikt u virtuele machines als doel-back-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines die door Azure worden gebruikt als back-endservers voor de toepassings gateway.

Hiervoor gaat u als volgt te werk:

1. Maak twee nieuwe Vm's, *myVM* en *myVM2*, die moeten worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassings gateway is gemaakt.
3. Voeg de back-endservers toe aan de back-end-groep.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer **Windows Server 2016 Data Center** in de lijst **populair** . De pagina **Een virtuele machine maken** wordt weergegeven.

   Application Gateway kunt verkeer routeren naar elk type virtuele machine dat wordt gebruikt in de back-endadresgroep. In dit voor beeld gebruikt u een Windows Server 2016 Data Center.

1. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resource groep**: Selecteer **myResourceGroupAG** voor de naam van de resource groep.
    - **Naam van virtuele machine**: Voer *myVM* in als de naam van de virtuele machine.
    - **Gebruikers**naam: Voer *azureuser* in als de gebruikernaam van de beheerder.
    - **Wacht woord**: Voer een wacht woord in voor het beheerders account.
1. Accepteer de andere standaard waarden en selecteer vervolgens **volgende: schijven**.  
2. Accepteer de standaard instellingen van het tabblad **schijven** en selecteer vervolgens **volgende: netwerken**.
3. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de andere standaard instellingen en selecteer vervolgens **volgende: beheer**.

   Application Gateway kunt communiceren met exemplaren buiten het virtuele netwerk waarin deze zich bevindt, maar u moet ervoor zorgen dat er een IP-verbinding is.
1. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uit**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
2. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
3. Wacht tot de implementatie is voltooid voordat u doorgaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor testen

In dit voor beeld installeert u IIS op de virtuele machines alleen om te controleren of de toepassings gateway door Azure is gemaakt.

1. Open [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Hiertoe selecteert u **Cloud Shell** in de bovenste navigatiebalk van de Azure-portal en vervolgens **PowerShell** in de vervolgkeuzelijst. 

    ![Aangepaste extensie installeren](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location EastUS
   ```

3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Gebruik *myVM2* voor de naam van de virtuele machine en voor de instelling **VMName** van de cmdlet **set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pool"></a>Back-endservers toevoegen aan back-end-groep

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **myBackendPool**.

4. Onder **Doelen** selecteert u **Virtuele machine** in de vervolgkeuzelijst.

5. Onder **VIRTUELE MACHINE** en **NETWERKINTERFACES** selecteert u de virtuele machines **myVM** en **myVM2** en de bijbehorende netwerkinterfaces in de vervolgkeuzelijsten.

    ![Back-endservers toevoegen](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u verdergaat met de volgende stap.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Selecteer **alle resources**en selecteer vervolgens **myAGPublicIPAddress**.

    ![Registreer het openbare IP-adres van de toepassingsgateway](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Typ in de adres balk van uw browser *https://\<uw IP-adres\>van de toepassings gateway*.

   Als u de beveiligings waarschuwing wilt accepteren als u een zelfondertekend certificaat hebt gebruikt, selecteert u **Details** (of **Geavanceerd** op Chrome) en gaat u naar de webpagina:

    ![Beveiligingswaarschuwing](./media/create-ssl-portal/application-gateway-secure.png)

    Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

    ![Basis-URL testen in de toepassingsgateway](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Application Gateway TLS-ondersteuning](ssl-overview.md)
