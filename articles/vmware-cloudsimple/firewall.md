---
title: Azure VMware-oplossing door CloudSimple - Firewalltabellen en -regels instellen
description: Beschrijft hoe u Private Cloud-firewalltabellen en -regels instelt om het verkeer op subnetten en VLAN's te beperken.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244665"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Firewalltabellen en -regels instellen voor privéclouds

Met firewalltabellen en de bijbehorende regels u beperkingen opgeven voor verkeer dat van toepassing is op bepaalde subnetten en VLAN's.

* Een subnet kan worden gekoppeld aan één firewalltabel.
* Een firewalltabel kan worden gekoppeld aan meerdere subnetten.

## <a name="add-a-new-firewall-table"></a>Een nieuwe firewalltabel toevoegen

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md) en selecteer **Netwerk** in het zijmenu.
2. Selecteer **Firewalltabellen**.
3. Selecteer **Firewalltabel maken**.

    ![VLAN/subnetpagina](media/firewall-tables-page.png)

4. Voer een naam in voor de tabel.
5. Er wordt een standaardregel voor de tabel weergegeven. Klik **op Nieuwe regel maken** om een extra regel te maken. Zie de volgende procedure voor meer informatie.
6. Klik **op Gereed** om de firewalltabel op te slaan.

> [!IMPORTANT]
> U maximaal twee Firewall-tabellen maken per Private Cloud.

## <a name="firewall-rules"></a>Firewall-regels

Firewallregels bepalen hoe de firewall specifieke typen verkeer behandelt. Op het tabblad **Regels** voor een geselecteerde firewalltabel worden alle bijbehorende regels weergegeven.

![Tabel Firewallregels](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Een firewallregel maken

1. Geef de instellingen weer om een firewallregel op een van de volgende manieren te maken:
    * Klik **op Regel toevoegen** bij het maken van een firewalltabel.
    * Selecteer een bepaalde firewalltabel op de pagina **Netwerk > Firewalltabellen** en klik op **Nieuwe firewallregel maken**.
2. Stel de regel als volgt in:
    * **Naam**. Geef de regel een naam.
    * **Prioriteit**. Wijs een prioriteit toe aan de regel. Regels met lagere getallen worden eerst uitgevoerd.
    * **Verkeerstype**. Selecteer of de regel voor Private Cloud, Internet of VPN-verkeer (stateless) of voor een openbaar IP-adres (stateful) is.
    * **Protocol**. Selecteer het protocol dat onder de regel valt (TCP, UDP of een protocol).
    * **Richting**. Selecteer of de regel voor binnenkomend of uitgaand verkeer is. U moet afzonderlijke regels definiëren voor binnenkomend en uitgaand verkeer.
    * **Actie**. Selecteer de actie die u wilt uitvoeren als de regel overeenkomt met (toestaan of weigeren).
    * **Bron**. Geef de bronnen op die onder de regel vallen (CIDR-blok, interne of een bron).
    * **Bronpoortbereik**. Geef het bereik van de poorten op waarop de regel van toepassing is.
    * **Richting**. Selecteer binnenkomend of uitgaand.
    * **Bestemming**. Geef de bestemmingen op die onder de regel vallen (CIDR-blok, interne of een bron).
    * **Bronpoortbereik**. Geef het bereik van de poorten op waarop de regel van toepassing is.

    ![Regels voor het toevoegen van firewalltabelen](media/firewall-rule-create.png)

3. Klik **op Gereed** om de regel op te slaan en deze toe te voegen aan de lijst met regels voor de firewalltabel.

> [!IMPORTANT]
> Elke Firewall-tabel kan maximaal 10 binnenkomende regels en 20 uitgaande regels hebben. Deze limieten kunnen worden verhoogd door [contact op te nemen met de ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>VLAN's/subnetten koppelen

Nadat u een firewalltabel hebt gedefinieerd, u de subnetten opgeven waarop de regels in de tabel van toepassing zijn.

1. Selecteer op de pagina > **Netwerkfirewalltabellen** een firewalltabel. **Network**
2. Open het tabblad **Gekoppelde VLAN's/Subnet.**
3. Klik **op Koppelen aan een VLAN/Subnet**.
4. Selecteer de Private Cloud en VLAN. De bijbehorende subnetnaam en CIDR-blok worden weergegeven.
5. Klik **op Verzenden**.
