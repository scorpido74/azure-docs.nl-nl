---
title: VLAN'S/subnetten maken
description: "Azure VMware-oplossing per CloudSimple: beschrijft hoe u VLAN'S/subnetten maakt en beheert voor uw privé-Clouds en vervolgens firewall regels kunt Toep assen."
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e6f781926e2a30ecf6d34274b20991b81f0bb96d
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972910"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>VLAN'S/subnetten voor uw privé-clouds maken en beheren

Open het tabblad VLAN'S/subnetten op de pagina netwerk om VLAN'S en subnetten voor uw Privécloud te maken en te beheren. Nadat u een VLAN/subnet hebt gemaakt, kunt u firewall regels Toep assen.

## <a name="create-a-vlansubnet"></a>Een VLAN/subnet maken

1. [Open de CloudSimple-Portal](monitor-activity.md) en selecteer **netwerk** in het menu aan de zijkant.
2. Selecteer **vlan's/subnetten**.
3. Klik op **VLAN/subnet maken**.

    ![VLAN/subnet-pagina](media/vlan-subnet-page.png)

4. Selecteer de Privécloud voor het nieuwe VLAN/subnet.
5. Voer een VLAN-ID in.
6. Voer de naam van het subnet in.
7. Als u route ring op het VLAN (subnet) wilt inschakelen, geeft u het CIDR-bereik van het subnet op. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises subnetten, Azure-subnetten of gateway-subnet.
8. Klik op **Indienen**.

    ![VLAN/subnet maken](media/create-new-vlan-subnet-details.png)

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VLAN-gegevens gebruiken om een gedistribueerde poort groep in te stellen in vSphere

Als u een gedistribueerde poort groep in vSphere wilt maken, volgt u de instructies in het VMware-onderwerp ' een gedistribueerde poort groep toevoegen ' in de <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere-netwerk handleiding</a>. Wanneer u de gedistribueerde poort groep instelt, geeft u de VLAN-informatie op uit de CloudSimple-configuratie.

![Gedistribueerde poort groep](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Een firewall tabel selecteren

Firewall tabellen en gekoppelde regels worden gedefinieerd op de pagina **netwerk > firewall tabellen** . Als u de firewall tabel wilt selecteren die moet worden toegepast op het VLAN/subnet voor een Privécloud, selecteert u het VLAN/subnet Klik op **firewall tabel bijlage** op de pagina **vlan's/subnetten** . Zie [firewall tabellen](firewall.md) voor instructies over het instellen van Firewall tabellen en het definiëren van regels.

![Koppeling Firewall tabel](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Een subnet kan worden gekoppeld aan één firewall tabel. Een firewall tabel kan worden gekoppeld aan meerdere subnetten.

## <a name="edit-a-vlansubnet"></a>Een VLAN/subnet bewerken

Als u de instellingen voor een VLAN/subnet wilt bewerken, selecteert u deze op de pagina **vlan's/subnetten** en klikt u op het pictogram **bewerken** . Breng wijzigingen aan en klik op **Submet**.

## <a name="delete-a-vlansubnet"></a>Een VLAN/subnet verwijderen

Als u een VLAN/subnet wilt verwijderen, selecteert u het op de pagina **vlan's/subnetten** en klikt u op het pictogram **verwijderen** . Klik op **verwijderen** om te bevestigen.
