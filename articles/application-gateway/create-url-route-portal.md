---
title: 'Zelfstudie: op URL-pad gebaseerde routeringsregels met portal - Azure Application Gateway'
description: In deze zelfstudie leert u hoe u op URL-pad gebaseerde routeringsregels maakt voor een toepassingsgateway en een virtuele machineschaalset met behulp van de Azure-portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 8d48ea133aaabbe9fd44bda545d672e68c93c08d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312203"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Zelfstudie: Een toepassingsgateway maken met routeringsregels op basis van paden met de Azure-portal

U de Azure-portal gebruiken om [op URL-paden gebaseerde routeringsregels](application-gateway-url-route-overview.md) te configureren wanneer u een [toepassingsgateway maakt.](application-gateway-introduction.md) In deze zelfstudie maakt u backendpools met behulp van virtuele machines. Vervolgens maakt u routeringsregels die ervoor zorgen dat webverkeer bij de juiste servers in de groepen terechtkomt.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken
> * Virtuele machines maken voor backendservers
> * Backendpools maken met de backendservers
> * Een backendlistener maken
> * Een routeringsregel op basis van paden maken

![Voorbeeld van URL-routering](./media/application-gateway-create-url-route-portal/scenario.png)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij de Azure-portal op[https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Virtuele machines maken

In dit voorbeeld maakt u drie virtuele machines die kunnen worden gebruikt als backendservers voor de toepassingsgateway. U installeert ook IIS op de virtuele machines om te controleren of de toepassingsgateway werkt zoals verwacht.

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer **Windows Server 2016 Datacenter** in de lijst Populair.
3. Voer deze waarden in voor de virtuele machine:

    - **Resourcegroep**selecteer **Nieuw maken**en typ *myResourceGroupAG*.
    - **Naam van virtuele machine**: *myVM1*
    - **Regio**: *(VS) Oost-VS*
    - **Gebruikersnaam**: *azureuser*
    - **Wachtwoord**: *Azure123456!*


4. Selecteer **Volgende: schijven**.
5. **Volgende:Netwerken selecteren**
6. Selecteer **Voor virtueel netwerk**de optie Nieuw **maken** en typ deze waarden voor het virtuele netwerk:

   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myBackendSubnet* voor de eerste subnetnaam
   - *10.0.1.0/24* - voor de subnetadresruimte.
   - *myAGSubnet* - voor de tweede subnetnaam.
   - *10.0.0.0/24* als de adresruimte van het subnet.
7. Selecteer **OK**.

8. Controleer of **myBackendSubnet** onder **Netwerkinterface**is geselecteerd voor het subnet en selecteer **Volgende: Beheer**.
9. Selecteer **Uit** om opstartdiagnoses uit te schakelen.
10. Klik **op Controleren + Maken,** controleer de instellingen op de overzichtspagina en selecteer Vervolgens **Maken**.
11. Maak nog twee virtuele machines, *myVM2* en *myVM3* en plaats ze in het *virtuele MyVNet-netwerk* en het *subnet myBackendSubnet.*

### <a name="install-iis"></a>IIS installeren

1. Open de interactieve shell en controleer of deze is ingesteld op **PowerShell**.

    ![Aangepaste extensie installeren](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren: 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Maak nog twee virtuele machines en installeer IIS met behulp van de stappen die u net hebt voltooid. Voer de namen van *myVM2* en *myVM3* in voor de namen en voor de waarden van VMName in Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en selecteer **Vervolgens Application Gateway** in de lijst **Aanbevolen.**

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **Basisbeginselen** de volgende waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep:** Selecteer **myResourceGroupAG** voor de resourcegroep.
   - Naam van de **toepassingsgateway:** Voer *myAppGateway in* voor de naam van de toepassingsgateway.
   - **Regio** - Selecteer **(VS) Oost-VS**.

        ![Nieuwe toepassingsgateway maken: basisbeginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Selecteer **myVNet** **onder Virtueel netwerk configureren**voor de naam van het virtuele netwerk.
3. Selecteer **myAGSubnet** voor het subnet.
3. Accepteer de standaardwaarden voor de andere instellingen en selecteer **Volgende: Frontends**.

### <a name="frontends-tab"></a>Tabblad Frontends

1. Controleer op het tabblad **Frontends** of **het IP-adrestype Frontend** is ingesteld op **Openbaar**.

   > [!NOTE]
   > Voor de Application Gateway v2 SKU u alleen **openbare** frontend IP-configuratie kiezen. Private frontend IP-configuratie is momenteel niet ingeschakeld voor deze v2 SKU.

2. Kies **Nieuw maken** voor het **ip-adres openbaar** en voer *myAGPublicIPAddress* in voor de openbare IP-adresnaam en selecteer **VERVOLGENS OK**. 
3. Selecteer **Volgende: Backends**.

### <a name="backends-tab"></a>Tabblad Back-ends

De backendpool wordt gebruikt om aanvragen te routeren naar de backendservers die het verzoek dienen. Backend-groepen kunnen bestaan uit NIC's, virtuele machineschaalsets, openbare IP's, interne IP's, volledig gekwalificeerde domeinnamen (FQDN) en back-ends met meerdere tenant's zoals Azure App Service.

1. Selecteer op het tabblad **Backends** de optie **+Een backendpool toevoegen**.

2. Voer in het venster **Een backendpool toevoegen** dat wordt geopend, de volgende waarden in om een lege backendpool te maken:

    - **Naam**: Voer *myBackendPool* in voor de naam van de backend pool.
3. Selecteer **Virtuele machine** in de vervolgkeuzelijst onder **Backend-doelen**, **doeltype.**

5. Selecteer **onder Doel** de netwerkinterface voor **myVM1**.
6. Selecteer **Toevoegen**.
7. Herhaal dit om een backendpool *voor afbeeldingen* toe te voegen met *myVM2* als doel en een *backendpool voor video's* met *myVM3* als doel.
8. Selecteer **Toevoegen** om de configuratie van de backendpool op te slaan en terug te keren naar het tabblad **Backends.**

4. Selecteer Op het tabblad **Backends** de optie **Volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **Configuratie** verbindt u de frontend- en backendpool die u hebt gemaakt met behulp van een routeringsregel.

1. Selecteer **Een regel toevoegen** in de kolom **Routeringsregels.**

2. Voer in het venster **Een routeringsregel toevoegen** dat wordt geopend, *mijnrouteregel* in voor de **regelnaam**.

3. Voor een routeringsregel is een listener vereist. Voer op het tabblad **Listener** in het venster **Een routeringsregel toevoegen** de volgende waarden voor de listener in:

    - **Listenernaam**: Voer *myListener* in voor de naam van de luisteraar.
    - **Frontend IP**: Selecteer **Openbaar** om het openbare IP te kiezen dat u voor de frontend hebt gemaakt.
    - **Poort**: Type *8080*
  
        Accepteer de standaardwaarden voor de andere instellingen op het tabblad **Listener** en selecteer vervolgens het tabblad **Backenddoelen** om de rest van de routeringsregel te configureren.

4. Selecteer op het tabblad **Backend-doelen** **myBackendPool** voor het **backend-doel**.

5. Selecteer Voor de **HTTP-instelling** **Nieuw maken** om een nieuwe HTTP-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routeringsregel. 

6. Voer in het venster **Een HTTP-instelling toevoegen** dat wordt geopend, *mijnHTTP-instelling* voor de **http-instellingsnaam**in . Accepteer de standaardwaarden voor de andere instellingen in het **instellingsvenster Een HTTP toevoegen** en selecteer **Toevoegen** om terug te keren naar het venster **Een routeringsregel** toevoegen.
7. Selecteer **onder Routeleiden op basis van paden**de optie Meerdere doelen toevoegen om een op paden **gebaseerde regel te maken**.
8. Typ **voor Pad** */afbeeldingen/*\*.
9. Typ *Afbeeldingen*voor **de regelnaam Pad**.
10. Selecteer **mijnhttp-instelling** voor **HTTP-instelling**
11. Selecteer **Afbeeldingen**voor **backend-doel**.
12. Selecteer **Toevoegen** om de padregel op te slaan en terug te keren naar het tabblad **Een routeringsregel toevoegen.**
13. Herhaal dit om een andere regel voor Video toe te voegen.
14. Selecteer **Toevoegen** om de routeringsregel toe te voegen en terug te keren naar het tabblad **Configuratie.**
15. Selecteer **Volgende: Tags** en vervolgens **Volgende: Controleren + maken**.

> [!NOTE]
> U hoeft geen aangepaste */** padregel toe te voegen om standaardaanvragen af te handelen. Dit wordt automatisch afgehandeld door de standaard backendpool.

### <a name="review--create-tab"></a>Tabblad Controleren + maken

Controleer de instellingen op het tabblad **Controleren + maken** en selecteer **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.


## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

    ![Registreer het openbare IP-adres van de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Zoals, http:\//52.188.72.175:8080.

    ![Basis-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   De listener op poort 8080 leidt dit verzoek naar de standaard backendpool.

3. Wijzig de URL in *http://&lt;ip-adres:8080/images/test.htm&gt;*&gt; , ip-adres vervangen &lt;door uw IP-adres en u ziet iets als het volgende voorbeeld:

    ![Afbeeldingen-URL in toepassingsgateway testen](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   De luisteraar op poort 8080 stuurt dit verzoek naar de *Images* backend pool.

4. Wijzig de URL naar *http://&lt;ip-adres:8080/video/test.htm&gt;*&gt; , ip-adres vervangen &lt;door uw IP-adres en u ziet iets als het volgende voorbeeld:

    ![Video-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   De luisteraar op poort 8080 *Video* stuurt dit verzoek naar de videobackendpool.


## <a name="next-steps"></a>Volgende stappen

- [End-to-end TLS inschakelen op Azure Application Gateway](application-gateway-backend-ssl.md)
