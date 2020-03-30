---
title: Voorbereiding van appSource-pakketten | Azure Marketplace
description: Explanaion in het voorbereiden en bouwen van AppSource pakketten.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280606"
---
# <a name="appsource-package-preparation"></a>Voorbereiding van appSource-pakketten

Naast een solution.zip-bestand hebt u een **AppSource-pakket**nodig. Dit is een .zip-bestand dat alle assets bevat die nodig zijn om het proces van het implementeren van uw oplossing in de CRM-omgeving van de klant te automatiseren. Het **AppSource-pakket** heeft de volgende componenten

* Pakket voor de pakketimplementatie
* **Content_Types.xml-bestand** met de elementen die u gebruikt
* xml-bestand met uw app-specifieke gegevens
* 32x32-logo dat wordt weergegeven met uw vermelding in het beheercentrum
* Licentievoorwaarden, privacybeleid

In de onderstaande stappen u uw AppSource-pakket maken.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Een pakket maken voor de pakketimplementatie

Het pakket voor de pakketimplementatie is een onderdeel van het AppSource-pakket.

Als u een pakket voor de pakketimplementatie [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)wilt maken, gebruikt u de volgende instructies: . Wanneer u klaar bent, bestaat uw pakket uit de onderstaande activa:

1. Pakketmap: bevat alle oplossingen, configuratiegegevens, platte bestanden en de inhoud van uw pakket. _Opmerking: In het voorbeeld dat volgt gaan we ervan uit dat de pakketmap "PkgFolder" wordt genoemd_
2. dll: De assemblage bevat de aangepaste code voor uw pakket. _Opmerking: In het volgende voorbeeld gaan we ervan uit dat dit bestand 'MicrosoftSample.dll' wordt genoemd._

Nu moet u een bestand maken met de naam "**Content_Types.xml**" In dit bestand worden alle elementenextensies weergegeven die deel uitmaken van uw pakket. Hier is voorbeeldcode voor het bestand.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

De laatste stap is om het volgende in één bestand te ritsen. Noem het **package.zip**. Het zal bevatten

1. PkgFolder (inclusief alles in de map)
2. Dll
3. **Content_Types.xml**

Stappen om package.zip te maken:

1. Plaats uw pakketmap, **Content_Types.xml-bestand** en PackageName.dll in één map.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Selecteer alle items in de map, klik met de rechtermuisknop en kies Map Verzenden naar gecomprimeerde (zip)-map

![CRMScreenShot3](media/CRMScreenShot3.png)

1. De naam wijzigen in package.zip

![CRMScreenShot4 CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Een AppSource-pakket maken

Het AppSource-pakket vereist een paar extra bestanden.

1. jpg (resolutie van 32x32)
2. html (HTML-opgemaakt bestand)
3. **Content_Types.xml** (hetzelfde als hierboven)
4. xml

Hier is voorbeeldcode voor input.xml. Zie definities in de onderstaande tabel.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Waar:**

|Veld|Details|
|---|---|
|ProviderName|Van wie komt de oplossing. Als een Microsoft-team, moet dit Microsoft.|
|PackageFile |Pakketdeployer-elementen die zijn\_samengevoegd met een bestand met inhoudstypen.xml. Dit zip-bestand moet de pakketimplementatieverzameling bevatten en de map met de pakkettendeployer-elementen. Dat wil zeggen, package.zip|
|SolutionAnchorName |Naam van het zip-bestand van de oplossing in de pakketimplementatieer die wordt gebruikt voor de weergavenaam en beschrijving van oplossingselementen.|
| Startdate| Dit is de datum waarop het oplossingspakket beschikbaar komt. Het formaat is MM/DD/YYYY|
|Enddate|Dit is de datum waarop het oplossingspakket niet meer beschikbaar zal zijn. Het formaat is MM/DD/YYYY |
|Ondersteunde landen |Dit is een door komma's afgebakende lijst van landen/regio's die dit pakket moeten zien. Neem contact op met online services voor een lijst met alle huidige landcodes. Op het moment, van dit schrijven van de lijst was: AE, AL,AM, AO, AR, AT, AU, AZ, BA, BB, BD, BE, BG, BH, BM, BN, BO, BR, DOOR, CA, CH, CI, CL, CM, CO, CR, CV, CW, CY, CZ, DE,DK, DO,DZ, EC, EE, EG ,ES,FI,FR,GB,GE,GH,GH,GR,GT,HK,H,HR,HU,ID,IE,IE,IN,IQ,IS,IT,JM,JO,J,KE,KG,KN,KR,KW,K,KZ,KZ,LB,LT,LU,LV,LY,MA,MC,MD,ME,ME,MN,MO,MT,MU,MU,MX,MY ,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY, QA, RO,R,RU,RW,SA, SE, SG, SI,SK,SN,SV,TH,TM,TN, TR, TT,TW,UA, US,UY,UZ,VE,VI,VN,ZA,ZW |
|LearnMoreLink | URL naar de meer info pagina voor dit pakket. |
|Locales|Een exemplaar van dit knooppunt voor elke UX-taal die u wilt ondersteunen in de UX met voorkeursoplossing. Dit knooppunt bevat kinderen die de landinstelling, het logo en de termen voor elke taal beschrijven|
|Landlocaties: PackageLocale.Code|LCID van de taal voor dit knooppunt. Voorbeeld: Amerikaans Engels is 1033|
|Landinstellingen: PackageLocale.IsDefault|Hiermee geeft u aan dat dit de standaardtaal is. Dit wordt gebruikt als de terugvaltaal als de ux-taal die door de klant is gekozen, niet beschikbaar is.|
|Landlocaties: Logo|Dit als het logo dat u wilt gebruiken voor dit pakket. De grootte voor Icon is 32x32. Indelingen toegestaan zijn PNG en JPG|
|Landlocaties:Voorwaarden: PackageTerm.File|Dit is de bestandsnaam van het HTML-document dat uw licentievoorwaarden bevat.|

Hier wordt het logo weergegeven:

![CRMScreenShot5](media/CRMScreenShot5.png)

De laatste stap is om het volgende in één bestand te ritsen.

1. zip (eerder gemaakt)
2. **Content_Types.xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Wijzig de naam van het bestand in iets dat geschikt is voor uw app. Wij geven er de voorkeur aan dat u uw bedrijfsnaam en app-naam opneemt. Bijvoorbeeld: **_Microsoft_SamplePackage.zip**.
