---
title: Een Azure Firewall Manager-beveiligings partner provider implementeren
description: Meer informatie over het implementeren van een Azure Firewall Manager security partner provider met behulp van de Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: victorh
ms.openlocfilehash: 91cf453247bfe4fa689df34bdf6b585ac72686aa
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85509051"
---
# <a name="deploy-a-security-partner-provider-preview"></a>Een beveiligings partner provider (preview-versie) implementeren

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Met *aanbieders van beveiligings partners* in azure firewall Manager kunt u uw vertrouwde, eersteklas SECaaS-aanbiedingen (Security as-a-Service) van derden gebruiken om de Internet toegang voor uw gebruikers te beveiligen.

Zie [Wat zijn vertrouwde beveiligings partners (preview)?](trusted-security-partners.md)voor meer informatie over ondersteunde scenario's en best practice richt lijnen.

De ondersteunde beveiligings partners zijn **ZScaler**, **Check Point**en **iboss** voor deze preview. Ondersteunde regio's zijn WestCentralUS, NorthCentralUS, Westus, WestUS2 en Oost-Timor.

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> Azure Firewall Manager-Preview moet expliciet worden ingeschakeld met behulp van de `Register-AzProviderFeature` Power shell-opdracht.

Voer vanuit een Power shell-opdracht prompt de volgende opdrachten uit:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Het duurt Maxi maal 30 minuten voordat de functie registratie is voltooid. Voer de volgende opdracht uit om de registratie status te controleren:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Een beveiligings provider van derden implementeren in een nieuwe hub

Sla deze sectie over als u een provider van derden in een bestaande hub implementeert.

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
9. Selecteer **volgende: provider van beveiligings partner**.
10. Selecteer **beveiligings partner provider** om deze in te stellen op **ingeschakeld**. Selecteer een partner. 
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

### <a name="create-and-authorize-a-service-principal"></a>Een service-principal maken en autoriseren

1. Create Azure Active Directory (AD) Service-Principal: u kunt de omleidings-URL overs Laan. 

   [Procedure: Gebruik de portal voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Voeg toegangs rechten en bereik voor de Service-Principal toe.
   [Procedure: Gebruik de portal voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > U kunt de toegang tot de resource groep beperken voor meer gedetailleerde besturings elementen.

### <a name="visit-partner-portal"></a>Partner Portal bezoeken

1. Volg de instructies van uw partner om de installatie te volt ooien. Dit omvat het verzenden van AAD-gegevens om de hub te detecteren en er verbinding mee te maken, het uitzonderings beleid bij te werken en de connectiviteits status en logboeken te controleren.

   - [Zscaler: configureer Microsoft Azure virtuele WAN-integratie](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Controle punt: configureer Microsoft Azure virtuele WAN-integratie](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   
2. U kunt de status voor het maken van de tunnel bekijken in de Azure Virtual WAN-Portal in Azure. Zodra de tunnels **zijn verbonden** met Azure en de Partner Portal, gaat u door met de volgende stappen om routes in te stellen om te selecteren welke branches en VNets Internet verkeer naar de partner moeten verzenden.

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