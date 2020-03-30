---
title: Een vertrouwde beveiligingspartner van Azure Firewall Manager implementeren
description: Meer informatie over het implementeren van een vertrouwde beveiliging van Azure Firewall Manager via de Azure-portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931303"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Een vertrouwde beveiligingspartner implementeren (preview)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Met vertrouwde beveiligingspartners* in Azure Firewall Manager u uw vertrouwde, best-in-breed third-party security-as-a-service (SECaaS)-aanbiedingen gebruiken om internettoegang voor uw gebruikers te beschermen.

Zie [Wat zijn vertrouwde beveiligingspartners (preview)voor](trusted-security-partners.md)meer informatie over ondersteunde scenario's en richtlijnen voor aanbevolen procedures?

De ondersteunde beveiligingspartners zijn **ZScaler** en **iboss** voor deze preview. Ondersteunde regio's zijn WestCentralUS, NorthCentralUS, WestUS, WestUS2 en EastUS.

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> Azure Firewall Manager Preview moet expliciet `Register-AzProviderFeature` zijn ingeschakeld met de opdracht PowerShell.

Voer in een PowerShell-opdrachtprompt de volgende opdrachten uit:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Het duurt maximaal 30 minuten voordat de functieregistratie is voltooid. Voer de volgende opdracht uit om uw registratiestatus te controleren:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Een externe beveiligingsprovider implementeren in een nieuwe hub

1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Typ in **Zoeken** **Firewall Manager** en selecteer deze onder **Services**.
3. Navigeer naar **Aan de slag**. Selecteer **Een beveiligde virtuele hub maken**. 
4. Voer u een abonnements- en brongroep in, selecteer een ondersteund gebied en voeg uw hub en virtuele WAN-informatie toe. 
5. **Vpn-gateway implementeren** is standaard ingeschakeld. Een VPN-gateway is vereist om een vertrouwde beveiligingspartner in de hub te implementeren. 
6. Volgende **selecteren: Azure Firewall**
   > [!NOTE]
   > Vertrouwde beveiligingspartners maken verbinding met uw hub via VPN Gateway-tunnels. Als u de VPN-gateway verwijdert, gaan de verbindingen met uw vertrouwde beveiligingspartners verloren.
7. Als u Azure Firewall wilt implementeren om privéverkeer te filteren samen met externe serviceproviderom internetverkeer te filteren, selecteert u een beleid voor Azure Firewall. Bekijk de [ondersteunde scenario's](trusted-security-partners.md#key-scenarios).
8. Als u alleen een beveiligingsprovider van derden in de hub wilt implementeren, selecteert u **Azure Firewall: ingeschakeld/uitgeschakeld** om deze in te stellen op **Uitgeschakeld.** 
9. Selecteer **Volgende: vertrouwde beveiligingspartners**.
10. Selecteer **Vertrouwde beveiligingspartner** om deze in te stellen **op Ingeschakeld**. Selecteer een partner. 
11. Selecteer **Volgende**. 
12. Bekijk de inhoud en selecteer **Vervolgens Maken**.

De VPN-gateway-implementatie kan meer dan 30 minuten duren.

Als u wilt controleren of de hub is gemaakt, navigeert u naar Azure Firewall Manager->Secured Hubs. Selecteer de pagina >hub->om de partnernaam en de status als **beveiligingsverbinding in behandeling weer te geven.**

Zodra de hub is gemaakt en de beveiligingspartner is ingesteld, blijft u de beveiligingsprovider verbinden met de hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Een externe beveiligingsprovider implementeren in een bestaande hub

U ook een bestaande hub in een virtueel WAN selecteren en deze converteren naar een *beveiligde virtuele hub.*

1. Selecteer **bestaande hubs converteren**in Aan de **slag**.
2. Selecteer een abonnement en een bestaande hub. Volg de rest van de stappen om een externe provider in een nieuwe hub te implementeren.

Vergeet niet dat een VPN-gateway moet worden geïmplementeerd om een bestaande hub om te zetten naar beveiligde hub met externe providers.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Beveiligingsproviders van derden configureren om verbinding te maken met een beveiligde hub

Om tunnels naar de VPN-gateway van uw virtuele hub in te stellen, hebben externe providers toegangsrechten nodig tot uw hub. Als u dit wilt doen, koppelt u een serviceprincipal aan uw abonnement of resourcegroep en verleent u toegangsrechten. U moet deze referenties vervolgens aan de derde partij geven met behulp van hun portal.

1. Hoofdvan de AD-serviceprincipal (Azure Active Directory) maken: u de omleidings-URL overslaan. 

   [How to: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Voeg toegangsrechten en ruimte toe voor de serviceprincipal.
   [How to: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > U de toegang beperken tot alleen uw brongroep voor meer gedetailleerde controle.
3. Volg de [ZScaler: Een Microsoft Azure Virtual WAN-integratie-instructies configureren](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) om:

   - Meld u aan bij de partnerportal en voeg uw referenties toe om de vertrouwde partner toegang te geven tot uw beveiligde hub.
   - Synchroniseer de virtuele hubs in het partnerportaal en stel de tunnel in naar de virtuele hub. U dit doen zodra uw Azure AD-verificatiereferenties zijn gevalideerd.
   
4. U de status van het maken van de tunnel bekijken op de Azure Virtual WAN-portal in Azure. Zodra de tunnels **zijn verbonden** op zowel Azure als de partnerportal, gaat u verder met de volgende stappen om routes in te stellen om te selecteren welke branches en VNets internetverkeer naar de partner moeten verzenden.

## <a name="configure-route-settings"></a>Route-instellingen configureren

1. Blader naar de Azure Firewall Manager -> Secured Hubs. 
2. Selecteer een hub. De hubstatus moet nu **Ingerichte** in plaats van **Beveiligingsverbinding in behandeling weergeven**.

   Zorg ervoor dat de externe provider verbinding kan maken met de hub. De tunnels op de VPN-gateway moeten in **een verbonden** toestand zijn. Deze toestand is meer een afspiegeling van de verbindingsstatus tussen de hub en de externe partner, in vergelijking met de vorige status.
3. Selecteer de hub en navigeer naar **Route-instellingen**.

   Wanneer u een externe provider in de hub implementeert, wordt de hub omgezet in een *beveiligde virtuele hub.* Dit zorgt ervoor dat de externe provider een 0.0.0.0/0 (standaard) route naar de hub adverteert. VNet-verbindingen en sites die zijn aangesloten op de hub krijgen deze route echter niet, tenzij u zich aanmaakt voor welke verbindingen deze standaardroute moeten krijgen.
4. Selecteer **onder internetverkeer** **VNet-naar-Internet** of **Branch-to-Internet** of beide routes worden geconfigureerd via de derde partij.

   Dit geeft alleen aan welk type verkeer naar de hub moet worden doorgestuurd, maar het heeft nog geen invloed op de routes op VNets of branches. Deze routes worden niet standaard gepropageerd naar alle VNets/branches die aan de hub zijn gekoppeld.
5. U moet **beveiligde verbindingen** selecteren en de verbindingen selecteren waarop deze routes moeten worden ingesteld. Dit geeft aan welke VNets/branches internetverkeer naar de externe provider kunnen gaan verzenden.
6. Selecteer **in Route-instellingen** **Beveiligde verbindingen** onder internetverkeer en selecteer vervolgens de VNet of branches *(sites* in Virtual WAN) die moeten worden beveiligd. Selecteer **Beveiligd internetverkeer**.
   ![Veilig internetverkeer](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Navigeer terug naar de hubspagina. De status **van de vertrouwde beveiligingspartner** van de hub moet nu worden **beveiligd.**

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Internetverkeer via internetverkeer via service van derden

Vervolgens u controleren of virtuele VNet-machines of de filiaalsite toegang hebben tot internet en valideren of het verkeer naar de service van derden stroomt.

Na het voltooien van de route-instellingsstappen, worden de virtuele VNet-machines en de filiaalsites een 0/0-serviceroute van derden verzonden. U niet RDP of SSH in deze virtuele machines. Als u zich wilt aanmelden, u de [Azure Bastion-service](../bastion/bastion-overview.md) implementeren in een peered VNet.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Uw cloudnetwerk beveiligen met Azure Firewall Manager Preview met de Azure-portal](secure-cloud-network.md)




