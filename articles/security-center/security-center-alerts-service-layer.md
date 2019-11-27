---
title: Detectie van Azure Service Layer Threat-Azure Security Center
description: In dit onderwerp vindt u de waarschuwingen van de Azure-service laag die beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 7db9f50b4fb1a9309737f05db13a914f414372ed
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186505"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Detectie van dreigingen voor de service laag van Azure in Azure Security Center

Dit onderwerp bevat de beschik bare waarschuwingen voor Azure Security Center bij het bewaken van de volgende Azure-service lagen:

* [Azure-netwerklaag](#network-layer)
* [Azure Management Layer (Azure Resource Manager) (preview-versie)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

>[!NOTE]
>De volgende analyse zijn van toepassing op alle resource typen. Ze gebruiken de telemetrie die Security Center biedt door te tikken op interne feeds van Azure.

## Azure-netwerklaag<a name="network-layer"></a>

Security Center Network-laag analyse zijn gebaseerd op voorbeeld [gegevens van IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), die pakket headers zijn die worden verzameld door Azure core-routers. Op basis van deze gegevensfeed kunnen Security Center machine learning modellen kwaad aardige verkeers activiteiten identificeren en markeren. Voor een verrijkend IP-adres Security Center maakt gebruik van de micro soft Threat Intelligence-data base.

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**Verdachte uitgaande RDP-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande Remote Desktop Protocol (RDP)-communicatie gedetecteerd, afkomstig van een bron in uw implementatie. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om een extern RDP-eind punt aanval op te treden. Dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte uitgaande RDP-netwerk activiteit naar meerdere bestemmingen**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande RDP-communicatie gedetecteerd, afkomstig van een bron in uw implementatie naar meerdere bestemmingen. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om externe RDP-eind punten voor beveiligings aanvallen af te dwingen. Dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte uitgaande SSH-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende SSH-communicatie (uitgaande beveiligde shell) gedetecteerd, afkomstig van een resource in uw implementatie. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop wijzen dat uw resource is aangetast en nu wordt gebruikt om een externe SSH-eind punt te laten afdwingen. Dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte uitgaande SSH-netwerk activiteit naar meerdere bestemmingen**|Voor beeld van netwerk verkeer analyse heeft afwijkende uitgaande SSH-communicatie gedetecteerd, afkomstig van een resource in uw implementatie naar meerdere bestemmingen. Deze activiteit wordt als abnormaal beschouwd voor deze omgeving. Dit kan erop duiden dat de bron is aangetast en wordt nu gebruikt voor de externe SSH-eind punten van de aanval. Dit type activiteit kan ertoe leiden dat uw IP als schadelijk wordt gemarkeerd door externe entiteiten.|
|**Verdachte binnenkomende SSH-netwerk activiteit van meerdere bronnen**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende SSH-communicatie gedetecteerd van meerdere bronnen naar een bron in uw implementatie. Verschillende unieke IP-adressen die verbinding maken met uw resource, worden als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface via meerdere hosts (botnet) aan te vallen.|
|**Verdachte binnenkomende SSH-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende SSH-communicatie gedetecteerd bij een bron in uw implementatie. Een relatief groot aantal binnenkomende verbindingen met uw resource wordt als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface aan te vallen.
|**Verdachte binnenkomende RDP-netwerk activiteit van meerdere bronnen**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende RDP-communicatie van meerdere bronnen naar een bron in uw implementatie gedetecteerd. Verschillende unieke IP-adressen die verbinding maken met uw resource, worden als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw RDP-interface via meerdere hosts (botnet) aan te vallen.|
|**Verdachte binnenkomende RDP-netwerk activiteit**|Voor beeld van netwerk verkeer analyse heeft afwijkende binnenkomende RDP-communicatie gedetecteerd naar een bron in uw implementatie. Een relatief groot aantal binnenkomende verbindingen met uw resource wordt als abnormaal beschouwd voor deze omgeving. Deze activiteit kan wijzen op een poging om uw SSH-interface aan te vallen.|
|**Er is een netwerk communicatie met een schadelijk adres gedetecteerd**|Voorbeeld analyse van netwerk verkeer heeft communicatie gedetecteerd die afkomstig is van een bron in uw implementatie met een mogelijke opdracht-en beheer server (C & C). Dit type activiteit kan ertoe leiden dat uw IP als schadelijk door externe entiteiten wordt gemarkeerd.|

Zie [heuristische DNS-detecties in azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)om te begrijpen hoe Security Center netwerk signalen kunt gebruiken om bedreigingen beveiliging toe te passen.

>[!NOTE]
>Waarschuwingen voor de detectie van Azure Network Layer Threats in Azure Security Center worden alleen gegenereerd op virtuele machines waaraan hetzelfde IP-adres is toegewezen voor het hele uur gedurende welke een verdachte communicatie heeft plaatsgevonden. Dit geldt voor virtuele machines, evenals voor virtuele machines die in het abonnement van de klant worden gemaakt als onderdeel van een beheerde service (bijvoorbeeld AKS, Databricks).

## Azure Management Layer (Azure Resource Manager) (preview-versie)<a name ="management-layer"></a>

>[!NOTE]
>Security Center beveiligingslaag op basis van Azure Resource Manager is momenteel beschikbaar als preview-versie.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen. dit wordt beschouwd als het controle vlak voor Azure. Door de Azure Resource Manager-records te analyseren, Security Center een ongebruikelijke of mogelijk schadelijke bewerking in de Azure-abonnements omgeving detecteert.

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**Uitvoering van microburst Toolkit**|Er is een bekend Reconnaissance-Toolkit voor Cloud omgevingen gedetecteerd in uw omgeving. Het hulp [programma dat](https://github.com/NetSPI/MicroBurst) door een aanvaller (of indringings tester) kan worden gebruikt voor het toewijzen van de resources van uw abonnementen, het identificeren van onveilige configuraties en het lekken van vertrouwelijke informatie.|
|**Azurite Toolkit uitvoeren**|Er is een bekend Reconnaissance-Toolkit voor Cloud omgevingen gedetecteerd in uw omgeving. Het hulp programma [Azurite](https://github.com/mwrlabs/Azurite) kan door een aanvaller (of indringings tester) worden gebruikt om de resources van uw abonnementen toe te wijzen en onveilige configuraties te identificeren.|
|**Verdachte beheer sessie met een inactief account**|Logboeken voor abonnements activiteiten er is een verdacht gedrag gedetecteerd. Een principal die gedurende een lange periode niet wordt gebruikt, is nu bezig met het uitvoeren van acties waarmee persistentie voor een aanvaller kan worden beveiligd.|
|**Verdachte beheer sessie met behulp van Power shell**|Logboeken voor abonnements activiteiten er is een verdacht gedrag gedetecteerd. Een principal die Power shell niet regel matig gebruikt voor het beheren van de abonnements omgeving is nu het gebruik van Power shell en het uitvoeren van acties waarmee persistentie kan worden beveiligd voor een aanvaller.|
|**Gebruik van geavanceerde Azure-persistentie technieken**|Logboeken voor abonnements activiteiten er is een verdacht gedrag gedetecteerd. Aangepaste rollen hebben legitimized-identiteits entiteiten gekregen. Dit kan ertoe leiden dat de aanvaller opereren in een Azure-klant omgeving krijgt.|
|**Activiteit van het niet-frequente land**|Activiteit van een locatie die niet recent of ooit door een gebruiker in de organisatie is bezocht.<br/>Deze detectie rekening gehouden met het verleden activiteit locaties om te bepalen van de nieuwe en onregelmatige locaties. De anomaliedetectie-engine bevat informatie over de voorgaande locaties die worden gebruikt door gebruikers in de organisatie. 
|**Activiteit van anonieme IP-adressen**|Gebruikers activiteit van een IP-adres dat is geïdentificeerd als een anonieme proxy-IP-adres is gedetecteerd. <br/>Deze proxy's worden gebruikt door mensen die het IP-adres van hun apparaat willen verbergen en kunnen worden gebruikt voor kwaad aardige doel einden. Deze detectie maakt gebruik van een algoritme voor machine learning dat onjuiste positieven, zoals verkeerd gelabelde IP-adressen die veel worden gebruikt door gebruikers in de organisatie, vermindert.|
|**Onmogelijk traject gedetecteerd**|Er zijn twee gebruikers activiteiten (in één of meerdere sessies) opgetreden, afkomstig van geografische locaties. Dit gebeurt binnen een periode die korter is dan de tijd die de gebruiker zou hebben nodig om vanaf de eerste locatie naar de tweede te gaan. Dit geeft aan dat een andere gebruiker dezelfde referenties gebruikt. <br/>Deze detectie maakt gebruik van een machine learning algoritme dat duidelijke fout-positieven negeert die bijdragen aan de niet-bestaans omstandigheden, zoals Vpn's en locaties die regel matig door andere gebruikers in de organisatie worden gebruikt. De detectie heeft een initiële leer periode van zeven dagen, waarbij het activiteiten patroon van een nieuwe gebruiker wordt geleerd.|

>[!NOTE]
> Verschillende van de voor gaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security. Als u van deze analyses wilt profiteren, moet u een Cloud App Security-licentie activeren. Als u een licentie voor Cloud App Security hebt, zijn deze waarschuwingen standaard ingeschakeld. Ze uitschakelen:
>
> 1. Selecteer **beveiligings beleid**op de blade **Security Center** . Selecteer **Instellingen bewerken**voor het abonnement dat u wilt wijzigen.
> 2. Selecteer **bedreigingen detectie**.
> 3. Schakel onder **integraties inschakelen**het selectie vakje **Microsoft Cloud app Security toegang tot mijn gegevens toestaan**uit en selecteer **Opslaan**.

>[!NOTE]
>Security Center klant gegevens met betrekking tot beveiliging worden opgeslagen in dezelfde geografische regio als de resource. Als micro soft Security Center nog niet heeft geïmplementeerd in de geografische regio van de resource, worden de gegevens opgeslagen in de Verenigde Staten. Als Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatie regels van Cloud App Security. Zie [gegevens opslag voor niet-regionale Services](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.

## Azure Key Vault<a name="azure-keyvault"></a>

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. 

Azure Security Center omvat Azure-systeem eigen, geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault, waarmee u een extra laag van beveiligings informatie kunt leveren. Security Center detecteert ongebruikelijke en mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Deze beveiligingslaag biedt u de mogelijkheid bedreigingen te verhelpen zonder een beveiligings expert en zonder de nood zaak om beveiligings bewakings systemen van derden te beheren.  

Als er afwijkende activiteiten optreden, worden in Security Center waarschuwingen weer gegeven en worden ze optioneel verzonden via e-mail naar abonnements beheerders. Deze waarschuwingen omvatten de details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**Toegang vanaf een TOR-eind knooppunt naar een Key Vault**|De Key Vault is geopend door iemand die het TOR IP anoniem maken-systeem gebruikt om de locatie te verbergen. Kwaad aardige actors proberen vaak hun locatie te verbergen wanneer er wordt geprobeerd om onbevoegde toegang te krijgen tot bronnen met Internet verbinding.|
|**Verdachte beleids wijziging en geheime query in een Key Vault**|Er is een Key Vault-beleid gewijzigd en vervolgens worden de bewerkingen voor het weer geven en/of ontvangen van geheimen uitgevoerd. Daarnaast wordt dit bewerkings patroon niet normaal uitgevoerd door de gebruiker op deze kluis. Dit is zeer indicatief dat de Key Vault is aangetast en dat de geheimen binnen zijn gestolen door een schadelijke actor.|
|**Verdachte geheime vermelding en query in een Key Vault**|Er is een geheime lijst bewerking gevolgd door veel geheime Get-bewerkingen. Dit bewerkings patroon wordt ook normaal gesp roken niet uitgevoerd door de gebruiker op deze kluis. Dit geeft aan dat iemand kan dumpen van de geheimen die zijn opgeslagen in de Key Vault voor mogelijk schadelijke doel einden.|
|**Ongebruikelijke gebruiker-toepassings koppeling heeft toegang gekregen tot een Key Vault**|De Key Vault is geopend door een combi natie van gebruikers toepassingen die normaal gesp roken geen toegang heeft. Dit kan een rechtmatige toegangs poging zijn (bijvoorbeeld het volgen van een infra structuur of code-update). Dit is ook een mogelijke indicatie dat uw infra structuur is aangetast en een kwaadwillende actor probeert toegang te krijgen tot uw Key Vault.|
|**Ongebruikelijke toepassing heeft toegang gekregen tot een Key Vault**|De Key Vault is geopend door een toepassing die normaal gesp roken geen toegang heeft. Dit kan een rechtmatige toegangs poging zijn (bijvoorbeeld het volgen van een infra structuur of code-update). Dit is ook een mogelijke indicatie dat uw infra structuur is aangetast en een kwaadwillende actor probeert toegang te krijgen tot uw Key Vault.|
|**Ongebruikelijke gebruiker heeft toegang gekregen tot een Key Vault**|De Key Vault is geopend door een gebruiker die deze niet normaal toegang heeft. Dit kan een rechtmatige toegangs poging zijn (bijvoorbeeld wanneer een nieuwe gebruiker die toegang nodig heeft lid is van de organisatie). Dit is ook een mogelijke indicatie dat uw infra structuur is aangetast en een kwaadwillende actor probeert toegang te krijgen tot uw Key Vault.|
|**Ongebruikelijk bewerkings patroon in een Key Vault**|Er is een ongebruikelijke set Key Vault bewerkingen uitgevoerd in vergelijking met historische gegevens. Key Vault activiteit is doorgaans in de loop van de tijd hetzelfde. Dit kan een rechtmatige wijziging in de activiteit zijn. Het is ook mogelijk dat uw infra structuur is aangetast en dat er verdere onderzoeken nood zakelijk zijn.|
|**Grote hoeveelheid bewerkingen in een Key Vault**|Er is een groter volume van Key Vault bewerkingen uitgevoerd in vergelijking met historische gegevens. Key Vault activiteit is doorgaans in de loop van de tijd hetzelfde. Dit kan een rechtmatige wijziging in de activiteit zijn. Het is ook mogelijk dat uw infra structuur is aangetast en dat er verdere onderzoeken nood zakelijk zijn.|
|**Gebruiker heeft grote hoeveelheid sleutel kluizen geopend**|Het aantal kluizen dat een gebruiker of toepassing toegang heeft, is gewijzigd ten opzichte van historische gegevens. Key Vault activiteit is doorgaans in de loop van de tijd hetzelfde. Dit kan een rechtmatige wijziging in de activiteit zijn. Het is ook mogelijk dat uw infra structuur is aangetast en dat er verdere onderzoeken nood zakelijk zijn.|