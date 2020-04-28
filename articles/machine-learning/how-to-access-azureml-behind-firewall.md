---
title: Azure Machine Learning achter een firewall gebruiken
titleSuffix: Azure Machine Learning
description: Gebruik veilig Azure Machine Learning achter Azure Firewall. Meer informatie over de hosts die u moet toestaan via de firewall om Azure Machine Learning correct te laten functioneren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196320"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>Azure Machine Learning werk ruimte gebruiken achter Azure Firewall

Dit artikel bevat informatie over het configureren van Azure Firewall voor gebruik met Azure Machine Learning.

Azure Firewall kan worden gebruikt om de toegang tot uw Azure Machine Learning-werk ruimte en het open bare Internet te beheren. Als niet correct is geconfigureerd, kan de firewall problemen veroorzaken met uw werk ruimte.

## <a name="network-rules"></a>Netwerkregels

Maak op uw firewall een netwerk regel die verkeer naar en van de adressen in dit artikel toestaat.

> [!TIP]
> Wanneer u de netwerk regel toevoegt, stelt u het __protocol__ in op wille keurige en de poorten `*`.
>
> Zie [Azure firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)voor meer informatie over het configureren van Azure firewall.

## <a name="microsoft-hosts"></a>Micro soft-hosts

De hosts in deze sectie zijn eigendom van micro soft en bieden services die nodig zijn om uw werk ruimte goed te laten functioneren.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Trainings clusters |
| **ml.azure.com** | Azure Machine Learning Studio |
| **\*. azureml.ms** | Gebruikt door Azure Machine Learning-Api's |
| **\*. experiments.azureml.net** | Gebruikt door experimenten die worden uitgevoerd in Azure Machine Learning|
| **\*. modelmanagement.azureml.net** | Wordt gebruikt om modellen te registreren en te implementeren|
| **mlworkspace.azure.ai** | Wordt gebruikt door de Azure Portal bij het weer geven van een werk ruimte |
| **\*. aether.ms** | Gebruikt bij het uitvoeren van Azure Machine Learning pijp lijnen |
| **\*. instances.azureml.net** | Azure Machine Learning Compute-instanties |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **microsoft.com** | Base docker-installatie kopieën |
| **azurecr.io** | Azure Container Registry |

## <a name="python-hosts"></a>Python-hosts

De hosts in deze sectie worden gebruikt voor het installeren van Python-pakketten. Ze zijn vereist tijdens de ontwikkeling, training en implementatie. 

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **anaconda.com** | Gebruikt bij het installeren van Conda-pakketten |
| **pypi.org** | Gebruikt bij het installeren van PIP-pakketten |

## <a name="r-hosts"></a>R-hosts

De hosts in deze sectie worden gebruikt voor het installeren van R-pakketten. Ze zijn vereist tijdens de ontwikkeling, training en implementatie.

> [!IMPORTANT]
> Intern maakt de R SDK voor Azure Machine Learning gebruik van Python-pakketten. Daarom moet u ook python-hosts via de Firewall toestaan.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **cloud.r-project.org** | Wordt gebruikt bij het installeren van KRANs pakketten. |

Volgende stappen

* [[Azure firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md)]
* [Azure ML-experimenten beveiligen en taken in een Azure-Virtual Network afzorgen](how-to-enable-virtual-network.md)