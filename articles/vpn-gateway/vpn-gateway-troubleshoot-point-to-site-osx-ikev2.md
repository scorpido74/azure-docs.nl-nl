---
title: 'Azure VPN-gateway: problemen met point-to-site-verbindingen oplossen: Mac OS X-clients'
description: Stappen om problemen op te lossen met P2S Mac OS X VPN-clientverbindingen
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425715"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Problemen met Point-to-Site VPN-verbindingen van Vpn-clients van Mac OS X oplossen

In dit artikel u problemen met de point-to-site-connectiviteit oplossen van Mac OS X met behulp van de native VPN-client en IKEv2. De VPN-client in Mac voor IKEv2 is erg basic en maakt niet veel maatwerk mogelijk. Er zijn slechts vier instellingen die moeten worden gecontroleerd:

* Serveradres
* Externe id
* Lokale id
* Verificatie-instellingen
* OS-versie (10.11 of hoger)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a>Verificatie op basis van certificaten oplossen
1. Controleer de VPN-clientinstellingen. Ga naar de **netwerkinstelling** door op Command + Shift te drukken en typ 'VPN' om de VPN-clientinstellingen te controleren. Klik in de lijst op de VPN-vermelding die moet worden onderzocht.

   ![IKEv2-certificaatgebaseerde verificatie](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Controleer of het **serveradres** de volledige FQDN is en de cloudapp.net bevat.
3. De **externe id** moet hetzelfde zijn als het serveradres (Gateway FQDN).
4. De **lokale id** moet hetzelfde zijn als het **onderwerp** van de clientcertificaat.
5. Klik op **Verificatie-instellingen** om de pagina Verificatie-instellingen te openen.

   ![Verificatie-instellingen](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Controleer of **certificaat** is geselecteerd in de vervolgkeuzelijst.
7. Klik **op** de knop Selecteren en controleer of het juiste certificaat is geselecteerd. Klik op **OK** om eventuele wijzigingen op te slaan.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Problemen met gebruikersnaam- en wachtwoordverificatie oplossen

1. Controleer de VPN-clientinstellingen. Ga naar de **netwerkinstelling** door op Command + Shift te drukken en typ 'VPN' om de VPN-clientinstellingen te controleren. Klik in de lijst op de VPN-vermelding die moet worden onderzocht.

   ![IKEv2 gebruikersnaam wachtwoord](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Controleer of het **serveradres** de volledige FQDN is en de cloudapp.net bevat.
3. De **externe id** moet hetzelfde zijn als het serveradres (Gateway FQDN).
4. De **lokale id** kan leeg zijn.
5. Klik op de knop **Verificatieinstelling** en controleer of 'Gebruikersnaam' is geselecteerd in de vervolgkeuzelijst.

   ![Verificatie-instellingen](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Controleer of de juiste referenties zijn ingevoerd.

## <a name="additional-steps"></a><a name="additional"></a>Aanvullende stappen

Als u de vorige stappen probeert en alles goed is geconfigureerd, downloadt u [Wireshark](https://www.wireshark.org/#download) en voert u een pakketopname uit.

1. Filter op *isakmp* en kijk naar de **IKE_SA** pakketten. Je moet in staat zijn om te kijken naar de SA voorstel details onder de **Payload: Security Association**. 
2. Controleer of de client en de server een gemeenschappelijke set hebben.

   ![Pakket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Als er geen serverrespons op de netwerksporen is, controleert u het IKEv2-protocol op de azure gatewayconfiguratiepagina op de website van de Azure-portal.

## <a name="next-steps"></a>Volgende stappen
Zie [Microsoft Support voor](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)extra hulp.
