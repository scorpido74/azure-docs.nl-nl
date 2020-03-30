---
title: Autorisatie - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
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
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728371"
---
# <a name="security-frame-authorization--mitigations"></a>Beveiligingskader: Vergunning | Mitigaties 
| Product/service | Artikel |
| --------------- | ------- |
| **Grens van machinevertrouwensrelatie** | <ul><li>[Ervoor zorgen dat de juiste ACL's zijn geconfigureerd om ongeautoriseerde toegang tot gegevens op het apparaat te beperken](#acl-restricted-access)</li><li>[Ervoor zorgen dat gevoelige gebruikersspecifieke toepassingsinhoud wordt opgeslagen in de gebruikersprofielmap](#sensitive-directory)</li><li>[Controleren of de geïmplementeerde toepassingen worden uitgevoerd met de minste bevoegdheden](#deployed-privileges)</li></ul> |
| **Webtoepassing** | <ul><li>[Sequentiële stapvolgorde afdwingen bij het verwerken van bedrijfslogicastromen](#sequential-logic)</li><li>[Tariefbeperkend mechanisme implementeren om opsomming te voorkomen](#rate-enumeration)</li><li>[Ervoor zorgen dat de juiste autorisatie is ingevoerd en het principe van de minste privileges wordt gevolgd](#principle-least-privilege)</li><li>[Beslissingen over bedrijfslogica en toegangsautorisatie voor resources mogen niet worden gebaseerd op inkomende aanvraagparameters](#logic-request-parameters)</li><li>[Zorg ervoor dat inhoud en bronnen niet enumerable of toegankelijk zijn via krachtig browsen](#enumerable-browsing)</li></ul> |
| **Database** | <ul><li>[Ervoor zorgen dat accounts met de minste bevoegdheden worden gebruikt om verbinding te maken met de databaseserver](#privileged-server)</li><li>[Implementeren Row Level Security RLS om te voorkomen dat tenants toegang krijgen tot elkaars gegevens](#rls-tenants)</li><li>[Sysadmin-rol mag alleen geldige benodigde gebruikers hebben](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Verbinding maken met Cloud Gateway met de minst bevoorrechte tokens](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Een SAS-sleutel voor verzenden-machtigingen gebruiken voor het genereren van apparaattokens](#sendonly-sas)</li><li>[Gebruik geen toegangstokens die directe toegang bieden tot de Gebeurtenishub](#access-tokens-hub)</li><li>[Verbinding maken met eventhub met SAS-sleutels die de vereiste minimummachtigingen hebben](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Gebruik resourcetokens om waar mogelijk verbinding te maken met Azure Cosmos DB](#resource-docdb)</li></ul> |
| **Azure-vertrouwensgrens** | <ul><li>[Fijnkorrelig toegangsbeheer inschakelen voor Azure-abonnement met RBAC](#grained-rbac)</li></ul> |
| **Vertrouwensgrens servicestructuur** | <ul><li>[De toegang van de client tot clusterbewerkingen beperken met RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Beveiligingsmodellering uitvoeren en veldniveaubeveiliging waar nodig gebruiken](#modeling-field)</li></ul> |
| **Dynamics CRM-portal** | <ul><li>[Beveiligingsmodellering van portalaccounts uitvoeren, rekening houdend met het feit dat het beveiligingsmodel voor de portal verschilt van de rest van CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Fijnmazige toestemming verlenen voor een reeks entiteiten in Azure Table Storage](#permission-entities)</li><li>[RBAC (Role-Based Access Control) inschakelen voor Azure-opslagaccount met Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Mobiele client** | <ul><li>[Impliciete jailbreak- of rootdetectie implementeren](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Zwakke klasse referentie in WCF](#weak-class-wcf)</li><li>[Beheer van de autorisatieregeling voor WCF-Implement](#wcf-authz)</li></ul> |
| **Web-API** | <ul><li>[Het juiste autorisatiemechanisme implementeren in ASP.NET Web API](#authz-aspnet)</li></ul> |
| **IoT-apparaat** | <ul><li>[Autorisatiecontroles uitvoeren in het apparaat als het verschillende acties ondersteunt waarvoor verschillende machtigingsniveaus nodig zijn](#device-permission)</li></ul> |
| **IoT-veldgateway** | <ul><li>[Autorisatiecontroles uitvoeren in de Veldgateway als deze verschillende acties ondersteunt waarvoor verschillende machtigingsniveaus nodig zijn](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Ervoor zorgen dat de juiste ACL's zijn geconfigureerd om ongeautoriseerde toegang tot gegevens op het apparaat te beperken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Ervoor zorgen dat de juiste ACL's zijn geconfigureerd om ongeautoriseerde toegang tot gegevens op het apparaat te beperken|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Ervoor zorgen dat gevoelige gebruikersspecifieke toepassingsinhoud wordt opgeslagen in de gebruikersprofielmap

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat gevoelige gebruikersspecifieke toepassingsinhoud wordt opgeslagen in de gebruikersprofielmap. Dit om te voorkomen dat meerdere gebruikers van de machine toegang krijgen tot elkaars gegevens.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Controleren of de geïmplementeerde toepassingen worden uitgevoerd met de minste bevoegdheden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Controleer of de geïmplementeerde toepassing wordt uitgevoerd met de minste bevoegdheden. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Sequentiële stapvolgorde afdwingen bij het verwerken van bedrijfslogicastromen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Om te controleren of deze fase is doorlopen door een echte gebruiker, wilt u de toepassing afdwingen om alleen bedrijfslogicastromen in opeenvolgende stapvolgorde te verwerken, waarbij alle stappen in realistische menselijke tijd worden verwerkt en niet buiten de orde worden verwerkt, stappen worden overgeslagen , verwerkte stappen van een andere gebruiker of te snel ingediende transacties.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Tariefbeperkend mechanisme implementeren om opsomming te voorkomen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat gevoelige id's willekeurig zijn. Implementeer CAPTCHA-besturingselement op anonieme pagina's. Ervoor zorgen dat fout en uitzondering geen specifieke gegevens mogen onthullen|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Ervoor zorgen dat de juiste autorisatie is ingevoerd en het principe van de minste privileges wordt gevolgd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Het principe betekent dat een gebruikersaccount alleen die bevoegdheden geeft die essentieel zijn voor het feit dat gebruikers werken. Een back-upgebruiker hoeft bijvoorbeeld geen software te installeren: de back-upgebruiker heeft dus alleen rechten om back-up- en back-uptoepassingen uit te voeren. Alle andere bevoegdheden, zoals het installeren van nieuwe software, worden geblokkeerd. Het principe geldt ook voor een personal computer gebruiker die meestal werkt in een normaal gebruikersaccount, en opent een bevoorrechte, wachtwoord beveiligde account (dat wil zeggen, een superuser) alleen wanneer de situatie absoluut vraagt. </p><p>Dit principe kan ook worden toegepast op uw webapplicaties. In plaats van uitsluitend afhankelijk te zijn van verificatiemethoden op basis van rollen die sessies gebruiken, willen we gebruikers liever bevoegdheden toewijzen door middel van een databasegebaseerd verificatiesysteem. We gebruiken nog steeds sessies om te bepalen of de gebruiker correct is ingelogd, alleen nu in plaats van die gebruiker een specifieke rol toe te wijzen, geven we hem bevoegdheden om te controleren welke acties hij op het systeem mag uitvoeren. Ook een grote pro van deze methode is, wanneer een gebruiker moet worden toegewezen minder privileges uw wijzigingen zullen worden toegepast op de vlieg, omdat de toewijzing is niet afhankelijk van de sessie die anders moest eerst verlopen.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Beslissingen over bedrijfslogica en toegangsautorisatie voor resources mogen niet worden gebaseerd op inkomende aanvraagparameters

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Wanneer u controleert of een gebruiker is beperkt tot het controleren van bepaalde gegevens, moeten de toegangsbeperkingen worden verwerkt server-side. De userID moet worden opgeslagen in een sessievariabele bij het inloggen en moet worden gebruikt om gebruikersgegevens uit de database op te halen |

### <a name="example"></a>Voorbeeld
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nu kan een mogelijke aanvaller niet knoeien en de toepassingsbewerking wijzigen, omdat de id voor het ophalen van de gegevens serverzijde wordt verwerkt.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Zorg ervoor dat inhoud en bronnen niet enumerable of toegankelijk zijn via krachtig browsen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Gevoelige statische en configuratiebestanden mogen niet in de webhoofd worden bewaard. Om inhoud die niet openbaar hoeft te zijn, moeten de juiste toegangscontroles worden toegepast of moet de inhoud zelf worden verwijderd.</p><p>Ook wordt krachtig browsen meestal gecombineerd met Brute Force-technieken om informatie te verzamelen door te proberen zoveel mogelijk URL's te openen om mappen en bestanden op een server op te sommen. Aanvallers kunnen controleren op alle varianten van algemeen bestaande bestanden. Een wachtwoordbestandszoekopdracht omvat bijvoorbeeld bestanden zoals psswd.txt, password.htm, password.dat en andere varianten.</p><p>Om dit te beperken, moeten mogelijkheden voor detectie van brute force-pogingen worden opgenomen.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Ervoor zorgen dat accounts met de minste bevoegdheden worden gebruikt om verbinding te maken met de databaseserver

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [SQL Database-machtigingshiërarchie](https://msdn.microsoft.com/library/ms191465), [SQL-databasesecurables](https://msdn.microsoft.com/library/ms190401) |
| **Stappen** | Accounts met de minste bevoegdheden moeten worden gebruikt om verbinding te maken met de database. Het inloggen op de toepassing moet worden beperkt in de database en mag alleen geselecteerde opgeslagen procedures uitvoeren. De login van de toepassing mag geen directe toegang tot de tabel hebben. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementeren Row Level Security RLS om te voorkomen dat tenants toegang krijgen tot elkaars gegevens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Sql Azure, OnPrem |
| **Kenmerken**              | SQL-versie - V12, SQL-versie - MsSQL2016 |
| **Verwijzingen**              | [SQL Server Row-Level Security (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Stappen** | <p>Met beveiliging op rijniveau kunnen klanten de toegang tot rijen in een databasetabel beheren op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld groepslidmaatschap of uitvoeringscontext).</p><p>Row-Level Security (RLS) vereenvoudigt het ontwerp en de codering van beveiliging in uw toepassing. Met RLS kunt u beperkingen instellen voor de toegang tot gegevens in rijen. U kunt bijvoorbeeld bepalen dat werkrollen alleen toegang hebben tot de rijen met gegevens die relevant zijn voor hun afdeling, of de toegang van klanten beperken tot de gegevens die relevant zijn voor hun bedrijf.</p><p>De logica voor toegangsbeperking bevindt zich in de databaselaag in plaats van in plaats van in de buurt van de gegevens in een andere toepassingslaag. Het databasesysteem past de toegangsbeperkingen toe telkens wanneer gegevenstoegang vanaf een laag wordt geprobeerd. Dit maakt het beveiligingssysteem betrouwbaarder en robuuster door het verkleinen van de oppervlakte van het beveiligingssysteem.</p><p>|

Houd er rekening mee dat RLS als kant-en-klare databasefunctie alleen van toepassing is op SQL Server vanaf 2016 en Azure SQL-database. Als de out-of-the-box RLS-functie niet wordt geïmplementeerd, moet ervoor worden gezorgd dat de toegang tot gegevens wordt beperkt met behulp van weergaven en procedures

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Sysadmin-rol mag alleen geldige benodigde gebruikers hebben

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [SQL Database-machtigingshiërarchie](https://msdn.microsoft.com/library/ms191465), [SQL-databasesecurables](https://msdn.microsoft.com/library/ms190401) |
| **Stappen** | Leden van de vaste serverrol van SysAdmin moeten zeer beperkt zijn en nooit accounts bevatten die door toepassingen worden gebruikt.  Bekijk de lijst met gebruikers in de rol en verwijder onnodige accounts|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Verbinding maken met Cloud Gateway met de minst bevoorrechte tokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT Cloud Gateway | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Keuze voor gateway - Azure IoT Hub |
| **Verwijzingen**              | [Toegangsbeheer voor Iot-hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Stappen** | Geef de minste bevoegdheden voor verschillende onderdelen die verbinding maken met Cloud Gateway (IoT Hub). Typisch voorbeeld is : Apparaatbeheer/inrichtingscomponent maakt gebruik van registerread/write, Event Processor (ASA) maakt gebruik van Service Connect. Afzonderlijke apparaten maken verbinding met apparaatreferenties|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Een SAS-sleutel voor verzenden-machtigingen gebruiken voor het genereren van apparaattokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Event Hub | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Overzicht van verificatie en beveiligingsmodel van Gebeurtenishubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Stappen** | Een SAS-sleutel wordt gebruikt om individuele apparaattokens te genereren. Een SAS-sleutel voor alleen-verzendenmachtigingen gebruiken terwijl u het apparaattoken genereert voor een bepaalde uitgever|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Gebruik geen toegangstokens die directe toegang bieden tot de Gebeurtenishub

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Event Hub | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Overzicht van verificatie en beveiligingsmodel van Gebeurtenishubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Stappen** | Een token dat directe toegang tot de gebeurtenishub verleent, mag niet aan het apparaat worden gegeven. Het gebruik van een minst geprivilegieerd token voor het apparaat dat alleen toegang geeft tot een uitgever, zou helpen bij het identificeren en op de zwarte lijst worden geplaatst als wordt vastgesteld dat het een malafide of gecompromitteerd apparaat is.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Verbinding maken met eventhub met SAS-sleutels die de vereiste minimummachtigingen hebben

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Event Hub | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Overzicht van verificatie en beveiligingsmodel van Gebeurtenishubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Stappen** | Geef de minste bevoegdheden voor verschillende back-endtoepassingen die verbinding maken met de Gebeurtenishub. Genereer afzonderlijke SAS-sleutels voor elke back-endtoepassing en geef alleen de vereiste machtigingen - Verzenden, ontvangen of beheren ervan.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Gebruik resourcetokens om waar mogelijk verbinding te maken met Cosmos DB

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Document DB | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Een resourcetoken is gekoppeld aan een Azure Cosmos DB-machtigingsbron en legt de relatie vast tussen de gebruiker van een database en de toestemming die de gebruiker heeft voor een specifieke Azure Cosmos DB-toepassingsbron (bijvoorbeeld verzameling, document). Gebruik altijd een resourcetoken om toegang te krijgen tot de Azure Cosmos DB als de client niet kan worden vertrouwd met verwerkingsmaster- of alleen-lezensleutels, zoals een toepassing van een eindgebruiker zoals een mobiele of desktopclient. Gebruik mastertoetsen of alleen-lezen sleutels van backendtoepassingen die deze sleutels veilig kunnen opslaan.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Fijnkorrelig toegangsbeheer inschakelen voor Azure-abonnement met RBAC

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure-vertrouwensgrens | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Roltoewijzingen gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Stappen** | Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over geavanceerd toegangsbeheer voor Azure. Met RBAC kunt u alleen de toegangsrechten aan gebruikers verlenen die ze nodig hebben om hun taken uit te voeren.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>De toegang van de client tot clusterbewerkingen beperken met RBAC

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Vertrouwensgrens servicestructuur | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving - Azure |
| **Verwijzingen**              | [Op rollen gebaseerde toegangscontrole voor Service Fabric-clients](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Stappen** | <p>Azure Service Fabric ondersteunt twee verschillende typen toegangscontrole voor clients die zijn verbonden met een Service Fabric-cluster: beheerder en gebruiker. Met toegangsbeheer kan de clusterbeheerder de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers beperken, waardoor het cluster veiliger wordt.</p><p>Beheerders hebben volledige toegang tot beheermogelijkheden (inclusief lees-/schrijfmogelijkheden). Gebruikers hebben standaard alleen leestoegang tot beheermogelijkheden (bijvoorbeeld querymogelijkheden) en de mogelijkheid om toepassingen en services op te lossen.</p><p>U geeft de twee clientrollen (beheerder en client) op het moment van het maken van het cluster op door afzonderlijke certificaten voor elk te verstrekken.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Beveiligingsmodellering uitvoeren en veldniveaubeveiliging waar nodig gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Beveiligingsmodellering uitvoeren en veldniveaubeveiliging waar nodig gebruiken|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Beveiligingsmodellering van portalaccounts uitvoeren, rekening houdend met het feit dat het beveiligingsmodel voor de portal verschilt van de rest van CRM

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Dynamics CRM-portal | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Beveiligingsmodellering van portalaccounts uitvoeren, rekening houdend met het feit dat het beveiligingsmodel voor de portal verschilt van de rest van CRM|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Fijnmazige toestemming verlenen voor een reeks entiteiten in Azure Table Storage

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | StorageType - Tabel |
| **Verwijzingen**              | [Toegang tot objecten in uw Azure-opslagaccount delegeren met SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Stappen** | In bepaalde bedrijfsscenario's is Azure Table Storage mogelijk vereist om gevoelige gegevens op te slaan die geschikt zijn voor verschillende partijen. Bijvoorbeeld gevoelige gegevens met betrekking tot verschillende landen/regio's. In dergelijke gevallen kunnen SAS-handtekeningen worden samengesteld door de partitie- en rijsleutelbereiken op te geven, zodat een gebruiker toegang heeft tot gegevens die specifiek zijn voor een bepaald land/regio.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>RBAC (Role-Based Access Control) inschakelen voor Azure-opslagaccount met Azure Resource Manager

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Uw opslagaccount beveiligen met RBAC (Role-Based Access Control)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Stappen** | <p>Wanneer u een nieuw opslagaccount maakt, selecteert u een implementatiemodel van Classic of Azure Resource Manager. Het klassieke model voor het maken van resources in Azure biedt alleen alles-of-niets toegang tot het abonnement en op zijn beurt het opslagaccount.</p><p>Met het Azure Resource Manager-model plaatst u het opslagaccount in een brongroep en beheert u de toegang tot het beheervlak van dat specifieke opslagaccount met Azure Active Directory. U specifieke gebruikers bijvoorbeeld de mogelijkheid geven om toegang te krijgen tot de opslagaccountsleutels, terwijl andere gebruikers informatie over het opslagaccount kunnen bekijken, maar geen toegang hebben tot de opslagaccountsleutels.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Impliciete jailbreak- of rootdetectie implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Mobiele client | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Toepassing moet zijn eigen configuratie en gebruikersgegevens te beschermen in het geval als telefoon is geworteld of gevangenis gebroken. Rooting / jail breaking impliceert ongeautoriseerde toegang, die normale gebruikers niet zullen doen op hun eigen telefoons. Daarom moet de toepassing impliciete detectielogica hebben bij het opstarten van de toepassing, om te detecteren of de telefoon is geworteld.</p><p>De detectielogica kan eenvoudig toegang hebben tot bestanden die normaal gesproken alleen de hoofdgebruiker kunnen openen, bijvoorbeeld:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/systeem/bak/su</li><li>/systeem/xbin/su</li><li>/gegevens/lokaal/xbin/su</li><li>/data/local/bin/su</li><li>/systeem/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Als de toepassing toegang heeft tot een van deze bestanden, geeft dit aan dat de toepassing wordt uitgevoerd als hoofdgebruiker.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Zwakke klasse referentie in WCF

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Stappen** | <p>Het systeem maakt gebruik van een zwakke klasse referentie, die een aanvaller in staat zou kunnen stellen om ongeautoriseerde code uit te voeren. Het programma verwijst naar een door de gebruiker gedefinieerde klasse die niet uniek is geïdentificeerd. Wanneer .NET deze zwak geïdentificeerde klasse laadt, zoekt de CLR-typelader naar de klasse op de volgende locaties in de opgegeven volgorde:</p><ol><li>Als de assemblage van het type bekend is, doorzoekt de lader de omleidingslocaties van het configuratiebestand, GAC, de huidige assemblage met configuratiegegevens en de map met de basis van de toepassing</li><li>Als de assemblage onbekend is, zoekt de lader door de huidige assemblage, mscorlib en de locatie die is geretourneerd door de gebeurtenishandler TypeResolve</li><li>Deze CLR-zoekvolgorde kan worden gewijzigd met haken zoals het mechanisme Type doorschakelen en de gebeurtenis AppDomain.TypeResolve</li></ol><p>Als een aanvaller de CLR-zoekorder exploiteert door een alternatieve klasse met dezelfde naam te maken en deze op een alternatieve locatie te plaatsen die de CLR als eerste zal laden, zal de CLR onbedoeld de door de aanvaller geleverde code uitvoeren</p>|

### <a name="example"></a>Voorbeeld
Het `<behaviorExtensions/>` element van het wcf-configuratiebestand hieronder instrueert WCF om een aangepaste gedragsklasse toe te voegen aan een bepaalde WCF-extensie.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Het gebruik van volledig gekwalificeerde (sterke) namen identificeert op unieke wijze een type en verhoogt de beveiliging van uw systeem verder. Gebruik volledig gekwalificeerde verzamelnamen bij het registreren van typen in de bestanden van machine.config en app.config.

### <a name="example"></a>Voorbeeld
Het `<behaviorExtensions/>` element van het wcf-configuratiebestand hieronder instrueert WCF om de aangepaste gedragsklasse met een sterke verwijzing toe te voegen aan een bepaalde WCF-extensie.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>Beheer van de autorisatieregeling voor WCF-Implement

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Stappen** | <p>Deze service maakt geen gebruik van een autorisatiebesturingselement. Wanneer een client een bepaalde WCF-service aanroept, biedt WCF verschillende autorisatieschema's die controleren of de beller toestemming heeft om de servicemethode op de server uit te voeren. Als autorisatiebesturingselementen niet zijn ingeschakeld voor WCF-services, kan een geverifieerde gebruiker een escalatie van bevoegdheden bereiken.</p>|

### <a name="example"></a>Voorbeeld
In de volgende configuratie wordt WCF geïnstrueerd om het autorisatieniveau van de client niet te controleren bij het uitvoeren van de service:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Gebruik een serviceautorisatieschema om te controleren of de beller van de servicemethode daartoe gemachtigd is. WCF biedt twee modi en maakt de definitie van een aangepaste autorisatieregeling mogelijk. De modus WindowsGroepen gebruiken maakt gebruik van Windows-rollen en -gebruikers en de useAspNetRoles-modus gebruikt een ASP.NET-rolprovider, zoals SQL Server, om te verifiëren.

### <a name="example"></a>Voorbeeld
In de volgende configuratie wordt WCF geïnstrueerd om ervoor te zorgen dat de client deel uitmaakt van de groep Administrators voordat de service Toevoegen wordt uitgevoerd:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
De dienst wordt vervolgens als volgt gedeclareerd:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Het juiste autorisatiemechanisme implementeren in ASP.NET Web API

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, MVC5 |
| **Kenmerken**              | N/A, Identity Provider - ADFS, Identity Provider - Azure AD |
| **Verwijzingen**              | [Verificatie en autorisatie in ASP.NET Web-API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Stappen** | <p>Rolgegevens voor de gebruikers van de toepassing kunnen worden afgeleid uit Azure AD- of ADFS-claims als de toepassing op hen is gebaseerd, omdat de identiteitsprovider of de toepassing zelf deze kan bieden. In elk van deze gevallen moet de aangepaste autorisatie-implementatie de gebruikersrolgegevens valideren.</p><p>Rolgegevens voor de gebruikers van de toepassing kunnen worden afgeleid uit Azure AD- of ADFS-claims als de toepassing op hen is gebaseerd, omdat de identiteitsprovider of de toepassing zelf deze kan bieden. In elk van deze gevallen moet de aangepaste autorisatie-implementatie de gebruikersrolgegevens valideren.</p>

### <a name="example"></a>Voorbeeld
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Alle controllers en actiemethoden die moeten worden beschermd, moeten worden versierd met bovenkenmerk.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Autorisatiecontroles uitvoeren in het apparaat als het verschillende acties ondersteunt waarvoor verschillende machtigingsniveaus nodig zijn

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-apparaat | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | <p>Het apparaat moet de beller toestemming geven om te controleren of de beller over de vereiste machtigingen beschikt om de gevraagde actie uit te voeren. Bijvoorbeeld laten we zeggen dat het apparaat is een Smart Door Lock die kan worden gecontroleerd vanuit de cloud, plus het biedt functionaliteiten zoals op afstand vergrendelen van de deur.</p><p>Het Smart Door Lock biedt alleen ontgrendelingsfunctionaliteit wanneer iemand fysiek bij de deur komt met een kaart. In dit geval moet de implementatie van de afstandsbediening commando en controle worden gedaan op een zodanige wijze dat het geen functionaliteit om de deur te ontgrendelen als de cloud gateway is niet bevoegd om een commando om de deur te ontgrendelen sturen.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Autorisatiecontroles uitvoeren in de Veldgateway als deze verschillende acties ondersteunt waarvoor verschillende machtigingsniveaus nodig zijn

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-veldgateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | De Veldgateway moet de beller toestemming geven om te controleren of de beller over de vereiste machtigingen beschikt om de gevraagde actie uit te voeren. Er moeten bijvoorbeeld verschillende machtigingen zijn voor een beheergebruikersinterface/API die wordt gebruikt om een veldgateway v/s-apparaten te configureren die ermee verbinding maken.|
