---
title: 'Azure VMware-oplossing op CloudSimple: CloudSimple-service maken'
description: Hierin wordt beschreven hoe u de CloudSimple-service maakt in de Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024820"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>De Azure VMware-oplossing maken op basis van de CloudSimple-service

Om aan de slag te gaan met de Azure VMware-oplossing door CloudSimple, maakt u de Azure VMware-oplossing door CloudSimple-service in de Azure Portal.

## <a name="before-you-begin"></a>Voordat u begint

Wijs een/28 CIDR-blok toe voor het gateway-subnet. Een gateway-subnet is vereist per CloudSimple-service en is uniek voor de regio waarin het is gemaakt. Het gateway-subnet wordt gebruikt voor Edge-netwerk services en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving. De netwerken die met CloudSimple communiceren, zijn onder andere on-premises netwerken en Azure Virtual Networks.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-the-service"></a>De service maken

1. Selecteer **alle services**.
2. Zoek naar **CloudSimple Services**.
    ![CloudSimple-service zoeken](media/create-cloudsimple-service-search.png)
3. Selecteer **CloudSimple Services**.
4. Klik op **toevoegen** om een nieuwe service te maken.
    ![CloudSimple-service toevoegen](media/create-cloudsimple-service-add.png)
5. Selecteer het abonnement waar u de CloudSimple-service wilt maken.
6. Selecteer de resource groep voor de service. Klik op **nieuwe maken**om een nieuwe resource groep toe te voegen.
7. Voer een naam in om de service te identificeren.
8. Voer de CIDR in voor de service gateway. Geef een/28-subnet op dat niet overlapt met een van uw on-premises subnetten, Azure-subnetten of geplande CloudSimple-subnetten. U kunt de CIDR niet wijzigen nadat de service is gemaakt.

    ![De CloudSimple-service maken](media/create-cloudsimple-service.png)
9. Klik op **OK**.

De service wordt gemaakt en toegevoegd aan de lijst met Services.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inrichten van knoop punten](create-nodes.md)
* Meer informatie over het [maken van een privécloud](create-private-cloud.md)
* Meer informatie over het [configureren van een privécloud](quickstart-create-private-cloud.md)
