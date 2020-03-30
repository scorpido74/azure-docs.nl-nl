---
title: Eindpunttypen verkeersbeheerder | Microsoft Documenten
description: In dit artikel worden verschillende typen eindpunten uitgelegd die kunnen worden gebruikt met Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rohink
ms.openlocfilehash: 3d8f899a7899243129d31c2620a51dc764a8e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250931"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-eindpunten

Met Microsoft Azure Traffic Manager u bepalen hoe het netwerkverkeer wordt gedistribueerd naar toepassingsimplementaties die in verschillende datacenters worden uitgevoerd. U configureert elke toepassingsimplementatie als een eindpunt in Traffic Manager. Wanneer Traffic Manager een DNS-aanvraag ontvangt, wordt een beschikbaar eindpunt gekozen om te retourneren in het DNS-antwoord. Verkeersmanager baseert de keuze op de huidige eindpuntstatus en de verkeersrouteringsmethode. Zie [Hoe Verkeersbeheerder werkt](traffic-manager-how-it-works.md)voor meer informatie.

Er zijn drie typen eindpunten die door Traffic Manager worden ondersteund:

* **Azure-eindpunten** worden gebruikt voor services die worden gehost in Azure.
* **Externe eindpunten** worden gebruikt voor IPv4-en IPv6-adressen, voor FQDN's of voor services die buiten Azure worden gehost, on-premises of via een andere hostingprovider.
* Met **Geneste eindpunten** worden Traffic Manager-profielen gecombineerd om flexibelere verkeersrouteringschema's te krijgen om de behoeften van grotere, complexere implementaties te ondersteunen.

Er is geen beperking voor de manier waarop eindpunten van verschillende typen worden gecombineerd in één Traffic Manager-profiel. Elk profiel kan elke combinatie van eindpunttypen bevatten.

In de volgende secties wordt elk eindpunttype dieper beschreven.

## <a name="azure-endpoints"></a>Azure-eindpunten

Azure-eindpunten worden gebruikt voor Azure-services in Traffic Manager. De volgende Azure-brontypen worden ondersteund:

* PaaS-cloudservices.
* Web-apps
* Web-app-sleuven
* PublicIPAddress-resources (die rechtstreeks of via een Azure Load Balancer kunnen worden verbonden met VM's). Het publicIpAddress moet een DNS-naam hebben toegewezen om te worden gebruikt in een Traffic Manager-profiel.

PublicIPAddress-resources zijn Azure Resource Manager-bronnen. Ze bestaan niet in het klassieke implementatiemodel. Ze worden dus alleen ondersteund in de Azure Resource Manager-ervaringen van Traffic Manager. De andere eindpunttypen worden ondersteund via zowel Resource Manager als het klassieke implementatiemodel.

Bij het gebruik van Azure-eindpunten detecteert Traffic Manager wanneer een web-app is gestopt en gestart. Deze status wordt weergegeven in de eindpuntstatus. Zie [Eindpuntbewaking van verkeersbeheer](traffic-manager-monitoring.md#endpoint-and-profile-status) voor meer informatie. Wanneer de onderliggende service is gestopt, voert Traffic Manager geen statuscontroles voor eindpunten uit of leidt u het verkeer naar het eindpunt. Er vinden geen factureringsgebeurtenissen voor traffic manager plaats voor de gestopte instantie. Wanneer de service opnieuw wordt opgestart, wordt de facturering hervat en komt het eindpunt in aanmerking voor verkeer. Deze detectie is niet van toepassing op PublicIpAddress-eindpunten.

## <a name="external-endpoints"></a>Externe eindpunten

Externe eindpunten worden gebruikt voor IPv4/IPv6-adressen, FQDN's of voor services buiten Azure. Met het gebruik van IPv4/IPv6-adreseindpunten kan verkeersbeheerder de status van eindpunten controleren zonder dat hiervoor een DNS-naam nodig is. Als gevolg hiervan kan Traffic Manager reageren op query's met A/AAAA-records bij het retourneren van dat eindpunt in een antwoord. Services buiten Azure kunnen een service bevatten die on-premises of met een andere provider wordt gehost. Externe eindpunten kunnen afzonderlijk worden gebruikt of gecombineerd met Azure-eindpunten in hetzelfde Traffic Manager-profiel, behalve voor eindpunten die zijn opgegeven als IPv4- of IPv6-adressen die alleen externe eindpunten kunnen zijn. Door Azure-eindpunten te combineren met externe eindpunten kunnen verschillende scenario's worden ingeschakeld:

* Zorg voor meer redundantie voor een bestaande on-premises toepassing in een actief actief of actief-passief failovermodel met Azure. 
* Leid verkeer naar eindpunten waaraan geen DNS-naam is gekoppeld. Verlaag bovendien de algehele latenuplatentie van DNS door de noodzaak om een tweede DNS-query uit te voeren te verwijderen om een IP-adres van een DNS-naam terug te krijgen.
* Verminder de latentie van toepassingen voor gebruikers over de hele wereld en breid een bestaande on-premises toepassing uit naar extra geografische locaties in Azure. Zie [Traffic Manager 'Performance' traffic routing](traffic-manager-routing-methods.md#performance)voor meer informatie.
* Zorg voor extra capaciteit voor een bestaande on-premises toepassing, continu of als een 'burst-to-cloud'-oplossing om te voldoen aan een piek in de vraag met Azure.

In bepaalde gevallen is het handig om externe eindpunten te gebruiken om naar Azure-services te verwijzen (zie bijvoorbeeld de [veelgestelde vragen).](traffic-manager-faqs.md#traffic-manager-endpoints) In dit geval worden statuscontroles gefactureerd op het Azure-eindpuntentarief, niet met het tarief voor externe eindpunten. In tegenstelling tot Azure-eindpunten gaat de facturering van de statuscontrole echter door totdat u het eindpunt in Traffic Manager uitschakelt of verwijdert.

## <a name="nested-endpoints"></a>Geneste eindpunten

Geneste eindpunten combineren meerdere Traffic Manager-profielen om flexibele verkeersrouteringsschema's te maken en de behoeften van grotere, complexe implementaties te ondersteunen. Met Geneste eindpunten wordt een 'onderliggend' profiel toegevoegd als eindpunt voor een 'bovenliggend' profiel. Zowel de onderliggende als de bovenliggende profielen kunnen andere eindpunten van elk type bevatten, waaronder andere geneste profielen. Zie [geneste traffic manager-profielen](traffic-manager-nested-profiles.md)voor meer informatie .

## <a name="web-apps-as-endpoints"></a>Web Apps als eindpunten

Er zijn enkele aanvullende overwegingen van toepassing bij het configureren van Web Apps als eindpunten in Traffic Manager:

1. Alleen Web Apps op de 'Standaard' SKU of hoger komen in aanmerking voor gebruik met Traffic Manager. Pogingen om een web-app van een lagere SKU toe te voegen mislukken. Als u de SKU van een bestaande web-app downgraden, wordt verkeersbeheer niet langer verzonden naar die web-app. Zie de [app-serviceplannen](https://azure.microsoft.com/pricing/details/app-service/plans/) voor meer informatie over ondersteunde abonnementen
2. Wanneer een eindpunt een HTTP-aanvraag ontvangt, gebruikt het de koptekst 'host' in het verzoek om te bepalen welke web-app de aanvraag moet onderhouden. De hostheader bevat de DNS-naam die wordt gebruikt om het verzoek te starten, bijvoorbeeld 'contosoapp.azurewebsites.net'. Als u een andere DNS-naam met uw web-app wilt gebruiken, moet de DNS-naam worden geregistreerd als een aangepaste domeinnaam voor de App. Wanneer u een eindpunt van een Web App toevoegt als Azure-eindpunt, wordt de DNS-naam van het Traffic Manager-profiel automatisch geregistreerd voor de App. Deze registratie wordt automatisch verwijderd wanneer het eindpunt wordt verwijderd.
3. Elk Traffic Manager-profiel kan ten hoogste één Web App-eindpunt hebben uit elke Azure-regio. Als u voor deze beperking wilt werken, u een web-app configureren als een extern eindpunt. Zie voor meer informatie de [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Eindpunten in- en uitschakelen

Het uitschakelen van een eindpunt in Traffic Manager kan handig zijn om verkeer tijdelijk te verwijderen uit een eindpunt dat in de onderhoudsmodus staat of opnieuw wordt geïmplementeerd. Zodra het eindpunt weer wordt uitgevoerd, kan het opnieuw worden ingeschakeld.

Eindpunten kunnen worden ingeschakeld en uitgeschakeld via de Traffic Manager-portal, PowerShell, CLI of REST API.

> [!NOTE]
> Het uitschakelen van een Azure-eindpunt heeft niets te maken met de implementatiestatus in Azure. Een Azure-service (zoals een VM of web-app blijft actief en kan verkeer ontvangen, zelfs wanneer deze is uitgeschakeld in Traffic Manager. Verkeer kan rechtstreeks worden gericht aan de service-instantie in plaats van via de DNS-naam van het Traffic Manager-profiel. Zie voor meer informatie [hoe Traffic Manager werkt.](traffic-manager-how-it-works.md)

De huidige geschiktheid van elk eindpunt voor het ontvangen van verkeer is afhankelijk van de volgende factoren:

* De profielstatus (ingeschakeld/uitgeschakeld)
* De status eindpunt (ingeschakeld/uitgeschakeld)
* De resultaten van de gezondheidscontroles voor dat eindpunt

Zie [Eindpuntbewaking van verkeersbeheer voor](traffic-manager-monitoring.md#endpoint-and-profile-status)meer informatie .

> [!NOTE]
> Aangezien Traffic Manager op DNS-niveau werkt, kan het geen invloed uitoefenen op bestaande verbindingen met een eindpunt. Wanneer een eindpunt niet beschikbaar is, stuurt Traffic Manager nieuwe verbindingen naar een ander beschikbaar eindpunt. De host achter het uitgeschakelde of ongezonde eindpunt kan echter verkeer blijven ontvangen via bestaande verbindingen totdat deze sessies zijn beëindigd. Toepassingen moeten de sessieduur beperken zodat verkeer kan worden verwijderd uit bestaande verbindingen.

Als alle eindpunten in een profiel zijn uitgeschakeld of als het profiel zelf is uitgeschakeld, stuurt Traffic Manager een 'NXDOMAIN'-antwoord op een nieuwe DNS-query.

## <a name="faqs"></a>Veelgestelde vragen

* [Kan ik Traffic Manager gebruiken met eindpunten van meerdere abonnementen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Kan ik Traffic Manager gebruiken met 'Staging'-sleuven van Cloud Service?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Ondersteunt Traffic Manager IPv6-eindpunten?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Kan ik Traffic Manager gebruiken met meer dan één web-app in dezelfde regio?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Hoe verplaats ik de Azure-eindpunten van mijn Traffic Manager-profiel naar een andere brongroep?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [hoe Traffic Manager werkt.](traffic-manager-how-it-works.md)
* Meer informatie over eindpuntbewaking van Traffic Manager [en automatische failover](traffic-manager-monitoring.md).
* Meer informatie over [verkeersrouteringsmethoden voor traffic](traffic-manager-routing-methods.md)manager .
