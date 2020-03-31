---
title: Kies een Azure-oplossing voor gegevensoverdracht| Microsoft Documenten
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevensoverdracht op basis van gegevensgroottes en beschikbare netwerkbandbreedte in uw omgeving
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ada96d0d4b167e30cb6e271aa02d0d399a9ae7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303083"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Kies een Azure-oplossing voor gegevensoverdracht

In dit artikel vindt u een overzicht van enkele van de meest voorkomende Azure-oplossingen voor gegevensoverdracht. Het artikel verwijst ook naar aanbevolen opties, afhankelijk van de netwerkbandbreedte in uw omgeving en de grootte van de gegevens die u wilt overdragen.

## <a name="types-of-data-movement"></a>Typen gegevensverplaatsing

Gegevensoverdracht kan offline of via de netwerkverbinding plaatsvinden. Kies uw oplossing, afhankelijk van uw:

- **Gegevensgrootte** - Omvang van de gegevens die bestemd zijn voor overdracht;
- **Overdrachtsfrequentie** - Eenmalige of periodieke gegevensopname, en
- **Netwerk** – Bandbreedte beschikbaar voor gegevensoverdracht in uw omgeving.

De gegevensverplaatsing kan van de volgende typen zijn:

- **Offline overdracht met verzendbare apparaten** - Gebruik fysieke verzendbare apparaten wanneer u offline eenmalige bulkgegevensoverdracht wilt doen. Microsoft stuurt u een schijf of een beveiligd gespecialiseerd apparaat. U ook uw eigen schijven kopen en verzenden. U kopieert gegevens naar het apparaat en verzendt deze vervolgens naar Azure waar de gegevens worden geüpload.  De beschikbare opties voor deze case zijn Data Box Disk, Data Box, Data Box Heavy en Import/Export (gebruik je eigen schijven).

- **Netwerkoverdracht** : u draagt uw gegevens over naar Azure via uw netwerkverbinding. Dit kan op vele manieren.

    - **Grafische interface** - Als u af en toe slechts een paar bestanden overdraagt en de gegevensoverdracht niet hoeft te automatiseren, u een grafische interfacetool kiezen, zoals Azure Storage Explorer of een webgebaseerdverkenningshulpprogramma in Azure-portal.
    - **Gescripte of programmatische overdracht** - U geoptimaliseerde softwaretools gebruiken die wij onze REST API's/SDK's rechtstreeks leveren of bellen. De beschikbare scriptbare hulpprogramma's zijn AzCopy, Azure PowerShell en Azure CLI. Gebruik voor programmatische interface een van de SDK's voor .NET, Java, Python, Node/JS, C++, Go, PHP of Ruby.
    - **On-premises apparaten** - Wij leveren u een fysiek of virtueel apparaat dat zich in uw datacenter bevindt en de gegevensoverdracht via het netwerk optimaliseert. Deze apparaten bieden ook een lokale cache van veelgebruikte bestanden. Het fysieke apparaat is de Data Box Edge en het virtuele apparaat is de Data Box Gateway. Beide worden permanent in uw gebouw uitgevoerd en maken verbinding met Azure via het netwerk.
    - **Beheerde gegevenspijplijn** - U een cloudpijplijn instellen om regelmatig bestanden over te zetten tussen verschillende Azure-services, on-premises of een combinatie van twee. Gebruik Azure Data Factory voor het instellen en beheren van gegevenspijplijnen en het verplaatsen en transformeren van gegevens voor analyse.

De volgende visual illustreert de richtlijnen voor het kiezen van de verschillende Azure-hulpprogramma's voor gegevensoverdracht, afhankelijk van de netwerkbandbreedte die beschikbaar is voor overdracht, gegevensgrootte die is bedoeld voor overdracht en frequentie van de overdracht.

![Azure-hulpprogramma's voor gegevensoverdracht](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**De bovengrenzen van de offline transferapparaten - Data Box Disk, Data Box en Data Box Heavy kunnen worden uitgebreid door meerdere bestellingen van een apparaattype te plaatsen.*

## <a name="selecting-a-data-transfer-solution"></a>Een oplossing voor gegevensoverdracht selecteren

Beantwoord de volgende vragen om een oplossing voor gegevensoverdracht te selecteren:

- Is uw beschikbare netwerkbandbreedte beperkt of niet-bestaand en wilt u grote gegevenssets overbrengen?
  
    Zo ja, zie: [Scenario 1: Grote gegevenssets overbrengen zonder of lage netwerkbandbreedte.](storage-solution-large-dataset-low-network.md)
- Wilt u grote datasets overleggen via het netwerk en heeft u een matige tot hoge netwerkbandbreedte?

    Zo ja, zie: [Scenario 2: Grote gegevenssets overbrengen met een matige tot hoge netwerkbandbreedte.](storage-solution-large-dataset-moderate-high-network.md)
- Wilt u af en toe slechts een paar bestanden over het netwerk overbrengen?

    Zo ja, zie [Scenario 3: Kleine gegevenssets overbrengen met beperkte tot matige netwerkbandbreedte.](storage-solution-small-dataset-low-moderate-network.md)
- Bent u op zoek naar point-in-time dataoverdracht op regelmatige tijdstippen?

    Zo ja, gebruik dan de gescripte/programmatische opties die worden beschreven in [scenario 4: Periodieke gegevensoverdrachten](storage-solution-periodic-data-transfer.md).
- Bent u op zoek naar voortdurende, continue gegevensoverdracht?

    Zo ja, gebruik dan de opties in [scenario 4: Periodieke gegevensoverdrachten](storage-solution-periodic-data-transfer.md).

## <a name="data-transfer-feature-in-azure-portal"></a>Functie gegevensoverdracht in Azure-portal

U ook naar uw Azure Storage-account in Azure-portal gaan en de functie **Gegevensoverdracht** selecteren. Zorg voor de netwerkbandbreedte in uw omgeving, de grootte van de gegevens die u wilt overdragen en de frequentie van gegevensoverdracht. U ziet de optimale oplossingen voor gegevensoverdracht die overeenkomen met de door u verstrekte informatie. 

## <a name="next-steps"></a>Volgende stappen

- [Een inleiding tot Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Lees een overzicht van AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Azure PowerShell gebruiken met Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Snelstart: blobs maken, downloaden en weergeven met Azure CLI](../blobs/storage-quickstart-blobs-cli.md)
- Meer informatie over:

    - [Azure Data Box, Azure Data Box Disk en Azure Data Box Heavy voor offline overdrachten](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box Gateway en Azure Data Box Edge voor online overdrachten.](https://docs.microsoft.com/azure/databox-online/)
- [Meer informatie over wat Azure Data Factory is.](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)
- Gebruik de REST API's om gegevens over te dragen

    - [In .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage)
