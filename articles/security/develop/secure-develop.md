---
title: Veilige toepassingen ontwikkelen op Microsoft Azure
description: In dit artikel worden best practices besproken om rekening mee te houden tijdens de implementatie- en verificatiefasen van uw webtoepassingsproject.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 03f5b0124f95465c4a5da5043364a2f5816dae62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685753"
---
# <a name="develop-secure-applications-on-azure"></a>Beveiligde toepassingen ontwikkelen in Azure
In dit artikel presenteren we beveiligingsactiviteiten en besturingselementen om rekening mee te houden wanneer u toepassingen voor de cloud ontwikkelt. Beveiligingsvragen en concepten die moeten worden overwogen tijdens de implementatie- en verificatiefasen van de Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) komen aan bod. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-services die u gebruiken om een veiligere toepassing te ontwikkelen.

De volgende SDL-fasen komen aan bod in dit artikel:

- Implementatie
- Verificatie

## <a name="implementation"></a>Implementatie
De focus van de implementatiefase ligt op het vaststellen van best practices voor vroegtijdige preventie en het opsporen en verwijderen van beveiligingsproblemen uit de code.
Stel dat uw toepassing zal worden gebruikt op een manier die u niet van plan om te worden gebruikt. Dit helpt u te beschermen tegen onbedoeld of opzettelijk misbruik van uw toepassing.

### <a name="perform-code-reviews"></a>Codebeoordelingen uitvoeren

Voordat u de code incheckt, voert u [codebeoordelingen](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) uit om de algehele codekwaliteit te verhogen en het risico op het maken van bugs te verminderen. U [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) gebruiken om het codecontroleproces te beheren.

### <a name="perform-static-code-analysis"></a>Statische codeanalyse uitvoeren

[Statische codeanalyse](https://www.owasp.org/index.php/Static_Code_Analysis) (ook wel *broncodeanalyse*genoemd) wordt meestal uitgevoerd als onderdeel van een codecontrole. Statische code analyse verwijst vaak naar het uitvoeren van statische code analyse tools om potentiële kwetsbaarheden in niet-lopende code te vinden met behulp van technieken zoals [taint checking](https://en.wikipedia.org/wiki/Taint_checking) en data [flow analyse](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace biedt [hulpprogramma's voor ontwikkelaars](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) die statische codeanalyse uitvoeren en helpen bij codebeoordelingen.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Alle input voor uw toepassing valideren en ontsmetten

Behandel alle invoer als niet-vertrouwd om uw toepassing te beschermen tegen de meest voorkomende kwetsbaarheden in webapplicaties. Niet-vertrouwde gegevens zijn een voertuig voor injectieaanvallen. Invoer voor uw toepassing omvat parameters in de URL, invoer van de gebruiker, gegevens uit de database of uit een API, en alles wat wordt doorgegeven in dat een gebruiker mogelijk kan manipuleren. Een toepassing moet [valideren](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) dat gegevens syntactisch en semantisch geldig zijn voordat de toepassing de gegevens op enigerlei wijze gebruikt (inclusief het weergeven ervan aan de gebruiker).

Valideer invoer vroeg in de gegevensstroom om ervoor te zorgen dat alleen goed gevormde gegevens de workflow binnenkomen. U wilt niet dat misvormde gegevens in uw database blijven bestaan of een storing veroorzaken in een downstreamcomponent.

Blacklisting en whitelisting zijn twee algemene benaderingen voor het uitvoeren van input syntaxvalidatie:

  - Blacklisting pogingen om te controleren of een bepaalde gebruiker input bevat geen "bekend als kwaadaardig" inhoud.

  - Whitelisting probeert te controleren of een bepaalde gebruikersinvoer overeenkomt met een set "bekende goede" ingangen. Op tekens gebaseerde whitelisting is een vorm van whitelisting waarbij een toepassing controleert of gebruikersinvoer alleen "bekende goede" tekens bevat of die invoer overeenkomt met een bekende indeling.
    Dit kan bijvoorbeeld inhouden dat u controleert of een gebruikersnaam alleen alfanumerieke tekens bevat of dat deze precies twee getallen bevat.

Whitelisting is de voorkeursbenadering voor het bouwen van veilige software.
Zwarte lijst is gevoelig voor fouten, omdat het onmogelijk is om te denken van een volledige lijst van potentieel slechte input.

Doe dit werk op de server, niet aan de clientkant (of op de server en aan de clientzijde).

### <a name="verify-your-applications-outputs"></a>De uitvoer van uw toepassing controleren

Elke uitvoer die u visueel of in een document presenteert, moet altijd worden gecodeerd en ontsnapt. [Ontsnappen](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), ook wel bekend als *uitvoercodering*, wordt gebruikt om ervoor te zorgen dat niet-vertrouwde gegevens geen voertuig zijn voor een injectieaanval. Ontsnappen, gecombineerd met gegevensvalidatie, biedt gelaagde verdedigingen om de beveiliging van het systeem als geheel te verhogen.

Ontsnappen zorgt ervoor dat alles wordt weergegeven als *output.* Ontsnappen laat de tolk ook weten dat de gegevens niet bedoeld zijn om te worden uitgevoerd, en dit voorkomt dat aanvallen werken. Dit is een andere veel voorkomende aanval techniek genaamd *cross-site scripting* (XSS).

Als u een webframework van een derde partij gebruikt, u uw opties voor uitvoercodering op websites verifiëren met behulp van het [spiekbriefje owasp XSS-preventie.](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Parameterquery's gebruiken wanneer u contact opneemt met de database

Maak nooit een inline database query "on the fly" in uw code en stuur deze rechtstreeks naar de database. Schadelijke code die in uw toepassing is ingevoegd, kan ertoe leiden dat uw database wordt gestolen, gewist of gewijzigd. Uw toepassing kan ook worden gebruikt om kwaadaardige besturingssysteemopdrachten uit te voeren op het besturingssysteem dat uw database host.

Gebruik in plaats daarvan parameterquery's of opgeslagen procedures. Wanneer u geparameteriseerde query's gebruikt, u de procedure van uw code veilig aanroepen en deze een tekenreeks doorgeven zonder u zorgen te maken dat deze wordt behandeld als onderdeel van de queryinstructie.

### <a name="remove-standard-server-headers"></a>Standaardserverkoppen verwijderen

Kopteksten zoals Server, X-Powered-By en X-AspNet-Version onthullen informatie over de server en onderliggende technologieën. We raden u aan deze headers te onderdrukken om te voorkomen dat de toepassing wordt afweten.
Zie [het verwijderen van standaardserverkoppen op Azure-websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Uw productiegegevens scheiden

Uw productiegegevens, of "echte" gegevens, mogen niet worden gebruikt voor ontwikkeling, testen of enig ander doel dan wat het bedrijf voor ogen had. Een gemaskeerde[(geanonimiseerde)](https://en.wikipedia.org/wiki/Data_anonymization)dataset moet worden gebruikt voor alle ontwikkeling en testen.

Dit betekent dat minder mensen toegang hebben tot uw echte gegevens, wat uw aanvalsoppervlak vermindert. Het betekent ook dat minder werknemers persoonlijke gegevens zien, waardoor een mogelijk schending van de vertrouwelijkheid wordt geëlimineerd.

### <a name="implement-a-strong-password-policy"></a>Een sterk wachtwoordbeleid implementeren

Om je te verdedigen tegen brute-force en woordenboek-gebaseerde gissingen, moet u een sterk wachtwoordbeleid implementeren om ervoor te zorgen dat gebruikers een complex wachtwoord maken (bijvoorbeeld 12 tekens minimale lengte en vereist alfanumerieke en speciale tekens).

U een identiteitskader gebruiken om wachtwoordbeleid te maken en af te dwingen. Azure AD B2C helpt u bij wachtwoordbeheer door [ingebouwde beleidsregels,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow) [zelfservicewachtwoordopnieuw instellen](../../active-directory-b2c/user-flow-self-service-password-reset.md)en meer.

Als u zich wilt verdedigen tegen aanvallen op standaardaccounts, controleert u of alle sleutels en wachtwoorden vervangbaar zijn en of ze worden gegenereerd of vervangen nadat u resources hebt geïnstalleerd.

Als de toepassing wachtwoorden automatisch moet genereren, moet u ervoor zorgen dat de gegenereerde wachtwoorden willekeurig zijn en dat ze een hoge entropie hebben.

### <a name="validate-file-uploads"></a>Bestandsuploads valideren

Als uw toepassing [bestandsuploads](https://www.owasp.org/index.php/Unrestricted_File_Upload)toestaat, moet u rekening houden met voorzorgsmaatregelen die u nemen voor deze riskante activiteit. De eerste stap in veel aanvallen is om een aantal kwaadaardige code te krijgen in een systeem dat wordt aangevallen. Het gebruik van een bestandsupload helpt de aanvaller dit te bereiken. OWASP biedt oplossingen voor het valideren van een bestand om ervoor te zorgen dat het bestand dat u uploadt veilig is.

Antimalwarebescherming helpt bij het identificeren en verwijderen van virussen, spyware en andere schadelijke software. U [Microsoft Antimalware](../fundamentals/antimalware.md) of de endpoint protection-oplossing van een Microsoft-partner installeren[(Trend Micro,](https://www.trendmicro.com/azure/) [Broadcom,](https://www.broadcom.com/products) [McAfee,](https://www.mcafee.com/us/products.aspx) [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)en [Endpoint Protection).](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)

[Microsoft Antimalware](../fundamentals/antimalware.md) bevat functies zoals real-time bescherming, gepland scannen, malwareherstel, handtekeningupdates, engine-updates, rapportage van monsters en het verzamelen van uitsluitingsgebeurtenissen. U Microsoft Antimalware- en partneroplossingen integreren met [Azure Security Center](../../security-center/security-center-partner-integration.md) voor eenvoudige implementatie en ingebouwde detecties (waarschuwingen en incidenten).

### <a name="dont-cache-sensitive-content"></a>Geen gevoelige inhoud in de cache plaatsen

Geen cachegevoelige inhoud in de browser. Browsers kunnen informatie opslaan voor caching en geschiedenis. Bestanden in cache worden opgeslagen in een map zoals de map Tijdelijke internetbestanden, in het geval van Internet Explorer. Wanneer deze pagina's opnieuw worden doorverwezen, worden de pagina's in de cache weergegeven in de browser. Als gevoelige informatie (adres, creditcardgegevens, burgerservicenummer, gebruikersnaam) aan de gebruiker wordt weergegeven, kan de informatie worden opgeslagen in de cache van de browser en kan worden opgehaald door de cache van de browser te onderzoeken of door simpelweg op de **knop Terug** van de browser te drukken.

## <a name="verification"></a>Verificatie
De verificatiefase omvat een uitgebreide inspanning om ervoor te zorgen dat de code voldoet aan de beveiligings- en privacyprincipes die in de voorgaande fasen zijn vastgesteld.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Kwetsbaarheden in uw toepassingsafhankelijkheden zoeken en oplossen

U scant uw toepassing en de bijbehorende bibliotheken om bekende kwetsbare onderdelen te identificeren. Producten die beschikbaar zijn om deze scan uit te voeren zijn [OWASP Dependency Check,](https://www.owasp.org/index.php/OWASP_Dependency_Check)[Snyk](https://snyk.io/)en [Black Duck](https://www.blackducksoftware.com/).

Het scannen van kwetsbaarheden aangedreven door [Tinfoil Security](https://www.tinfoilsecurity.com/) is beschikbaar voor Azure App Service Web Apps. [Tinfoil Security scannen via App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) biedt ontwikkelaars en beheerders een snelle, geïntegreerde en economische manier om kwetsbaarheden te ontdekken en aan te pakken voordat een kwaadwillende acteur er gebruik van kan maken.

> [!NOTE]
> U [Tinfoil Security](../../active-directory/saas-apps/tinfoil-security-tutorial.md)ook integreren met Azure AD. De integratie van Tinfoil Security met Azure AD biedt u de volgende voordelen:
>  - In Azure AD u bepalen wie toegang heeft tot Tinfoil Security.
>  - Uw gebruikers kunnen automatisch worden aangemeld bij Tinfoil Security (eenmalige aanmelding) met behulp van hun Azure AD-accounts.
>  - U uw accounts beheren op één centrale locatie, de Azure-portal.

### <a name="test-your-application-in-an-operating-state"></a>Uw toepassing in een bedrijfstoestand testen

Dynamic application security testing (DAST) is een proces waarbij een toepassing in een operationele staat wordt getest om beveiligingsproblemen te vinden. DAST-hulpprogramma's analyseren programma's tijdens het uitvoeren van beveiligingsproblemen, zoals geheugenbeschadiging, onveilige serverconfiguratie, cross-site scripting, problemen met gebruikersbevoegdheden, SQL-injectie en andere kritieke beveiligingsproblemen.

DAST is anders dan statische applicatiebeveiligingstests (SAST). SAST-tools analyseren broncode of gecompileerde versies van code wanneer de code niet wordt uitgevoerd om beveiligingsfouten te vinden.

Voer DAST uit, bij voorkeur met de hulp van een beveiligingsprofessional (een [penetratietester](../fundamentals/pen-testing.md) of kwetsbaarheidsbeoordelaar). Als een beveiligingsprofessional niet beschikbaar is, u DAST zelf uitvoeren met een webproxyscanner en een training. Sluit al vroeg een DAST-scanner aan om ervoor te zorgen dat u geen duidelijke beveiligingsproblemen in uw code introduceert. Zie de [OWASP-site](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) voor een lijst met kwetsbaarheidsscanners voor webtoepassingen.

### <a name="perform-fuzz-testing"></a>Fuzz-testen uitvoeren

In [fuzz testen,](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)induceert u programma mislukking door opzettelijk invoering van misvormde of willekeurige gegevens aan een toepassing. Het induceren van programmafouten helpt potentiële beveiligingsproblemen te onthullen voordat de toepassing wordt uitgebracht.

[Security Risk Detection](https://docs.microsoft.com/security-risk-detection/) is de microsoft unieke fuzz testing service voor het vinden van security-critical bugs in software.

### <a name="conduct-attack-surface-review"></a>Aanvalsoppervlaktebeoordeling uitvoeren

Door het aanvalsoppervlak te controleren na het voltooien van de code, wordt overwogen dat ontwerp- of implementatiewijzigingen in een toepassing of systeem zijn overwogen. Het helpt ervoor te zorgen dat nieuwe aanvalsvectoren die zijn gemaakt als gevolg van de wijzigingen, waaronder bedreigingsmodellen, zijn beoordeeld en beperkt.

U een afbeelding van het aanvalsoppervlak maken door de toepassing te scannen. Microsoft biedt een aanval oppervlak analyse tool genaamd [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). U kiezen uit vele commerciële dynamische testen en kwetsbaarheid scannen tools of diensten, waaronder [OWASP Zed Attack Proxy Project,](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project) [Arachni](http://arachni-scanner.com/), [Skipfish,](https://code.google.com/p/skipfish/)en [w3af](http://w3af.sourceforge.net/). Deze scantools crawlen uw app en brengen de delen van de toepassing in kaart die toegankelijk zijn via internet. U ook zoeken in Azure Marketplace naar vergelijkbare [hulpprogramma's voor ontwikkelaars.](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)

### <a name="perform-security-penetration-testing"></a>Beveiligingspenetratietesten uitvoeren

Ervoor zorgen dat uw toepassing veilig is, is net zo belangrijk als het testen van andere functionaliteit. Maak [penetratietesten](../fundamentals/pen-testing.md) een standaardonderdeel van het build- en implementatieproces. Plan regelmatig beveiligingstests en het scannen van kwetsbaarheden op geïmplementeerde toepassingen en controleer op open poorten, eindpunten en aanvallen.

### <a name="run-security-verification-tests"></a>Beveiligingsverificatietests uitvoeren

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) bevat SVT's voor meerdere services van het Azure-platform. U voert deze SVT's regelmatig uit om ervoor te zorgen dat uw Azure-abonnement en de verschillende bronnen die uw toepassing omvatten, in een veilige status verkeren. U deze tests ook automatiseren met behulp van de functie continuous integration/continuous deployment (CI/CD) van AzSK, waardoor SVT's beschikbaar zijn als Visual Studio-extensie.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden we beveiligingsbesturingselementen en activiteiten aan waarmee u beveiligde toepassingen ontwerpen en implementeren.

- [Veilige toepassingen ontwerpen](secure-design.md)
- [Veilige toepassingen implementeren](secure-deploy.md)
