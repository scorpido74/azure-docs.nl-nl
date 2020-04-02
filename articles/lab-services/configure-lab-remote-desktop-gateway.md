---
title: Een lab configureren om Extern bureaublad-gateway te gebruiken in Azure DevTest Labs
description: Meer informatie over het configureren van een lab in Azure DevTest Labs met een externe desktopgateway om veilige toegang tot de vm's van het lab te garanderen zonder de RDP-poort bloot te stellen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2cdafa9a36a5f906151ca6946e18ef82bc7f1e01
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529428"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Uw lab configureren in Azure DevTest Labs om een externe desktopgateway te gebruiken
In Azure DevTest Labs u een externe desktopgateway voor uw lab configureren om veilige toegang tot de virtuele machines van het lab te garanderen zonder dat u de RDP-poort hoeft bloot te leggen. Het lab biedt een centrale plek voor uw labgebruikers om alle virtuele machines waartoe ze toegang hebben te bekijken en te verbinden. Met de knop **Verbinding** maken op de pagina **Virtuele machine** wordt een machinespecifiek RDP-bestand gemaakt dat u openen om verbinding te maken met de machine. U de RDP-verbinding verder aanpassen en beveiligen door uw lab te verbinden met een externe desktopgateway. 

Deze aanpak is veiliger omdat de labgebruiker zich rechtstreeks bij de gatewaymachine verifieert of bedrijfsreferenties op een gatewaymachine met domeinverbinding kan gebruiken om verbinding te maken met hun machines. Het lab ondersteunt ook het gebruik van tokenauthenticatie naar de gatewaymachine waarmee gebruikers verbinding kunnen maken met hun virtuele machines in het lab zonder dat de RDP-poort wordt blootgesteld aan het internet. In dit artikel wordt een voorbeeld gegeven over het instellen van een lab dat tokenverificatie gebruikt om verbinding te maken met labmachines.

## <a name="architecture-of-the-solution"></a>Architectuur van de oplossing

![Architectuur van de oplossing](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. De actie [RdP-bestandsinhoud opbrengen](/rest/api/dtl/virtualmachines/getrdpfilecontents) wordt aangeroepen wanneer u de knop **Verbinding maken** selecteert.1. 
1. De actie RdP-bestandsinhoud `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` downloaden roept een verificatietoken aan.
    1. `{gateway-hostname}`is de gatewayhostnaam die is opgegeven op de pagina **Lab-instellingen** voor uw lab in de Azure-portal. 
    1. `{lab-machine-name}`is de naam van de machine die u probeert aan te sluiten.
    1. `{port-number}`is de haven waarop de verbinding moet worden gemaakt. Meestal is deze poort 3389. Als de lab-VM de [gedeelde IP-functie](devtest-lab-shared-ip.md) in DevTest Labs gebruikt, zal de poort anders zijn.
1. De externe desktopgateway stelt `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` de aanroep uit naar een Azure-functie om het verificatietoken te genereren. De DevTest Labs-service bevat automatisch de functiesleutel in de aanvraagheader. De functiesleutel moet worden opgeslagen in de sleutelkluis van het lab. De naam voor dat geheim wordt weergegeven als **Gateway-tokengeheim** op de pagina **Lab-instellingen** voor het lab.
1. De Azure-functie zal naar verwachting een token voor tokenverificatie op basis van certificaten retourneren tegen de gatewaymachine.  
1. Met de actie RdP-bestandsinhoud downloaden retourneert u het volledige RDP-bestand, inclusief de verificatiegegevens.
1. U opent het RDP-bestand met het gewenste RDP-verbindingsprogramma. Vergeet niet dat niet alle RDP-verbindingsprogramma's tokenverificatie ondersteunen. Het verificatietoken heeft wel een vervaldatum, ingesteld door de functie-app. Maak de verbinding met de lab-VM voordat het token verloopt.
1. Zodra de externe desktopgatewaymachine het token in het RDP-bestand verifieert, wordt de verbinding doorgestuurd naar uw labmachine.

### <a name="solution-requirements"></a>Oplossingsvereisten
Om te werken met de devTest Labs-tokenverificatiefunctie, zijn er een aantal configuratievereisten voor de gatewaymachines, dns-domeinnaamservices en -functies.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Vereisten voor extern bureaublad-gatewaymachines
- TLS/SSL-certificaat moet op de gatewaymachine worden geïnstalleerd om HTTPS-verkeer te verwerken. Het certificaat moet overeenkomen met de volledig gekwalificeerde domeinnaam (FQDN) van de load balancer voor het gatewayfarm of de FQDN van de machine zelf als er slechts één machine is. Tls/SSL-certificaten met een wildkaart werken niet.  
- Een ondertekeningscertificaat dat is geïnstalleerd op gatewaymachine(s). Maak een ondertekeningscertificaat met het script [Create-SigningCertificate.ps1.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)
- Installeer de [module Pluggable Authentication](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) die tokenverificatie voor de extern bureaublad-gateway ondersteunt. Een voorbeeld van een `RDGatewayFedAuth.msi` dergelijke module is dat wordt geleverd met [System Center Virtual Machine Manager (VMM) beelden.](/system-center/vmm/install-console?view=sc-vmm-1807) Zie Documentatie en [prijsdetails](https://www.microsoft.com/cloud-platform/system-center-pricing) [van het Systeemcentrum](https://docs.microsoft.com/system-center/) voor meer informatie over Systeemcentrum.  
- De gatewayserver kan aanvragen `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`verwerken die zijn gedaan aan .

    De gateway-hostname is de FQDN van de load balancer van de gateway farm of de FQDN van de machine zelf als er maar één machine is. Het `{lab-machine-name}` is de naam van de lab machine die `{port-number}` u probeert aan te sluiten, en de is poort waarop de verbinding zal worden gemaakt.  Standaard is deze poort 3389.  Als de virtuele machine echter de [gedeelde IP-functie](devtest-lab-shared-ip.md) in DevTest Labs gebruikt, zal de poort anders zijn.
- De module [Aanvraag voor toepassingsroutering](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) voor Internet Information `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` Server (IIS) kan worden gebruikt om aanvragen om te leiden naar de azure-functie, die het verzoek om een token voor verificatie verwerkt.


## <a name="requirements-for-azure-function"></a>Vereisten voor azure-functie
Azure-functie verwerkt aanvraag `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` met indeling van en retourneert het verificatietoken op basis van hetzelfde ondertekeningscertificaat dat is geïnstalleerd op de gatewaymachines. Het `{function-app-uri}` is de uri die wordt gebruikt om toegang te krijgen tot de functie. De functiesleutel wordt automatisch doorgegeven in de koptekst van de aanvraag. Zie [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)voor een voorbeeldfunctie . 


## <a name="requirements-for-network"></a>Vereisten voor het netwerk

- DNS voor de FQDN die is gekoppeld aan het TLS/SSL-certificaat dat op de gatewaymachines is geïnstalleerd, moet verkeer naar de gatewaymachine of de load balancer van de gatewaymachinefarm leiden.
- Als de labmachine privé-IP's gebruikt, moet er een netwerkpad zijn van de gatewaymachine naar de labmachine, hetzij door hetzelfde virtuele netwerk te delen of door peered virtuele netwerken te gebruiken.

## <a name="configure-the-lab-to-use-token-authentication"></a>Het lab configureren om tokenverificatie te gebruiken 
In deze sectie ziet u hoe u een lab configureert om een extern bureaublad-gatewaymachine te gebruiken die tokenverificatie ondersteunt. In deze sectie wordt niet geopperd op het instellen van een extern bureaublad-gatewayfarm zelf. Zie het voorbeeld om aan het einde van dit artikel een extern [bureaublad-gatewaygedeelte te maken](#sample-to-create-a-remote-desktop-gateway) voor die informatie. 

Voordat u de labinstellingen bijwerkt, slaat u de sleutel op die nodig is om de functie met succes uit te voeren om een verificatietoken terug te sturen in de sleutelkluis van het lab. U de waarde van de functiesleutel op de pagina **Beheren** voor de functie in de Azure-portal opvragen. Zie [Een geheim toevoegen aan Key Vault voor](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)meer informatie over het opslaan van een geheim in een sleutelkluis. Bewaar de naam van het geheim voor later gebruik.

Voer de volgende opdracht Azure CLI uit om de id van de sleutelkluis van het lab te vinden: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configureer het lab om de tokenverificatie te gebruiken met behulp van de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met labs uw **lab.**
1. Selecteer **configuratie en beleid**op de pagina van het lab.
1. Selecteer in het linkermenu in de sectie **Instellingen** de optie **Lab-instellingen**.
1. Voer in de sectie **Extern bureaublad** de volledig gekwalificeerde domeinnaam (FQDN) of het IP-adres van de gatewaymachine voor extern bureaublad-services of de farm voor het veld **Gateway hostname** in. Deze waarde moet overeenkomen met de FQDN van het TLS/SSL-certificaat dat wordt gebruikt op gatewaymachines.

    ![Opties voor extern bureaublad in labinstellingen](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Voer in de sectie **Extern bureaublad** voor **Gateway-tokengeheim** de naam in van het eerder gemaakte geheim. Deze waarde is niet de functiesleutel zelf, maar de naam van het geheim in de sleutelkluis van het lab die de functiesleutel bevat.

    ![Gatewaytokengeheim in labinstellingen](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Opslaan** Wijzigingen.

    > [!NOTE] 
    > Door op **Opslaan**te klikken, gaat u akkoord met [de licentievoorwaarden van Extern bureaublad-gateway.](https://www.microsoft.com/licensing/product-licensing/products) Zie Welkom bij Extern [bureaublad-services](https://aka.ms/rds) en [Implementeer uw externe desktopomgeving](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)voor meer informatie over externe gateway.


Zie [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) voor een voorbeeld van PowerShell-script om de status van **gatewayhost** en **gatewaytoken in** te stellen als u het lab via automatisering configureert. De [GitHub-repository azure DevTest Labs](https://github.com/Azure/azure-devtestlab) biedt ook een Azure Resource Manager-sjabloon waarmee een lab wordt gemaakt of bijgewerkt met de geheime **gatewaynamen** en de geheime instellingen **voor gateway-token.**

## <a name="configure-network-security-group"></a>Netwerkbeveiligingsgroep configureren
Om het lab verder te beveiligen, kan een netwerkbeveiligingsgroep (NSG) worden toegevoegd aan het virtuele netwerk dat wordt gebruikt door de virtuele machines van het lab. Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen voor](../virtual-network/manage-network-security-group.md)instructies voor het instellen van een NSG.

Hier is een voorbeeld NSG dat alleen het verkeer dat eerst gaat via de gateway naar lab machines te bereiken. De bron in deze regel is het IP-adres van de enkele gatewaymachine of het IP-adres van de load balancer voor de gatewaymachines.

![Netwerkbeveiligingsgroep - regels](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Voorbeeld om een extern bureaublad-gateway te maken

> [!NOTE] 
> Door de voorbeeldsjablonen te gebruiken, gaat u akkoord met [de licentievoorwaarden van Extern bureaublad-gateway.](https://www.microsoft.com/licensing/product-licensing/products) Zie Welkom bij Extern [bureaublad-services](https://aka.ms/rds) en [Implementeer uw externe desktopomgeving](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)voor meer informatie over externe gateway.

De [GitHub-repository van Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) biedt een paar voorbeelden om de resources in te stellen die nodig zijn om tokenverificatie en externe desktopgateway met DevTest Labs te gebruiken. Deze voorbeelden omvatten Azure Resource Manager-sjablonen voor gatewaymachines, labinstellingen en functie-app.

Volg deze stappen om een voorbeeldoplossing voor de extern bureaublad-gatewayfarm in te stellen.

1. Maak een ondertekeningscertificaat.  Uitvoeren [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Sla de duimafdruk, het wachtwoord en de Base64-codering van het gemaakte certificaat op.
2. Ontvang een TLS/SSL-certificaat. FQDN dat is gekoppeld aan het TLS/SSL-certificaat moet zijn voor het domein dat u beheert. Sla de duimafdruk, wachtwoord en Base64-codering op voor dit certificaat. Als u duimafdruk wilt maken met PowerShell, gebruikt u de volgende opdrachten.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Als u de Base64-codering wilt krijgen met PowerShell, gebruikt u de volgende opdracht.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Bestanden downloaden [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)van .

    De sjabloon vereist toegang tot een paar andere Resource Manager-sjablonen en gerelateerde bronnen op dezelfde basis URI. Kopieer alle bestanden [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) van rdgatewayFedAuth.msi naar een blobcontainer in een opslagaccount.  
4. **Azuredeploy.json** implementeren [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)vanuit . De sjabloon neemt de volgende parameters:
    - adminUsername - Vereist.  Beheerdersgebruikersnaam voor de gatewaymachines.
    - adminPassword - Vereist. Wachtwoord voor het beheerdersaccount voor de gatewaymachines.
    - instanceCount – Aantal gatewaymachines dat moet worden gemaakt.  
    - alwaysOn – geeft aan of de gemaakte Azure Functions-app in een warme staat moet blijven of niet. Als u de Azure Functions-app houdt, voorkomt u vertragingen wanneer gebruikers voor het eerst verbinding proberen te maken met hun lab-vm, maar dit heeft wel gevolgen voor de kosten.  
    - tokenLifetime – De tijdsduur van het gemaakte token is geldig. Formaat is HH:MM:SS.
    - sslCertificate – De Base64-codering van het TLS/SSL-certificaat voor de gatewaymachine.
    - sslCertificatePassword – Het wachtwoord van het TLS/SSL-certificaat voor de gatewaymachine.
    - sslCertificateThumbprint - De duimafdruk van het certificaat voor identificatie in het lokale certificaatarchief van het TLS/SSL-certificaat.
    - signCertificate – De Base64-codering voor het ondertekenen van certificaat voor de gatewaymachine.
    - signCertificatePassword – Het wachtwoord voor het ondertekenen van certificaat voor de gatewaymachine.
    - signCertificateThumbprint - De duimafdruk van het certificaat voor identificatie in het lokale certificaatarchief van het ondertekeningscertificaat.
    - _artifactsLocation - URI-locatie waar alle ondersteunende bronnen kunnen worden gevonden. Deze waarde moet een volledig gekwalificeerde UIR zijn, geen relatief pad.
    - _artifactsLocationSasToken : het SAS-token (Shared Access Signature) dat wordt gebruikt om toegang te krijgen tot ondersteunende bronnen, als de locatie een Azure-opslagaccount is.

    De sjabloon kan worden geïmplementeerd met de Azure CLI met behulp van de volgende opdracht:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Hier zijn de beschrijvingen van de parameters:

    - Het {storage-account-endpoint} kan worden `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`verkregen door het uitvoeren van .  De {storage-acct-name} is de naam van het opslagaccount dat bestanden bevat die u hebt geüpload.  
    - De {containernaam} is de naam van de container in de {storage-acct-name} die bestanden bevat die u hebt geüpload.  
    - De {sas-token} kan worden `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`verkregen door het uitvoeren van . 
        - De {storage-acct-name} is de naam van het opslagaccount dat bestanden bevat die u hebt geüpload.  
        - De {containernaam} is de naam van de container in de {storage-acct-name} die bestanden bevat die u hebt geüpload.  
        - De {utc-expiratiedatum} is de datum in UTC waarop het SAS-token verloopt en het SAS-token niet langer kan worden gebruikt om toegang te krijgen tot het opslagaccount.

    Noteer de waarden voor gatewayFQDN en gatewayIP vanuit de implementatieuitvoer van de sjabloon. U moet ook de waarde van de functiesleutel opslaan voor de nieuw gemaakte functie, die u vinden op het tabblad [Instellingen voor de functie-app.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Configureer DNS zodat FQDN van TLS/SSL-cert vanaf vorige stap naar IP-adres van gatewayIP wordt doorgestroomd.

    Nadat de Remote Desktop Gateway-farm is gemaakt en de juiste DNS-updates zijn gemaakt, is deze klaar om te worden gebruikt door een lab in DevTest Labs. De **toegangspoorthostname** en **de geheime gatewaytoken-instellingen** moeten zijn geconfigureerd om de gatewaymachine(s) te gebruiken die u hebt geïmplementeerd. 

    > [!NOTE]
    > Als de labmachine privé-IP's gebruikt, moet er een netwerkpad zijn van de gatewaymachine naar de labmachine, hetzij door hetzelfde virtuele netwerk te delen of door een peered virtueel netwerk te gebruiken.

    Zodra zowel gateway als lab zijn geconfigureerd, bevat het verbindingsbestand dat is gemaakt wanneer de labgebruiker op de **Verbinding** klikt automatisch informatie die nodig is om verbinding te maken met behulp van tokenverificatie.     

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over Extern bureaublad-services: [documentatie over Extern bureaublad-services](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


