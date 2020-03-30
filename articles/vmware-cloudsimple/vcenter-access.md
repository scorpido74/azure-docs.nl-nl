---
title: Azure VMware-oplossing door CloudSimple - Toegang vSphere-client
description: Beschrijft hoe u toegang krijgt tot vCenter van uw Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022661"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Toegang tot uw Private Cloud vCenter-portal

U uw Private Cloud vCenter-portal starten via Azure portal of CloudSimple portal.  vCenter portal u VMware infrastructuur te beheren op uw Private Cloud.

## <a name="before-you-begin"></a>Voordat u begint

Netwerkverbinding moet tot stand worden gebracht en DNS-naamomzetting moet zijn ingeschakeld voor toegang tot vCenter-portal.  U een netwerkverbinding met uw private cloud tot stand brengen met een van de onderstaande opties.

* [Maak verbinding van on-premises naar CloudSimple via ExpressRoute](on-premises-connection.md)
* [Een VPN-verbinding configureren met uw CloudSimple Private Cloud](set-up-vpn.md)

Zie [DNS configureren voor naamomzetting voor Private Cloud vCenter-toegang vanaf on-premises werkstations](on-premises-dns-setup.md) instellen als u DNS-naamomzetting van uw Private Cloud VMware-infrastructuurcomponenten wilt instellen.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="access-vcenter-from-azure-portal"></a>VCenter openen vanuit Azure-portal

U vCenter-portal van uw Private Cloud starten vanuit Azure-portal.

1. Selecteer **Alle services**.

2. Zoek naar **CloudSimple Services**.

3. Selecteer de CloudSimple-service van uw Private Cloud waarmee u verbinding wilt maken.

4. Klik **op** de pagina Overzicht op **VMware Private Clouds weergeven**

    ![Overzicht van cloudSimple-service](media/cloudsimple-service-overview.png)

5. Selecteer de private cloud in de lijst met privéwolken en klik op **VSphere-client starten**.

    ![VSphere-client starten](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Toegang tot vCenter vanuit CloudSimple-portal

U vCenter portal van uw Private Cloud starten vanuit CloudSimple portal.

1. Toegang tot uw [CloudSimple portal.](access-cloudsimple-portal.md)

2. Selecteer **in** de bronnen de private cloud, die u wilt openen en klik op **VSphere-client starten.**

    ![VSphere-client starten - Resources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. U de vCenter-portal ook starten vanaf het overzichtsscherm van uw Private Cloud.

    ![VSphere-client starten - Samenvatting](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Volgende stappen

* [VLAN's/subnetten maken en beheren voor uw private clouds](create-vlan-subnet.md)
* [CloudSimple Private Cloud-machtigingsmodel van VMware vCenter](learn-private-cloud-permissions.md)