---
title: Richtlijnen voor noodherstel voor Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Richtlijnen voor noodherstel voor Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966059"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Richtlijnen voor noodherstel voor gegevens in Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 biedt lokaal redundante opslag (LRS). Daarom zijn de gegevens in uw Data Lake Storage Gen1-account bestand tegen tijdelijke hardwarefouten in een datacenter via geautomatiseerde replica's. Dit zorgt voor duurzaamheid en hoge beschikbaarheid, voldoen aan de Data Lake Storage Gen1 SLA. In dit artikel vindt u richtlijnen voor het verder beschermen van uw gegevens tegen zeldzame regiostoringen of onbedoelde verwijderingen.

## <a name="disaster-recovery-guidance"></a>Richtlijnen voor herstel na noodgevallen
Het is essentieel dat elke klant een eigen plan voor herstel na noodgevallen voorbereidt. Lees de informatie in dit artikel om uw disaster recovery plan op te bouwen. Hier volgen enkele bronnen waarmee u uw eigen plan kunt maken.

* [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technische richtlijnen voor flexibiliteit van Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Aanbevolen procedures
We raden u aan uw kritieke gegevens te kopiëren naar een ander Data Lake Storage Gen1-account in een andere regio met een frequentie die is afgestemd op de behoeften van uw noodherstelplan. Er zijn verschillende methoden om gegevens te kopiëren, waaronder [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) en [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory is een handige service voor het regelmatig maken en implementeren van pijplijnen voor gegevensverplaatsing.

Als er een regionale storing optreedt, u vervolgens toegang krijgen tot uw gegevens in de regio waar de gegevens zijn gekopieerd. U het [Azure Service Health Dashboard](https://azure.microsoft.com/status/) controleren om de status van de Azure-service over de hele wereld te bepalen.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Herstelrichtlijnen voor gegevensbeschadiging en onbedoelde verwijdering
Hoewel Data Lake Storage Gen1 gegevenstolerantie biedt via geautomatiseerde replica's, voorkomt dit niet dat uw toepassing (of ontwikkelaars/gebruikers) gegevens corrumpert of per ongeluk wordt verwijderd.

### <a name="best-practices"></a>Aanbevolen procedures
Om onbedoelde verwijdering te voorkomen, raden we u aan eerst het juiste toegangsbeleid in te stellen voor uw Data Lake Storage Gen1-account.  Dit omvat het toepassen van [Azure-bronvergrendelingen](../azure-resource-manager/management/lock-resources.md) om belangrijke bronnen te vergrendelen en toegangsbeheer op account- en bestandsniveau toe te passen met behulp van de beschikbare [beveiligingsfuncties voor Gegevenslake Storage Gen1.](data-lake-store-security-overview.md) We raden u ook aan regelmatig kopieën van uw kritieke gegevens te maken met [ADLCopy,](data-lake-store-copy-data-azure-storage-blob.md) [Azure PowerShell](data-lake-store-get-started-powershell.md) of [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in een ander Data Lake Storage Gen1-account, -map of Azure-abonnement.  Dit kan worden gebruikt om gegevens te herstellen nadat ze beschadigd zijn geraakt of per ongeluk zijn verwijderd. Azure Data Factory is een handige service voor het regelmatig maken en implementeren van pijplijnen voor gegevensverplaatsing.

Organisaties kunnen diagnostische [logboekregistratie](data-lake-store-diagnostic-logs.md) voor hun Data Lake Storage Gen1-account ook inschakelen om controlepaden voor gegevenstoegang te verzamelen die informatie bevatten over wie mogelijk een bestand heeft verwijderd of bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
* [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)

