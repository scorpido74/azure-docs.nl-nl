---
title: Hoe en waarom apps worden toegevoegd aan Azure AD
titleSuffix: Microsoft identity platform
description: Wat betekent het dat een toepassing wordt toegevoegd aan Azure AD en hoe komen ze daar?
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: d47ed3a4cd4fbdcb69b956d3c8418f70a71cf44f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263138"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hoe en waarom toepassingen worden toegevoegd aan Azure AD

Er zijn twee weergaven van toepassingen in Azure AD:

* [Toepassingsobjecten](app-objects-and-service-principals.md#application-object) - Hoewel er [uitzonderingen](#notes-and-exceptions)zijn, kunnen toepassingsobjecten worden beschouwd als de definitie van een toepassing.
* [Serviceprincipals](app-objects-and-service-principals.md#service-principal-object) - Kan worden beschouwd als een instantie van een toepassing. Serviceprincipals verwijzen over het algemeen naar een toepassingsobject en één toepassingsobject kan worden verwezen door meerdere serviceprincipals in mappen.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Wat zijn toepassingsobjecten en waar komen ze vandaan?

U [toepassingsobjecten](app-objects-and-service-principals.md#application-object) in de Azure-portal beheren via de ervaring [App-registraties.](https://aka.ms/appregistrations) Toepassingsobjecten beschrijven de toepassing op Azure AD en kunnen worden beschouwd als de definitie van de toepassing, zodat de service weet hoe tokens aan de toepassing kunnen worden toegewezen op basis van de instellingen. Het toepassingsobject bestaat alleen in de thuismap, zelfs als het een multi-tenanttoepassing is die serviceprincipals in andere mappen ondersteunt. Het toepassingsobject kan een van de volgende gegevens bevatten (evenals aanvullende informatie die hier niet wordt vermeld):

* Naam, logo en uitgever
* URI's omleiden
* Geheimen (symmetrische en/of asymmetrische sleutels die worden gebruikt om de toepassing te verifiëren)
* API-afhankelijkheden (OAuth)
* Gepubliceerde API's/resources/scopes (OAuth)
* App-rollen (RBAC)
* SSO-metagegevens en -configuratie
* Metagegevens en configuratie van gebruikers
* Proxymetagegevens en -configuratie

Toepassingsobjecten kunnen worden gemaakt via meerdere paden, waaronder:

* Toepassingsregistraties in de Azure-portal
* Een nieuwe toepassing maken met Visual Studio en configureren om Azure AD-verificatie te gebruiken
* Wanneer een beheerder een toepassing uit de app-galerie toevoegt (die ook een serviceprincipal maakt)
* De Microsoft Graph API of PowerShell gebruiken om een nieuwe toepassing te maken
* Vele andere, waaronder verschillende ontwikkelaarservaringen in Azure en in API explorer-ervaringen in ontwikkelaarscentra

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Wat zijn serviceprincipals en waar komen ze vandaan?

U [serviceprincipals](app-objects-and-service-principals.md#service-principal-object) beheren in de Azure-portal via de [Enterprise Applications-ervaring.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Serviceprincipals zijn wat gelden voor een toepassing die verbinding maakt met Azure AD en kan worden beschouwd als de instantie van de toepassing in uw directory. Voor een bepaalde toepassing kan het hoogstens één toepassingsobject hebben (dat is geregistreerd in een "home"-map) en een of meer servicehoofdobjecten die exemplaren van de toepassing vertegenwoordigen in elke map waarin het werkt. 

De serviceprincipal kan bestaan uit:

* Een verwijzing naar een toepassingsobject via de eigenschap toepassings-id
* Records van lokale gebruikers- en groepstoepassingsrollentoewijzingen
* Records van lokale gebruikers- en beheerdersmachtigingen die aan de toepassing zijn verleend
  * Bijvoorbeeld: toestemming voor de toepassing om toegang te krijgen tot de e-mail van een bepaalde gebruiker
* Records van lokaal beleid, inclusief beleid voor voorwaardelijke toegang
* Records van alternatieve lokale instellingen voor een toepassing
  * Regels voor claimtransformatie
  * Toewijzingen (gebruikersinrichting)
  * Mapspecifieke app-rollen (als de toepassing aangepaste rollen ondersteunt)
  * Adreslijstspecifieke naam of logo

Net als toepassingsobjecten kunnen serviceprincipals ook worden gemaakt via meerdere trajecten, waaronder:

* Wanneer gebruikers zich aanmelden bij een toepassing van derden die is geïntegreerd met Azure AD
  * Tijdens het aanmelden wordt gebruikers gevraagd toestemming te geven aan de toepassing om toegang te krijgen tot hun profiel en andere machtigingen. De eerste persoon die toestemming geeft, zorgt ervoor dat een serviceprincipal die de toepassing vertegenwoordigt, wordt toegevoegd aan de map.
* Wanneer gebruikers zich aanmelden bij onlineservices van Microsoft, zoals [Office 365](https://products.office.com/)
  * Wanneer u zich abonneert op Office 365 of een proefversie start, worden een of meer serviceprincipals gemaakt in de map die de verschillende services vertegenwoordigt die worden gebruikt om alle functionaliteit te leveren die is gekoppeld aan Office 365.
  * Sommige Office 365-services zoals SharePoint maken voortdurend serviceprincipals om veilige communicatie tussen componenten, waaronder werkstromen, mogelijk te maken.
* Wanneer een beheerder een toepassing uit de app-galerie toevoegt (hiermee wordt ook een onderliggend app-object gemaakt)
* Een toepassing toevoegen om de [Azure AD-toepassingsproxy](/azure/active-directory/manage-apps/application-proxy) te gebruiken
* Een toepassing voor één aanmelding verbinden met SAML of wachtwoordeenmalige aanmelding (SSO)
* Programmatisch via de Microsoft Graph API of PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hoe zijn toepassingsobjecten en serviceprincipals met elkaar verbonden?

Een toepassing heeft één toepassingsobject in de thuismap waarnaar wordt verwezen door een of meer serviceprincipals in elk van de mappen waar deze actief is (inclusief de thuismap van de toepassing).

![Toont relatie tussen app-objecten en serviceprincipals][apps_service_principals_directory]

In het voorgaande diagram onderhoudt Microsoft intern twee mappen (links weergegeven) die het gebruikt om toepassingen te publiceren:

* Eén voor Microsoft Apps (Microsoft-servicesmap)
* Een voor vooraf geïntegreerde toepassingen van derden (app-galerijmap)

Uitgevers/leveranciers van toepassingen die integreren met Azure AD, moeten een publicatiemap hebben (rechts weergegeven als 'Sommige SaaS-map').

Toepassingen die u zelf toevoegt (weergegeven als **App (van u)** in het diagram) omvatten:

* Apps die u hebt ontwikkeld (geïntegreerd met Azure AD)
* Apps die u hebt verbonden voor één aanmelding
* Apps die u hebt gepubliceerd met de proxy van de Azure AD-toepassing

### <a name="notes-and-exceptions"></a>Opmerkingen en uitzonderingen

* Niet alle serviceprincipals wijzen terug op een toepassingsobject. Toen Azure AD oorspronkelijk werd gebouwd, waren de services voor toepassingen beperkter en was de serviceprincipal voldoende voor het vaststellen van een toepassingsidentiteit. De oorspronkelijke serviceprincipal was dichter in vorm bij het Windows Server Active Directory-serviceaccount. Om deze reden is het nog steeds mogelijk om serviceprincipals te maken via verschillende trajecten, zoals het gebruik van Azure AD PowerShell, zonder eerst een toepassingsobject te maken. De Microsoft Graph API vereist een toepassingsobject voordat een serviceprincipal wordt gemaakt.
* Niet alle hierboven beschreven informatie wordt momenteel programmatisch belicht. De volgende gegevens zijn alleen beschikbaar in de gebruikersinterface:
  * Regels voor claimtransformatie
  * Toewijzingen (gebruikersinrichting)
* Zie de referentiedocumentatie voor microsoft graph API-verwijzingen voor meer gedetailleerde informatie over de hoofd- en toepassingsobjecten van de service:
  * [Toepassing](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)
  * [Serviceprincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Waarom integreren toepassingen met Azure AD?

Toepassingen worden toegevoegd aan Azure AD om gebruik te maken van een of meer van de services die het biedt, waaronder:

* Toepassingsverificatie en -autorisatie
* Gebruikersverificatie en -autorisatie
* SSO met federatie of wachtwoord
* Gebruikersinrichting en synchronisatie
* Op rollen gebaseerd toegangsbeheer - De map gebruiken om toepassingsrollen te definiëren om op rollen gebaseerde autorisatiecontroles in een toepassing uit te voeren
* OAuth-autorisatieservices - Gebruikt door Office 365 en andere Microsoft-toepassingen om toegang tot API's/resources te autoriseren
* Publicatie en proxy van toepassingen - Een toepassing publiceren van een privénetwerk naar het internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Wie heeft toestemming om toepassingen toe te voegen aan mijn Azure AD-exemplaar?

Hoewel er een aantal taken zijn die alleen globale beheerders kunnen uitvoeren (zoals het toevoegen van toepassingen uit de app-galerie en het configureren van een toepassing om de toepassingsproxy te gebruiken), hebben alle gebruikers in uw directory standaard rechten om toepassingsobjecten te registreren die ze ontwikkelen en discretie over welke toepassingen ze delen/toegang geven tot hun organisatiegegevens door middel van toestemming. Als een persoon de eerste gebruiker in uw directory is die zich aanmeldt bij een toepassing en toestemming verleent, wordt er een serviceprincipal in uw tenant gemaakt. anders worden de informatie over toestemmingverlening opgeslagen op de bestaande serviceprincipal.

Gebruikers toestaan zich te registreren en toestemming te geven voor toepassingen klinkt in eerste instantie misschien met betrekking tot, maar houd rekening met het volgende:


* Toepassingen kunnen al vele jaren gebruikmaken van Windows Server Active Directory voor gebruikersverificatie zonder dat de toepassing moet worden geregistreerd of geregistreerd in de directory. Nu zal de organisatie hebben verbeterd zichtbaarheid om precies hoeveel toepassingen zijn met behulp van de directory en voor welk doel.
* Het delegeren van deze verantwoordelijkheden aan gebruikers ontkent de noodzaak van een admin-driven applicatie registratie en publicatie proces. Met Active Directory Federation Services (ADFS) was het waarschijnlijk dat een beheerder een toepassing moest toevoegen als relying party namens hun ontwikkelaars. Nu kunnen ontwikkelaars self-service.
* Gebruikers die zich aanmelden bij toepassingen met behulp van hun organisatie accounts voor zakelijke doeleinden is een goede zaak. Als ze vervolgens de organisatie verlaten, verliezen ze automatisch de toegang tot hun account in de toepassing die ze gebruikten.
* Het hebben van een record van welke gegevens werden gedeeld met welke toepassing is een goede zaak. Gegevens zijn transporteerbaar dan ooit en het is handig om een duidelijk overzicht te hebben van wie welke gegevens met welke toepassingen heeft gedeeld.
* API-eigenaren die Azure AD voor OAuth gebruiken, bepalen precies welke machtigingen gebruikers aan toepassingen kunnen verlenen en welke machtigingen een beheerder vereisen om ermee in te stemmen. Alleen beheerders kunnen instemmen met grotere scopes en belangrijkere machtigingen, terwijl de toestemming van de gebruiker wordt beperkt tot de eigen gegevens en mogelijkheden van de gebruikers.
* Wanneer een gebruiker een toepassing toevoegt of toegang geeft tot zijn gegevens, kan de gebeurtenis worden gecontroleerd, zodat u de controlerapporten in de Azure-portal bekijken om te bepalen hoe een toepassing aan de map is toegevoegd.

Als u nog steeds wilt voorkomen dat gebruikers in uw map toepassingen registreren en zich zonder goedkeuring van de beheerder aanmelden bij toepassingen, zijn er twee instellingen die u wijzigen om deze mogelijkheden uit te schakelen:

* Om te voorkomen dat gebruikers namens hen toestemming geven voor toepassingen:
  1. Ga in de Azure-portal naar de sectie [Gebruikersinstellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) onder Enterprise-toepassingen.
  2. Wijzigen **Gebruikers kunnen namens hen toestemming geven voor apps die namens hen toegang hebben** tot bedrijfsgegevens **.**
     
     > [!NOTE]
     > Als u besluit de toestemming van de gebruiker uit te schakelen, moet een beheerder toestemming geven voor elke nieuwe toepassing die een gebruiker moet gebruiken.

* Ga als u ervoor zorgen dat gebruikers hun eigen toepassingen niet registreren:
  1. Ga in de Azure-portal naar de sectie [Gebruikersinstellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) onder Azure Active Directory
  2. **Gebruikers wijzigen kan aanvragen registreren** bij **Nr.**

> [!NOTE]
> Microsoft zelf maakt gebruik van de standaardconfiguratie met gebruikers die applicaties kunnen registreren en zelf toestemming kunnen geven voor toepassingen.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
