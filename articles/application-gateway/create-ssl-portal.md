---
title: 'Zelfstudie: TLS-beëindiging configureren in portal - Azure Application Gateway'
description: In deze zelfstudie leert u hoe u een toepassingsgateway configureert en een certificaat voor TLS-beëindiging toevoegt met behulp van de Azure-portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: b40eb107fc975d2ef0170944892b936680de3c9f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312377"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Zelfstudie: Een toepassingsgateway configureren met TLS-beëindiging met behulp van de Azure-portal

U de Azure-portal gebruiken om een [toepassingsgateway](overview.md) te configureren met een certificaat voor TLS-beëindiging dat virtuele machines voor backendservers gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een zelfondertekend certificaat maken
> * Een toepassingsgateway maken met behulp van het certificaat
> * De virtuele machines maken die als backendservers worden gebruikt
> * De toepassingsgateway testen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij de Azure-portal op[https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

In deze sectie gebruikt u [Nieuw-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) om een zelfondertekend certificaat te maken. U uploadt het certificaat naar de Azure-portal wanneer u de listener voor de toepassingsgateway maakt.

Open op uw lokale computer een Windows PowerShell-venster als beheerder. Voer de volgende opdracht uit om het certificaat te maken:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Je zou zoiets als dit antwoord moeten zien:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Gebruik [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) met de duimafdruk die is geretourneerd om een pfx-bestand van het certificaat te exporteren:

> [!NOTE]
> Gebruik geen speciale tekens in uw .pfx-bestandswachtwoord. Alleen alfanumerieke tekens worden ondersteund.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

1. Selecteer **Een resource maken** in het linkermenu van de Azure-portal. Het venster **Nieuw** wordt weergegeven.

2. Selecteer **Netwerken** en selecteer **Vervolgens Application Gateway** in de lijst **Aanbevolen.**

### <a name="basics-tab"></a>Tabblad Basisinformatie

1. Voer op het tabblad **Basisbeginselen** de volgende waarden in voor de volgende toepassingsgateway-instellingen:

   - **Resourcegroep:** Selecteer **myResourceGroupAG** voor de resourcegroep. Als deze nog niet bestaat, selecteert u **Nieuwe maken** om deze te maken.
   - Naam van de **toepassingsgateway:** Voer *myAppGateway in* voor de naam van de toepassingsgateway.

        ![Nieuwe toepassingsgateway maken: basisbeginselen](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt. U een nieuw virtueel netwerk maken of een bestaand netwerk gebruiken. In dit voorbeeld maakt u een nieuw virtueel netwerk op hetzelfde moment dat u de toepassingsgateway maakt. Toepassingsgateway-exemplaren worden gemaakt in afzonderlijke subnetten. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers.

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
   > Voor de Application Gateway v2 SKU u alleen **openbare** frontend IP-configuratie kiezen. Private frontend IP-configuratie is momenteel niet ingeschakeld voor deze v2 SKU.

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
    - **Protocol:** Selecteer **HTTPS**.
    - **Poort**: Controleer of er 443 is ingevoerd voor de poort.

   Onder **HTTPS-certificaat:**

   - **PFX-certificaatbestand** - Blader naar en selecteer het c:\appgwcert.pfx-bestand dat u eerder hebt gemaakt.
   - **Certificaatnaam** - Typ *mycert1* voor de naam van het certificaat.
   - **Wachtwoord** - Typ *Azure123456* voor het wachtwoord.
  
        Accepteer de standaardwaarden voor de andere instellingen op het tabblad **Listener** en selecteer vervolgens het tabblad **Backenddoelen** om de rest van de routeringsregel te configureren.

   ![Nieuwe toepassingsgateway maken: listener](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Selecteer op het tabblad **Backend-doelen** **myBackendPool** voor het **backend-doel**.

5. Selecteer Voor de **HTTP-instelling** **Nieuw maken** om een nieuwe HTTP-instelling te maken. De HTTP-instelling bepaalt het gedrag van de routeringsregel. Voer in het venster **Een HTTP-instelling toevoegen** dat wordt geopend, *mijnHTTP-instelling* voor de **http-instellingsnaam**in . Accepteer de standaardwaarden voor de andere instellingen in het **instellingsvenster Een HTTP toevoegen** en selecteer **Toevoegen** om terug te keren naar het venster **Een routeringsregel** toevoegen. 

   ![Nieuwe toepassingsgateway maken: HTTP-instelling](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. Selecteer **toevoegen** om de routeringsregel op te slaan en keer terug naar het tabblad **Configuratie** in het venster **Een routeringsregel** toevoegen.

   ![Nieuwe toepassingsgateway maken: routeringsregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecteer **Volgende: Tags** en vervolgens **Volgende: Controleren + maken**.

### <a name="review--create-tab"></a>Tabblad Controleren + maken

Controleer de instellingen op het tabblad **Controleren + maken** en selecteer **Maken** om het virtuele netwerk, het openbare IP-adres en de toepassingsgateway te maken. Het kan enkele minuten duren om de toepassingsgateway te maken in Azure. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie.

## <a name="add-backend-targets"></a>Backenddoelen toevoegen

In dit voorbeeld gebruikt u virtuele machines als doelbackend. U bestaande virtuele machines gebruiken of nieuwe machines maken. U maakt twee virtuele machines die Azure gebruikt als backendservers voor de toepassingsgateway.

Om dit te doen, zul je:

1. Maak twee nieuwe VM's, *myVM* en *myVM2,* om te worden gebruikt als backend servers.
2. Installeer IIS op de virtuele machines om te controleren of de toepassingsgateway is gemaakt.
3. Voeg de backendservers toe aan de backendpool.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **Een resource maken** in de Azure-portal. Het venster **Nieuw** wordt weergegeven.
2. Selecteer **Windows Server 2016 Datacenter** in de lijst **Populair.** De pagina **Een virtuele machine maken** wordt weergegeven.

   Application Gateway kan verkeer routeren naar elk type virtuele machine dat in de backendpool wordt gebruikt. In dit voorbeeld gebruikt u een Windows Server 2016-datacenter.

1. Voer deze waarden in op het tabblad **Basisinformatie** voor de volgende instellingen voor de virtuele machine:

    - **Resourcegroep**: Selecteer **myResourceGroupAG** voor de naam van de resourcegroep.
    - **Naam van virtuele machine**: Voer *myVM* in voor de naam van de virtuele machine.
    - **Gebruikersnaam**: Voer *azureuser* in voor de gebruikersnaam van de beheerder.
    - **Wachtwoord**: Voer *Azure123456* in voor het beheerderswachtwoord.
4. Accepteer de andere standaardinstellingen en selecteer **Volgende: Schijven**.  
5. Accepteer de standaardinstellingen van het tabblad **Schijven** en selecteer **Volgende: Netwerken**.
6. Zorg ervoor dat, op het tabblad **Netwerken**, **myVNet** is geselecteerd bij **Virtueel netwerk** en dat **Subnet** is ingesteld op **myBackendSubnet**. Accepteer de andere standaardinstellingen en selecteer **Volgende: Beheer**.

   Application Gateway kan communiceren met instanties buiten het virtuele netwerk waarin het zich bevindt, maar u moet ervoor zorgen dat er IP-connectiviteit is.
1. Op het tabblad **Beheer** stelt u **Diagnostische gegevens over opstarten** in op **Uit**. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
2. Controleer de instellingen op het tabblad **Beoordelen en maken**, corrigeer eventuele validatiefouten en selecteer vervolgens **Maken**.
3. Wacht tot de implementatie is voltooid voordat u verdergaat.

### <a name="install-iis-for-testing"></a>IIS installeren voor tests

In dit voorbeeld installeert u IIS alleen op de virtuele machines om te controleren of Azure de toepassingsgateway heeft gemaakt.

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

3. Maak een tweede virtuele machine en installeer IIS met behulp van de stappen die u zojuist hebt voltooid. Gebruik *myVM2* voor de naam van de virtuele machine en voor de **VMName-instelling** van de cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Backend-servers toevoegen aan backendpool

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Back-endpools** in het linkermenu.

3. Selecteer **myBackendPool**.

4. Onder **Doelen** selecteert u **Virtuele machine** in de vervolgkeuzelijst.

5. Onder **VIRTUELE MACHINE** en **NETWERKINTERFACES** selecteert u de virtuele machines **myVM** en **myVM2** en de bijbehorende netwerkinterfaces in de vervolgkeuzelijsten.

    ![Back-endservers toevoegen](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecteer **Opslaan**.

7. Wacht tot de implementatie is voltooid voordat u doorgaat naar de volgende stap.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

1. Selecteer **Alle resources**en selecteer vervolgens **myAGPublicIPAddress**.

    ![Registreer het openbare IP-adres van de toepassingsgateway](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Typ in de adresbalk van uw browser *https://\<ip-adres\>van de toepassingsgateway.*

   Als u de beveiligingswaarschuwing wilt accepteren als u een zelfondertekend certificaat hebt gebruikt, selecteert u **Details** (of **Geavanceerd** in Chrome) en gaat u vervolgens naar de webpagina:

    ![Beveiligingswaarschuwing](./media/create-ssl-portal/application-gateway-secure.png)

    Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

    ![Basis-URL testen in de toepassingsgateway](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over TLS-ondersteuning voor Application Gateway](ssl-overview.md)
