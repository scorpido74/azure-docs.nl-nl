---
title: 'PowerShell: BYOK TDE inschakelen - Azure SQL Database Managed Instance '
description: Meer informatie over het configureren van een Azure SQL Managed Instance om BYOK Transparent Data Encryption (TDE) te gebruiken voor versleuteling in rust met PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691406"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption beheren in een beheerd exemplaar met behulp van uw eigen sleutel uit Azure Key Vault

In dit PowerShell-scriptvoorbeeld configureert Ude (Transparent Data Encryption) met een door de klant beheerde sleutel voor Azure SQL Managed Instance, met behulp van een sleutel uit Azure Key Vault. Dit wordt vaak aangeduid als een Bring Your Own Key scenario voor TDE. Zie [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md)voor meer informatie over de TDE met door de klant beheerde sleutel.

## <a name="prerequisites"></a>Vereisten

- Een bestaand beheerde instantie. Zie [PowerShell gebruiken om een door Azure SQL Database beheerde instantie te maken.](sql-database-create-configure-managed-instance-powershell.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Voor het lokaal gebruiken van PowerShell of het gebruik van Azure Cloud Shell is AZ PowerShell 2.3.2 of een latere versie vereist. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps)of voer het onderstaande voorbeeldscript uit om de module voor de huidige gebruiker te installeren als u wilt upgraden:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-scripts"></a>Voorbeeldscripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../sql-database-powershell-samples.md).
