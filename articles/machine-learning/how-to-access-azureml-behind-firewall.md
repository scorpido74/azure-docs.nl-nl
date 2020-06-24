---
title: Een firewall gebruiken
titleSuffix: Azure Machine Learning
description: Toegang tot Azure Machine Learning-werk ruimten beheren met Azure-firewalls. Meer informatie over de hosts die u moet toestaan via de firewall om Azure Machine Learning correct te laten functioneren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.custom: tracking-python
ms.openlocfilehash: 31daec93352c0e142075a55c61f2b8d3a6d56fab
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080232"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Werk ruimte achter Azure Firewall gebruiken voor Azure Machine Learning

In dit artikel leert u hoe u Azure Firewall kunt configureren voor gebruik met een Azure Machine Learning-werk ruimte.

Azure Firewall kan worden gebruikt om de toegang tot uw Azure Machine Learning-werk ruimte en het open bare Internet te beheren. Als niet correct is geconfigureerd, kan de firewall problemen veroorzaken met uw werk ruimte. Er zijn verschillende hostnamen die worden gebruikt door de Azure Machine Learning-werk ruimte, die in dit artikel worden beschreven.

## <a name="network-rules"></a>Netwerk regels

Maak op uw firewall een netwerk regel die verkeer naar en van de adressen in dit artikel toestaat.

> [!TIP]
> Wanneer u de netwerk regel toevoegt, stelt u het __protocol__ in op wille keurige en de poorten `*` .
>
> Zie [Azure firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)voor meer informatie over het configureren van Azure firewall.

## <a name="microsoft-hosts"></a>Micro soft-hosts

De hosts in deze sectie zijn eigendom van micro soft en bieden services die nodig zijn om uw werk ruimte goed te laten functioneren.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Trainings clusters |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Gebruikt door de Azure Machine Learning Studio |
| **\*. azureml.ms** | Gebruikt door Azure Machine Learning-Api's |
| **\*. experiments.azureml.net** | Gebruikt door experimenten die worden uitgevoerd in Azure Machine Learning |
| **\*. modelmanagement.azureml.net** | Wordt gebruikt om modellen te registreren en te implementeren|
| **mlworkspace.azure.ai** | Wordt gebruikt door de Azure Portal bij het weer geven van een werk ruimte |
| **\*. aether.ms** | Gebruikt bij het uitvoeren van Azure Machine Learning pijp lijnen |
| **\*. instances.azureml.net** | Azure Machine Learning Compute-instanties |
| **\*. instances.azureml.ms** | Azure Machine Learning Compute-instanties waarvoor een persoonlijke koppeling is ingeschakeld voor de werk ruimte |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | Micro soft Container Registry voor base docker-installatie kopieën |

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
