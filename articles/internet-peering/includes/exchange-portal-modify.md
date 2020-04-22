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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680948"
---
In deze sectie wordt beschreven hoe u de volgende wijzigingsbewerkingen voor Direct peering uitvoert.

### <a name="add-exchange-peering-connections"></a>Exchange-peeringverbindingen toevoegen

1. Selecteer de knop **+ Verbindingen toevoegen** en configureer een nieuwe peeringverbinding.
    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-exchange-modify-addconnection.png)
1. Vul het formulier **Exchange-peeringverbinding** in en selecteer **Opslaan**. Voor hulp bij het configureren van een peering-verbinding controleert u de stappen in de sectie Direct peering maken en inrichten.
    > [!div class="mx-imgBorder"]
    > ![Exchange-peeringverbindingsformulier](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-peeringverbindingen verwijderen

1. Selecteer een peeringverbinding die u wilt verwijderen en selecteer **vervolgens ...**  >  **Verbinding verwijderen**.
    > [!div class="mx-imgBorder"]
    > ![Knop Verbinding verwijderen](../media/setup-exchange-modify-deleteconnection.png)
1. Voer de bron-id in het vak **Verwijderen bevestigen** in en selecteer **Verwijderen**.
    > [!div class="mx-imgBorder"]
    > ![Bevestiging verwijderen](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4- of IPv6-sessie toevoegen op Actieve verbindingen

1. Selecteer een peeringverbinding die u wilt wijzigen en selecteer **vervolgens ...**  >  **Verbinding bewerken**.
    > [!div class="mx-imgBorder"]
    > ![Knop Verbinding bewerken](../media/setup-exchange-modify-editconnection.png)
1. Voeg **IPv4-adres-** of **IPv6-adresgegevens** toe en selecteer **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Wijzigingen in peering-verbindingen](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4- of IPv6-sessie verwijderen op Actieve verbindingen

Het verwijderen van een IPv4- of IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund op de portal. Neem voor meer informatie contact op met [Microsoft peering](mailto:peeringexperience@microsoft.com).