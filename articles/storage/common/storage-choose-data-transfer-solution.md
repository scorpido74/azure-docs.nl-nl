---
title: Een Azure-oplossing kiezen voor gegevens overdracht | Microsoft Docs
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevens overdracht op basis van de gegevens grootte en de beschik bare netwerk bandbreedte in uw omgeving
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 838d65da90ec0daef69375e5a75bcb497a0c3512
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900393"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Een Azure-oplossing kiezen voor gegevens overdracht

Dit artikel bevat een overzicht van een aantal algemene oplossingen voor Azure-gegevens overdracht. Het artikel is ook gekoppeld aan de aanbevolen opties, afhankelijk van de netwerk bandbreedte in uw omgeving en de grootte van de gegevens die u wilt overdragen.

## <a name="types-of-data-movement"></a>Typen gegevens verplaatsing

Gegevens overdracht kan offline of via de netwerk verbinding zijn. Kies uw oplossing, afhankelijk van uw:

- **Gegevens grootte** : grootte van de gegevens die bestemd zijn voor overdracht,
- **Overdrachts frequentie** -eenmalige of periodieke gegevens opname, en
- **Netwerk** : band breedte die beschikbaar is voor gegevens overdracht in uw omgeving.

De gegevens verplaatsing kan van de volgende typen zijn:

- **Offline overdracht met behulp van shippable-apparaten** : fysieke shippable-apparaten gebruiken wanneer u offline een eenmalige bulk gegevens overdracht wilt uitvoeren. Micro soft stuurt u een schijf of een beveiligd gespecialiseerd apparaat. U kunt ook uw eigen schijven kopen en verzenden. U kopieert gegevens naar het apparaat en verzendt het naar Azure waar de gegevens worden geüpload.  De beschik bare opties voor dit geval zijn Data Box Disk, Data Box, Data Box Heavy en importeren/exporteren (gebruik uw eigen schijven).

- **Netwerk overdracht** : u brengt uw gegevens over naar Azure via uw netwerk verbinding. Dit kan op verschillende manieren worden gedaan.

    - **Grafische interface** : als u af en toe slechts enkele bestanden overbrengt en de gegevens overdracht niet hoeft te automatiseren, kunt u kiezen voor een grafisch interface programma zoals Azure Storage Explorer of een hulp programma voor het verkennen van het web in azure Portal.
    - **Scripted of programmatische overdracht** : u kunt geoptimaliseerde software hulpprogramma's gebruiken die u rechtstreeks aanbiedt of aanroept. De beschik bare hulpprogram ma's die scriptbaar zijn, zijn AzCopy, Azure PowerShell en Azure CLI. Gebruik voor een programmatische interface een van de Sdk's voor .NET, Java, Python, node/JS C++,, go, PHP of Ruby.
    - **On-premises apparaten** : we bieden u een fysiek of virtueel apparaat dat zich in uw Data Center bevindt en optimaliseert de gegevens overdracht via het netwerk. Deze apparaten bieden ook een lokale cache met veelgebruikte bestanden. Het fysieke apparaat is de Data Box Edge en het virtuele apparaat is de Data Box Gateway. Zowel in uw lokale als permanent worden uitgevoerd en via het netwerk verbinding maken met Azure.
    - **Pijp lijn voor beheerde gegevens** : u kunt een Cloud pijplijn instellen om regel matig bestanden over te dragen tussen verschillende Azure-Services, on-premises of een combi natie van twee. Gebruik Azure Data Factory voor het instellen en beheren van gegevens pijplijnen en het verplaatsen en transformeren van gegevens voor analyse.

De volgende Visual illustreert de richt lijnen voor het kiezen van de verschillende hulpprogram ma's voor gegevens overdracht van Azure, afhankelijk van de beschik bare netwerk bandbreedte voor overdracht, gegevens grootte die is bedoeld voor overdracht en de frequentie van de overdracht.

![Hulpprogram ma's voor Azure-gegevens overdracht](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**De maximale limieten van de apparaten voor offline overdracht-Data Box Disk, Data Box en Data Box Heavy kunnen worden uitgebreid door meerdere orders van het apparaattype te plaatsen.*

## <a name="selecting-a-data-transfer-solution"></a>Een oplossing voor gegevens overdracht selecteren

Beantwoord de volgende vragen om een oplossing voor gegevens overdracht te selecteren:

- Is uw beschik bare netwerk bandbreedte beperkt of niet bestaande en wilt u grote gegevens sets overdragen?
  
    Zo ja, zie: [Scenario 1: Overdracht van grote gegevens sets zonder of lage netwerk](storage-solution-large-dataset-low-network.md)bandbreedte.
- Wilt u grote gegevens sets via het netwerk overdragen en hebt u een gemiddeld tot hoge netwerk bandbreedte?

    Zo ja, zie: [Scenario 2: Overzetten van grote gegevens sets met gemiddeld naar](storage-solution-large-dataset-moderate-high-network.md)hoge netwerk bandbreedte.
- Wilt u af en toe slechts een paar bestanden overzetten via het netwerk?

    Zo ja, Zie [scenario 3: De overdracht van kleine gegevens sets met een beperkt](storage-solution-small-dataset-low-moderate-network.md)aantal netwerk bandbreedte.
- Zoekt u regel matig naar tijd gegevens overdracht?

    Zo ja, gebruik dan de scripted/programmatische opties die worden [beschreven in scenario 4: Periodieke gegevens overdracht](storage-solution-periodic-data-transfer.md).
- Bent u op zoek naar voortdurende, doorlopende gegevens overdracht?

    Zo ja, gebruikt u de opties [in scenario 4: Periodieke gegevens overdracht](storage-solution-periodic-data-transfer.md).
 

## <a name="data-transfer-feature-in-azure-portal"></a>Functie voor gegevens overdracht in Azure Portal

U kunt ook naar uw Azure Storage-account gaan in Azure Portal en de functie voor **gegevens overdracht** selecteren. Geef de netwerk bandbreedte op in uw omgeving, de grootte van de gegevens die u wilt overdragen en de frequentie van gegevens overdracht. U ziet de optimale oplossingen voor gegevens overdracht die overeenkomen met de informatie die u hebt verstrekt. 

## <a name="next-steps"></a>Volgende stappen

- [Krijg een inleiding tot Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Lees een overzicht van AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Azure PowerShell gebruiken met Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Azure CLI gebruiken met Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Meer informatie over:

    - [Azure data box, Azure data Box disk en Azure data Box Heavy voor offline overdrachten](https://docs.microsoft.com/azure/databox/).
    - [Azure data Box gateway en Azure data Box Edge voor online overdrachten](https://docs.microsoft.com/azure/databox-online/).
- [Meer informatie over Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- De REST-Api's gebruiken om gegevens over te dragen

    - [In .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage)
