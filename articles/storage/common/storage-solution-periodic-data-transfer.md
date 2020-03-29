---
title: Kies een Azure-oplossing voor periodieke gegevensoverdracht| Microsoft Documenten
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevensoverdracht wanneer u periodiek gegevens overdraagt.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fb49802adf6242f445b700d06622d7e6aa336b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67357041"
---
# <a name="solutions-for-periodic-data-transfer"></a>Oplossingen voor periodieke gegevensoverdracht
 
Dit artikel geeft een overzicht van de oplossingen voor gegevensoverdracht wanneer u periodiek gegevens overdraagt. Periodieke gegevensoverdracht via het netwerk kan worden gecategoriseerd als terugkerend met regelmatige tussenpozen of continue gegevensverplaatsing. Het artikel beschrijft ook de aanbevolen opties voor gegevensoverdracht en de respectievelijke sleutelcapaciteitsmatrix voor dit scenario.

Als u een overzicht wilt begrijpen van alle beschikbare opties voor gegevensoverdracht, gaat u naar [Een Azure-oplossing voor gegevensoverdracht kiezen.](storage-choose-data-transfer-solution.md)

## <a name="recommended-options"></a>Aanbevolen opties

De aanbevolen opties voor periodieke gegevensoverdracht vallen in twee categorieën, afhankelijk van of de overdracht terugkerend of continu is.

- **Scripted/programmatische hulpprogramma's** – Voor gegevensoverdracht die regelmatig plaatsvindt, gebruikt u de gescripte en programmatische hulpprogramma's zoals AzCopy en Azure Storage REST API's. Deze tools zijn gericht op IT-professionals en ontwikkelaars.

    - **AzCopy** - Gebruik dit opdrachtregelgereedschap om eenvoudig gegevens van en naar Azure Blobs-, Bestanden- en Tabelopslag te kopiëren met optimale prestaties. AzCopy ondersteunt gelijktijdigheid en parallellisme en de mogelijkheid om kopieerbewerkingen te hervatten wanneer deze worden onderbroken.
    - **Azure Storage REST API's/SDKs** – Bij het bouwen van een toepassing u de toepassing ontwikkelen tegen Azure Storage REST API's en de Azure SDK's gebruiken die in meerdere talen worden aangeboden. De REST API's kunnen ook gebruikmaken van de Azure Storage Data Movement Library die speciaal is ontworpen voor het krachtige kopiëren van gegevens van en naar Azure.

- **Tools voor continue gegevensopname** – Voor continue, doorlopende gegevensopname u een van de online transfer-apparaten van Data Box of Azure Data Factory selecteren. Deze tools zijn opgezet door IT-professionals en kunnen gegevensoverdracht transparant automatiseren.

    - **Azure Data Factory** – Data Factory moet worden gebruikt om een overdrachtsbewerking uit te schalen en als er behoefte is aan mogelijkheden voor orchestration en enterprise grade monitoring. Gebruik Azure Data Factory om een cloudpijplijn in te stellen die regelmatig bestanden overbrengt tussen verschillende Azure-services, on-premises of een combinatie van beide. Met Azure Data Factory u gegevensgestuurde werkstromen orkestreren die gegevens uit verschillende gegevensopslag opnemen en gegevensverplaatsing en gegevenstransformatie automatiseren.
    - **Azure Data Box-familie voor online overdrachten** - Data Box Edge en Data Box Gateway zijn online netwerkapparaten die gegevens van en naar Azure kunnen verplaatsen. Data Box Edge maakt gebruik van kunstmatige intelligentie (AI)-enabled Edge compute om gegevens vooraf te verwerken voordat ze worden geüpload. Data Box Gateway is een virtuele versie van het apparaat met dezelfde mogelijkheden voor gegevensoverdracht.


## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

In de volgende tabel worden de verschillen in sleutelmogelijkheden samengevat.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Scripted/Programmatic netwerkgegevensoverdracht

| Mogelijkheid                  | AzCopy                                 | Azure Storage REST API's       |
|-----------------------------|----------------------------------------|-------------------------------|
| Vormfactor                 | Opdrachtregelgereedschap van Microsoft       | Klanten ontwikkelen zich tegen storage <br> REST-API's gebruiken Azure-clientbibliotheken |
| Initiële eenmalige installatie     | Minimaal                                | Matige, variabele ontwikkelingsinspanning    |
| Gegevensindeling                 | Azure Blobs, Azure Files, Azure-tabellen | Azure Blobs, Azure Files, Azure-tabellen   |
| Prestaties                 | Al geoptimaliseerd                      | Optimaliseren naarmate je ontwikkelt                  |
| Prijzen                     | Gratis, data uitgangskosten van toepassing      | Gratis, data uitgangskosten van toepassing        |

### <a name="continuous-data-ingestion-over-network"></a>Continue gegevensopname via netwerk

| Functie                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Vormfactor                                   | Virtueel apparaat             | Fysiek apparaat          | Service in Azure-portal, on-premises agent                                                            |
| Hardware                                      | Je hypervisor            | Geleverd door Microsoft    | N.v.t.                                                            |
| Eerste installatie-inspanning                          | Laag (<30 minuten.)            | Matig (~paar uur) | Groot (~dagen)                                                 |
| Gegevensindeling                                   | Azure Blobs, Azure-bestanden   | Azure Blobs, Azure-bestanden | [Ondersteunt meer dan 70-gegevensconnectoren voor gegevensarchieven en -indelingen](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Gegevens voorverwerking                           | Nee                         | Ja, via Edge compute    | Ja                                                           |
| Lokale cache<br>(om on-premises gegevens op te slaan)    | Ja                        | Ja                      | Nee                                                            |
| Overdracht van andere wolken                    | Nee                         | Nee                       | Ja                                                           |
| Prijzen                                       | [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prijzen](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Volgende stappen

- [Gegevens overbrengen met AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Meer informatie over gegevensoverdracht met Storage REST API's](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Begrijpen hoe u:
    - [Gegevens overbrengen met Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Gegevens transformeren met Data Box Edge voordat u naar Azure verzendt.](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute)
- [Meer informatie over het overbrengen van gegevens met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
