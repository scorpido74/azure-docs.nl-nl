---
title: 'Zelf studie: een Windows-webserver beveiligen met TLS/SSL-certificaten in azure'
description: In deze zelf studie leert u hoe u Azure PowerShell kunt gebruiken om een virtuele Windows-machine te beveiligen waarop de IIS-webserver wordt uitgevoerd met TLS/SSL-certificaten die zijn opgeslagen in Azure Key Vault.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: da9834636944c6bb816c4f49b0e9bf3abda2264a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097775"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Zelf studie: een webserver op een virtuele Windows-machine beveiligen in azure met TLS/SSL-certificaten die zijn opgeslagen in Key Vault

> [!NOTE]
> Dit document werkt momenteel alleen voor gegeneraliseerde installatie kopieën. Als u deze zelf studie probeert te gebruiken met behulp van een speciale schijf, treedt er een fout op. 

Als u webservers wilt beveiligen, kan een Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), worden gebruikt voor het versleutelen van webverkeer. Deze TLS/SSL-certificaten kunnen worden opgeslagen in Azure Key Vault en beveiligde implementaties van certificaten op virtuele Windows-machines (Vm's) in azure toestaan. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Key Vault maken
> * Een certificaat genereren of uploaden naar de Key Vault
> * Een virtuele machine maken en de IIS-webserver installeren
> * Het certificaat in de virtuele machine injecteren en IIS configureren met een TLS-binding


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook starten op een afzonderlijk browser tabblad door naar te [https://shell.azure.com/powershell](https://shell.azure.com/powershell)gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.


## <a name="overview"></a>Overzicht
Azure Key Vault beschermt cryptografische sleutels en geheimen, zoals certificaten of wachtwoorden. Key Vault helpt het beheerproces voor certificaten te stroomlijnen en zorgt dat u de controle houdt over de sleutels waarmee deze certificaten toegankelijk zijn. U kunt een zelfondertekend certificaat in Key Vault maken of een bestaand, vertrouwd certificaat dat u al bezit uploaden.

In plaats van met een aangepaste VM-installatiekopie die standaard certificaten bevat, kunt u certificaten invoeren in een actieve virtuele machine. Dit proces zorgt ervoor dat de meest recente certificaten tijdens de implementatie op een webserver zijn geïnstalleerd. Als u een certificaat wilt vernieuwen of vervangen, hoeft u niet ook een nieuwe aangepaste VM-installatiekopie te maken. De meest recente certificaten worden automatisch toegevoegd als u extra virtuele machines maakt. Tijdens het hele proces verlaten de certificaten nooit het Azure-platform of zijn ze blootgesteld in een script, opdrachtregelgeschiedenis of sjabloon.


## <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken
Voordat u een Key Vault en certificaten kunt maken, moet u eerst een resourcegroep maken met [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupSecureWeb* gemaakt op de locatie *VS - oost*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Maak vervolgens een Key Vault met [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault). Elke Key Vault moet een unieke naam hebben van alleen kleine letters. Vervang `mykeyvault` in het volgende voorbeeld door de naam van uw eigen unieke Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Een certificaat genereren en opslaan in Key Vault
Voor productie gebruik moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider met [import-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate). Voor deze zelf studie ziet u in het volgende voor beeld hoe u een zelfondertekend certificaat kunt genereren met [add-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) dat gebruikmaakt van het standaard certificaat beleid van [New-AzKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Stel een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

U kunt de virtuele machine nu maken met [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt op de locatie *VS Oost*. Als ze nog niet bestaan, worden de ondersteunende netwerkresources gemaakt. Om beveiligd webverkeer mogelijk te maken, opent de cmdlet ook poort *443*.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. In de laatste stap wordt gebruikgemaakt van de Azure-extensie voor aangepaste scripts om de IIS-webserver te installeren met [Set AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Een certificaat toevoegen aan de virtuele machine vanuit Key Vault
Om het certificaat van Key Vault toe te voegen aan een virtuele machine, moet u de ID van uw certificaat verkrijgen met [Get-AzKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret). Voeg het certificaat toe aan de virtuele machine met [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>IIS configureren voor gebruik van het certificaat
Gebruik de extensie voor aangepaste scripts opnieuw met [Set AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) om de IIS-configuratie bij te werken. Deze update is van toepassing op het certificaat dat is ingevoerd vanuit Key Vault en configureert de webbinding:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testen van de beveiligde web-app
Haal het openbare IP-adres van uw virtuele machine op met [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). In het volgende voorbeeld wordt het IP-adres opgehaald voor de `myPublicIP` die eerder is gemaakt:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Nu kunt u een webbrowser openen en `https://<myPublicIP>` in de adresbalk invoeren. Voor het accepteren van de beveiligingswaarschuwing als u een zelfondertekend certificaat hebt gebruikt, selecteert u **Details** en vervolgens **Ga verder naar de webpagina**:

![Beveiligingswaarschuwing voor web browser accepteren](./media/tutorial-secure-web-server/browser-warning.png)

Uw beveiligde IIS-website wordt vervolgens weergegeven zoals in het volgende voorbeeld:

![Beveiligde actieve IIS-site weergeven](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een IIS-webserver beveiligd met een TLS/SSL-certificaat dat is opgeslagen in Azure Key Vault. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Azure Key Vault maken
> * Een certificaat genereren of uploaden naar de Key Vault
> * Een virtuele machine maken en de IIS-webserver installeren
> * Het certificaat in de virtuele machine injecteren en IIS configureren met een TLS-binding

Volg deze link om voorbeelden te zien van vooraf gemaakte virtuele machinescripts.

> [!div class="nextstepaction"]
> [Scriptvoorbeelden van virtuele Windows-machines](./powershell-samples.md)
