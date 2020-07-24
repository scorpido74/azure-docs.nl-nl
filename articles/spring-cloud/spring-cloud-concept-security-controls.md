---
title: Concept beveiligings controles voor Azure lente-Cloud service
description: Gebruik beveiligings mechanismen die zijn ingebouwd in azure lente-Cloud service.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2e001e5e927d9d4c5dc4c3eb74f7b5ad33617b99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037573"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Beveiligingscontroles voor Azure Spring Cloud Service
Beveiligings controles zijn ingebouwd in azure lente-Cloud service.

Een beveiligings controle is een kwaliteit of functie van een Azure-service die bijdraagt aan de mogelijkheid van de service om beveiligings problemen te voor komen, te detecteren en op te lossen.  Voor elk besturings element gebruiken we *Ja* of *Nee* om aan te geven of deze momenteel aanwezig is voor de service.  We gebruiken *n.v.t.* voor een besturings element dat niet van toepassing is op de service. 

**Beveiligings controles voor gegevens beveiliging**

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|:-------------|:-------|:-------------------------------|:----------------------|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Yes | Door de gebruiker geüploade bron en artefacten, configuratie server instellingen, app-instellingen en gegevens in permanente opslag worden opgeslagen in Azure Storage, die de inhoud automatisch versleutelt in rust.<br><br>Config server cache, runtime binaire bestanden die zijn gebouwd op basis van de geüploade bron en toepassings logboeken tijdens de levens duur van de toepassing worden opgeslagen op een door Azure beheerde schijf, waarmee de inhoud automatisch wordt versleuteld in rust.<br><br>Container installatie kopieën die zijn gebaseerd op de door de gebruiker geüploade bron worden opgeslagen in Azure Container Registry, die de installatie kopie-inhoud automatisch versleutelt in rust. | [Azure Storage-versleuteling voor inactieve gegevens](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Versleuteling aan de server zijde van Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Opslag van container installatie kopieën in Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Versleuteling in tijdelijke | Yes | Open bare eind punten van gebruikers-apps gebruiken standaard HTTPS voor binnenkomend verkeer. |  |
| Versleutelde API-aanroepen | Yes | Beheer aanroepen voor het configureren van Azure lente-Cloud service vindt plaats via Azure Resource Manager-aanroepen via HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**Beveiligings beheer van netwerk toegang**

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie |
|:-------------|:-------|:-------------------------------|:----------------------|
| Servicetag | Yes | Gebruik **AzureSpringCloud** service tag om uitgaande netwerk toegangs beheer voor [netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) of [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags)te definiëren om verkeer naar Azure lente-Cloud toepassingen toe te staan.<br><br>*Opmerking:* Momenteel alleen nieuwe Azure lente-Cloud service-exemplaar gemaakt na 2020/07/14 ondersteunt **AzureSpringCloud** -service label. | [Servicetags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |
