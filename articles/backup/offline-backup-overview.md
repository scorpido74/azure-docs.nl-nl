---
title: Overzicht van offline back-up
description: Meer informatie over de verschillende onderdelen van offline back-up, met inbegrip van Azure Data Box offline back-up en Azure import/export op basis van een offline back-up.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027015"
---
# <a name="overview-of-offline-backup"></a>Overzicht van offline back-up

In dit artikel vindt u een overzicht van offline back-ups.

Bij de eerste volledige back-ups naar Azure worden doorgaans grote hoeveel heden gegevens online overgebracht en is er meer netwerk bandbreedte nodig in vergelijking met de volgende back-ups die alleen incrementele wijzigingen overdragen. Externe kant oren of data centers in bepaalde geografische gebieden hebben niet altijd voldoende netwerk bandbreedte. Deze eerste back-ups nemen daarom enkele dagen in beslag en gedurende deze tijd wordt continu hetzelfde netwerk gebruikt dat is ingericht voor toepassingen die worden uitgevoerd in het on-premises Data Center.

Azure Backup ondersteunt ' offline back-up ', waarmee de eerste back-upgegevens offline kunnen worden gezet zonder gebruik van netwerk bandbreedte. Het biedt een mechanisme voor het kopiëren van back-upgegevens naar fysieke opslag apparaten die vervolgens worden verzonden naar een nabijgelegen Azure-Data Center en geüpload naar een Recovery Services kluis. Dit proces zorgt voor een robuuste overdracht van back-upgegevens zonder gebruik te maken van netwerk bandbreedte.

## <a name="offline-backup-options"></a>Opties voor offline back-ups

Offline back-ups worden in twee modi aangeboden op basis van het eigendom van de opslag apparaten.

1. Offline back-up op basis van Azure Data Box (preview)
2. Offline back-up op basis van importeren/exporteren in azure

## <a name="azure-data-box-based-offline-backup-preview"></a>Offline back-up op basis van Azure Data Box (preview)

Deze modus wordt momenteel ondersteund door de MARS-agent in de preview-versie. Met deze optie profiteert u van de [Azure data Box-Service](https://azure.microsoft.com/services/databox/) voor het verzenden van micro soft-eigendoms-, veilige en verwerkings apparaten met USB-connectors, naar uw Data Center of externe kantoor, en back-upgegevens worden rechtstreeks naar deze apparaten geschreven. **Met deze optie bespaart u de inspanningen die nodig zijn om uw eigen Azure-compatibele schijven en connectors te kopen of om tijdelijke opslag in te richten als faserings locatie.** Daarnaast verwerkt micro soft de end-to-end-overdracht logistiek, die u via de Azure Portal kunt volgen. Hieronder ziet u een architectuur waarin de verplaatsing van back-upgegevens met deze optie wordt beschreven.

![Data Box architectuur Azure Backup](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Hier volgt een samen vatting van de architectuur:

1. Azure Backup kopieert rechtstreeks back-upgegevens naar deze vooraf geconfigureerde apparaten.
2. U kunt deze apparaten vervolgens weer verzenden naar een Azure-Data Center.
3. De Azure Data Box-Service kopieert de gegevens naar een opslag account dat eigendom is van de klant.
4. Azure Backup kopieert automatisch back-upgegevens van het opslag account naar de aangewezen Recovery Services kluis en incrementele online back-ups worden gepland.

Raadpleeg [dit artikel](offline-backup-azure-data-box.md)als u Azure data Box gebaseerde offline back-up wilt gebruiken.

## <a name="azure-importexport-based-offline-backup"></a>Offline back-up op basis van importeren/exporteren in azure

Deze optie wordt ondersteund door Azure Backup Server (MABS)/DPM-A/MARS-agent. Het maakt gebruik van de [Azure import/export-service](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) waarmee u de eerste back-upgegevens naar Azure kunt overdragen met behulp van uw eigen Azure-compatibele schijven en connectors. Deze aanpak vereist een tijdelijke opslag die bekend staat als de **faserings locatie** en het gebruik van vooraf ontwikkelde hulpprogram ma's voor het format teren en kopiëren van de back-upgegevens op schijven die eigendom zijn van de klant. Hieronder ziet u een architectuur die de verplaatsing van back-upgegevens met deze optie beschrijft:

![Architectuur van Azure Backup import/export](./media/offline-backup-overview/azure-backup-import-export.png)

Hier volgt een samen vatting van de architectuur:

1. In plaats van de back-upgegevens via het netwerk te verzenden, schrijft Azure Backup de back-upgegevens naar een faserings locatie.
2. De gegevens in de faserings locatie worden naar een of meer SATA-schijven geschreven met behulp van een aangepast hulp programma.
3. Als onderdeel van de voorbereidende werkzaamheden maakt het hulp programma een Azure import-taak. De SATA-schijven worden verzonden naar het dichtstbijzijnde Azure-Data Center en verwijzen naar de import taak om de activiteiten te verbinden.
4. In het Azure-Data Center worden de gegevens op de schijven gekopieerd naar een Azure-opslag account.
5. Azure Backup kopieert de back-upgegevens van het opslag account naar de Recovery Services kluis en worden incrementele back-ups gepland.

Raadpleeg [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)als u een offline back-up van Azure import/export wilt gebruiken met de Mars-agent.

Raadpleeg [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)als u hetzelfde wilt gebruiken in combi natie met MABS/DPM-A.

## <a name="offline-backup-support-summary"></a>Samen vatting van offline back-upondersteuning

In de volgende tabel worden de twee beschik bare opties vergeleken, zodat u de juiste keuzes kunt maken op basis van uw scenario.

| **Overweging**                                            | **Offline back-up op basis van Azure Data Box**                     | **Offline back-up op basis van importeren/exporteren in azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup implementatie modellen                              | MARS-agent (preview-versie)                                              | MARS-agent, Azure Backup Server (MABS), DPM-A                                           |
| Maximum aantal back-upgegevens per server (MARS) of per beveiligings groep (MABS, DPM-A) | [Azure data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7,2 TB <br> [Azure data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (Maxi maal 10 schijven van 8 TB elk)                          |
| Beveiliging (gegevens, apparaat &-Service)                           | [Data](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES-256 bit versleuteld <br> [Apparaat](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) -robuuste behuizing, bedrijfs eigen, op referenties gebaseerde interface voor het kopiëren van gegevens <br> [Service](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) -beveiligd door Azure-beveiligings functies | Door BitLocker versleutelde gegevens                                 |
| Tijdelijke opslag locatie inrichten                     | Niet vereist                                                | Meer dan of gelijk aan de geschatte grootte van de back-upgegevens        |
| Ondersteunde regio's                                           | [Azure Data Box Disk regio's](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box regio's](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure-regio's voor importeren/exporteren](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Verzen ding tussen landen *                                     | **Niet ondersteund**  <br>    *Bron adres & doel Azure-Data Center moet zich in hetzelfde land bedoen* | Ondersteund                                                    |
| Logistiek overdragen (levering, Trans Port, ophalen)           | Volledig door micro soft beheerd                                     | Door de klant beheerd                                            |
| Prijzen                                                      | [Azure Data Box prijzen](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box Disk prijzen](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prijzen voor importeren/exporteren van Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Als uw land geen Azure-Data Center heeft, moet u uw schijven verzenden naar een Azure-Data Center in een ander land.*

## <a name="next-steps"></a>Volgende stappen

* [Offline back-ups op basis van Azure Data Box voor MARS-agent](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure import/export op basis van een offline back-up voor MARS-agent](backup-azure-backup-import-export.md)  
* [Azure import/export op basis van een offline back-up voor MABS/DPM-A](backup-azure-backup-server-import-export-.md)
