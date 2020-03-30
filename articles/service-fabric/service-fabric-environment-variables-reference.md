---
title: Azure Service Fabric-omgevingsvariabelen
description: Meer informatie over omgevingsvariabelen in Azure Service Fabric. Bevat een referentie van een volledige lijst met variabelen en het gebruik ervan.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645681"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric-omgevingsvariabelen

Service Fabric heeft ingebouwde omgevingsvariabelen die zijn ingesteld voor elke serviceinstantie. De volledige lijst met omgevingsvariabelen vindt u:

| Omgevingsvariabele                         | Beschrijving                                                            | Voorbeeld                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | De naam van de stofuri van de toepassing                                 | stof:/MyApplication                                                |
| Fabric_CodePackageName                       | De naam van het codepakket waartoe het proces behoort              | Code                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Het ip-adres of FQDN van het eindpunt                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Poortnummer voor het eindpunt                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Logboekmap                                                             | C:\\\\\\\\Gegevens\\\\\\\\_App\\\\_Node_0 MyApplicationType_App12 logboek      |
| Fabric_Folder_App_Temp                       | De map Temp                                                            | C:\\\\\\\\Gegevens\\\\\\\\_App\\\\_Node_0 MyApplicationType_App12 temp _App     |
| Fabric_Folder_App_Work                       | Werkmap                                                            | C:\\\\\\\\_App\\\\\\\\_App\\\\_Node_0 MyApplicationType_App12 werken     |
| Fabric_Folder_Application                    | De thuismap toepassingen                                           | C:\\\\\\\\Gegevens\\\\\\\\_App _Node_0 MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Een bool die aangeeft of het proces een container is                   | false                                                                |
| Fabric_NodeId                                | De node-id van het knooppunt waarop het proces wordt uitgevoerd                            | bf865279ba2777deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Het IP- of FQDN van het knooppunt, zoals gespecificeerd in het clustermanifestbestand. | localhost of 10.0.0.1                                                |
| Fabric_NodeName                              | De naam van het knooppunt dat het proces uitvoert                          | _Node_0                                                              |
| Fabric_ServiceName                           | De naam van de fabric uri van de service, als de service wordt gehost in de ExclusiveProcess-modus. Deze variabele waarde is alleen beschikbaar als u de service maakt met ServicePackageActivationMode ExclusiveProcess.  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | De ServicePackageActivationId                                         | Een GUID                                                               |
| Fabric_ServicePackageName                    | Naam van het servicepakket waar het proces deel van uitmaakt                     | Web1Pkg                                                              |

Interne omgevingsvariabelen die worden gebruikt door runtime van servicefabric:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
