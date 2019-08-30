---
title: Windows Virtual Desktop Preview Session Host automatisch schalen-Azure
description: Hierin wordt beschreven hoe u het script voor automatisch schalen instelt voor hosts met virtuele bureau blad-sessies voor Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 7babfca617ab42da615518726d1b1d4cafe112b5
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163239"
---
# <a name="automatically-scale-session-hosts"></a>Sessiehosts automatisch schalen

Voor veel Windows-preview-implementaties van virtuele Bureau bladen in azure vertegenwoordigen de kosten van de virtuele machine een aanzienlijk deel van de totale kosten voor de implementatie van Windows virtueel bureau blad. Om de kosten te reduceren, kunt u het beste de virtuele machines van de sessiehost afsluiten en de toewijzing ervan ongedaan maken tijdens daluren, en ze vervolgens opnieuw opstarten tijdens piek uren.

In dit artikel wordt gebruikgemaakt van een eenvoudig schaalbaar script om de virtuele machines van de host automatisch in uw Windows Virtual Desktop-omgeving te schalen. Voor meer informatie over hoe het schaal script werkt, zie de sectie [How to Scaling script](#how-the-scaling-script-works) (Engelstalig).

## <a name="prerequisites"></a>Vereisten

De omgeving waarin u het script uitvoert, moet het volgende doen:

- Een Windows-Tenant en-account voor virtueel bureau blad of een service-principal met machtigingen voor het opvragen van de Tenant (zoals RDS Contributor).
- Vm's van de hostgroep zijn geconfigureerd en geregistreerd bij de virtuele bureau blad-service van Windows.
- Een extra virtuele machine waarop de geplande taak wordt uitgevoerd via taak planner en netwerk toegang heeft tot sessie-hosts. Dit wordt later in het document verwezen als Scaler VM.
- De [Microsoft Azure Resource Manager Power shell-module](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) die is geïnstalleerd op de VM waarop de geplande taak wordt uitgevoerd.
- De [Windows Virtual Desktop Power shell-module](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) die is geïnstalleerd op de VM waarop de geplande taak wordt uitgevoerd.

## <a name="recommendations-and-limitations"></a>Aanbevelingen en beperkingen

Houd bij het uitvoeren van het schaal script de volgende zaken in acht:

- Met dit script voor schalen kan slechts één hostgroep per exemplaar van de geplande taak waarmee het schaal script wordt uitgevoerd, worden afgehandeld.
- De geplande taken voor het uitvoeren van schaal scripts moeten zich op een VM bevindt die altijd is ingeschakeld.
- Maak een afzonderlijke map voor elk exemplaar van het schaal script en de configuratie.
- Dit script biedt geen ondersteuning voor het aanmelden als beheerder voor een virtueel bureau blad van Windows met Azure AD-gebruikers accounts waarvoor multi-factor Authentication is vereist. We raden u aan service-principals te gebruiken voor toegang tot de virtueel-bureaublad service van Windows en Azure. Volg [deze zelf studie](create-service-principal-role-powershell.md) om een Service-Principal en een roltoewijzing te maken met Power shell.
- De SLA-garantie van Azure geldt alleen voor Vm's in een beschikbaarheidsset. In de huidige versie van het document wordt een omgeving met één virtuele machine beschreven, die mogelijk niet aan de beschikbaarheids vereisten voldoet.

## <a name="deploy-the-scaling-script"></a>Het schaal script implementeren

In de volgende procedures wordt uitgelegd hoe u het schaal script implementeert.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Uw omgeving voorbereiden voor het schaal script

Bereid eerst uw omgeving voor op het schaal script:

1. Meld u aan bij de virtuele machine (scaleer VM) waarmee de geplande taak wordt uitgevoerd met een domein beheerders account.
2. Maak een map op de schaal machine om het schaal script en de configuratie ervan te bewaren (bijvoorbeeld **C:\\schalen-HostPool1**).
3. Down load de bestanden **basicScale. ps1**, **config. XML**en **functions-PSStoredCredentials. ps1** en de map **PowershellModules** van de [opslag plaats](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) voor het schalen van het script en kopieer deze naar de map die u in stap 2 hebt gemaakt. Er zijn twee manieren om de bestanden op te halen voordat u ze naar de scaleer-machine kopieert:
    - Kloon de Git-opslag plaats naar uw lokale computer.
    - Bekijk de onbewerkte versie van elk bestand, kopieer en plak de inhoud van elk bestand in een tekst editor en sla de bestanden vervolgens op met de bijbehorende bestands naam en dit bestands type. 

### <a name="create-securely-stored-credentials"></a>Veilig opgeslagen referenties maken

Vervolgens moet u de veilig opgeslagen referenties maken:

1. Open Power shell ISE als beheerder.
2. Importeer de RDS Power shell-module door de volgende cmdlet uit te voeren:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Open het bewerkings venster en laad het bestand **Function-PSStoredCredentials. ps1** en voer vervolgens het hele script uit (F5)
4. Voer de volgende cmdlet uit:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Bijvoorbeeld: **set-variable-name sleutelpad-Scope Global-value "c:\\scaling-HostPool1"**
5. Voer de cmdlet **New-StoredCredential- \$** sleutelpad. Wanneer u hierom wordt gevraagd, voert u de referenties voor uw Windows-bureau blad in met machtigingen voor het opvragen van de hostgroep (de hostgroep is opgegeven in het **bestand config. XML**).
    - Als u verschillende service-principals of standaard account gebruikt, voert u de cmdlet **New-StoredCredential \$-sleutelpad pad** naar een keer uit voor elk account om lokale opgeslagen referenties te maken.
6. Voer **Get-StoredCredential-List** uit om te bevestigen dat de referenties zijn gemaakt.

### <a name="configure-the-configxml-file"></a>Het bestand config. xml configureren

Voer de relevante waarden in de volgende velden in om de instellingen voor het schalen van het script bij te werken in config. XML:

| Veld                     | Description                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | De Azure AD-Tenant-ID waarmee het abonnement wordt gekoppeld waarbij de Vm's van de host worden uitgevoerd     |
| AADApplicationId              | Service-Principal-toepassings-ID                                                       |
| AADServicePrincipalSecret     | Dit kan tijdens de test fase worden ingevoerd, maar moet leeg blijven nadat u referenties hebt gemaakt met **functions-PSStoredCredentials. ps1**    |
| currentAzureSubscriptionId    | De ID van het Azure-abonnement waar de host-Vm's voor de sessie worden uitgevoerd                        |
| tenantName                    | Tenant naam van virtueel bureau blad van Windows                                                    |
| hostPoolName                  | Naam van de Windows-hostgroep voor virtueel bureau blad                                                 |
| RDBroker                      | URL naar de WVD-service, standaard waarde https\/:/rdbroker.WVD.Microsoft.com             |
| Gebruikersnaam                      | De ID van de Service-Principal-toepassing (dit is mogelijk dezelfde service-principal als in AADApplicationId) of standaard gebruiker zonder multi-factor Authentication |
| isServicePrincipal            | Geaccepteerde waarden zijn **waar** of onwaar. Hiermee wordt aangegeven of de tweede set met gebruikte referenties een service-principal of een standaard account is. |
| BeginPeakTime                 | Wanneer de piek gebruiks tijd begint                                                            |
| EndPeakTime                   | Wanneer de piek gebruiks tijd eindigt                                                              |
| TimeDifferenceInHours         | Tijd verschil tussen lokale tijd en UTC, in uren                                   |
| SessionThresholdPerCPU        | Maximum aantal sessies per CPU-drempel die wordt gebruikt om te bepalen wanneer een nieuwe sessiehost van een host moet worden gestart tijdens piek uren.  |
| MinimumNumberOfRDSH           | Minimum aantal Vm's van de hostgroep dat moet worden uitgevoerd tijdens de gebruiks tijd buiten de piek             |
| LimitSecondsToForceLogOffUser | Het aantal seconden dat moet worden gewacht voordat gebruikers zich afmelden. Als deze waarde is ingesteld op 0, kunnen gebruikers zich niet afmelden.  |
| LogOffMessageTitle            | Titel van het bericht dat naar een gebruiker wordt verzonden voordat ze worden afgemeld                  |
| LogOffMessageBody             | Hoofd tekst van het waarschuwings bericht dat naar gebruikers wordt verzonden voordat ze worden afgemeld. Bijvoorbeeld: "deze computer wordt over X minuten afgesloten. Sla uw werk op en meld u af. " |

### <a name="configure-the-task-scheduler"></a>De taak planner configureren

Nadat u het XML-configuratie bestand hebt geconfigureerd, moet u de taak planner configureren om het basicScaler. ps1-bestand met een regel matig interval uit te voeren.

1. Start de **taak planner**.
2. Selecteer in het venster **taak planner** de optie **taak maken...**
3. In **het dialoog venster taak maken** selecteert u het tabblad **Algemeen** , voert u een **naam** in (bijvoorbeeld ' dynamische RDSH '), selecteert u **uitvoeren of gebruiker is aangemeld of niet** en **met de hoogste bevoegdheden uitvoeren**.
4. Ga naar het tabblad **Triggers** en selecteer vervolgens **Nieuw...**
5. Controleer in het dialoog venster **nieuwe trigger** onder **Geavanceerde instellingen**de optie **taak herhalen elke** en selecteer de gewenste periode en duur (bijvoorbeeld **15 minuten** of oneindig).
6. Selecteer het tabblad **acties** en **Nieuw...**
7. Voer in het dialoog venster **nieuwe actie** **Power shell. exe** in het veld **programma/script** in en **\\Voer vervolgens\\C: basicScale. ps1** in het veld **toevoegen (optioneel)** in.
8. Ga naar de tabbladen **voor waarden** en **instellingen** en selecteer **OK** om de standaard instellingen voor elk te accepteren.
9. Voer het wacht woord in voor het beheerders account waarop u het schaal script wilt uitvoeren.

## <a name="how-the-scaling-script-works"></a>Hoe het schaal script werkt

Met dit schaal script worden de instellingen van een config. XML-bestand, inclusief het begin en het einde van de piek gebruiks periode gedurende de dag, gelezen.

Tijdens de piek gebruiks tijd controleert het script het huidige aantal sessies en de huidige actieve RDSH-capaciteit voor elke hostgroep. Er wordt berekend of de actieve host-Vm's voldoende capaciteit hebben voor de ondersteuning van bestaande sessies op basis van de para meter SessionThresholdPerCPU die is gedefinieerd in het bestand config. XML. Als dat niet het geval is, start het script extra Vm's voor de host van hosts in de hostgroep.

Tijdens de gebruiks tijd bepaalt het script welke host-Vm's moeten worden afgesloten op basis van de para meter MinimumNumberOfRDSH in het bestand config. XML. Met het script worden de Vm's van de host ingesteld op drain-modus om te voor komen dat nieuwe sessies verbinding maken met de hosts. Als u de para meter **LimitSecondsToForceLogOffUser** in het bestand config. XML instelt op een positieve waarde die niet gelijk is aan nul, zal het script alle momenteel aangemelde gebruikers op de hoogte stellen om werk op te slaan, de ingestelde tijd te wachten en de gebruikers vervolgens af te dwingen om af te melden. Zodra alle gebruikers sessies zijn afgemeld op een host-VM, wordt de server door het script afgesloten.

Als u de para meter **LimitSecondsToForceLogOffUser** in het bestand config. XML instelt op nul, staat het script de instelling van de sessie configuratie toe in de eigenschappen van de hostgroep om het afmelden van gebruikers sessies af te handelen. Als er sessies op een host-VM worden uitgevoerd, wordt de VM van de host verlaten. Als er geen sessies zijn, wordt de host-VM van de sessie afgesloten door het script.

Het script is ontworpen om periodiek te worden uitgevoerd op de scaleer-VM-server met behulp van de taak planner. Selecteer het juiste tijds interval op basis van de grootte van uw Extern bureaublad-services omgeving en houd er rekening mee dat het starten en afsluiten van virtuele machines enige tijd kan duren. U wordt aangeraden om de 15 minuten het schaal script uit te voeren.

## <a name="log-files"></a>Logboekbestanden

Het schaal script maakt twee logboek bestanden: **WVDTenantScale. log** en **WVDTenantUsage. log**. In het bestand **WVDTenantScale. log** worden de gebeurtenissen en fouten (indien aanwezig) geregistreerd tijdens de uitvoering van het schaal script.

In het bestand **WVDTenantUsage. log** wordt het actieve aantal kernen en actieve aantal virtuele machines telkens vastgelegd wanneer u het schaal script uitvoert. U kunt deze informatie gebruiken om het werkelijke gebruik van Microsoft Azure Vm's en de kosten te schatten. Het bestand wordt opgemaakt als door komma's gescheiden waarden, waarbij elk item de volgende informatie bevat:

>tijd, hostgroep, kern geheugens, Vm's

De naam van het bestand kan ook worden gewijzigd in een CSV-extensie, wordt geladen in micro soft Excel en geanalyseerd.
