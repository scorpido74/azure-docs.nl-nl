---
title: Toepassingen en gegevens kopiëren naar pool knooppunten
description: Meer informatie over het kopiëren van toepassingen en gegevens naar pool knooppunten.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85954890"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Toepassingen en gegevens kopiëren naar pool knooppunten

Azure Batch ondersteunt verschillende manieren voor het ophalen van gegevens en toepassingen op reken knooppunten, zodat de gegevens en toepassingen beschikbaar zijn voor gebruik door taken. Gegevens en toepassingen zijn mogelijk vereist voor het uitvoeren van de volledige taak en moeten dus op elk knoop punt worden geïnstalleerd. Sommige zijn mogelijk alleen vereist voor een specifieke taak of moeten worden geïnstalleerd voor de taak, maar moeten niet op elk knoop punt aanwezig zijn. Batch bevat hulpprogram ma's voor elk van deze scenario's.

- **Resource bestanden**voor het starten van de groep: voor toepassingen of gegevens die moeten worden geïnstalleerd op elk knoop punt in de pool. Gebruik deze methode samen met een toepassings pakket of de bron bestands verzameling van de begin taak om een installatie opdracht uit te voeren.  

Voorbeelden: 
- De opdracht regel voor het starten van de taak gebruiken om toepassingen te verplaatsen of te installeren

- Geef een lijst met specifieke bestanden of containers op in een Azure-opslag account. Zie [# resource file toevoegen in rest-documentatie](/rest/api/batchservice/pool/add#resourcefile) voor meer informatie

- Elke taak die wordt uitgevoerd op de pool wordt uitgevoerd MyApplication.exe die eerst met MyApplication.msi moet worden geïnstalleerd. Als u dit mechanisme gebruikt, moet u de eigenschap **wachten op geslaagd** van de begin taak instellen op **True**. Zie de [documentatie # starttask in rest toevoegen](/rest/api/batchservice/pool/add#starttask)voor meer informatie.

- **Toepassings pakket verwijzingen** in de groep: voor toepassingen of gegevens die moeten worden geïnstalleerd op elk knoop punt in de groep. Er is geen installatie opdracht gekoppeld aan een toepassings pakket, maar u kunt een begin taak gebruiken om elke installatie opdracht uit te voeren. Als voor uw toepassing geen installatie of een groot aantal bestanden is vereist, kunt u deze methode gebruiken. Toepassings pakketten zijn goed geschikt voor grote aantallen bestanden omdat ze een groot aantal bestands verwijzingen combi neren in een kleine nettolading. Als u meer dan 100 afzonderlijke bron bestanden wilt toevoegen aan één taak, kan de batch-service worden uitgevoerd op basis van interne systeem beperkingen voor één taak. Gebruik toepassings pakketten ook als u strenge vereisten voor versie beheer hebt waarbij u veel verschillende versies van dezelfde toepassing kunt gebruiken en er een moet kiezen. Lees voor meer informatie [Deploy Applications to compute nodes with batch Application packages](./batch-application-packages.md).

- **Resource bestanden voor taak voorbereidings taak**: voor toepassingen of gegevens die moeten worden geïnstalleerd om de taak uit te voeren, maar die niet moeten worden geïnstalleerd op de hele groep. Bijvoorbeeld: als uw pool veel verschillende typen taken heeft, en er slechts één taak type moet MyApplication.msi worden uitgevoerd, is het zinvol om de installatie te Step Into een taak voorbereidings taak. Zie voor meer informatie over taken voor taak voorbereiding taken [uitvoeren voor taak voorbereiding en taak release op batch Compute-knoop punten](./batch-job-prep-release.md).

- **Taak bron bestanden**: voor wanneer een toepassing of gegevens alleen relevant zijn voor een afzonderlijke taak. Bijvoorbeeld: u hebt vijf taken, elk een ander bestand verwerkt en vervolgens de uitvoer naar Blob-opslag schrijft.  In dit geval moet het invoer bestand worden opgegeven in de verzameling **taken bron bestanden** , omdat elke taak een eigen invoer bestand heeft.

## <a name="determine-the-scope-required-of-a-file"></a>Het bereik bepalen dat vereist is voor een bestand

U moet het bereik van een bestand bepalen: het vereiste bestand voor een groep, een taak of een taak. Bestanden die aan de groep zijn toegewezen, moeten groeps toepassings pakketten of een begin taak gebruiken. Bestanden die aan de taak zijn toegewezen, moeten een taak voorbereidings taak gebruiken. Een goed voor beeld van bestanden binnen het bereik van de groep of het taak niveau zijn toepassingen. Bestanden die aan de taak zijn toegewezen, moeten taak bron bestanden gebruiken.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Andere manieren om gegevens op te halen voor batch Compute-knoop punten

Er zijn andere manieren om gegevens op te halen voor batch Compute-knoop punten die niet officieel zijn geïntegreerd in de batch-REST API. Omdat u controle hebt over Azure Batch knoop punten en aangepaste uitvoer bare bestanden kunt uitvoeren, is het mogelijk om gegevens uit een wille keurig aantal aangepaste bronnen op te halen, zolang het batch-knoop punt verbinding met het doel heeft en u de referenties voor die bron op het Azure Batch knoop punt hebt. Enkele algemene voor beelden zijn:

- Gegevens downloaden van SQL
- Gegevens downloaden van andere webservices/aangepaste locaties
- Een netwerk share toewijzen

### <a name="azure-storage"></a>Azure Storage

Blob-opslag heeft schaal baarheids doelen voor down loads. De schaalbaarheids doelen van de bestands share van Azure Storage zijn hetzelfde als voor één blob. De grootte is van invloed op het aantal knoop punten en groepen dat u nodig hebt.

