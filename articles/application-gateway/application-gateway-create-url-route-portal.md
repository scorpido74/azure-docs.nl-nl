---
title: Maak een toepassings gateway met behulp van op URL-pad gebaseerde routerings regels-Azure Portal
description: Meer informatie over het maken van op URL-pad gebaseerde routerings regels voor een toepassings gateway en een schaalset voor virtuele machines met behulp van de Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: a9809846a8029d819dd847b78dc3c11605ddfc46
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194552"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Een toepassings gateway met op pad gebaseerde routerings regels maken met behulp van de Azure Portal

U kunt de Azure Portal gebruiken om op [URL-pad gebaseerde routerings regels](application-gateway-url-route-overview.md) te configureren wanneer u een [toepassings gateway](application-gateway-introduction.md)maakt. In deze zelf studie maakt u back-endservers met virtuele machines. Vervolgens maakt u routerings regels die ervoor zorgen dat webverkeer binnenkomt op de juiste servers in de groepen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsgateway maken
> * Virtuele machines maken voor back-endservers
> * Back-endservers maken met de back-endservers
> * Een backend-listener maken
> * Een op pad gebaseerde routerings regel maken

![Voorbeeld van URL-routering](./media/application-gateway-create-url-route-portal/scenario.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op [https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Virtuele machines maken

In dit voor beeld maakt u drie virtuele machines die worden gebruikt als back-endservers voor de toepassings gateway. U installeert ook IIS op de virtuele machines om te controleren of de toepassings gateway werkt zoals verwacht.

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer **Windows Server 2016 Data Center** in de lijst populair.
3. Voer deze waarden in voor de virtuele machine:

    - **Resource groep**, selecteer **nieuwe maken**en typ vervolgens *myResourceGroupAG*.
    - **Naam van de virtuele machine**: *myVM1*
    - **Regio**: *VS VS-Oost*
    - **Gebruikers naam**: *azureuser*
    - **Wachtwoord**: *Azure123456!*


4. Selecteer **Volgende: schijven**.
5. Selecteer **volgende: netwerken**
6. Voor **virtueel netwerk**selecteert u **nieuwe maken** en typt u deze waarden voor het virtuele netwerk:

   - *myVnet* als de naam van het virtuele netwerk.
   - *10.0.0.0/16* als de adresruimte van het virtuele netwerk.
   - *myBackendSubnet* voor de eerste subnetnaam
   - *10.0.1.0/24* : voor het subnet een adresruimte.
   - *myAGSubnet* : de naam van het tweede subnet.
   - *10.0.0.0/24* als de adresruimte van het subnet.
7. Selecteer **OK**.

8. Zorg ervoor dat onder **netwerk interface** **myBackendSubnet** is geselecteerd voor het subnet en selecteer **volgende: Beheer**.
9. Selecteer **uitschakelen** om diagnostische gegevens over opstarten uit te scha kelen.
10. Klik op **beoordeling + maken**, Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.
11. Maak twee meer virtuele machines, *myVM2* en *myVM3* en plaats deze in het virtuele *MyVNet* -netwerk en het *myBackendSubnet* -subnet.

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

3. Maak twee meer virtuele machines en Installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Voer de namen van *myVM2* en *myVM3* in voor de namen en voor de waarden van VMName in set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en vervolgens **Application Gateway** in de lijst **Aanbevolen**.

### <a name="basics-tab"></a>Tabblad basis beginselen

1. Voer op het tabblad **basis beginselen** deze waarden in voor de volgende instellingen voor de toepassings gateway:

   - **Resourcegroep**: Selecteer **myResourceGroupAG** als de resourcegroep.
   - **Naam van de toepassings gateway**: Typ *myAppGateway* als naam voor de toepassingsgateway.
   - **Regio** -Selecteer **(VS) vs-Oost**.

        ![Nieuwe toepassings gateway maken: Basics](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Onder **virtueel netwerk configureren**selecteert u **myVNet** voor de naam van het virtuele netwerk.
3. Selecteer **myAGSubnet** voor het subnet.
3. Accepteer de standaard waarden voor de overige instellingen en selecteer **volgende: Front-end.**

### <a name="frontends-tab"></a>Tabblad front-ends

1. Controleer op het tabblad frontends of het **frontend-IP-adres type** is ingesteld op **openbaar**.

   > [!NOTE]
   > Voor de SKU van Application Gateway v2 kunt u alleen de **open bare** frontend-IP-configuratie kiezen. De persoonlijke frontend-IP-configuratie is op dit moment niet ingeschakeld voor deze v2-SKU.

2. Kies **Nieuw maken** voor het **open bare IP-adres** en voer *myAGPublicIPAddress* in als naam voor het open bare IP-adres en selecteer vervolgens **OK**. 
3. Selecteer **Volgende: Back-end.**

### <a name="backends-tab"></a>Tabblad back-ends

De back-end-groep wordt gebruikt voor het routeren van aanvragen naar de back-endservers die de aanvraag behandelen. Back-endservers kunnen bestaan uit Nic's, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service.

1. Selecteer op het tabblad **back** -end **+ een back-end-groep toevoegen**.

2. In het venster **een back-Endadresgroep toevoegen** dat wordt geopend, voert u de volgende waarden in om een lege back-end-groep te maken:

    - **Naam**: Voer *myBackendPool* in als de naam van de back-end-groep.
3. Selecteer **virtuele machine** in de vervolg keuzelijst onder **back-end doelen**, **doel type**.

5. Onder **doel** selecteert u de netwerk interface voor **myVM1**.
6. Selecteer **Toevoegen**.
7. Herhaal de stappen voor het toevoegen van een *installatie kopie* back-end met *myVM2* als doel en een *video* back-end-pool met *myVM3* als doel.
8. Selecteer **toevoegen** om de configuratie van de back-endserver op te slaan en terug te keren naar het tabblad **back-end** .

4. Op het tabblad **back-end** selecteert **u volgende: Configuratie**.

### <a name="configuration-tab"></a>Tabblad Configuratie

Op het tabblad **configuratie** verbindt u de front-end-en back-end-groep die u hebt gemaakt met behulp van een routerings regel.

1. Selecteer **een regel toevoegen** in de kolom **routerings regels** .

2. Voer in het venster **een regel voor de route ring toevoegen** die wordt geopend, *myRoutingRule* in als naam van de **regel**.

3. Een routerings regel vereist een listener. Voer op het tabblad **listener** in het venster **een regel voor de route ring toevoegen** de volgende waarden in voor de listener:

    - **Naam van listener**: Voer *myListener* in als de naam van de listener.
    - **Frontend-IP**: Selecteer **openbaar** om het open bare IP-adres te kiezen dat u hebt gemaakt voor de front-end.
    - **Poort**: Type *8080*
  
        Accepteer de standaard waarden voor de overige instellingen op het tabblad **listener** en selecteer vervolgens het tabblad **backend-doelen** om de rest van de routerings regel te configureren.

4. Op het tabblad **backend-doelen** selecteert u **MyBackendPool** voor het back- **end-doel**.

5. Voor de **http-instelling**selecteert u **Nieuw maken** om een nieuwe http-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routerings regel. 

6. In het venster **een HTTP-instelling toevoegen** dat wordt geopend, voert u *myHTTPSetting* in voor de naam van de **http-instelling**. Accepteer de standaard waarden voor de overige instellingen in het venster **een HTTP-instelling toevoegen** en selecteer vervolgens **toevoegen** om terug te gaan naar het venster een regel voor het routeren van **een route ring toevoegen** .
7. Onder **route ring op basis van paden**selecteert **u meerdere doelen toevoegen om een op pad gebaseerde regel te maken**.
8. Typ */images/* \*bij **pad**.
9. Typ *installatie kopieën*voor de naam van de **padregel**.
10. Selecteer voor **http-instelling** **myHTTPSetting**
11. Selecteer **installatie kopieën**voor het **back-end-doel**.
12. Selecteer **toevoegen** om de padregel op te slaan en terug te keren naar het tabblad **een regel voor de route ring toevoegen** .
13. Herhaal deze stap om een andere regel voor video toe te voegen.
14. Selecteer **toevoegen** om de regel voor door sturen toe te voegen en terug te keren naar het tabblad **configuratie** .
15. Selecteer **Volgende: Tags** en vervolgens **volgende: Bekijk + maken**.

> [!NOTE]
> U hoeft geen aangepaste */* regel * padregel toe te voegen om standaard cases af te handelen. Dit wordt automatisch afgehandeld door de standaard back-end-groep.

### <a name="review--create-tab"></a>Tabblad controleren en maken

Controleer de instellingen op het tabblad **beoordelen en maken** en selecteer vervolgens **maken** om het virtuele netwerk, het open bare IP-adres en de toepassings gateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.


## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

    ![Registreer het openbare IP-adres van de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Zoals http:\//52.188.72.175:8080.

    ![Basis-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   De listener op poort 8080 stuurt deze aanvraag naar de standaard back-end-groep.

3. Wijzig de URL in *http://&lt;IP-adres&gt;: 8080/images/test.htm*, vervang &lt;het IP-&gt; adres door uw IP-adres en u ziet iets als in het volgende voor beeld:

    ![Afbeeldingen-URL in toepassingsgateway testen](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   De listener op poort 8080 routeert deze aanvraag naar de back-end- *installatie kopie* .

4. Wijzig de URL in *http://&lt;IP-adres&gt;: 8080/video/test.htm*, vervang &lt;het IP-&gt; adres door uw IP-adres en u ziet iets als in het volgende voor beeld:

    ![Video-URL testen in de toepassingsgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   De listener op poort 8080 stuurt deze aanvraag naar de back-end van de *video* .


## <a name="next-steps"></a>Volgende stappen

- [End-to-end SSL inschakelen op Azure-toepassing gateway](application-gateway-backend-ssl.md)
