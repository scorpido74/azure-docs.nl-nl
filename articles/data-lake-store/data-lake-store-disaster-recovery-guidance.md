---
title: Richt lijnen voor herstel na nood gevallen voor Azure Data Lake Storage Gen1 | Microsoft Docs
description: Richt lijnen voor herstel na nood gevallen voor Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966059"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Richt lijnen voor herstel na nood gevallen voor gegevens in Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 biedt lokaal redundante opslag (LRS). De gegevens in uw Data Lake Storage Gen1-account zijn daarom robuust tegen tijdelijke hardwarefouten binnen een Data Center via geautomatiseerde replica's. Dit zorgt voor duurzaamheid en hoge Beschik baarheid, die voldoen aan de Data Lake Storage Gen1 SLA. Dit artikel bevat richt lijnen voor het verder beschermen van uw gegevens tegen zeldzame regionale onderbrekingen of onopzettelijke verwijderingen.

## <a name="disaster-recovery-guidance"></a>Richtlijnen voor herstel na noodgeval
Het is essentieel dat elke klant een eigen plan voor herstel na noodgevallen voorbereidt. Lees de informatie in dit artikel om uw plan voor herstel na nood gevallen te maken. Hier volgen enkele bronnen waarmee u uw eigen plan kunt maken.

* [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technische richtlijnen voor flexibiliteit van Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Aanbevolen procedures
U wordt aangeraden uw kritieke gegevens naar een andere Data Lake Storage Gen1 account in een andere regio te kopiëren met een frequentie die is afgestemd op de behoeften van uw plan voor herstel na nood gevallen. Er zijn verschillende methoden om gegevens te kopiëren, waaronder [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) en [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory is een handige service voor het regelmatig maken en implementeren van pijplijnen voor gegevensverplaatsing.

Als er een regionale storing optreedt, kunt u vervolgens toegang krijgen tot uw gegevens in de regio waar de gegevens zijn gekopieerd. U kunt het [Azure service Health-dash board](https://azure.microsoft.com/status/) bewaken om de status van de Azure-service wereld wijd te bepalen.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Herstelrichtlijnen voor gegevensbeschadiging en onbedoelde verwijdering
Hoewel Data Lake Storage Gen1 gegevens tolerantie biedt via geautomatiseerde replica's, wordt hiermee niet voor komen dat uw toepassing (of ontwikkel aars/gebruikers) gegevens beschadigt of per ongeluk worden verwijderd.

### <a name="best-practices"></a>Aanbevolen procedures
Als u onbedoeld verwijderen wilt voor komen, wordt u aangeraden eerst het juiste toegangs beleid voor uw Data Lake Storage Gen1-account in te stellen.  Dit geldt ook voor het Toep assen van [Azure-resource vergrendelingen](../azure-resource-manager/management/lock-resources.md) voor het vergren delen van belang rijke resources, en het Toep assen van toegangs beheer op basis van de beschik bare [Data Lake Storage gen1 beveiligings functies](data-lake-store-security-overview.md). We raden u ook aan om regel matig kopieën te maken van uw kritieke gegevens met behulp van [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) of [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in een ander data Lake Storage gen1 account, map of Azure-abonnement.  Dit kan worden gebruikt om gegevens te herstellen nadat ze beschadigd zijn geraakt of per ongeluk zijn verwijderd. Azure Data Factory is een handige service voor het regelmatig maken en implementeren van pijplijnen voor gegevensverplaatsing.

Organisaties kunnen ook [Diagnostische logboek registratie](data-lake-store-diagnostic-logs.md) inschakelen voor hun data Lake Storage gen1-account voor het verzamelen van gegevens toegangscontrole traject die informatie bevat over wie mogelijk een bestand heeft verwijderd of bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
* [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)

