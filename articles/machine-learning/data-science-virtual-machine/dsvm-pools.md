---
title: Data Science Virtual Machine-pools - Azure | Microsoft Docs
description: Groepen van Dsvm implementeren als een gedeelde resource voor een team
keywords: deep learning, AI, hulpprogramma's voor data science, virtuele machine voor datatechnologie, georuimtelijke analyses, team data science process
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: vijetaj
ms.openlocfilehash: 76a715838fa5eb4f806fccc98675ea5acdbe1822
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992034"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Maken van een gedeelde groep van virtuele Machines voor Datatechnologie

In dit artikel wordt beschreven hoe u een gedeelde pool van data Science Virtual Machines (Dsvm) maakt voor een team. De voor delen van het gebruik van een gedeelde groep zijn het verbeteren van het resource gebruik, het eenvoudig delen en samen werken en een efficiëntere beheer van DSVM-resources.

U kunt vele methoden en -technologieën gebruiken om een pool van Dsvm te maken. Dit artikel richt zich op Pools voor interactieve virtuele machines (Vm's). Een alternatieve beheerde Compute-infra structuur is Azure Machine Learning Reken kracht. Zie [reken doelen instellen](../service/how-to-set-up-training-targets.md#amlcompute)voor meer informatie.

## <a name="interactive-vm-pool"></a>Interactieve VM-pool

Een pool van interactieve virtuele machines die worden gedeeld door het hele AI/data science-team kan gebruikers zich aanmelden bij een beschikbaar exemplaar van de DSVM in plaats van een toegewezen exemplaar voor elke set van gebruikers. Deze installatie maakt betere Beschik baarheid en efficiëntere gebruik van resources mogelijk.

U gebruikt de [Azure virtual machine Scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) -technologie om een interactieve VM-groep te maken. U kunt schaalsets gebruiken voor een groep identieke, taakverdeling en virtuele machines automatisch schalen maken en beheren.

De gebruiker zich aanmeldt de hoofdpool IP- of DNS-adres. De schaal automatisch ingesteld routes de sessie op een beschikbare DSVM in de schaalset. Omdat gebruikers een consistente en vertrouwde omgeving willen, ongeacht de VM waarin ze zich aanmelden bij, kunnen alle exemplaren van de virtuele machine in de schaalset een gedeeld netwerk station koppelen, zoals een Azure Files share of een NFS-share (Network File System). Gedeelde werkruimte van de gebruiker wordt normaal gesproken opgeslagen op de gedeelde bestandsopslag die gekoppeld op elk van de exemplaren.

U vindt een voorbeeld Azure Resource Manager-sjabloon maakt u een schaalset met Ubuntu-DSVM-exemplaren op [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). U vindt een voor beeld van het [parameter bestand](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) voor de Azure Resource Manager sjabloon op dezelfde locatie.

U kunt de schaalset maken op basis van de sjabloon Azure Resource Manager door waarden op te geven voor het parameter bestand in de Azure CLI:

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
De voorgaande opdrachten wordt ervan uitgegaan dat u hebt:
* Een kopie van het parameterbestand met de waarden die zijn opgegeven voor uw exemplaar van de schaalset.
* Het aantal VM-exemplaren.
* Verwijzingen naar de Azure-bestanden delen.
* Referenties voor het opslagaccount dat wordt gekoppeld op elke virtuele machine.

Het parameterbestand wordt lokaal verwezen in de opdrachten. U kunt ook parameters in line- of vragen om ze in uw script doorgeven.  

De voorgaande sjabloon kunt u de SSH- en de poort JupyterHub van de front-schaalset ingesteld op de back-end-pool van Ubuntu-Dsvm. Als gebruiker meldt u zich op de normale manier aan bij de virtuele machine op een Secure Shell (SSH) of op JupyterHub. Omdat de VM-exemplaren dynamisch omhoog of omlaag kunnen worden geschaald, moet elke status worden opgeslagen in de gekoppelde Azure Files share. U kunt dezelfde benadering gebruiken om een pool van Windows-Dsvm te maken.

De [-script waarmee de Azure Files-share koppelt](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) is ook beschikbaar in de Azure-DataScienceVM-opslagplaats in GitHub. Het script koppelt u de Azure-bestandsshare op het opgegeven koppelpunt in het parameterbestand. Het script maakt ook zachte koppelingen naar het gekoppelde station in de basismap van de eerste gebruiker. Een gebruikersspecifieke notebook-map in de Azure Files share is zacht gekoppeld aan de `$HOME/notebooks/remote` Directory, zodat gebruikers hun Jupyter-notebooks kunnen openen, uitvoeren en opslaan. U kunt dezelfde conventie gebruiken wanneer u extra gebruikers maakt op de virtuele machine van elke gebruiker Jupyter werkruimte verwijzen naar de Azure-bestandsshare.

Virtuele-machineschaalsets ondersteuning voor automatisch schalen. U kunt regels instellen over wanneer u extra instanties wilt maken en wanneer u exemplaren omlaag wilt schalen. U kunt bijvoorbeeld omlaag schalen naar nul exemplaren op te slaan op de hardwarekosten gebruik cloud wanneer de virtuele machines worden helemaal niet gebruikt. De documentatie van pagina's van virtuele-machineschaalsets bieden gedetailleerde stappen voor het [automatisch schalen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Volgende stappen

* [Een gemeenschappelijke identiteit instellen](dsvm-common-identity.md)
* [Veilig opslaan van referenties voor toegang tot cloud-bronnen](dsvm-secure-access-keys.md)















