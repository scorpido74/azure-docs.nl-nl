---
title: Aangepaste logboeken verzamelen in Azure Monitor | Microsoft Documenten
description: Azure Monitor kan gebeurtenissen verzamelen van tekstbestanden op zowel Windows- als Linux-computers.  In dit artikel wordt beschreven hoe u een nieuw aangepast logboek en details van de records die ze maken in Azure Monitor definiëren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 1e889aaef7cd01cd743e8063a8a1dd5138ba9d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670590"
---
# <a name="custom-logs-in-azure-monitor"></a>Aangepaste logboeken in Azure Monitor

Met de gegevensbron Aangepaste logboeken in Azure Monitor u gebeurtenissen verzamelen van tekstbestanden op zowel Windows- als Linux-computers. Veel toepassingen registreren informatie aan tekstbestanden in plaats van standaardlogboekservices zoals Windows Event log of Syslog. Eenmaal verzameld, u de gegevens ontweken in afzonderlijke velden in uw query's of de gegevens extraheren tijdens het verzamelen naar afzonderlijke velden.

![Aangepaste logboekverzameling](media/data-sources-custom-logs/overview.png)

De te verzamelen logbestanden moeten aan de volgende criteria voldoen.

- Het logboek moet één vermelding per regel hebben of een tijdstempel gebruiken die overeenkomt met een van de volgende indelingen aan het begin van elke vermelding.

    YYYY-MM-DD HH:MM:SS<br>M/D/YYYY HH:MM:SS AM/PM<br>Mon DD, YYYY HH:MM:SS<br />yyMMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- Het logboekbestand mag geen cirkellogboekregistratie of logboekrotatie toestaan, waarbij het bestand wordt overschreven met nieuwe vermeldingen.
- Het logboekbestand moet ASCII- of UTF-8-codering gebruiken.  Andere formaten zoals UTF-16 worden niet ondersteund.

>[!NOTE]
> Als er dubbele vermeldingen in het logboekbestand staan, verzamelt Azure Monitor deze. De queryresultaten zijn echter inconsistent wanneer de filterresultaten meer gebeurtenissen weergeven dan het aantal resultaten. Het is belangrijk dat u het logboek valideert om te bepalen of de toepassing die het maakt dit gedrag veroorzaakt en het zo mogelijk aanpakt voordat u de aangepaste definitie van logboekverzameling maakt.  
>

>[!NOTE]
> Een werkruimte Log Analytics ondersteunt de volgende limieten:
> 
> * Er kunnen slechts 500 aangepaste logboeken worden gemaakt.
> * Een tabel ondersteunt slechts maximaal 500 kolommen. 
> * Het maximum aantal tekens voor de kolomnaam is 500. 
>

>[!IMPORTANT]
>Aangepaste logboekverzameling vereist dat de toepassing die het logboekbestand schrijft, de loginhoud periodiek naar de schijf doorspoelt. Dit komt omdat de aangepaste logboekverzameling afhankelijk is van meldingen van bestandssysteemwijziging voor het logboekbestand dat wordt bijgehouden.

## <a name="defining-a-custom-log"></a>Een aangepast logboek definiëren
Gebruik de volgende procedure om een aangepast logboekbestand te definiëren.  Schuif naar het einde van dit artikel voor een walkthrough van een voorbeeld van het toevoegen van een aangepast logboek.

### <a name="step-1-open-the-custom-log-wizard"></a>Step 1. De wizard Aangepast logboek openen
De wizard Aangepast logboek wordt uitgevoerd in de Azure-portal en stelt u in staat een nieuw aangepast logboek te definiëren dat u wilt verzamelen.

1. Selecteer in de Azure-portal de optie **Logboekanalysewerkruimten** > uw werkruimte > **Geavanceerde instellingen.**
2. Klik op **Gegevens** > **aangepaste logboeken**.
3. Standaard worden alle configuratiewijzigingen automatisch naar alle agents gepusht. Voor Linux-agents wordt een configuratiebestand verzonden naar de Fluentd-gegevensverzamelaar.
4. Klik **op Toevoegen+** om de wizard Aangepast logboek te openen.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Stap 2. Een voorbeeldlogboek uploaden en ontzien
U begint met het uploaden van een voorbeeld van het aangepaste logboek.  De wizard onteert en geeft de vermeldingen in dit bestand weer om te valideren.  Azure Monitor gebruikt de scheidingsteken die u opgeeft om elke record te identificeren.

**Nieuwe regel** is de standaardscheidingsscheidingen en wordt gebruikt voor logboekbestanden met één vermelding per regel.  Als de regel begint met een datum en tijd in een van de beschikbare indelingen, u een **timestamp-scheidingsteken** opgeven die items ondersteunt die meer dan één regel omvatten.

Als een tijdstempelscheidinger wordt gebruikt, wordt de eigenschap TimeGenerated van elke record die is opgeslagen in Azure Monitor gevuld met de datum/tijd die is opgegeven voor die vermelding in het logboekbestand.  Als een nieuwe regelscheidingsteken wordt gebruikt, wordt TimeGenerated gevuld met datum en tijd waarop Azure Monitor de vermelding heeft verzameld.

1. Klik **op Bladeren** en blader naar een voorbeeldbestand.  Houd er rekening mee dat deze knop in sommige browsers het label **Bestand kiezen** kan krijgen.
2. Klik op **Volgende**.
3. De wizard Aangepast logboek uploadt het bestand en geeft een overzicht van de records die het identificeert.
4. Wijzig de scheidingsteken die wordt gebruikt om een nieuwe record te identificeren en selecteer de scheidingsteken die de records in uw logboekbestand het beste identificeert.
5. Klik op **Volgende**.

### <a name="step-3-add-log-collection-paths"></a>Stap 3. Logboekverzamelingspaden toevoegen
U moet een of meer paden op de agent definiëren waar het aangepaste logboek kan worden gevonden.  U een specifiek pad en een specifieke naam opgeven voor het logboekbestand of u een pad opgeven met een wildcard voor de naam. Dit ondersteunt toepassingen die elke dag een nieuw bestand maken of wanneer een bestand een bepaalde grootte bereikt. U ook meerdere paden voor één logboekbestand bieden.

Een toepassing kan bijvoorbeeld elke dag een logboekbestand maken met de datum die in de naam is opgenomen als in log20100316.txt. Een patroon voor een dergelijk logboek kan *log\*.txt* zijn dat van toepassing zou zijn op elk logbestand dat het naamgevingsschema van de toepassing volgt.

In de volgende tabel vindt u voorbeelden van geldige patronen om verschillende logboekbestanden op te geven.

| Beschrijving | Pad |
|:--- |:--- |
| Alle bestanden in *C:\Logboeken* met .txt-extensie op Windows-agent |C:\Logs\\\*.txt |
| Alle bestanden in *C:\Logboeken* met een naam die begint met logboek en een .txt-extensie op Windows-agent |C:\Logs\log\*.txt |
| Alle bestanden in */var/log/audit* met .txt extensie op Linux agent |/var/log/audit/*.txt |
| Alle bestanden in */var/log/audit* met een naam die begint met log en een .txt extensie op Linux agent |/var/log/audit/log\*.txt |

1. Selecteer Windows of Linux om op te geven welke padindeling u toevoegt.
2. Typ het pad en **+** klik op de knop.
3. Herhaal het proces voor eventuele extra paden.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Stap 4. Een naam en beschrijving opgeven voor het logboek
De naam die u opgeeft, wordt gebruikt voor het logboektype zoals hierboven beschreven.  Het zal altijd eindigen met _CL om het te onderscheiden als een aangepaste log.

1. Typ een naam voor het logboek.  Het ** \_** CL-achtervoegsel wordt automatisch verstrekt.
2. Voeg een optionele **beschrijving toe**.
3. Klik **op Volgende** om de aangepaste logboekdefinitie op te slaan.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Stap 5. Valideren dat de aangepaste logboeken worden verzameld
Het kan tot een uur duren voordat de eerste gegevens uit een nieuw aangepast logboek worden weergegeven in Azure Monitor.  Het zal beginnen met het verzamelen van items uit de logboeken gevonden in het pad dat u hebt opgegeven vanaf het punt dat u het aangepaste logboek gedefinieerd.  Het zal niet de items die u hebt geüpload tijdens de aangepaste log creatie, maar het zal verzamelen al bestaande items in de log bestanden die het lokaliseert.

Zodra Azure Monitor begint te verzamelen vanuit het aangepaste logboek, zijn de records beschikbaar met een logboekquery.  Gebruik de naam die u het aangepaste logboek hebt opgegeven als **het type** in uw query.

> [!NOTE]
> Als de eigenschap RawData ontbreekt in de query, moet u mogelijk uw browser sluiten en opnieuw openen.

### <a name="step-6-parse-the-custom-log-entries"></a>Stap 6. De aangepaste logboekvermeldingen ontschepen
De volledige logboekvermelding wordt opgeslagen in één eigenschap genaamd **RawData.**  U zult waarschijnlijk de verschillende stukken informatie in elke ingang in individuele eigenschappen voor elke verslag willen scheiden. Raadpleeg [Parse-tekstgegevens in Azure Monitor](../log-query/parse-text.md) voor opties voor het ontwijsmaken van **RawData** in meerdere eigenschappen.

## <a name="removing-a-custom-log"></a>Een aangepast logboek verwijderen
Gebruik het volgende proces in de Azure-portal om een aangepast logboek te verwijderen dat u eerder hebt gedefinieerd.

1. Selecteer **aangepaste logboeken** in het menu **Gegevens** in de **geavanceerde instellingen** voor uw werkruimte om al uw aangepaste logboeken weer te geven.
2. Klik **op Verwijderen** naast het aangepaste logboek om te verwijderen.

## <a name="data-collection"></a>Gegevensverzameling
Azure Monitor verzamelt ongeveer elke 5 minuten nieuwe items uit elk aangepast logboek.  De agent registreert zijn plaats in elk logboekbestand waarvan het verzamelt.  Als de agent een bepaalde tijd offline gaat, verzamelt Azure Monitor items van waar deze voor het laatst was gebleven, zelfs als deze vermeldingen zijn gemaakt terwijl de agent offline was.

De volledige inhoud van de logvermelding wordt naar één eigenschap geschreven die **RawData**heet.  Zie [Parse-tekstgegevens in Azure Monitor](../log-query/parse-text.md) voor methoden om elke geïmporteerde logboekinvoer in meerdere eigenschappen te ontlopen.

## <a name="custom-log-record-properties"></a>Eigenschappen van aangepaste logboekrecord
Aangepaste logboekrecords hebben een type met de logboeknaam die u opgeeft en de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated |Datum en tijd waarop de record is verzameld door Azure Monitor.  Als het logboek een op tijd gebaseerde scheidingsteken gebruikt, is dit de tijd die wordt verzameld uit de vermelding. |
| SourceSystem |Type agent waar de plaat vandaan kwam. <br> OpsManager – Windows-agent, direct connect of System Center Operations Manager <br> Linux – Alle Linux-agents |
| RawData RawData |Volledige tekst van de verzamelde vermelding. U zult deze gegevens waarschijnlijk willen [ontlopen in afzonderlijke eigenschappen.](../log-query/parse-text.md) |
| ManagementGroupName |Naam van de beheergroep voor system center operations manage agents.  Voor andere agents is dit\<AOI-werkplek-ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Voorbeeldvan het toevoegen van een aangepast logboek
In de volgende sectie wordt een voorbeeld van het maken van een aangepast logboek doorlopen.  Het voorbeeldlogboek dat wordt verzameld, heeft één vermelding op elke regel die begint met een datum en tijd en vervolgens door komma's afgebakende velden voor code, status en bericht.  Hieronder worden verschillende voorbeeldgegevens weergegeven.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Een voorbeeldlogboek uploaden en ontzien
Wij bieden een van de log bestanden en kunnen zien welke gebeurtenissen het zal verzamelen.  In dit geval is New Line een voldoende scheidingsteken.  Als een enkele vermelding in het logboek meerdere regels kan omvatten, moet er wel een tijdstempelscheiding worden gebruikt.

![Een voorbeeldlogboek uploaden en ontzien](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Logboekverzamelingspaden toevoegen
De logbestanden bevinden zich in *C:\MyApp\Logs*.  Elke dag wordt er een nieuw bestand gemaakt met een naam die de datum bevat in het patroon *appYYYYMMDD.log*.  Een voldoende patroon voor dit logboek zou *\\\*C:\MyApp\Logs .log*zijn.

![Pad voor logboekverzameling](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Een naam en beschrijving opgeven voor het logboek
We gebruiken een naam van *MyApp_CL* en typen een **beschrijving**in.

![Logboeknaam](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Valideren dat de aangepaste logboeken worden verzameld
We gebruiken een eenvoudige query van *MyApp_CL* om alle records terug te sturen uit het verzamelde logboek.

![Logboekquery zonder aangepaste velden](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternatieven voor aangepaste logboeken
Hoewel aangepaste logboeken handig zijn als uw gegevens voldoen aan de criteria die worden vermeld, maar er gevallen zijn, zoals de volgende waarin u een andere strategie nodig hebt:

- De gegevens passen niet in de vereiste structuur, zoals het hebben van de tijdstempel in een andere indeling.
- Het logboekbestand voldoet niet aan vereisten zoals bestandscodering of een niet-ondersteunde mapstructuur.
- De gegevens moeten worden voorverwerkt of gefilterd voordat deze worden opgehaald. 

In de gevallen waarin uw gegevens niet kunnen worden verzameld met aangepaste logboeken, u de volgende alternatieve strategieën overwegen:

- Gebruik een aangepast script of een andere methode om gegevens naar [Windows-gebeurtenissen](data-sources-windows-events.md) of [Syslog](data-sources-syslog.md) te schrijven die door Azure Monitor worden verzameld. 
- Stuur de gegevens rechtstreeks naar Azure Monitor met behulp van [HTTP Data Collector API](data-collector-api.md). 

## <a name="next-steps"></a>Volgende stappen
* Zie [Parse-tekstgegevens in Azure Monitor](../log-query/parse-text.md) voor methoden om elke geïmporteerde logboekinvoer in meerdere eigenschappen te ontlopen.
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen.
