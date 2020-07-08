---
title: Een Azure-oplossing kiezen voor periodieke gegevens overdracht | Microsoft Docs
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevens overdracht wanneer u periodiek gegevens overbrengt.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 9ffa35e158d34a1fc6945ee2730dcf136d13edb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85504334"
---
# <a name="solutions-for-periodic-data-transfer"></a>Oplossingen voor periodieke gegevensoverdracht
 
Dit artikel bevat een overzicht van de oplossingen voor gegevens overdracht wanneer u periodiek gegevens overbrengt. Periodieke gegevens overdracht via het netwerk kan worden gecategoriseerd als herhaald met regel matige intervallen of continue gegevens verplaatsing. In het artikel worden ook de aanbevolen opties voor gegevens overdracht en de bijbehorende matrix voor de belangrijkste functionaliteit voor dit scenario beschreven.

Voor een overzicht van alle beschik bare opties voor gegevens overdracht gaat u naar [een Azure-oplossing voor gegevens overdracht kiezen](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Aanbevolen opties

De aanbevolen opties voor periodieke gegevens overdracht vallen in twee categorieën, afhankelijk van het feit of de overdracht terugkerend of doorlopend is.

- **Scripted/programmatische hulpprogram ma's** : voor gegevens overdracht die met regel matige tussen pozen plaatsvindt, gebruikt u het script en Program Ma's zoals AzCopy en Azure Storage rest-api's. Deze hulpprogram ma's zijn gericht op IT-professionals en ontwikkel aars.

    - **AzCopy** : gebruik dit opdracht regel programma om eenvoudig gegevens te kopiëren van en naar Azure-blobs,-bestanden en-tabel opslag met optimale prestaties. AzCopy ondersteunt gelijktijdigheid en parallellisme, en de mogelijkheid om Kopieer bewerkingen te hervatten wanneer deze worden onderbroken.
    - **Azure Storage rest api's/sdk's** : wanneer u een toepassing bouwt, kunt u de toepassing ontwikkelen tegen Azure Storage rest api's en de Azure-sdk's gebruiken die in meerdere talen worden aangeboden. De REST-Api's kunnen ook gebruikmaken van de Azure Storage gegevens verplaatsings bibliotheek die speciaal is ontworpen voor het snel kopiëren van gegevens van en naar Azure.

- **Voortdurende hulp middelen voor gegevens opname** : voor continue, doorlopende opname van gegevens kunt u een van data Box apparaat voor online overdracht of Azure Data Factory selecteren. Deze hulpprogram ma's worden ingesteld door IT-professionals en kunnen de gegevens overdracht transparant automatiseren.

    - **Azure Data Factory** -Data Factory moet worden gebruikt voor het uitschalen van een overdrachts bewerking en als er sprake is van indelings-en bewakings mogelijkheden voor bedrijfs kwaliteit. Gebruik Azure Data Factory om een Cloud pijplijn in te stellen die regel matig bestanden overbrengt tussen verschillende Azure-Services, on-premises of een combi natie van deze twee. Met Azure Data Factory kunt u gegevensgestuurde werk stromen organiseren die gegevens uit verschillende gegevens archieven opnemen en gegevens verplaatsing en gegevens transformatie automatiseren.
    - **Azure data Box-familie voor online overdrachten** : Data Box Edge en data Box gateway zijn online netwerk apparaten waarmee gegevens kunnen worden verplaatst van en naar Azure. Data Box Edge een Edge Compute-functie (kunst matige intelligentie) gebruiken om gegevens vooraf te verwerken vóór het uploaden. Data Box Gateway is een virtuele versie van het apparaat met dezelfde mogelijkheden voor gegevens overdracht.


## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

De volgende tabel bevat een overzicht van de verschillen in de belangrijkste mogelijkheden.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Script-en programmatische netwerk gegevens overdracht

| Mogelijkheid                  | AzCopy                                 | Azure Storage REST-Api's       |
|-----------------------------|----------------------------------------|-------------------------------|
| Vorm factor                 | Opdracht regel programma van micro soft       | Klanten ontwikkelen op basis van opslag <br> REST-Api's met behulp van Azure-client bibliotheken |
| Eerste eenmalige installatie     | Minimaal                                | Gemiddelde ontwikkelings inspanning van variabele    |
| Gegevensindeling                 | Azure-blobs, Azure Files, Azure-tabellen | Azure-blobs, Azure Files, Azure-tabellen   |
| Prestaties                 | Al geoptimaliseerd                      | Optimaliseren tijdens het ontwikkelen                  |
| Prijzen                     | Er zijn gratis kosten voor het uitbrengen van gegevens van toepassing      | Er zijn gratis kosten voor het uitbrengen van gegevens van toepassing        |

### <a name="continuous-data-ingestion-over-network"></a>Doorlopende gegevens opname via het netwerk

| Functie                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Vorm factor                                   | Virtueel apparaat             | Fysiek apparaat          | Service in Azure Portal, on-premises agent                                                            |
| Hardware                                      | Uw Hyper Visor            | Geleverd door micro soft    | NA                                                            |
| Initiële installatie-inspanning                          | Laag (<30 minuten)            | Gemiddeld (~ paar uur) | Groot (~ dagen)                                                 |
| Gegevensindeling                                   | Azure-blobs, Azure Files   | Azure-blobs, Azure Files | [Ondersteunt 70 en data connectors voor gegevens archieven en-indelingen](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Vooraf verwerkte gegevens                           | No                         | Ja, via Edge compute    | Yes                                                           |
| Lokale cache<br>(voor het opslaan van on-premises gegevens)    | Ja                        | Ja                      | No                                                            |
| Overdracht van andere Clouds                    | Nee                         | Nee                       | Yes                                                           |
| Prijzen                                       | [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prijzen](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Volgende stappen

- [Gegevens overdragen met AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Meer informatie over gegevens overdracht met rest-api's voor opslag](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Meer informatie over:
    - [Gegevens overdragen met data Box gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformeer gegevens met data Box Edge voordat ze naar Azure worden verzonden](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Meer informatie over het overdragen van gegevens met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
