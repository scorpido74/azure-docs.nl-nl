---
title: 'Azure VPN Gateway: problemen met Point-to-site-verbindingen oplossen: Mac OS X-clients'
description: Stappen voor het oplossen van P2S Mac OS X-VPN-clientverbindingen
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f76fd7bce539ebcf79216aabb5bf868b2d18107a
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780178"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Problemen met punt-naar-Site VPN-verbindingen van Mac OS X-VPN-clients oplossen

Dit artikel helpt bij het oplossen van problemen met punt-naar-Site de netwerkverbinding van Mac OS X met behulp van de systeemeigen VPN-client en IKEv2. De VPN-client in Mac voor IKEv2 is zeer eenvoudig en is niet toegestaan voor veel aanpassing. Er zijn slechts vier instellingen die moeten worden gecontroleerd:

* Serveradres
* Externe ID
* Lokale ID
* Verificatie-instellingen
* Versie van het besturingssysteem (10.11 of hoger)


## <a name="VPNClient"></a> Verificatie op basis van certificaten oplossen
1. Controleer de instellingen van de VPN-client. Ga naar de **netwerkinstelling** door te drukken opdracht + Shift en typ vervolgens 'VPN' om te controleren of de instellingen voor de VPN-client. In de lijst, klikt u op de VPN-vermelding die moet worden onderzocht.

   ![IKEv2-certificaat gebaseerde verificatie](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Controleer de **serveradres** is de volledige FQDN en de cloudapp.net bevat.
3. De **externe ID** moet hetzelfde zijn als het adres van de Server (FQDN-Gateway).
4. De **lokale ID** moet gelijk zijn aan de **onderwerp** van het clientcertificaat.
5. Klik op **verificatie-instellingen** om de pagina verificatie-instellingen te openen.

   ![verificatie-instellingen](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Controleer **certificaat** is geselecteerd in de vervolgkeuzelijst.
7. Klik op de **Selecteer** knop en controleer of het juiste certificaat is geselecteerd. Klik op **OK** eventuele wijzigingen op te slaan.

## <a name="ikev2"></a>Verificatie via gebruikersnaam en wachtwoord oplossen

1. Controleer de instellingen van de VPN-client. Ga naar de **netwerkinstelling** door te drukken opdracht + Shift en typ vervolgens 'VPN' om te controleren of de instellingen voor de VPN-client. In de lijst, klikt u op de VPN-vermelding die moet worden onderzocht.

   ![Wachtwoord voor IKEv2-gebruikersnaam](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Controleer de **serveradres** is de volledige FQDN en de cloudapp.net bevat.
3. De **externe ID** moet hetzelfde zijn als het adres van de Server (FQDN-Gateway).
4. De **lokale ID** mag leeg zijn.
5. Klik op de **Authentication-instelling** knop en controleer of dat 'Username' is geselecteerd in de vervolgkeuzelijst.

   ![verificatie-instellingen](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Controleer of de juiste referenties zijn ingevoerd.

## <a name="additional"></a>Extra stappen

Als u de vorige stappen en alles juist is geconfigureerd, downloadt u [Wireshark](https://www.wireshark.org/#download) en een pakketopname uit te voeren.

1. Filteren op *isakmp* en bekijk de **IKE_SA** pakketten. U zou het mogelijk om te kijken naar de details van het voorstel SA onder de **nettolading: Security Association**. 
2. Controleer of de client en de server een gemeenschappelijke set hebben.

   ![pakket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Als er geen serverreactie op de Netwerktraceringen is, controleert u of dat u IKEv2-protocol op de pagina configuratie van de Azure-Gateway op de website van Azure Portal hebt ingeschakeld.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie, [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
