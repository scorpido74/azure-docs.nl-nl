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
ms.openlocfilehash: aae2270417e0bb3bdf0dd847e2e09e7cb1999d39
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972474"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Detectie van bedreigingen voor Azure-service lagen in Azure Security Center

Dit onderwerp bevat de beschik bare waarschuwingen voor Security Center bij het bewaken van de volgende Azure-service lagen.

* [Azure-netwerklaag](#network-layer)
* [Azure Management Layer (Azure Resource Manager) (preview-versie)](#management-layer)

>[!NOTE]
>Met behulp van de telemetrie die Security Center gebruikt om te tikken in azure interne feeds, zijn de hieronder vermelde analyses van toepassing op alle resource typen.

## Azure-netwerklaag<a name="network-layer"></a>

Security Center Network-laag analyse zijn gebaseerd op voorbeeld [gegevens van IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), die pakket headers zijn die worden verzameld door Azure core-routers. Op basis van deze gegevensfeed kunnen Security Center machine learning modellen kwaad aardige verkeers activiteiten identificeren en markeren. Voor een verrijkend IP-adres Security Center maakt gebruik van de micro soft Threat Intelligence-data base.

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Verdachte uitgaande RDP-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande Remote Desktop Protocol (RDP)-communicatie gedetecteerd die afkomstig is van een bron in uw implementatie. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving en kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt voor een beveiligings aanval van het externe RDP-eind punt. Houd er rekening mee dat dit type activiteit ervoor kan zorgen dat uw IP-adres wordt gemarkeerd als kwaadaardig bij externe entiteiten.|
|**Verdachte uitgaande RDP-netwerk activiteit naar meerdere bestemmingen**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande Remote Desktop Protocol (RDP)-communicatie gedetecteerd die afkomstig is van een bron in uw implementatie naar meerdere bestemmingen. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving en kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om externe RDP-eind punten te afdwingen. Houd er rekening mee dat dit type activiteit ervoor kan zorgen dat uw IP-adres wordt gemarkeerd als kwaadaardig bij externe entiteiten.|
|**Verdachte uitgaande SSH-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende SSH-communicatie gedetecteerd die afkomstig is van een resource in uw implementatie. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving en kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om een externe SSH-eind punt te verzorgen. Houd er rekening mee dat dit type activiteit ervoor kan zorgen dat uw IP-adres wordt gemarkeerd als kwaadaardig bij externe entiteiten.|
|**Verdachte uitgaande SSH-netwerk activiteit naar meerdere bestemmingen**|Voor beeld van netwerk verkeer analyse heeft afwijkende SSH-communicatie gedetecteerd die afkomstig is van een resource in uw implementatie naar meerdere bestemmingen. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving en kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om externe SSH-eind punten te afdwingen. Houd er rekening mee dat dit type activiteit ervoor kan zorgen dat uw IP-adres wordt gemarkeerd als kwaadaardig bij externe entiteiten.|
|**Verdachte binnenkomende SSH-netwerk activiteit van meerdere bronnen**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende SSH-communicatie gedetecteerd van meerdere bronnen naar een bron in uw implementatie. Verschillende unieke IP-adressen die verbinding maken met uw resource, worden als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface te forceren vanaf meerdere hosts (botnet).|
|**Verdachte binnenkomende SSH-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende SSH-communicatie gedetecteerd bij een bron in uw implementatie. Relatief groot aantal binnenkomende verbindingen met uw resource wordt als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface te forceren.
|**Verdachte binnenkomende RDP-netwerk activiteit van meerdere bronnen**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende RDP-communicatie van meerdere bronnen naar een bron in uw implementatie gedetecteerd. Verschillende unieke IP-adressen die verbinding maken met uw resource, worden als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw RDP-interface te forceren van meerdere hosts (botnet).|
|**Verdachte binnenkomende RDP-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende RDP-communicatie gedetecteerd naar een bron in uw implementatie. Relatief groot aantal binnenkomende verbindingen met uw resource wordt als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface te forceren.|
|**Er is een netwerk communicatie met een schadelijk adres gedetecteerd**|Voorbeeld analyse van netwerk verkeer heeft communicatie gedetecteerd die afkomstig is van een bron in uw implementatie met een mogelijke opdracht-en beheer server (C & C). Houd er rekening mee dat dit type activiteit ervoor kan zorgen dat uw IP-adres wordt gemarkeerd als kwaadaardig bij externe entiteiten.|

Zie [heuristische DNS-detecties in azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)om te begrijpen hoe Security Center netwerk signalen kunt gebruiken om bedreigings beveiliging toe te passen.

>[!NOTE]
>Meldingen van de detectie van Azure-netwerk lagen, in Azure Security Center, worden niet gegenereerd tijdens het eerste uur nadat de virtuele machine is gemaakt.

## Azure Management Layer (Azure Resource Manager) (preview-versie)<a name ="management-layer"></a>

>[!NOTE]
>Security Center beveiligingslaag op basis van Azure Resource Manager is momenteel beschikbaar als preview-versie.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen, die worden beschouwd als het controle vlak voor Azure. Door de Azure Resource Manager-records te analyseren, Security Center een ongebruikelijke of mogelijk schadelijke bewerking in de Azure-abonnements omgeving detecteert.

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Uitvoering van microburst Toolkit**|Er is een bekend Reconnaissance-Toolkit voor Cloud omgevingen gedetecteerd in uw omgeving. Het hulp programma ' microburst ' ( https://github.com/NetSPI/MicroBurst) Zie kan worden gebruikt door een aanvaller (of indringings test) om uw abonnement (en) te koppelen, onveilige configuraties te identificeren en vertrouwelijke informatie te lekken.|
|**Azurite Toolkit uitvoeren**|Er is een bekend Reconnaissance-Toolkit voor Cloud omgevingen gedetecteerd in uw omgeving. Het hulp programma ' Azurite ' ( https://github.com/mwrlabs/Azurite) Zie kan worden gebruikt door een aanvaller (of indringings test) om uw abonnement (en) toe te wijzen en onveilige configuraties te identificeren.|
|**Verdachte beheer sessie met een inactief account**|In Logboeken voor abonnements activiteiten is een verdacht gedrag gedetecteerd. Een principal die gedurende een lange periode niet wordt gebruikt, is nu bezig met het uitvoeren van acties waarmee persistentie voor een aanvaller kan worden beveiligd.|
|**Verdachte beheer sessie met behulp van Power shell**|In Logboeken voor abonnements activiteiten is een verdacht gedrag gedetecteerd. Een principal die Power shell niet regel matig gebruikt voor het beheren van de abonnements omgeving, maakt nu gebruik van Power shell en voert acties uit waarmee persistentie voor een aanvaller kan worden beveiligd.|
|**Gebruik van geavanceerde Azure-persistentie technieken**|In Logboeken voor abonnements activiteiten is een verdacht gedrag gedetecteerd. Aangepaste rollen hebben legitimized-identiteits entiteiten gekregen. Dit kan ertoe leiden dat de aanvaller opereren in een Azure-klant omgeving krijgt.|
|**Activiteit vanuit niet-gangbaar land**|Activiteit van een locatie die niet recent of nooit is bezocht door een gebruiker in de organisatie.<br/>Deze detectie rekening gehouden met het verleden activiteit locaties om te bepalen van de nieuwe en onregelmatige locaties. De anomaliedetectie-engine bevat informatie over de voorgaande locaties die worden gebruikt door gebruikers in de organisatie. 
|**Activiteit vanaf anonieme IP-adressen**|Gebruikers activiteit van een IP-adres dat is geïdentificeerd als een anonieme proxy-IP-adres is gedetecteerd. <br/>Deze proxy's worden gebruikt door mensen die u wilt verbergen, IP-adres van hun apparaat en kunnen voor kwade bedoelingen worden gebruikt. Deze detectie maakt gebruik van een machine learning algoritme dat ' fout-positieven ' reduceert, zoals verkeerd gelabelde IP-adressen die veel worden gebruikt door gebruikers in de organisatie.|
|**Onmogelijk traject gedetecteerd**|Er zijn twee gebruikers activiteiten (een of meerdere sessies), die afkomstig zijn van geografische locaties binnen een bepaalde tijds periode, en die de gebruiker de tijd zou hebben genomen om vanaf de eerste locatie naar de tweede te gaan. Dit geeft aan dat een andere gebruiker dezelfde referenties gebruikt. <br/>Deze detectie maakt gebruik van een machine learning algoritme dat duidelijk ' valse positieven ' bijdraagt aan de onmogelijke reis omstandigheden, zoals Vpn's en locaties die regel matig door andere gebruikers in de organisatie worden gebruikt. De detectie is een eerste leerperiode van zeven dagen gedurende welke het activiteitenpatroon van een nieuwe gebruiker hoort.|

>[!NOTE]
> Verschillende van de bovenstaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security (MCAS). Als u van deze analyses wilt profiteren, is een geactiveerde MCAS-licentie vereist. Als u een MCAS-licentie hebt, zijn deze waarschuwingen standaard ingeschakeld. Ze uitschakelen:
>
> 1. Selecteer **beveiligings beleid**op de blade Security Center. Klik op **Instellingen bewerken**voor het abonnement dat u wilt wijzigen.
> 2. Klik op **bedreigings detectie**.
> 3. Schakel onder **integraties inschakelen**de optie **toestaan dat Microsoft Cloud app Security toegang heeft tot mijn gegevens**uit en klik op **Opslaan**.

>[!NOTE]
>Azure Security Center klant gegevens met betrekking tot beveiliging worden opgeslagen in dezelfde geografische regio als de resource. Als micro soft nog niet Azure Security Center in de geografische regio van de resource heeft geïmplementeerd, worden de gegevens opgeslagen in de Verenigde Staten. Als Microsoft Cloud App Security (MCAS) is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatie regels van MCAS. Zie [gegevens opslag voor niet-regionale Services voor meer informatie](https://azuredatacentermap.azurewebsites.net/).
