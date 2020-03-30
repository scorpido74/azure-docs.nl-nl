---
title: De Azure File Sync-agent implementeren
description: De Azure File Sync-agent implementeren. Een gemeenschappelijk tekstblok, gedeeld tussen migratiedocumenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209413"
---
In deze sectie installeert u de Azure File Sync-agent op uw Windows Server.
De [implementatiehandleiding](../articles/storage/files/storage-sync-files-deployment-guide.md) illustreert dat u de verbeterde beveiliging van **IE**moet uitschakelen. IE enhanced security is een beveiligingsmaatregel die niet van toepassing is met Azure File Sync en door deze uit te schakelen u zonder problemen verifiëren naar Azure.

Open PowerShell en installeer de vereiste PowerShell-modules met de volgende opdrachten. Zorg ervoor dat u de volledige module en de NuGet-provider installeert wanneer u daarom wordt gevraagd:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Als u problemen hebt met het bereiken van het internet van uw server, is dit het moment om ze op te lossen. Azure File Sync maakt gebruik van elke beschikbare netwerkverbinding met het internet.
Het vereisen van een proxyserver om het internet te bereiken wordt ook ondersteund. U nu een machinebrede proxy configureren of een proxy opgeven die alleen bestandssynchronisatie zal gebruiken tijdens de installatie van de agent.

Als dat betekent dat u uw firewalls voor deze server moet openen, dan is dat misschien een aanvaardbare benadering voor u. Aan het einde van de serverinstallatie, na voltooiing van de serverregistratie, wordt een rapport over de netwerkconnectiviteit weergegeven met de exacte eindpunt-URL's in Azure, waarmee bestandssynchronisatie moet communiceren voor de regio die u hebt geselecteerd. Het rapport vertelt u ook de reden waarom communicatie nodig is. U het rapport gebruiken om vervolgens de firewalls rond deze server te vergrendelen naar specifieke URL's.

U ook een conservatievere benadering volgen, waarbij u de firewalls niet breed opent, maar in plaats daarvan de server beperkt om te communiceren naar DNS-naamruimten op hoger niveau - er is meer documentatie en details beschikbaar in het artikel [azure file sync proxy en firewall-instellingen.](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) Volg uw eigen netwerkbest practices.

Aan het einde van de wizard *serverinstallatie* verschijnt een wizard *serverregistratie.*
Registreer de server van eerder op uw Azure-bron voor Storage Sync Service.

Deze stappen worden in de implementatiehandleiding nader beschreven, inclusief de bovenstaande PowerShell-modules die u eerst moet installeren: [Azure File Sync-agent installeert](../articles/storage/files/storage-sync-files-deployment-guide.md).

De nieuwste agent moet worden gebruikt en kan worden gedownload van Microsoft Download Center: [Azure File Sync - agent](https://aka.ms/AFS/agent "Azure File Sync-agent downloaden").

Na een succesvolle installatie- en serverregistratie u controleren of u deze stap hebt voltooid: Navigeer naar de bron Storage Sync Service in de Azure-portal en volg het linkermenu naar 'Geregistreerde servers'. U ziet uw server daar meteen vermeld.
