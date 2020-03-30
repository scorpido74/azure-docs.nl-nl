---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471507"
---
Conditional Access zorgt voor fijnkorrelige toegangscontrole per toepassing. Als u Voorwaardelijke toegang wilt gebruiken, moet azure AD Premium 1 of meer licenties worden toegepast op de gebruikers waarop de regels voor voorwaardelijke toegang zijn onderworpen.

1. Navigeer naar de **Enterprise-toepassingen - Alle toepassingenpagina** en klik op **Azure VPN**.

   - Klik **op Voorwaardelijke toegang**.
   - Klik **op Nieuw beleid** om het deelvenster **Nieuw** te openen.
2. Navigeer in het deelvenster **Nieuw** naar **Toewijzingen -> gebruikers en groepen**. Ga op het tabblad **Gebruikers en groepen >** **opnemen:**

   - Klik **op Gebruikers en groepen selecteren**.
   - **Gebruikers en groepen controleren**.
   - Klik **op Selecteren** om een groep of een groep gebruikers te selecteren die door MFA worden beïnvloed.
   - Klik op **Gereed**.

   ![Toewijzingen](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Navigeer in het deelvenster **Nieuw** naar het deelvenster **Access-besturingselementen -> verlenen:**

   - Klik **op Toegang verlenen**.
   - Klik **op Meervoudige verificatie vereisen**.
   - Klik **op Alle geselecteerde besturingselementen vereisen**.
   - Klik **op Selecteren**.
   
   ![Subsidietoegang - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Ga als bedoeld in de sectie **Beleid inschakelen:**

   - Selecteer **Op**.
   - Klik **op Maken**.

   ![Beleid inschakelen](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)