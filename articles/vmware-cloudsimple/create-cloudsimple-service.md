---
title: Azure VMware-oplossing door CloudSimple - CloudSimple-service maken
description: Beschrijft hoe u de CloudSimple-service maakt in de Azure-portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024820"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>De Azure VMware-oplossing maken op cloudeenvoudige service

Als u aan de slag wilt met Azure VMware Solution by CloudSimple, maakt u de Azure VMware Solution by CloudSimple-service in de Azure-portal.

## <a name="before-you-begin"></a>Voordat u begint

Wijs een CIDR-blok van /28 toe voor het gatewaysubnet. Een gatewaysubnet is vereist per CloudSimple-service en is uniek voor de regio waarin het is gemaakt. Het gatewaysubnet wordt gebruikt voor edge-netwerkservices en vereist een /28 CIDR-blok. De subnetadresruimte van de gateway moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving. De netwerken die communiceren met CloudSimple omvatten on-premises netwerken en Virtuele Azure-netwerken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-the-service"></a>De service maken

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
    ![CloudSimple-service zoeken](media/create-cloudsimple-service-search.png)
3. Selecteer **CloudSimple Services**.
4. Klik **op Toevoegen** om een nieuwe service te maken.
    ![CloudSimple-service toevoegen](media/create-cloudsimple-service-add.png)
5. Selecteer het abonnement waar u de CloudSimple-service wilt maken.
6. Selecteer de resourcegroep voor de service. Als u een nieuwe resourcegroep wilt toevoegen, klikt u op **Nieuw maken**.
7. Voer de naam in om de service te identificeren.
8. Voer de CIDR voor de servicegateway in. Geef een /28-subnet op dat niet overlapt met een van uw on-premises subnetten, Azure-subnetten of geplande CloudSimple-subnetten. U de CIDR niet wijzigen nadat de service is gemaakt.

    ![De CloudSimple-service maken](media/create-cloudsimple-service.png)
9. Klik op **OK**.

De service wordt gemaakt en toegevoegd aan de lijst met services.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inrichten van knooppunten](create-nodes.md)
* Meer informatie over het [maken van een private cloud](create-private-cloud.md)
* Meer informatie over het [configureren van een private cloudomgeving](quickstart-create-private-cloud.md)
