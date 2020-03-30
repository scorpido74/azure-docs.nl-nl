---
title: Clusterverticale schaling (opschalen) beheren om aan de vraag in Azure Data Explorer te voldoen
description: In dit artikel worden stappen beschreven om een Azure Data Explorer-cluster op te schalen en te schalen op basis van veranderende vraag.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560436"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Clusterverticale schaling (opschalen) beheren in Azure Data Explorer om aan de veranderende vraag te voldoen

Het op de juiste manier aanpassen van het formaat van een cluster is van cruciaal belang voor de prestaties van Azure Data Explorer. Een statische clustergrootte kan leiden tot onderbenutting of overmatig gebruik, waarvan geen van beide ideaal is.

Aangezien de vraag op een cluster niet met absolute nauwkeurigheid kan worden voorspeld, is een betere aanpak het *schalen van* een cluster, het toevoegen en verwijderen van capaciteit en CPU-resources met veranderende vraag. 

Er zijn twee werkstromen voor het schalen van een Azure Data Explorer-cluster:

* [Horizontale schaling](manage-cluster-horizontal-scaling.md), ook wel schalen in en uit.
* Verticale schaling, ook wel op en neer schalen genoemd.

In dit artikel wordt de verticale schalingsworkflow uitgelegd:

## <a name="configure-vertical-scaling"></a>Verticaal schalen configureren

1. Ga in de Azure-portal naar uw Azure Data Explorer-clusterbron. Selecteer Onder **Instellingen**de optie **Opschalen**.

1. In het venster **Opschalen** ziet u een lijst met beschikbare SKU's voor uw cluster. In de volgende afbeelding zijn bijvoorbeeld slechts vier SKU's beschikbaar.

    ![Omhoog schalen](media/manage-cluster-vertical-scaling/scale-up.png)

    De SKU's zijn uitgeschakeld omdat ze de huidige SKU zijn of omdat ze niet beschikbaar zijn in het gebied waar het cluster zich bevindt.

1. Als u uw SKU wilt wijzigen, selecteert u een nieuwe SKU en klikt u op **Selecteren**.

> [!NOTE]
> * Het verticale schalingproces kan enkele minuten duren en gedurende die tijd wordt uw cluster opgeschort. 
> * Het verkleinen van de cluster kan de prestaties van uw cluster schaden.
> * De prijs is een schatting van de virtuele machines van het cluster en de servicekosten van Azure Data Explorer. Andere kosten zijn niet inbegrepen. Zie de [pagina kostenschatting van](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) Azure Data Explorer voor een schatting en de [prijspagina van](https://azure.microsoft.com/pricing/details/data-explorer/) Azure Data Explorer voor volledige prijsinformatie.

U hebt nu verticale schaling geconfigureerd voor uw Azure Data Explorer-cluster. Voeg een andere regel toe voor een horizontale schaling. Als u hulp nodig hebt bij clusterschalingsproblemen, [opent u een ondersteuningsaanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

* [Beheer clusterhorizontale schaling](manage-cluster-horizontal-scaling.md) om het aantal instantieen dynamisch uit te schalen op basis van de door u opgegeven statistieken.

* Houd uw resourcegebruik in de gaten door dit artikel te volgen: [De prestaties, status en het gebruik van Azure Data Explorer controleren met statistieken](using-metrics.md).

