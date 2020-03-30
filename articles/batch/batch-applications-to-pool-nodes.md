---
title: Toepassingen en gegevens kopiëren naar knooppunten
description: Meer informatie over het kopiëren van toepassingen en gegevens naar het bundelen van knooppunten.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385582"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Toepassingen en gegevens kopiëren naar knooppunten

Azure Batch ondersteunt verschillende manieren om gegevens en toepassingen op compute nodes te krijgen, zodat de gegevens en toepassingen beschikbaar zijn voor gebruik door taken. Gegevens en toepassingen kunnen nodig zijn om de hele taak uit te voeren en moeten dus op elk knooppunt worden geïnstalleerd. Sommige kunnen alleen nodig zijn voor een specifieke taak, of moeten worden geïnstalleerd voor de taak, maar hoeven niet op elk knooppunt te zijn. Batch heeft tools voor elk van deze scenario's.

- **Bronbestanden voor het starten van taken**groep: voor toepassingen of gegevens die op elk knooppunt in de groep moeten worden geïnstalleerd. Gebruik deze methode samen met een toepassingspakket of de verzameling van het bronbestand van de starttaak om een installatieopdracht uit te voeren.  

Voorbeelden: 
- De opdrachtregel voor taak starten gebruiken om toepassingen te verplaatsen of te installeren

- Geef een lijst op met specifieke bestanden of containers in een Azure-opslagaccount. Zie [add#resourcefile voor](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile) meer informatie in REST-documentatie

- Elke taak die op het zwembad wordt uitgevoerd, wordt uitgevoerd op MyApplication.exe die eerst moet worden geïnstalleerd met MyApplication.msi. Als u dit mechanisme gebruikt, moet u de wacht van de starttaak **op de** eigenschap Succes instellen op **true.** Zie de taak [toevoegen#starttaak in REST-documentatie](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)voor meer informatie.

- **Verwijzingen naar het toepassingspakket** op de groep: voor toepassingen of gegevens die op elk knooppunt in de groep moeten worden geïnstalleerd. Er is geen installatieopdracht gekoppeld aan een toepassingspakket, maar u een starttaak gebruiken om een installatieopdracht uit te voeren. Als uw toepassing geen installatie vereist of uit een groot aantal bestanden bestaat, u deze methode gebruiken. Applicatiepakketten zijn zeer geschikt voor grote aantallen bestanden omdat ze een groot aantal bestandsverwijzingen combineren tot een kleine payload. Als u meer dan 100 afzonderlijke bronbestanden in één taak probeert op te nemen, stuit de batchservice mogelijk op interne systeembeperkingen voor één taak. Gebruik ook toepassingspakketten als u strenge versievereisten hebt waarbij u mogelijk veel verschillende versies van dezelfde toepassing hebt en er tussen moet kiezen. Lees Voor meer informatie [Toepassingen implementeren om knooppunten te berekenen met batchtoepassingspakketten](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **Taakvoorbereidingstaakbronbestanden:** voor toepassingen of gegevens die moeten worden geïnstalleerd om de taak uit te voeren, maar die niet op de hele groep hoeven te worden geïnstalleerd. Als uw pool bijvoorbeeld veel verschillende soorten taken heeft en slechts één taaktype MyApplication.msi nodig heeft om uit te voeren, is het zinvol om de installatiestap in een taakvoorbereidingstaak te plaatsen. Zie [Taakvoorbereidings- en taakreleasetaken uitvoeren op batchcomputenodes](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)voor meer informatie over taken voor het voorbereiden van taken voor taken voor taakvoorbereiding.

- **Taakbronbestanden:** voor wanneer een toepassing of gegevens alleen relevant is voor een afzonderlijke taak. Bijvoorbeeld: U hebt vijf taken, elk verwerkt een ander bestand en schrijft vervolgens de uitvoer naar blobopslag.  In dit geval moet het invoerbestand worden opgegeven op de verzameling **takenbronbestanden** omdat elke taak een eigen invoerbestand heeft.

## <a name="determine-the-scope-required-of-a-file"></a>Het vereiste bereik van een bestand bepalen

U moet de reikwijdte van een bestand bepalen - is het bestand dat nodig is voor een groep, een taak of een taak. Bestanden die zijn samengevoegd tot de groep, moeten pooltoepassingspakketten of een starttaak gebruiken. Bestanden die zijn opgeslagen in de taak, moeten een taakvoorbereidingstaak gebruiken. Een goed voorbeeld van bestanden die op pool- of taakniveau worden verdeeld, zijn sollicitaties. Bestanden die zijn opgeslagen in de taak, moeten taakbronbestanden gebruiken.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Andere manieren om gegevens op batchcomputeknooppunten te krijgen

Er zijn andere manieren om gegevens op Batch compute nodes te krijgen die niet officieel zijn geïntegreerd in de Batch REST API. Omdat u controle hebt over Azure Batch-knooppunten en aangepaste uitvoerbare bestanden uitvoeren, u gegevens uit een willekeurig aantal aangepaste bronnen ophalen zolang het batchknooppunt verbinding heeft met het doel en u de referenties naar die bron op het Azure Batch-knooppunt hebt. Een paar veelvoorkomende voorbeelden zijn:

- Gegevens downloaden uit SQL
- Gegevens downloaden van andere webservices/aangepaste locaties
- Een netwerkshare in kaart brengen

### <a name="azure-storage"></a>Azure Storage

Blob-opslag heeft schaalbaarheidsdoelen voor downloaden. De schaalbaarheidsdoelen voor azure-opslagbestanden zijn hetzelfde als voor één blob. De grootte heeft invloed op het aantal knooppunten en groepen dat u nodig hebt.

