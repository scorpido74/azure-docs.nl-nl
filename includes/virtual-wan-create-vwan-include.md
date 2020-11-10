---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 62d466e81309765540bcbd52714733b97d241ebc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93354025"
---
Open een browser, ga naar de Azure-portal en meld u aan met uw Azure-account.

1. Ga naar de pagina Virtual WAN. Klik in de portal op **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer **Enter**.
1. Selecteer **Virtual WAN** uit de resultaten. Selecteer op de pagina Virtual WAN **Maken** om de pagina WAN maken te openen.
1. Vul op de pagina **Wan maken** , op het tabblad **Basisprincipes** , de volgende velden in:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Schermopname toont het deelvenster 'WAN maken' waarbij het tabblad Basisprincipes geselecteerd is.":::

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - maak een nieuwe resourcegroep of gebruik een bestaande.
   * **Locatie van de resourcegroep** - kies een resourcelocatie uit de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt te kunnen beheren en vinden.
   * **Naam** - typ de naam die u voor uw WAN hebt gekozen.
   * **Type** : Basic of Standard. selecteer **Standaard**. Als u Basic-VWAN selecteert, moet u onthouden dat Basic-VWAN's alleen Basic-hubs kunnen bevatten. Dit beperkt uw verbindingstype tot site-naar-site.
1. Nadat u klaar bent met het invullen van de velden, selecteert u **+Maken**.
1. Wanneer de validatie is geslaagd, selecteert u **Maken** om het virtuele WAN te maken.
