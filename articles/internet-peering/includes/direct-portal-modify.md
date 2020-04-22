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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681036"
---
In deze sectie wordt beschreven hoe u de volgende wijzigingsbewerkingen voor Direct peering uitvoert.

### <a name="add-direct-peering-connections"></a>Directe peeringverbindingen toevoegen
1. Selecteer de knop **+ Verbindingen toevoegen** en configureer een nieuwe peeringverbinding.
    > [!div class="mx-imgBorder"]
    > ![Weergave van peeringbronnen](../media/setup-direct-modify-addconnection.png)

1. Vul het formulier **Direct peering-verbinding** in en selecteer **Opslaan**. Voor hulp bij het configureren van een peering-verbinding controleert u de stappen in de sectie Direct peering maken en inrichten.
    > [!div class="mx-imgBorder"]
    > ![Formulier directe peeringverbinding](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Directe peeringverbindingen verwijderen

Het verwijderen van een verbinding wordt momenteel niet ondersteund in de Azure-portal. Neem voor meer informatie contact op met [Microsoft peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Bandbreedte upgraden of downgraden op Actieve verbindingen
1. Selecteer een peeringverbinding die u wilt wijzigen en selecteer **vervolgens ...**  >  **Verbinding bewerken**.
    > [!div class="mx-imgBorder"]
    > ![Verbinding bewerken](../media/setup-direct-modify-editconnection.png)

1. Wijzig de bandbreedte door de schuifregelaar te verplaatsen en selecteer **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Bandbreedte wijzigen](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>IPv4- of IPv6-sessiegegevens toevoegen aan actieve verbindingen
1. Selecteer een peeringverbinding die u wilt wijzigen en selecteer **vervolgens ...**  >  **Verbindingsverbinding bewerken** zoals weergegeven in stap 1.
1. Voer **voorvoegsel van Sessie IPv4** of **IPv6-voorvoegsel van sessie in** en selecteer **Opslaan**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>IPv4- of IPv6-sessiegegevens verwijderen over actieve verbindingen
Het verwijderen van **sessie IPv4-voorvoegsel** of **Sessie IPv6-voorvoegselinformatie** wordt momenteel niet ondersteund in de portal. Neem voor meer informatie contact op met [Microsoft peering](mailto:peeringexperience@microsoft.com).
