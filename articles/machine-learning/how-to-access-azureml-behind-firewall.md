---
title: Een firewall gebruiken
titleSuffix: Azure Machine Learning
description: Toegang tot Azure Machine Learning-werk ruimten beheren met Azure-firewalls. Meer informatie over de hosts die u moet toestaan via de firewall om Azure Machine Learning correct te laten functioneren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7d270ac9a6597645c5a98b6af77d19021ef00329
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827423"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>De werk ruimte achter een firewall gebruiken voor Azure Machine Learning

In dit artikel vindt u informatie over het configureren van Azure Firewall voor het beheren van de toegang tot uw Azure Machine Learning-werk ruimte en het open bare Internet.   Zie [Enter prise Security for Azure machine learning](concept-enterprise-security.md) voor meer informatie over het beveiligen van Azure machine learning.

Hoewel de informatie in dit document is gebaseerd op het gebruik van [Azure firewall](../firewall/tutorial-firewall-deploy-portal.md), kunt u het gebruiken met andere firewall producten. Als u vragen hebt over het toestaan van communicatie via uw firewall, raadpleegt u de documentatie voor de firewall die u gebruikt.

## <a name="network-rules"></a>Netwerk regels

Maak op uw firewall een netwerk regel die verkeer naar en van de adressen in dit artikel toestaat.

> [!TIP]
> Wanneer u de netwerk regel toevoegt, stelt u het __protocol__ in op wille keurige en de poorten `*` .
>
> Zie [Azure firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)voor meer informatie over het configureren van Azure firewall.

## <a name="microsoft-hosts"></a>Micro soft-hosts

Als niet correct is geconfigureerd, kan de firewall problemen veroorzaken met uw werk ruimte. Er zijn verschillende hostnamen die worden gebruikt door de Azure Machine Learning-werk ruimte.

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
| **your-acr-server-name.azurecr.io** | Alleen nodig als uw Azure Container Registry zich achter het virtuele netwerk bevindt. In deze configuratie wordt een persoonlijke koppeling vanuit de micro soft-omgeving gemaakt naar het ACR-exemplaar in uw abonnement. Gebruik de ACR-server naam voor uw Azure Machine Learning-werk ruimte. |
| **\*. notebooks.azure.net** | Vereist door de notitie blokken in Azure Machine Learning Studio. |
| **graph.windows.net** | Vereist voor notebooks |

## <a name="python-hosts"></a>Python-hosts

De hosts in deze sectie worden gebruikt voor het installeren van Python-pakketten. Ze zijn vereist tijdens de ontwikkeling, training en implementatie. 

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **anaconda.com** | Wordt gebruikt om standaard pakketten te installeren. |
| **\*. anaconda.org** | Wordt gebruikt om opslag plaats-gegevens op te halen. |
| **pypi.org** | Wordt gebruikt voor het weer geven van afhankelijkheden uit de standaard index, indien aanwezig, en de index wordt niet overschreven door gebruikers instellingen. Als de index wordt overschreven, moet u ook ** \* . pythonhosted.org**toestaan. |

## <a name="r-hosts"></a>R-hosts

De hosts in deze sectie worden gebruikt voor het installeren van R-pakketten. Ze zijn vereist tijdens de ontwikkeling, training en implementatie.

> [!IMPORTANT]
> Intern maakt de R SDK voor Azure Machine Learning gebruik van Python-pakketten. Daarom moet u ook python-hosts via de Firewall toestaan.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **cloud.r-project.org** | Wordt gebruikt bij het installeren van KRANs pakketten. |

## <a name="azure-government-region"></a>Azure Government regio

Vereiste Url's voor de Azure Government regio's.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | De regio VS-Arizona |
| **usgovvirginia.api.ml.azure.us** | De regio vs-Virginia |

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Azure Firewall implementeren en configureren met Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure ML-experimenten beveiligen en taken in een Azure-Virtual Network afzorgen](how-to-enable-virtual-network.md)
