---
title: Gedeelde zwembaden
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het maken & implementeren van een gedeelde pool van Virtuele Data Machines (DSVMs) als gedeelde bron voor een team.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science proces
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: cc0efc0a076ddc3fc9425999f1e38b4a32dec7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477337"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Een gedeelde pool van virtuele machines voor Data Science maken

In dit artikel leert u hoe u een gedeelde pool van Virtuele Data Machines (DSVMs) maakt voor een team. De voordelen van het gebruik van een gedeelde groep zijn een beter gebruik van resources, eenvoudiger delen en samenwerking en een effectiever beheer van DSVM-resources.

U veel methoden en technologieën gebruiken om een pool van DSVMs te maken. Dit artikel richt zich op pools voor interactieve virtuele machines (VM's). Een alternatieve beheerde compute-infrastructuur is Azure Machine Learning Compute. Zie [Rekendoelen instellen](../how-to-set-up-training-targets.md#amlcompute)voor meer informatie .

## <a name="interactive-vm-pool"></a>Interactieve VM-pool

Een pool van interactieve VM's die worden gedeeld door het hele AI/ data science-team stelt gebruikers in staat om in te loggen op een beschikbaar exemplaar van de DSVM in plaats van een speciale instantie voor elke set gebruikers. Deze instelling maakt een betere beschikbaarheid en een effectiever gebruik van resources mogelijk.

U gebruikt [Azure virtual machine scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) technology om een interactieve VM-groep te maken. U schaalsets gebruiken om een groep identieke vm's voor laden en automatisch schalen te maken en te beheren.

De gebruiker meldt zich aan bij het IP- of DNS-adres van de hoofdgroep. De schaalset leidt de sessie automatisch naar een beschikbare DSVM in de schaalset. Omdat gebruikers een consistente en vertrouwde omgeving willen, ongeacht de VM waarop ze zich aanmelden, monteren alle exemplaren van de VM in de schaalset een gedeeld netwerkstation, zoals een Azure Files share of een NFS-share (Network File System). De gedeelde werkruimte van de gebruiker wordt normaal gesproken bewaard in het gedeelde bestandsarchief dat op elk van de instanties is gemonteerd.

U een voorbeeld van Azure Resource Manager-sjabloon vinden waarmee een schaalset is gemaakt met Ubuntu DSVM-exemplaren op [GitHub.](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) Op dezelfde locatie vindt u een voorbeeld van het [parameterbestand](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) voor de sjabloon Azure Resource Manager.

U de schaalset maken vanuit de sjabloon Azure Resource Manager door waarden op te geven voor het parameterbestand in het Azure CLI:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

De voorgaande opdrachten gaan ervan uit dat u:

* Een kopie van het parameterbestand met de waarden die zijn opgegeven voor uw instantie van de schaalset.
* Het aantal VM-exemplaren.
* Verwijzingen naar het aandeel Azure-bestanden.
* Referenties voor het opslagaccount dat op elke vm wordt gemonteerd.

Naar het parameterbestand wordt lokaal verwezen in de opdrachten. U ook parameters inline of prompt voor hen in uw script.  

De voorgaande sjabloon maakt de SSH- en De JupyterHub-poort vanaf de front-endschaal ingesteld op de back-end pool van Ubuntu DSVMs. Als gebruiker logt u op de normale manier in bij de VM op een Secure Shell (SSH) of op JupyterHub. Omdat de VM-exemplaren dynamisch kunnen worden opgeschaald of omlaag, moet elke status worden opgeslagen in het gemonteerde Azure-bestandenaandeel. U dezelfde aanpak gebruiken om een pool van Windows-DSVMs te maken.

Het [script dat het aandeel Azure Files monteert,](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) is ook beschikbaar in de Azure DataScienceVM-repository in GitHub. Het script monteert de azure-bestanden die worden gedeeld op het opgegeven bevestigingspunt in het parameterbestand. Het script maakt ook zachte links naar het gemonteerde station in de thuismap van de eerste gebruiker. Een gebruikersspecifieke notitieblokmap in het Azure-bestandenaandeel `$HOME/notebooks/remote` is gekoppeld aan de map, zodat gebruikers hun Jupyter-notitieblokken kunnen openen, uitvoeren en opslaan. U dezelfde conventie gebruiken wanneer u extra gebruikers op de VM maakt om de Jupyter-werkruimte van elke gebruiker te richten op het delen van Azure-bestanden.

Virtuele machineschaalsets ondersteunen automatisch schalen. U regels instellen over wanneer u extra exemplaren moet maken en wanneer u exemplaren wilt schalen. U bijvoorbeeld naar nul exemplaren schalen om te besparen op de gebruikskosten van cloudhardware wanneer de VM's helemaal niet worden gebruikt. De documentatiepagina's van virtuele machineschaalsets bieden gedetailleerde stappen voor [automatisch schalen.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)

## <a name="next-steps"></a>Volgende stappen

* [Een algemene identiteit instellen](dsvm-common-identity.md)
* [Veilige referenties opslaan om toegang te krijgen tot cloudbronnen](dsvm-secure-access-keys.md)
