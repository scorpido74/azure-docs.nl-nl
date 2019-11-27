---
title: Een Azure Kubernetes service-cluster resource maken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een AKS-resource (Azure Kubernetes service).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383437"
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
    |Kubernetes-versie|Wille keurige waarde is gemarkeerd als **(standaard)** .|
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
