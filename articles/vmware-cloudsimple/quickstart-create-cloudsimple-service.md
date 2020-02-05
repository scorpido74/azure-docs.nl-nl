---
title: Snelstartgids voor Azure VMware Solutions (AVS)-service maken
description: Meer informatie over het maken van de AVS-service, het aanschaffen van knoop punten en het reserveren van knoop punten
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024429"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Quick Start: een AVS-service (Azure VMware Solutions) maken

Om aan de slag te gaan, maakt u de Azure VMware-oplossingen (AVS) in de Azure Portal.

## <a name="vmware-solutions-avs---service-overview"></a>VMware-oplossingen (AVS)-overzicht van de service

Met de AVS-service kunt u de Azure VMware-oplossing door AVS gebruiken. Door de service te maken, kunt u knoop punten inrichten, knoop punten reserveren en Privécloud persoonlijke clouds maken. U voegt de AVS-service toe aan elke Azure-regio waar de AVS-service beschikbaar is. De service definieert het Edge-netwerk van de Azure VMware-oplossing door AVS. Dit Edge-netwerk wordt gebruikt voor services die VPN, ExpressRoute en Internet connectiviteit bevatten voor uw AVS-persoonlijke Clouds.

U moet een gateway-subnet maken om de AVS-service toe te voegen. Het gateway-subnet wordt gebruikt bij het maken van het Edge-netwerk en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een van uw on-premises netwerk adres ruimten of de adres ruimte van het virtuele Azure-netwerk.

## <a name="before-you-begin"></a>Voordat u begint

Een/28 CIDR-blok voor gateway-subnet toewijzen. Een gateway-subnet is vereist per AVS-service en is uniek voor de regio waarin het is gemaakt. Het gateway-subnet wordt gebruikt voor de Azure VMware-oplossing door te AVS Edge netwerk services en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de AVS-omgeving. De netwerken die communiceren met AVS, zijn onder andere on-premises netwerken en Azure Virtual Networks.

Controleer de [netwerk vereisten](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>De service maken

1. Selecteer **Alle services**.
2. Zoeken naar **AVS-service**.

    ![Search AVS-service](media/create-cloudsimple-service-search.png)

3. Selecteer **AVS-Services**.
4. Klik op **toevoegen** om een nieuwe service te maken.

    ![AVS-service toevoegen](media/create-cloudsimple-service-add.png)

5. Selecteer het abonnement waar u de AVS-service wilt maken.
6. Selecteer de resource groep voor de service. Klik op **nieuwe maken**om een nieuwe resource groep toe te voegen.
7. Voer een naam in om de service te identificeren.
8. Voer de CIDR in voor de service gateway. Geef een/28-subnet op dat niet overlapt met een van uw on-premises subnetten, Azure-subnetten of geplande AVS-subnetten. U kunt de CIDR niet wijzigen nadat de service is gemaakt.

    ![De AVS-service maken](media/create-cloudsimple-service.png)

9. Klik op **OK**.

De service wordt gemaakt en toegevoegd aan de lijst met Services.

## <a name="provision-nodes"></a>Knooppunten inrichten

Als u betalen naar gebruik-capaciteit wilt instellen voor een Privécloud-cloud omgeving, moet u eerst knoop punten inrichten in de Azure Portal.

1. Selecteer **Alle services**.
2. Zoeken naar **AVS-knoop punten**.

    ![AVS-knoop punten zoeken](media/create-cloudsimple-node-search.png)

3. Selecteer de **AVS-knoop punten**.
4. Klik op **toevoegen** om knoop punten te maken.

    ![AVS-knoop punten toevoegen](media/create-cloudsimple-node-add.png)

5. Selecteer het abonnement waar u de AVS-knoop punten wilt inrichten.
6. Selecteer de resource groep voor de knoop punten. Klik op **nieuwe maken**om een nieuwe resource groep toe te voegen.
7. Voer het voor voegsel in om de knoop punten te identificeren.
8. Selecteer de locatie voor de knooppunt resources.
9. Selecteer de toegewezen locatie om de knooppunt resources te hosten.
10. Selecteer het [knooppunt type](cloudsimple-node.md).
11. Selecteer het aantal knoop punten dat moet worden ingericht.
12. Selecteer **Controleren + maken**.
13. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
14. Selecteer **Maken**.

## <a name="next-steps"></a>Volgende stappen

* [Een Privécloud maken en omgeving configureren](quickstart-create-private-cloud.md)
* Meer informatie over de [AVS-service](https://docs.azure.cloudsimple.com/cloudsimple-service)
