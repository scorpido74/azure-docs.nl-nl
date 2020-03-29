---
title: RBAC-machtigingen die nodig zijn om mogelijkheden te gebruiken
titleSuffix: Azure Network Watcher
description: Ontdek welke Azure-machtigingen voor toegangsbeheer op basis van toegang zijn vereist om te werken met netwerkwatcher-mogelijkheden.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: f8743f19d6cd262ad140659be55a4fc57e842564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840550"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Op rollen gebaseerde toegangsbeheermachtigingen die nodig zijn om de mogelijkheden van Network Watcher te gebruiken

Met RBAC (Azure role-based access control) u alleen de specifieke acties toewijzen aan leden van uw organisatie die ze nodig hebben om hun toegewezen verantwoordelijkheden te voltooien. Als u de mogelijkheden van Network Watcher wilt gebruiken, moet het account waarmee u zich aanmeldt bij Azure, worden toegewezen aan de ingebouwde rollen [eigenaar,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner) [inzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)of [netwerkbijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) of die is toegewezen aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) waaraan de acties zijn toegewezen die worden vermeld voor elke netwerkwatcher-mogelijkheid in de volgende secties. Zie [Wat is Network Watcher?](network-watcher-monitoring-overview.md)

## <a name="network-watcher"></a>Network Watcher

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Een netwerkwatcher                                          |
| Microsoft.Network/networkWatchers/write                             | Een netwerkwatcher maken of bijwerken                             |
| Microsoft.Network/networkWatchers/delete                            | Een netwerkwatcher verwijderen                                       |

## <a name="nsg-flow-logs"></a>NSG-stroomlogboeken

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Een stroomlogboek configureren                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/actie         | Querystatus voor een stroomlogboek                                    |

## <a name="connection-troubleshoot"></a>Problemen met de verbinding oplossen

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Een test voor het oplossen van verbindingsproblemen starten
| Microsoft.Network/networkWatchers/queryProblemenProbleemopspoorresultaat/actie    | Queryresultaten van een test voor het oplossen van verbindingsproblemen                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Een test voor het oplossen van verbindingen uitvoeren                             |

## <a name="connection-monitor"></a>Verbindingsmonitor

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Een verbindingsmonitor starten                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Een verbindingsmonitor stoppen                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Een verbindingsmonitor opvragen                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Een verbindingsmonitor ophalen                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Een verbindingsmonitor maken                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Een verbindingsmonitor verwijderen                                    |

## <a name="packet-capture"></a>Pakketopname

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/actie | De status van een pakketopname opvragen                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Een pakketopname stoppen                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Een pakketopname krijgen                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Een pakketopname maken                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Een pakketopname verwijderen                                        |

## <a name="ip-flow-verify"></a>IP-stroomverificatie

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Een IP-stroom verifiëren                                              |

## <a name="next-hop"></a>Volgende hop

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/actie                    | Haal de volgende hop van een VM                                     |

## <a name="network-security-group-view"></a>Netwerkbeveiligingsgroep weergeven

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Beveiligingsgroepen weergeven                                           |

## <a name="topology"></a>Topologie

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topologie/actie                   | Topologie                                                   |

## <a name="reachability-report"></a>Reachability-rapport

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Een Azure-reachability-rapport opdoen                               |


## <a name="additional-actions"></a>Aanvullende acties

Network Watcher-mogelijkheden vereisen ook de volgende acties:

| Actie(en)                                                           | Beschrijving                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Lezen                                     | Wordt gebruikt om RBAC-roltoewijzingen en beleidsdefinities op te halen          |
| Microsoft.Resources/abonnementen/resourceGroepen/Lezen               | Wordt gebruikt om alle resourcegroepen in een abonnement op te sommen    |
| Microsoft.Storage/storageAccounts/Read                              | Wordt gebruikt om de eigenschappen voor het opgegeven opslagaccount op te halen   |
| Microsoft.Storage/storageAccounts/listServiceSas/Action, </br> Microsoft.Storage/storageAccounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Wordt gebruikt om sas -handtekeningen (shared access signatures) op te halen, zodat [u veilige toegang tot een opslagaccount kunt](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) openen en naar het opslagaccount schrijven |
| Microsoft.Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| Wordt gebruikt om in te loggen op de VM, een pakketopname uit te leggen en te uploaden naar een opslagaccount|
| Microsoft.Compute/virtualMachines/extensies/Lezen </br> Microsoft.Compute/virtualMachines/extensies/Schrijven| Wordt gebruikt om te controleren of de uitbreiding Network Watcher aanwezig is en indien nodig te installeren |
| Microsoft.Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Wordt gebruikt om toegang te krijgen tot virtuele machineschaalsets, pakketopnames te maken en deze te uploaden naar een opslagaccount|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensies/Schrijven| Wordt gebruikt om te controleren of de uitbreiding Network Watcher aanwezig is en indien nodig te installeren |
| Microsoft.Insights/alertRegels/*                                     | Wordt gebruikt voor het instellen van metrische waarschuwingen                                     |
| Microsoft.Support/*                                                 | Wordt gebruikt om ondersteuningstickets van Network Watcher te maken en bij te werken |
