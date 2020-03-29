---
title: Opstarttaken uitvoeren in Azure Cloud Services | Microsoft Documenten
description: Opstarttaken helpen bij het voorbereiden van uw cloudserviceomgeving voor uw app. Dit leert u hoe opstarttaken werken en hoe u ze maken
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360307"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Opstarttaken configureren en uitvoeren voor een cloudservice
U opstarttaken gebruiken om bewerkingen uit te voeren voordat een rol wordt gestart. Bewerkingen die u mogelijk wilt uitvoeren, zijn het installeren van een component, het registreren van COM-onderdelen, het instellen van registersleutels of het starten van een langlopend proces.

> [!NOTE]
> Opstarttaken zijn niet van toepassing op virtuele machines, alleen op rollen van Cloud Service Web en Worker.
> 
> 

## <a name="how-startup-tasks-work"></a>Hoe opstarttaken werken
Opstarttaken zijn acties die worden uitgevoerd voordat uw rollen beginnen en worden gedefinieerd in het bestand [ServiceDefinition.csdef] met behulp van het [element Taak] binnen het [element Opstarten.] Vaak opstarten taken zijn batch bestanden, maar ze kunnen ook console-toepassingen, of batch bestanden die PowerShell scripts te starten.

Omgevingsvariabelen geven informatie door in een opstarttaak en lokale opslag kan worden gebruikt om informatie door te geven aan een opstarttaak. Een omgevingsvariabele kan bijvoorbeeld het pad opgeven naar een programma dat u wilt installeren en bestanden kunnen worden geschreven naar lokale opslag die later door uw rollen kan worden gelezen.

Uw opstarttaak kan informatie en fouten registreren in de map die is opgegeven door de **temp-omgevingsvariabele.** Tijdens de opstarttaak wordt de **temp-omgevingsvariabele** opgelost naar de *C:\\Resources\\temp\\[guid].[ rolename]\\RoleTemp-map* wanneer u in de cloud wordt uitgevoerd.

Opstarttaken kunnen ook verschillende keren worden uitgevoerd tussen het opnieuw opstarten. De opstarttaak wordt bijvoorbeeld uitgevoerd telkens wanneer de rol opnieuw wordt gebruikt, en bij het opnieuw gebruiken van rollen wordt niet altijd opnieuw opgestart. Opstarttaken moeten worden geschreven op een manier die hen in staat stelt om meerdere keren zonder problemen uit te voeren.

Opstarttaken moeten eindigen met een **foutniveau** (of afsluitcode) van nul voor het opstartproces dat moet worden voltooid. Als een opstarttaak eindigt met een foutniveau zonder **nul,** wordt de rol niet gestart.

## <a name="role-startup-order"></a>Opstartvolgorde voor rollen
In de volgende programma's wordt de procedure voor het opstarten van rollen in Azure weergegeven:

1. De instantie is gemarkeerd als **Start** en ontvangt geen verkeer.
2. Alle opstarttaken worden uitgevoerd op basis van het kenmerk **taskType.**
   
   * De **eenvoudige** taken worden synchroon uitgevoerd, één voor één.
   * De **achtergrond-** en **voorgrondtaken** worden asynchroon gestart, parallel aan de opstarttaak.  
     
     > [!WARNING]
     > IIS is mogelijk niet volledig geconfigureerd tijdens de opstarttaakfase in het opstartproces, zodat functiespecifieke gegevens mogelijk niet beschikbaar zijn. Opstarttakentaken waarvoor rolspecifieke gegevens nodig zijn, moeten [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100))gebruiken .
     > 
     > 
3. Het rolhostproces wordt gestart en de site wordt gemaakt in IIS.
4. De methode [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) wordt aangeroepen.
5. De instantie is gemarkeerd als **Gereed** en het verkeer wordt doorgestuurd naar de instantie.
6. De methode [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) wordt aangeroepen.

## <a name="example-of-a-startup-task"></a>Voorbeeld van een opstarttaak
Opstarttaken worden gedefinieerd in het bestand [ServiceDefinition.csdef] in het element **Taak.** Het kenmerk **commandLine** geeft de naam en parameters op van het opstartbatchbestand of de consoleopdracht, het kenmerk **executionContext** geeft het bevoegdhedenniveau van de opstarttaak op en het kenmerk **taakType** geeft aan hoe de taak wordt uitgevoerd.

In dit voorbeeld wordt een omgevingsvariabele, **MyVersionNumber,** gemaakt voor de opstarttaak en ingesteld op de waarde "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

In het volgende voorbeeld schrijft het batchbestand **Opstarten.cmd** de regel 'De huidige versie is 1.0.0.0' naar het bestand StartupLog.txt in de map die is opgegeven door de temp-omgevingsvariabele. De `EXIT /B 0` regel zorgt ervoor dat de opstarttaak eindigt met een **foutniveau** van nul.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> In Visual Studio moet de eigenschap **Kopiëren naar uitvoermap** voor uw opstartbatchbestand worden ingesteld op **Altijd kopiëren** om er zeker van te zijn dat uw opstartbatchbestand correct is geïmplementeerd in uw project in Azure **(approot-opslaglocatie\\** voor webrollen en **approot** voor werknemersrollen).
> 
> 

## <a name="description-of-task-attributes"></a>Beschrijving van de taakkenmerken
Het volgende beschrijft de kenmerken van het **element Taak** in het bestand [ServiceDefinition.csdef:]

**commandLine** - Hiermee geeft u de opdrachtregel voor de opstarttaak op:

* De opdracht, met optionele opdrachtregelparameters, waarmee de opstarttaak wordt gestart.
* Vaak is dit de bestandsnaam van een .cmd of .bat batch bestand.
* De taak is relatief\\ten opzichte van de map AppRoot-opslag voor de implementatie. Omgevingsvariabelen worden niet uitgebreid bij het bepalen van het pad en het bestand van de taak. Als uitbreiding van de omgeving vereist is, u een klein .cmd-script maken dat uw opstarttaak aanroept.
* Kan een consoletoepassing zijn of een batchbestand waarmee een [PowerShell-script](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)wordt gestart.

**executionContext** - Hiermee geeft u het bevoegdhedenniveau voor de opstarttaak op. Het bevoegdheidsniveau kan worden beperkt of verhoogd:

* **Beperkt**  
  De opstarttaak wordt uitgevoerd met dezelfde bevoegdheden als de rol. Wanneer het kenmerk **executionContext** voor het element [Runtime] ook **beperkt**is, worden gebruikersbevoegdheden gebruikt.
* **Verhoogde**  
  De opstarttaak wordt uitgevoerd met beheerdersbevoegdheden. Hierdoor kunnen opstarttaken programma's installeren, IIS-configuratiewijzigingen aanbrengen, registerwijzigingen en andere taken op administratorniveau uitvoeren, zonder het bevoegdhedenniveau van de rol zelf te verhogen.  

> [!NOTE]
> Het bevoegdheidsniveau van een opstarttaak hoeft niet hetzelfde te zijn als de rol zelf.
> 
> 

**taskType** - Hiermee geeft u de manier op waarop een opstarttaak wordt uitgevoerd.

* **Eenvoudige**  
  Taken worden één voor één synchroon uitgevoerd in de volgorde die is opgegeven in het bestand [ServiceDefinition.csdef.] Wanneer een **eenvoudige** opstarttaak eindigt met een **foutniveau** van nul, wordt de volgende **eenvoudige** opstarttaak uitgevoerd. Als er geen **eenvoudige** opstarttaken meer moeten worden uitgevoerd, wordt de rol zelf gestart.   
  
  > [!NOTE]
  > Als de **eenvoudige** taak eindigt met een **foutniveau**zonder nul, wordt de instantie geblokkeerd. Latere **eenvoudige** opstarttaken en de rol zelf worden niet gestart.
  > 
  > 
  
    Als u ervoor wilt zorgen dat uw batchbestand `EXIT /B 0` eindigt met een **foutniveau** van nul, voert u de opdracht uit aan het einde van het batchbestandsproces.
* **Achtergrond**  
  Taken worden asynchroon uitgevoerd, parallel met het opstarten van de rol.
* **Voorgrond**  
  Taken worden asynchroon uitgevoerd, parallel met het opstarten van de rol. Het belangrijkste verschil tussen een **voorgrond-** en een **achtergrondtaak** is dat een **voorgrondtaak** voorkomt dat de rol wordt gerecycled of afgesloten totdat de taak is beëindigd. De **achtergrondtaken** hebben deze beperking niet.

## <a name="environment-variables"></a>Omgevingsvariabelen
Omgevingsvariabelen zijn een manier om informatie door te geven aan een opstarttaak. U het pad bijvoorbeeld naar een blob plaatsen met een te installeren programma of poortnummers die uw rol zal gebruiken, of instellingen om functies van uw opstarttaak te beheren.

Er zijn twee soorten omgevingsvariabelen voor opstarttaken; statische omgevingsvariabelen en omgevingsvariabelen op basis van leden van de klasse [RoleEnvironment.] Beide bevinden zich in de sectie [Omgeving] van het bestand [ServiceDefinition.csdef] en beide gebruiken het kenmerk [Variable] element en **name.**

Statische omgevingsvariabelen gebruiken het **waardekenmerk** van het element [Variabele.] In het bovenstaande voorbeeld wordt de omgevingsvariabele **MyVersionNumber gemaakt** met een statische waarde van "**1.0.0.0**". Een ander voorbeeld is het maken van een **stagingorProduction-omgevingsvariabele** die u handmatig instellen op waarden van**staging**of**productie**om verschillende opstartacties uit te voeren op basis van de waarde van de omgevingsvariabele **StagingOrProduction.**

Omgevingsvariabelen op basis van leden van de klasse RoleEnvironment gebruiken het **waardekenmerk** van het element [Variabele] niet. In plaats daarvan wordt het onderliggende [element RoleInstanceValue,] met de juiste **XPath-kenmerkwaarde,** gebruikt om een omgevingsvariabele te maken op basis van een specifiek lid van de klasse [RoleEnvironment.] Waarden voor het kenmerk **XPath** om toegang te krijgen tot verschillende [waarden voor roleenvironment] vindt u [hier](cloud-services-role-config-xpath.md).

Als u bijvoorbeeld een omgevingsvariabele wilt maken die "**true**" is wanneer de instantie wordt uitgevoerd in de compute emulator en "**false**" wanneer deze in de cloud wordt uitgevoerd, gebruikt u de volgende [variabele] en [RoleInstanceValue-elementen:]

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het uitvoeren van een aantal [veelvoorkomende opstarttaken](cloud-services-startup-tasks-common.md) met uw Cloud Service.

[Pakket](cloud-services-model-and-package.md) uw Cloud Service.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Taak]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Opstarten]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Omgeving]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variabele]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceWaarde]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



