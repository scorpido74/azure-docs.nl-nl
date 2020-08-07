---
title: Azure Machine Learning Enter prise-en Basic-edities
description: Meer informatie over de verschillen tussen de edities van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: aa754868677802b7d0000045f22090fbca62d9b6
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927416"
---
# <a name="enterprise-and-basic-editions-of-azure-machine-learning"></a>Enter prise-en Basic-edities van Azure Machine Learning 

Azure Machine Learning biedt twee edities die zijn afgestemd op uw machine learning behoeften. Deze edities bepalen welke tools voor machine learning beschikbaar zijn voor ontwikkelaars en gegevenswetenschappers in hun werkruimte.

## <a name="choose-an-edition"></a>Een editie kiezen

U wijst de gewenste editie toe wanneer u een werkruimte maakt. Klanten zijn verantwoordelijk voor kosten die gedurende deze periode zijn gemaakt voor rekenresources en andere Azure-resources. Meer informatie over het [beheren van de kosten voor Azure machine learning](concept-plan-manage-cost.md).

Lees hier meer over het [upgraden van een Basic-werkruimte naar de Enterprise-editie](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Wat zit er in elke editie?

### <a name="data-for-machine-learning-capabilities"></a>Gegevens voor Machine Learning mogelijkheden  

| Functies                     | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| Labelen: [projecten labelen maken en beheren](tutorial-labeling.md) in Studio (web)                                                | Alle                     |
| Labelen: Labeler in Studio (web)                                    | Alle                     |
| Labelen: privé personeel gebruiken                               | Alle                     |
| Labelen: [ml gesteunde afbeeldings classificatie en object detectie](how-to-label-images.md)                  | Alleen Enter prise Edition |
| Gegevens sets + gegevens opslag: maken en beheren in python                       | Alle                     |
| Gegevens sets + gegevens opslag: maken en beheren in Studio (web)                         | Alle                     |
| Drift: gegevensset-monitors in python weer geven en beheren                           | Alle                     |
| Drift: gegevensset-monitors weer geven en beheren in Studio (web)                            | Alleen Enter prise Edition |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Geautomatiseerde trainings mogelijkheden (AutoML)

| Functies    | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| AutoML experimenten maken en uitvoeren [in notitie blokken](how-to-configure-auto-train.md)               | Alle                     |
| AutoML experimenten maken en uitvoeren [in Studio (web)](how-to-use-automated-ml-for-ml-models.md)   | Alleen Enter prise Edition |
| Toonaangevende AutoML-prognose mogelijkheden             | Alleen Enter prise Edition |
| Ondersteuning voor diep gaande lessen en andere geavanceerde kennissen | Alleen Enter prise Edition |
| Grote gegevens ondersteuning classificatie en regressie taken (Maxi maal 100 GB)                     | Alleen Enter prise Edition |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Verantwoordelijke Machine Learning

| Functies    | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Model uitleg](how-to-machine-learning-interpretability-automl.md)                                              | Alle                     |
| [Differentiële privacy](how-to-differential-privacy.md)                          | Alle                     |
| Aangepaste labels voor het implementeren van gegevens bladen    | Alle                     |
| Verdeling AzureML-integratie                                      | Alle                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Mogelijkheden voor bouwen en trainen

| Functies    | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integratie van Visual Studio code                                                     | Alle                     |
| Leer versterking                                                             | Alle                     |
| Gebruikers interface voor experimenten                                                                 | Alle                     |
| Jupyter, Jjupyterlab-integratie                                                    | Alle                     |
| Ondersteuning voor python-SDK                                                                 | Alle                     |
| R SDK-ondersteuning                                                                      | Alle                     |
| ML pijp lijnen: maken, uitvoeren en publiceren in python                           | Alle                     |
| ML pijp lijnen: geplande uitvoeringen van pijp lijnen maken, bewerken en verwijderen in python| Alle                     |
| ML pijp lijnen: pijplijn eindpunten maken in python SDK                                   | Alle                     |
| ML van pijp lijnen: uitvoerings details weer geven in Studio (web)                                              | Alle                     |
| ML pijp lijnen: maken, uitvoeren, visualiseren en publiceren in Designer                  | Alleen Enter prise Edition |
| ML pijp lijnen: pijplijn eindpunten maken in Designer | Alleen Enter prise Edition |
| Beheerde Compute-instanties voor geïntegreerde notebooks                                 | Alle                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Mogelijkheden voor implementatie en model beheer

| Functies                            | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| De Azure DevOps-extensie voor Machine Learning en de Azure ML CLI                 | Alle                     |
| [Event Grid-integratie](how-to-use-event-grid.md)                                                             | Alle                     |
| Azure Stream Analytics integreren met Azure Machine Learning                       | Alle                     |
| ML-pijp lijnen maken in SDK                                                         | Alle                     |
| Batch-deinterferentie                                                                  | Alle                     |
| Op FPGA gebaseerd Modellen met hardwareversnelling                                             | Alle                     |
| Model profilering                                                                    | Alle                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Mogelijkheden voor beveiliging, governance en beheer

| Functies     | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Azure RBAC-ondersteuning (op rollen gebaseerd toegangs beheer)](how-to-assign-roles.md)                                           | Alle                     |
| [Virtual Network-ondersteuning (VNet)](how-to-enable-virtual-network.md) voor compute                                         | Alle                     |
| Score endpoint-verificatie                                                    | Alle                     |
| [Persoonlijke werkruimte koppeling](how-to-configure-private-link.md)                                                            | Alle                     |
| [Quota beheer](how-to-manage-quotas.md) in werk ruimten                                                 | Alleen Enter prise Edition |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de beschik baarheid vindt u op de [pagina overzicht van Azure Machine Learning Edition en prijzen](https://azure.microsoft.com/pricing/details/machine-learning/). 

Lees hier meer over het [upgraden van een Basic-werkruimte naar de Enterprise-editie](how-to-manage-workspace.md#upgrade). 
