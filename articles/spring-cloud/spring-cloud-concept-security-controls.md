---
title: Concept beveiligings controles voor Azure lente-Cloud service
description: Gebruik beveiligings mechanismen die zijn ingebouwd in azure lente-Cloud service.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594981"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Beveiligings controles voor Azure lente-Cloud service
Beveiligings controles zijn ingebouwd in azure lente-Cloud service.

Een beveiligings controle is een kwaliteit of functie van een Azure-service die bijdraagt aan de mogelijkheid van de service om beveiligings problemen te voor komen, te detecteren en op te lossen.  Voor elk besturings element gebruiken we *Ja* of *Nee* om aan te geven of deze momenteel aanwezig is voor de service.  We gebruiken *n.v.t.* voor een besturings element dat niet van toepassing is op de service. 

**Beveiligings controles voor gegevens beveiliging**

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|:-------------|:-------|:-------------------------------|:----------------------|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Door de gebruiker geüploade bron en artefacten, configuratie server instellingen, app-instellingen en gegevens in permanente opslag worden opgeslagen in Azure Storage, die de inhoud automatisch versleutelt in rust.<br><br>Config server cache, runtime binaire bestanden die zijn gebouwd op basis van de geüploade bron en toepassings logboeken tijdens de levens duur van de toepassing worden opgeslagen op een door Azure beheerde schijf, waarmee de inhoud automatisch wordt versleuteld in rust.<br><br>Container installatie kopieën die zijn gebaseerd op de door de gebruiker geüploade bron worden opgeslagen in Azure Container Registry, die de installatie kopie-inhoud automatisch versleutelt in rust. | [Azure Storage-versleuteling voor inactieve gegevens](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Versleuteling aan de server zijde van Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Opslag van container installatie kopieën in Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Versleuteling in tijdelijke | Ja | Open bare eind punten van gebruikers-apps gebruiken standaard HTTPS voor binnenkomend verkeer. |  |
| Versleutelde API-aanroepen | Ja | Beheer aanroepen voor het configureren van Azure lente-Cloud service vindt plaats via Azure Resource Manager-aanroepen via HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

