---
title: Azure VMware-oplossingen (AVS)-Access vSphere-client
description: Hierin wordt beschreven hoe u toegang krijgt tot vCenter vanuit de Privécloud van uw AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022661"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Toegang tot de vCenter-portal van uw AVS-Privécloud

U kunt uw AVS-portal voor de Privécloud van uw Cloud starten vanuit Azure Portal of de AVS-Portal. met de vCenter-Portal kunt u VMware-infra structuur beheren in de Privécloud van uw AVS.

## <a name="before-you-begin"></a>Voordat u begint

Er moet een netwerk verbinding tot stand worden gebracht en DNS-naam omzetting moet zijn ingeschakeld voor toegang tot de vCenter-Portal. U kunt met behulp van de onderstaande opties een netwerk verbinding met de Privécloud van uw AVS tot stand brengen.

* [Verbinding maken tussen on-premises en AVS met ExpressRoute](on-premises-connection.md)
* [Een VPN-verbinding met de Privécloud van uw AVS configureren](set-up-vpn.md)

Voor het instellen van de DNS-naam omzetting van uw onderdelen uit de VMware-infra structuur van uw AVS, raadpleegt [u DNS configureren voor naam omzetting voor automatische AVS-toegang tot de Cloud vCenter vanaf on-premises werk stations](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Toegang tot vCenter vanuit Azure Portal

U kunt de vCenter-portal van de Privécloud van uw AVS starten vanuit Azure Portal.

1. Selecteer **Alle services**.

2. Zoeken naar **AVS-Services**.

3. Selecteer de AVS-service van de Privécloud van uw AVS waarmee u verbinding wilt maken.

4. Klik op de pagina **overzicht** op **persoonlijke Clouds van VMware-AVS weer geven**

    ![Overzicht van AVS-service](media/cloudsimple-service-overview.png)

5. Selecteer in de lijst met persoonlijke Clouds de AVS en klik op **vSphere-client starten**.

    ![VSphere-client starten](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Toegang tot vCenter vanuit de AVS-Portal

U kunt de vCenter-portal van de Privécloud van uw AVS starten vanuit de AVS-Portal.

1. Toegang tot uw [AVS-Portal](access-cloudsimple-portal.md).

2. Selecteer vanuit de **resources** de automatische AVS-Cloud die u wilt openen en klik op **VSphere-client starten**.

    ![VSphere-client starten-resources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. U kunt ook de vCenter-Portal starten vanuit het overzichts scherm van de Privécloud van uw AVS.

    ![VSphere-client starten-samen vatting](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Volgende stappen

* [VLAN'S/subnetten maken en beheren voor uw AVS-persoonlijke Clouds](create-vlan-subnet.md)
* [Machtigings model voor de Privécloud van de cloud van VMware vCenter](learn-private-cloud-permissions.md)