---
title: Een Azure Kubernetes service-cluster resource maken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een AKS-resource (Azure Kubernetes service).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877811"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Een Azure Kubernetes service-cluster resource maken

1. Ga naar de [Azure Kubernetes-service](https://ms.portal.azure.com/#create/microsoft.aks)en selecteer **maken**.

1. Voer op het tabblad **basis beginselen** de volgende gegevens in:

    |Instelling|Waarde|
    |--|--|
    |Abonnement|Selecteer een geschikt abonnement.|
    |Resourcegroep|Selecteer een beschik bare resource groep.|
    |Kubernetes-cluster naam|Voer een naam in (kleine letters).|
    |Regio|Selecteer een locatie in de buurt.|
    |Kubernetes-versie|Wille keurige waarde is gemarkeerd als **(standaard)**.|
    |DNS-naam voorvoegsel|Automatisch gemaakt, maar u kunt dit overschrijven.|
    |Knooppunt grootte|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Aantal knoop punten|Wijzig de schuif regelaar op de standaard waarde.|

1. Op het tabblad **schaal** kunt u **virtuele knoop punten** en **VM-schaal sets** instellen op de standaard waarden.
1. Op het tabblad **verificatie** verlaat u **Service-Principal** en **schakelt u RBAC** in op de standaard waarden.
1. Op het tabblad **netwerken** voert u de volgende selecties in:

    |Instelling|Waarde|
    |--|--|
    |Routering van HTTP-toepassing|Nee|
    |Netwerk configuratie|Basic|

1. Controleer op het tabblad **controle** of **container controle inschakelen** is ingesteld op **Ja**en laat **log Analytics werk ruimte** staan als de standaard waarde.
1. Laat op het tabblad **Tags** de naam/waarde-paren voor nu leeg.
1. Selecteer **controleren en maken**.
1. Nadat de validatie is geslaagd, selecteert u **maken**.

> [!NOTE]
> Als de validatie mislukt, kan dit worden veroorzaakt door een ' Service Principal-fout. Ga terug naar het tabblad **verificatie** en ga terug naar weer **geven + maken**, waarbij validatie moet worden uitgevoerd en voer vervolgens door.
