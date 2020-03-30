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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775340"
---
In deze sectie wordt beschreven hoe u de volgende wijzigingsbewerkingen voor Direct peering uitvoert:

### <a name="add-direct-peering-connections"></a>Directe peeringverbindingen toevoegen
1. Klik op de knop **+ Verbindingen toevoegen** bovenaan en configureer een nieuwe peeringverbinding.
    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-direct-modify-addconnection.png)
1. Vul het formulier **Verbinding direct peering** in en klik op **Opslaan**. Voor hulp bij het configureren van een peering-verbinding controleert u de stappen onder de sectie Direct peering maken en inrichten.
    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Directe peeringverbindingen verwijderen

Het verwijderen van een verbinding wordt momenteel niet ondersteund op portal. Neem contact op met [Microsoft-peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Bandbreedte upgraden of downgraden op Actieve verbindingen
1. Klik op een peering verbinding die u wilt wijzigen en klik dan op de **...**  >  **Knop Verbinding bewerken.**
    > [!div class="mx-imgBorder"]
    > ![Bewerken van peeringverbinding](../media/setup-direct-modify-editconnection.png)
1. Wijzig de bandbreedte zoals hieronder wordt weergegeven en klik op **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Bandbreedte wijzigen van peering-verbinding](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie toevoegen op Actieve verbindingen.
1. Klik op een peering verbinding die u wilt wijzigen en klik dan op de **...**  >  **De knop Verbindingsknop bewerken** zoals hierboven weergegeven.
1. Voeg **sessie IPv4-voorvoegsel** of **Sessie IPv6-voorvoegselgegevens toe** en klik op **Opslaan**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie verwijderen op Actieve verbindingen.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
