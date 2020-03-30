---
title: 'Zelfstudie: Host meerdere websites met de Azure-portal'
titleSuffix: Azure Application Gateway
description: In deze zelfstudie leert u hoe u een toepassingsgateway maakt die meerdere websites host met de Azure-portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: ca6be666a9b77532b4f1c61f6e3391c239e82c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74075155"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Zelfstudie: Een toepassingsgateway maken en configureren om meerdere websites te hosten met de Azure-portal

U de Azure-portal gebruiken om [de hosting van meerdere websites](multiple-site-overview.md) te configureren wanneer u een [toepassingsgateway maakt.](overview.md) In deze zelfstudie definieert u backend-adresgroepen met behulp van virtuele machines. Vervolgens configureert u listeners en regels op basis van domeinen waarvan u eigenaar bent om er zeker van te zijn dat webverkeer bij de juiste servers in de pools binnenkomen. In deze zelfstudie wordt ervan uitgegaan dat u eigenaar bent van meerdere domeinen en voorbeelden gebruikt van *www.contoso.com* en *www.fabrikam.com*.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken
> * Virtuele machines maken voor backendservers
> * Backendpools maken met de backendservers
> * Back-endlisteners maken
> * Routeringsregels maken
> * Een CNAME-record in uw domein maken

![Voorbeeld van routeren voor meerdere sites](./media/create-multiple-sites-portal/scenario.png)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij de Azure-portal op[https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en selecteer **Vervolgens Application Gateway** in de lijst **Aanbevolen.**

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **Basisbeginselen** de volgende waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep:** Selecteer **myResourceGroupAG** voor de resourcegroep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - Naam van de **toepassingsgateway:** Voer *myAppGateway in* voor de naam van de toepassingsgateway.

     ![Nieuwe toepassingsgateway maken: basisbeginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U een nieuw virtueel netwerk maken of een bestaand netwerk gebruiken. In dit voorbeeld maakt u een nieuw virtueel netwerk op hetzelfde moment dat u de toepassingsgateway maakt. Toepassingsgateway-exemplaren worden gemaakt in afzonderlijke subnetten. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

    Selecteer **onder Virtueel netwerk configureren**de optie Nieuw **maken** om een nieuw virtueel netwerk te maken . Voer in **het venster Virtueel netwerk maken** dat wordt geopend de volgende waarden in om het virtuele netwerk en twee subnetten te maken:

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
   > Voor de Application Gateway v2 SKU u alleen **openbare** frontend IP-configuratie kiezen. Private frontend IP-configuratie is momenteel niet ingeschakeld voor deze v2 SKU.

2. Kies **Nieuw maken** voor het **ip-adres openbaar** en voer *myAGPublicIPAddress* in voor de openbare IP-adresnaam en selecteer **VERVOLGENS OK**. 

     ![Nieuwe toepassingsgateway maken: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecteer **Volgende: Backends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De backendpool wordt gebruikt om aanvragen te routeren naar de backendservers die het verzoek dienen. Backend-groepen kunnen NIC's, virtuele machineschaalsets, openbare IP's, interne IP's, volledig gekwalificeerde domeinnamen (FQDN) en back-ends met meerdere tenant's zijn, zoals Azure App Service. In dit voorbeeld maakt u een lege backendpool met uw toepassingsgateway en voegt u backenddoelen toe aan de backendpool.

1. Selecteer op het tabblad **Backends** de optie **+Een backendpool toevoegen**.

2. Voer in het venster **Een backendpool toevoegen** dat wordt geopend, de volgende waarden in om een lege backendpool te maken:

    - **Naam**: Voer *contosoPool* in voor de naam van de backend pool.
    - **Backend pool toevoegen zonder doelen**: Selecteer **Ja** om een backend pool te maken zonder doelen. U voegt backenddoelen toe nadat u de toepassingsgateway hebt hebt hebt maken.

3. Selecteer **Add a backend pool** **Toevoegen** om de backendpoolconfiguratie op te slaan en ga terug naar het tabblad **Backends.**
4. Voeg nu nog een backend pool genaamd *fabrikamPool*.

     ![Nieuwe toepassingsgateway maken: backends](./media/create-multiple-sites-portal/backend-pools.png)

4. Selecteer Op het tabblad **Backends** de optie **Volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **Configuratie** verbindt u de frontend- en backendgroepen die u hebt gemaakt met behulp van een routeringsregel.

1. Selecteer **Een regel toevoegen** in de kolom **Routeringsregels.**

2. Voer in het venster **Een routeringsregel** toevoegen dat wordt geopend, *contosoRegel* in voor de **regelnaam**.

3. Voor een routeringsregel is een listener vereist. Voer op het tabblad **Listener** in het venster **Een routeringsregel toevoegen** de volgende waarden voor de listener in:

    - **Listenernaam**: Voer *contosoListener* in voor de naam van de luisteraar.
    - **Frontend IP**: Selecteer **Openbaar** om het openbare IP te kiezen dat u voor de frontend hebt gemaakt.

   Onder **Aanvullende instellingen:**
   - **Listenertype:** Meerdere sites
   - **Hostnaam**: **www.contoso.com**

   Accepteer de standaardwaarden voor de andere instellingen op het tabblad **Listener** en selecteer vervolgens het tabblad **Backenddoelen** om de rest van de routeringsregel te configureren.

   ![Nieuwe toepassingsgateway maken: listener](./media/create-multiple-sites-portal/routing-rule.png)

4. Selecteer op het tabblad **Backend-doelen** **contosoPool** voor het **backenddoel**.

5. Selecteer Voor de **HTTP-instelling** **Nieuw maken** om een nieuwe HTTP-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routeringsregel. Voer in het **instellingsvenster Een HTTP-instelling toevoegen** dat wordt *geopend, in contosoHTTPInstellen* voor de **http-instellingsnaam**. Accepteer de standaardwaarden voor de andere instellingen in het **instellingsvenster Een HTTP toevoegen** en selecteer **Toevoegen** om terug te keren naar het venster **Een routeringsregel** toevoegen. 

6. Selecteer **toevoegen** om de routeringsregel op te slaan en keer terug naar het tabblad **Configuratie** in het venster **Een routeringsregel** toevoegen.
7. Selecteer **Een regel toevoegen** en voeg een vergelijkbare regel, listener, backenddoel en HTTP-instelling toe voor Fabrikam.

     ![Nieuwe toepassingsgateway maken: routeringsregel](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Selecteer **Volgende: Tags** en vervolgens **Volgende: Controleren + maken**.

### <a name="review--create-tab"></a>Tabblad Controleren + maken

Controleer de instellingen op het tabblad **Controleren + maken** en selecteer **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure.

Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Backenddoelen toevoegen

In dit voorbeeld gebruikt u virtuele machines als doelbackend. U bestaande virtuele machines gebruiken of nieuwe machines maken. U maakt twee virtuele machines die Azure gebruikt als backendservers voor de toepassingsgateway.

Als u backenddoelen wilt toevoegen, gaat u als het gaat om het opzien plaats zet van de tekst:

1. Maak twee nieuwe VM's, *contosoVM* en *fabrikamVM,* om te worden gebruikt als backend servers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de backendservers toe aan de backendpools.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer **Berekenen** en selecteer **Vervolgens Windows Server 2016 Datacenter** in de lijst **Populair.** De pagina **Een virtuele machine maken** wordt weergegeven.<br>Application Gateway kan verkeer routeren naar elk type virtuele machine dat in de backendpool wordt gebruikt. In dit voorbeeld gebruikt u een Windows Server 2016-datacenter.
3. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resourcegroep**: Selecteer **myResourceGroupAG** voor de naam van de resourcegroep.
    - **Naam virtuele machine**: Voer *contosoVM* in voor de naam van de virtuele machine.
    - **Gebruikersnaam**: Voer *azureuser* in voor de gebruikersnaam van de beheerder.
    - **Wachtwoord**: Voer *Azure123456 in!* als beheerderswachtwoord.
4. Accepteer de andere standaardinstellingen en selecteer **Volgende: Schijven**.  
5. Accepteer de standaardinstellingen van het tabblad **Schijven** en selecteer **Volgende: Netwerken**.
6. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de andere standaardinstellingen en selecteer **Volgende: Beheer**.<br>Application Gateway kan communiceren met instanties buiten het virtuele netwerk waarin het zich bevindt, maar u moet ervoor zorgen dat er IP-connectiviteit is.
7. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uit**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
8. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
9. Wacht tot de virtuele machine is gemaakt voordat u verder gaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor tests

In dit voorbeeld installeert u IIS alleen op de virtuele machines om te controleren of Azure de toepassingsgateway heeft gemaakt.

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

3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u eerder hebt voltooid. Gebruik *fabrikamVM* voor de naam van de virtuele machine en voor de **VMName-instelling** van de cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pools"></a>Backendservers toevoegen aan backendpools

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **contosoPool**.

4. Onder **Doelen** selecteert u **Virtuele machine** in de vervolgkeuzelijst.

5. Selecteer **onder virtuele machine-** en **netwerkinterfaces**de **virtuele computer contosoVM** en de bijbehorende netwerkinterface in de vervolgkeuzelijsten.

    ![Back-endservers toevoegen](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Selecteer **Opslaan**.
7. Herhaal dit om de *fabrikamVM* en interface toe te voegen aan de *fabrikamPool.*

Wacht tot de implementatie is voltooid voordat u doorgaat naar de volgende stap.

## <a name="create-a-www-a-record-in-your-domains"></a>Een www A-record maken in uw domeinen

Nadat de toepassingsgateway is gemaakt met het openbare IP-adres, u het IP-adres opvragen en gebruiken om een A-record in uw domeinen te maken. 

1. Klik op **Alle resources**en klik vervolgens op **myAGPublicIPAddress**.

    ![DNS-adres van toepassingsgateway opnemen](./media/create-multiple-sites-portal/public-ip.png)

2. Kopieer het IP-adres en gebruik het als de waarde voor een nieuwe *www* A-record in uw domeinen.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Voer uw domeinnaam in de adresbalk van de browser in. Bijvoorbeeld `http://www.contoso.com`.

    ![Contoso-site testen in toepassingsgateway](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Wijzig het adres in uw andere domein. U krijgt iets te zien zoals in het volgende voorbeeld:

    ![Fabrikam-site testen in toepassingsgateway](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Wanneer u de brongroep verwijdert, verwijdert u ook de toepassingsgateway en alle bijbehorende bronnen.

Ga als volgt te werk om de resourcegroep te verwijderen:

1. Selecteer **Resourcegroepen** in het linkermenu van de Azure-portal.
2. Zoek en selecteer **myResourceGroupAG** in de lijst op de pagina **Resourcegroepen**.
3. Selecteer **Resourcegroep verwijderen** op de **pagina van de resourcegroep**.
4. Voer *myResourceGroupAG* in bij **TYP DE RESOURCEGROEPNAAM** en selecteer vervolgens **Verwijderen**

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over wat u doen met Azure Application Gateway](application-gateway-introduction.md)