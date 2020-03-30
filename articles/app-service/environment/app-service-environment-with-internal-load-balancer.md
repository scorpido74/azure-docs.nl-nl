---
title: Een ILB ASE v1 maken
description: Een ASE maken en gebruiken met een ILB. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0c03905017629e28e41cce2adaa65eac347b8185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294722"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Een interne load balancer gebruiken met een app-serviceomgeving

> [!NOTE] 
> Dit artikel gaat over de App Service Environment v1. Er is een nieuwere versie van de App Service-omgeving die gemakkelijker te gebruiken is en draait op een krachtigere infrastructuur. Voor meer informatie over de nieuwe versie start u met de [inleiding tot de app-serviceomgeving.](intro.md)
>

De ASE-functie (App Service Environment Environment) is een Premium-serviceoptie van Azure App Service die een verbeterde configuratiemogelijkheid biedt die niet beschikbaar is in de stempels met meerdere tenants. De ASE-functie implementeert in wezen de Azure App Service in uw Azure Virtual Network (VNet). Lees de documentatie [Wat is een app-serviceomgeving][WhatisASE] om meer inzicht te krijgen in de mogelijkheden die appserviceomgeving biedt. Als u de voordelen van werken in een VNet niet kent, leest u de [veelgestelde vragen over azure virtual network.][virtualnetwork] 

## <a name="overview"></a>Overzicht
Een ASE kan worden geïmplementeerd met een internettoegankelijk eindpunt of met een IP-adres in uw VNet. Om het IP-adres in te stellen op een VNet-adres moet u uw ASE implementeren met een Internal Load Balancer (ILB). Wanneer uw ASE is geconfigureerd met een ILB, geeft u het:

* uw eigen domein of subdomein. Om het u gemakkelijk te maken, gaat dit document uit van subdomein, maar u het hoe dan ook configureren. 
* het certificaat dat wordt gebruikt voor HTTPS
* DNS-beheer voor uw subdomein. 

Hierna kunt u dingen doen zoals:

* intranettoepassingen, zoals line-of-business-applicaties, veilig hosten in de cloud die u via een site naar site of ExpressRoute VPN verkrijgt
* host-apps in de cloud die niet worden vermeld in openbare DNS-servers
* internetgeïsoleerde backend-apps maken waarmee uw front-end apps veilig kunnen integreren

#### <a name="disabled-functionality"></a>Uitgeschakelde functionaliteit
Er zijn een aantal dingen die je niet doen bij het gebruik van een ILB ASE. Deze dingen zijn onder andere:

* IPSSL gebruiken
* IP-adressen toewijzen aan specifieke apps
* het kopen en gebruiken van een certificaat met een app via de portal. U natuurlijk nog steeds certificaten rechtstreeks verkrijgen bij een certificaatautoriteit en deze gebruiken met uw apps, maar niet via de Azure-portal.

## <a name="creating-an-ilb-ase"></a>Een ILB ASE maken
Het maken van een ILB ASE is niet veel anders dan het maken van een ASE normaal. Zie [Een app-serviceomgeving maken][HowtoCreateASE]voor een diepere discussie over het maken van een ASE. Het proces voor het maken van een ILB ASE is hetzelfde tussen het maken van een VNet tijdens het maken van ASE of het selecteren van een reeds bestaand VNet. Ga als volgt te werk om een ILB AS-omgeving te maken: 

1. Selecteer in de Azure-portal de optie **Een bron maken > Web + Mobile-> App Service-omgeving**.
2. Selecteer uw abonnement.
3. Selecteer of maak een resourcegroep.
4. Selecteer of maak een VNet.
5. Maak een subnet als u een VNet selecteert.
6. Selecteer **Virtueel netwerk/locatie-> VNet-configuratie** en stel het VIP-type in op Intern.
7. Geef een subdomeinnaam op (deze naam is het subdomein dat wordt gebruikt voor de apps die in deze ASE zijn gemaakt).
8. Selecteer **OK** en maak **vervolgens .**

![][1]

In het deelvenster Virtueel netwerk is er een VNet-configuratieoptie waarmee u kiezen tussen een externe VIP of interne VIP. De standaard is Extern. Als u deze hebt ingesteld op Extern, maakt uw ASE gebruik van een vip die toegankelijk is via internet. Als u Intern selecteert, is de AS-omgeving geconfigureerd met een ILB op een IP-adres binnen uw VNet. 

Nadat u Intern hebt geselecteerd, wordt de mogelijkheid om meer IP-adressen aan uw ASE toe te voegen verwijderd en in plaats daarvan moet u het subdomein van de ASE opgeven. In een ASE met een externe VIP wordt de naam van de ASE gebruikt in het subdomein voor apps die in die ASE zijn gemaakt. Als uw ASE ***contosotest*** wordt genoemd en uw app in die ASE ***mytest***wordt genoemd, is het subdomein van het formaat ***contosotest.p.azurewebsites.net*** en is de URL voor die app ***mytest.contosotest.p.azurewebsites.net***. Als u het VIP-type instelt op Intern, wordt uw ASE-naam niet gebruikt in het subdomein voor de ASE. U geeft het subdomein expliciet op. Als uw subdomein ***contoso.corp.net*** is en u een app hebt gemaakt in die ASE-timereporting, wordt de URL voor die app ***timereporting.contoso.corp.net***. ***timereporting***

## <a name="apps-in-an-ilb-ase"></a>Apps in een ILB ASE
Het maken van een app in een ILB ASE is hetzelfde als normaal een app maken in een ASE. 

1. Selecteer in de Azure-portal de optie **Een bron maken > Web + Mobile -> Web-** of **Mobile-** of **API-app**.
2. Voer de naam van de app in.
3. Selecteer uw abonnement.
4. Selecteer of maak een resourcegroep.
5. Selecteer of maak een App Service Plan(ASP). Als u een nieuwe ASP maakt, selecteert u uw ASE als locatie en selecteert u de werknemersgroep waarin u wilt dat uw ASP wordt gemaakt. Wanneer u de ASP maakt, selecteert u uw ASE als locatie en de werknemersgroep. Wanneer u de naam van de app opgeeft, ziet u dat het subdomein onder uw app-naam wordt vervangen door het subdomein voor uw ASE. 
6. Selecteer **Maken**. Schakel het selectievakje **Vastmaken aan dashboard** in als u wilt dat de app op uw dashboard wordt weergegeven. 

![][2]

Onder de naam van de app wordt de subdomeinnaam bijgewerkt om het subdomein van uw ASE weer te geven. 

## <a name="post-ilb-ase-creation-validation"></a>Validatie van ILB ASE-creatie plaatsen
Een ILB AS-omgeving verschilt iets van de niet-ILB AS-omgeving. Zoals reeds opgemerkt moet je je eigen DNS beheren en moet je ook je eigen certificaat voor HTTPS-verbindingen verstrekken. 

Nadat u uw ASE hebt gemaakt, zult u merken dat het subdomein het subdomein toont dat u hebt opgegeven en dat er een nieuw item in het **menu Instelling staat** dat **ILB-certificaat wordt genoemd.** De ASE is gemaakt met een zelfondertekend certificaat dat het eenvoudiger maakt om HTTPS te testen. De portal vertelt u dat u uw eigen certificaat voor HTTPS moet verstrekken, maar dit is om u aan te moedigen om een certificaat te hebben dat bij uw subdomein hoort. 

![][3]

Als u gewoon dingen uitprobeert en niet weet hoe u een certificaat moet maken, u de IIS MMC-consoletoepassing gebruiken om een zelfondertekend certificaat te maken. Zodra het is gemaakt, u het exporteren als een .pfx-bestand en vervolgens uploaden in de ILB-certificaatgebruikersinterface. Wanneer u toegang krijgt tot een site die is beveiligd met een zelfondertekend certificaat, geeft uw browser u een waarschuwing dat de site die u opent niet veilig is vanwege het onvermogen om het certificaat te valideren. Als u die waarschuwing wilt vermijden, hebt u een correct ondertekend certificaat nodig dat overeenkomt met uw subdomein en een vertrouwensketen heeft die door uw browser wordt herkend.

![][6]

Als u de stroom met uw eigen certificaten wilt proberen en zowel HTTP- als HTTPS-toegang tot uw ASE wilt testen:

1. Ga naar ASE UI nadat ASE ASE is gemaakt **> Instellingen > ILB-certificaten**.
2. Stel het ILB-certificaat in door het PFX-bestand van certificaten te selecteren en een wachtwoord op te geven. Deze stap duurt even om te verwerken en het bericht dat een schaalbewerking wordt uitgevoerd, wordt weergegeven.
3. Zoek het ILB-adres op voor uw**ASE (ASE-> Properties -> Virtueel IP-adres).**
4. Maak een web-app in ASE na het maken. 
5. Maak een VM als u er geen in dat VNET hebt (niet in hetzelfde subnet als de ASE of dingen breken).
6. Stel DNS in voor uw subdomein. U een wildcard gebruiken met uw subdomein in uw DNS of als u enkele eenvoudige tests wilt uitvoeren, bewerkt u het hosts-bestand op uw VM om de naam van de web-app in te stellen op VIP IP-adres. Als uw ASE de subdomeinnaam .ilbase.com had en u de mytestapp van de webapp hebt gemaakt zodat deze op mytestapp.ilbase.com zou worden aangepakt, stelt u dat in uw hosts-bestand in. (In Windows bevindt het hosts-bestand zich op C:\Windows\System32\drivers\etc\)
7. Gebruik een browser op die `https://mytestapp.ilbase.com` VM en ga naar (of wat uw web-app naam is met uw subdomein).
8. Gebruik een browser op die virtuele machine en ga naar `https://mytestapp.ilbase.com`. U moet het gebrek aan beveiliging accepteren als u een zelfondertekend certificaat gebruikt. 

Het IP-adres voor uw ILB wordt in uw eigenschappen vermeld als het virtuele IP-adres.

![][4]

## <a name="using-an-ilb-ase"></a>Een ILB ASE gebruiken
#### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Een ILB ASE maakt netwerkisolatie voor uw apps mogelijk. De apps zijn niet toegankelijk of zelfs bekend door het internet. Deze aanpak is uitstekend geschikt voor het hosten van intranetsites, zoals line-of-business applicaties. Wanneer u de toegang nog verder moet beperken, u nog steeds NSG's (Network Security Groups) gebruiken om de toegang op netwerkniveau te beheren. 

Als u NSG's wilt gebruiken om de toegang verder te beperken, moet u ervoor zorgen dat u de communicatie die de ASE nodig heeft om te kunnen werken, niet overtreedt. Hoewel de HTTP/HTTPS-toegang alleen via de ILB wordt gebruikt door de ASE, is de ASE nog steeds afhankelijk van bronnen buiten het VNet. Zie [Binnenkomend verkeer beheren naar een app-serviceomgeving][ControlInbound] en [netwerkconfiguratiegegevens voor app-serviceomgevingen met ExpressRoute][ExpressRoute]om te zien welke netwerktoegang nog nodig is. 

Als u uw NSG's wilt configureren, moet u het IP-adres kennen dat door Azure wordt gebruikt om uw ASE te beheren. Dat IP-adres is ook het uitgaande IP-adres van uw ASE als het internetverzoeken doet. Het uitgaande IP-adres voor uw ASE blijft statisch voor de levensduur van uw ASE. Als u uw ASE verwijdert en opnieuw maakt, krijgt u een nieuw IP-adres. Als u het IP-adres wilt vinden, gaat u naar **Instellingen -> Eigenschappen** en zoekt u het **uitgaande IP-adres**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Algemeen Beheer ILB ASE
Het beheren van een ILB ASE is grotendeels hetzelfde als het normaal beheren van een ASE. U moet uw werknemerspools opschalen om meer ASP-exemplaren te hosten en uw front-endservers opschalen om grotere hoeveelheden HTTP/HTTPS-verkeer te verwerken. Zie [Een app-serviceomgeving configureren][ASEConfig]voor algemene informatie over het beheren van de configuratie van een ASE. 

De extra beheeritems zijn certificaatbeheer en DNS-beheer. U moet het certificaat dat voor HTTPS wordt gebruikt na het maken van ILB ASE verkrijgen en uploaden en het vervangen voordat het verloopt. Omdat Azure eigenaar is van het basisdomein, kan het certificaten voor ASE's leveren met een externe VIP. Aangezien het subdomein dat door een ILB ASE wordt gebruikt van alles kan zijn, moet u uw eigen certificaat voor HTTPS opgeven. 

#### <a name="dns-configuration"></a>DNS-configuratie
Bij het gebruik van een externe VIP wordt de DNS beheerd door Azure. Elke app die is gemaakt in de AS-omgeving wordt automatisch toegevoegd aan Azure DNS, wat een openbaar DNS is. In een ILB AS-omgeving moet u uw eigen DNS beheren. Voor een bepaald subdomein, zoals contoso.corp.net, moet u DNS A-records maken die naar uw ILB-adres wijzen voor:

    * 
    *.scm ftp publiceren 


## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app-serviceomgevingen][WhatisASE] om aan de slag te gaan met app-serviceomgevingen

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
