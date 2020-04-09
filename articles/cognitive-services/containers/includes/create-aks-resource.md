---
title: Een Azure Kubernetes Service-clusterbron maken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een AKS-bron (Azure Kubernetes Service).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877811"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Een Azure Kubernetes Service-clusterbron maken

1. Ga naar [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)en selecteer **Maken**.

1. Voer op het tabblad **Basisbeginselen** de volgende gegevens in:

    |Instelling|Waarde|
    |--|--|
    |Abonnement|Selecteer een geschikt abonnement.|
    |Resourcegroep|Selecteer een beschikbare resourcegroep.|
    |Kubernetes-clusternaam|Voer een naam (kleine letters) in.|
    |Regio|Selecteer een locatie in de buurt.|
    |Kubernetes-versie|Welke waarde is gemarkeerd als **(standaard)**.|
    |DNS-naamvoorvoegsel|Automatisch gemaakt, maar u overschrijven.|
    |Grootte van knooppunt|Standaard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Aantal knooppunts|Laat de schuifregelaar op de standaardwaarde.|

1. Laat op het tabblad **Schalen** **virtuele knooppunten** en **VM-schaalsets** instellen op hun standaardwaarden.
1. Laat op het tabblad **Verificatie** **serviceprincipal** en **Inschakel RBAC** instellen op de standaardwaarden.
1. Voer op het tabblad **Netwerken** de volgende selecties in:

    |Instelling|Waarde|
    |--|--|
    |Routering van HTTP-toepassing|Nee|
    |Netwerkconfiguratie|Basic|

1. Controleer op het tabblad **Controle** of **Containerbewaking inschakelen** is ingesteld op **Ja**en laat de werkruimte **Log Analytics** als standaardwaarde.
1. Laat op het tabblad **Labels** de naam/waardeparen voorlopig leeg.
1. Selecteer **Controleren en maken**.
1. Nadat de validatie is doorgegeven, selecteert **u Maken**.

> [!NOTE]
> Als de validatie mislukt, kan dit het gevolg zijn van een fout van de 'Serviceprincipal'. Ga terug naar het tabblad **Verificatie** en ga terug naar **Controleren + maken,** waar validatie moet worden uitgevoerd en vervolgens moet worden uitgevoerd.
