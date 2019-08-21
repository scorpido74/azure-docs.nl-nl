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
ms.openlocfilehash: 0f589adf5cbcd04b9642dfe1362fd13d5be1f9aa
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640640"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>De Azure VMware-oplossing maken op basis van de CloudSimple-service

Om aan de slag te gaan met de Azure VMware-oplossing door CloudSimple, maakt u de Azure VMware-oplossing door CloudSimple-service in de Azure Portal.

> [!IMPORTANT]
> Voordat u de CloudSimple-service maakt, moet u de resource provider micro soft. VMwareCloudSimple registreren bij uw Azure-abonnement. Volg de stappen in [de resource provider micro soft. VMwareCloudSimple inschakelen voor uw Azure-abonnement](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](http://portal.azure.com).

## <a name="create-the-service"></a>De service maken

1. Selecteer **Alle services**.
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

* Meer informatie over het [maken van een privécloud](create-private-cloud.md)
* Meer informatie over het [configureren van een privécloud](quickstart-create-private-cloud.md)
