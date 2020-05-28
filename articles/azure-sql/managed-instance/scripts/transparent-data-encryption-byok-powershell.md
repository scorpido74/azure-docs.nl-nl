---
title: 'Power shell: enable-your-own-Key (BYOK) TDE'
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over het configureren van een Azure SQL Managed instance om met behulp van Power shell (BYOK Transparent Data Encryption) (TDE) voor versleuteling-at-rest te gaan gebruiken.
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
ms.openlocfilehash: cff98c0acfe06a9dbf9e3d7c7dae1b2411823d28
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051792"
---
# <a name="transparent-data-encryption-tde-in-a-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption (TDE) in een door SQL beheerd exemplaar met behulp van uw eigen sleutel vanuit Azure Key Vault
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

In dit voor beeld van een Power shell-script wordt Transparent Data Encryption (TDE) met door de klant beheerde sleutel geconfigureerd voor Azure SQL Managed instance, met behulp van een sleutel van Azure Key Vault. Dit wordt vaak een Bring Your Own Key scenario genoemd voor TDE. Zie [TDE Bring your own Key naar Azure SQL](../../database/transparent-data-encryption-byok-overview.md)voor meer informatie over de TDe met door de klant beheerde sleutels.

## <a name="prerequisites"></a>Vereisten

- Een bestaand exemplaar van SQL Managed. Zie [Power shell gebruiken voor het maken van een door Azure SQL beheerd exemplaar](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal gebruikt of Azure Cloud Shell gebruikt, is AZ Power Shell 2.3.2 of een nieuwere versie vereist. Als u een upgrade wilt uitvoeren, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps)of voert u het onderstaande voorbeeld script uit om de module voor de huidige gebruiker te installeren:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-scripts"></a>Voorbeeldscripts

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van SQL Managed instance Power shell-scripts vindt u in de [Azure SQL Managed instance Power shell-scripts](../../database/powershell-script-content-guide.md).
