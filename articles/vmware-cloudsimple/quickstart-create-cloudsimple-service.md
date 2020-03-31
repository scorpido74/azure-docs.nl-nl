---
title: 'Snelstart: VMware CloudSimple-service maken'
titleSuffix: Azure VMware Solution by CloudSimple
description: Meer informatie over het maken van de CloudSimple-service, inkoopknooppunten en het reserveren van knooppunten
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ca8c5cacd2b1a1a7b4f70615831d2901510045e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024429"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Quickstart - Azure VMware-oplossing maken met CloudSimple-service

Maak de Azure VMware-oplossing van CloudSimple in de Azure-portal om aan de slag te gaan.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple - Serviceoverzicht

Met de CloudSimple-service u Azure VMware-oplossing van CloudSimple gebruiken.  Als u de service maakt, u knooppunten inrichten, knooppunten reserveren en privéclouds maken.  U voegt de CloudSimple-service toe in elke Azure-regio waar de CloudSimple-service beschikbaar is.  De service definieert het edge-netwerk van Azure VMware-oplossing door CloudSimple.  Dit edge-netwerk wordt gebruikt voor services zoals VPN, ExpressRoute en internetverbinding met uw privéclouds.

Als u de CloudSimple-service wilt toevoegen, moet u een gateway-subnet maken. Het gatewaysubnet wordt gebruikt bij het maken van het edge-netwerk en vereist een /28 CIDR-blok. De subnetadresruimte van de gateway moet uniek zijn. Het kan niet overlappen met een van uw on-premises netwerkadresruimten of Azure virtuele netwerkadresruimte.

## <a name="before-you-begin"></a>Voordat u begint

Wijs een /28 CIDR-blok toe voor gatewaysubnet.  Een gatewaysubnet is vereist per CloudSimple-service en is uniek voor de regio waarin het is gemaakt. Het gatewaysubnet wordt gebruikt voor Azure VMware Solution door CloudSimple edge network services en vereist een /28 CIDR-blok. De subnetadresruimte van de gateway moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving.  De netwerken die communiceren met CloudSimple omvatten on-premises netwerken en Virtuele Azure-netwerken.

Netwerkvereisten [bekijken](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="create-the-service"></a>De service maken

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Service**.

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

## <a name="provision-nodes"></a>Knooppunten inrichten

Als u de capaciteit voor betalen per gebruik wilt instellen voor een CloudSimple Private Cloud-omgeving, worden knooppunten in de eerste voorziening in de Azure-portal ingericht.

1. Selecteer **Alle services**.
2. Zoeken naar **CloudSimple-knooppunten**.

    ![Zoeken in CloudSimple-knooppunten](media/create-cloudsimple-node-search.png)

3. Selecteer **CloudSimple-knooppunten**.
4. Klik **op Toevoegen** om knooppunten te maken.

    ![CloudSimple-knooppunten toevoegen](media/create-cloudsimple-node-add.png)

5. Selecteer het abonnement waar u CloudSimple-knooppunten wilt inrichten.
6. Selecteer de brongroep voor de knooppunten. Als u een nieuwe resourcegroep wilt toevoegen, klikt u op **Nieuw maken**.
7. Voer het voorvoegsel in om de knooppunten te identificeren.
8. Selecteer de locatie voor de knooppuntbronnen.
9. Selecteer de specifieke locatie om de knooppuntbronnen te hosten.
10. Selecteer het [knooppunttype](cloudsimple-node.md).
11. Selecteer het aantal knooppunten dat u wilt inrichten.
12. Selecteer **Controleren + maken**.
13. Bekijk de instellingen. Als u instellingen wilt wijzigen, klikt u op **Vorige**.
14. Selecteer **Maken**.

## <a name="next-steps"></a>Volgende stappen

* [Private Cloud maken en omgeving configureren](quickstart-create-private-cloud.md)
* Meer informatie over [cloudsimple-service](https://docs.azure.cloudsimple.com/cloudsimple-service)
