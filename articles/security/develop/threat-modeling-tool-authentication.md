---
title: Verificatie - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
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
ms.openlocfilehash: 1bef73e6be4bdbe8828e1d20ea6e684759984627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72244637"
---
# <a name="security-frame-authentication--mitigations"></a>Beveiligingsframe: verificatie | Mitigaties 

| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing**    | <ul><li>[Overweeg een standaardverificatiemechanisme te gebruiken om te verifiëren bij webtoepassing](#standard-authn-web-app)</li><li>[Toepassingen moeten mislukte verificatiescenario's veilig afhandelen](#handle-failed-authn)</li><li>[Stap up- of adaptieve verificatie inschakelen](#step-up-adaptive-authn)</li><li>[Ervoor zorgen dat administratieve interfaces op de juiste manier zijn vergrendeld](#admin-interface-lockdown)</li><li>[Wachtwoordfunctionaliteiten voor vergeten wachtwoorden veilig implementeren](#forgot-pword-fxn)</li><li>[Ervoor zorgen dat wachtwoord- en accountbeleid worden geïmplementeerd](#pword-account-policy)</li><li>[Besturingselementen implementeren om opsommen van gebruikersnamen te voorkomen](#controls-username-enum)</li></ul> |
| **Database** | <ul><li>[Gebruik indien mogelijk Windows Authentication voor verbinding met SQL Server](#win-authn-sql)</li><li>[Gebruik waar mogelijk Azure Active Directory Authentication voor verbinding maken met SQL Database](#aad-authn-sql)</li><li>[Wanneer sql-verificatiemodus wordt gebruikt, moet u ervoor zorgen dat het account- en wachtwoordbeleid wordt afgedwongen op SQL-server](#authn-account-pword)</li><li>[SQL-verificatie niet gebruiken in opgenomen databases](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Verificatiereferenties per apparaat gebruiken met SaS-tokens](#authn-sas-tokens)</li></ul> |
| **Azure-vertrouwensgrens** | <ul><li>[Azure Multi-Factor Authentication inschakelen voor Azure-beheerders](#multi-factor-azure-admin)</li></ul> |
| **Vertrouwensgrens servicestructuur** | <ul><li>[Anonieme toegang tot het cluster servicestructuur beperken](#anon-access-cluster)</li><li>[Ervoor zorgen dat het certificaat van client-to-node van Service Fabric verschilt van het certificaat van knooppunt tot knooppunt](#fabric-cn-nn)</li><li>[AAD gebruiken om clients te authenticeren om fabricclusters te onderhouden](#aad-client-fabric)</li><li>[Ervoor zorgen dat servicefabriccertificaten worden verkregen bij een erkende certificeringsinstantie (CA)](#fabric-cert-ca)</li></ul> |
| **Identiteitsserver** | <ul><li>[Standaardverificatiescenario's gebruiken die worden ondersteund door Identity Server](#standard-authn-id)</li><li>[De standaard-tokencache van Identity Server overschrijven met een schaalbaar alternatief](#override-token)</li></ul> |
| **Grens van machinevertrouwensrelatie** | <ul><li>[Ervoor zorgen dat de binaire bestanden van geïmplementeerde toepassingen digitaal zijn ondertekend](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Verificatie inschakelen bij verbinding maken met MSMQ-wachtrijen in WCF](#msmq-queues)</li><li>[WCF-Stel Message clientCredentialType niet in op geen](#message-none)</li><li>[WCF-Stel niet 'TransportclientCredentialType' in op geen](#transport-none)</li></ul> |
| **Web-API** | <ul><li>[Ervoor zorgen dat standaardverificatietechnieken worden gebruikt om web-API's te beveiligen](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Standaardverificatiescenario's gebruiken die worden ondersteund door Azure Active Directory](#authn-aad)</li><li>[De standaardADAL-tokencache overschrijven met een schaalbaar alternatief](#adal-scalable)</li><li>[Ervoor zorgen dat TokenReplayCache wordt gebruikt om het opnieuw afspelen van ADAL-verificatietokens te voorkomen](#tokenreplaycache-adal)</li><li>[ADAL-bibliotheken gebruiken om tokenaanvragen van OAuth2-clients naar AAD (of on-premises AD) te beheren](#adal-oauth2)</li></ul> |
| **IoT-veldgateway** | <ul><li>[Apparaten verifiëren die verbinding maken met de Field Gateway](#authn-devices-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Ervoor zorgen dat apparaten die verbinding maken met cloudgateway worden geverifieerd](#authn-devices-cloud)</li><li>[Verificatiereferenties per apparaat gebruiken](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Zorg ervoor dat alleen de vereiste containers en blobs anonieme leestoegang krijgen](#req-containers-anon)</li><li>[Beperkte toegang verlenen tot objecten in Azure-opslag met SAS of SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Overweeg een standaardverificatiemechanisme te gebruiken om te verifiëren bij webtoepassing

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Verificatie is het proces waarbij een entiteit zijn identiteit bewijst, meestal via referenties, zoals een gebruikersnaam en wachtwoord. Er zijn meerdere verificatieprotocollen beschikbaar die kunnen worden overwogen. Sommigen van hen zijn hieronder vermeld:</p><ul><li>Clientcertificaten</li><li>Windows gebaseerd</li><li>Formulieren gebaseerd</li><li>Federatie - ADFS</li><li>Federatie - Azure AD</li><li>Federatie - Identiteitsserver</li></ul><p>Overweeg een standaardverificatiemechanisme te gebruiken om het bronproces te identificeren</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Toepassingen moeten mislukte verificatiescenario's veilig afhandelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Toepassingen die gebruikers expliciet verifiëren, moeten mislukte verificatiescenario's veilig verwerken. Het verificatiemechanisme moet:</p><ul><li>Toegang tot bevoegde bronnen weigeren wanneer verificatie mislukt</li><li>Een algemeen foutbericht weergeven nadat mislukte verificatie en geweigerde toegang plaatsvindt</li></ul><p>Test voor:</p><ul><li>Bescherming van bevoorrechte bronnen na mislukte aanmeldingen</li><li>Er wordt een algemeen foutbericht weergegeven op mislukte verificatie en toegang geweigerde gebeurtenis(en)</li><li>Accounts worden uitgeschakeld na een buitensporig aantal mislukte pogingen</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Stap up- of adaptieve verificatie inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Controleer of de toepassing aanvullende autorisatie heeft (zoals opvoeren of adaptieve verificatie, via multi-factor authenticatie zoals het verzenden van OTP in SMS, e-mail etc. of vragen om re-authenticatie), zodat de gebruiker wordt uitgedaagd voordat hij toegang krijgt tot gevoelige informatie. Deze regel is ook van toepassing op het aanbrengen van kritieke wijzigingen in een account of actie</p><p>Dit betekent ook dat de aanpassing van authenticatie moet worden uitgevoerd op een zodanige wijze dat de toepassing correct afdwingt context-gevoelige autorisatie om niet toe te staan ongeoorloofde manipulatie door middel van bijvoorbeeld, parameter knoeien</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Ervoor zorgen dat administratieve interfaces op de juiste manier zijn vergrendeld

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | De eerste oplossing is om alleen toegang te verlenen van een bepaald IP-bereik van de bron tot de administratieve interface. Als die oplossing niet mogelijk zou zijn dan is het altijd aanbevolen om een opstap- of adaptieve verificatie af te dwingen voor het inloggen op de administratieve interface |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Wachtwoordfunctionaliteiten voor vergeten wachtwoorden veilig implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Het eerste ding is om te controleren of vergeten wachtwoord en andere herstelpaden een link sturen met een tijdelijke activeringstoken in plaats van het wachtwoord zelf. Aanvullende verificatie op basis van soft-tokens (bijvoorbeeld SMS-token, native mobiele applicaties, enz.) kan ook worden vereist voordat de link wordt verzonden. Ten tweede moet u het gebruikersaccount niet vergrendelen terwijl het proces van het verkrijgen van een nieuw wachtwoord aan de gang is.</p><p>Dit kan leiden tot een Denial of service-aanval wanneer een aanvaller besluit om opzettelijk te vergrendelen van de gebruikers met een geautomatiseerde aanval. Ten derde, wanneer de nieuwe wachtwoordaanvraag is ingesteld, moet het bericht dat u weergeeft worden gegeneraliseerd om de opsomming van gebruikersnamen te voorkomen. Ten vierde, altijd verbieden het gebruik van oude wachtwoorden en implementeren van een sterk wachtwoord beleid.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Ervoor zorgen dat wachtwoord- en accountbeleid worden geïmplementeerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| Details | <p>Wachtwoord- en accountbeleid in overeenstemming met organisatiebeleid en aanbevolen procedures moeten worden geïmplementeerd.</p><p>Om te verdedigen tegen brute-force en woordenboek gebaseerd eis: Sterk wachtwoord beleid moet worden geïmplementeerd om ervoor te zorgen dat gebruikers complexe wachtwoord (bijvoorbeeld, 12 tekens minimale lengte, alfanumerieke en speciale tekens).</p><p>Het vergrendelingsbeleid voor accountkan op de volgende manier worden geïmplementeerd:</p><ul><li>**Zachte lock-out:** Dit kan een goede optie zijn om uw gebruikers te beschermen tegen brute force-aanvallen. Bijvoorbeeld, wanneer de gebruiker een verkeerd wachtwoord drie keer de toepassing kan vergrendelen van de rekening voor een minuut om het proces van brute dwingen hun wachtwoord waardoor het minder winstgevend voor de aanvaller om verder te gaan vertragen. Als u harde lock-out tegenmaatregelen voor dit voorbeeld zou u een "DoS" te bereiken door permanent vergrendelen van rekeningen. Als alternatief kan de toepassing genereren van een OTP (One Time Password) en stuur het out-of-band (via e-mail, sms etc.) naar de gebruiker. Een andere benadering kan zijn om CAPTCHA te implementeren nadat een drempel aantal mislukte pogingen is bereikt.</li><li>**Harde lock-out:** Dit type van lock-out moet worden toegepast wanneer u een gebruiker die uw toepassing aanvalt en hen tegengaat ontdekken door hen permanent uit hun rekening te sluiten tot een reactieteam tijd had om hun forensisch onderzoek te doen. Na dit proces u besluiten om de gebruiker terug te geven hun account of verdere juridische stappen tegen hen te nemen. Dit type aanpak voorkomt dat de aanvaller uw toepassing en infrastructuur verder doordringt.</li></ul><p>Als u zich wilt verdedigen tegen aanvallen op standaard- en voorspelbare accounts, controleert u of alle sleutels en wachtwoorden vervangbaar zijn en na de installatietijd worden gegenereerd of vervangen.</p><p>Als de toepassing wachtwoorden automatisch moet genereren, moet u ervoor zorgen dat de gegenereerde wachtwoorden willekeurig zijn en een hoge entropie hebben.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Besturingselementen implementeren om opsommen van gebruikersnamen te voorkomen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Alle foutmeldingen moeten worden gegeneraliseerd om gebruikersbenaming opsomming te voorkomen. Soms u ook niet voorkomen dat informatie lekt in functionaliteiten zoals een registratiepagina. Hier moet u tariefbeperkende methoden zoals CAPTCHA gebruiken om een geautomatiseerde aanval door een aanvaller te voorkomen. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Gebruik indien mogelijk Windows Authentication voor verbinding met SQL Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | OnPrem (OnPrem) |
| **Kenmerken**              | SQL-versie - Alles |
| **Verwijzingen**              | [SQL Server - Kies een verificatiemodus](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Stappen** | Windows Authentication maakt gebruik van kerberos beveiligingsprotocol, biedt wachtwoord beleid handhaving met betrekking tot complexiteit validatie voor sterke wachtwoorden, biedt ondersteuning voor account lockout, en ondersteunt wachtwoord verlopen.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Gebruik waar mogelijk Azure Active Directory Authentication voor verbinding maken met SQL Database

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | SQL Azure |
| **Kenmerken**              | SQL-versie - V12 |
| **Verwijzingen**              | [Verbinding maken met SQL-database met Azure Active Directory Authentication](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Stappen** | **Minimale versie:** Azure SQL Database V12 vereist om Azure SQL Database aad-verificatie te laten gebruiken tegen de Microsoft Directory |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Wanneer sql-verificatiemodus wordt gebruikt, moet u ervoor zorgen dat het account- en wachtwoordbeleid wordt afgedwongen op SQL-server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [SQL Server-wachtwoordbeleid](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Stappen** | Wanneer u SQL Server Authentication gebruikt, worden aanmeldingen gemaakt in SQL Server die niet zijn gebaseerd op Windows-gebruikersaccounts. Zowel de gebruikersnaam als het wachtwoord worden gemaakt met SQL Server en opgeslagen in SQL Server. SQL Server kan windows-wachtwoordbeleidsmechanismen gebruiken. Het kan dezelfde complexiteit en vervaldatumbeleid toepassen dat in Windows wordt gebruikt op wachtwoorden die in SQL Server worden gebruikt. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>SQL-verificatie niet gebruiken in opgenomen databases

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | OnPrem, SQL Azure |
| **Kenmerken**              | SQL-versie - MSSQL2012, SQL-versie - V12 |
| **Verwijzingen**              | [Aanbevolen procedures voor beveiliging met opgenomen databases](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Stappen** | Het ontbreken van een afgedwongen wachtwoordbeleid kan de kans vergroten dat een zwakke referentie in een opgenomen database wordt opgesteld. Maak gebruik van Windows-verificatie. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Verificatiereferenties per apparaat gebruiken met SaS-tokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Event Hub | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Overzicht van verificatie en beveiligingsmodel van Gebeurtenishubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Stappen** | <p>Het beveiligingsmodel van Event Hubs is gebaseerd op een combinatie van SAS-tokens (Shared Access Signature) en uitgevers van gebeurtenissen. De naam van de uitgever vertegenwoordigt de DeviceID die het token ontvangt. Dit zou helpen bij het koppelen van de tokens gegenereerd met de respectieve apparaten.</p><p>Alle berichten zijn gelabeld met initiator aan de service kant waardoor detectie van in-payload oorsprong spoofing pogingen. Wanneer u apparaten authenticeert, genereert u een SaS-token per apparaat die is uitgevoerd bij een unieke uitgever.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Azure Multi-Factor Authentication inschakelen voor Azure-beheerders

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure-vertrouwensgrens | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Wat is Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Stappen** | <p>Multi-factor authenticatie (MFA) is een verificatiemethode waarvoor meer dan één verificatiemethode nodig is en die een kritieke tweede beveiligingslaag toevoegt aan aanmeldingen en transacties van gebruikers. Het werkt door twee of meer van de volgende verificatiemethoden te vereisen:</p><ul><li>Iets wat u weet (meestal een wachtwoord)</li><li>Iets wat je hebt (een vertrouwd apparaat dat niet gemakkelijk wordt gedupliceerd, zoals een telefoon)</li><li>Iets wat je bent (biometrie)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Anonieme toegang tot het cluster servicestructuur beperken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Vertrouwensgrens servicestructuur | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving - Azure  |
| **Verwijzingen**              | [Beveiligingsscenario's voor servicefabric-cluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Stappen** | <p>Clusters moeten altijd worden beveiligd om te voorkomen dat onbevoegde gebruikers verbinding maken met uw cluster, vooral wanneer er productieworkloads op worden uitgevoerd.</p><p>Controleer tijdens het maken van een cluster van servicefabric's of de beveiligingsmodus is ingesteld op 'beveiligen' en configureert het vereiste X.509-servercertificaat. Als u een 'onveilig' cluster maakt, kan elke anonieme gebruiker er verbinding mee maken als het beheereindpunten blootstelt aan het openbare internet.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Ervoor zorgen dat het certificaat van client-to-node van Service Fabric verschilt van het certificaat van knooppunt tot knooppunt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Vertrouwensgrens servicestructuur | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving - Azure, Omgeving - Sta op |
| **Verwijzingen**              | [Beveiliging van client-to-no-nodecertificaten](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security)van Service Fabric, [Verbinding maken met een beveiligd cluster met clientcertificaat](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Stappen** | <p>Client-to-node-certificaatbeveiliging wordt geconfigureerd tijdens het maken van het cluster via de Azure-portal, Resource Manager-sjablonen of een zelfstandige JSON-sjabloon door een beheerclientcertificaat en/of een gebruikersclientcertificaat op te geven.</p><p>De door u opgegeven beheerdersclient- en gebruikersclientcertificaten moeten anders zijn dan de primaire en secundaire certificaten die u opgeeft voor beveiliging van knooppunt tot knooppunt.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>AAD gebruiken om clients te authenticeren om fabricclusters te onderhouden

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Vertrouwensgrens servicestructuur | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving - Azure |
| **Verwijzingen**              | [Clusterbeveiligingsscenario's - Beveiligingsaanbevelingen](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Stappen** | Clusters die op Azure worden uitgevoerd, kunnen ook toegang tot de beheereindpunten beveiligen met Azure Active Directory (AAD), afgezien van clientcertificaten. Voor Azure-clusters wordt aanbevolen aad-beveiliging te gebruiken om clients en certificaten te verifiëren voor beveiliging tussen knooppunten en services.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Ervoor zorgen dat servicefabriccertificaten worden verkregen bij een erkende certificeringsinstantie (CA)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Vertrouwensgrens servicestructuur | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Omgeving - Azure |
| **Verwijzingen**              | [X.509-certificaten en servicestof](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Stappen** | <p>Service Fabric gebruikt X.509-servercertificaten voor het verifiëren van knooppunten en clients.</p><p>Enkele belangrijke dingen om te overwegen tijdens het gebruik van certificaten in servicestoffen:</p><ul><li>Certificaten die worden gebruikt in clusters met productieworkloads, moeten worden gemaakt met behulp van een correct geconfigureerde Windows Server-certificaatservice of die zijn verkregen bij een erkende certificaatautoriteit (CA). De CA kan een goedgekeurde externe CA zijn of een goed beheerde interne openbare sleutelinfrastructuur (PKI)</li><li>Gebruik nooit tijdelijke of testcertificaten in productie die zijn gemaakt met tools zoals MakeCert.exe</li><li>U een zelfondertekend certificaat gebruiken, maar moet dit alleen doen voor testclusters en niet in productie</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Standaardverificatiescenario's gebruiken die worden ondersteund door Identity Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteitsserver | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [IdentityServer3 - The Big Picture](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Stappen** | <p>Hieronder vindt u de typische interacties die worden ondersteund door Identity Server:</p><ul><li>Browsers communiceren met webtoepassingen</li><li>Webapplicaties communiceren met web API's (soms alleen, soms namens een gebruiker)</li><li>Browsergebaseerde toepassingen communiceren met web-API's</li><li>Native applicaties communiceren met web API's</li><li>Servergebaseerde toepassingen communiceren met web-API's</li><li>Web API's communiceren met web API's (soms alleen, soms namens een gebruiker)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>De standaard-tokencache van Identity Server overschrijven met een schaalbaar alternatief

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Identiteitsserver | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Implementatie van identiteitsserver - Caching](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Stappen** | <p>IdentityServer heeft een eenvoudige ingebouwde cache in het geheugen. Hoewel dit goed is voor kleinschalige native apps, schaalt het niet op voor mid-tier- en backend-toepassingen om de volgende redenen:</p><ul><li>Deze toepassingen zijn toegankelijk voor veel gebruikers tegelijk. Het opslaan van alle toegangstokens in dezelfde winkel zorgt voor isolatieproblemen en brengt uitdagingen met zich mee wanneer ze op schaal werken: veel gebruikers, elk met evenveel tokens als de bronnen die de app namens hen opent, kunnen enorme aantallen en zeer dure opzoekbewerkingen betekenen</li><li>Deze toepassingen worden meestal geïmplementeerd op gedistribueerde topologieën, waarbij meerdere knooppunten toegang moeten hebben tot dezelfde cache</li><li>Gecachede tokens moeten procesrecyclën en deactiveringen overleven</li><li>Om alle bovenstaande redenen wordt het aanbevolen om tijdens het implementeren van web-apps de tokencache van de standaardidentityserver te overschrijven met een schaalbaar alternatief, zoals Azure Cache voor Redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Ervoor zorgen dat de binaire bestanden van geïmplementeerde toepassingen digitaal zijn ondertekend

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Grens van machinevertrouwensrelatie | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Ervoor zorgen dat de binaire bestanden van de geïmplementeerde toepassing digitaal worden ondertekend, zodat de integriteit van de binaire bestanden kan worden geverifieerd|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Verificatie inschakelen bij verbinding maken met MSMQ-wachtrijen in WCF

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, NET Framework 3 |
| **Kenmerken**              | N.v.t. |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Stappen** | Het programma kan verificatie niet inschakelen wanneer hij verbinding maakt met MSMQ-wachtrijen, een aanvaller kan anoniem berichten indienen in de wachtrij voor verwerking. Als verificatie niet wordt gebruikt om verbinding te maken met een MSMQ-wachtrij die wordt gebruikt om een bericht naar een ander programma te verzenden, kan een aanvaller een anoniem bericht verzenden dat kwaadaardig is.|

### <a name="example"></a>Voorbeeld
Het `<netMsmqBinding/>` element van het wcf-configuratiebestand hieronder instrueert WCF om verificatie uit te schakelen wanneer u verbinding maakt met een MSMQ-wachtrij voor berichtbezorging.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Configureer MSMQ om te allen tijde verificatie van Windows-domein of certificaat te vereisen voor binnenkomende of uitgaande berichten.

### <a name="example"></a>Voorbeeld
Het `<netMsmqBinding/>` element van het wcf-configuratiebestand hieronder instrueert WCF om certificaatverificatie in te schakelen wanneer u verbinding maakt met een MSMQ-wachtrij. De client is geverifieerd met X.509-certificaten. Het clientcertificaat moet aanwezig zijn in het certificaatarchief van de server.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Stel Message clientCredentialType niet in op geen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | Clientreferentietype - Geen |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Stappen** | Het ontbreken van verificatie betekent dat iedereen toegang heeft tot deze service. Een service die zijn clients niet verifieert, geeft toegang tot alle gebruikers. Configureer de toepassing om te verifiëren op basis van clientreferenties. Dit kan worden gedaan door het bericht clientCredentialType in te stellen op Windows of Certificaat. |

### <a name="example"></a>Voorbeeld
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-Stel niet 'TransportclientCredentialType' in op geen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | WCF | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, .NET Framework 3 |
| **Kenmerken**              | Clientreferentietype - Geen |
| **Verwijzingen**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Stappen** | Het ontbreken van verificatie betekent dat iedereen toegang heeft tot deze service. Een service die zijn clients niet verifieert, geeft alle gebruikers toegang tot de functionaliteit. Configureer de toepassing om te verifiëren op basis van clientreferenties. Dit kan door de transportclientCredentialType in te stellen naar Windows of Certificaat. |

### <a name="example"></a>Voorbeeld
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Ervoor zorgen dat standaardverificatietechnieken worden gebruikt om web-API's te beveiligen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web-API | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Verificatie en autorisatie in ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), Externe [verificatieservices met ASP.NET Web API (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Stappen** | <p>Verificatie is het proces waarbij een entiteit zijn identiteit bewijst, meestal via referenties, zoals een gebruikersnaam en wachtwoord. Er zijn meerdere verificatieprotocollen beschikbaar die kunnen worden overwogen. Sommigen van hen zijn hieronder vermeld:</p><ul><li>Clientcertificaten</li><li>Windows gebaseerd</li><li>Formulieren gebaseerd</li><li>Federatie - ADFS</li><li>Federatie - Azure AD</li><li>Federatie - Identiteitsserver</li></ul><p>Koppelingen in de sectie Verwijzingen bieden details op laag niveau over hoe elk van de verificatieschema's kan worden geïmplementeerd om een web-API te beveiligen.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Standaardverificatiescenario's gebruiken die worden ondersteund door Azure Active Directory

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Verificatiescenario's voor Azure AD,](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) [Azure Active Directory Code Samples](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), Azure Active [Directory-ontwikkelaarshandleiding](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Stappen** | <p>Azure Active Directory (Azure AD) vereenvoudigt verificatie voor ontwikkelaars door identiteit als service te bieden, met ondersteuning voor industriestandaardprotocollen zoals OAuth 2.0 en OpenID Connect. Hieronder vindt u de vijf primaire toepassingsscenario's die worden ondersteund door Azure AD:</p><ul><li>Webbrowser naar webtoepassing: een gebruiker moet zich aanmelden bij een webtoepassing die is beveiligd door Azure AD</li><li>Single Page Application (SPA): een gebruiker moet zich aanmelden bij één paginatoepassing die is beveiligd door Azure AD</li><li>Native Application to Web API: een native toepassing die wordt uitgevoerd op een telefoon, tablet of pc moet een gebruiker verifiëren om bronnen te krijgen van een web-API die is beveiligd door Azure AD</li><li>Webtoepassing naar web-API: een webtoepassing moet resources krijgen uit een web-API die is beveiligd door Azure AD</li><li>Daemon of Server Application to Web API: een daemon-toepassing of een servertoepassing zonder webgebruikersinterface moet resources krijgen van een web-API die is beveiligd door Azure AD</li></ul><p>Raadpleeg de links in de rubriek referenties voor implementatiegegevens op laag niveau</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>De standaardADAL-tokencache overschrijven met een schaalbaar alternatief

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Moderne verificatie met Azure Active Directory voor webtoepassingen](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [Met Redis als ADAL-tokencache](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Stappen** | <p>De standaardcache die ADAL (Active Directory Authentication Library) gebruikt, is een cache in het geheugen die is gebaseerd op een statisch archief dat procesbreed beschikbaar is. Hoewel dit werkt voor native toepassingen, wordt het niet geschaald voor mid-tier- en backendtoepassingen om de volgende redenen:</p><ul><li>Deze toepassingen zijn toegankelijk voor veel gebruikers tegelijk. Het opslaan van alle toegangstokens in dezelfde winkel zorgt voor isolatieproblemen en brengt uitdagingen met zich mee wanneer ze op schaal werken: veel gebruikers, elk met evenveel tokens als de bronnen die de app namens hen opent, kunnen enorme aantallen en zeer dure opzoekbewerkingen betekenen</li><li>Deze toepassingen worden meestal geïmplementeerd op gedistribueerde topologieën, waarbij meerdere knooppunten toegang moeten hebben tot dezelfde cache</li><li>Gecachede tokens moeten procesrecyclën en deactiveringen overleven</li></ul><p>Om al de bovenstaande redenen wordt tijdens het implementeren van web-apps aanbevolen om de standaard ADAL-tokencache te overschrijven met een schaalbaar alternatief, zoals Azure Cache voor Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Ervoor zorgen dat TokenReplayCache wordt gebruikt om het opnieuw afspelen van ADAL-verificatietokens te voorkomen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [Moderne verificatie met Azure Active Directory voor webtoepassingen](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Stappen** | <p>Met de eigenschap TokenReplayCache kunnen ontwikkelaars een token replay-cache definiëren, een winkel die kan worden gebruikt voor het opslaan van tokens om te controleren of geen token meer dan één keer kan worden gebruikt.</p><p>Dit is een maatregel tegen een veelvoorkomende aanval, de toepasselijk genaamde token replay-aanval: een aanvaller die het token onderschept dat bij aanmelding is verzonden, kan proberen het opnieuw naar de app te sturen ("replay" het) voor het instellen van een nieuwe sessie. In oidc code-grant flow wordt bijvoorbeeld na succesvolle gebruikersverificatie een verzoek gedaan om "/signin-oidc" eindpunt van de relying party te maken met "id_token", "code" en "state" parameters.</p><p>De relying party valideert dit verzoek en stelt een nieuwe sessie in. Als een tegenstander dit verzoek vastlegt en opnieuw speelt, kan hij/zij een geslaagde sessie instellen en de gebruiker spoofen. De aanwezigheid van de nonce in OpenID Connect kan de omstandigheden waarin de aanval met succes kan worden uitgevoerd beperken, maar niet volledig elimineren. Om hun toepassingen te beschermen, kunnen ontwikkelaars een implementatie van ITokenReplayCache bieden en een instantie toewijzen aan TokenReplayCache.</p>|

### <a name="example"></a>Voorbeeld
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Voorbeeld
Hier is een voorbeeld implementatie van de ITokenReplayCache interface. (Pas uw projectspecifieke cachingframework aan en implementeer deze)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
De geïmplementeerde cache moet als volgt worden verwezen in OIDC-opties via de eigenschap "TokenValidationParameters".
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Houd er rekening mee dat om de effectiviteit van deze configuratie te testen, `"/signin-oidc"` in te loggen op uw lokale OIDC-beschermde toepassing en het verzoek om eindpunt in fiddler vast te leggen. Wanneer de beveiliging niet aanwezig is, zal het opnieuw afspelen van dit verzoek in fiddler een nieuwe sessiecookie instellen. Wanneer het verzoek opnieuw wordt afgespeeld nadat de TokenReplayCache-beveiliging is toegevoegd, wordt er als volgt een uitzondering gemaakt:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>ADAL-bibliotheken gebruiken om tokenaanvragen van OAuth2-clients naar AAD (of on-premises AD) te beheren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure AD | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Stappen** | <p>Met de Azure AD-verificatiebibliotheek (ADAL) kunnen ontwikkelaars van clienttoepassingen eenvoudig gebruikers verifiëren naar cloud- of on-premises Active Directory (AD) en vervolgens toegangstokens verkrijgen voor het beveiligen van API-aanroepen.</p><p>ADAL heeft veel functies die verificatie gemakkelijker maken voor ontwikkelaars, zoals asynchrone ondersteuning, een configureerbare tokencache die toegangstokens opslaat en tokens vernieuwt, automatische tokenvernieuwing wanneer een toegangstoken verloopt en er een vernieuwingstoken beschikbaar is, en Meer.</p><p>Door het grootste deel van de complexiteit te verwerken, kan ADAL een ontwikkelaar helpen zich te concentreren op bedrijfslogica in hun toepassing en eenvoudig resources beveiligen zonder een expert op het gebied van beveiliging te zijn. Er zijn aparte bibliotheken beschikbaar voor .NET, JavaScript (client en Node.js), Python, iOS, Android en Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Apparaten verifiëren die verbinding maken met de Field Gateway

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT-veldgateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Verwijzingen**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat elk apparaat wordt geverifieerd door de Field Gateway voordat u gegevens van hen accepteert en voordat upstream-communicatie met de Cloud Gateway wordt vergemakkelijkt. Zorg er ook voor dat apparaten verbinding maken met een referentie per apparaat, zodat afzonderlijke apparaten op unieke wijze kunnen worden geïdentificeerd.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Ervoor zorgen dat apparaten die verbinding maken met cloudgateway worden geverifieerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT Cloud Gateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Generiek, C#, Node.JS,  |
| **Kenmerken**              | N/A, Gateway-keuze - Azure IoT Hub |
| **Verwijzingen**              | N/A, [Azure IoT-hub met .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Aan de slag met IoT-hub en Node JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [IoT beveiligen met SAS en certificaten,](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) [Git-repository](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Stappen** | <ul><li>**Generiek:** Verifieer het apparaat met TLS (Transport Layer Security) of IPSec. Infrastructuur moet ondersteuning bieden voor het gebruik van pre-shared key (PSK) op apparaten die volledige asymmetrische cryptografie niet aankunnen. Maak gebruik van Azure AD, Oauth.</li><li>**C#:** Bij het maken van een DeviceClient-exemplaar maakt de methode Maken standaard een DeviceClient-exemplaar dat het AMQP-protocol gebruikt om te communiceren met IoT Hub. Als u het HTTPS-protocol wilt gebruiken, dient u de Create-methode te overschrijven. Zo kunt u zelf het protocol bepalen. Als u het HTTPS-protocol gebruikt, `Microsoft.AspNet.WebApi.Client` moet u ook het `System.Net.Http.Formatting` NuGet-pakket aan uw project toevoegen om de naamruimte op te nemen.</li></ul>|

### <a name="example"></a>Voorbeeld
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Voorbeeld
**Node.JS: Verificatie**
#### <a name="symmetric-key"></a>Symmetrische sleutel
* Een IoT-hub maken op azure
* Een vermelding maken in het identiteitsregister van het apparaat
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Een gesimuleerd apparaat maken
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS-token
* Wordt intern gegenereerd bij het gebruik van symmetrische sleutel, maar we kunnen het genereren en expliciet gebruiken ook
* Een protocol definiëren:`var Http = require('azure-iot-device-http').Http;`
* Maak een sas-token:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Maak verbinding met sas-token: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Certificaten
* Een zelfondertekend X509-certificaat genereren met behulp van een tool zoals OpenSSL om respectievelijk een .cert- en .key-bestanden te genereren om het certificaat en de sleutel op te slaan
* Inrichten van een apparaat dat beveiligde verbinding accepteert met behulp van certificaten.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Een apparaat verbinden met een certificaat
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Verificatiereferenties per apparaat gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | IoT Cloud Gateway  | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Keuze voor gateway - Azure IoT Hub |
| **Verwijzingen**              | [Azure IoT Hub-beveiligingstokens](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Stappen** | Gebruik verificatiereferenties per apparaat met SaS-tokens op basis van apparaatsleutel of clientcertificaat, in plaats van het beleid voor gedeelde toegangsbeleid op IoT-hubniveau. Dit voorkomt het hergebruik van verificatietokens van het ene apparaat of veldgateway door een ander |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Zorg ervoor dat alleen de vereiste containers en blobs anonieme leestoegang krijgen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | StorageType - Blob |
| **Verwijzingen**              | [Anonieme leestoegang tot containers en blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/)beheren, [gedeelde toegangshandtekeningen, deel 1: Inzicht in het SAS-model](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Stappen** | <p>Standaard kunnen een container en eventuele blobs erin alleen worden geopend door de eigenaar van het opslagaccount. Om anonieme gebruikers leesmachtigingen te geven aan een container en de blobs, kan men de containermachtigingen instellen om openbare toegang toe te staan. Anonieme gebruikers kunnen blobs lezen in een openbaar toegankelijke container zonder het verzoek te verifiëren.</p><p>Containers bieden de volgende opties voor het beheren van containertoegang:</p><ul><li>Volledige openbare leestoegang: Container- en blobgegevens kunnen worden gelezen via een anoniem verzoek. Clients kunnen blobs in de container opsommen via een anoniem verzoek, maar kunnen geen containers opsommen binnen het opslagaccount.</li><li>Alleen openbare leestoegang voor blobs: Blob-gegevens in deze container kunnen worden gelezen via een anoniem verzoek, maar containergegevens zijn niet beschikbaar. Clients kunnen blobs in de container niet opsommen via een anoniem verzoek</li><li>Geen openbare leestoegang: container- en blobgegevens kunnen alleen door de eigenaar van het account worden gelezen</li></ul><p>Anonieme toegang is het beste voor scenario's waarin bepaalde blobs altijd beschikbaar moeten zijn voor anonieme leestoegang. Voor fijnmazige besturingselementen kan men een handtekening voor gedeelde toegang maken, waarmee beperkte toegang kan worden gedelegeerd met behulp van verschillende machtigingen en over een opgegeven tijdsinterval. Zorg ervoor dat containers en blobs, die mogelijk gevoelige gegevens bevatten, niet per ongeluk anonieme toegang krijgen</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Beperkte toegang verlenen tot objecten in Azure-opslag met SAS of SAP

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Azure Storage | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t. |
| **Verwijzingen**              | [Gedeelde toegangshandtekeningen, deel 1: Inzicht in het SAS-model,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) [Gedeelde toegangshandtekeningen, deel 2: Een SAS maken en gebruiken met Blob-opslag](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [Toegang delegeren tot objecten in uw account met behulp van gedeelde toegangshandtekeningen en beleid voor opgeslagen toegang](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Stappen** | <p>Het gebruik van een gedeelde toegangshandtekening (SAS) is een krachtige manier om beperkte toegang tot objecten in een opslagaccount te verlenen aan andere clients, zonder dat u de accounttoegangssleutel hoeft bloot te leggen. De SAS is een URI die in de queryparameters alle informatie omvat die nodig is voor geverifieerde toegang tot een opslagbron. Om toegang te krijgen tot opslagbronnen met de SAS, hoeft de client alleen in de SAS door te geven aan de juiste constructor of methode.</p><p>U een SAS gebruiken wanneer u toegang wilt bieden tot bronnen in uw opslagaccount aan een client die niet kan worden vertrouwd met de accountsleutel. Uw opslagaccountsleutels bevatten zowel een primaire als een secundaire sleutel, die beide administratieve toegang verlenen tot uw account en alle bronnen die erin staan. Het blootstellen van een van uw accountsleutels opent uw account voor de mogelijkheid van kwaadaardig of nalatig gebruik. Gedeelde toegangshandtekeningen bieden een veilig alternatief waarmee andere clients gegevens in uw opslagaccount kunnen lezen, schrijven en verwijderen op basis van de machtigingen die u hebt verleend en zonder de accountsleutel.</p><p>Als u telkens een logische set parameters hebt die vergelijkbaar zijn, is het gebruik van een SAP (Stored Access Policy) een beter idee. Omdat het gebruik van een SAS die is afgeleid van een beleid voor opgeslagen toegang u de mogelijkheid biedt om die SAS onmiddellijk in te trekken, is het de aanbevolen aanbevolen aanbevolen manier om altijd beleid voor opgeslagen toegang te gebruiken wanneer dat mogelijk is.</p>|
