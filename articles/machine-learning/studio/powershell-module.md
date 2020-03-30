---
title: PowerShell-modules
titleSuffix: ML Studio (classic) - Azure
description: Gebruik PowerShell om Azure Machine Learning Studio (klassieke) werkruimten, experimenten, webservices en meer te maken en te beheren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204287"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>PowerShell-modules voor Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Met PowerShell-modules u uw Studio-bronnen en -assets zoals werkruimten, gegevenssets en webservices programmatisch beheren.

U communiceren met Studio(klassieke) resources met behulp van drie Powershell-modules:

* [Azure PowerShell Az,](#az-rm) uitgebracht in 2018, bevat alle functionaliteit van AzureRM, hoewel met verschillende cmdlet-namen
* [AzureRM](#az-rm) uitgebracht in 2016, vervangen door PowerShell Az
* [Azure Machine Learning PowerShell-klassieker](#classic) uitgebracht in 2016

Hoewel deze PowerShell-modules enkele overeenkomsten hebben, is elk ontworpen voor bepaalde scenario's. In dit artikel worden de verschillen tussen de PowerShell-modules beschreven en u bepalen welke modules u wilt kiezen.  

Bekijk de [onderstaande ondersteuningstabel](#support-table) om te zien welke bronnen door elke module worden ondersteund. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a>Azure PowerShell Az en AzureRM

Az is nu de beoogde PowerShell-module voor interactie met Azure en bevat alle eerdere functionaliteit van AzureRM. AzureRM blijft bugfixes ontvangen, maar ontvangt geen nieuwe cmdlets of -functies.  Az en AzureRM beheren beide oplossingen die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel. **Azure Resource Manager** Deze bronnen omvatten Studio (klassieke) werkruimten en Studio (klassieke) "Nieuwe" webservices. 

PowerShell classic kan worden geïnstalleerd naast Az of AzureRM voor zowel "nieuwe" als "klassieke" resourcetypen. Het wordt echter afgeraden om Az en AzureRM tegelijkertijd te laten installeren. Om te kiezen tussen Az en AzureRM raadt Microsoft Az aan voor alle toekomstige implementaties.  Meer informatie over Az versus AzureRM en het migratiepad in [inleiding tot de Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Volg de [installatie-instructies voor Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps)om met Az aan de slag te gaan.

## <a name="powershell-classic"></a><a name="classic"></a>PowerShell klassieker

Met de klassieke [PowerShell-module](https://aka.ms/amlps) van Studio (klassieke) PowerShell u resources beheren die zijn geïmplementeerd met behulp van het **klassieke implementatiemodel.** Deze bronnen omvatten Studio (klassieke) gebruikerselementen, "klassieke" webservices en "klassieke" webserviceeindpunten.

Microsoft raadt u echter aan het implementatiemodel resourcebeheer te gebruiken voor alle toekomstige resources om de implementatie en het beheer van resources te vereenvoudigen. Zie het artikel Azure Resource Manager versus [klassiek implementatieartikel](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) als u meer wilt weten over de implementatiemodellen.

Download het [releasepakket](https://github.com/hning86/azuremlps/releases) van GitHub en volg de [installatieinstructies om](https://github.com/hning86/azuremlps/blob/master/README.md)aan de slag te gaan met PowerShell classic. In de instructies wordt uitgelegd hoe u de gedownloade/uitgepakte DLL deblokkeren en vervolgens importeren in uw PowerShell-omgeving.

PowerShell classic kan worden geïnstalleerd naast Az of AzureRM voor zowel "nieuwe" als "klassieke" resourcetypen.

## <a name="powershell-support-table"></a><a name="support-table"></a>PowerShell-ondersteuningstabel


| | **Az** |  **PowerShell klassieker** |
| --- | --- | --- |
| Werkruimten maken/verwijderen | [Resource Manager-sjablonen](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Toezeggingsplannen voor werkruimtes beheren | [Nieuw-azmlcommitmentplan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Werkruimtegebruikers beheren |  | [Gebruikers van Add-AmlWorkspace](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Webservices beheren | [Nieuwe azmlwebservice](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nieuwe" webservices)|| [Nieuwe-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klassieke" webservices) |
| Eindpunten/sleutels voor webservice beheren |  [Get-azmlwebservicekey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gebruikersgegevenssets/getrainde modellen beheren| | [Get-Amldataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gebruikersexperimenten beheren |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Aangepaste modules beheren | | [Nieuw-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volledige documentatie van deze PowerShell module:
* [PowerShell klassieker](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
