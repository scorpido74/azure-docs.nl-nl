---
title: De Azure File Sync-agent implementeren
description: De Azure File Sync-agent implementeren. Een gemeen schappelijk tekst blok, gedeeld tussen migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209413"
---
In deze sectie installeert u de Azure File Sync-agent op uw Windows-Server.
In de [implementatie handleiding](../articles/storage/files/storage-sync-files-deployment-guide.md) ziet u dat u de **Verbeterde beveiliging van Internet Explorer**moet uitschakelen. Verbeterde beveiliging van Internet Explorer is een beveiligings maatregel die niet van toepassing is op Azure File Sync. Als u deze functie uitschakelt, kunt u zonder problemen verifiëren bij Azure.

Open Power shell en installeer de vereiste Power shell-modules met de volgende opdrachten. Zorg ervoor dat u de volledige module en de NuGet-provider installeert, wanneer u hierom wordt gevraagd:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Als er problemen zijn met het Internet vanaf uw server, is dit nu de tijd om deze op te lossen. Azure File Sync gebruikt een beschik bare netwerk verbinding met het internet.
Het vereisen van een proxy server voor het bereiken van Internet wordt ook ondersteund. U kunt nu een proxy voor alle computers configureren, of een proxy opgeven die alleen voor bestands synchronisatie wordt gebruikt tijdens de installatie van de agent.

Als dat het geval is, moet u de firewalls voor deze server openen, en dat kan een acceptabele benadering voor u zijn. Na voltooiing van de server-installatie wordt er na de registratie van de server een netwerk connectiviteits rapport weer gegeven met de exacte eindpunt-Url's in azure, waarna de bestands synchronisatie moet communiceren met voor de regio die u hebt geselecteerd. Het rapport geeft ook de reden aan waarom communicatie nodig is. U kunt het rapport gebruiken om de firewalls rond deze server op specifieke Url's te vergren delen.

U kunt ook een meer conservatieve benadering volgen, waarbij u de firewalls niet breed opent, maar de server wordt beperkt tot het gebruik van DNS-naam ruimten op een hoger niveau. er zijn meer documentatie en Details beschikbaar in het artikel [Azure file sync proxy en Firewall-instellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) . Volg uw aanbevolen procedures voor netwerk gebruik.

Aan het einde van de wizard Server- *installatie* wordt de wizard Server *registratie* weer gegeven.
Registreer de server bij de opslag synchronisatie service Azure-resource van vroeger.

Deze stappen worden uitgebreid beschreven in de implementatie handleiding, inclusief de bovenstaande Power shell-modules die u eerst moet installeren: [Azure file sync agent installeren](../articles/storage/files/storage-sync-files-deployment-guide.md).

De nieuwste agent moet worden gebruikt en kan worden gedownload van het micro soft Download centrum: [Azure file sync-agent](https://aka.ms/AFS/agent "Downloaden van Azure File Sync-agent").

Na een geslaagde installatie en Server registratie kunt u controleren of u deze stap hebt voltooid: Navigeer naar de resource van de opslag synchronisatie service in de Azure Portal en volg vervolgens het menu aan de linkerkant naar ' geregistreerde servers '. U ziet dat uw server meteen wordt weer gegeven.
