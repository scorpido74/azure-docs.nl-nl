---
title: VLAN's/subnetten maken - Azure VMware-oplossing door CloudSimple
description: Azure VMware Solutions by CloudSimple - Beschrijft hoe u VLAN's/subnetten voor uw Private Clouds maken en beheren en past vervolgens firewallregels toe.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565992"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>VLAN's/subnetten maken en beheren voor uw private clouds

Open het tabblad VLAN's/subnetten op de pagina Netwerk om VLAN's/subnetten voor uw privéclouds te maken en te beheren. Nadat u een VLAN/subnet hebt gemaakt, u firewallregels toepassen.

## <a name="create-a-vlansubnet"></a>Een VLAN/subnet maken

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md) en selecteer **Netwerk** in het zijmenu.
2. Selecteer **VLAN's/subnetten**.
3. Klik **op VLAN/Subnet maken**.

    ![VLAN/subnetpagina](media/vlan-subnet-page.png)

4. Selecteer de Private Cloud voor het nieuwe VLAN/subnet.
5. Voer een VLAN-id in.
6. Voer de subnetnaam in.
7. Als u routering op het VLAN-subnet wilt inschakelen, geeft u het subnet CIDR-bereik op. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises subnetten, Azure-subnetten of gatewaysubnet.
8. Klik **op Verzenden**.

    ![VLAN/subnet maken](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Er is een quotum van 30 VLAN's per private cloud. Deze limieten kunnen worden verhoogd door [contact op te nemen met de ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VLAN-informatie gebruiken om een gedistribueerde poortgroep in vSphere in te stellen

Als u een gedistribueerde poortgroep in vSphere wilt maken, volgt u de instructies in het VMware-onderwerp 'Een gedistribueerde poortgroep toevoegen' in de <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere-netwerkgids.</a> Geef bij het instellen van de gedistribueerde poortgroep de VLAN-informatie op uit de CloudSimple-configuratie.

![Gedistribueerde poortgroep](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Een firewalltabel selecteren

Firewalltabellen en bijbehorende regels worden gedefinieerd op de pagina **Network > Firewall-tabellen.** Als u de firewalltabel wilt selecteren die u wilt toepassen op het VLAN/subnet voor een private cloud, selecteert u de VLAN/subnet klik op **Firewall-tabelbijlage** op de pagina **VLAN/Subnets.** Zie [Firewalltabellen](firewall.md) voor instructies voor het instellen van firewalltabellen en het definiëren van regels.

![Koppeling firewalltabel](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Een subnet kan worden gekoppeld aan één firewalltabel. Een firewalltabel kan worden gekoppeld aan meerdere subnetten.

## <a name="edit-a-vlansubnet"></a>Een VLAN/subnet bewerken

Als u de instellingen voor een VLAN/Subnet wilt bewerken, selecteert u deze op de pagina **VLN's/Subnetten** en klikt u op het pictogram **Bewerken.** Breng wijzigingen aan en klik op **Submet**.

## <a name="delete-a-vlansubnet"></a>Een VLAN/subnet verwijderen

Als u een VLAN/Subnet wilt verwijderen, selecteert u deze op de pagina **VLAn's/Subnetten** en klikt u op het pictogram **Verwijderen.** Klik **op Verwijderen** om te bevestigen.
