---
title: Problemen oplossen van rollen die niet worden gestart | Microsoft Documenten
description: Hier volgen enkele veelvoorkomende redenen waarom een Cloud Service-rol mogelijk niet wordt gestart. Oplossingen voor deze problemen worden ook geboden.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 869453d92f536a62aacc2be52598223158566ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122735"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Problemen oplossen met Cloud Service-rollen die niet kunnen worden gestart
Hier volgen enkele veelvoorkomende problemen en oplossingen met betrekking tot Azure Cloud Services-rollen die niet worden gestart.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Ontbrekende DLL's of afhankelijkheden
Niet-reagerende rollen en rollen die tussen **Initialiseren,** **Bezet**en **Stoppen** kunnen worden veroorzaakt door ontbrekende DLL's of samenstellingen.

Symptomen van ontbrekende DLL's of samenstellingen kunnen zijn:

* Uw rolinstantie fietst door **initialiseren,** **Bezet**en **Stoppen.**
* Uw rolinstantie is verplaatst naar **Gereed,** maar als u naar uw webtoepassing navigeert, wordt de pagina niet weergegeven.

Er zijn verschillende aanbevolen methoden voor het onderzoeken van deze problemen.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnose van ontbrekende DLL-problemen in een webrol
Wanneer u naar een website navigeert die in een webrol wordt geïmplementeerd en de browser een serverfout weergeeft die vergelijkbaar is met de volgende, kan dit erop wijzen dat een DLL ontbreekt.

![Serverfout in '/' toepassing.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Problemen diagnosticeren door aangepaste fouten uit te schakelen
Meer volledige foutinformatie kan worden bekeken door de web.config voor de webrol te configureren om de aangepaste foutmodus in te stellen op Uitschakelen en de service opnieuw te implementeren.

Ga als een beter beeld van completefouten zonder Extern bureaublad te gebruiken:

1. Open de oplossing in Microsoft Visual Studio.
2. Zoek in de **Solution Explorer**het web.config-bestand en open het.
3. Zoek in het bestand web.config de sectie system.web en voeg de volgende regel toe:

    ```xml
    <customErrors mode="Off" />
    ```
4. Sla het bestand op.
5. De service opnieuw verpakken en opnieuw implementeren.

Zodra de service opnieuw is geïmplementeerd, ziet u een foutmelding met de naam van de ontbrekende assemblage of DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Problemen diagnosticeren door de fout op afstand te bekijken
U Extern bureaublad gebruiken om toegang te krijgen tot de rol en op afstand meer volledige foutgegevens weer te geven. Gebruik de volgende stappen om de fouten weer te geven met Extern bureaublad:

1. Controleer of Azure SDK 1.3 of hoger is geïnstalleerd.
2. Schakel tijdens de implementatie van de oplossing met Visual Studio Extern bureaublad in. Zie [Verbinding met extern bureaublad inschakelen voor een rol in Azure Cloud Services met Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)voor meer informatie.
3. In de Microsoft Azure-portal wordt, zodra de instantie een status van **Gereed,** op afstand in de instantie wordt weergegeven. Zie Extern bureaublad in [rolinstanties](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances)voor meer informatie over het gebruik van het externe bureaublad met Cloud Services.
5. Meld u aan bij de virtuele machine met behulp van de referenties die tijdens de configuratie van extern bureaublad zijn opgegeven.
6. Open een opdrachtvenster.
7. Typ `IPconfig`.
8. Let op de waarde IPV4-adres.
9. Open Internet Explorer.
10. Typ het adres en de naam van de webtoepassing. Bijvoorbeeld `http://<IPV4 Address>/default.aspx`.

Als u naar de website navigeert, worden nu meer expliciete foutmeldingen weergegeven:

* Serverfout in '/' toepassing.
* Beschrijving: Er is een niet-afgehandelde uitzondering opgetreden tijdens de uitvoering van de huidige webaanvraag. Controleer de stacktrace voor meer informatie over de fout en waar deze in de code is ontstaan.
* Uitzonderingsgegevens: System.IO.FIleNotFoundException: Kan geen bestand of assemblage laden 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' of een van de afhankelijkheden ervan. Het systeem kan het opgegeven bestand niet vinden.

Bijvoorbeeld:

![Expliciete serverfout in '/' toepassing](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Problemen diagnosticeren met behulp van de compute emulator
U de Microsoft Azure compute emulator gebruiken om problemen met ontbrekende afhankelijkheden en web.config-fouten te diagnosticeren en op te lossen.

Voor de beste resultaten bij het gebruik van deze diagnosemethode moet u een computer of virtuele machine gebruiken die een schone installatie van Windows heeft. Als u de Azure-omgeving het beste wilt simuleren, gebruikt u Windows Server 2008 R2 x64.

1. Installeer de zelfstandige versie van de [Azure SDK.](https://azure.microsoft.com/downloads/)
2. Bouw op de ontwikkelmachine het cloudserviceproject.
3. Navigeer in Windows Verkenner naar de map bin\debug van het cloudserviceproject.
4. Kopieer de .csx-map en het CSCFG-bestand naar de computer die u gebruikt om de problemen te debuggen.
5. Open op de schone machine een Azure `csrun.exe /devstore:start`SDK-opdrachtpromptvenster en typ .
6. Typ bij de `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`opdrachtprompt .
7. Wanneer de rol wordt gestart, ziet u gedetailleerde foutinformatie in Internet Explorer. U ook standaard hulpprogramma's voor het oplossen van problemen met Windows gebruiken om het probleem verder te diagnosticeren.

## <a name="diagnose-issues-by-using-intellitrace"></a>Problemen diagnosticeren met IntelliTrace
Voor werknemers- en webrollen die .NET Framework 4 gebruiken, u [IntelliTrace](/visualstudio/debugger/intellitrace)gebruiken, dat beschikbaar is in Microsoft Visual Studio Enterprise.

Volg de volgende stappen om de service te implementeren met IntelliTrace ingeschakeld:

1. Controleer of Azure SDK 1.3 of hoger is geïnstalleerd.
2. Implementeer de oplossing met Visual Studio. Schakel tijdens de implementatie het selectievakje **IntelliTrace inschakelen voor .NET 4-rollen** in.
3. Zodra de instantie wordt gestart, opent u de **Server verkenner**.
4. Vouw het knooppunt **Azure\\Cloud Services** uit en lokaliseer de implementatie.
5. Vouw de implementatie uit totdat u de rolinstanties ziet. Klik met de rechtermuisknop op een van de instanties.
6. Kies **IntelliTrace-logboeken weergeven**. De **IntelliTrace Samenvatting** wordt geopend.
7. Zoek het gedeelte uitzonderingen van de samenvatting. Als er uitzonderingen zijn, wordt de sectie voorzien van **uitzonderingsgegevens**.
8. Vouw de **uitzonderingsgegevens** uit en zoek naar fouten van **System.IO.FileNotFoundException,** vergelijkbaar met de volgende fouten:

![Uitzonderingsgegevens, ontbrekend bestand of verzameling](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Ontbrekende DLL's en samenstellingen aanpakken
Voer de volgende stappen uit om ontbrekende DLL- en montagefouten aan te pakken:

1. Open de oplossing in Visual Studio.
2. Open **in Solution Explorer**de map **Verwijzingen.**
3. Klik op de verzameling die in de fout is geïdentificeerd.
4. Zoek in het deelvenster **Eigenschappen** de **eigenschap Lokaal kopiëren** en stel de waarde in op **True**.
5. Implementeer de cloudservice opnieuw.

Nadat u hebt geverifieerd dat alle fouten zijn gecorrigeerd, u de service implementeren zonder het selectievakje **IntelliTrace inschakelen voor .NET 4-rollen** in te schakelen.

## <a name="next-steps"></a>Volgende stappen
Bekijk meer [artikelen over probleemoplossing](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) voor cloudservices.

Zie de [blogreeks van Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)voor meer informatie over het oplossen van problemen met de functieproblemen met de cloudservice door azure PaaS-computerdiagnosegegevens te gebruiken.
