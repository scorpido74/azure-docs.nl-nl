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
ms.openlocfilehash: ab8a49f62735a47c4ccc9fa488eed60088cd9c20
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658598"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>Enter prise (preview) en basis versies van Azure Machine Learning 

Azure Machine Learning biedt twee edities die zijn afgestemd op uw machine learning behoeften. Deze edities bepalen welke tools voor machine learning beschikbaar zijn voor ontwikkelaars en gegevenswetenschappers in hun werkruimte.

## <a name="choose-an-edition"></a>Een editie kiezen

U wijst de gewenste editie toe wanneer u een werkruimte maakt. Klanten zijn verantwoordelijk voor kosten die gedurende deze periode zijn gemaakt voor rekenresources en andere Azure-resources. Meer informatie over het [beheren van de kosten voor Azure machine learning](concept-plan-manage-cost.md).

Meer informatie over het [upgraden van een Basic-werk ruimte naar Enter prise (preview) Edition](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Wat zit er in elke editie?

### <a name="data-for-machine-learning-capabilities"></a>Gegevens voor Machine Learning mogelijkheden  

| Functies                     | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| Labelen: [projecten labelen maken en beheren](tutorial-labeling.md) in Studio (web)                                                | Alles                     |
| Labelen: Labeler in Studio (web)                                    | Alles                     |
| Labelen: privé personeel gebruiken                               | Alles                     |
| Labelen: [ml gesteunde afbeeldings classificatie en object detectie](how-to-label-images.md)                  | Alleen Enter prise Edition |
| Gegevens sets + gegevens opslag: maken en beheren in python                       | Alles                     |
| Gegevens sets + gegevens opslag: maken en beheren in Studio (web)                         | Alles                     |
| Drift: gegevensset-monitors in python weer geven en beheren                           | Alles                     |
| Drift: gegevensset-monitors weer geven en beheren in Studio (web)                            | Alleen Enter prise Edition |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Geautomatiseerde trainings mogelijkheden (AutoML)

| Functies    | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| AutoML experimenten maken en uitvoeren [in notitie blokken](how-to-configure-auto-train.md)               | Alles                     |
| AutoML experimenten maken en uitvoeren  [in Studio (web)](how-to-use-automated-ml-for-ml-models.md)   | Alleen Enter prise Edition |
| Toonaangevende AutoML-prognose mogelijkheden             | Alleen Enter prise Edition |
| Ondersteuning voor diep gaande lessen en andere geavanceerde kennissen | Alleen Enter prise Edition |
| Grote gegevens ondersteuning classificatie en regressie taken (Maxi maal 100 GB)                     | Alleen Enter prise Edition |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Verantwoordelijke Machine Learning

| Functies    | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Model uitleg](how-to-machine-learning-interpretability-automl.md)                                              | Alles                     |
| [Differentiële privacy](how-to-differential-privacy.md)                          | Alles                     |
| Aangepaste labels voor het implementeren van gegevens bladen    | Alles                     |
| Verdeling AzureML-integratie                                      | Alles                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Mogelijkheden voor bouwen en trainen

| Functies    | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integratie van Visual Studio code                                                     | Alles                     |
| Leer versterking                                                             | Alles                     |
| Gebruikers interface voor experimenten                                                                 | Alles                     |
| Jupyter, Jjupyterlab-integratie                                                    | Alles                     |
| Ondersteuning voor python-SDK                                                                 | Alles                     |
| R SDK-ondersteuning                                                                      | Alles                     |
| ML pijp lijnen: maken, uitvoeren en publiceren in python                           | Alles                     |
| ML pijp lijnen: geplande uitvoeringen van pijp lijnen maken, bewerken en verwijderen in python| Alles                     |
| ML pijp lijnen: pijplijn eindpunten maken in python SDK                                   | Alles                     |
| ML van pijp lijnen: uitvoerings details weer geven in Studio (web)                                              | Alles                     |
| ML pijp lijnen: maken, uitvoeren, visualiseren en publiceren in Designer                  | Alleen Enter prise Edition |
| ML pijp lijnen: pijplijn eindpunten maken in Designer | Alleen Enter prise Edition |
| Beheerde Compute-instanties voor geïntegreerde notebooks                                 | Alles                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Mogelijkheden voor implementatie en model beheer

| Functies                            | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| De Azure DevOps-extensie voor Machine Learning en de Azure ML CLI                 | Alles                     |
| [Event Grid-integratie](how-to-use-event-grid.md)                                                             | Alles                     |
| Azure Stream Analytics integreren met Azure Machine Learning                       | Alles                     |
| ML-pijp lijnen maken in SDK                                                         | Alles                     |
| Batch-deinterferentie                                                                  | Alles                     |
| Op FPGA gebaseerd Modellen met hardwareversnelling                                             | Alles                     |
| Model profilering                                                                    | Alles                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Mogelijkheden voor beveiliging, governance en beheer

| Functies     | Editie                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Azure RBAC-ondersteuning (op rollen gebaseerd toegangs beheer)](how-to-assign-roles.md)                                           | Alles                     |
| [Virtual Network-ondersteuning (VNet)](how-to-secure-training-vnet.md) voor compute                                         | Alles                     |
| Score endpoint-verificatie                                                    | Alles                     |
| [Persoonlijke werkruimte koppeling](how-to-configure-private-link.md)                                                            | Alles                     |
| [Quota beheer](how-to-manage-quotas.md) in werk ruimten                                                 | Alleen Enter prise Edition |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de beschik baarheid vindt u op de [pagina overzicht van Azure Machine Learning Edition en prijzen](https://azure.microsoft.com/pricing/details/machine-learning/). 

Lees hier meer over het [upgraden van een Basic-werkruimte naar de Enterprise-editie](how-to-manage-workspace.md#upgrade). 
