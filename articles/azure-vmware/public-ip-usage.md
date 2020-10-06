---
title: De open bare IP-functionaliteit gebruiken in virtuele WAN
description: In dit artikel wordt uitgelegd hoe u de open bare IP-functionaliteit in azure Virtual WAN kunt gebruiken.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: ec8af45a98e82a7c1c657776c4fee2c3ef068dca
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744825"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-virtual-wan"></a>De open bare IP-functionaliteit gebruiken in azure Virtual WAN

Open bare IP is een nieuwe functie in azure VMware-oplossings connectiviteit en klanten kunnen op twee manieren open bare Internet toegang inschakelen. 

- Toepassingen kunnen worden gehost en gepubliceerd onder het Application Gateway load balancer voor HTTP/HTTPS-verkeer.
- Gepubliceerd via open bare IP-functies in azure Virtual WAN.

Als onderdeel van de implementatie van een privécloud in azure VMware, kunt u, als u open bare IP-functionaliteit inschakelt, de vereiste onderdelen met automatisering automatisch maken en inschakelen:

-  Virtual WAN

-  Virtuele WAN-hub met ExpressRoute-connectiviteit

-  Azure Firewall Services met een openbaar IP-adres

In dit artikel wordt beschreven hoe u de open bare IP-functionaliteit in virtuele WAN kunt gebruiken om resources, zoals webservers, virtuele machines (Vm's) en hosts, toegankelijk te maken via een openbaar netwerk.

## <a name="prerequisites"></a>Vereisten

-   Azure VMware-oplossings omgeving

-   Een webserver die wordt uitgevoerd in de Azure VMware-oplossings omgeving.

## <a name="reference-architecture"></a>Referentiearchitectuur

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagram van open bare IP-architectuur" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Het architectuur diagram toont een webserver van de klant die wordt gehost in de Azure VMware-oplossings omgeving en is geconfigureerd met persoonlijke IP-adressen van RFC1918.  Deze webservice wordt beschikbaar gesteld op Internet via de open bare IP-functionaliteit voor virtuele WAN-verkeer.  Het open bare IP-adres is doorgaans een NAT-bestemming in Azure Firewall. Met DNAT-regels worden de open bare IP-adres aanvragen door het firewall beleid omgezet naar een privé adres (webserver) met een poort.

Gebruikers aanvragen hebben de firewall op een openbaar IP-adres dat op zijn beurt wordt omgezet naar privé-IP met behulp van DNAT-regels in de Azure Firewall. De firewall controleert de NAT-tabel en als de aanvraag overeenkomt met een vermelding, wordt het verkeer doorgestuurd naar het vertaalde adres en de poort in de Azure VMware-oplossings omgeving.

De webserver ontvangt de aanvraag en beantwoordt met de aangevraagde informatie of pagina aan de firewall, waarna de firewall de informatie doorstuurt naar de gebruiker op het open bare IP-adres.

## <a name="test-case"></a>Testcase
In dit scenario moet u de IIS-webserver publiceren op internet. Gebruik de open bare IP-functie in de Azure VMware-oplossing om de website te publiceren op een openbaar IP-adres.  We configureren NAT-regels op de firewall en hebben toegang tot Azure VMware-oplossings resources (Vm's met webserver) met een openbaar IP-adres.

## <a name="deploy-virtual-wan"></a>Virtual WAN implementeren

1. Meld u aan bij de Azure Portal en zoek naar en selecteer de **Azure VMware-oplossing**.

1. Selecteer de privécloud van de Azure VMware-oplossing.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. Selecteer onder **beheren**de optie **connectiviteit**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Selecteer het tabblad **openbaar IP-adres** en selecteer vervolgens **configureren**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

2.  Accepteer de standaard waarden of wijzig deze en selecteer **maken**.

   -  Resource groep voor virtueel Wide Area Network

   -  Naam van virtueel Wide Area Network

   -  Virtuele-hub-adres blok

   -  Aantal open bare Ip's (1-100)

Het duurt ongeveer een uur voordat de implementatie van alle onderdelen is voltooid. Deze implementatie hoeft slechts eenmaal te worden uitgevoerd om alle toekomstige open bare Ip's voor deze Azure VMware-oplossings omgeving te ondersteunen.  

>[!TIP]
>U kunt de status vanuit het **systeemvak** bewaken. 

## <a name="view-and-add-public-ip-addresses"></a>Open bare IP-adressen weer geven en toevoegen

U kunt de onderstaande stappen volgen om meer open bare IP-adressen te controleren en toe te voegen.

1. Zoek in het Azure Portal naar en selecteer de **firewall**.

1. Selecteer een geïmplementeerde firewall en selecteer vervolgens **Ga naar Azure firewall Manager om deze firewall te configureren en te beheren**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selecteer **beveiligde virtuele hubs** en selecteer in de lijst een virtuele hub.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Diagram van open bare IP-architectuur" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Selecteer op de pagina virtuele hub de optie **open bare IP-configuratie**en klik vervolgens op **toevoegen**om meer open bare IP-adressen toe te voegen. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Geef het aantal Ip's op dat is vereist en selecteer **toevoegen**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Diagram van open bare IP-architectuur" border="true":::


## <a name="create-firewall-policies"></a>Firewall beleid maken

Zodra alle onderdelen zijn geïmplementeerd, kunt u ze weer geven in de toegevoegde resource groep. De volgende stap is het toevoegen van een firewall beleid.

1. Zoek in het Azure Portal naar en selecteer de **firewall**.

1. Selecteer een geïmplementeerde firewall en selecteer vervolgens **Ga naar Azure firewall Manager om deze firewall te configureren en te beheren**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selecteer **Azure firewall-beleid** en selecteer vervolgens **Azure firewall beleid maken**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Geef op het tabblad **basis beginselen** de vereiste gegevens op en selecteer **volgende: DNS-instellingen**. 

1. Selecteer op het tabblad **DNS** de optie **uitschakelen**en selecteer vervolgens **volgende: regels**.

1. Selecteer **een regel verzameling toevoegen**, geef de onderstaande gegevens op en selecteer **toevoegen** en selecteer vervolgens **volgende: bedreigings informatie**.

   -  Naam
   -  Type regel verzameling-DNAT
   -  Prioriteit
   -  Actie verzameling van regel: toestaan
   -  Naam van regel
   -  Bron type- **IP-adres**
   -  Bron **\***
   -  Protocol: **TCP**
   -  Doel poort: **80**
   -  Doel type: **IP-adres**
   -  Doel- **openbaar IP-adres**
   -  Vertaald adres: **Azure VMware Solution webserver privé IP-adres**
   -  Vertaalde poort: **Azure VMware Solution web server-poort**

1. Wijzig de standaard waarde en selecteer vervolgens **volgende: hubs**.

1. Selecteer **virtuele hub koppelen**.

   :::image type="content" source="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png":::

1. Selecteer een hub in de lijst en selecteer **toevoegen**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Diagram van open bare IP-architectuur" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Selecteer **Volgende: Labels**. 

1. Beschrijving Maak naam/waarde-paren om uw resources te categoriseren. 

1. Selecteer **volgende: controleren + maken** en selecteer vervolgens **maken**.

## <a name="limitations"></a>Beperkingen

U kunt 100 open bare Ip's per SDDCs hebben.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van open bare IP-adressen met behulp van [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md).

