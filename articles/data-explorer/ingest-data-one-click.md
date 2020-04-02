---
title: Gebruik met één klik opname om gegevens in te nemen in Azure Data Explorer
description: Overzicht van het innemen van (laden) gegevens in Azure Data Explorer eenvoudig, met behulp van een klik opname.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521589"
---
# <a name="what-is-one-click-ingestion"></a>Wat is inname met één klik? 

Met één klik u snel gegevens opnemen en automatisch tabellen en toewijzingsstructuren voorstellen op basis van een gegevensbron in Azure Data Explorer. 

Met de Web-gebruikersinterface van Azure Data Explorer u gegevens opnemen uit opslag (blobbestand), een lokaal bestand of een container (tot 10.000 blobs). U ook een gebeurtenisraster op een container definiëren voor continue inname. De gegevens kunnen worden opgenomen in een bestaande of nieuwe tabel in JSON, CSV en [andere indelingen.](#file-formats) Met één klik kan een structuur voor een nieuwe tabel- en tabeltoewijzing worden voorgesteld op basis van de gegevensbron en een intuïtief platform bieden om de tabelstructuur van een bestaande tabel- en tabeltoewijzing aan te passen. Een klik opname zal inslikken van de gegevens in de tabel binnen slechts een paar minuten.

Inname met één klik is vooral handig bij het voor het eerst innemen van gegevens of wanneer het schema van uw gegevens niet bekend voor u is.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Maak [een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md).
* Meld u aan bij de [webgebruikers interface van Azure Data Explorer](https://dataexplorer.azure.com/) en voeg een verbinding toe aan uw [cluster.](/azure/data-explorer/web-query-data#add-clusters)

## <a name="file-formats"></a>Bestandsindelingen

Met één klik wordt een nieuwe tabel ineengehaald uit brongegevens in een van de volgende indelingen:
* JSON
* CSV
* Tsv
* SCSV (SCSV)
* SOHSV SOHSV
* TSVE TSVE
* Psv

## <a name="one-click-ingestion-wizard"></a>Wizard Inname met één klik

De wizard Opname met één klik leidt u door het opnameproces met één klik. 

> [!Note]
> In deze sectie wordt de wizard in het algemeen beschreven. Welke opties u selecteert, is afhankelijk van of u in een nieuwe of bestaande tabel wordt opgenomen. Zie voor meer informatie:
    > * Inslikken in [een nieuwe tabel](one-click-ingestion-new-table.md)
    > * Innemen in een [bestaande tabel](one-click-ingestion-existing-table.md) 
    
1. Als u de wizard wilt openen, klikt u met de rechtermuisknop op de *database* of *tabelrij* in het linkermenu van de webgebruikersinterface van Azure Data Explorer en selecteert u **Nieuwe gegevens innemen (voorbeeld).**

    ![Selecteer opname met één klik in de webgebruikersinterface](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. De wizard leidt u door de volgende opties:
       * Innemen in een [bestaande tabel](one-click-ingestion-existing-table.md)
       * Inslikken in [een nieuwe tabel](one-click-ingestion-new-table.md)
       * Inname van gegevens uit: * Blob-opslag * Een lokaal bestand * Een container
       * Voer de steekproefgrootte in, van 1 tot 10.000 rijen (alleen uit container)
       
1. Wanneer u de gegevensbron hebt geselecteerd, wordt een voorbeeld van de gegevens weergegeven. 
    Als u gegevens uit een container inneemt, u de gegevens filteren zodat alleen bestanden met specifieke voorvoegsels of bestandsextensies worden ingenomen. U bijvoorbeeld alleen bestanden met bestandsnamen opnemen die beginnen met het woord *Europa*of alleen bestanden met de extensie *.json*. 

1. Klik **op Schema bewerken**. Als u gegevens in neemt in een specifieke tabel, u de bronkolommen toewijzen aan de doelkolommen en beslissen of u kolomnamen al dan niet wilt opnemen.

1. Start het proces voor het innemen van gegevens.

> [!Note]
> Als uw gegevensbron een container is, moet u er rekening mee houden dat het beleid van Azure Data Explorer (batching) is ontworpen om het opnameproces te optimaliseren. Standaard is het beleid geconfigureerd tot 5 minuten of 500 MB aan gegevens, zodat u latentie ervaren. Zie [batchingbeleid](/azure/kusto/concepts/batchingpolicy) voor aggregatieopties. Bij het innemen van gegevens uit andere bronnen wordt de opname onmiddellijk van kracht.

## <a name="next-steps"></a>Volgende stappen

* Bepaal of u met één klik gegevens wilt opnemen in [een bestaande tabel](one-click-ingestion-existing-table.md) of een nieuwe [tabel](one-click-ingestion-new-table.md)
* [Querygegevens in Azure Data Explorer Web- gebruikersinterface](/azure/data-explorer/web-query-data)
* [Query's schrijven voor Azure Data Explorer met Kusto QueryLanguage](/azure/data-explorer/write-queries)