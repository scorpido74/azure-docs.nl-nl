---
title: Apps beheren voor meerdere omgevingen
description: Azure Service Fabric-toepassingen kunnen worden uitgevoerd op clusters die variëren in grootte van één machine tot duizenden machines. In sommige gevallen wilt u uw toepassing anders configureren voor deze gevarieerde omgevingen. In dit artikel wordt uitgelegd hoe u per omgeving verschillende toepassingsparameters definieert.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196976"
---
# <a name="manage-applications-for-multiple-environments"></a>Toepassingen voor meerdere omgevingen beheren

Azure Service Fabric-clusters stellen u in staat clusters te maken met overal van één tot vele duizenden machines. In de meeste gevallen vindt u dat u uw toepassing moet implementeren in meerdere clusterconfiguraties: uw cluster voor lokale ontwikkeling, een gedeeld ontwikkelingscluster en uw productiecluster. Al deze clusters worden beschouwd als verschillende omgevingen waarin uw code moet worden uitgevoerd. Toepassingsbinairebestanden kunnen zonder wijzigingen over dit brede spectrum worden uitgevoerd, maar u wilt de toepassing vaak anders configureren.

Denk aan twee eenvoudige voorbeelden:
  - uw service luistert op een gedefinieerde poort, maar u hebt die poort nodig om anders te zijn in de omgevingen
  - u verschillende bindende referenties voor een database in de omgevingen moet verstrekken

## <a name="specifying-configuration"></a>Configuratie opgeven

De configuratie die u opgeeft, kan in twee categorieën worden onderverdeeld:

- Configuratie die van toepassing is op de manier waarop uw services worden uitgevoerd
  - Bijvoorbeeld het poortnummer voor een eindpunt of het aantal exemplaren van een service
  - Deze configuratie is opgegeven in het bestand van de toepassing of servicemanifest
- Configuratie die van toepassing is op uw toepassingscode
  - Bindende informatie voor een database
  - Deze configuratie kan worden geleverd via configuratiebestanden of omgevingsvariabelen

> [!NOTE]
> Niet alle kenmerken in de ondersteuningsparameters voor het toepassings- en servicemanifest.
> In die gevallen moet u vertrouwen op het vervangen van tekenreeksen als onderdeel van uw implementatieworkflow. In Azure DevOps u een https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens extensie gebruiken zoals Tokens vervangen: of in Jenkins u een scripttaak uitvoeren om de waarden te vervangen.
>

## <a name="specifying-parameters-during-application-creation"></a>Parameters opgeven tijdens het maken van toepassingen

Wanneer u een benoemde toepassingsinstanties maakt in Service Fabric, u parameters doorgeven. De manier waarop u dit doet, is afhankelijk van hoe u de toepassingsinstantie maakt.

  - In PowerShell [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) neemt de cmdlet de toepassingsparameters als hashtable.
  - Met behulp van [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) sfctl, De opdracht neemt parameters als een JSON string. Het install.sh script maakt gebruik van sfctl.
  - Visual Studio biedt u een set parameterbestanden in de map Parameters in het toepassingsproject. Deze parameterbestanden worden gebruikt bij het publiceren vanuit Visual Studio, met Azure DevOps Services of Azure DevOps Server. In Visual Studio worden de parameterbestanden doorgegeven aan het Deploy-FabricApplication.ps1-script.

## <a name="next-steps"></a>Volgende stappen
De volgende artikelen laten u zien hoe u een aantal van de hier beschreven concepten gebruiken:

- [Omgevingsvariabelen voor services in Service Fabric opgeven](service-fabric-how-to-specify-environment-variables.md)
- [Het poortnummer van een service opgeven met behulp van parameters in Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Configuratiebestanden parameteriseren](service-fabric-how-to-parameterize-configuration-files.md)

- [Referentie voor omgevingsvariabele](service-fabric-environment-variables-reference.md)
