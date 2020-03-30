---
title: Een cloudservice lokaal profileren in de Compute Emulator | Microsoft Documenten
services: cloud-services
description: Prestatieproblemen in cloudservices onderzoeken met de Visual Studio-profiler
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 21270d3c7143ce063ffe30d939368b9813e9072e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70094107"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testen van de prestaties van een cloudservice lokaal in de Azure Compute Emulator met behulp van de Visual Studio Profiler
Er zijn verschillende tools en technieken beschikbaar voor het testen van de prestaties van cloudservices.
Wanneer u een cloudservice naar Azure publiceert, u Visual Studio profileringsgegevens laten verzamelen en deze vervolgens lokaal analyseren, zoals beschreven in [Profilering van een Azure-toepassing.][1]
U ook diagnostische gegevens gebruiken om verschillende prestatiemeteritems bij te houden, zoals beschreven in [Het gebruik van prestatiemeteritems in Azure.][2]
U uw toepassing ook lokaal profileren in de compute emulator voordat u deze implementeert in de cloud.

Dit artikel behandelt de CPU Sampling methode van profilering, die lokaal kan worden gedaan in de emulator. CPU sampling is een methode van profilering die niet erg opdringerig is. Met een aangewezen bemonsteringsinterval maakt de profiler een momentopname van de oproepstack. De gegevens worden verzameld over een periode van tijd en weergegeven in een rapport. Deze methode van profilering heeft de neiging om aan te geven waar in een rekenintensieve toepassing het grootste deel van het CPU-werk wordt gedaan.  Dit geeft u de mogelijkheid om zich te concentreren op de "hot path" waar uw toepassing is de meeste tijd doorbrengen.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Visual Studio configureren voor profilering
Ten eerste zijn er een paar Visual Studio-configuratieopties die handig kunnen zijn bij het profileren. Om de profileringsrapporten te begrijpen, hebt u symbolen (.pdb-bestanden) nodig voor uw toepassing en ook symbolen voor systeembibliotheken. U wilt er zeker van zijn dat u naar de beschikbare symboolservers verwijst. Kies hiervoor in het menu **Extra** in Visual Studio **de**optie Opties en kies **Vervolgens Foutopsporing**en **Symbolen**. Controleer of Microsoft Symbol Servers wordt vermeld onder **Locaties voor symboolbestand (.pdb).**  U https://referencesource.microsoft.com/symbolsook verwijzen naar, die mogelijk extra symboolbestanden hebben.

![Symboolopties][4]

Indien gewenst u de rapporten vereenvoudigen die de profiler genereert door Alleen Mijn code in te stellen. Met Just My Code ingeschakeld, worden functiecallstacks vereenvoudigd, zodat oproepen volledig intern naar bibliotheken en het .NET Framework worden verborgen in de rapporten. Kies **Opties** in **Options**het menu Extra . Vouw vervolgens het knooppunt **Prestatiehulpmiddelen** uit en kies **Algemeen**. Schakel het selectievakje in voor **Alleen Mijn code inschakelen voor profilerrapporten**.

![Alleen mijn code-opties][17]

U deze instructies gebruiken bij een bestaand project of bij een nieuw project.  Als u een nieuw project maakt om de hieronder beschreven technieken uit te proberen, kiest u een C# **Azure Cloud Service-project** en selecteert u een **webrol** en een **werknemersrol.**

![Azure Cloud Service-projectrollen][5]

Voeg bijvoorbeeld een code toe aan uw project die veel tijd in beslag neemt en een duidelijk prestatieprobleem aantoont. Voeg bijvoorbeeld de volgende code toe aan een project voor een werkrol:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Roep deze code aan vanuit de RunAsync-methode in de klasse RoleEntryPoint van de werknemerrol. (Negeer de waarschuwing over de methode die synchroon wordt uitgevoerd.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Bouw en voer uw cloudservice lokaal uit zonder foutopsporing (Ctrl+F5), waarbij de oplossingsconfiguratie is ingesteld op **Release.** Dit zorgt ervoor dat alle bestanden en mappen worden gemaakt voor het lokaal uitvoeren van de toepassing en zorgt ervoor dat alle emulators worden gestart. Start de Compute Emulator-gebruikersinterface vanaf de taakbalk om te controleren of uw werkrol wordt uitgevoerd.

## <a name="2-attach-to-a-process"></a>2: Hechten aan een proces
In plaats van de toepassing te profileren door deze te starten vanuit de Visual Studio 2010 IDE, moet u de profiler aan een lopend proces koppelen. 

Als u de profiler aan een proces wilt koppelen, kiest u in het menu **Analyseren** **de** optie Profiler en **Koppelen/koppelen**.

![Profiel toevoegen, optie][6]

Zoek voor een werkrol het WaWorkerHost.exe-proces.

![WaWorkerHost-proces][7]

Als uw projectmap zich op een netwerkstation bevindt, vraagt de profiler u een andere locatie op te geven om de profileringsrapporten op te slaan.

 U ook een webrol toevoegen door u te koppelen aan WaIISHost.exe.
Als er meerdere werkrolprocessen in uw toepassing zijn, moet u de processID gebruiken om deze te onderscheiden. U de processID programmatisch opvragen door toegang te krijgen tot het object Proces. Als u deze code bijvoorbeeld toevoegt aan de methode Uitvoeren van de klasse RoleEntryPoint in een rol, u het logboek in de Compute Emulator-gebruikersinterface bekijken om te weten met welk proces u verbinding moet maken.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Als u het logboek wilt bekijken, start u de Compute Emulator-gebruikersinterface.

![De Compute Emulator-gebruikersinterface starten][8]

Open het consolevenster voor de rolvan de werknemer in de Gebruikersinterface van Compute Emulator door op de titelbalk van het consolevenster te klikken. U de proces-ID in het logboek zien.

![Proces-id weergeven][9]

Een die u hebt gekoppeld, voert de stappen in de gebruikersinterface van uw toepassing uit (indien nodig) om het scenario te reproduceren.

Als u wilt stoppen met profileren, kiest u de koppeling **Profilering stoppen.**

![Profilering stoppen, optie][10]

## <a name="3-view-performance-reports"></a>3: Prestatierapporten bekijken
Het prestatierapport voor uw toepassing wordt weergegeven.

Op dit moment stopt de profiler met uitvoeren, slaat gegevens op in een .vsp-bestand en geeft een rapport weer dat een analyse van deze gegevens weergeeft.

![Profiler-rapport][11]

Als u String.wstrcpy in het hoofdpad ziet, klikt u op Alleen Mijn code om de weergave te wijzigen om alleen gebruikerscode weer te geven.  Als u String.Concat ziet, probeert u op de knop Alle code weergeven te drukken.

U zou de methode Concatenate en String.Concat moeten zien die een groot gedeelte van de uitvoeringstijd in beslag nemen.

![Analyse van het rapport][12]

Als u de tekenreekssamenvoegcode in dit artikel hebt toegevoegd, ziet u hiervoor een waarschuwing in de takenlijst. U ook een waarschuwing zien dat er een te grote hoeveelheid garbage collection is, wat te wijten is aan het aantal tekenreeksen dat wordt gemaakt en verwijderd.

![Prestatiewaarschuwingen][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Wijzigingen aanbrengen en prestaties vergelijken
U de prestaties ook vergelijken voor en na een codewijziging.  Stop het lopende proces en bewerk de code om de tekenreekssamenvoegbewerking te vervangen door het gebruik van StringBuilder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Doe een andere performance run, en vergelijk de prestaties. Als de uitvoeringen zich in dezelfde sessie bevinden, u in de Prestatieverkenner gewoon beide rapporten selecteren, het snelmenu openen en **Prestatierapporten vergelijken**kiezen. Als u wilt vergelijken met een run in een andere prestatiesessie, opent u het menu **Analyseren** en kiest **u Prestatierapporten vergelijken**. Geef beide bestanden op in het dialoogvenster dat wordt weergegeven.

![Prestatierapporten vergelijken, optie][15]

In de rapporten worden de verschillen tussen de twee runs belicht.

![Vergelijkingsrapport][16]

Gefeliciteerd! Je bent begonnen met de profiler.

## <a name="troubleshooting"></a>Problemen oplossen
* Zorg ervoor dat u een release build profileert en start zonder foutopsporing.
* Als de optie Koppelen/koppelen niet is ingeschakeld in het menu Profiler, voert u de wizard Prestaties uit.
* Gebruik de Compute Emulator-gebruikersinterface om de status van uw toepassing weer te geven. 
* Als u problemen hebt met het starten van toepassingen in de emulator of als u de profiler bevestigt, sluit u de compute emulator af en start u deze opnieuw op. Als dat het probleem niet oplost, probeer dan opnieuw op te starten. Dit probleem kan optreden als u de Compute Emulator gebruikt om lopende implementaties op te schorten en te verwijderen.
* Als u een van de profileringsopdrachten van de opdrachtregel hebt gebruikt, met name de globale instellingen, moet u ervoor zorgen dat VSPerfClrEnv /globaloff is aangeroepen en dat VsPerfMon.exe is uitgeschakeld.
* Als u bij het samplen het bericht 'PRF0025: er zijn geen gegevens is verzameld' ziet, controleert u of het proces waaraan u bent gekoppeld, CPU-activiteit heeft. Toepassingen die geen rekenwerk doen, produceren mogelijk geen bemonsteringsgegevens.  Het is ook mogelijk dat het proces is afgesloten voordat een steekproef werd gedaan. Controleer of de methode Uitvoeren voor een rol die u nu profileert, niet wordt beëindigd.

## <a name="next-steps"></a>Volgende stappen
Het instrumenteren van Azure-binaries in de emulator wordt niet ondersteund in de Visual Studio-profiler, maar als u geheugentoewijzing wilt testen, u die optie kiezen bij profilering. U ook kiezen voor gelijktijdigheidsprofilering, waarmee u bepalen of threads tijd verspillen aan het concurreren om vergrendelingen of niveauinteractieprofilering, waardoor u prestatieproblemen opsporen bij interactie tussen lagen van een toepassing, de meeste vaak tussen de gegevenslaag en een werkrol.  U de databasequery's bekijken die uw app genereert en de profileringsgegevens gebruiken om uw gebruik van de database te verbeteren. Zie de [blogpost Walkthrough: Met behulp van de Tier Interaction Profiler in Visual Studio Team System 2010][3]voor informatie over het profileren van niveauinteractie.

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
