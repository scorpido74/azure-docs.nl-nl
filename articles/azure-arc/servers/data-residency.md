---
title: Gegevenslocatie
description: Gegevens locatie en informatie over Azure Arc-servers (preview).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 8f207f5889c1764eebcc6081960ff70c0d5bca3a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048853"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Servers met Azure Arc ingeschakeld (preview): data locatie

In dit artikel wordt uitgelegd wat het concept van gegevens locatie is en hoe dit van toepassing is op servers met Azure Arc-functionaliteit (preview).

Servers met Azure-Arc (preview) **[zijn beschikbaar als preview-versie](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** in de **Verenigde Staten, Europe of Azië en Stille Oceaan**.

## <a name="data-residency"></a>Gegevenslocatie

Azure Arc-servers (preview) configuratie-instellingen voor [Azure VM-extensie](manage-vm-extensions.md) opslaan (dat wil zeggen, eigenschaps waarden) voor de uitbrei ding moet worden opgegeven voordat er wordt geprobeerd op de verbonden computer in te scha kelen. Wanneer u bijvoorbeeld de VM-extensie Log Analytics inschakelt, wordt u gevraagd om de Log Analytics **werk ruimte-id** en de **primaire sleutel**.

Er wordt ook informatie over de meta gegevens van de verbonden computer verzameld. Met name:

* Naam en versie van het besturings systeem
* Computernaam
* Computer Fully Qualified Domain Name (FQDN)
* Versie van de verbonden machine agent

Met Arc ingeschakelde servers (preview) kunt u de regio opgeven waar uw gegevens worden opgeslagen. Micro soft kan repliceren naar andere regio's voor gegevens tolerantie, maar micro soft repliceert of verplaatst geen gegevens buiten de geografie. Deze gegevens worden opgeslagen in de regio waar de Azure Arc machine-resource is geconfigureerd. Als de computer bijvoorbeeld is geregistreerd met de Arc in de regio VS-Oost, worden deze gegevens opgeslagen in de regio vs.

Zie voor meer informatie over onze regionale tolerantie en nalevings ondersteuning [Azure geografie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwerpen van [Azure-tolerantie](/azure/architecture/reliability/architect).