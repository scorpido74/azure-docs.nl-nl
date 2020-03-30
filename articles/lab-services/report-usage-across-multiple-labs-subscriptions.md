---
title: Azure DevTest Labs-gebruik in meerdere labs en abonnementen
description: Meer informatie over het melden van Azure DevTest Labs-gebruik in meerdere labs en abonnementen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169186"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Azure DevTest Labs-gebruik rapporteren in meerdere labs en abonnementen

De meeste grote organisaties willen het gebruik van resources bijhouden om effectiever te zijn met deze resources door trends en uitschieters in het gebruik te visualiseren. Op basis van resourcegebruik kunnen de labeigenaren of -beheerders de labs aanpassen om het gebruik en de kosten van resources te [verbeteren.](https://docs.microsoft.com/azure/billing/billing-getting-started) In Azure DevTest Labs u het gebruik van resources per lab downloaden, zodat u de gebruikspatronen dieper bekijken. Deze gebruikspatronen kunnen helpen bij het lokaliseren van wijzigingen om de efficiëntie te verbeteren. De meeste ondernemingen willen zowel individueel laboratoriumgebruik als algemeen gebruik in [meerdere laboratoria en abonnementen.](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

In dit artikel wordt besproken hoe u informatie over resourcegebruik in meerdere labs en abonnementen verwerken.

![Gebruik rapporteren](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Individueel laboratoriumgebruik

In dit gedeelte wordt besproken hoe u het gebruik van resources voor één lab exporteren.

Voordat u het gebruik van resources van DevTest Labs exporteren, moet u een Azure Storage-account instellen zodat de verschillende bestanden die de gebruiksgegevens bevatten, kunnen worden opgeslagen. Er zijn twee veelvoorkomende manieren om de export van gegevens uit te voeren:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* De PowerShell Az.Resource-module [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) met de actie van `exportResourceUsage`, de labresource-ID en de benodigde parameters. 

    Het artikel [over het exporteren of verwijderen van persoonsgegevens](personal-data-delete-export.md) bevat een voorbeeld van PowerShell-script met gedetailleerde informatie over de gegevens die worden geëxporteerd. 

    > [!NOTE]
    > De datumparameter bevat geen tijdstempel, dus de gegevens bevatten alles vanaf middernacht op basis van de tijdzone waar het lab zich bevindt.

Zodra de export is voltooid, zijn er meerdere CSV-bestanden in de blob-opslag met de verschillende brongegevens.
  
Momenteel zijn er twee CSV-bestanden:

* *virtualmachines.csv* - bevat informatie over de virtuele machines in het lab
* *disks.csv* - bevat informatie over de verschillende schijven in het lab 

Deze bestanden worden opgeslagen in de *blobcontainer voor labresourceusage* onder de labnaam, lab unieke ID, uitgevoerde datum en volledige of de begindatum die is gebaseerd op de exportaanvraag. Een voorbeeld blob structuur zou zijn:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Gebruik exporteren voor alle laboratoria

Als u de gebruiksgegevens voor meerdere laboratoria wilt exporteren, u overwegen 

* [Azure-functies](https://docs.microsoft.com/azure/azure-functions/), beschikbaar in vele talen, waaronder PowerShell, of 
* [Azure Automation runbook,](https://docs.microsoft.com/azure/automation/)gebruik PowerShell, Python of een aangepaste grafische ontwerper om de exportcode te schrijven.

Met behulp van deze technologieën u de afzonderlijke laboratoriumexport op alle laboratoria uitvoeren op een specifieke datum en tijd. 

Uw Azure-functie moet de gegevens naar de opslag op langere termijn pushen. Bij het exporteren van gegevens voor meerdere laboratoria kan de export enige tijd duren. Om te helpen met de prestaties en de mogelijkheid van duplicatie van informatie te verminderen, raden we aan om elk lab parallel uit te voeren. Voer Azure-functies asynchroon uit om parallellisme te bereiken. Profiteer ook van de timertrigger die Azure-functies bieden.

## <a name="using-a-long-term-storage"></a>Met behulp van een langdurige opslag

Een opslag op lange termijn consolideert de exportinformatie uit verschillende laboratoria in één gegevensbron. Een ander voordeel van het gebruik van de opslag op lange termijn is de mogelijkheid om de bestanden van de opslagrekening te verwijderen om duplicatie en kosten te verminderen. 

De opslag op lange termijn kan worden gebruikt om tekstmanipulatie uit te vullen, bijvoorbeeld: 

* het toevoegen van vriendelijke namen
* complexe groeperingen maken
* het aggregeren van de gegevens.

Enkele veelvoorkomende opslagoplossingen zijn: [SQL Server,](https://azure.microsoft.com/services/sql-database/) [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)en Cosmos [DB](https://azure.microsoft.com/services/cosmos-db/). Het kiezen van welke opslagoplossing voor de lange termijn u kiest, hangt af van de voorkeur. U overwegen het hulpprogramma te kiezen, afhankelijk van wat het biedt in termen van interactiebeschikbaarheid bij het visualiseren van de gegevens.

## <a name="visualizing-data-and-gathering-insights"></a>Gegevens visualiseren en inzichten verzamelen

Gebruik een datavisualisatietool van uw keuze om verbinding te maken met uw opslag op lange termijn om de gebruiksgegevens weer te geven en inzichten te verzamelen om de gebruiksefficiëntie te verifiëren. [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) kan bijvoorbeeld worden gebruikt om de gebruiksgegevens te ordenen en weer te geven. 

U [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken om uw resources binnen één locatie-interface te maken, te koppelen en te beheren. Als er meer controle nodig is, kan de afzonderlijke resource worden gemaakt binnen één resourcegroep en onafhankelijk van de Data Factory-service worden beheerd.  

## <a name="next-steps"></a>Volgende stappen

Zodra het systeem is ingesteld en de gegevens is het verplaatsen naar de lange termijn opslag, de volgende stap is om te komen met de vragen die de gegevens moeten beantwoorden. Bijvoorbeeld: 

-   Wat is het gebruik van de VM-grootte?

    Selecteren gebruikers hoge prestaties (duurdere) VM-formaten?
-   Welke Marketplace-afbeeldingen worden gebruikt?

    Zijn aangepaste afbeeldingen de meest voorkomende VM-basis, moet een gemeenschappelijke Image Store worden gebouwd zoals [Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md) of Image [factory](image-factory-create.md).
-   Welke aangepaste afbeeldingen worden gebruikt of niet gebruikt?
