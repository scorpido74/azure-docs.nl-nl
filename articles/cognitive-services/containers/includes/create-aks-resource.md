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
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298829"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Een Azure Kubernetes service-cluster resource maken

1. Ga naar de [Azure Kubernetes-service](https://ms.portal.azure.com/#create/microsoft.aks)en selecteer **maken**.

1. Voer op het tabblad **basis beginselen** de volgende gegevens in:

    |Instelling|Waarde|
    |--|--|
    |Abonnement|Selecteer een geschikt abonnement.|
    |Resourcegroep|Selecteer een beschikbare resourcegroep.|
    |Kubernetes-cluster naam|Voer een naam in (kleine letters).|
    |Regio|Selecteer een locatie in de buurt.|
    |Kubernetes-versie|Wille keurige waarde is gemarkeerd als **(standaard)**.|
    |DNS-naam voorvoegsel|Automatisch gemaakt, maar u kunt dit overschrijven.|
    |Knooppuntgrootte|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Aantal knoop punten|Wijzig de schuif regelaar op de standaard waarde.|

1. Op het tabblad **knooppunt groepen** kunt u **virtuele knoop punten** en **VM-schaal sets** instellen op de standaard waarden.
1. Op het tabblad **verificatie** verlaat u **Service-Principal** en **schakelt u RBAC** in op de standaard waarden.
1. Op het tabblad **netwerken** voert u de volgende selecties in:

    |Instelling|Waarde|
    |--|--|
    |Routering van HTTP-toepassing|Nee|
    |Netwerk configuratie|Basic|

1. Controleer op het tabblad **integraties** of de **container controle** is ingesteld op **ingeschakeld**en laat **log Analytics werk ruimte** staan als de standaard waarde.
1. Laat op het tabblad **Tags** de naam/waarde-paren voor nu leeg.
1. Selecteer **controleren en maken**.
1. Nadat de validatie is geslaagd, selecteert u **maken**.

> [!NOTE]
> Als de validatie mislukt, kan dit worden veroorzaakt door een ' Service Principal-fout. Ga terug naar het tabblad **verificatie** en ga terug naar weer **geven + maken**, waarbij validatie moet worden uitgevoerd en voer vervolgens door.
