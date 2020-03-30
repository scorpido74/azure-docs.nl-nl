---
title: 'VPN-gateway: problemen met VPN-client - Azure AD-verificatie'
description: Problemen met VPN Gateway P2S Azure AD-verificatieclients oplossen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151971"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Problemen met een AZURE AD-verificatieVPN-client oplossen

Met dit artikel u problemen oplossen met een VPN-client om verbinding te maken met een virtueel netwerk met behulp van Point-to-Site VPN en Azure Active Directory-verificatie.

## <a name="view-status-log"></a><a name="status"></a>Statuslogboek weergeven

Het statuslogboek weergeven voor foutberichten.

![logboeken](./media/troubleshoot-ad-vpn-client/1.png)

1. Klik op het pijlenpictogram rechtsonder in het clientvenster om de **statuslogboeken**weer te geven.
2. Controleer de logboeken op fouten die op het probleem kunnen wijzen.
3. Foutberichten worden rood weergegeven.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Inloggegevens wissen

De aanmeldingsgegevens wissen.

![aanmelden](./media/troubleshoot-ad-vpn-client/2.png)

1. Selecteer de ... naast het profiel dat u wilt oplossen. Selecteer **> opgeslagen account configureren > wissen**.
2. Selecteer **Opslaan**.
3. Probeer verbinding te maken.
4. Als de verbinding nog steeds mislukt, gaat u verder naar de volgende sectie.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Diagnose uitvoeren

Voer diagnostische gegevens uit op de VPN-client.

![diagnostische gegevens](./media/troubleshoot-ad-vpn-client/3.png)

1. Klik op de **...** naast het profiel waarop u diagnoses wilt uitvoeren. Selecteer **Diagnose -> Stel diagnose uit.**
2. De klant voert een reeks tests uit en geeft het resultaat van de test weer

   * Internettoegang : controleert of de client een internetverbinding heeft
   * Clientreferenties : controleer of het eindpunt azure Active Directory-verificatie bereikbaar is
   * Server Resolvable – Neemt contact op met de DNS-server om het IP-adres van de geconfigureerde VPN-server op te lossen
   * Server bereikbaar – Controleert of de VPN-server reageert of niet
3. Als een van de tests mislukt, neemt u contact op met uw netwerkbeheerder om het probleem op te lossen.
4. In de volgende sectie ziet u hoe u de logboeken verzamelen, indien nodig.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Logboekbestanden voor client verzamelen

Verzamel de VPN-clientlogbestanden. De logbestanden kunnen via een methode naar keuze naar ondersteuning/beheerder worden gestuurd. Bijvoorbeeld e-mail.

1. Klik op de "..." naast het profiel waarop u diagnoses wilt uitvoeren. Selecteer **Diagnose -> Logboekenoverzicht weergeven**.

   ![logboeken weergeven](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows Verkenner wordt geopend voor de map die de logboekbestanden bevat.

   ![weergavebestand](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Een Azure Active Directory-tenant maken voor P2S Open VPN-verbindingen die Azure AD-verificatie gebruiken voor](openvpn-azure-ad-tenant.md)meer informatie.