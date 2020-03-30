---
title: Azure Cloud Shell-functies | Microsoft Documenten
description: Overzicht van functies in Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 0aa71e4b78df8087093f183b146c525d2a8a0f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366237"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Functies & hulpprogramma's voor Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell `Ubuntu 16.04 LTS`draait op .

## <a name="features"></a>Functies

### <a name="secure-automatic-authentication"></a>Automatische verificatie beveiligen

Cloud Shell verifieert veilig en automatisch accounttoegang voor azure CLI en Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME volharding over sessies heen

Als u bestanden voor alle sessies wilt blijven beheren, leidt Cloud Shell u door het koppelen van een Azure-bestandsshare bij de eerste lancering.
Na voltooiing zal Cloud Shell uw opslag automatisch `$HOME\clouddrive`koppelen (gemonteerd als ) voor alle toekomstige sessies.
Bovendien wordt `$HOME` uw map gehandhaafd als een .img in uw Azure File-share.
Bestanden buiten `$HOME` en machinestatus blijven niet bestaan in alle sessies. Gebruik aanbevolen procedures bij het opslaan van geheimen zoals SSH-sleutels. Services zoals [Azure Key Vault hebben zelfstudies voor het instellen.](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites)

[Meer informatie over doorgaande bestanden in Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-station (Azure:)

PowerShell in Cloud Shell start`Azure:`u in Azure drive ( ).
Het Azure-station maakt eenvoudige detectie en navigatie van Azure-bronnen mogelijk, zoals Compute, Network, Storage etc. vergelijkbaar met bestandssysteemnavigatie.
U de bekende [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure) blijven gebruiken om deze resources te beheren, ongeacht het station waarin u zich bevindt.
Alle wijzigingen die zijn aangebracht in de Azure-resources, rechtstreeks in azure-portal of via Azure PowerShell-cmdlets, worden weergegeven in het Azure-station.  U kunt `dir -Force` uitvoeren om uw resources te vernieuwen.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online beheren

PowerShell in Cloud Shell bevat een private build van de Exchange Online module.  Ren `Connect-EXOPSSession` om uw Exchange-cmdlets te krijgen.

![](media/features-powershell/exchangeonline.png)

 Voer `Get-Command -Module tmp_*` uit.
> [!NOTE]
> De naam van `tmp_`de module moet beginnen met , als u modules met hetzelfde voorvoegsel hebt geïnstalleerd, zullen hun cmdlets ook worden opgedoken. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Diepe integratie met open-source tooling

Cloud Shell bevat vooraf geconfigureerde verificatie voor open-source tools zoals Terraform, Ansible en Chef InSpec. Probeer het uit van het voorbeeld walkthroughs.

## <a name="tools"></a>Hulpprogramma's

|Categorie   |Name   |
|---|---|
|Linux-tools            |bash<br> Zsh<br> Sh<br> tmux<br> Graven<br>               |
|Azure-hulpprogramma's            |[Azure CLI](https://github.com/Azure/azure-cli) en [Azure klassieke CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy (AzCopy)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric-CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Teksteditors           |code (Cloud Shell-editor)<br> vim<br> nano<br> emacs    |
|Broncodebeheer         |git                    |
|Gereedschappen bouwen            |make<br> maven<br> npm<br> Pip         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Roer](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Databases              |MySQL-client<br> PostgreSql-client<br> [sqlcmd-hulpprogramma](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Overige                  |iPython-client<br> [Cloud Gieterij CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible (Ansible)](https://www.ansible.com/microsoft-azure)<br> [Chef-kok Inspec](https://www.chef.io/inspec/)<br> [Poppenbout](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)|

## <a name="language-support"></a>Taalondersteuning

|Taal   |Versie   |
|---|---|
|.NET Core  |2.2.402       |
|Aan de slag         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 en 3.5 (standaard)|

## <a name="next-steps"></a>Volgende stappen
[Bash in Cloud Shell Quickstart](quickstart.md) <br>
[PowerShell in Cloud Shell Quickstart](quickstart-powershell.md) <br>
[Meer informatie over Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Meer informatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
