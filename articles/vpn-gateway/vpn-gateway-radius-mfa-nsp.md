---
title: NPS integreren met VPN Gateway RADIUS-verificatie voor MFA
description: Beschrijft de integratie van Azure-gateway RADIUS-verificatie met NPS-server voor multifactorverificatie.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 941b6ac86941824351f83592998e8735e3eb8ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780365"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Azure VPN-gateway RADIUS-verificatie integreren met NPS-server voor multifactorverificatie 

In het artikel wordt beschreven hoe u Network Policy Server (NPS) integreren met Azure VPN-gateway RADIUS-verificatie om Multi-Factor Authentication (MFA) te leveren voor point-to-site VPN-verbindingen. 

## <a name="prerequisite"></a>Vereiste

Als u MFA wilt inschakelen, moeten de gebruikers zich bevinden in Azure Active Directory (Azure AD), dat moet worden gesynchroniseerd vanuit de on-premises of cloudomgeving. Ook moet de gebruiker het automatische inschrijvingsproces voor MFA al hebben voltooid.  Zie Mijn account instellen voor verificatie in twee stappen voor meer [informatie.](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Gedetailleerde stappen

### <a name="step-1-create-a-virtual-network-gateway"></a>Stap 1: Een virtuele netwerkgateway maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Subnetsubnet subnet**en selecteer vervolgens **subnet Gateway** om een subnet te maken. 

    ![De afbeelding over het toevoegen van gatewaysubnet](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Maak een virtuele netwerkgateway door de volgende instellingen op te geven:

    - **Gatewaytype**: selecteer **VPN**.
    - **VPN-type**: Selecteer **Route-gebaseerd**.
    - **SKU:** Selecteer een SKU-type op basis van uw vereisten.
    - **Virtueel netwerk:** selecteer het virtuele netwerk waarin u het gateway-subnet hebt gemaakt.

        ![De afbeelding over instellingen voor virtuele netwerkgateway](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Stap 2 De NPS configureren voor Azure MFA

1. Installeer op de NPS-server [de NPS-extensie voor Azure MFA.](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)
2. Open de NPS-console, klik met de rechtermuisknop op **RADIUS-clients**en selecteer **Nieuw**. Maak de RADIUS-client door de volgende instellingen op te geven:

    - **Vriendelijke naam:** Typ een naam.
    - **Adres (IP of DNS):** Typ het gatewaysubnet dat u hebt gemaakt in stap 1.
    - **Gedeeld geheim:** typ een geheime sleutel en onthoud deze voor later gebruik.

      ![De afbeelding over RADIUS-clientinstellingen](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Stel op het tabblad **Geavanceerd** de naam van de leverancier in **op RADIUS-standaard** en controleer of het selectievakje **Extra opties** niet is ingeschakeld.

    ![De afbeelding over geavanceerde instellingen van RADIUS-client](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Ga naar > **Beleidsbeleid,** dubbelklik op **Verbindingen met microsoft-routering en extern toegang-serverbeleid,** selecteer **Toegang verlenen**en klik op **OK**. **Policies**

### <a name="step-3-configure-the-virtual-network-gateway"></a>Stap 3 De virtuele netwerkgateway configureren

1. Meld u aan bij [Azure portal](https://portal.azure.com).
2. Open de virtuele netwerkgateway die u hebt gemaakt. Zorg ervoor dat het gatewaytype is ingesteld op **VPN** en dat het **VPN-type op route is gebaseerd.**
3. Klik **op Punt naar siteconfiguratie** > Nu**configureren**en geef vervolgens de volgende instellingen op:

    - **Adresgroep:** Typ het gatewaysubnet dat u in stap 1 hebt gemaakt.
    - **Verificatietype:** **RADIUS-verificatie**selecteren .
    - **Ip-adres server:** typ het IP-adres van de NPS-server.

      ![De afbeelding over punt-naar-site-instellingen](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-verificatie met meerdere factoren](../active-directory/authentication/multi-factor-authentication.md)
- [Uw bestaande NPS-infrastructuur integreren met Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
