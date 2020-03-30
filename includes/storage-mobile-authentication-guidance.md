---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673305"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Uw toepassing configureren om toegang te krijgen tot Azure Storage
Er zijn twee manieren om uw toepassing te verifiëren om toegang te krijgen tot opslagservices:

* Gedeelde sleutel: gebruik gedeelde sleutel alleen voor testdoeleinden
* Shared Access Signature (SAS): SAS gebruiken voor productietoepassingen

### <a name="shared-key"></a>Gedeelde sleutel
Verificatie van gedeelde sleutels betekent dat uw toepassing uw accountnaam en accountsleutel gebruikt om toegang te krijgen tot opslagservices. Om snel te laten zien hoe deze bibliotheek te gebruiken, gebruiken we Shared Key-verificatie in deze start.

> [!WARNING] 
> **Gebruik alleen Shared Key-verificatie voor testdoeleinden!** Uw accountnaam en accountsleutel, die volledige lees-/schrijftoegang geven tot het bijbehorende Opslagaccount, worden gedistribueerd naar elke persoon die uw app downloadt. Dit is **geen** goede praktijk omdat u het risico loopt dat uw sleutel wordt gecompromitteerd door niet-vertrouwde clients.
> 
> 

Wanneer u verificatie met gedeelde sleutel gebruikt, maakt u een [verbindingstekenreeks](../articles/storage/common/storage-configure-connection-string.md). De verbindingstekenreeks bestaat uit:  

* Het **DefaultEndpointsProtocol** - u HTTP of HTTPS kiezen. Het gebruik van HTTPS is echter een aanrader.
* De **accountnaam** - de naam van uw opslagaccount
* De **accountsleutel** - Ga op de [Azure Portal](https://portal.azure.com)naar uw opslagaccount en klik op het pictogram **Sleutels** om deze informatie te vinden.
* (Optioneel) **EndpointSuffix** - Dit wordt gebruikt voor opslagservices in regio's met verschillende eindpuntachtervoegsels, zoals Azure China of Azure Governance.

Hier is een voorbeeld van verbindingstekenreeks met verificatie van gedeelde sleutels:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Shared Access Signatures (SAS)
Voor een mobiele toepassing is de aanbevolen methode voor het verifiëren van een aanvraag door een client tegen de Azure Storage-service een SAS (Shared Access Signature). Met SAS u een client voor een bepaalde periode toegang verlenen tot een resource, met een opgegeven set machtigingen.
Als eigenaar van het opslagaccount moet u een SAS genereren die uw mobiele klanten kunnen verbruiken. Als u de SAS wilt genereren, wilt u waarschijnlijk een aparte service schrijven die de SAS genereert die aan uw clients moet worden gedistribueerd. Voor testdoeleinden u de [Microsoft Azure Storage Explorer](https://storageexplorer.com) of de Azure [Portal](https://portal.azure.com) gebruiken om een SAS te genereren. Wanneer u de SAS maakt, u het tijdsinterval opgeven waarover de SAS geldig is en de machtigingen die de SAS aan de client verleent.

In het volgende voorbeeld ziet u hoe u de Microsoft Azure Storage Explorer gebruiken om een SAS te genereren.

1. Als u dat nog niet hebt gedaan, [installeert u de Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Maak verbinding met uw abonnement.
3. Klik op uw opslagaccount en klik linksonder op het tabblad Acties. Klik op 'Gedeelde toegangshandtekening genereren' om een 'verbindingstekenreeks' voor uw SAS te genereren.
4. Hier volgt een voorbeeld van een SAS-verbindingstekenreeks die lees- en schrijfmachtigingen verleent op service-, container- en objectniveau voor de blobservice van het opslagaccount.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Zoals u zien, stelt u bij het gebruik van een SAS uw accountsleutel niet bloot in uw toepassing. U meer informatie krijgen over SAS en aanbevolen procedures voor het gebruik van SAS door te kijken naar [Gedeelde toegangshandtekeningen: Inzicht in het SAS-model](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

