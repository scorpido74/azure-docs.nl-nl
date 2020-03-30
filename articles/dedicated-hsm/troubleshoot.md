---
title: Wat is Toegewezen HSM? - Azure Toegewezen HSM | Microsoft Docs
description: Overzicht van Azure Toegewezen HSM, welke mogelijkheden biedt voor sleutelopslag binnen Azure die voldoet aan de FIPS 140-2 Niveau 3-certificering
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ad3c9d702384b8a32a9d4f0c8aebe44de4bb526e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80337174"
---
# <a name="troubleshooting"></a>Problemen oplossen

De Azure Dedicated HSM-service heeft twee verschillende facetten. Ten eerste de registratie en implementatie in Azure van de HSM-apparaten met hun onderliggende netwerkcomponenten. Ten tweede, de configuratie van de HSM-apparaten ter voorbereiding op gebruik/integratie met een bepaalde werkbelasting of toepassing. Hoewel de Thales Luna Network HSM-apparaten in Azure hetzelfde zijn als u rechtstreeks bij Thales zou kopen, zorgt het feit dat ze een bron in Azure zijn, voor een aantal unieke overwegingen. Deze overwegingen en eventuele daaruit voortvloeiende inzichten op het oplossen van problemen of best practices worden hier gedocumenteerd om een hoge zichtbaarheid en toegang tot kritieke informatie te garanderen. Zodra de service in gebruik is, is definitieve informatie beschikbaar via ondersteuningsverzoeken rechtstreeks aan Microsoft of Thales. 

> [!NOTE]
> Opgemerkt moet worden dat voordat u een configuratie uitvoert op een nieuw geïmplementeerd HSM-apparaat, het moet worden bijgewerkt met relevante patches. Een specifieke vereiste patch is [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) in Thales support portal die een reboot hang probleem adressen.

## <a name="hsm-registration"></a>HSM-registratie

Dedicated HSM is niet vrij beschikbaar voor gebruik omdat het hardwarebronnen in de cloud levert en daarom een kostbare bron is die moet worden beschermd. We gebruiken daarom een whitelisting HSMrequest@microsoft.comproces via e-mail met behulp van . 

### <a name="getting-access-to-dedicated-hsm"></a>Toegang krijgen tot Dedicated HSM

Als u denkt dat Dedicated HSM voldoet HSMrequest@microsoft.com aan uw belangrijkste opslagvereisten, e-mail dan om toegang aan te vragen. Geef een overzicht van uw toepassing, de regio's die u wilt hsm's en het volume van hsm's die u zoekt. Als u samenwerkt met een Microsoft-vertegenwoordiger, zoals een Account Executive of Cloud Solution Architect, neemt u deze op in elk verzoek.

## <a name="hsm-provisioning"></a>HSM-voorziening

Het inrichten van een HSM-apparaat in Azure kan via CLI of PowerShell. Bij registratie voor de service wordt een voorbeeld-ARM-sjabloon verstrekt en wordt er hulp gegeven voor de eerste aanpassing. 

### <a name="hsm-deployment-failure-information"></a>Gegevens over hsm-implementatiefouten

Dedicated HSM ondersteunt CLI en PowerShell voor implementatie, zodat foutinformatie op basis van portalen beperkt en niet verbose is. Betere informatie kan worden gevonden met behulp van de Resource Explorer. De portal startpagina heeft een pictogram voor deze en meer gedetailleerde foutinformatie is beschikbaar. Deze informatie helpt veel als deze wordt geplakt bij het maken van een ondersteuningsaanvraag met betrekking tot implementatieproblemen.

![Foutgegevens](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>HSM Subnet Delegatie
De belangrijkste reden voor implementatiefouten is het vergeten om de juiste delegatie in te stellen voor het door de klant gedefinieerde subnet waarop de HSM's zullen worden ingericht. Het instellen van die delegatie maakt deel uit van de vereisten voor VNet en subnet voor implementatie en meer details vindt u in de zelfstudies.

![Subnetdelegatie](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>HSM-implementatieracevoorwaarde

De standaard ARM-sjabloon voor implementatie heeft hsm- en ExpressRoute-gatewaygerelateerde bronnen. Netwerkbronnen zijn afhankelijk van een succesvolle HSM-implementatie en timing kan cruciaal zijn.  Af en toe hebben we implementatiefouten gezien in verband met afhankelijkheidsproblemen en het opnieuw uitvoeren van de implementatie lost het probleem vaak op. Zo niet, dan is het vaak succesvol om resources te verwijderen en vervolgens opnieuw te implementeren. Nadat u dit hebt geprobeerd en nog steeds een probleem hebt gevonden, dient u een ondersteuningsverzoek in de Azure-portal in de selectie van het probleemtype 'Problemen met configureren van de Azure-installatie'.

### <a name="hsm-deployment-using-terraform"></a>HSM-implementatie met terraform

Een paar klanten hebben Terraform gebruikt als automatiseringsomgeving in plaats van ARM-sjablonen zoals geleverd bij de registratie van deze service. De HSM's kunnen niet op deze manier worden geïmplementeerd, maar de afhankelijke netwerkbronnen wel. Terraform heeft een module om een minimale ARM-sjabloon te noemen die de HSM-implementatie heeft.  In deze situatie moet ervoor worden gezorgd dat netwerkbronnen zoals de vereiste ExpressRoute Gateway volledig worden geïmplementeerd voordat hsm's worden geïmplementeerd. De volgende OPDRACHT CLI kan worden gebruikt om te testen op voltooide implementatie en indien nodig te integreren. Vervang de hoekbeugelplaatshouders voor uw specifieke naamgeving. U moet op zoek naar een resultaat van "provisioningState is Succeeded"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Implementatiefout op basis van quotum
Implementaties kunnen mislukken als u meer dan 2 HSM's per stempel en 4 HSM's per regio overschrijdt. Als u deze situatie wilt voorkomen, moet u ervoor zorgen dat u resources hebt verwijderd uit eerder mislukte implementaties voordat u opnieuw wordt geïmplementeerd. Raadpleeg hieronder het item 'Hoe zie ik HSM's' om de bronnen te controleren. Als u van mening bent dat u dit quotum, dat HSMrequest@microsoft.com in de eerste plaats als waarborg is, moet overschrijden, u een e-mail sturen met details.

### <a name="deployment-failure-based-on-capacity"></a>Implementatiefout op basis van capaciteit
Wanneer een bepaalde stempel of regio vol raakt, dat wil zeggen dat bijna alle gratis HSM's zijn ingericht, kan dit leiden tot implementatiefouten. Elke stempel heeft 11 HSM's beschikbaar voor klanten, wat betekent 22 per regio. Er zijn ook 3 reserveonderdelen en 1 testapparaat in elke stempel. Als u denkt dat u een HSMrequest@microsoft.com limiet hebt bereikt, e-mail dan voor informatie over het vulniveau van specifieke stempels.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Hoe zie ik HSM's wanneer deze zijn ingericht?
Omdat Dedicated HSM een service op de witte lijst is, wordt het beschouwd als een "verborgen type" in de Azure-portal. Als u de HSM-bronnen wilt zien, moet u het selectievakje Verborgen typen weergeven inschakelt, zoals hieronder weergegeven. De NIC-bron volgt altijd de HSM en is een goede plek om het IP-adres van de HSM te achterhalen voordat u SSH gebruikt om verbinding te maken.

![Subnetdelegatie](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Netwerkbronnen

De implementatie van Dedicated HSM heeft een afhankelijkheid van netwerkbronnen en een aantal daaruit voortvloeiende beperkingen om rekening mee te houden.

### <a name="provisioning-expressroute"></a>Inrichting ExpressRoute

Dedicated HSM gebruikt ExpressRoute Gateway als een "tunnel" voor communicatie tussen de particuliere IP-adresruimte van de klant en de fysieke HSM in een Azure-datacenter.  Aangezien er een beperking is van één gateway per Vnet, moeten klanten die via ExpressRoute verbinding maken met hun on-premises resources, een andere Vnet gebruiken voor die verbinding.  

### <a name="hsm-private-ip-address"></a>HSM Privé IP-adres

De voorbeeldsjablonen voor Dedicated HSM gaan ervan uit dat het HSM-IP automatisch uit een bepaald subnetbereik wordt gehaald. U een expliciet IP-adres voor de HSM opgeven via een kenmerk NetworkInterfaces in de ARM-sjabloon. 

![Subnetdelegatie](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM-initialisatie

Initialisatie bereidt een nieuwe HSM voor gebruik, of een bestaande HSM voor hergebruik. Initialisatie van de HSM moet voltooid zijn voordat u objecten genereren of opslaan, clients in staat stellen verbinding te maken of cryptografische bewerkingen uit te voeren.

### <a name="lost-credentials"></a>Verloren referenties

Verlies van het Shell-beheerderswachtwoord zal leiden tot verlies van HSM-sleutelmateriaal. Er moet een ondersteuningsverzoek worden ingediend om de HSM opnieuw in te stellen.
Wanneer u de HSM initialiseert, worden referenties veilig opgeslagen. Shell- en HSM-referenties moeten worden bewaard in overeenstemming met het beleid van uw bedrijf.

### <a name="failed-logins"></a>Mislukte aanmeldingen

Het verstrekken van onjuiste referenties aan HSM's kan destructieve gevolgen hebben. Hieronder volgen standaardgedragingen voor HSM-rollen.

| Rol | Drempelwaarde (aantal pogingen) | Gevolg van te veel slechte inlogpogingen | Herstel |
|--|--|--|--|
| HSM SO | 3 |  HSM is gezeroiseerd (alle identiteiten van HSM-objecten en alle partities zijn verdwenen)  |  HSM moet opnieuw worden geïnitialiseerd. Inhoud kan worden hersteld vanuit back-up(s). | 
| Partitie SO | 10 |  Partitie is gezeroiseerd. |  Partitie moet opnieuw worden geïnitialiseerd. Inhoud kan worden hersteld vanuit back-up. |  
| Controleren | 10 | Lockout | Automatisch ontgrendeld na 10 minuten. |  
| Crypto-officier | 10 (kan worden verlaagd) | Als HSM-beleid 15: Zo instellen van partitiepincode is ingesteld op 1 (ingeschakeld), zijn de CO- en CU-rollen vergrendeld.<br>Als HSM-beleid 15: Zo instellen van partitiepincode is ingesteld op 0 (uitgeschakeld), zijn de CO- en CU-rollen permanent vergrendeld en zijn de inhoud van de partitie niet langer toegankelijk. Dit is de standaardinstelling. | De CO-rol moet worden ontgrendeld en de `role resetpw -name co`referentie-reset door de partitie SO, met behulp van .<br>De partitie moet opnieuw worden geïnitialiseerd en belangrijk materiaal moet worden hersteld vanaf een back-upapparaat. |  

## <a name="hsm-configuration"></a>HSM-configuratie 

De volgende items zijn situaties waarin configuratiefouten vaak voorkomen of een impact hebben die het waard is om uit te roepen:

### <a name="hsm-documentation-and-software"></a>HSM Documentatie en Software
Software en documentatie voor de Thales SafeNet Luna 7 HSM-apparaten is niet beschikbaar bij Microsoft en moet rechtstreeks worden gedownload van Thales. Registratie is vereist met behulp van de Thales Customer ID die tijdens het registratieproces is ontvangen. De apparaten, zoals geleverd door Microsoft, hebben softwareversie 7.2 en firmwareversie 7.0.3. Begin 2020 maakte Thales documentatie openbaar en deze is [hier](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)te vinden.  

### <a name="hsm-networking-configuration"></a>HSM-netwerkconfiguratie

Wees voorzichtig bij het configureren van de netwerken binnen de HSM.  De HSM heeft een verbinding via de ExpressRoute Gateway van een klant prive IP-adres ruimte rechtstreeks naar de HSM.  Dit communicatiekanaal is alleen voor klantcommunicatie en Microsoft heeft geen toegang. Als de HSM zodanig is geconfigureerd dat dit netwerkpad wordt beïnvloed, betekent dit dat alle communicatie met de HSM wordt verwijderd.  In deze situatie is de enige optie om een Microsoft-ondersteuningsverzoek via de Azure-portal in te stellen om het apparaat te laten resetten. Met deze resetprocedure wordt de HSM teruggezet naar de oorspronkelijke status en gaat alle configuratie en sleutelmateriaal verloren.  De configuratie moet opnieuw worden gemaakt en wanneer het apparaat lid wordt van de HA-groep, wordt het sleutelmateriaal gerepliceerd.  

### <a name="hsm-device-reboot"></a>HSM-apparaat opnieuw opstarten

Voor sommige configuratiewijzigingen moet de HSM worden gefietst of opnieuw opgestart. Microsoft testen van de HSM in Azure vastgesteld dat in sommige gevallen de reboot zou kunnen hangen. De implicatie is dat een ondersteuningsaanvraag moet worden gemaakt in de Azure-portal met het verzoek om een harde herstart en dat kan tot 48 uur duren, aangezien het een handmatig proces is in een Azure-datacenter.  Om deze situatie te voorkomen, moet u ervoor zorgen dat u de rebootpatch die rechtstreeks bij Thales beschikbaar is, hebt geïmplementeerd. Raadpleeg [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) in de Thales Luna Network HSM 7.2 Downloads voor een aanbevolen patch voor een reboot hang probleem (Let op: je moet je hebben geregistreerd in de Thales support portal om te downloaden).

### <a name="ntls-certificates-out-of-sync"></a>NTLS-certificaten niet gesynchroniseerd
Een client kan de verbinding met een HSM verliezen wanneer een certificaat verloopt of is overschreven via configuratie-updates. De clientconfiguratie van de certificaatuitwisseling moet opnieuw worden toegepast bij elke HSM.
Voorbeeld NTLS-logboekregistratie met ongeldig certificaat:

> NTLS[8508]: info: 0 : Inkomende verbindingsaanvraag... : 192.168.50.2/59415 NTLS[8508]: Foutbericht van SSLAccept is : fout:14094418:SSL routines:ssl3_read_bytes:tlsv1 onbekende ca NTLS[8508]: Fout tijdens SSL accept ( RC_SSL_ERROR ) NTLS [8508]: info : 0xc0000711 : Geen veilig kanaal met client : 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE NTLS[8508]: info : 0 : NTLS Client "Onbekende hostnaam" Verbindingsinstantie verwijderd : 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Mislukte TCP-communicatie

Communicatie van de Luna Client installatie naar de HSM vereist op een minimale TCP poort 1792. Beschouw dit als alle netwerkconfiguraties worden gewijzigd in de omgeving.

### <a name="failed-ha-group-member-doesnt-recover"></a>Mislukt HA-groepslid herstelt niet

Als een mislukt HA-groepslid niet herstelt, moet het handmatig worden hersteld van de Luna-client met behulp van de opdracht hagroup herstellen.
Het is noodzakelijk om een aantal opnieuw proberen te configureren voor een HA-groep om automatisch herstellen in te schakelen. Standaard zal een HA-groep niet proberen om een HA-lid terug te krijgen in de groep wanneer deze herstelt.

### <a name="ha-group-doesnt-sync"></a>HA-groep synchroniseert niet

In het geval dat lidpartities niet hetzelfde kloondomein hebben, wordt in de opdracht ha synchroniseren het volgende weergegeven: Waarschuwing: Synchroniseren kan mislukken.  De leden in sleuf 0 en slot 1 hebben conflicterende instellingen voor privésleutelklonen.
Een nieuwe partitie met het juiste kloondomein moet worden toegevoegd aan de HA-groep, gevolgd door het verwijderen van de onjuist geconfigureerde partitie.

## <a name="hsm-deprovisioning"></a>HSM-deprovisioning 

Alleen wanneer volledig klaar met een HSM kan worden gedeprovisioneerd en dan Microsoft zal resetten en terug te keren naar een gratis zwembad. 

### <a name="how-to-delete-an-hsm-resource"></a>Een HSM-bron verwijderen

De Azure-bron voor een HSM kan niet worden verwijderd, tenzij de HSM een 'zeroized'-status heeft.  Daarom moet al het sleutelmateriaal zijn verwijderd voordat u probeert het als bron te verwijderen. De snelste manier om zeroize is om de HSM admin wachtwoord verkeerd te krijgen 3 keer (let op: dit verwijst naar de HSM admin en niet toestel niveau admin). De Luna shell `hsm -factoryreset` heeft wel een commando dat zeroizes, maar het kan alleen worden uitgevoerd via console op de seriële poort en klanten hebben geen toegang tot deze.

## <a name="next-steps"></a>Volgende stappen

Dit artikel heeft inzicht gegeven in gebieden in de levenscyclus van hsm-implementatie die problemen kunnen hebben of het oplossen van problemen of zorgvuldige overweging vereisen. Hopelijk helpt dit artikel u onnodige vertragingen en frustratie te voorkomen, en als u relevante toevoegingen of wijzigingen hebt, dien dan een ondersteuningsverzoek in bij Microsoft en laat het ons weten. 
