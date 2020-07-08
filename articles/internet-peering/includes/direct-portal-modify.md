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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81681036"
---
In deze sectie wordt beschreven hoe u de volgende wijzigings bewerkingen voor directe peering uitvoert.

### <a name="add-direct-peering-connections"></a>Directe peering-verbindingen toevoegen
1. Selecteer de knop **+ verbindingen toevoegen** en configureer een nieuwe peering-verbinding.
    > [!div class="mx-imgBorder"]
    > ![Bron weergave voor peering](../media/setup-direct-modify-addconnection.png)

1. Vul het **verbindings formulier direct peering** in en selecteer **Opslaan**. Voor hulp bij het configureren van een peering-verbinding raadpleegt u de stappen in de sectie ' een directe peering maken en inrichten '.
    > [!div class="mx-imgBorder"]
    > ![Verbindings formulier voor directe peering](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Directe peering-verbindingen verwijderen

Het verwijderen van een verbinding wordt momenteel niet ondersteund in de Azure Portal. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com)voor meer informatie.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>De band breedte van actieve verbindingen upgraden of verlagen
1. Selecteer een peering-verbinding die u wilt wijzigen en selecteer vervolgens **...**  >  **Verbinding bewerken**.
    > [!div class="mx-imgBorder"]
    > ![Verbinding bewerken](../media/setup-direct-modify-editconnection.png)

1. Wijzig de band breedte door de schuif regelaar te verplaatsen en vervolgens **Opslaan**te selecteren.
    > [!div class="mx-imgBorder"]
    > ![Band breedte wijzigen](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>IPv4-of IPv6-sessie gegevens toevoegen aan actieve verbindingen
1. Selecteer een peering-verbinding die u wilt wijzigen en selecteer vervolgens **...**  >  **Verbinding bewerken** , zoals wordt weer gegeven in stap 1.
1. Voer informatie over het **IPv4-voor voegsel** of het **IPv6-voor voegsel** van de sessie in en selecteer **Opslaan**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>IPv4-of IPv6-sessie-informatie voor actieve verbindingen verwijderen
Het verwijderen van de IPv4-voor **voegsel voor de sessie** of de **IPv6-voor voegsel** wordt momenteel niet ondersteund in de portal. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com)voor meer informatie.
