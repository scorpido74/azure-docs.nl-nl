---
title: VLAN'S/subnetten maken
description: "Azure VMware-oplossingen (AVS): beschrijft hoe u VLAN'S/subnetten maakt en beheert voor uw automatische AVS-Clouds en vervolgens firewall regels toepast."
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 249c48500dbcd75f62f856b3345b3a2c02502d1a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024769"
---
# <a name="create-and-manage-vlanssubnets-for-your-avs-private-clouds"></a>VLAN'S/subnetten maken en beheren voor uw AVS-persoonlijke Clouds

Open het tabblad VLAN'S/subnetten op de pagina netwerk om VLAN'S en subnetten voor uw AVS-Privécloud te maken en te beheren. Nadat u een VLAN/subnet hebt gemaakt, kunt u firewall regels Toep assen.

## <a name="create-a-vlansubnet"></a>Een VLAN/subnet maken

1. [Open de AVS-Portal](access-cloudsimple-portal.md) en selecteer **netwerk** in het menu aan de zijkant.
2. Selecteer **vlan's/subnetten**.
3. Klik op **VLAN/subnet maken**.

    ![VLAN/subnet-pagina](media/vlan-subnet-page.png)

4. Selecteer de automatische AVS-Cloud voor het nieuwe VLAN/subnet.
5. Voer een VLAN-ID in.
6. Voer de naam van het subnet in.
7. Als u route ring op het VLAN (subnet) wilt inschakelen, geeft u het CIDR-bereik van het subnet op. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises subnetten, Azure-subnetten of gateway-subnet.
8. Klik op **Submit**

    ![VLAN/subnet maken](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Er is een quotum van 30 VLAN'S per privécloud. Deze limieten kunnen worden verhoogd door [contact op](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te nemen met de ondersteuning.

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VLAN-gegevens gebruiken om een gedistribueerde poort groep in te stellen in vSphere

Als u een gedistribueerde poort groep in vSphere wilt maken, volgt u de instructies in het VMware-onderwerp ' een gedistribueerde poort groep toevoegen ' in de <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere-netwerk handleiding</a>. Wanneer u de gedistribueerde poort groep instelt, geeft u de VLAN-informatie op uit de AVS-configuratie.

![Gedistribueerde poort groep](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Een firewall tabel selecteren

Firewall tabellen en gekoppelde regels worden gedefinieerd op de pagina **netwerk > firewall tabellen** . Als u de firewall tabel wilt selecteren die moet worden toegepast op het VLAN/subnet voor een open bare AVS-Cloud, selecteert u het VLAN/subnet Klik op de pagina met **vlan's/subnetten** op **firewall tabel bijlage** . Zie [firewall tabellen](firewall.md) voor instructies over het instellen van Firewall tabellen en het definiëren van regels.

![Koppeling Firewall tabel](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Een subnet kan worden gekoppeld aan één firewall tabel. Een firewall tabel kan worden gekoppeld aan meerdere subnetten.

## <a name="edit-a-vlansubnet"></a>Een VLAN/subnet bewerken

Als u de instellingen voor een VLAN/subnet wilt bewerken, selecteert u deze op de pagina **vlan's/subnetten** en klikt u op het pictogram **bewerken** . Breng wijzigingen aan en klik op **Submet**.

## <a name="delete-a-vlansubnet"></a>Een VLAN/subnet verwijderen

Als u een VLAN/subnet wilt verwijderen, selecteert u het op de pagina **vlan's/subnetten** en klikt u op het pictogram **verwijderen** . Klik op **verwijderen** om te bevestigen.
