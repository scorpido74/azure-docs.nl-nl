---
title: AppSource-pakket voorbereiding | Azure Marketplace
description: Explanaion in het voorbereiden en bouwen van AppSource-pakketten.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280606"
---
# <a name="appsource-package-preparation"></a>AppSource-pakket voorbereiding

Naast een Solution. zip-bestand hebt u een **AppSource-pakket**nodig. Dit is een zip-bestand met alle assets die nodig zijn om het proces voor het implementeren van uw oplossing in de CRM-omgeving van de klant te automatiseren. Het **AppSource-pakket** bevat de volgende onderdelen

* Pakket voor pakket-deployer
* **Content_Types. XML-** bestand met de activa die u gebruikt
* XML-bestand met uw app-specifieke gegevens
* het logo van 32x32 dat wordt weer gegeven met uw vermelding in het beheer centrum
* Licentie voorwaarden, privacybeleid

Met de volgende stappen kunt u uw AppSource-pakket maken.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Een pakket maken voor de pakket-deployer

Het pakket voor de package deployer is een onderdeel van het AppSource-pakket.

Gebruik de volgende instructies om een pakket te maken voor de pakket-deployer [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx):. Als u klaar bent, bestaat het pakket uit de volgende activa:

1. Pakketmap: bevat alle oplossingen, configuratie gegevens, platte bestanden en de inhoud van uw pakket. _Opmerking: in het volgende voor beeld wordt ervan uitgegaan dat de pakketmap "PkgFolder" wordt genoemd._
2. dll: de assembly bevat de aangepaste code voor uw pakket. _Opmerking: in het volgende voor beeld wordt ervan uitgegaan dat dit bestand ' MicrosoftSample. dll ' wordt genoemd._

Nu moet u een bestand met de naam '**Content_Types. XML**' maken. dit bestand bevat alle asset-uitbrei dingen die onderdeel zijn van uw pakket. Hier volgt een voorbeeld code voor het bestand.

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

De laatste stap bestaat uit het volgende te doen in één bestand. Noem het **pakket. zip**. Deze bevat

1. PkgFolder (inclusief alles in de map)
2. bestand
3. **Content_Types. XML**

Stappen voor het maken van package. zip:

1. Plaats de pakketmap, **Content_Types. XML** -bestand en pakket naam. dll in één map.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Selecteer alle items in de map, klik met de rechter muisknop en kies naar gecomprimeerde map verzenden

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Wijzig de naam in package. zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Een AppSource-pakket maken

Het AppSource-pakket vereist enkele extra bestanden.

1. jpg (resolutie van 32x32)
2. HTML-bestand (HTML-indeling)
3. **Content_Types. XML** (hetzelfde als hierboven)
4. xml

Hier volgt een voorbeeld code voor input. XML. Zie de definities in de onderstaande tabel.

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
 
**Positie**

|Veld|Details|
|---|---|
|ProviderName|Wie is de oplossing die afkomstig is van. Als u een micro soft-team bent, moet dit micro soft zijn.|
|PackageFile |Pakket-deployer assets verpakt samen met een\_inhouds type. XML-bestand. Dit zip-bestand moet de package deployer-assembly bevatten en de map met de pakketten van de pakket-deployer. Dat wil zeggen, Package. zip|
|SolutionAnchorName |De naam van het zip-bestand van de oplossing in de pakket-deployer die wordt gebruikt voor de weergave naam en beschrijving van de oplossings assets.|
| Begin| Dit is de datum waarop het oplossings pakket beschikbaar wordt. De notatie is MM/DD/JJJJ|
|EndDate|Dit is de datum waarop het oplossings pakket niet meer beschikbaar is. De notatie is MM/DD/JJJJ |
|SupportedCountries |Dit is een door komma's gescheiden lijst met landen/regio's waarvoor dit pakket zou moeten worden weer geven. Neem contact op met onlineservices voor een lijst met alle huidige land codes. Op dit moment is het schrijven van de lijst: AE, AL, AM, AO, AR, AT, AU, AZ, BA, BB, BD, ben, BG, BH, BM, BN, BO, BR, BY, CA, CH, CI, LC, CM, CO, CR, CV, CW, CY, CZ, DE, DK, DO, DZ, EG, EE, BIJV. ES, FI, FR, GB, GE, GH, GR, GT, Hongkong, HN, HR, HU, ID, IE, IL , IN, IQ, IS, JM, JO, JP, KE, KG, KN, KR, KW, KY, KZ, LB, LK, LT, LU, LV, ONWAAR, MA, MC, MD, ME, MK, MN, MO, MT, MU, MX, MIJN, NG, NI, NL, NO, NZ, OM, PA, PE, PH, PK, PL, PR, PS, PT, PY, QA, RO, RS, RU, RW, DE (SE) , TR, TT, TW, UA, VS, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | URL naar de meer informatie pagina voor dit pakket. |
|Land instellingen|Eén exemplaar van dit knoop punt voor elke UX-taal die u wilt ondersteunen in de voor keur UX van de oplossing. Dit knoop punt bevat onderliggende items die de land instellingen, het logo en de voor waarden voor elke taal beschrijven|
|Land instellingen: PackageLocale. code|De LCID van de taal voor dit knoop punt. Voor beeld: Amerikaans Engels is 1033|
|Land instellingen: PackageLocale. IsDefault|Geeft aan dat dit de standaard taal is. Dit wordt gebruikt als de taal van de terugvallen als de door de klant gekozen UX-taal niet beschikbaar is.|
|Land instellingen: logo|Dit als het logo dat u wilt gebruiken voor dit pakket. Grootte van pictogram is 32x32. Toegestane notaties zijn PNG en JPG|
|Land instellingen: voor waarden: PackageTerm. File|Dit is de bestands naam van het HTML-document met de licentie voorwaarden.|

Hier wordt het logo weer gegeven:

![CRMScreenShot5](media/CRMScreenShot5.png)

De laatste stap bestaat uit het volgende te doen in één bestand.

1. zip (eerder gemaakt)
2. **Content_Types. XML**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Wijzig de naam van het bestand in een waarde die geschikt is voor uw app. U kunt het beste de naam van uw bedrijf en de naam van de app toevoegen. Bijvoorbeeld: **_Microsoft_SamplePackage. zip**.
