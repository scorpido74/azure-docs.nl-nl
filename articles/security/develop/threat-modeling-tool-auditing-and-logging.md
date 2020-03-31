---
title: Controle en logboekregistratie - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
description: oplossingen voor bedreigingen die worden blootgesteld in de Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728417"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Beveiligingskader: controle en houtkap | Mitigaties 

| Product/service | Artikel |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificeren van gevoelige entiteiten in uw oplossing en implementeren van change auditing](#sensitive-entities)</li></ul> |
| **Webtoepassing** | <ul><li>[Ervoor zorgen dat controle en logboekregistratie wordt afgedwongen op de toepassing](#auditing)</li><li>[Zorg ervoor dat logboekrotatie en -scheiding aanwezig zijn](#log-rotation)</li><li>[Ervoor zorgen dat de toepassing geen gevoelige gebruikersgegevens registreert](#log-sensitive-data)</li><li>[Controleren en logboekbestanden hebben beperkte toegang](#log-restricted-access)</li><li>[Controleren of gebruikersbeheergebeurtenissen zijn geregistreerd](#user-management)</li><li>[Zorg ervoor dat het systeem heeft ingebouwde verdediging tegen misbruik](#inbuilt-defenses)</li><li>[Diagnostische logboekregistratie inschakelen voor webapps in Azure App Service](#diagnostics-logging)</li></ul> |
| **Database** | <ul><li>[Controleren op inloggegevens is ingeschakeld op SQL Server](#identify-sensitive-entities)</li><li>[Bedreigingsdetectie inschakelen op Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Azure Storage Analytics gebruiken om de toegang tot Azure Storage te controleren](#analytics)</li></ul> |
| **WCF** | <ul><li>[Voldoende logboekregistratie implementeren](#sufficient-logging)</li><li>[Voldoende controlefoutafhandeling implementeren](#audit-failure-handling)</li></ul> |
| **Web-API** | <ul><li>[Controleren en loggen wordt afgedwongen op web-API](#logging-web-api)</li></ul> |
| **IoT-veldgateway** | <ul><li>[Ervoor zorgen dat de juiste controle en logboekregistratie wordt afgedwongen op Field Gateway](#logging-field-gateway)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Ervoor zorgen dat de juiste controle en logboekregistratie wordt afgedwongen op Cloud Gateway](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identificeren van gevoelige entiteiten in uw oplossing en implementeren van change auditing

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen**                   | Entiteiten in uw oplossing identificeren die gevoelige gegevens bevatten en wijzigingscontrole implementeren op die entiteiten en velden |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Ervoor zorgen dat controle en logboekregistratie wordt afgedwongen op de toepassing

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen**                   | Schakel controle en logboekregistratie op alle onderdelen in. Controlelogboeken moeten de context van de gebruiker vastleggen. Identificeer alle belangrijke gebeurtenissen en log deze gebeurtenissen. Gecentraliseerde logboekregistratie implementeren |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Zorg ervoor dat logboekrotatie en -scheiding aanwezig zijn

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen**                   | <p>Logrotatie is een geautomatiseerd proces dat wordt gebruikt in systeembeheer waarbij gedateerde logbestanden worden gearchiveerd. Servers die grote toepassingen uitvoeren, loggen vaak elke aanvraag: in het licht van omvangrijke logboeken is logboekrotatie een manier om de totale grootte van de logboeken te beperken en tegelijkertijd analyse van recente gebeurtenissen mogelijk te maken. </p><p>Scheiding van logboek betekent in feite dat u uw logbestanden op een andere partitie moet opslaan als waar uw OS/toepassing wordt uitgevoerd om een Denial of service-aanval of de verlaging van de prestaties ervan af te wenden</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Ervoor zorgen dat de toepassing geen gevoelige gebruikersgegevens registreert

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen**                   | <p>Controleer of u geen gevoelige gegevens registreert die een gebruiker naar uw site verzendt. Controleer op opzettelijke logging en bijwerkingen veroorzaakt door ontwerpproblemen. Voorbeelden van gevoelige gegevens zijn:</p><ul><li>Gebruikersreferenties</li><li>Sofi-nummer of andere identificerende informatie</li><li>Creditcardnummers of andere financiële informatie</li><li>Gezondheidsinformatie</li><li>Privésleutels of andere gegevens die kunnen worden gebruikt om versleutelde informatie te decoderen</li><li>Systeem- of toepassingsinformatie die kan worden gebruikt om de toepassing effectiever aan te vallen</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Controleren en logboekbestanden hebben beperkte toegang

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen**                   | <p>Controleer of de toegangsrechten voor logbestanden op de juiste manier zijn ingesteld. Toepassingsaccounts moeten alleen-schrijftoegang hebben en operators en ondersteunend personeel moeten indien nodig alleen-lezen toegang hebben.</p><p>Beheerdersaccounts zijn de enige accounts die volledige toegang moeten hebben. Controleer Windows ACL op logboekbestanden om te controleren of ze correct zijn beperkt:</p><ul><li>Toepassingsaccounts moeten alleen-schrijftoegang hebben</li><li>Exploitanten en ondersteunend personeel moeten indien nodig alleen-lezen toegang hebben</li><li>Beheerders zijn de enige accounts die volledige toegang moeten hebben</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Controleren of gebruikersbeheergebeurtenissen zijn geregistreerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen**                   | <p>Zorg ervoor dat de toepassing gebruikersbeheergebeurtenissen controleert, zoals geslaagde en mislukte gebruikersaanmeldingen, wachtwoordresets, wachtwoordwijzigingen, accountuitsluiting, gebruikersregistratie. Door dit te doen helpt bij het detecteren en reageren op potentieel verdacht gedrag. Het maakt het ook mogelijk om bedrijfsgegevens te verzamelen; bijvoorbeeld om bij te houden wie toegang heeft tot de toepassing</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Zorg ervoor dat het systeem heeft ingebouwde verdediging tegen misbruik

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen**                   | <p>Er moeten controles worden uitgevoerd die een uitzondering vormen voor de beveiliging in geval van misbruik van toepassingen. Bijvoorbeeld, Als input validatie is op zijn plaats en een aanvaller probeert om kwaadaardige code die niet overeenkomt met de regex injecteren, kan een beveiligingsuitzondering worden gegooid die een indicatie van systeemmisbruik kan zijn</p><p>Het wordt bijvoorbeeld aanbevolen om beveiligingsuitzonderingen te laten registreren en acties te laten uitvoeren voor de volgende problemen:</p><ul><li>Invoervalidatie</li><li>CSRF-schendingen</li><li>Brute kracht (bovengrens voor aantal aanvragen per gebruiker per resource)</li><li>Schendingen van het uploaden van bestanden</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Diagnostische logboekregistratie inschakelen voor webapps in Azure App Service

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType - Azure |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Azure biedt ingebouwde diagnostiek om te helpen bij het debuggen van een App Service-web-app. Het is ook van toepassing op API-apps en mobiele apps. App Service-webapps bieden diagnostische functionaliteit voor het registreren van informatie van zowel de webserver als de webtoepassing.</p><p>Deze zijn logisch gescheiden in webserverdiagnostiek en toepassingsdiagnostiek</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Controleren op inloggegevens is ingeschakeld op SQL Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Controle van aanmelding configureren](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Stappen** | <p>Database Server login auditing moet worden ingeschakeld om te detecteren / bevestigen wachtwoord raden aanvallen. Het is belangrijk om mislukte inlogpogingen vast te leggen. Het vastleggen van zowel succesvolle als mislukte inlogpogingen biedt extra voordeel tijdens forensisch onderzoek</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Bedreigingsdetectie inschakelen op Azure SQL

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | SQL Azure |
| **Kenmerken**              | SQL-versie - V12 |
| **Verwijzingen**              | [Aan de slag met SQL Database Threat Detection](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Stappen** |<p>Threat Detection detecteert afwijkende databaseactiviteiten die potentiële beveiligingsbedreigingen voor de database aangeven. Het biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop kunnen reageren wanneer ze zich voordoen door beveiligingswaarschuwingen te geven over afwijkende activiteiten.</p><p>Gebruikers kunnen de verdachte gebeurtenissen verkennen met Azure SQL Database Auditing om te bepalen of ze het gevolg zijn van een poging om toegang te krijgen tot, inbreuk te maken of gegevens in de database te exploiteren.</p><p>Threat Detection maakt het eenvoudig om potentiële bedreigingen voor de database aan te pakken zonder dat u beveiligingsexpert hoeft te zijn of geavanceerde beveiligingsbewakingssystemen hoeft te beheren</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Azure Storage Analytics gebruiken om de toegang tot Azure Storage te controleren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t. |
| **Verwijzingen**              | [Storage Analytics gebruiken om het autorisatietype te controleren](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Stappen** | <p>Voor elk opslagaccount kan azure storage analytics gegevens over logboekregistratie staken en vastleggen. De opslaganalyselogboeken bevatten belangrijke informatie, zoals de verificatiemethode die door iemand wordt gebruikt wanneer hij toegang heeft tot opslag.</p><p>Dit kan echt handig zijn als je strak bewaken toegang tot opslag. In Blob Storage u bijvoorbeeld alle containers op privé instellen en het gebruik van een SAS-service in uw toepassingen implementeren. Vervolgens u de logboeken regelmatig controleren om te zien of uw blobs worden geopend met behulp van de opslagaccountsleutels, wat kan duiden op een inbreuk op de beveiliging, of als de blobs openbaar zijn, maar dat zouden ze niet moeten zijn.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Voldoende logboekregistratie implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Stappen** | <p>Het ontbreken van een goed controlespoor na een veiligheidsincident kan forensische inspanningen belemmeren. Windows Communication Foundation (WCF) biedt de mogelijkheid om geslaagde en/of mislukte verificatiepogingen te registreren.</p><p>Mislukte verificatiepogingen loggen kunnen beheerders waarschuwen voor mogelijke brute-force-aanvallen. Op dezelfde manier kan het registreren van succesvolle verificatiegebeurtenissen een handig controlespoor bieden wanneer een legitiem account wordt gecompromitteerd. De functie voor servicebeveiliging van WCF inschakelen |

### <a name="example"></a>Voorbeeld
Het volgende is een voorbeeldconfiguratie met controle ingeschakeld
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Voldoende controlefoutafhandeling implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Stappen** | <p>De ontwikkelde oplossing is geconfigureerd om geen uitzondering te genereren wanneer deze niet naar een controlelogboek wordt geschreven. Als WCF is geconfigureerd om geen uitzondering te maken wanneer het niet in staat is om naar een controlelogboek te schrijven, wordt het programma niet op de hoogte gesteld van het falen en kan het niet plaatsvinden dat kritieke beveiligingsgebeurtenissen worden gecontroleerd.</p>|

### <a name="example"></a>Voorbeeld
Het `<behavior/>` element van het onderstaande WCF-configuratiebestand geeft WCF de opdracht om de toepassing niet op de hoogte te stellen wanneer WCF niet naar een controlelogboek schrijft.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Configureer WCF om het programma op de hoogte te stellen wanneer het niet in staat is om naar een controlelogboek te schrijven. Het programma moet een alternatief meldingsschema hebben om de organisatie te waarschuwen dat audittrails niet worden onderhouden. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Controleren en loggen wordt afgedwongen op web-API

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Controle en logboekregistratie inschakelen op web-API's. Controlelogboeken moeten de context van de gebruiker vastleggen. Identificeer alle belangrijke gebeurtenissen en log deze gebeurtenissen. Gecentraliseerde logboekregistratie implementeren |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Ervoor zorgen dat de juiste controle en logboekregistratie wordt afgedwongen op Field Gateway

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-veldgateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Wanneer meerdere apparaten verbinding maken met een veldgateway, moet u ervoor zorgen dat verbindingspogingen en verificatiestatus (succes of uitval) voor afzonderlijke apparaten worden geregistreerd en onderhouden in de Veldgateway.</p><p>Ook in gevallen waarin Field Gateway de IoT Hub-referenties voor afzonderlijke apparaten onderhoudt, moet u ervoor zorgen dat de controle wordt uitgevoerd wanneer deze referenties worden opgehaald. Ontwikkel een proces om de logboeken periodiek te uploaden naar Azure IoT Hub/storage voor langdurige retentie.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Ervoor zorgen dat de juiste controle en logboekregistratie wordt afgedwongen op Cloud Gateway

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT Cloud Gateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Inleiding tot monitoring van IoT Hub-bewerkingen](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Stappen** | <p>Ontwerp voor het verzamelen en opslaan van controlegegevens die zijn verzameld via IoT Hub Operations Monitoring. Schakel de volgende bewakingscategorieën in:</p><ul><li>Apparaatidentiteitsbewerkingen</li><li>Device-to-cloud communicatie</li><li>Communicatie tussen cloud en apparaat</li><li>Verbindingen</li><li>Bestandsuploads</li></ul>|