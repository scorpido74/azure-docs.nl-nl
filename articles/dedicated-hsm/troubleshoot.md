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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80337174"
---
# <a name="troubleshooting"></a>Problemen oplossen

De toegewezen HSM-service van Azure heeft twee verschillende facetten. Ten eerste, de registratie en implementatie in azure van de HSM-apparaten met de onderliggende netwerk onderdelen. Ten tweede, de configuratie van de HSM-apparaten ter voor bereiding op gebruik/integratie met een bepaalde werk belasting of toepassing. Hoewel de Thales Luna netwerk HSM-apparaten in azure hetzelfde zijn als u rechtstreeks aanschaft vanuit Thales, maakt het feit dat ze een resource zijn in azure enkele unieke overwegingen. Deze overwegingen en eventuele resulterende problemen met inzichten of aanbevolen procedures worden hier gedocumenteerd om te zorgen voor een hoge zicht baarheid en toegang tot essentiële informatie. Zodra de service wordt gebruikt, is definitieve informatie beschikbaar via ondersteunings aanvragen voor micro soft of Thales rechtstreeks. 

> [!NOTE]
> Voordat een configuratie op een pas geïmplementeerd HSM-apparaat wordt uitgevoerd, moet deze worden bijgewerkt met alle relevante patches. Een specifieke vereiste patch is [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) in de Thales-ondersteunings portal, waarmee een probleem met het opnieuw opstarten wordt opgelost.

## <a name="hsm-registration"></a>HSM-registratie

Toegewezen HSM is niet vrij beschikbaar voor gebruik bij het leveren van hardwarebronnen in de Cloud en is daarom een kost bare resource die moet worden beveiligd. We gebruiken daarom een white list-proces via e-mail HSMrequest@microsoft.commet. 

### <a name="getting-access-to-dedicated-hsm"></a>Toegang krijgen tot toegewezen HSM

Als u denkt dat toegewezen HSM voldoet aan de opslag vereisten voor uw HSMrequest@microsoft.com sleutel, wordt u via e-mail om toegang te vragen. Betrek uw toepassing, de regio's die u wilt Hsm's en het volume van Hsm's dat u zoekt. Als u met een vertegenwoordiger van micro soft werkt, bijvoorbeeld een account executive of Cloud Solution architect, neemt u deze op in een aanvraag.

## <a name="hsm-provisioning"></a>HSM-inrichting

Het inrichten van een HSM-apparaat in azure kan worden uitgevoerd via CLI of Power shell. Wanneer u zich registreert voor de service, wordt er een voor beeld van een ARM-sjabloon gegeven en wordt er hulp gegeven voor de eerste aanpassing. 

### <a name="hsm-deployment-failure-information"></a>Fout gegevens voor HSM-implementatie

Toegewezen HSM ondersteunt CLI en Power shell voor implementatie, zodat op de portal gebaseerde fout gegevens beperkt en niet uitgebreid zijn. Meer informatie vindt u in resource Explorer. Op de start pagina van de portal vindt u een pictogram voor deze en meer gedetailleerde informatie over de fout is beschikbaar. Deze informatie helpt veel op het moment dat er een ondersteunings aanvraag wordt gemaakt met betrekking tot implementatie problemen.

![Fout gegevens](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>HSM-subnet delegering
Het aantal redenen voor implementatie fouten neemt af om de juiste delegatie in te stellen voor het door de klant gedefinieerde subnet waarop de Hsm's wordt ingericht. Het instellen van de delegatie maakt deel uit van de vereisten voor het VNet en het subnet voor implementatie. meer informatie vindt u in de zelf studies.

![Subnet delegering](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Race voorwaarde voor HSM-implementatie

De standaard ARM-sjabloon die voor de implementatie wordt gegeven, heeft HSM-en ExpressRoute-gerelateerde resources. Netwerk bronnen zijn afhankelijk van een geslaagde HSM-implementatie en timing kan cruciaal zijn.  In sommige gevallen hebben we implementatie fouten met betrekking tot afhankelijkheids problemen gezien en wordt het probleem vaak opgelost door de implementatie opnieuw uit te voeren. Als dat niet het geval is, kunt u resources verwijderen en vervolgens opnieuw implementeren. Nadat u dit hebt gedaan en nog steeds een probleem hebt gevonden, verhoogt u een ondersteunings aanvraag in het Azure Portal het probleem type ' problemen met het configureren van de installatie van Azure ' te selecteren.

### <a name="hsm-deployment-using-terraform"></a>HSM-implementatie met behulp van terraform

Een aantal klanten hebben terraform als een automatiserings omgeving gebruikt in plaats van ARM-sjablonen die zijn opgegeven bij het registreren voor deze service. De Hsm's kan niet op deze manier worden geïmplementeerd, maar de afhankelijke netwerk bronnen kunnen. Terraform heeft een module voor het aanroepen van een minimale ARM-sjabloon die Jut de HSM-implementatie heeft.  In dit geval moet u er zeker van zijn dat netwerk bronnen, zoals de vereiste ExpressRoute-gateway, volledig zijn geïmplementeerd voordat u Hsm's implementeert. De volgende CLI-opdracht kan worden gebruikt om te testen of de implementatie is voltooid en zo nodig worden geïntegreerd. Vervang de punt haakers voor uw specifieke naamgeving. Zoek naar het resultaat ' provisioningState is voltooid '

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Implementatie fout op basis van quotum
Implementaties kunnen mislukken als u 2 Hsm's per stempel en 4 Hsm's per regio overschrijdt. Om deze situatie te voor komen, moet u ervoor zorgen dat u resources van eerder mislukte implementaties hebt verwijderd voordat u deze opnieuw implementeert. Raadpleeg het item ' Hoe kan ik zie Hsm's ' hieronder om de resources te controleren. Als u van mening bent dat u dit quotum moet overschrijden, dat voornamelijk het geval is, kunt u HSMrequest@microsoft.com een e-mail bericht met details ontvangen.

### <a name="deployment-failure-based-on-capacity"></a>Implementatie fout op basis van capaciteit
Wanneer een bepaalde stempel of regio vol is, dat wil zeggen dat bijna alle gratis Hsm's zijn ingericht, kan dit leiden tot implementatie fouten. Elk stempel heeft 11 Hsm's beschikbaar voor klanten, wat betekent 22 per regio. Er zijn ook drie reserves en één test apparaat in elk stempel. Als u van mening bent dat u een limiet hebt bereikt, HSMrequest@microsoft.com kunt u een e-mail sturen naar informatie over het opvul niveau van specifieke stem pels.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Hoe kan ik raadpleegt u Hsm's bij het inrichten?
Omdat een speciale HSM een white list-service is, wordt deze als een ' verborgen type ' beschouwd in de Azure Portal. Als u de HSM-resources wilt weer geven, schakelt u het selectie vakje verborgen typen weer geven in, zoals hieronder wordt weer gegeven. De NIC-resource volgt altijd de HSM en is een goede plaats om het IP-adres van de HSM te achterhalen voordat SSH wordt gebruikt om verbinding te maken.

![Subnet delegering](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Netwerk bronnen

De implementatie van een toegewezen HSM heeft een afhankelijkheid van netwerk bronnen en enkele beperkingen om rekening mee te houden.

### <a name="provisioning-expressroute"></a>ExpressRoute inrichten

De toegewezen HSM maakt gebruik van de ExpressRoute-gateway als een tunnel voor communicatie tussen de privé-IP-adres ruimte van de klant en de fysieke HSM in een Azure-Data Center.  Als er rekening wordt gehouden met een beperking van één gateway per Vnet, kunnen klanten die verbinding willen met hun on-premises resources via ExpressRoute, een ander Vnet voor die verbinding gebruiken.  

### <a name="hsm-private-ip-address"></a>Persoonlijk IP-adres van HSM

De voorbeeld sjablonen die zijn opgegeven voor de toegewezen HSM, nemen aan dat het HSM-IP-adres automatisch wordt opgehaald uit een bepaald subnet-bereik. U kunt een expliciet IP-adres voor de HSM opgeven via een ' NetworkInterfaces-kenmerk in de ARM-sjabloon. 

![Subnet delegering](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM-initialisatie

Met de initialisatie wordt een nieuwe HSM voor bereid voor gebruik, of een bestaande HSM voor hergebruik. De initialisatie van de HSM moet zijn voltooid voordat u objecten kunt genereren of opslaan, clients toestemming wilt geven om verbinding te maken of cryptografische bewerkingen uit te voeren.

### <a name="lost-credentials"></a>Verloren referenties

Het verlies van het shell beheerders wachtwoord leidt ertoe dat het HSM-sleutel materiaal verloren gaat. Er moet een ondersteunings aanvraag worden gedaan om de HSM opnieuw in te stellen.
Bij het initialiseren van de HSM kunt u de referenties veilig opslaan. De shell-en HSM-referenties moeten worden bewaard in overeenstemming met het beleid van uw bedrijf.

### <a name="failed-logins"></a>Mislukte aanmeldingen

Het opgeven van onjuiste referenties voor Hsm's kan destructieve gevolgen hebben. Hieronder volgen standaard gedragingen voor HSM-rollen.

| Rol | Drempel waarde (aantal pogingen) | Resultaat van te veel mislukte aanmeldings pogingen | Herstel |
|--|--|--|--|
| HSM, SO | 3 |  HSM is nul (alle identiteiten van HSM-objecten en alle partities zijn verwijderd)  |  HSM moet opnieuw worden geïnitialiseerd. Inhoud kan worden teruggezet vanuit een back-up (s). | 
| Partitioneren, zodat | 10 |  Partitie is nul. |  De partitie moet opnieuw worden geïnitialiseerd. Inhoud kan worden hersteld vanuit een back-up. |  
| Controleren | 10 | Vergrendel | Na 10 minuten automatisch ontgrendeld. |  
| Crypto medewerker | 10 (kan afnemen) | Als HSM-beleid 15: inschakelen is ingesteld op het opnieuw instellen van de partitie pincode is in te stellen op 1 (ingeschakeld), de CO-en CU-rollen worden vergrendeld.<br>Als HSM-beleid 15: inschakelen is ingeschakeld voor het opnieuw instellen van de partitie pincode is ingesteld op 0 (uitgeschakeld), worden de CO-en CU-rollen permanent vergrendeld en zijn de partitie-inhoud niet meer toegankelijk. Dit is de standaardinstelling. | De rol CO moet worden ontgrendeld en de referentie wordt opnieuw ingesteld door de partitie, `role resetpw -name co`met behulp van.<br>De partitie moet opnieuw worden geïnitialiseerd en het sleutel materiaal kan worden hersteld vanaf een back-upapparaat. |  

## <a name="hsm-configuration"></a>HSM-configuratie 

De volgende items zijn een situatie waarin configuratie fouten gebruikelijk zijn of een impact hebben die betrouw bare is:

### <a name="hsm-documentation-and-software"></a>HSM-documentatie en-software
Software en documentatie voor de Thales SafeNet Luna 7 HSM-apparaten zijn niet beschikbaar van micro soft en moeten rechtstreeks worden gedownload van Thales. Registratie is vereist met behulp van de Thales-klant-ID die tijdens het registratie proces is ontvangen. De apparaten die door micro soft worden meegeleverd, hebben software versie 7,2 en firmware versie 7.0.3. In 2020 Thales zijn documentatie openbaar gemaakt en deze kunt u [hier](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)vinden.  

### <a name="hsm-networking-configuration"></a>HSM-netwerk configuratie

Wees voorzichtig bij het configureren van het netwerk binnen de HSM.  De HSM heeft een verbinding via de ExpressRoute-gateway van een privé-IP-adres ruimte van een klant rechtstreeks naar de HSM.  Dit communicatie kanaal is alleen voor communicatie met de klant en micro soft heeft geen toegang. Als de HSM zodanig is geconfigureerd dat dit netwerkpad wordt beïnvloed, betekent dit dat alle communicatie met de HSM wordt verwijderd.  In deze situatie is de enige optie een micro soft-ondersteunings aanvraag via de Azure Portal om het apparaat opnieuw in te stellen. Met deze procedure reset wordt de HSM weer ingesteld op de oorspronkelijke status en worden alle configuratie-en sleutel materiaal verloren gegaan.  De configuratie moet opnieuw worden gemaakt en wanneer het apparaat wordt toegevoegd aan de HA-groep, wordt het sleutel materiaal gerepliceerd.  

### <a name="hsm-device-reboot"></a>HSM-apparaat opnieuw opstarten

Voor sommige configuratie wijzigingen moet de HSM worden gerecycled of opnieuw worden opgestart. Door micro soft tests van de HSM in Azure is bepaald dat het opnieuw opstarten van het systeem kan vastlopen. De implicatie is dat er een ondersteunings aanvraag moet worden gemaakt in de Azure Portal het aanvragen van de computer opnieuw op te starten. Dit kan tot 48 uur duren, omdat het een hand matig proces is in een Azure-Data Center.  Om deze situatie te voor komen, moet u ervoor zorgen dat u de patch voor opnieuw opstarten hebt geïmplementeerd die rechtstreeks via Thales beschikbaar is. Raadpleeg [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) in de Thales Luna Network HSM 7,2 down loads voor een aanbevolen patch voor een vastgelopen probleem bij het opnieuw opstarten (Opmerking: u moet zijn geregistreerd in het Thales-ondersteunings portal om te downloaden).

### <a name="ntls-certificates-out-of-sync"></a>NTLS certificaten zijn niet synchroon
Een client kan geen verbinding meer met een HSM wanneer een certificaat verloopt of overschreven is via configuratie-updates. De client configuratie van de certificaat uitwisseling moet met elke HSM opnieuw worden toegepast.
Voor beeld van NTLS-logboek registratie met ongeldig certificaat:

> NTLS [8508]: info: 0: binnenkomende verbindings aanvraag...: 192.168.50.2/59415 NTLS [8508]: fout bericht van SSLAccept: fout: 14094418: SSL-routines: ssl3_read_bytes: tlsv1-waarschuwing onbekende ca NTLS [8508]: er is een fout opgetreden tijdens SSL accept (RC_SSL_ERROR) NTLS [8508]: info: 0xc0000711: het maken van een beveiligd kanaal met client: 192.168.50.2/59415: RC_SSL_FAILED_HANDSHAKE NTLS [8508]: info: 0: NTLS client "Onbekende hostnaam" is verwijderd: 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Mislukte TCP-communicatie

Communicatie van de Luna-client installatie naar de HSM vereist ten minste TCP-poort 1792. U kunt dit beschouwen als netwerk configuraties worden gewijzigd in de omgeving.

### <a name="failed-ha-group-member-doesnt-recover"></a>Het lid van de mislukte HA-groep wordt niet hersteld

Als een lid van een mislukte HA-groep niet wordt hersteld, moet het hand matig worden hersteld vanaf de Luna-client met behulp van de opdracht hagroup Recover.
U moet een aantal nieuwe pogingen voor een HA-groep configureren om automatisch herstellen in te scha kelen. Standaard wordt een ha-groep niet geprobeerd een HA-lid in de groep te herstellen wanneer het wordt hersteld.

### <a name="ha-group-doesnt-sync"></a>HA-groep wordt niet gesynchroniseerd

Als leden partities niet hetzelfde kloon domein hebben, wordt met de opdracht ha synchroniseren het volgende weer gegeven: waarschuwing: synchronisatie kan mislukken.  De leden in sleuf 0 en sleuf 1 hebben conflicterende instellingen voor het klonen van persoonlijke sleutels.
Er moet een nieuwe partitie met het juiste kloon domein worden toegevoegd aan de groep HA, gevolgd door het verwijderen van de onjuist geconfigureerde partitie.

## <a name="hsm-deprovisioning"></a>HSM-inrichting opheffen 

Alleen wanneer de inrichting volledig is voltooid met een HSM, kan deze worden opheffen en vervolgens opnieuw worden ingesteld door micro soft. 

### <a name="how-to-delete-an-hsm-resource"></a>Een HSM-resource verwijderen

De Azure-resource voor een HSM kan alleen worden verwijderd als de HSM de status ' Zeroed ' heeft.  Daarom moeten alle belang rijke materialen worden verwijderd voordat ze als een resource kunnen worden verwijderd. De snelste manier om te zeroize is het wacht woord voor de HSM-beheerder niet-probleemloos te verkrijgen (Opmerking: dit verwijst naar de HSM-beheerder en niet op het niveau van het apparaatniveau). De Luna-Shell heeft een `hsm -factoryreset` opdracht die zeroizes, maar kan alleen worden uitgevoerd via de console op de seriële poort en klanten hebben geen toegang tot dit bestand.

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u meer informatie over de levens cyclus van de HSM-implementatie, die mogelijk problemen ondervindt of problemen oplost of een zorgvuldige overweging moet nemen. Hopelijk wordt in dit artikel overbodige vertragingen en frustraties voor komen en als u relevante toevoegingen of wijzigingen hebt aangebracht, kunt u een ondersteunings aanvraag met micro soft genereren en ons laten weten. 
