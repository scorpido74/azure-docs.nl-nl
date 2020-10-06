---
title: 'Releaseopmerkingen: Azure Synapse Analytics (preview van werkruimten)'
description: Releaseopmerkingen voor Azure Synapse Analytics (preview van werkruimten)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031667"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Releaseopmerkingen van Azure Synapse Analytics (preview van werkruimten)

In dit artikel worden beperkingen en problemen in Azure Synapse Analytics (werkruimten) beschreven. Raadpleeg [Wat is Azure Synapse Analytics (werkruimten)?](overview-what-is.md) voor gerelateerde informatie

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- Probleem en gevolgen voor de klant: Synapse Studio kan niet worden gestart in werkruimten die zijn gemaakt met SDK

- Tijdelijke oplossing: Voltooi de volgende stappen: 
  1.    Maak een werkruimte door `az synapse workspace create` uit te voeren.
  2.    Extraheer de id van de beheerde identiteit door `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` uit te voeren.
  3.    Voeg de werkruimte als rol toe aan het opslagaccount door ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` uit te voeren.
  4.    Voeg een firewallregel toe door ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure Synapse?](overview-what-is.md)
* [Aan de slag](get-started.md)
* [Veelgestelde vragen](overview-faq.md)
