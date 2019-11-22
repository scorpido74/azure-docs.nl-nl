---
title: RBAC-machtigingen die zijn vereist voor het gebruik van mogelijkheden
titleSuffix: Azure Network Watcher
description: Meer informatie over op rollen gebaseerde toegangs beheer machtigingen van Azure zijn vereist om met Network Watcher mogelijkheden te werken.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 9d56865a558f027a044e990a2da697dc53e7a311
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277702"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Op rollen gebaseerde toegangs beheer machtigingen die zijn vereist voor het gebruik van Network Watcher mogelijkheden

Met op rollen gebaseerd toegangs beheer (RBAC) van Azure kunt u alleen de specifieke acties toewijzen aan leden van uw organisatie die ze nodig hebben om hun toegewezen verantwoordelijkheden te volt ooien. Als u Network Watcher mogelijkheden wilt gebruiken, moet het account waarmee u zich aanmeldt bij Azure met, worden toegewezen aan de ingebouwde rollen [eigenaar](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)of [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) of worden toegewezen aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) waaraan de acties worden toegewezen die worden vermeld voor elke Network Watcher functionaliteit in de volgende secties. Zie [Wat is Network Watcher?](network-watcher-monitoring-overview.md)voor meer informatie over de mogelijkheden van Network Watcher.

## <a name="network-watcher"></a>Network Watcher

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Een netwerk-Watcher ophalen                                          |
| Microsoft.Network/networkWatchers/write                             | Een netwerk-Watcher maken of bijwerken                             |
| Microsoft.Network/networkWatchers/delete                            | Een netwerk-Watcher verwijderen                                       |

## <a name="nsg-flow-logs"></a>NSG-stroom logboeken

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Een stroom logboek configureren                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Query status voor een stroom logboek                                    |

## <a name="connection-troubleshoot"></a>Problemen met verbinding oplossen

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Problemen met testen van verbinding starten
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Query resultaten van de test verbinding oplossen                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Een test voor verbinding oplossen uitvoeren                             |

## <a name="connection-monitor"></a>Verbindings monitor

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Verbindings monitor starten                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Verbindings monitor stoppen                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Query's uitvoeren op een verbindings monitor                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Een verbindings monitor ophalen                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Een verbindingsmonitor maken                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Een verbindings monitor verwijderen                                    |

## <a name="packet-capture"></a>Pakketopname

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | De status van een pakket opname opvragen                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Een pakket opname stoppen                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Pakket opname ophalen                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Een pakket opname maken                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Een pakket opname verwijderen                                        |

## <a name="ip-flow-verify"></a>IP-stroomverificatie

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Een IP-stroom controleren                                              |

## <a name="next-hop"></a>Volgende hop

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | De volgende hop van een virtuele machine ophalen                                     |

## <a name="network-security-group-view"></a>Netwerkbeveiligingsgroep weergeven

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Beveiligings groepen weer geven                                           |

## <a name="topology"></a>Topologie

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Topologie ophalen                                                   |

## <a name="reachability-report"></a>Bereik baarheids rapport

| Actie                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Een Azure-bereik baarheids rapport ophalen                               |


## <a name="additional-actions"></a>Aanvullende acties

Voor Network Watcher mogelijkheden zijn ook de volgende acties vereist:

| Actie (s)                                                           | Beschrijving                                                    |
| ---------                                                           | -------------                                                  |
| Micro soft. Authorization/\*/Read                                     | Wordt gebruikt voor het ophalen van RBAC-Roltoewijzingen en beleids definities          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | Gebruikt voor het inventariseren van alle resource groepen in een abonnement    |
| Microsoft.Storage/storageAccounts/Read                              | Wordt gebruikt om de eigenschappen van het opgegeven opslag account op te halen   |
| Micro soft. Storage/Storage accounts/listServiceSas/Action, </br> Micro soft. Storage/Storage accounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Gebruikt voor het ophalen van Shared Access signatures (SAS) om [beveiligde toegang tot het opslag account](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) in te scha kelen en te schrijven naar het opslag account |
| Micro soft. Compute/informatie/lezen, </br> Microsoft.Compute/virtualMachines/Write| Wordt gebruikt om u aan te melden bij de VM, een pakket vast te leggen en dit te uploaden naar een opslag account|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Wordt gebruikt om te controleren of Network Watcher extensie aanwezig is en zo nodig installeren |
| Micro soft. Compute/virtualMachineScaleSets/lezen, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Wordt gebruikt om toegang te krijgen tot de schaal sets van virtuele machines, pakketten vastleggen en uploaden naar het opslag account|
| Micro soft. Compute/virtualMachineScaleSets/uitbrei dingen/lezen, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Wordt gebruikt om te controleren of Network Watcher extensie aanwezig is en zo nodig installeren |
| Microsoft.Insights/alertRules/*                                     | Gebruikt voor het instellen van metrische waarschuwingen                                     |
| Microsoft.Support/*                                                 | Wordt gebruikt om ondersteunings tickets te maken en bij te werken van Network Watcher |
