---
title: Minimale TLS-versie configureren-beheerd exemplaar
description: Meer informatie over het configureren van een minimale TLS-versie voor een beheerd exemplaar
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 7982cb80c253ec3966319528e39390920c653e38
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974259"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Minimale TLS-versie configureren in Azure SQL Managed instance
Met de instelling minimale versie van [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) kunnen klanten de versie van TLS beheren die wordt gebruikt door hun Azure SQL Managed instance.

Momenteel ondersteunen we TLS 1,0, 1,1 en 1,2. Als u een minimale TLS-versie instelt, worden nieuwe versies van TLS ondersteund. Bijvoorbeeld, bijvoorbeeld het kiezen van een TLS-versie die groter is dan 1,1. houdt in dat alleen verbindingen met TLS 1,1 en 1,2 worden geaccepteerd en dat TLS 1,0 wordt afgewezen. Nadat u hebt getest om te bevestigen dat uw toepassingen deze ondersteunen, wordt u aangeraden minimale TLS-versie in te stellen op 1,2, omdat deze oplossingen bevat voor beveiligings problemen die in vorige versies zijn gevonden en de hoogste versie van TLS wordt ondersteund in Azure SQL Managed instance.

Voor klanten met toepassingen die afhankelijk zijn van oudere versies van TLS, wordt u aangeraden de minimale TLS-versie in te stellen volgens de vereisten van uw toepassingen. Voor klanten die afhankelijk zijn van toepassingen om verbinding te maken met behulp van een niet-versleutelde verbinding, wordt u aangeraden geen minimale TLS-versie in te stellen. 

Zie [TLS-overwegingen voor SQL database connectiviteit](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)voor meer informatie.

Nadat u de minimale versie van TLS hebt ingesteld, mislukken aanmeldings pogingen van clients die een TLS-versie gebruiken die lager is dan de minimale TLS-versie van de server, mislukt met de volgende fout:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Minimale TLS-versie instellen via Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

Het volgende Power shell-script laat zien hoe `Get` en hoe `Set` de eigenschap **minimale TLS-versie** op instantie niveau is:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Minimale TLS-versie instellen via Azure CLI

> [!IMPORTANT]
> Alle scripts in deze sectie vereisen [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u de instelling **minimale TLS-versie** in een bash-shell wijzigt:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```
