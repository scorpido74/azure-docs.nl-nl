---
title: Overzicht van offline back-up
description: Meer informatie over de onderdelen van offline back-up. Ze bevatten offline back-up op basis van Azure Data Box en offline back-up op basis van de Azure Import/Export-service.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196151"
---
# <a name="overview-of-offline-backup"></a>Overzicht van offline back-up

Dit artikel geeft een overzicht van offline back-up.

Initiële volledige back-ups naar Azure brengen doorgaans grote hoeveelheden gegevens online over en vereisen meer netwerkbandbreedte in vergelijking met volgende back-ups die alleen incrementele wijzigingen overbrengen. Externe kantoren of datacenters in bepaalde regio's hebben niet altijd voldoende netwerkbandbreedte. Om deze reden duren deze eerste back-ups enkele dagen. Gedurende deze periode maken de back-ups continu gebruik van hetzelfde netwerk dat is ingericht voor toepassingen die worden uitgevoerd in het on-premises datacenter.

Azure Backup ondersteunt offline back-ups, die de eerste back-upgegevens offline overzetten, zonder het gebruik van netwerkbandbreedte. Het biedt een mechanisme om back-upgegevens te kopiëren naar fysieke opslagapparaten. De apparaten worden vervolgens verzonden naar een nabijgelegen Azure-datacenter en geüpload naar een Vault recovery Services. Dit proces zorgt voor een robuuste overdracht van back-upgegevens zonder gebruik te maken van netwerkbandbreedte.

## <a name="offline-backup-options"></a>Offline back-upopties

Offline back-up wordt aangeboden in twee modi op basis van het eigendom van de opslagapparaten:

- Offline back-up op basis van Azure Data Box (voorbeeld)
- Offline back-up op basis van de Azure Import/Export-service

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Offline back-up op basis van Azure Data Box (voorbeeld)

Deze modus wordt momenteel ondersteund met de Microsoft Azure Recovery Services (MARS) Agent, in preview. Deze optie maakt gebruik van [Azure Data Box](https://azure.microsoft.com/services/databox/) om microsoft-eigen, veilige en sabotagebestendige transferapparaten met USB-connectors te verzenden naar uw datacenter of op afstand. Back-upgegevens worden rechtstreeks op deze apparaten geschreven. Deze optie bespaart de moeite die nodig is om uw eigen Azure-compatibele schijven en connectoren aan te schaffen of om tijdelijke opslag in te richten als een faseringslocatie. Microsoft verzorgt ook de end-to-end transferlogistiek, die u volgen via de Azure-portal. 

Een architectuur die de beweging van back-upgegevens met deze optie beschrijft, wordt hier weergegeven.

![Azure Backup Data Box-architectuur](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Hier is een samenvatting van de architectuur:

1. Azure Backup kopieert rechtstreeks back-upgegevens naar deze vooraf geconfigureerde apparaten.
2. U deze apparaten vervolgens terugsturen naar een Azure-datacenter.
3. Azure Data Box kopieert de gegevens naar een opslagaccount dat eigendom is van een klant.
4. Azure Backup kopieert automatisch back-upgegevens van het opslagaccount naar de aangewezen vault van Recovery Services. Incrementele online back-ups zijn gepland.

Zie [Offline back-up met Azure Data Box](offline-backup-azure-data-box.md)voor offline back-upopbasis.

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Offline back-up op basis van de Azure Import/Export-service

Deze optie wordt ondersteund door Microsoft Azure Backup Server (MABS), System Center Data Protection Manager (DPM) DPM-A en de MARS-agent. Het maakt gebruik van de [Azure Import/Export-service](https://docs.microsoft.com/azure/storage/common/storage-import-export-service). U de eerste back-upgegevens overbrengen naar Azure met behulp van uw eigen Azure-compatibele schijven en connectoren. Deze aanpak vereist dat u tijdelijke opslag indient die bekend staat als de faseringslocatie en vooraf gebouwde hulpprogramma's gebruikt om de back-upgegevens op te maken en te kopiëren naar schijven die eigendom zijn van de klant. 

Een architectuur die de beweging van back-upgegevens met deze optie beschrijft, wordt hier weergegeven.

![Azure Backup Import/Export-servicearchitectuur](./media/offline-backup-overview/azure-backup-import-export.png)

Hier is een samenvatting van de architectuur:

1. In plaats van de back-upgegevens via het netwerk te verzenden, schrijft Azure Backup de back-upgegevens naar een faseringslocatie.
2. De gegevens in de faseringslocatie worden naar een of meer SATA-schijven geschreven met behulp van een aangepast hulpprogramma.
3. Als onderdeel van het voorbereidende werk maakt het hulpprogramma een Azure-importtaak. De SATA-stations worden verzonden naar het dichtstbijzijnde Azure-datacenter en verwijzen naar de importtaak om de activiteiten met elkaar te verbinden.
4. In het Azure-datacenter worden de gegevens op de schijven gekopieerd naar een Azure-opslagaccount.
5. Azure Backup kopieert de back-upgegevens van het opslagaccount naar de kluis Recovery Services. Incrementele back-ups zijn gepland.

Zie [Offline back-upwerkstroom in Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)als u offline back-up wilt gebruiken op basis van de Azure Import/Export-service met de MARS-agent.

Zie [Offline back-upwerkstroom voor DPM en Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)als u hetzelfde wilt gebruiken, samen met MABS of DPM-A.

## <a name="offline-backup-support-summary"></a>Offline back-upondersteuningsoverzicht

In de volgende tabel worden de twee beschikbare opties vergeleken, zodat u de juiste keuzes maken op basis van uw scenario.

| **Overweging**                                            | **Offline back-up op basis van Azure Data Box**                     | **Offline back-up op basis van de Azure Import/Export-service**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup-implementatiemodellen                              | MARS-agent (preview)                                              | MARS Agent, MABS, DPM-A                                           |
| Maximale back-upgegevens per server (MARS) of per beveiligingsgroep (MABS, DPM-A) | [Azure Data Box-schijf](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7,2 TB <br> [Azure-gegevensvak](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (tot 10 schijven van elk 8 TB)                          |
| Beveiliging (gegevens, apparaat en service)                           | [Gegevens](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256-bits versleuteld <br> [Apparaat](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Robuuste behuizing, eigen, op referenties gebaseerde interface om gegevens te kopiëren <br> [Service](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Beveiligd door Azure-beveiligingsfuncties | Gegevens - BitLocker versleuteld                                 |
| Tijdelijke faseringslocatievoorziening                     | Niet vereist                                                | Meer dan of gelijk aan de geschatte grootte van back-upgegevens        |
| Ondersteunde regio’s                                           | [Schijfgebieden van Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box-regio's](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure-servicegebieden importeren/exporteren](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Verzending tussen het land                                     | Niet ondersteund  <br>    Bronadres en azure-datacenter moeten zich in hetzelfde land bevinden* | Ondersteund                                                    |
| Transfer logistiek (levering, transport, pick-up)           | Volledig Microsoft beheerd                                     | Klant beheerd                                            |
| Prijzen                                                      | [Azure Data Box-prijzen](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box-schijfprijzen](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prijzen voor Azure Import/Export-service](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Als uw land geen Azure-datacenter heeft, moet u uw schijven verzenden naar een Azure-datacenter in een ander land.

## <a name="next-steps"></a>Volgende stappen

* [Azure Backup offline back-up met Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Offline back-upwerkstroom in Azure Backup](backup-azure-backup-import-export.md) 
* [Offline back-upwerkstroom voor DPM en Azure Backup Server](backup-azure-backup-server-import-export-.md)
