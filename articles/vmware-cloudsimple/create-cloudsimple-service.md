---
title: Azure VMware-oplossingen (AVS)-AVS-service maken
description: Hierin wordt beschreven hoe u de AVS-service maakt in de Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024820"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>De Azure VMware Solutions (AVS)-service maken

Om aan de slag te gaan met Azure VMware-oplossingen (AVS), maakt u de Azure VMware Solutions (AVS)-service in de Azure Portal.

## <a name="before-you-begin"></a>Voordat u begint

Wijs een/28 CIDR-blok toe voor het gateway-subnet. Een gateway-subnet is vereist per AVS-service en is uniek voor de regio waarin het is gemaakt. Het gateway-subnet wordt gebruikt voor Edge-netwerk services en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de AVS-omgeving. De netwerken die communiceren met AVS, zijn onder andere on-premises netwerken en Azure Virtual Networks.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure Portal](https://portal.azure.com).

## <a name="create-the-service"></a>De service maken

1. Selecteer **Alle services**.
2. Zoeken naar **AVS-Services**.
    ![Search AVS-service](media/create-cloudsimple-service-search.png)
3. Selecteer **AVS-Services**.
4. Klik op **toevoegen** om een nieuwe service te maken.
    AVS-service ![toevoegen](media/create-cloudsimple-service-add.png)
5. Selecteer het abonnement waar u de AVS-service wilt maken.
6. Selecteer de resource groep voor de service. Klik op **nieuwe maken**om een nieuwe resource groep toe te voegen.
7. Voer een naam in om de service te identificeren.
8. Voer de CIDR in voor de service gateway. Geef een/28-subnet op dat niet overlapt met een van uw on-premises subnetten, Azure-subnetten of geplande AVS-subnetten. U kunt de CIDR niet wijzigen nadat de service is gemaakt.

    ![De AVS-service maken](media/create-cloudsimple-service.png)
9. Klik op **OK**.

De service wordt gemaakt en toegevoegd aan de lijst met Services.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inrichten van knoop punten](create-nodes.md)
* Meer informatie over het [maken van een AVS-privécloud](create-private-cloud.md)
* Meer informatie over het [configureren van een persoonlijke cloud omgeving van een AVS](quickstart-create-private-cloud.md)
