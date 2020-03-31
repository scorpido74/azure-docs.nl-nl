---
title: DNS-zones beheren in Azure DNS - Azure-portal | Microsoft Documenten
description: U DNS-zones beheren met behulp van de Azure-portal. In dit artikel wordt beschreven hoe u DNS-zones op Azure DNS bijwerken, verwijderen en maken
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936792"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>DNS-zones beheren in de Azure-portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Klassieke versie van Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure-CLI](dns-operations-dnszones-cli.md)

In dit artikel ziet u hoe u uw DNS-zones beheert met behulp van de Azure-portal. U uw DNS-zones ook beheren met behulp van het cross-platform [Azure CLI](dns-operations-dnszones-cli.md) of de Azure [PowerShell.](dns-operations-dnszones.md)

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

1. Aanmelden bij Azure Portal
2. Navigeer in het menu Hub naar **Een bron > Netwerk > DNS-zone** maken om het **DNS-zoneblad maken** te openen.

    ![DNS-zone](./media/dns-operations-dnszones-portal/openzone650.png)

4. Voer op de blade **DNS-zone maken** de volgende waarden in en klik op **Maken**:


   | **Instelling** | **Waarde** | **Details** |
   |---|---|---|
   |**Naam**|contoso.com|De naam van de DNS-zone|
   |**Abonnement**|[Uw abonnement]|Selecteer een abonnement waarin de DNS-zone moet worden gemaakt.|
   |**Resourcegroep**|**Nieuwe maken:** contosoDNSRG|Maak een resourcegroep. De naam van de resourcegroep moet uniek zijn binnen het abonnement dat u hebt geselecteerd. Lees het [overzichtsartikel Resourcemanager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) voor meer informatie over resourcegroepen.|
   |**Locatie**|VS - west||

> [!NOTE]
> De resourcegroep verwijst naar de locatie van de resourcegroep en heeft geen invloed op de DNS-zone. De locatie van de DNS-zone is altijd 'global' en wordt niet weergegeven.

## <a name="list-dns-zones"></a>DNS-zones vermelden

Navigeer in de Azure-portal naar > **DNS-zones voor** > **netwerknetwerken** **met meer services.** Elke DNS-zone is zijn eigen bron en informatie zoals het aantal recordsets en naamservers zijn zichtbaar vanuit deze weergave. De **kolomNAAMSERVERS** bevindt zich niet in de standaardweergave. Als u deze wilt toevoegen, klikt u op **Kolommen,** selecteert u **Naamservers**en klikt u op **Gereed**.

![DNS-zones aanbieden](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Een DNS-zone verwijderen

Navigeer naar een DNS-zone in de portal. Klik op het **DNS-zoneblad** op **Zone verwijderen**. U wordt dan gevraagd om te bevestigen dat u de DNS-zone wilt verwijderen. Als u een DNS-zone verwijdert, worden ook alle records in de zone verwijderd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het werken met uw DNS-zone en records door naar [Aan de slag te gaan met Azure DNS via de Azure-portal.](dns-getstarted-portal.md)