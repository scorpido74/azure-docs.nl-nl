---
title: Releaseopmerkingen
description: Releasenotes voor Azure Synapse Analytics (werkruimten)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423857"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Releasenotes voor Azure Synapse Analytics (preview)

In dit artikel worden beperkingen en problemen met Azure Synapse Analytics (werkruimten) beschreven. Zie [Wat is Azure Synapse Analytics (werkruimten) voor](overview-what-is.md) gerelateerde informatie

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (werkruimten) 

### <a name="azure-synapse-cli"></a>Azure Synapsen CLI

- Probleem en impact van de klant: werkruimten die door SDK zijn gemaakt, kunnen Synapse Studio niet starten

- Tijdelijke oplossing: Voer de volgende stappen uit: 
  1.    Werkruimte maken `az synapse workspace create`door 2 uit te voeren.    Beheerde identiteits-id extraheren door uit te voeren`$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Werkruimte toevoegen als rol aan opslagaccount door uit te voeren` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Firewallregel toevoegen door uit te voeren` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>Volgende stappen

* [Een werkruimte maken](quickstart-create-workspace.md)
* [Synapse Studio gebruiken](quickstart-synapse-studio.md)
* [Een SQL-groep maken](quickstart-create-sql-pool.md)
* [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
* [Een Apache Spark-pool maken](quickstart-create-apache-spark-pool.md)