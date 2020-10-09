---
title: Aangepaste DNS-server gebruiken
titleSuffix: Azure Machine Learning
description: Een aangepaste DNS-server configureren voor gebruik met een Azure Machine Learning-werk ruimte en een persoonlijk eind punt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1d215c9564d89e5bd410e68839807f5c2c752356
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828512"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Uw werk ruimte gebruiken met een aangepaste DNS-server

Wanneer u Azure Machine Learning met een virtueel netwerk gebruikt, zijn er [verschillende manieren om DNS-naam omzetting te verwerken](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances). Standaard verwerkt Azure automatisch naam omzetting voor uw werk ruimte en persoonlijk eind punt. Wanneer u echter __uw eigen aangepaste DNS-server gebruikt__, moet u hand matig DNS-vermeldingen maken voor de werk ruimte.

> [!IMPORTANT]
> In dit artikel wordt alleen beschreven hoe u de Fully Qualified Domain Name (FQDN) en IP-adressen voor deze vermeldingen kunt vinden. Deze bevat geen informatie over het configureren van de DNS-records voor deze items. Raadpleeg de documentatie bij uw DNS-software voor informatie over het toevoegen van records.

## <a name="prerequisites"></a>Vereisten

- Een Azure-Virtual Network die gebruikmaakt van [uw eigen DNS-server](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

- Een Azure Machine Learning-werk ruimte met een persoonlijk eind punt. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Vertrouwd met het gebruik van [netwerk isolatie tijdens de training & deinterferentie](how-to-enable-virtual-network.md).

- Optioneel, [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) of [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>De IP-adressen zoeken

De volgende lijst bevat de volledig gekwalificeerde domein namen (FQDN) die worden gebruikt door uw werk ruimte en persoonlijk eind punt:

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.studio.workspace.<region>.api.azureml.ms`
* `cert-<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.ml`
* Als u een reken instantie maakt, moet u ook een vermelding voor toevoegen `<instance-name>.<region>.instances.azureml.ms` .

Gebruik een van de volgende methoden om de interne IP-adressen voor de FQDN-namen in het VNet te vinden:

> [!NOTE]
> De volledig gekwalificeerde domein namen en IP-adressen verschillen op basis van uw configuratie. De GUID-waarde in de domein naam is bijvoorbeeld specifiek voor uw werk ruimte.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Selecteer uw Azure Machine Learning- __werk ruimte__In de [Azure Portal](https://portal.azure.com).
1. Selecteer in de sectie __instellingen__ de optie verbindingen met een __privé-eind punt__.
1. Selecteer de koppeling in de kolom met het __persoonlijke eind punt__ die wordt weer gegeven.
1. Onder aan de pagina vindt u een lijst met de FQDN-namen (Fully Qualified Domain names) en IP-adressen voor het persoonlijke eind punt van de werk ruimte.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lijst met FQDN-namen in de portal":::

---

De informatie die door alle methoden wordt geretourneerd, is hetzelfde. een lijst met de FQDN en het persoonlijke IP-adres voor de resources.

| FQDN | IP-adres |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.studio.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `cert-fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Sommige FQDN-vormen worden niet weer gegeven in de lijst die wordt weer gegeven door het persoonlijke eind punt, maar zijn vereist voor de werk ruimte. Deze FQDN-namen worden weer gegeven in de volgende tabel en moeten ook worden toegevoegd aan uw DNS-server:
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Als u een reken instantie hebt, gebruikt u `<instance-name>.<region>.instances.azureml.ms` , waarbij `<instance-name>` de naam van uw reken instantie is.
>
> Voor al deze IP-adressen gebruikt u hetzelfde adres als de `*.api.azureml.ms` vermeldingen die in de vorige stappen zijn geretourneerd.

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Virtual Network](how-to-network-security-overview.md)voor meer informatie over het gebruik van Azure machine learning met een virtueel netwerk.