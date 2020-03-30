---
title: Openbare endpoint-connectiviteit voor Azure VM's&Standaard ILB in SAP HA-scenario's
description: Openbare endpoint-connectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's met hoge beschikbaarheid
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293918"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Openbare endpoint-connectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's met hoge beschikbaarheid

Het bereik van dit artikel is het beschrijven van configuraties, die uitgaande connectiviteit met het openbare eindpunt(en) mogelijk maken. De configuraties zijn voornamelijk in het kader van Hoge Beschikbaarheid met Pacemaker voor SUSE / RHEL.  

Als u Pacemaker met Azure-afrasteringsagent gebruikt in uw oplossing met hoge beschikbaarheid, moeten de VM's uitgaande connectiviteit hebben met de Azure-beheer-API.  
Het artikel bevat verschillende opties waarmee u de optie selecteren die het meest geschikt is voor uw scenario.  

## <a name="overview"></a>Overzicht

Bij het implementeren van hoge beschikbaarheid voor SAP-oplossingen via clustering is azure [load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)een van de benodigde componenten. Azure biedt twee Load balancer SKU's: standaard en basic.

Standaard Azure load balancer biedt enkele voordelen ten opzichte van de Basic load balancer. Het werkt bijvoorbeeld in azure-beschikbaarheidszones, het heeft betere controle- en registratiemogelijkheden voor eenvoudigere probleemoplossing, verminderde latentie. De "HA-poorten" functie heeft betrekking op alle poorten, dat wil zeggen, is het niet langer nodig om alle afzonderlijke poorten lijst.  

Er zijn enkele belangrijke verschillen tussen de basis en de standaard SKU van Azure load balancer. Een van hen is de afhandeling van uitgaand verkeer naar het openbare eindpunt. Zie Vergelijking van [Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)voor volledige basis- versus standaardsku-load balancervergelijking .  
 
Wanneer VM's zonder openbare IP-adressen in de backendpool van interne (geen openbare IP-adres) Standaard Azure-loadbalancer worden geplaatst, is er geen uitgaande verbinding met openbare eindpunten, tenzij extra configuratie wordt uitgevoerd.  

Als aan een vm een openbaar IP-adres is toegewezen of als de VM zich in de backendpool bevindt van een load balancer met een openbaar IP-adres, heeft deze uitgaande verbinding met openbare eindpunten.  

SAP-systemen bevatten vaak gevoelige bedrijfsgegevens. Het is zelden aanvaardbaar dat VM's die SAP-systemen hosten, openbare IP-adressen hebben. Tegelijkertijd zijn er scenario's, waarvoor uitgaande connectiviteit van de VM naar openbare eindpunten vereist is.  

Voorbeelden van scenario's waarvoor toegang tot het openbare eindpunt van Azure vereist is:  
- Azure Fence Agent gebruiken als afrasteringsmechanisme in pacemakerclusters
- Azure Backup
- Azure Site Recovery  
- Openbare repository gebruiken voor het patchen van het besturingssysteem
- De gegevensstroom van SAP-toepassingen vereist mogelijk uitgaande connectiviteit met het openbare eindpunt

Als uw SAP-implementatie geen uitgaande connectiviteit met openbare eindpunten vereist, hoeft u de extra configuratie niet te implementeren. Het volstaat om interne standaard SKU Azure Load Balancer te maken voor uw scenario met hoge beschikbaarheid, ervan uitgaande dat er ook geen behoefte is aan inkomende connectiviteit van openbare eindpunten.  

> [!Note]
> Wanneer VM's zonder openbare IP-adressen in de backendpool van interne (geen openbare IP-adres) Standaard Azure-loadbalancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij extra configuratie wordt uitgevoerd om routering naar openbare eindpunten mogelijk te maken.  
>Als de VM's openbare IP-adressen hebben of al in de backendpool van Azure Load balancer met openbaar IP-adres staan, heeft de VM al uitgaande connectiviteit met openbare eindpunten.


Lees eerst de volgende papers:

* Azure Standard Load Balancer
  * [Azure Standard Load Balancer overzicht](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) - uitgebreid overzicht van Azure Standard Load balancer, belangrijke principes, concepten en tutorials 
  * [Uitgaande verbindingen in Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) - scenario's voor het bereiken van uitgaande connectiviteit in Azure
  * [Uitgaande regels voor load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)- legt de concepten uit van uitgaande regels voor load balancer en hoe u uitgaande regels maakt
* Azure Firewall
  * [Overzicht azure firewall](https://docs.microsoft.com/azure/firewall/overview)- overzicht van Azure Firewall
  * [Zelfstudie: Azure Firewall implementeren en configureren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) - instructies voor het configureren van Azure Firewall via Azure Portal
* [Virtuele netwerken -Door de gebruiker gedefinieerde regels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) - Azure-routeringsconcepten en -regels  
* [Servicetags voor beveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) - hoe u uw netwerkbeveiligingsgroepen en firewallconfiguratie vereenvoudigen met servicetags

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Extra externe Azure Standard Load Balancer voor uitgaande verbindingen met internet

Een optie om uitgaande connectiviteit met openbare eindpunten te bereiken, zonder inkomende connectiviteit met de VM vanaf het openbare eindpunt toe te staan, is het maken van een tweede load balancer met openbaar IP-adres, de VM's toevoegen aan de backendpool van de tweede load balancer en alleen [uitgaande regels](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)definiëren.  
Gebruik [netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview) om de openbare eindpunten te beheren die toegankelijk zijn voor uitgaande oproepen van de VM.  
Zie Scenario 2 in uitgaande [verbindingen](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)voor documenten voor meer informatie .  
De configuratie zou eruit zien als volgt:  

![Connectiviteit met openbare eindpunten beheren met netwerkbeveiligingsgroepen](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Belangrijke overwegingen

- U één extra Public Load Balancer gebruiken voor meerdere VM's in hetzelfde subnet om uitgaande connectiviteit met het openbare eindpunt te bereiken en de kosten te optimaliseren  
- Gebruik [netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview) om te bepalen welke openbare eindpunten toegankelijk zijn vanuit de VM's. U de netwerkbeveiligingsgroep toewijzen aan het subnet of aan elke virtuele machine. Gebruik waar mogelijk [Servicetags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) om de complexiteit van de beveiligingsregels te verminderen.  
- Azure-standaard load balancer met openbare IP-adres en uitgaande regels maakt directe toegang tot het openbare eindpunt mogelijk. Als u zakelijke beveiligingsvereisten hebt om alle uitgaande verkeerspas via gecentraliseerde bedrijfsoplossing voor controle en logboekregistratie te hebben, u mogelijk niet aan de vereiste voldoen met dit scenario.  

>[!TIP]
>Gebruik waar mogelijk [Servicetags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) om de complexiteit van de Netwerkbeveiligingsgroep te verminderen. 

### <a name="deployment-steps"></a>Implementatiestappen

1. Load Balancer maken  
   1. Klik in de [Azure-portal](https://portal.azure.com) op Alle resources, Toevoegen en zoek vervolgens naar **Load Balancer**  
   1. Klik **op Maken** 
   1. Load Balancer Naam **MyPublicILB**  
   1. **Selecteer Openbaar** als type, **standaard** als SKU  
   1. Selecteer **Openbaar IP-adres maken** en opgeven als naam **MyPublicILBFrondEndIP**  
   1. **Zone redundant** selecteren als beschikbaarheidszone  
   1. Klik op Controleren en maken en klik vervolgens op Maken  
2. Maak Backend pool **MyBackendPoolOfPublicILB** en voeg de VM's toe.  
   1. Het virtuele netwerk selecteren  
   1. Selecteer de VM's en hun IP-adressen en voeg ze toe aan de backendpool  
3. [Uitgaande regels maken](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). Momenteel is het niet mogelijk om uitgaande regels te maken vanuit de Azure-portal. U uitgaande regels maken met [Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Netwerkbeveiligingsgroepregels maken om de toegang tot specifieke openbare eindpunten te beperken. Als er een bestaande netwerkbeveiligingsgroep is, u deze aanpassen. In het onderstaande voorbeeld ziet u hoe u toegang tot de Azure-beheer-API inschakelen: 
   1. Navigeren naar de netwerkbeveiligingsgroep
   1. Klik op Uitgaande beveiligingsregels
   1. Voeg een regel toe aan Alle uitgaande toegang tot **internet** **weigeren** .
   1. Voeg een regel toe om toegang tot **AzureCloud**toe te **staan,** met een prioriteit die lager is dan de prioriteit van de regel om alle internettoegang te weigeren.


   De uitgaande beveiligingsregels zien eruit als volgt: 

   ![Uitgaande verbinding met Second Load Balancer met openbaar IP](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Zie [Beveiligingsgroepen ](https://docs.microsoft.com/azure/virtual-network/security-overview)voor meer informatie over beveiligingsgroepen van Azure Network . 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Azure Firewall voor uitgaande verbindingen met internet

Een andere optie om uitgaande connectiviteit met openbare eindpunten te bereiken, zonder inkomende connectiviteit met de VM toe te staan vanaf openbare eindpunten, is met Azure Firewall. Azure Firewall is een beheerde service, met ingebouwde hoge beschikbaarheid en kan meerdere beschikbaarheidszones omvatten.  
U moet ook [user defined route](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)implementeren, gekoppeld aan subnet waar VM's en de Azure load balancer worden geïmplementeerd, wijzend naar de Azure-firewall, om verkeer door de Azure Firewall te leiden.  
Zie Azure Firewall implementeren en configureren voor meer informatie over het implementeren van Azure [Firewall.](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)  

De architectuur zou eruit zien als:

![Uitgaande verbinding met Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Belangrijke overwegingen

- Azure firewall is cloud native service, met ingebouwde High Availability en het ondersteunt zonale implementatie.
- Vereist extra subnet dat azurefirewallsubnet moet heten. 
- Als het overbrengen van grote gegevens uitgaande sets van het virtuele netwerk waar de SAP VM's zich bevinden, naar een VM in een ander virtueel netwerk, of naar het openbare eindpunt, is het mogelijk niet een kosteneffectieve oplossing. Een voorbeeld hiervan is het kopiëren van grote back-ups over virtuele netwerken. Zie Azure Firewall-prijzen voor meer informatie.  
- Als de bedrijfsfirewalloplossing geen Azure Firewall is en u beveiligingsvereisten hebt om alle uitgaande verkeerspas te hebben, hoewel een gecentraliseerde bedrijfsoplossing, is deze oplossing mogelijk niet praktisch.  

>[!TIP]
>Gebruik waar mogelijk [Servicetags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) om de complexiteit van de Azure Firewall-regels te verminderen.  

### <a name="deployment-steps"></a>Implementatiestappen

1. De implementatiestappen gaan ervan uit dat u al een virtueel netwerk en subnet hebt gedefinieerd voor uw VM's.  
2. Subnet **AzureFirewallSubnet maken** in hetzelfde virtuele netwerk, waar de VMS en de StandaardLoad Balancer worden geïmplementeerd.  
   1. Navigeer in azure-portal naar het virtuele netwerk: klik op Alle bronnen, zoek naar het virtuele netwerk, klik op het virtuele netwerk, Selecteer Subnetten.  
   1. Klik op Subnet toevoegen. Voer **AzureFirewallSubnet** in als naam. Voer het juiste adresbereik in. Sla op.  
3. Azure Firewall maken.  
   1. Selecteer in Azure-portal Alle bronnen, klik op Toevoegen, Firewall, Maken. Selecteer Resourcegroep (selecteer dezelfde resourcegroep, waar het virtuele netwerk zich bevindt).  
   1. Voer de naam in voor de Azure Firewall-bron. **MyAzureFirewall**.  
   1. Selecteer Regio en selecteer ten minste twee beschikbaarheidszones, uitgelijnd met de beschikbaarheidszones waar uw VM's worden geïmplementeerd.  
   1. Selecteer uw virtuele netwerk, waar de SAP VM's en Azure Standard Load balancer worden geïmplementeerd.  
   1. Openbaar IP-adres: klik op maken en voer een naam in. Bijvoorbeeld **MyFirewallPublicIP**.  
4. Maak Azure Firewall Rule om uitgaande connectiviteit toe te staan voor opgegeven openbare eindpunten. In het voorbeeld wordt uitgelegd hoe u toegang tot het openbare eindpunt van de Azure Management API toestaan.  
   1. Selecteer Regels, netwerkregelverzameling en klik op Netwerkregelverzameling toevoegen.  
   1. Naam: **MyOutboundRule**, voer Prioriteit in, Selecteer Actie **toestaan**.  
   1. Service: Naam **ToAzureAPI**.  Protocol: Selecteer **Een aantal**. Bronadres: voer het bereik in voor uw subnet, waarbij bijvoorbeeld de VM's en standaardloadbalansr worden geïmplementeerd: **11.97.0.0/24**. Bestemmingspoorten: <b>*</b>voer .  
   1. Opslaan
   1. Terwijl u zich nog steeds op de Azure Firewall bevindt, selecteert u Overzicht. Noteer het privé-IP-adres van de Azure Firewall.  
5. Route naar Azure Firewall maken  
   1. Selecteer in Azure-portal Alle resources en klik vervolgens op Toevoegen, Routetabel, Maken.  
   1. Voer Naam MyRouteTable in, selecteer Abonnement, Resourcegroep en Locatie (overeenkomend met de locatie van uw virtuele netwerk en firewall).  
   1. Opslaan  

   De firewallregel ziet ![eruit als: Uitgaande verbinding met Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Gebruikersgedefinieerde route maken van het subnet van uw VM's naar het privé-IP van **MyAzureFirewall**.
   1. Klik op Routes terwijl u zich in de routetabel bevindt. Selecteer Toevoegen. 
   1. Routenaam: ToMyAzureFirewall, Adresvoorvoegsel: **0.0.0.0/0**. Volgende hoptype: Selecteer Virtueel toestel. Volgende hopadres: voer het privé-IP-adres in van de firewall die u hebt geconfigureerd: **11.97.1.4**.  
   1. Opslaan

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Proxy gebruiken voor pacemakeraanroepen naar Azure Management API

U proxy gebruiken om pacemakeroproepen toe te staan naar het openbare eindpunt van de Azure-beheer-API.  

### <a name="important-considerations"></a>Belangrijke overwegingen

  - Als er al bedrijfsproxy is, u uitgaande oproepen naar openbare eindpunten erdoorheen leiden. Uitgaande oproepen naar openbare eindpunten gaan via het controlepunt van de corporate.  
  - Controleer of de proxyconfiguratie uitgaande connectiviteit met Azure-beheer-API mogelijk maakt:`https://management.azure.com`  
  - Zorg ervoor dat er een route is van de VM's naar de proxy  
  - Proxy verwerkt alleen HTTP/HTTPS-aanroepen. Als er extra behoefte is om uitgaande oproepen naar het openbare eindpunt over verschillende protocollen (zoals RFC) te voeren, is een alternatieve oplossing nodig  
  - De Proxy-oplossing moet zeer beschikbaar zijn om instabiliteit in het pacemakercluster te voorkomen  
  - Afhankelijk van de locatie van de proxy kan het extra latentie introduceren in de aanroepen van de Azure Fence Agent naar de Azure Management API. Als uw bedrijfsproxy zich nog steeds op de locatie bevindt, terwijl uw Pacemaker-cluster zich in Azure bevindt, meet u latentie en overweegt u of deze oplossing geschikt is voor u  
  - Als er nog geen zeer beschikbare bedrijfsproxy is, raden we deze optie niet aan, omdat de klant extra kosten en complexiteit zou maken. Als u echter besluit om extra proxy-oplossing te implementeren, moet u ervoor zorgen dat de proxy in hoge mate beschikbaar is en dat de latentie van de VM's naar de proxy laag is.  

### <a name="pacemaker-configuration-with-proxy"></a>Pacemakerconfiguratie met proxy 

Er zijn veel verschillende Proxy-opties beschikbaar in de industrie. Stapsgewijze instructies voor de proxy-implementatie vallen buiten het bereik van dit document. In het onderstaande voorbeeld gaan we ervan uit dat uw proxy reageert op **MyProxyService** en luistert naar port **MyProxyPort**.  
Als u de pacemaker wilt laten communiceren met de Azure-beheer-API, voert u de volgende stappen uit op alle clusterknooppunten:  

1. Bewerk het pacemakerconfiguratiebestand /etc/sysconfig/pacemaker en voeg de volgende regels (alle clusterknooppunten) toe:

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Start de pacemakerservice opnieuw op **alle** clusterknooppunten.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het configureren van Pacemaker op SUSE in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Meer informatie over het configureren van Pacemaker op Red Hat in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
