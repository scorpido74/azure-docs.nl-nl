---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775340"
---
In deze sectie wordt beschreven hoe u de volgende wijzigings bewerkingen uitvoert voor directe peering:

### <a name="add-direct-peering-connections"></a>Directe peering-verbindingen toevoegen
1. Klik op de knop **+ verbindingen toevoegen** aan de bovenkant en configureer een nieuwe peering-verbinding.
    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-direct-modify-addconnection.png)
1. Vul het verbindings formulier **direct peering** in en klik op **Opslaan**. Voor hulp bij het configureren van een peering-verbinding raadpleegt u de stappen onder "een directe peering maken en inrichten" hierboven.
    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Directe peering-verbindingen verwijderen

Het verwijderen van een verbinding wordt momenteel niet ondersteund in de portal. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>De band breedte van actieve verbindingen upgraden of verlagen
1. Klik op een peering-verbinding die u wilt wijzigen en klik vervolgens op de knop verbinding **..** . > **bewerken** .
    > [!div class="mx-imgBorder"]
    > ![peering-verbinding bewerken](../media/setup-direct-modify-editconnection.png)
1. Wijzig de band breedte zoals hieronder wordt weer gegeven en klik vervolgens op **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![peering-verbinding band breedte wijzigen](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Voeg een IPv4/IPv6-sessie toe aan actieve verbindingen.
1. Klik op een peering-verbinding die u wilt wijzigen en klik vervolgens op de knop verbinding **..** . > **bewerken** , zoals hierboven wordt weer gegeven.
1. Voeg informatie over het **IPv4-voor voegsel** of de **IPv6-voor voegsel** voor de sessie toe en klik op **Opslaan**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie op actieve verbindingen verwijderen.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
