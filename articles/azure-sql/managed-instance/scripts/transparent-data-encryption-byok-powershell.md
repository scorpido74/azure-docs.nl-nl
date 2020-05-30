---
title: 'Power shell: enable-your-own-Key (BYOK) TDE'
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over het configureren van een Azure SQL Managed instance om met behulp van Power shell (BYOK) Transparent Data Encryption (TDE) te gaan gebruiken voor versleuteling-at-rest.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 348b69933ae6b3706cf16642a138f1b00247cb5a
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219193"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption in SQL Managed instance met uw eigen sleutel vanuit Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Met dit Power shell-voorbeeld script wordt Transparent Data Encryption (TDE) geconfigureerd met een door de klant beheerde sleutel voor Azure SQL Managed instance, met behulp van een sleutel van Azure Key Vault. Dit wordt vaak een BYOK-scenario (proyour-own-Key) genoemd voor TDE. Zie [Azure SQL transparent Data Encryption met door de klant beheerde sleutel](../../database/transparent-data-encryption-byok-overview.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Een bestaand beheerd exemplaar. Zie [Power shell gebruiken om een beheerd exemplaar te maken](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Voor het lokaal gebruiken van Power shell of het gebruik van Azure Cloud Shell is Azure PowerShell 2.3.2 of een latere versie vereist. Als u een upgrade wilt uitvoeren, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps)of voert u het onderstaande voorbeeld script uit om de module voor de huidige gebruiker te installeren:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-scripts"></a>Voorbeeldscripts

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/overview)voor meer informatie over Azure PowerShell.

Extra Power shell-script voorbeelden voor SQL Managed instance vindt u in [Azure SQL Managed instance Power shell-scripts](../../database/powershell-script-content-guide.md).
