---
title: 'Zelf studie: meerdere websites hosten met behulp van de Azure Portal'
titleSuffix: Azure Application Gateway
description: In deze zelf studie leert u hoe u een toepassings gateway maakt die als host fungeert voor meerdere websites met behulp van de Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: ca6be666a9b77532b4f1c61f6e3391c239e82c91
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74075155"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Zelf studie: een toepassings gateway maken en configureren om meerdere websites te hosten met behulp van de Azure Portal

U kunt de Azure Portal gebruiken om [het hosten van meerdere websites te configureren](multiple-site-overview.md) wanneer u een [toepassings gateway](overview.md)maakt. In deze zelf studie definieert u back-end-adres groepen met behulp van virtuele machines. Vervolgens configureert u listeners en regels op basis van domeinen waarvan u eigenaar bent om er zeker van te zijn dat webverkeer bij de juiste servers in de pools binnenkomen. In deze zelfstudie wordt ervan uitgegaan dat u eigenaar bent van meerdere domeinen en voorbeelden gebruikt van *www.contoso.com* en *www.fabrikam.com*.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken
> * Virtuele machines maken voor back-endservers
> * Back-endservers maken met de back-endservers
> * Back-endlisteners maken
> * Routeringsregels maken
> * Een CNAME-record in uw domein maken

![Voorbeeld van routeren voor meerdere sites](./media/create-multiple-sites-portal/scenario.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op[https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **netwerken** en selecteer in de lijst met **Aanbevolen** **Application Gateway** .

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **basis beginselen** deze waarden in voor de volgende instellingen voor de toepassings gateway:

   - **Resource groep**: Selecteer **myResourceGroupAG** voor de resource groep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - **Naam van de toepassings gateway**: Voer *myAppGateway* in als de naam van de toepassings gateway.

     ![Nieuwe toepassings gateway maken: basis beginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U kunt een nieuw virtueel netwerk maken of een bestaande gebruiken. In dit voor beeld maakt u een nieuw virtueel netwerk op het moment dat u de toepassings gateway maakt. Application Gateway exemplaren worden in afzonderlijke subnetten gemaakt. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    Onder **virtueel netwerk configureren**selecteert u **nieuwe maken** om een nieuw virtueel netwerk te maken. In het venster **virtueel netwerk maken** dat wordt geopend, voert u de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

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

De back-end-groep wordt gebruikt voor het routeren van aanvragen naar de back-endservers die de aanvraag behandelen. Back-endservers kunnen Nic's zijn, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voor beeld maakt u een lege back-end-pool met uw toepassings gateway en voegt u vervolgens de back-endservers toe aan de back-end-groep.

1. Selecteer op het tabblad **back** -end **+ een back-end-groep toevoegen**.

2. In het venster **een back-Endadresgroep toevoegen** dat wordt geopend, voert u de volgende waarden in om een lege back-end-groep te maken:

    - **Naam**: Voer *contosoPool* in als de naam van de back-end-groep.
    - **Back-end-pool toevoegen zonder doelen**: Selecteer **Ja** om een back-end-groep met geen doelen te maken. U voegt back-endservers toe nadat u de toepassings gateway hebt gemaakt.

3. Selecteer in het venster **een back-Endadresgroep toevoegen** de optie **toevoegen** om de back-endadresgroep op te slaan en terug te keren naar het tabblad **back-end** .
4. Voeg nu nog een back-end-groep toe met de naam *fabrikamPool*.

     ![Nieuwe toepassings gateway maken: back-end](./media/create-multiple-sites-portal/backend-pools.png)

4. Op het tabblad **back-end** selecteert u **volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **configuratie** verbindt u de front-end-en back-endservers die u hebt gemaakt met behulp van een routerings regel.

1. Selecteer **een regel toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **een regel voor de route ring toevoegen** die wordt geopend, *contosoRule* in als naam van de **regel**.

3. Een routerings regel vereist een listener. Voer op het tabblad **listener** in het venster **een regel voor de route ring toevoegen** de volgende waarden in voor de listener:

    - **Naam van listener**: Voer *contosoListener* in als de naam van de listener.
    - **Frontend-IP**: Selecteer **openbaar** om het open bare IP-adres te kiezen dat u hebt gemaakt voor de front-end.

   Onder **aanvullende instellingen**:
   - **Type listener**: meerdere sites
   - **Hostnaam**: **www.contoso.com**

   Accepteer de standaard waarden voor de overige instellingen op het tabblad **listener** en selecteer vervolgens het tabblad **backend-doelen** om de rest van de routerings regel te configureren.

   ![Nieuwe toepassings gateway maken: listener](./media/create-multiple-sites-portal/routing-rule.png)

4. Op het tabblad **backend-doelen** selecteert u **ContosoPool** voor het back- **end-doel**.

5. Voor de **http-instelling**selecteert u **Nieuw maken** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routerings regel. In het venster **een HTTP-instelling toevoegen** dat wordt geopend, voert u *contosoHTTPSetting* in voor de naam van de **http-instelling**. Accepteer de standaard waarden voor de overige instellingen in het venster **een HTTP-instelling toevoegen** en selecteer vervolgens **toevoegen** om terug te gaan naar het venster een regel voor het **routeren van een route ring toevoegen** . 

6. Selecteer in het venster **een routerings regel toevoegen** de optie **toevoegen** om de routerings regel op te slaan en terug te keren naar het tabblad **configuratie** .
7. Selecteer **een regel toevoegen** en voeg een soort gelijke regel, listener, back-end en http-instelling voor fabrikam toe.

     ![Nieuwe toepassings gateway maken: routerings regel](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Selecteer **volgende: Tags** en vervolgens **volgende: controleren + maken**.

### <a name="review--create-tab"></a>Tabblad controleren en maken

Controleer de instellingen op het tabblad **beoordelen en maken** en selecteer vervolgens **maken** om het virtuele netwerk, het open bare IP-adres en de toepassings gateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure.

Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Back-end doelen toevoegen

In dit voor beeld gebruikt u virtuele machines als doel-back-end. U kunt bestaande virtuele machines gebruiken of nieuwe maken. U maakt twee virtuele machines die door Azure worden gebruikt als back-endservers voor de toepassings gateway.

Voor het toevoegen van back-end-doelen voert u de volgende handelingen uit:

1. Maak twee nieuwe Vm's, *contosoVM* en *fabrikamVM*, die moeten worden gebruikt als back-endservers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassings gateway is gemaakt.
3. Voeg de back-endservers toe aan de back-endservers.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer **Compute** en selecteer vervolgens **Windows Server 2016 Data Center** in de lijst **populair** . De pagina **Een virtuele machine maken** wordt weergegeven.<br>Application Gateway kunt verkeer routeren naar elk type virtuele machine dat wordt gebruikt in de back-endadresgroep. In dit voor beeld gebruikt u een Windows Server 2016 Data Center.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resource groep**: Selecteer **myResourceGroupAG** voor de naam van de resource groep.
    - **Naam van virtuele machine**: Voer *contosoVM* in als de naam van de virtuele machine.
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

    ![Aangepaste extensie installeren](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Maak een tweede virtuele machine en Installeer IIS met behulp van de stappen die u eerder hebt voltooid. Gebruik *fabrikamVM* voor de naam van de virtuele machine en voor de instelling **VMName** van de cmdlet **set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pools"></a>Back-endservers toevoegen aan back-end-Pools

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **contosoPool**.

4. Onder **Doelen** selecteert u **Virtuele machine** in de vervolgkeuzelijst.

5. Onder **virtuele machine** en **netwerk interfaces**selecteert u de virtuele machine **contosoVM** en de netwerk interface is gekoppeld aan de vervolg keuzelijst.

    ![Back-endservers toevoegen](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Selecteer **Opslaan**.
7. Herhaal dit om de *fabrikamVM* en-interface toe te voegen aan de *fabrikamPool*.

Wacht tot de implementatie is voltooid voordat u verdergaat met de volgende stap.

## <a name="create-a-www-a-record-in-your-domains"></a>Een www A-record maken in uw domeinen

Nadat de toepassings gateway is gemaakt met het open bare IP-adres, kunt u het IP-adres ophalen en gebruiken om een A-record in uw domeinen te maken. 

1. Klik op **alle resources**en klik vervolgens op **myAGPublicIPAddress**.

    ![DNS-adres van de toepassings gateway opnemen](./media/create-multiple-sites-portal/public-ip.png)

2. Kopieer het IP-adres en gebruik dit als de waarde voor een nieuwe *www* a-record in uw domeinen.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Voer uw domeinnaam in de adresbalk van de browser in. Bijvoorbeeld `http://www.contoso.com`.

    ![Contoso-site testen in toepassingsgateway](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Wijzig het adres in uw andere domein. U krijgt iets te zien zoals in het volgende voorbeeld:

    ![Fabrikam-site testen in toepassingsgateway](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Wanneer u de resource groep verwijdert, verwijdert u ook de toepassings gateway en alle gerelateerde resources.

Ga als volgt te werk om de resourcegroep te verwijderen:

1. Selecteer **Resourcegroepen** in het linkermenu van de Azure-portal.
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in bij **TYP DE RESOURCEGROEPNAAM** en selecteer vervolgens **Verwijderen**

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over wat u kunt doen met Azure-toepassing gateway](application-gateway-introduction.md)