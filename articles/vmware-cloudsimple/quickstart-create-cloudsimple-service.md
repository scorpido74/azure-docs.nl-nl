---
title: Azure VMware-oplossing op CloudSimple Quick start-service maken
description: Meer informatie over het maken van de CloudSimple-service, het aanschaffen van knoop punten en het reserveren van knoop punten
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574539"
---
# <a name="quickstart---create-cloudsimple-service"></a>Quick Start-CloudSimple-service maken

Om aan de slag te gaan, maakt u de Azure VMware-oplossing door CloudSimple in de Azure Portal.

Met de CloudSimple-service kunt u Azure VMware-oplossing gebruiken door CloudSimple.  Door de service te maken, kunt u knoop punten aanschaffen, knoop punten reserveren en persoonlijke clouds maken.  U voegt de CloudSimple-service toe aan elke Azure-regio waar de CloudSimple-service beschikbaar is.  De service definieert het Edge-netwerk van de Azure VMware-oplossing door CloudSimple.  Dit Edge-netwerk wordt gebruikt voor services die VPN, ExpressRoute en Internet connectiviteit met uw persoonlijke Clouds bevatten.

U moet een gateway-subnet maken om de CloudSimple-service toe te voegen. Het gateway-subnet wordt gebruikt bij het maken van het Edge-netwerk en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een van uw on-premises netwerk adres ruimten of de adres ruimte van het virtuele Azure-netwerk.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Resource provider micro soft. VMwareCloudSimple inschakelen

Volg de onderstaande stappen om de resource provider voor de CloudSimple-service in te scha kelen.

1. Selecteer **Alle services**.
2. Zoek en selecteer **abonnementen**.

    ![Abonnementen selecteren](media/cloudsimple-service-select-subscriptions.png)

3. Selecteer het abonnement waarvoor u de CloudSimple-service wilt inschakelen.
4. Klik op **resource providers** voor het abonnement.
5. Gebruik **micro soft. VMwareCloudSimple** om de resource provider te filteren.
6. Selecteer de resource provider **micro soft. VMwareCloudSimple** en klik op **registreren**.

    ![Resourceprovider registreren](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>De service maken

1. Selecteer **Alle services**.
2. Zoek naar de **CloudSimple-service**.

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

## <a name="purchase-nodes"></a>Knooppunten kopen

Als u betalen naar gebruik-capaciteit wilt instellen voor een CloudSimple Privécloud, moet u eerst knoop punten inrichten in de Azure Portal.

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple-knoop punten**.

    ![CloudSimple-knoop punten zoeken](media/create-cloudsimple-node-search.png)

3. Selecteer **CloudSimple-knoop punten**.
4. Klik op **toevoegen** om knoop punten te maken.

    ![CloudSimple-knoop punten toevoegen](media/create-cloudsimple-node-add.png)

5. Selecteer het abonnement waar u CloudSimple-knoop punten wilt kopen.
6. Selecteer de resource groep voor de knoop punten. Klik op **nieuwe maken**om een nieuwe resource groep toe te voegen.
7. Voer het voor voegsel in om de knoop punten te identificeren.
8. Selecteer de locatie voor de knooppunt resources.
9. Selecteer de toegewezen locatie om de knooppunt resources te hosten.
10. Selecteer het knooppunt type. U kunt de [optie CS28 of CS36](cloudsimple-node.md)kiezen. De laatste optie omvat de maximale reken-en geheugen capaciteit.
11. Selecteer het aantal knoop punten dat moet worden ingericht.
12. Selecteer **Controleren + maken**.
13. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
14. Selecteer **Maken**.

## <a name="next-steps"></a>Volgende stappen

* [Een Privécloud maken en omgeving configureren](quickstart-create-private-cloud.md)
* Meer informatie over de [CloudSimple-service](cloudsimple-service.md)
