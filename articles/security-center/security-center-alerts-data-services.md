---
title: Detectie van bedreigingen voor gegevens Services in Azure Security Center | Microsoft Docs
description: Dit artikel bevat de beschik bare Data Services-waarschuwingen in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665731"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detectie van bedreigingen voor gegevens Services in Azure Security Center

 Azure Security Center analyseert de logboeken van de services voor gegevens opslag en activeert waarschuwingen wanneer een bedreiging voor uw gegevens bronnen wordt gedetecteerd. In dit artikel vindt u de waarschuwingen die Security Center gegenereerd voor de volgende services:

* [Azure SQL Database en Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database en SQL Data Warehouse<a name="data-sql"></a>

Advanced Threat Protection voor Azure SQL Database detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

U ziet waarschuwingen wanneer er verdachte database activiteiten, potentiële kwetsbaar heden of SQL-injectie aanvallen zijn, evenals afwijkende database toegang en query patronen.

Advanced Threat Protection voor Azure SQL Database en SQL maakt deel uit van het geïntegreerde pakket voor geavanceerde [gegevens beveiliging (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) voor geavanceerde SQL-beveiligings mogelijkheden, waaronder Azure SQL-data bases, Azure SQL database beheerde instanties, Azure SQL Data Warehouse data bases en SQL-servers in azure virtual machines.

Zie voor meer informatie:

* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor SQL-servers in azure Virtual Machines](security-center-iaas-advanced-data.md)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor SQL Database en SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure Storage<a name="azure-storage"></a>

Geavanceerde bedreigings beveiliging voor opslag (momenteel alleen beschikbaar voor Blob Storage) detecteert ongebruikelijke en mogelijk schadelijke pogingen om opslag accounts te openen of misbruik te maken. Deze beveiligingslaag biedt u de mogelijkheid om bedreigingen te verhelpen zonder dat u een beveiligings expert hoeft te zijn, en helpt u bij het beheren van uw systemen voor beveiligings bewaking.

>[!NOTE]
>Advanced Threat Protection voor opslag is momenteel niet beschikbaar in azure Government-en soevereine Cloud regio's.

Zie voor meer informatie:

* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Storage](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

De Azure Cosmos DB waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om Azure Cosmos DB accounts te openen of misbruik te maken.

Zie voor meer informatie:

* [Advanced Threat Protection voor Azure Cosmos DB (preview-versie)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Cosmos DB (preview-versie)](alerts-reference.md#alerts-azurecosmos)
