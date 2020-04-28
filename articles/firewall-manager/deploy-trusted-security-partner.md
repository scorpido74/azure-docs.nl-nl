---
title: Een vertrouwde beveiligings partner van Azure Firewall Manager implementeren
description: Meer informatie over het implementeren van een Azure Firewall Manager-vertrouwde beveiliging met behulp van de Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73931303"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Een vertrouwde beveiligingspartner implementeren (preview)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Met *vertrouwde beveiligings partners* in azure firewall Manager kunt u uw vertrouwde en beste SECaaS-aanbiedingen (Security as a Service) van derden gebruiken om de Internet toegang voor uw gebruikers te beveiligen.

Zie [Wat zijn vertrouwde beveiligings partners (preview)?](trusted-security-partners.md)voor meer informatie over ondersteunde scenario's en best practice richt lijnen.

De ondersteunde beveiligings partners zijn **ZScaler** en **iboss** voor deze preview. Ondersteunde regio's zijn WestCentralUS, NorthCentralUS, Westus, WestUS2 en Oost-Timor.

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> Azure Firewall Manager-Preview moet expliciet worden ingeschakeld met `Register-AzProviderFeature` behulp van de Power shell-opdracht.

Voer vanuit een Power shell-opdracht prompt de volgende opdrachten uit:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Het duurt Maxi maal 30 minuten voordat de functie registratie is voltooid. Voer de volgende opdracht uit om de registratie status te controleren:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Een beveiligings provider van derden implementeren in een nieuwe hub

1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. In **zoeken**, typt u **firewall Manager** en selecteert u deze onder **Services**.
3. Ga naar aan de **slag**. Selecteer **een beveiligde virtuele hub maken**. 
4. Voer abonnement en resource groep in, selecteer een ondersteunde regio en voeg uw hub en virtuele WAN-gegevens toe. 
5. **Implementeren van VPN-gateway** is standaard ingeschakeld. Een VPN Gateway is vereist voor het implementeren van een vertrouwde beveiligings partner in de hub. 
6. Selecteer **volgende: Azure firewall**
   > [!NOTE]
   > Vertrouwde beveiligings partners maken verbinding met uw hub met behulp van VPN Gateway tunnels. Als u de VPN Gateway verwijdert, gaan de verbindingen met uw vertrouwde beveiligings partners verloren.
7. Als u Azure Firewall wilt implementeren voor het filteren van privé verkeer samen met de service provider van derden om het Internet verkeer te filteren, selecteert u een beleid voor Azure Firewall. Zie de [ondersteunde scenario's](trusted-security-partners.md#key-scenarios).
8. Als u alleen een beveiligings provider van derden wilt implementeren in de hub, selecteert u **Azure firewall: ingeschakeld/uitgeschakeld** om deze in te stellen op **uitgeschakeld**. 
9. Selecteer **volgende: vertrouwde beveiligings partners**.
10. Selecteer **vertrouwde beveiligings partner** om deze in te stellen op **ingeschakeld**. Selecteer een partner. 
11. Selecteer **Next**. 
12. Bekijk de inhoud en selecteer vervolgens **maken**.

De implementatie van de VPN-gateway kan meer dan 30 minuten duren.

Ga naar Azure Firewall Manager->beveiligde hubs om te controleren of de hub is gemaakt. Selecteer de pagina overzicht van hub->om de naam van de partner en de status weer te geven als **in behandeling zijnde beveiligings verbinding**.

Zodra de hub is gemaakt en de beveiligings partner is ingesteld, gaat u door met om de beveiligings provider te verbinden met de hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Een beveiligings provider van derden implementeren in een bestaande hub

U kunt ook een bestaande hub in een virtueel WAN selecteren en deze converteren naar een *beveiligde virtuele hub*.

1. Selecteer **bestaande hubs converteren**bij **aan de slag**.
2. Selecteer een abonnement en een bestaande hub. Volg de overige stappen voor het implementeren van een provider van derden in een nieuwe hub.

Houd er rekening mee dat een VPN-gateway moet worden geïmplementeerd om een bestaande hub te converteren naar een beveiligde hub met externe providers.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Beveiligings providers van derden configureren om verbinding te maken met een beveiligde hub

Om tunnels in te stellen voor de VPN Gateway van de virtuele hub, hebben externe providers toegangs rechten nodig voor uw hub. U kunt dit doen door een service-principal te koppelen aan uw abonnement of resource groep en toegangs rechten te verlenen. Vervolgens moet u deze referenties aan de derde partij geven met behulp van hun portal.

1. Create Azure Active Directory (AD) Service-Principal: u kunt de omleidings-URL overs Laan. 

   [Procedure: een Azure AD-toepassing en service-principal maken met behulp van de portal om toegang te krijgen tot resources](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Voeg toegangs rechten en bereik voor de Service-Principal toe.
   [Procedure: een Azure AD-toepassing en service-principal maken met behulp van de portal om toegang te krijgen tot resources](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > U kunt de toegang tot de resource groep beperken voor meer gedetailleerde besturings elementen.
3. Volg de [ZScaler: configure a Microsoft Azure instructies voor het configureren van virtuele WAN-integratie](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) naar:

   - Meld u aan bij de Partner Portal en voeg uw referenties toe om de vertrouwde partner toegang te geven tot uw beveiligde hub.
   - Synchroniseer de virtuele hubs in de Partner Portal en stel de tunnel in op de virtuele hub. U kunt dit doen zodra uw Azure AD-verificatie referenties zijn gevalideerd.
   
4. U kunt de status voor het maken van de tunnel bekijken in de Azure Virtual WAN-Portal in Azure. Zodra de tunnels **zijn verbonden** met Azure en de Partner Portal, gaat u door met de volgende stappen om routes in te stellen om te selecteren welke branches en VNets Internet verkeer naar de partner moeten verzenden.

## <a name="configure-route-settings"></a>Route-instellingen configureren

1. Blader naar de Azure Firewall Manager-> beveiligde hubs. 
2. Selecteer een hub. De status van de hub moet nu **worden weer gegeven** in plaats van de **beveiligings verbinding in behandeling**.

   Zorg ervoor dat de provider van derden verbinding kan maken met de hub. De tunnels op de VPN-gateway moeten een **verbonden status hebben** . Deze status is beter zichtbaar voor de verbindings status tussen de hub en de partner van derden, vergeleken met de vorige status.
3. Selecteer de hub en navigeer naar **router instellingen**.

   Wanneer u een provider van derden op de hub implementeert, wordt de hub geconverteerd naar een *beveiligde virtuele hub*. Dit zorgt ervoor dat de provider van derden een ' 0.0.0.0/0-route (standaard) adverteert naar de hub. VNet-verbindingen en sites die zijn verbonden met de hub, krijgen deze route echter alleen als u zich aanmeldt op welke verbindingen deze standaard route moeten worden verkregen.
4. Onder **Internet verkeer**selecteert u **VNet-naar-Internet** of **vertakking-naar-Internet** of beide, zodat routes worden geconfigureerd via de derde partij.

   Hiermee wordt alleen aangegeven welk type verkeer moet worden doorgestuurd naar de hub, maar dit is niet van invloed op de routes op VNets of branches. Deze routes worden standaard niet door gegeven aan alle VNets/vertakkingen die zijn gekoppeld aan de hub.
5. U moet **beveiligde verbindingen** selecteren en de verbindingen selecteren waarop deze routes moeten worden ingesteld. Hiermee wordt aangegeven welke VNets/branches Internet verkeer naar de externe provider kunnen gaan verzenden.
6. Selecteer vanuit **route**-instellingen **beveiligde verbindingen** onder Internet verkeer en selecteer vervolgens de VNet of vertakkingen (*sites* in virtueel WAN) die moeten worden beveiligd. Selecteer **beveiligd Internet verkeer**.
   ![Beveiligd Internet verkeer](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Ga terug naar de pagina hubs. De status van de **vertrouwde beveiligings partner** van de hub moet nu worden **beveiligd**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Vertakkings-of VNet-Internet verkeer via een service van derden

Vervolgens kunt u controleren of de virtuele VNet-machines of de filiaal site toegang hebben tot internet en controleren of het verkeer naar de service van derden loopt.

Na het volt ooien van de stappen voor het instellen van de route worden de virtuele VNet-machines en de branch sites verzonden naar een service route van 0/0 naar derden. U kunt geen RDP of SSH in deze virtuele machines. Als u zich wilt aanmelden, kunt u de [Azure Bastion](../bastion/bastion-overview.md) -service in een gepeerd VNet implementeren.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: uw Cloud netwerk beveiligen met Azure Firewall Manager Preview met behulp van de Azure Portal](secure-cloud-network.md)




