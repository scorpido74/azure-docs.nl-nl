---
title: Detectie van bedreigingen voor Azure Service Layer in Azure Security Center | Microsoft Docs
description: In dit onderwerp vindt u de waarschuwingen van de Azure-service laag die beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 5458d26172123f0f6cb9914a04ec05b3163a084f
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013275"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Detectie van dreigingen voor de service laag van Azure in Azure Security Center

Dit onderwerp bevat de beschik bare waarschuwingen voor Azure Security Center bij het bewaken van de volgende Azure-service lagen:

* [Azure-netwerklaag](#network-layer)
* [Azure Management Layer (Azure Resource Manager) (preview-versie)](#management-layer)

>[!NOTE]
>De volgende analyse zijn van toepassing op alle resource typen. Ze gebruiken de telemetrie die Security Center biedt door te tikken op interne feeds van Azure.

## Azure-netwerklaag<a name="network-layer"></a>

Security Center Network-laag analyse zijn gebaseerd op voorbeeld [gegevens van IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), die pakket headers zijn die worden verzameld door Azure core-routers. Op basis van deze gegevensfeed kunnen Security Center machine learning modellen kwaad aardige verkeers activiteiten identificeren en markeren. Voor een verrijkend IP-adres Security Center maakt gebruik van de micro soft Threat Intelligence-data base.

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Verdachte uitgaande RDP-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande Remote Desktop Protocol (RDP)-communicatie gedetecteerd, afkomstig van een bron in uw implementatie. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om een extern RDP-eind punt aanval op te treden. Houd er rekening mee dat dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte uitgaande RDP-netwerk activiteit naar meerdere bestemmingen**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande RDP-communicatie gedetecteerd, afkomstig van een bron in uw implementatie naar meerdere bestemmingen. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om externe RDP-eind punten voor beveiligings aanvallen af te dwingen. Houd er rekening mee dat dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte uitgaande SSH-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende SSH-communicatie (uitgaande beveiligde shell) gedetecteerd, afkomstig van een resource in uw implementatie. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om een externe SSH-eind punt te laten afdwingen. Houd er rekening mee dat dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte uitgaande SSH-netwerk activiteit naar meerdere bestemmingen**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande SSH-communicatie gedetecteerd, afkomstig van een resource in uw implementatie naar meerdere bestemmingen. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop duiden dat de bron is aangetast en wordt nu gebruikt voor de externe SSH-eind punten van de aanval. Houd er rekening mee dat dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte binnenkomende SSH-netwerk activiteit van meerdere bronnen**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende SSH-communicatie gedetecteerd van meerdere bronnen naar een bron in uw implementatie. Verschillende unieke IP-adressen die verbinding maken met uw resource, worden als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface via meerdere hosts (botnet) aan te vallen.|
|**Verdachte binnenkomende SSH-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende SSH-communicatie gedetecteerd bij een bron in uw implementatie. Een relatief groot aantal binnenkomende verbindingen met uw resource wordt als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface aan te vallen.
|**Verdachte binnenkomende RDP-netwerk activiteit van meerdere bronnen**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende RDP-communicatie van meerdere bronnen naar een bron in uw implementatie gedetecteerd. Verschillende unieke IP-adressen die verbinding maken met uw resource, worden als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw RDP-interface via meerdere hosts (botnet) aan te vallen.|
|**Verdachte binnenkomende RDP-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende RDP-communicatie gedetecteerd naar een bron in uw implementatie. Een relatief groot aantal binnenkomende verbindingen met uw resource wordt als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface aan te vallen.|
|**Er is een netwerk communicatie met een schadelijk adres gedetecteerd**|Voorbeeld analyse van netwerk verkeer heeft communicatie gedetecteerd die afkomstig is van een bron in uw implementatie met een mogelijke opdracht-en beheer server (C & C). Houd er rekening mee dat dit type activiteit ervoor kan zorgen dat uw IP-adres wordt gemarkeerd als kwaadaardig bij externe entiteiten.|

Zie [heuristische DNS-detecties in azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)om te begrijpen hoe Security Center netwerk signalen kunt gebruiken om bedreigings beveiliging toe te passen.

>[!NOTE]
>Meldingen van de detectie van Azure-netwerk lagen, in Azure Security Center, worden niet gegenereerd tijdens het eerste uur nadat de virtuele machine is gemaakt.

## Azure Management Layer (Azure Resource Manager) (preview-versie)<a name ="management-layer"></a>

>[!NOTE]
>Security Center beveiligingslaag op basis van Azure Resource Manager is momenteel beschikbaar als preview-versie.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen. dit wordt beschouwd als het controle vlak voor Azure. Door de Azure Resource Manager-records te analyseren, Security Center een ongebruikelijke of mogelijk schadelijke bewerking in de Azure-abonnements omgeving detecteert.

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Uitvoering van microburst Toolkit**|Er is een bekend Reconnaissance-Toolkit voor Cloud omgevingen gedetecteerd in uw omgeving. Het hulp [](https://github.com/NetSPI/MicroBurst) programma dat door een aanvaller (of indringings tester) kan worden gebruikt voor het toewijzen van de resources van uw abonnementen, het identificeren van onveilige configuraties en het lekken van vertrouwelijke informatie.|
|**Azurite Toolkit uitvoeren**|Er is een bekend Reconnaissance-Toolkit voor Cloud omgevingen gedetecteerd in uw omgeving. Het hulp programma [Azurite](https://github.com/mwrlabs/Azurite) kan door een aanvaller (of indringings tester) worden gebruikt om de resources van uw abonnementen toe te wijzen en onveilige configuraties te identificeren.|
|**Verdachte beheer sessie met een inactief account**|Logboeken voor abonnements activiteiten er is een verdacht gedrag gedetecteerd. Een principal die gedurende een lange periode niet wordt gebruikt, is nu bezig met het uitvoeren van acties waarmee persistentie voor een aanvaller kan worden beveiligd.|
|**Verdachte beheer sessie met behulp van Power shell**|Logboeken voor abonnements activiteiten er is een verdacht gedrag gedetecteerd. Een principal die Power shell niet regel matig gebruikt voor het beheren van de abonnements omgeving is nu het gebruik van Power shell en het uitvoeren van acties waarmee persistentie kan worden beveiligd voor een aanvaller.|
|**Gebruik van geavanceerde Azure-persistentie technieken**|Logboeken voor abonnements activiteiten er is een verdacht gedrag gedetecteerd. Aangepaste rollen hebben legitimized-identiteits entiteiten gekregen. Dit kan ertoe leiden dat de aanvaller opereren in een Azure-klant omgeving krijgt.|
|**Activiteit vanuit niet-gangbaar land**|Activiteit van een locatie die niet recent of ooit door een gebruiker in de organisatie is bezocht.<br/>Deze detectie rekening gehouden met het verleden activiteit locaties om te bepalen van de nieuwe en onregelmatige locaties. De anomaliedetectie-engine bevat informatie over de voorgaande locaties die worden gebruikt door gebruikers in de organisatie. 
|**Activiteit vanaf anonieme IP-adressen**|Gebruikers activiteit van een IP-adres dat is geïdentificeerd als een anonieme proxy-IP-adres is gedetecteerd. <br/>Deze proxy's worden gebruikt door mensen die het IP-adres van hun apparaat willen verbergen en kunnen worden gebruikt voor kwaad aardige doel einden. Deze detectie maakt gebruik van een algoritme voor machine learning dat onjuiste positieven, zoals verkeerd gelabelde IP-adressen die veel worden gebruikt door gebruikers in de organisatie, vermindert.|
|**Onmogelijk traject gedetecteerd**|Er zijn twee gebruikers activiteiten (in één of meerdere sessies) opgetreden, afkomstig van geografische locaties. Dit gebeurt binnen een periode die korter is dan de tijd die de gebruiker zou hebben nodig om vanaf de eerste locatie naar de tweede te gaan. Dit geeft aan dat een andere gebruiker dezelfde referenties gebruikt. <br/>Deze detectie maakt gebruik van een machine learning algoritme dat duidelijke fout-positieven negeert die bijdragen aan de niet-bestaans omstandigheden, zoals Vpn's en locaties die regel matig door andere gebruikers in de organisatie worden gebruikt. De detectie heeft een initiële leer periode van zeven dagen, waarbij het activiteiten patroon van een nieuwe gebruiker wordt geleerd.|

>[!NOTE]
> Verschillende van de voor gaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security. Als u van deze analyses wilt profiteren, moet u een Cloud App Security-licentie activeren. Als u een licentie voor Cloud App Security hebt, zijn deze waarschuwingen standaard ingeschakeld. Ze uitschakelen:
>
> 1. Selecteer **beveiligings beleid**op de blade **Security Center** . Selecteer **Instellingen bewerken**voor het abonnement dat u wilt wijzigen.
> 2. Selecteer **bedreigingen detectie**.
> 3. Schakel onder **integraties inschakelen**het selectie vakje **Microsoft Cloud app Security toegang tot mijn gegevens toestaan**uit en selecteer **Opslaan**.

>[!NOTE]
>Security Center klant gegevens met betrekking tot beveiliging worden opgeslagen in dezelfde geografische regio als de resource. Als micro soft Security Center nog niet heeft geïmplementeerd in de geografische regio van de resource, worden de gegevens opgeslagen in de Verenigde Staten. Als Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatie regels van Cloud App Security. Zie [gegevens opslag voor niet-regionale Services](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.
