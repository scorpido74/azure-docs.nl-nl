---
title: Prestatiediagnostiek voor virtuele Azure-machines| Microsoft Documenten
description: Introduceert Azure Performance Diagnostics voor Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 16be3d1695608165405a3490b686a01ba6a2a62c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70080602"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostische gegevens over prestaties voor virtuele Azure-machines

Met het hulpprogramma voor prestatiediagnostiek u prestatieproblemen oplossen die van invloed kunnen zijn op een virtuele Windows- of Linux-machine (VM). Ondersteunde probleemoplossingsscenario's omvatten snelle controles op bekende problemen en aanbevolen procedures, en complexe problemen die betrekking hebben op trage VM-prestaties of een hoog gebruik van CPU, schijfruimte of geheugen.

U prestatiediagnostiek rechtstreeks uitvoeren vanuit de Azure-portal, waar u ook inzichten en een rapport bekijken over verschillende logboeken, uitgebreide configuratie- en diagnostische gegevens. We raden u aan prestatiediagnostiek uit te voeren en de inzichten en diagnostische gegevens te bekijken voordat u contact opneemt met Microsoft Support.

> [!NOTE]
> Voor Windows wordt prestatiediagnostiek momenteel ondersteund op VM's waarop .NET SDK-versie 4.5 of een latere versie is geïnstalleerd. Zie [Azure Performance Diagnostics VM-extensie](performance-diagnostics-vm-extension.md)voor de stappen die u uitvoeren voor de uitvoering van prestatiediagnostiek op klassieke VM's.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standaard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle Linux Server`*`6.10 [ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 marketplace image), CentOS 6.5 [`*`], 7.6, RHEL 7.2, 7.5, 8.0 [`*`], Ubuntu 14.04, 16.04, 18.04, Debian 8, 9, 10 [`*`], SLES 12 SP4 [ ]`*`

>[!Note]
>[`*`] Raadpleeg [bekende problemen](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Prestatiediagnostiek installeren en uitvoeren op uw vm

Prestatiediagnostiek installeert een VM-extensie met een diagnosetool met de naam PerfInsights. PerfInsights is beschikbaar voor zowel [Windows](https://aka.ms/perfinsights) als [Linux.](https://aka.ms/perfinsightslinux) Voer de volgende stappen uit om prestatiediagnostiek te installeren en uit te voeren:

1. Selecteer **virtuele machines**in de linkerkolom met opdrachten .
1. Selecteer in de lijst met VM-namen de VM waarop u diagnoses wilt uitvoeren.
1. Selecteer **Prestatiediagnostiek**in de rechterkolom met opdrachten .

    ![Schermafbeelding van Azure-portal, met de knop Prestatiediagnose installeren gemarkeerd](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > In deze screenshot wordt het blad van VM-namen verborgen.
1. Een opslagaccount selecteren (optioneel)

    Als u één opslagaccount wilt gebruiken om de resultaten van prestatiediagnostiek voor meerdere VM's op te slaan, u een opslagaccount selecteren door op de knop **Instellingen** op de werkbalk te klikken. Klik op de knop **OK** zodra u het opslagaccount hebt geselecteerd.

    Als u geen opslagaccount opgeeft, wordt er standaard een nieuw opslagaccount gemaakt.

    ![Schermafbeelding van het blad Prestatiediagnostiek, met de knop Instellingen gemarkeerd](media/performance-diagnostics/settings-button.png)

    ![Schermafbeelding van de selectie van opslagaccount en het blad van de instellingen voor prestatiediagnostische gegevens](media/performance-diagnostics/select-storage-account.png)

1. Selecteer de knop **Prestatiediagnostiek installeren.**
1. Schakel het selectievakje **Diagnostische gegevens uitvoeren** in als u een diagnose wilt uitvoeren nadat de installatie is voltooid. Als u deze selectie maakt, u het scenario voor prestatieanalyse en gerelateerde opties kiezen.

    ![Schermafbeelding van de knop Installatie van prestatiediagnostiek](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Selecteer een analysescenario dat moet worden uitgevoerd

De volgende analysescenario's zijn beschikbaar via de Azure-portal. Selecteer een analyse, afhankelijk van het prestatieprobleem dat u ondervindt. Selecteer de duur- en traceringsopties die nodig zijn voor de analyse.

* **Snelle prestatieanalyse**  
    Controleert op bekende problemen, analyseert best practices en verzamelt diagnostische gegevens. Deze analyse duurt enkele minuten om uit te voeren. Meer informatie [Over Windows](https://aka.ms/perfinsights/quick) of [Linux](https://aka.ms/perfinsightslinux/quick)

* **Prestatieanalyse**  
    Bevat alle controles in de snelle prestatieanalyse en bewaakt een hoog resourceverbruik. Gebruik deze versie om algemene prestatieproblemen op te lossen, zoals een hoog CPU-, geheugen- en schijfgebruik. Deze analyse duurt 30 seconden tot 15 minuten, afhankelijk van de geselecteerde duur. Meer informatie [Over Windows](https://aka.ms/perfinsights/vmslow) of [Linux](https://aka.ms/perfinsightslinux/vmslow)

* **Geavanceerde prestatieanalyse**`*`  
    Bevat alle controles in de prestatieanalyse en verzamelt een of meer van de traces, zoals vermeld in de volgende secties. Gebruik dit scenario om complexe problemen op te lossen waarvoor extra sporen nodig zijn. Als u dit scenario voor langere perioden uitvoert, wordt de totale grootte van de diagnostische uitvoer verhoogd, afhankelijk van de grootte van de VM en de geselecteerde traceringsopties. Deze analyse duurt 30 seconden tot 15 minuten, afhankelijk van de geselecteerde duur. [Meer informatie](https://aka.ms/perfinsights/advanced)

* **Azure-bestandenanalyse**`*`  
    Bevat alle controles in de prestatieanalyse en legt een netwerktracering en MKB-tellers vast. Gebruik dit scenario om de prestaties van Azure-bestanden op te lossen. Deze analyse duurt 30 seconden tot 15 minuten, afhankelijk van de geselecteerde duur. [Meer informatie](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Deze analysescenario's worden alleen ondersteund op Windows.

![Schermafbeelding van het deelvenster Diagnostische gegevens uitvoeren in het hoofd van prestatiediagnostiek](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Symptomen bieden (optioneel)

Selecteer vooraf geselecteerde symptomen uit de lijst of voeg nieuwe symptomen toe. Dit helpt ons om de analyse in de toekomst te verbeteren.

### <a name="provide-support-request-number-if-available-optional"></a>Geef ondersteuningsaanvraagnummer, indien beschikbaar (optioneel)

Als u met een Microsoft-ondersteuningstechnicus werkt aan een bestaand ondersteuningsticket, geeft u het ondersteuningsticketnummer op.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Bekijk het privacybeleid en de wettelijke voorwaarden en schakel het selectievakje in om te erkennen (vereist)

Als u de diagnose wilt uitvoeren, moet u akkoord gaan met de wettelijke voorwaarden en het privacybeleid accepteren.

### <a name="select-ok-to-run-the-diagnostics"></a>Selecteer OK om de diagnose uit te voeren

Een melding wordt weergegeven wanneer prestatiediagnostiek wordt gestart. Nadat de installatie is voltooid, ziet u een melding die aangeeft dat de installatie is geslaagd. De geselecteerde analyse wordt vervolgens uitgevoerd voor de opgegeven duur. Dit zou een goed moment zijn om het prestatieprobleem te reproduceren, zodat de diagnostische gegevens op het juiste moment kunnen worden vastgelegd.

Nadat de analyse is voltooid, worden de volgende items geüpload naar Azure-tabellen en een blobcontainer (binary large object) in het opgegeven opslagaccount:

* Alle inzichten en gerelateerde informatie over de run
* Een uitvoergecomprimeerd bestand (.zip) bestand (genaamd **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip)** op Windows en een teer bal bestand (genaamd **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.tar.gz** ) op Linux dat logbestanden bevat
* Een HTML-rapport

Na het uploaden wordt een nieuw diagnoserapport weergegeven in de Azure-portal.

![Schermafbeelding van een lijst met diagnostische gegevens in het blad Prestatiediagnostiek](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Instellingen voor prestatiediagnostiek wijzigen

Gebruik de werkbalk **Instellingen** om het opslagaccount te wijzigen waar de diagnostische inzichten en uitvoer kunnen worden opgeslagen. U hetzelfde opslagaccount gebruiken voor meerdere VM's die prestatiediagnostiek gebruiken. Wanneer u het opslagaccount wijzigt, worden de oude rapporten en inzichten niet verwijderd. Ze worden echter niet meer weergegeven in de lijst met diagnostische rapporten.

## <a name="review-insights-and-performance-diagnostics-report"></a>Bekijk het rapport inzichten en prestatiediagnostiek

Elke diagnostische run bevat een lijst met inzichten en aanbevelingen, getroffen bronnen, logboekbestanden en andere uitgebreide diagnostische informatie die wordt verzameld, plus een rapport voor offline weergave. Zie Wat voor informatie wordt verzameld **door PerfInsights** [voor](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) een volledige lijst met alle verzamelde diagnostische [gegevens.](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights)

### <a name="select-a-performance-diagnostics-report"></a>Een prestatiediagnoserapport selecteren

U de lijst met diagnoserapporten gebruiken om alle diagnostische rapporten te vinden die zijn uitgevoerd. De lijst bevat details over de analyse die is gebruikt, inzichten die zijn gevonden en de impactniveaus ervan. Selecteer een rij om meer details weer te geven.

![Schermafbeelding van het selecteren van een diagnostisch rapport in het blad Prestatiediagnostiek](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Een rapport over prestatiediagnostiek bekijken

Elk prestatiediagnoserapport kan verschillende inzichten bevatten en een impactniveau van Hoog, Gemiddeld of Laag aangeven. Elk inzicht bevat ook aanbevelingen om de bezorgdheid te verminderen. Inzichten zijn gegroepeerd voor eenvoudige filtering.

Impactniveaus vertegenwoordigen het potentieel voor prestatieproblemen, op basis van factoren zoals verkeerde configuratie, bekende problemen of problemen die door andere gebruikers worden gerapporteerd. Het is mogelijk dat u nog geen of meer van de genoemde problemen ondervindt. U bijvoorbeeld SQL-logboekbestanden en databasebestanden op dezelfde gegevensschijf hebben. Deze voorwaarde heeft een groot potentieel voor knelpunten en andere prestatieproblemen als het databasegebruik hoog is, terwijl u mogelijk geen probleem opmerkt als het gebruik laag is.

![Schermafbeelding van overzichtsblad voor prestatiediagnostiek](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Inzichten en aanbevelingen voor prestatiediagnostiek bekijken

U een inzicht selecteren om meer details over de betreffende resources, voorgestelde oplossingen en referentiekoppelingen weer te geven.

![Schermafbeelding van een inzichtindetail voor prestatiediagnostiek](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Het volledige rapport over prestatiediagnostiek downloaden en bekijken

U de knop **Rapport downloaden** gebruiken om een HTML-rapport te downloaden met aanvullende uitgebreide diagnostische informatie, zoals opslag- en netwerkconfiguratie, prestatiemeteritems, traces, lijst met processen en logboeken. De inhoud is afhankelijk van de geselecteerde analyse. Voor geavanceerde probleemoplossing kan het rapport aanvullende informatie en interactieve grafieken bevatten die gerelateerd zijn aan hoog CPU-gebruik, hoog schijfgebruik en processen die overmatig geheugen verbruiken. Zie [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) of [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report)voor meer informatie over het rapport prestatiediagnostiek.

## <a name="manage-performance-diagnostics-reports"></a>Prestatierapporten beheren

U een of meer prestatiediagnostische rapporten verwijderen met de knop **Rapport verwijderen.**

## <a name="how-to-uninstall-performance-diagnostics"></a>Prestatiediagnostiek verwijderen

U prestatiediagnostiek van een vm verwijderen. Met deze actie wordt de VM-extensie verwijderd, maar heeft dit geen invloed op diagnostische gegevens die zich in het opslagaccount begeven.

![Schermafbeelding van de werkbalk Prestatiediagnostische blad met knop Verwijderen gemarkeerd](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Waar worden de diagnostische gegevens van mijn VM opgeslagen

Alle inzichten en rapporten van prestatiediagnostiek worden opgeslagen in uw eigen opslagaccount. Inzichten worden opgeslagen in Azure-tabellen. Het gecomprimeerde bestand voor rapporten wordt opgeslagen in een blob-container (binary large object) met de naam azdiagextnresults.

U de opslagaccountgegevens bekijken met de knop Instellingen op de werkbalk.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Hoe deel ik deze gegevens met Microsoft Customer Support

Er zijn meerdere manieren om het diagnoserapport met Microsoft te delen.

**Optie 1:** Het laatste rapport automatisch delen  
Wanneer u een ondersteuningsticket opent met Microsoft, is het belangrijk om het rapport over prestatiediagnostiek te delen. Als u ervoor hebt gekozen om deze informatie met Microsoft te delen terwijl u de diagnose uitvoert (door het selectievakje " Ik ga ermee akkoord om diagnostische gegevens met Microsoft te delen " in te**schakelen),** heeft Microsoft toegang tot het rapport vanuit uw opslagaccount via een SAS-koppeling naar het uitvoerzip-bestand gedurende maximaal 30 dagen vanaf de rundatum. Alleen het laatste rapport is beschikbaar voor de support engineer.

**Optie 2:** Een gedeelde toegangshandtekening genereren voor het gecomprimeerde bestand van het diagnoserapport  
U een koppeling naar het gecomprimeerde bestand van rapporten delen met behulp van Gedeelde toegangshandtekeningen. Voer de volgende stappen uit om dit te doen:

1. Blader in de Azure-portal naar het opslagaccount waarin de diagnostische gegevens zijn opgeslagen.
1. Selecteer **Blobs** onder de sectie **Klodderservice.**
1. Selecteer de **azdiagextnresults-container.**
1. Selecteer het gecomprimeerde gecomprimeerde bestand Prestatiediagnostiek uitvoer uitvoer.
1. Selecteer op het tabblad **SAS genereren** de criteria voor delen.
1. Klik **op Blob SAS-token en URL genereren**.
1. Kopieer de **Url van Blob SAS**en deel deze met de ondersteuningstechnicus.

**Optie 3:** Het rapport downloaden van het opslagaccount

U het gecomprimeerde bestand voor prestatiediagnostiek ook vinden met behulp van stap 1–4 in optie 2. Selecteer om het bestand te downloaden en deel het vervolgens via e-mail of vraag de ondersteuningstechnicus om instructies om het bestand te uploaden.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Hoe leg ik de diagnostische gegevens vast op het juiste moment

Elke prestatiediagnostiek heeft twee fasen:

1. De VM-extensie voor prestatiediagnostiek installeren of bijwerken.
1. Voer de diagnose uit voor de opgegeven duur.

Momenteel is er geen gemakkelijke manier om precies te weten wanneer de VM-extensie installatie is voltooid. Over het algemeen duurt het ongeveer 45 seconden tot 1 minuut om de VM-extensie te installeren. Nadat de VM-extensie is geïnstalleerd, u uw repro-stappen uitvoeren om de prestatiediagnostiek de juiste set gegevens voor het oplossen van problemen te laten vastleggen.

## <a name="next-steps"></a>Volgende stappen

Nadat u de inzichten en rapportage van prestatiediagnostiek hebt bekeken, u een ondersteuningsticket openen met Microsoft Customer Support als u de oorzaak van het probleem nog steeds niet vaststellen en meer hulp nodig hebt.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen**. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure-ondersteuning.
