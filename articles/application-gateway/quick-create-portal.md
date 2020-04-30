---
title: 'Quick Start: direct webverkeer met behulp van de portal'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van de Azure Portal voor het maken van een Azure-toepassing gateway waarmee webverkeer wordt doorgestuurd naar virtuele machines in een back-end-groep.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 17adc800bd5a2ae53e27350c7e0d588eaeee4a8f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79241400"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Snelstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal

In deze Quick Start gebruikt u de Azure Portal om een toepassings gateway te maken. Vervolgens test u de app om te controleren of deze correct werkt. 

De toepassings gateway stuurt webverkeer van toepassingen naar specifieke bronnen in een back-end-pool. U wijst listeners toe aan poorten, maakt regels en voegt resources toe aan een back-end-groep. Voor het gemak maakt dit artikel gebruik van een eenvoudige configuratie met een openbaar front-end-IP, een basis-listener voor het hosten van één site op de toepassings gateway, een basis regel voor aanvraag Routering en twee virtuele machines in de back-end-pool.

U kunt deze Snelstartgids ook volt ooien met behulp van [Azure PowerShell](quick-create-powershell.md) of [Azure cli](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U maakt de toepassings gateway met behulp van de tabbladen op de pagina **een toepassings gateway maken** .

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **netwerken** en selecteer in de lijst met **Aanbevolen** **Application Gateway** .

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **basis beginselen** deze waarden in voor de volgende instellingen voor de toepassings gateway:

   - **Resource groep**: Selecteer **myResourceGroupAG** voor de resource groep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam van de toepassings gateway**: Voer *myAppGateway* in als de naam van de toepassings gateway.

     ![Nieuwe toepassings gateway maken: basis beginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaande gebruiken. In dit voor beeld maakt u een nieuw virtueel netwerk op het moment dat u de toepassings gateway maakt. Application Gateway exemplaren worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

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
   > Voor de SKU van Application Gateway v2 moet er een **open bare** frontend-IP-configuratie zijn. U kunt nog steeds een open bare en een privé-front-end-IP-configuratie hebben, maar alleen persoonlijke frontend-IP-configuratie (alleen ILB modus) is momenteel niet ingeschakeld voor de v2-SKU. 

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
  
      Accepteer de standaard waarden voor de overige instellingen op het tabblad **listener** en selecteer vervolgens het tabblad **backend-doelen** om de rest van de routerings regel te configureren.

   ![Nieuwe toepassings gateway maken: listener](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Op het tabblad **backend-doelen** selecteert u **MyBackendPool** voor het back- **end-doel**.

5. Voor de **http-instelling**selecteert u **Nieuw maken** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routerings regel. In het venster **een HTTP-instelling toevoegen** dat wordt geopend, voert u *myHTTPSetting* in voor de naam van de **http-instelling** en *80* voor de back- **End-poort**. Accepteer de standaard waarden voor de overige instellingen in het venster **een HTTP-instelling toevoegen** en selecteer vervolgens **toevoegen** om terug te gaan naar het venster een regel voor het **routeren van een route ring toevoegen** . 

     ![Nieuwe toepassings gateway maken: HTTP-instelling](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Selecteer in het venster **een routerings regel toevoegen** de optie **toevoegen** om de routerings regel op te slaan en terug te keren naar het tabblad **configuratie** .

     ![Nieuwe toepassings gateway maken: routerings regel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecteer **volgende: Tags** en vervolgens **volgende: controleren + maken**.

### <a name="review--create-tab"></a>Tabblad controleren en maken

Controleer de instellingen op het tabblad **beoordelen en maken** en selecteer vervolgens **maken** om het virtuele netwerk, het open bare IP-adres en de toepassings gateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-end doelen toevoegen

In dit voor beeld gebruikt u virtuele machines als doel-back-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines als back-endservers voor de toepassings gateway.

Hiervoor gaat u als volgt te werk:

1. Maak twee nieuwe Vm's, *myVM* en *myVM2*, die moeten worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassings gateway is gemaakt.
3. Voeg de back-endservers toe aan de back-end-groep.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Het venster **Nieuw** wordt weergegeven.
2. Selecteer **Windows Server 2016 Data Center** in de lijst **populair** . De pagina **Een virtuele machine maken** wordt weergegeven.<br>Application Gateway kunt verkeer routeren naar elk type virtuele machine dat wordt gebruikt in de back-endadresgroep. In dit voor beeld gebruikt u een Windows Server 2016 Data Center.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resource groep**: Selecteer **myResourceGroupAG** voor de naam van de resource groep.
    - **Naam van virtuele machine**: Voer *myVM* in als de naam van de virtuele machine.
    - **Regio**: Selecteer de regio waarin u de toepassings gateway hebt gemaakt.
    - **Username**: Typ *azureuser* voor de gebruikers naam van de beheerder.
    - **Wacht woord**: Typ een wacht woord.
4. Accepteer de andere standaard waarden en selecteer vervolgens **volgende: schijven**.  
5. Accepteer de standaard instellingen van het tabblad **schijven** en selecteer vervolgens **volgende: netwerken**.
6. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de andere standaard instellingen en selecteer vervolgens **volgende: beheer**.<br>Application Gateway kunt communiceren met exemplaren buiten het virtuele netwerk waarin deze zich bevindt, maar u moet ervoor zorgen dat er een IP-verbinding is.
7. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uit**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
8. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
9. Wacht tot de virtuele machine is gemaakt voordat u verder gaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor testen

In dit voor beeld installeert u IIS op de virtuele machines alleen om te controleren of de toepassings gateway door Azure is gemaakt.

1. Open Azure PowerShell. Selecteer **Cloud shell** in de bovenste navigatie balk van de Azure Portal en selecteer vervolgens **Power shell** in de vervolg keuzelijst. 

    ![Aangepaste extensie installeren](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren. Wijzig indien nodig de *locatie* parameter: 

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

1. Selecteer **alle resources** in het menu Azure portal of zoek naar *alle resources*en selecteer deze. Selecteer vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **myBackendPool**.

4. Selecteer **virtuele machine** in de vervolg keuzelijst onder **back-end doelen**, **doel type**.

5. Onder **doel**selecteert u de virtuele machines **myVM** en **myVM2** en de bijbehorende netwerk interfaces in de vervolg keuzelijst.


   > [!div class="mx-imgBorder"]
   > ![Back-endservers toevoegen](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u verdergaat met de volgende stap.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Hoewel IIS niet is vereist voor het maken van de toepassings gateway, hebt u dit in deze Snelstartgids geïnstalleerd om te controleren of de toepassings gateway is gemaakt met Azure. Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het open bare IP-adres voor de toepassings gateway op de pagina **overzicht** . ![Neem het open bare IP-](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) adres van de toepassings gateway op of u kunt **alle resources**selecteren, *myAGPublicIPAddress* invoeren in het zoekvak en deze vervolgens selecteren in de zoek resultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het open bare IP-adres en plak het in de adres balk van uw browser om door dat IP-adres te bladeren.
3. Controleer het antwoord. Een geldige reactie verifieert of de toepassings gateway is gemaakt en kan verbinding maken met de back-end.

   ![Toepassingsgateway testen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Vernieuw de browser meerdere keren en controleer de verbindingen met zowel myVM als myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met de Application Gateway niet meer nodig hebt, verwijdert u de resource groep. Wanneer u de resource groep verwijdert, verwijdert u ook de toepassings gateway en alle gerelateerde resources.

De resourcegroep verwijderen:

1. Selecteer **resource groepen** in het menu Azure portal of zoek en selecteer *resource groepen*.
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in bij **TYP DE RESOURCEGROEPNAAM** en selecteer vervolgens **Verwijderen**

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)
