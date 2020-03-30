---
title: Een op internet gerichte load-balancer implementeren met IPv6 - Azure-sjabloon
titleSuffix: Azure Load Balancer
description: Meer informatie over het implementeren van IPv6-ondersteuning voor Azure Load Balancer en vm's met een Azure-sjabloon met belastingsafweging.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, public ip, native ipv6, mobile, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: eb9703a1944a650f41d76c05d79764f8bdf8cd52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045444"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Een op internet gerichte load-balancer-oplossing implementeren met IPv6 met behulp van een sjabloon

> [!div class="op_single_selector"]
> * [Powershell](load-balancer-ipv6-internet-ps.md)
> * [Azure-CLI](load-balancer-ipv6-internet-cli.md)
> * [Sjabloon](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>In dit artikel wordt een inleidende IPv6-functie beschreven waarmee Basic Load Balancers zowel IPv4- als IPv6-connectiviteit kunnen bieden. Uitgebreide IPv6-connectiviteit is nu beschikbaar met [IPv6 voor Azure VNETs](../virtual-network/ipv6-overview.md) die IPv6-connectiviteit integreert met uw virtuele netwerken en belangrijke functies bevat, zoals IPv6 Network Security Group-regels, IPv6-gebruikersgedefinieerde routering, IPv6 Basic en Standard load balancing, en meer.  IPv6 voor Azure VNETs is de aanbevolen standaard voor IPv6-toepassingen in Azure. Zie [IPv6 voor Azure VNET Powershell-implementatie](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Azure Load Balancer is een Layer-4 (TCP, UDP) load balancer. De load balancer biedt hoge beschikbaarheid bij het verdelen van inkomend verkeer over gezonde service-exemplaren in cloudservices of virtuele machines in een load balancer-set. Azure Load Balancer kan deze services ook toepassen op meerdere poorten, meerdere IP-adressen of allebei.

## <a name="example-deployment-scenario"></a>Voorbeeldimplementatiescenario

In het volgende diagram ziet u de oplossing voor het balanceren van de last en de in dit artikel beschreven voorbeeldsjabloon.

![Load balancer-scenario](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

In dit scenario maakt u de volgende Azure-bronnen:

* een virtuele netwerkinterface voor elke virtuele virtuele virtuele interface met zowel IPv4- als IPv6-adressen toegewezen
* een op internet gerichte load balancer met een IPv4 en een IPv6 Public IP-adres
* twee regels voor het balanceren van de lasten om de openbare VIP's in kaart te brengen naar de privéeindpunten
* een beschikbaarheidsset met de twee VM's
* twee virtuele machines (VM's)

## <a name="deploying-the-template-using-the-azure-portal"></a>De sjabloon implementeren met de Azure-portal

In dit artikel wordt verwezen naar een sjabloon die wordt gepubliceerd in de azure [quickstartsjablonengalerie.](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) U de sjabloon uit de galerie downloaden of de implementatie in Azure rechtstreeks vanuit de galerie starten. In dit artikel wordt ervan uitgegaan dat u de sjabloon naar uw lokale computer hebt gedownload.

1. Open de Azure-portal en meld u aan met een account met machtigingen voor het maken van VM's en netwerkbronnen binnen een Azure-abonnement. Tenzij u bestaande bronnen gebruikt, heeft het account ook toestemming nodig om een brongroep en een opslagaccount te maken.
2. Klik in het menu op '+Nieuw' en typ 'sjabloon' in het zoekvak. Selecteer 'Sjabloonimplementatie' in de zoekresultaten.

    ![lb-ipv6-portal-stap2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Klik in het blad Alles op 'Sjabloonimplementatie'.

    ![lb-ipv6-portal-stap3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klik op 'Maken'.

    ![lb-ipv6-portal-stap4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klik op 'Sjabloon bewerken'. Verwijder de bestaande inhoud en kopiëren/plakken in de volledige inhoud van het sjabloonbestand (om het begin en einde { }) op te nemen en klik vervolgens op 'Opslaan'.

    > [!NOTE]
    > Als u Microsoft Internet Explorer gebruikt, ontvangt u wanneer u plakt een dialoogvenster waarin u wordt gevraagd toegang tot het Windows-klembord toe te staan. Klik op 'Toegang toestaan'.

    ![lb-ipv6-portal-stap5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klik op Parameters bewerken. Geef in het blad Parameters de waarden op per de richtlijn in de sectie Sjabloonparameters en klik op 'Opslaan' om het blad Parameters te sluiten. Selecteer in het blad Aangepaste implementatie uw abonnement, een bestaande brongroep of maak er een. Als u een resourcegroep maakt, selecteert u een locatie voor de resourcegroep. Klik vervolgens op **Juridische voorwaarden**en klik vervolgens op **Kopen** voor de wettelijke voorwaarden. Azure begint met het implementeren van de resources. Het duurt enkele minuten om alle resources te implementeren.

    ![lb-ipv6-portal-stap6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Zie de sectie [Sjabloonparameters en -variabelen](#template-parameters-and-variables) later in dit artikel voor meer informatie over deze parameters.

7. Als u de bronnen wilt zien die door de sjabloon zijn gemaakt, klikt u op Bladeren, scrolt u omlaag in de lijst totdat u 'Brongroepen' ziet en klikt u erop.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Klik op het blad Resourcegroepen op de naam van de resourcegroep die u in stap 6 hebt opgegeven. U ziet een lijst met alle resources die zijn geïmplementeerd. Als alles goed ging, zou het "Geslaagd" moeten zeggen onder "Laatste implementatie." Als dit niet het zo is, moet u ervoor zorgen dat het account dat u gebruikt, machtigingen heeft om de benodigde bronnen te maken.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Als u onmiddellijk na het voltooien van stap 6 door uw resourcegroepen bladert, wordt in 'Laatste implementatie' de status 'Implementeren' weergegeven terwijl de resources worden geïmplementeerd.

9. Klik op 'myIPv6PublicIP' in de lijst met bronnen. U ziet dat het een IPv6-adres onder IP-adres heeft en dat de DNS-naam de waarde is die u hebt opgegeven voor de parameter dnsNameforIPv6LbIP in stap 6. Deze bron is het openbare IPv6-adres en de hostnaam die toegankelijk is voor internetclients.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Connectiviteit valideren

Wanneer de sjabloon is geïmplementeerd, u de connectiviteit valideren door de volgende taken uit te voeren:

1. Meld u aan bij de Azure-portal en maak verbinding met elk van de VM's die door de sjabloonimplementatie zijn gemaakt. Als u een Windows Server VM hebt geïmplementeerd, voert u ipconfig /allen uit vanaf een opdrachtprompt. U ziet dat de VM's zowel IPv4- als IPv6-adressen hebben. Als u Linux-VM's hebt geïmplementeerd, moet u het Linux-besturingssysteem configureren om dynamische IPv6-adressen te ontvangen met behulp van de instructies voor uw Linux-distributie.
2. Start vanuit een met IPv6 verbonden client een verbinding met het openbare IPv6-adres van de load balancer. Als u wilt bevestigen dat de load balancer balanceert tussen de twee VM's, u een webserver zoals Microsoft Internet Information Services (IIS) op elk van de VM's installeren. De standaardwebpagina op elke server kan de tekst "Server0" of "Server1" bevatten om deze op unieke wijze te identificeren. Open vervolgens een internetbrowser op een met IPv6 verbonden client en blader naar de hostnaam die u hebt opgegeven voor de parameter dnsNameforIPv6LbIP van de load balancer om end-to-end IPv6-connectiviteit met elke VM te bevestigen. Als u de webpagina slechts vanaf één server ziet, moet u mogelijk de cache van uw browser wissen. Open meerdere privébrowsersessies. U ziet een antwoord van elke server.
3. Start vanuit een met IPv4 verbonden client een verbinding met het openbare IPv4-adres van de load balancer. Als u wilt bevestigen dat de load balancer de twee VM's in balans brengt, u iis testen, zoals beschreven in stap 2.
4. Start vanaf elke virtuele machine een uitgaande verbinding met een iPv6- of IPv4-verbinding met internet. In beide gevallen is het bron-IP dat door het doelapparaat wordt gezien het openbare IPv4- of IPv6-adres van de load balancer.

> [!NOTE]
> ICMP voor zowel IPv4 als IPv6 is geblokkeerd in het Azure-netwerk. Als gevolg hiervan falen ICMP-tools zoals ping altijd. Als u de connectiviteit wilt testen, gebruikt u een TCP-alternatief zoals TCPing of de PowerShell Test-NetConnection-cmdlet. Houd er rekening mee dat de IP-adressen in het diagram voorbeelden zijn van waarden die u mogelijk ziet. Aangezien de IPv6-adressen dynamisch zijn toegewezen, verschillen de adressen die u ontvangt en kunnen deze per regio verschillen. Ook is het gebruikelijk dat het openbare IPv6-adres op de load balancer begint met een ander voorvoegsel dan de privé IPv6-adressen in de back-endpool.

## <a name="template-parameters-and-variables"></a>Sjabloonparameters en -variabelen

Een Azure Resource Manager-sjabloon bevat meerdere variabelen en parameters die u aan uw behoeften aanpassen. Variabelen worden gebruikt voor vaste waarden die u niet wilt dat een gebruiker wijzigt. Parameters worden gebruikt voor waarden die een gebruiker moet opgeven bij het implementeren van de sjabloon. De voorbeeldsjabloon is geconfigureerd voor het scenario dat in dit artikel wordt beschreven. U dit aanpassen aan de behoeften van uw omgeving.

De voorbeeldsjabloon die in dit artikel wordt gebruikt, bevat de volgende variabelen en parameters:

| Parameter / Variabele | Opmerkingen |
| --- | --- |
| adminUsername |Geef de naam op van het beheerdersaccount waarmee u zich aanmeldt bij de virtuele machines. |
| adminPassword |Geef het wachtwoord op voor het beheerdersaccount waarmee u zich aanmeldt bij de virtuele machines. |
| dnsNameforIPv4LbIP |Geef de DNS-hostnaam op die u wilt toewijzen als de openbare naam van de load balancer. Deze naam wordt opgelost in het openbare IPv4-adres van de load balancer. De naam moet kleine letters zijn en overeenkomen met de regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Geef de DNS-hostnaam op die u wilt toewijzen als de openbare naam van de load balancer. Deze naam wordt opgelost in het openbare IPv6-adres van de load balancer. De naam moet kleine letters zijn en overeenkomen met de regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. Dit kan dezelfde naam hebben als het IPv4-adres. Wanneer een client een DNS-query voor deze naam verzendt, retourneert Azure zowel de A- als de AAAA-records wanneer de naam wordt gedeeld. |
| vmNameVoorvoegsel |Geef het voorvoegsel vm-naam op. De sjabloon voegt een getal (0, 1, enz.) toe aan de naam wanneer de VM's worden gemaakt. |
| nicNamePrefix |Geef het voorvoegsel van de netwerkinterface-naam op. De sjabloon voegt een getal (0, 1, enz.) toe aan de naam wanneer de netwerkinterfaces worden gemaakt. |
| storageAccountName |Voer de naam in van een bestaand opslagaccount of geef de naam op van een nieuw account dat door de sjabloon moet worden gemaakt. |
| beschikbaarheidSetName |Voer vervolgens de naam in van de beschikbaarheidsset die met de VM's moet worden gebruikt |
| addressPrefix |Het adresvoorvoegsel dat wordt gebruikt om het adresbereik van het virtuele netwerk te definiëren |
| subnetNaam |De naam van het subnet in gemaakt voor de VNet |
| subnetPrefix |Het adresvoorvoegsel dat wordt gebruikt om het adresbereik van het subnet te definiëren |
| vnetName |Geef de naam op voor het VNet dat door de VM's wordt gebruikt. |
| ipv4PrivateIPAddressType |De toewijzingsmethode die wordt gebruikt voor het privé-IP-adres (Statisch of Dynamisch) |
| ipv6PrivateIPAddressType |De toewijzingsmethode die wordt gebruikt voor het privé-IP-adres (Dynamic). IPv6 ondersteunt alleen dynamische toewijzing. |
| aantalOfInstances |Het aantal load balanced instances dat door de sjabloon wordt geïmplementeerd |
| ipv4PublicIPAddressName |Geef de DNS-naam op die u wilt gebruiken om te communiceren met het openbare IPv4-adres van de load balancer. |
| ipv4PublicIPAddressType |De toewijzingsmethode die wordt gebruikt voor het openbare IP-adres (Statisch of Dynamisch) |
| Ipv6PublicIPAddressName |Geef de DNS-naam op die u wilt gebruiken om te communiceren met het openbare IPv6-adres van de load balancer. |
| ipv6PublicIPAddressType |De toewijzingsmethode die wordt gebruikt voor het openbare IP-adres (Dynamic). IPv6 ondersteunt alleen dynamische toewijzing. |
| lbName |Geef de naam van de load balancer op. Deze naam wordt weergegeven in de portal of wordt gebruikt wanneer u ernaar verwijst met een opdracht CLI of PowerShell. |

De overige variabelen in de sjabloon bevatten afgeleide waarden die worden toegewezen wanneer Azure de resources maakt. Verander deze variabelen niet.

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)voor de syntaxis en eigenschappen van json van een load balancer in een sjabloon .
