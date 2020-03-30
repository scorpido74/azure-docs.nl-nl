---
title: 'Snelstart: direct webverkeer via de portal'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van de Azure-portal om een Azure Application Gateway te maken die webverkeer naar virtuele machines in een backendpool leidt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 17adc800bd5a2ae53e27350c7e0d588eaeee4a8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241400"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Snelstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal

In deze quickstart gebruikt u de Azure-portal om een toepassingsgateway te maken. Dan test je het om ervoor te zorgen dat het goed werkt. 

De toepassingsgateway leidt het webverkeer van toepassingen naar specifieke bronnen in een backend-groep. U wijst luisteraars toe aan poorten, maakt regels en voegt resources toe aan een backendpool. Omwille van de eenvoud, dit artikel maakt gebruik van een eenvoudige setup met een openbare front-end IP, een fundamentele luisteraar om een enkele site host op de applicatie gateway, een basisaanvraag routing regel, en twee virtuele machines in de backend pool.

U deze quickstart ook voltooien met [Azure PowerShell](quick-create-powershell.md) of [Azure CLI.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U maakt de toepassingsgateway met de tabbladen op de pagina **Een toepassingsgateway maken.**

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en selecteer **Vervolgens Application Gateway** in de lijst **Aanbevolen.**

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **Basisbeginselen** de volgende waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep:** Selecteer **myResourceGroupAG** voor de resourcegroep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - Naam van de **toepassingsgateway:** Voer *myAppGateway in* voor de naam van de toepassingsgateway.

     ![Nieuwe toepassingsgateway maken: basisbeginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U een nieuw virtueel netwerk maken of een bestaand netwerk gebruiken. In dit voorbeeld maakt u een nieuw virtueel netwerk op hetzelfde moment dat u de toepassingsgateway maakt. Toepassingsgateway-exemplaren worden gemaakt in afzonderlijke subnetten. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    Maak **onder Virtueel netwerk configureren**een nieuw virtueel netwerk door Nieuw maken **te**selecteren . Voer in **het venster Virtueel netwerk maken** dat wordt geopend de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

    - **Naam**: Voer *myVNet* in voor de naam van het virtuele netwerk.

    - **Subnetnaam** (Subnet Application Gateway): op het **subnetraster** wordt een subnet weergegeven met de naam *Standaard*. Wijzig de naam van dit subnet in *myAGSubnet*.<br>Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.

    - **Subnetnaam** (subnet van de backendserver): voer in de tweede rij van het **subnetraster** *myBackendSubnet* in de kolom **Subnetnaam** in.

    - **Adresbereik** (subnet van backendserver): voer in de tweede rij van het **subnetnetraster** een adresbereik in dat niet overlapt met het adresbereik van *myAGSubnet*. Als het adresbereik van *myAGSubnet* bijvoorbeeld 10.0.0.0/24 is, voert u *10.0.1.0/24* in voor het adresbereik van *myBackendSubnet*.

    Selecteer **OK** om het **venster Virtueel netwerk maken** te sluiten en de instellingen voor virtueel netwerk op te slaan.

     ![Nieuwe toepassingsgateway maken: virtueel netwerk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Accepteer op het tabblad **Basisbeginselen** de standaardwaarden voor de andere instellingen en selecteer **Volgende: Frontends**.

### <a name="frontends-tab"></a>Tabblad Frontends

1. Controleer op het tabblad **Frontends** of **het IP-adrestype Frontend** is ingesteld op **Openbaar**. <br>U het Frontend IP configureren als openbaar of privé volgens uw use case. In dit voorbeeld kiest u een openbaar frontend-ip.
   > [!NOTE]
   > Voor de Application Gateway v2 SKU moet er een **IP-configuratie** voor het publiek zijn. U nog steeds zowel een openbare als een private ip-configuratie voor frontend hebben, maar alleen frontend IP-configuratie voor privé (alleen iLB-modus) is momenteel niet ingeschakeld voor de v2 SKU. 

2. Kies **Nieuw maken** voor het **ip-adres openbaar** en voer *myAGPublicIPAddress* in voor de openbare IP-adresnaam en selecteer **VERVOLGENS OK**. 

     ![Nieuwe toepassingsgateway maken: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecteer **Volgende: Backends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De backendpool wordt gebruikt om aanvragen te routeren naar de backendservers die het verzoek dienen. Backend-groepen kunnen bestaan uit NIC's, virtuele machineschaalsets, openbare IP's, interne IP's, volledig gekwalificeerde domeinnamen (FQDN) en back-ends met meerdere tenant's zoals Azure App Service. In dit voorbeeld maakt u een lege backendpool met uw toepassingsgateway en voegt u backenddoelen toe aan de backendpool.

1. Selecteer op het tabblad **Backends** de optie **+Een backendpool toevoegen**.

2. Voer in het venster **Een backendpool toevoegen** dat wordt geopend, de volgende waarden in om een lege backendpool te maken:

    - **Naam**: Voer *myBackendPool* in voor de naam van de backend pool.
    - **Backend pool toevoegen zonder doelen**: Selecteer **Ja** om een backend pool te maken zonder doelen. U voegt backenddoelen toe nadat u de toepassingsgateway hebt hebt hebt maken.

3. Selecteer **Add a backend pool** **Toevoegen** om de backendpoolconfiguratie op te slaan en ga terug naar het tabblad **Backends.**

     ![Nieuwe toepassingsgateway maken: backends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Selecteer Op het tabblad **Backends** de optie **Volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **Configuratie** verbindt u de frontend- en backendpool die u hebt gemaakt met behulp van een routeringsregel.

1. Selecteer **Een regel toevoegen** in de kolom **Routeringsregels.**

2. Voer in het venster **Een routeringsregel toevoegen** dat wordt geopend, *mijnrouteregel* in voor de **regelnaam**.

3. Voor een routeringsregel is een listener vereist. Voer op het tabblad **Listener** in het venster **Een routeringsregel toevoegen** de volgende waarden voor de listener in:

    - **Listenernaam**: Voer *myListener* in voor de naam van de luisteraar.
    - **Frontend IP**: Selecteer **Openbaar** om het openbare IP te kiezen dat u voor de frontend hebt gemaakt.
  
      Accepteer de standaardwaarden voor de andere instellingen op het tabblad **Listener** en selecteer vervolgens het tabblad **Backenddoelen** om de rest van de routeringsregel te configureren.

   ![Nieuwe toepassingsgateway maken: listener](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Selecteer op het tabblad **Backend-doelen** **myBackendPool** voor het **backend-doel**.

5. Selecteer Voor de **HTTP-instelling** **Nieuw maken** om een nieuwe HTTP-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routeringsregel. Voer in het **instellingsvenster Een HTTP-instelling toevoegen** dat wordt geopend, *mijnHTTP-instelling* in voor de **http-instellingsnaam** en *80* voor de **backend-poort**. Accepteer de standaardwaarden voor de andere instellingen in het **instellingsvenster Een HTTP toevoegen** en selecteer **Toevoegen** om terug te keren naar het venster **Een routeringsregel** toevoegen. 

     ![Nieuwe toepassingsgateway maken: HTTP-instelling](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Selecteer **toevoegen** om de routeringsregel op te slaan en keer terug naar het tabblad **Configuratie** in het venster **Een routeringsregel** toevoegen.

     ![Nieuwe toepassingsgateway maken: routeringsregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecteer **Volgende: Tags** en vervolgens **Volgende: Controleren + maken**.

### <a name="review--create-tab"></a>Tabblad Controleren + maken

Controleer de instellingen op het tabblad **Controleren + maken** en selecteer **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Backenddoelen toevoegen

In dit voorbeeld gebruikt u virtuele machines als doelbackend. U bestaande virtuele machines gebruiken of nieuwe machines maken. U maakt twee virtuele machines als backendservers voor de toepassingsgateway.

Om dit te doen, zul je:

1. Maak twee nieuwe VM's, *myVM* en *myVM2,* om te worden gebruikt als backend servers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de backendservers toe aan de backendpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Het venster **Nieuw** wordt weergegeven.
2. Selecteer **Windows Server 2016 Datacenter** in de lijst **Populair.** De pagina **Een virtuele machine maken** wordt weergegeven.<br>Application Gateway kan verkeer routeren naar elk type virtuele machine dat in de backendpool wordt gebruikt. In dit voorbeeld gebruikt u een Windows Server 2016-datacenter.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resourcegroep**: Selecteer **myResourceGroupAG** voor de naam van de resourcegroep.
    - **Naam van virtuele machine**: Voer *myVM* in voor de naam van de virtuele machine.
    - **Regio**: Selecteer hetzelfde gebied waar u de toepassingsgateway hebt gemaakt.
    - **Gebruikersnaam**: Typ *azureuser* voor de gebruikersnaam van de beheerder.
    - **Wachtwoord:** Typ een wachtwoord.
4. Accepteer de andere standaardinstellingen en selecteer **Volgende: Schijven**.  
5. Accepteer de standaardinstellingen van het tabblad **Schijven** en selecteer **Volgende: Netwerken**.
6. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de andere standaardinstellingen en selecteer **Volgende: Beheer**.<br>Application Gateway kan communiceren met instanties buiten het virtuele netwerk waarin het zich bevindt, maar u moet ervoor zorgen dat er IP-connectiviteit is.
7. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uit**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
8. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
9. Wacht tot de virtuele machine is gemaakt voordat u verder gaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor tests

In dit voorbeeld installeert u IIS alleen op de virtuele machines om te controleren of Azure de toepassingsgateway heeft gemaakt.

1. Open Azure PowerShell. Selecteer **Cloud Shell** in de bovenste navigatiebalk van de Azure-portal en selecteer Vervolgens **PowerShell** in de vervolgkeuzelijst. 

    ![Aangepaste extensie installeren](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren. Wijzig indien nodig de parameter *Locatie:* 

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

3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Gebruik *myVM2* voor de naam van de virtuele machine en voor de **VMName-instelling** van de cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Backend-servers toevoegen aan backendpool

1. Selecteer in het menu van de Azure-portal **Alle bronnen** of zoek naar alle bronnen en selecteer *Alle bronnen*. Selecteer vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **myBackendPool**.

4. Selecteer **Virtuele machine** onder **Backend-doelen**, **doeltype**, in de vervolgkeuzelijst.

5. Selecteer **onder Target**de virtuele machines **myVM** en **myVM2** en de bijbehorende netwerkinterfaces in de vervolgkeuzelijsten.


   > [!div class="mx-imgBorder"]
   > ![Back-endservers toevoegen](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u doorgaat naar de volgende stap.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Hoewel IIS niet vereist is om de toepassingsgateway te maken, hebt u deze in deze snelstart geïnstalleerd om te controleren of Azure de toepassingsgateway heeft gemaakt. Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het openbare IP-adres voor de toepassingsgateway op de **pagina Overzicht.** ![Openbare IP-adres](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) van de toepassingsgateway opnemen Of u **Alle bronnen**selecteren, *myAGPublicIPAddress* invoeren in het zoekvak en het vervolgens selecteren in de zoekresultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het openbare IP-adres en plak het vervolgens in de adresbalk van uw browser om door dat IP-adres te bladeren.
3. Controleer het antwoord. Een geldig antwoord controleert of de toepassingsgateway is gemaakt en kan met de backend verbinding maken.

   ![Toepassingsgateway testen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Vernieuw de browser meerdere keren en u ziet verbindingen met zowel myVM als myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de toepassingsgateway hebt gemaakt, niet meer nodig hebt, verwijdert u de brongroep. Wanneer u de brongroep verwijdert, verwijdert u ook de toepassingsgateway en alle gerelateerde bronnen.

De resourcegroep verwijderen:

1. Selecteer **resourcegroepen** in het menu Van de Azure-portal of zoek naar *resourcegroepen*en selecteer deze .
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in bij **TYP DE RESOURCEGROEPNAAM** en selecteer vervolgens **Verwijderen**

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)
