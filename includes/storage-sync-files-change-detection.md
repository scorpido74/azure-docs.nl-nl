---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391615"
---
Wijzigingen die zijn aangebracht in het Azure-bestandsaandeel met behulp van de Azure-portal of SMB worden niet onmiddellijk gedetecteerd en gerepliceerd, zoals wijzigingen in het eindpunt van de server. Azure Files heeft nog geen wijzigingsmeldingen of journaling, dus er is geen manier om automatisch een synchronisatiesessie te starten wanneer bestanden worden gewijzigd. Op Windows Server gebruikt Azure File Sync [Windows USN journaling](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) om automatisch een synchronisatiesessie te starten wanneer bestanden worden gewijzigd.

Als u wijzigingen in de Azure-bestandsshare wilt detecteren, heeft Azure File Sync een geplande taak die een *wijzigingsdetectietaak wordt genoemd.* Een taak voor het detecteren van wijzigingen someert elk bestand in het bestandsshare en vergelijkt het vervolgens met de synchronisatieversie voor dat bestand. Wanneer de wijzigingsdetectietaak bepaalt dat bestanden zijn gewijzigd, start Azure File Sync een synchronisatiesessie. De taak voor het detecteren van wijzigingen wordt elke 24 uur gestart. Omdat de wijzigingsdetectietaak werkt door elk bestand in het Azure-bestandsaandeel op te sommen, duurt wijzigingsdetectie langer in grotere naamruimten dan in kleinere naamruimten. Voor grote naamruimten kan het langer dan eens per 24 uur duren om te bepalen welke bestanden zijn gewijzigd.

Als u bestanden die in de Azure-bestandsshare worden gewijzigd, onmiddellijk wilt synchroniseren, kan de PowerDlet **Invoke-AzStorageChangeDetection** PowerShell worden gebruikt om handmatig de detectie van wijzigingen in de Azure-bestandsshare te starten. Deze cmdlet is bedoeld voor scenario's waarbij een of ander type geautomatiseerd proces wijzigingen aanbrengt in de Azure-bestandsshare of de wijzigingen worden uitgevoerd door een beheerder (zoals het verplaatsen van bestanden en mappen naar het aandeel). Voor wijzigingen van eindgebruikers is de aanbeveling om de Azure File Sync-agent in een IaaS-vm te installeren en eindgebruikers toegang te geven tot het bestandsaandeel via de IaaS-VM. Op deze manier worden alle wijzigingen snel gesynchroniseerd met andere agents zonder dat u de cmdlet Invoke-AzStorageSyncChangeDetection hoeft te gebruiken. Zie de documentatie [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) voor meer informatie.

>[!NOTE]
>Wijzigingen die zijn aangebracht in een Azure-bestandsshare met REST, werken de laatst gewijzigde smb niet bij en worden niet gezien als een wijziging door synchronisatie.

We onderzoeken het toevoegen van wijzigingsdetectie voor een Azure-bestandsaandeel dat vergelijkbaar is met USN voor volumes op Windows Server. Help ons deze functie te prioriteren voor toekomstige ontwikkeling door ervoor te stemmen bij [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
