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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774560"
---
In deze sectie wordt beschreven hoe u de volgende wijzigings bewerkingen uitvoert voor directe peering:

### <a name="add-exchange-peering-connections"></a>Exchange-peering verbindingen toevoegen

1. Klik op de knop **+ verbindingen toevoegen** aan de bovenkant en configureer een nieuwe peering-verbinding.
    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-exchange-modify-addconnection.png)
1. Vul het verbindings formulier voor **Exchange-peering** in en klik op **Opslaan**. Voor hulp bij het configureren van een peering-verbinding raadpleegt u de stappen onder "een directe peering maken en inrichten" hierboven.
    > [!div class="mx-imgBorder"]
    > ![peering resource weergave](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-peering verbindingen verwijderen

1. Klik op een peering-verbinding die u wilt verwijderen en klik vervolgens op de knop **..** . > **verbinding verwijderen** .
    > [!div class="mx-imgBorder"]
    > ![peering-verbinding verwijderen](../media/setup-exchange-modify-deleteconnection.png)
1. Voer de resource-ID in het vak **verwijderen bevestigen** in, zoals wordt weer gegeven in de gemarkeerde vakken en klik op **verwijderen**.
    > [!div class="mx-imgBorder"]
    > ![peering Connect DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie toevoegen aan actieve verbindingen

1. Klik op een peering-verbinding die u wilt wijzigen en klik vervolgens op de knop verbinding **..** . > **bewerken** .
    > [!div class="mx-imgBorder"]
    > ![peering-verbinding bewerken](../media/setup-exchange-modify-editconnection.png)
1. Voeg informatie over **IPv4-adres** of **IPv6-adres** toe en klik op **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![peering-verbinding](../media/setup-exchange-modify-editconnectionsettings.png) wijzigen

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie op actieve verbindingen verwijderen

Het verwijderen van een IPv4/IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund in de portal. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com).