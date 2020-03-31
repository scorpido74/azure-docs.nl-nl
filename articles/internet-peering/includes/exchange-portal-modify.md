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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774560"
---
In deze sectie wordt beschreven hoe u de volgende wijzigingsbewerkingen voor Direct peering uitvoert:

### <a name="add-exchange-peering-connections"></a>Exchange-peeringverbindingen toevoegen

1. Klik op de knop **+ Verbindingen toevoegen** bovenaan en configureer een nieuwe peeringverbinding.
    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-exchange-modify-addconnection.png)
1. Vul het formulier **Exchange-peeringconnection** in en klik op **Opslaan**. Voor hulp bij het configureren van een peering-verbinding controleert u de stappen onder de sectie Direct peering maken en inrichten.
    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-peeringverbindingen verwijderen

1. Klik op een peering verbinding die u wilt verwijderen en klik vervolgens op de **...**  >  **Verbindingsknop verwijderen.**
    > [!div class="mx-imgBorder"]
    > ![Peering-verbinding verwijderen](../media/setup-exchange-modify-deleteconnection.png)
1. Voer de bron-id in het vak **Verwijderen bevestigen** in, zoals weergegeven in de gemarkeerde vakken, en klik op **Verwijderen**.
    > [!div class="mx-imgBorder"]
    > ![Verwijderen van peeringverbindingBevestigen](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie toevoegen aan Actieve verbindingen

1. Klik op een peering verbinding die u wilt wijzigen en klik dan op de **...**  >  **Knop Verbinding bewerken.**
    > [!div class="mx-imgBorder"]
    > ![Bewerken van peeringverbinding](../media/setup-exchange-modify-editconnection.png)
1. Voeg **IPv4-adres-** of **IPv6-adresgegevens** toe en klik op **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Peering-verbinding wijzigen](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie verwijderen op Actieve verbindingen

Het verwijderen van een IPv4/IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund op portal. Neem contact op met [Microsoft-peering](mailto:peeringexperience@microsoft.com).