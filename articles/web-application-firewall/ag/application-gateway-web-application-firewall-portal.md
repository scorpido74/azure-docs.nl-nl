---
title: 'Zelf studie: maken met portal-web application firewall'
description: In deze zelf studie leert u hoe u een toepassings gateway met een Web Application Firewall maakt met behulp van de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 68a9f051bf3d59cbf32377cb503e9ded0a54d5e9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74049225"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Zelf studie: een toepassings gateway maken met een firewall voor webtoepassingen met behulp van de Azure Portal

In deze zelf studie wordt uitgelegd hoe u de Azure Portal gebruikt om een Application Gateway te maken met een Web Application firewall (WAF). De WAF gebruikt [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regels om uw toepassing te beveiligen. Deze regels omvatten bescherming tegen aanvallen als SQL-injectie, XSS-aanvallen (cross-site scripting) en sessiekapingen. Nadat u de toepassings gateway hebt gemaakt, test u deze om er zeker van te zijn dat deze correct werkt. Met Azure-toepassing gateway stuurt u het webverkeer van uw toepassing naar specifieke bronnen door listeners toe te wijzen aan poorten, regels te maken en resources toe te voegen aan een back-end-groep. Ter wille van de eenvoud gebruikt deze zelf studie een eenvoudige configuratie met een openbaar front-end-IP, een basis-listener voor het hosten van één site op deze toepassings gateway, twee virtuele machines die worden gebruikt voor de back-end-pool en een regel voor basis routering van aanvragen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken met WAF ingeschakeld
> * De virtuele machines maken die als back-endservers worden gebruikt
> * Een opslagaccount maken en diagnostische gegevens configureren
> * De toepassingsgateway testen

![Voorbeeld van een WAF (Web Application Firewall)](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Voor Azure om te communiceren tussen resources, heeft het een virtueel netwerk nodig. U kunt een nieuw virtueel netwerk maken of een bestaande gebruiken. In dit voor beeld maakt u een nieuw virtueel netwerk. U kunt een virtueel netwerk maken op hetzelfde moment dat u de toepassingsgateway maakt. Application Gateway exemplaren worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

Selecteer **netwerken** en selecteer in de lijst met **Aanbevolen** **Application Gateway** .

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **basis beginselen** deze waarden in voor de volgende instellingen voor de toepassings gateway:

   - **Resource groep**: Selecteer **myResourceGroupAG** voor de resource groep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam van de toepassings gateway**: Voer *myAppGateway* in als de naam van de toepassings gateway.
   - **Laag**: Selecteer **WAF v2**.

     ![Nieuwe toepassings gateway maken: basis beginselen](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaande gebruiken. In dit voor beeld maakt u een nieuw virtueel netwerk op het moment dat u de toepassings gateway maakt. Application Gateway exemplaren worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    Maak onder **virtueel netwerk configureren**een nieuw virtueel netwerk door **Nieuw maken**te selecteren. In het venster **virtueel netwerk maken** dat wordt geopend, voert u de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

    - **Naam**: Voer *myVNet* in als de naam van het virtuele netwerk.

    - **Subnetnaam** (Application Gateway subnet): in het raster **subnetten** wordt een subnet weer gegeven met de naam *standaard*. Wijzig de naam van dit subnet in *myAGSubnet*.<br>Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.

    - **Subnetnaam** (subnet van de back-endserver): in de tweede rij van het raster **subnetten** voert u *myBackendSubnet* in de kolom **subnet name** in.

    - **Adres bereik** (subnet van de back-endserver): Voer in de tweede rij van het raster **subnetten** een adres bereik in dat niet overlapt met het adres bereik van *myAGSubnet*. Als het adres bereik van *myAGSubnet* bijvoorbeeld 10.0.0.0/24 is, voert u *10.0.1.0/24* in voor het adres bereik van *myBackendSubnet*.

    Selecteer **OK** om het venster **virtueel netwerk maken** te sluiten en de instellingen voor het virtuele netwerk op te slaan.

     ![Nieuwe toepassings gateway maken: virtueel netwerk](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Accepteer op het tabblad **basis beginselen** de standaard waarden voor de overige instellingen en selecteer vervolgens **volgende:** front-ends.

### <a name="frontends-tab"></a>Tabblad front-ends

1. Controleer op het tabblad **frontends** of het **frontend-IP-adres type** is ingesteld op **openbaar**. <br>U kunt de frontend-IP zo configureren dat deze openbaar of privé is volgens uw use-case. In dit voor beeld kiest u een openbaar frontend-IP.
   > [!NOTE]
   > Voor de SKU van Application Gateway v2 kunt u alleen de **open bare** frontend-IP-configuratie kiezen. De persoonlijke frontend-IP-configuratie is op dit moment niet ingeschakeld voor deze v2-SKU.

2. Kies **Nieuw maken** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in als naam voor het open bare IP-adres en selecteer vervolgens **OK**. 

     ![Nieuwe toepassings gateway maken: front-end](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Selecteer **volgende: back-end**.

### <a name="backends-tab"></a>Tabblad back-ends

De back-end-groep wordt gebruikt voor het routeren van aanvragen naar de back-endservers die de aanvraag behandelen. Back-endservers kunnen bestaan uit Nic's, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voor beeld maakt u een lege back-end-pool met uw toepassings gateway en voegt u vervolgens de back-endservers toe aan de back-end-groep.

1. Selecteer op het tabblad **back** -end **+ een back-end-groep toevoegen**.

2. In het venster **een back-Endadresgroep toevoegen** dat wordt geopend, voert u de volgende waarden in om een lege back-end-groep te maken:

    - **Naam**: Voer *myBackendPool* in als de naam van de back-end-groep.
    - **Back-end-pool toevoegen zonder doelen**: Selecteer **Ja** om een back-end-groep met geen doelen te maken. U voegt back-endservers toe nadat u de toepassings gateway hebt gemaakt.

3. Selecteer in het venster **een back-Endadresgroep toevoegen** de optie **toevoegen** om de back-endadresgroep op te slaan en terug te keren naar het tabblad **back-end** .

     ![Nieuwe toepassings gateway maken: back-end](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. Op het tabblad **back-end** selecteert u **volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **configuratie** verbindt u de front-end-en back-end-groep die u hebt gemaakt met behulp van een routerings regel.

1. Selecteer **een regel toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **een regel voor de route ring toevoegen** die wordt geopend, *myRoutingRule* in als naam van de **regel**.

3. Een routerings regel vereist een listener. Voer op het tabblad **listener** in het venster **een regel voor de route ring toevoegen** de volgende waarden in voor de listener:

    - **Naam van listener**: Voer *myListener* in als de naam van de listener.
    - **Frontend-IP**: Selecteer **openbaar** om het open bare IP-adres te kiezen dat u hebt gemaakt voor de front-end.
  
      Accepteer de standaard waarden voor de overige instellingen op het tabblad **listener** en selecteer vervolgens het tabblad **backend-doelen** om de rest van de routerings regel te configureren.

   ![Nieuwe toepassings gateway maken: listener](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. Op het tabblad **backend-doelen** selecteert u **MyBackendPool** voor het back- **end-doel**.

5. Voor de **http-instelling**selecteert u **Nieuw maken** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routerings regel. In het venster **een HTTP-instelling toevoegen** dat wordt geopend, voert u *myHTTPSetting* in voor de naam van de **http-instelling**. Accepteer de standaard waarden voor de overige instellingen in het venster **een HTTP-instelling toevoegen** en selecteer vervolgens **toevoegen** om terug te gaan naar het venster een regel voor het **routeren van een route ring toevoegen** . 

     ![Nieuwe toepassings gateway maken: HTTP-instelling](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. Selecteer in het venster **een routerings regel toevoegen** de optie **toevoegen** om de routerings regel op te slaan en terug te keren naar het tabblad **configuratie** .

     ![Nieuwe toepassings gateway maken: routerings regel](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Selecteer **volgende: Tags** en vervolgens **volgende: controleren + maken**.

### <a name="review--create-tab"></a>Tabblad controleren en maken

Controleer de instellingen op het tabblad **beoordelen en maken** en selecteer vervolgens **maken** om het virtuele netwerk, het open bare IP-adres en de toepassings gateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. 

Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-end doelen toevoegen

In dit voor beeld gebruikt u virtuele machines als doel-back-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines die door Azure worden gebruikt als back-endservers voor de toepassings gateway.

Hiervoor gaat u als volgt te werk:

1. Maak twee nieuwe Vm's, *myVM* en *myVM2*, die moeten worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassings gateway is gemaakt.
3. Voeg de back-endservers toe aan de back-end-groep.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer **Windows Server 2016 Data Center** in de lijst **populair** . De pagina **Een virtuele machine maken** wordt weergegeven.<br>Application Gateway kunt verkeer routeren naar elk type virtuele machine dat wordt gebruikt in de back-endadresgroep. In dit voor beeld gebruikt u een Windows Server 2016 Data Center.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resource groep**: Selecteer **myResourceGroupAG** voor de naam van de resource groep.
    - **Naam van virtuele machine**: Voer *myVM* in als de naam van de virtuele machine.
    - **Gebruikers**naam: Voer *azureuser* in als de gebruikernaam van de beheerder.
    - **Wacht woord**: Voer *Azure123456!* als beheerderswachtwoord.
4. Accepteer de andere standaard waarden en selecteer vervolgens **volgende: schijven**.  
5. Accepteer de standaard instellingen van het tabblad **schijven** en selecteer vervolgens **volgende: netwerken**.
6. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de andere standaard instellingen en selecteer vervolgens **volgende: beheer**.<br>Application Gateway kunt communiceren met exemplaren buiten het virtuele netwerk waarin deze zich bevindt, maar u moet ervoor zorgen dat er een IP-verbinding is.
7. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uit**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
8. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
9. Wacht tot de virtuele machine is gemaakt voordat u verder gaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor testen

In dit voor beeld installeert u IIS op de virtuele machines alleen om te controleren of de toepassings gateway door Azure is gemaakt.

1. Open [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Hiertoe selecteert u **Cloud Shell** in de bovenste navigatiebalk van de Azure-portal en vervolgens **PowerShell** in de vervolgkeuzelijst. 

    ![Aangepaste extensie installeren](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

    ![Back-endservers toevoegen](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u verdergaat met de volgende stap.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Een opslagaccount maken en diagnostische gegevens configureren

### <a name="create-a-storage-account"></a>Create a storage account

Voor dit artikel maakt de toepassings gateway gebruik van een opslag account voor het opslaan van gegevens voor detectie en preventie. U kunt ook Azure Monitor-logboeken of Event Hub gebruiken om gegevens vast te leggen.

1. Selecteer **een resource maken** in de linkerbovenhoek van de Azure Portal.
1. Selecteer **opslag**en selecteer vervolgens **opslag account**.
1. Voor *resource groep*selecteert u **myResourceGroupAG** voor de resource groep.
1. Typ *myagstore1* voor de naam van het opslag account.
1. Accepteer de standaard waarden voor de overige instellingen en selecteer vervolgens **controleren + maken**.
1. Controleer de instellingen en selecteer vervolgens **Maken**.

### <a name="configure-diagnostics"></a>Diagnostische gegevens configureren

Configureer diagnostische gegevens om gegevens vast te leggen in de logboeken ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog en ApplicationGatewayFirewallLog.

1. Selecteer in het menu links **alle resources**en selecteer vervolgens *myAppGateway*.
2. Onder bewaking selecteert u **instellingen voor diagnostische gegevens**.
3. Selecteer **instelling diagnostische gegevens toevoegen**.
4. Voer *myDiagnosticsSettings* in als de naam voor de diagnostische instellingen.
5. Selecteer **archiveren naar een opslag account**, selecteer **configureren** om het *myagstore1* -opslag account te selecteren dat u eerder hebt gemaakt, en selecteer vervolgens **OK**.
6. Selecteer de logboeken van de toepassings gateway die u wilt verzamelen en blijven gebruiken.
7. Selecteer **Opslaan**.

    ![Diagnostische gegevens configureren](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Een firewall beleid voor webtoepassingen maken en koppelen

Alle WAF-aanpassingen en-instellingen bevinden zich in een afzonderlijk object, een WAF-beleid genoemd. Het beleid moet worden gekoppeld aan uw Application Gateway. Zie [een WAF-beleid maken](create-waf-policy-ag.md)voor het maken van een WAF-beleid. Zodra de app is gemaakt, kunt u het beleid aan uw WAF (of een afzonderlijke listener) koppelen vanuit het WAF-beleid op het tabblad **gekoppelde toepassings gateways** . 

![Gekoppelde toepassings gateways](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Hoewel IIS niet is vereist voor het maken van de toepassings gateway, hebt u het geïnstalleerd om te controleren of de toepassings gateway is gemaakt met Azure. Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het open bare IP-adres voor de toepassings gateway op de pagina **overzicht** . ![Openbaar IP-adres van toepassings gateway opnemen](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   U kunt ook **alle resources**selecteren, *myAGPublicIPAddress* invoeren in het zoekvak en deze vervolgens selecteren in de zoek resultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
1. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.
1. Controleer het antwoord. Een geldige reactie verifieert of de toepassings gateway is gemaakt en kan verbinding maken met de back-end.

   ![Toepassingsgateway testen](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Als u de resourcegroep verwijdert, worden ook de toepassingsgateway en alle gerelateerde resources verwijderd. 

Ga als volgt te werk om de resourcegroep te verwijderen:

1. Selecteer **Resourcegroepen** in het linkermenu van de Azure-portal.
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in bij **Typ de naam van de resource groep** en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Web Application firewall](../overview.md)
