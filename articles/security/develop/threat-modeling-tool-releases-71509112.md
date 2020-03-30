---
title: Microsoft Threat Modeling Tool release 9/12/2018
titleSuffix: Azure
description: Het documenteren van de releasenotes voor het hulpprogramma voor bedreigingsmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: a921310f14f0d48922165a46d750366e170ee374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269921"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA release 7.1.50911.2 - 9/12/2018

We zijn verheugd om aan te kondigen dat de Microsoft Threat Modeling Tool nu beschikbaar is om te downloaden als een ondersteunde algemeen beschikbare (GA) release. Deze release bevat belangrijke privacy- en beveiligingsupdates, evenals bugfixes, functie-updates en stabiliteitsverbeteringen. Bestaande gebruikers van de 2017 Preview-versie worden gevraagd om bij te werken naar de nieuwste release via de ClickOnce-technologie bij het openen van de client. Voor nieuwe gebruikers van de tool u [de client downloaden.](https://aka.ms/threatmodelingtool)

Met deze release beëindigen we de ondersteuning voor de 2017 Preview en raden we alle gebruikers van de Preview-update aan voor de GA-release. Op of na 15 oktober 2018 stellen we de minimaal vereiste ClickOnce-versie in voor de Threat Modeling Tool en moeten alle Preview-clients upgraden.

De Microsoft Threat Modeling Tool 2016, die beschikbaar is in het [Microsoft Download Center,](https://www.microsoft.com/en-us/download/details.aspx?id=49168)blijft tot 1 oktober 2019 ondersteund voor alleen kritieke beveiligingsoplossingen.

## <a name="feature-changes"></a>Functiewijzigingen

### <a name="azure-stencil-updates"></a>Azure-stencilupdates

Extra Azure-stencils en de bijbehorende bedreigingen en oplossingen zijn toegevoegd aan de verzending van de stencilset met deze release. Er zijn belangrijke wijzigingen aangebracht in de focusgebieden van 'Azure App Services', 'Azure Database Offerings' en 'Azure Storage'.

![Azure-stencilupdates](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive-integratiefunctie verwijderd

De functies 'Opslaan naar OneDrive', 'Openen vanuit OneDrive' en 'Een koppeling delen' van de preview zijn verwijderd. Gebruikers van OneDrive worden aangemoedigd om de [OneDrive voor Windows-client](https://onedrive.live.com/about/en-us/download/) van Microsoft te gebruiken om toegang te krijgen tot hun bestanden die zijn opgeslagen op OneDrive via de standaarddialoogvensters voor het opslaan en openen van bestanden.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Opmerkelijke vaste bugs gemeld door klanten

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>In TMT Preview loopt het gereedschap vast bij het gebruik van de standaardsjabloon

- Wanneer een algemeen stencil (bijvoorbeeld 'Generieke gegevensstroom') aan het tekenoppervlak wordt toegevoegd en bedreigingen genereert, kan het hulpprogramma vastlopen. Dit probleem is opgelost.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>In TMT Preview, wanneer ik een rapport opte slaan of kopieer de bedreigingen, de risico's onjuist zijn

- Als een gebruiker het risiconiveau van specifieke bedreigingen wijzigt en vervolgens een rapport opslaat of de risico's kopieert, kan het risiconiveau terugkeren naar "Hoog". Dit probleem is opgelost.

## <a name="known-issues-and-faq"></a>Bekende problemen en veelgestelde vragen

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Gebruikers van schermen met hoge resolutie kunnen kleine tekst ervaren in de bedreigingseigenschappen

#### <a name="issue"></a>Probleem

Als de gebruiker in de analyseweergave van het hulpprogramma een scherm met hoge resolutie heeft dat standaard is ingesteld om de leesbaarheid in Windows te vergroten, kan het gedeelte 'Mogelijke mitigatie(s)' van een bedreiging worden weergegeven met kleine tekst.

![Bekend probleem met schermen met hoge resolutie](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Tijdelijke oplossing

De gebruiker kan op de mitigatietekst klikken en het standaard Windows-zoombesturingselement (Crtl-Mouse Wheel Up) gebruiken om de vergroting van die sectie te vergroten.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Bestanden in het gedeelte 'Onlangs geopende modellen' van het hoofdvenster kunnen mogelijk niet worden geopend

#### <a name="issue"></a>Probleem

De functie 'Openen vanaf OneDrive' van de preview-release is verwijderd. Gebruikers met 'Onlangs geopende modellen' die zijn opgeslagen in OneDrive, ontvangen de volgende fout.

![OneDrive-functie verwijderd](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Tijdelijke oplossing

Gebruikers van OneDrive worden aangemoedigd om de [OneDrive voor Windows-client](https://onedrive.live.com/about/en-us/download/) van Microsoft te gebruiken om toegang te krijgen tot hun bestanden die zijn opgeslagen op OneDrive via het standaarddialoogvenster 'Een model openen'.

![OneDrive-functie verwijderd](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Mijn organisatie gebruikt de 2016-versie van de tool, kan ik de Azure-stencilset gebruiken?

Ja, dat kan! De [Azure-stencilset is beschikbaar op GitHub](https://github.com/Microsoft/threat-modeling-templates/)en kan worden geladen in de 2016-versie van het hulpprogramma. Als u een nieuw model wilt maken met de Azure-stencilset, gebruikt u het dialoogvenster Sjabloon voor nieuwe modellen in het hoofdmenuscherm. TMT 2016 kan de koppelingen in de velden 'Mogelijke oplossingen' van de Azure-stencilset niet renderen, daarom u koppelingen zien die worden weergegeven als HTML-tags.

![Azure Stencil-updates in 2016-client](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - Microsoft Windows 10
- .NET-versie vereist
  - .NET 3.5.2
- Aanvullende vereisten
  - Er is een internetverbinding vereist om updates van de tool en sjablonen te ontvangen.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.microsoft.com](threat-modeling-tool.md)en bevat informatie [over het gebruik van de tool.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Volgende stappen

Download de nieuwste versie van de [Microsoft Threat Modeling Tool.](https://aka.ms/threatmodelingtool)
