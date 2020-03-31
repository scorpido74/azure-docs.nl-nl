---
title: Bestandsintegriteit bewaken in Azure Security Center
description: Meer informatie over het vergelijken van basislijnen met Bestandsintegriteitscontrole in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664413"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Basislijnen vergelijken met behulp van bewaking bestandsintegriteit (FIM)

File Integrity Monitoring (FIM) informeert u wanneer er wijzigingen optreden in gevoelige gebieden in uw resources, zodat u ongeautoriseerde activiteiten onderzoeken en aanpakken. FIM controleert Windows-bestanden, Windows-registers en Linux-bestanden.

In dit onderwerp wordt uitgelegd hoe u FIM inschakelt op de bestanden en registers. Zie [Bestandsintegriteitscontrole in Azure Security Center](security-center-file-integrity-monitoring.md)voor meer informatie over FIM.

## <a name="why-use-fim"></a>Waarom FIM gebruiken?

Besturingssysteem, toepassingen en bijbehorende configuraties bepalen het gedrag en de beveiligingsstatus van uw resources. Daarom richten aanvallers zich op de bestanden die uw resources beheren, om het besturingssysteem van een bron in te halen en/of activiteiten uit te voeren zonder te worden gedetecteerd.

In feite vereisen veel nalevingsnormen voor regelgeving, zoals PCI-DSS & ISO 17799, de implementatie van FIM-controles.  

## <a name="enable-built-in-recursive-registry-checks"></a>Ingebouwde recursieve registercontroles inschakelen

De standaardstandaardstandaardstandaards van de FIM-registerhive bieden een handige manier om recursieve wijzigingen binnen gemeenschappelijke beveiligingsgebieden te controleren.  Een tegenstander kan bijvoorbeeld een script configureren om in LOCAL_SYSTEM context uit te voeren door een uitvoering bij het opstarten of afsluiten te configureren.  Als u dit type wijzigingen wilt controleren, schakelt u de ingebouwde controle in.  

![Register](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Recursieve controles zijn alleen van toepassing op aanbevolen beveiligingskasten en niet op aangepaste registerpaden.  

## <a name="adding-a-custom-registry-check"></a>Een aangepaste registercontrole toevoegen

FIM-basislijnen beginnen met het identificeren van kenmerken van een bekende goede status voor het besturingssysteem en de ondersteunende toepassing.  In dit voorbeeld richten we ons op de wachtwoordbeleidsconfiguraties voor Windows Server 2008 en hoger.


|Policy Name                 | Registerinstelling|
|---------------------------------------|-------------|
|Domeincontroller: wijzigen van wachtwoorden van computeraccounts weigeren| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Lid van domein: gegevens in beveiligd kanaal digitaal versleutelen of ondertekenen (altijd)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Lid van domein: gegevens in beveiligd kanaal digitaal versleutelen (indien mogelijk)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Lid van domein: gegevens in beveiligd kanaal digitaal ondertekenen (indien mogelijk)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Lid van domein: geen systeemonderhoud van wachtwoord van computeraccount|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Lid van domein: het wachtwoord van het machineaccount heeft de maximale leeftijd bereikt|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Lid van domein: sterke sessiesleutel verplicht (Windows 2000 of hoger)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Netwerkbeveiliging: NTLM beperken: NTLM-verificatie in dit domein|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Netwerkbeveiliging: NTLM beperken: uitzonderingen voor servers in dit domein toevoegen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Netwerkbeveiliging: NTLM beperken: NTLM-verificatie controleren in dit domein|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Zie de [referentiespreadsheet Groepsbeleidsinstellingen](https://www.microsoft.com/download/confirmation.aspx?id=25250)voor meer informatie over registerinstellingen die worden ondersteund door verschillende versies van het besturingssysteem.

*Ga als volgt te werk om FIM te configureren om registerbasislijnen te controleren:*

1. Voer in het venster **Windows-register toevoegen voor het bijhouden van wijzigingen** in het tekstvak **Windows-registersleutel** de registersleutel in.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![FIM inschakelen in een register](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Wijzigingen in Windows-bestanden bijhouden

1. Voer in het venster **Windows-bestand toevoegen voor bijhouden wijzigen** in het tekstvak **Pad invoeren** de map in die de bestanden bevat die u wilt bijhouden. In het voorbeeld in de volgende afbeelding bevindt **Contoso Web App** zich in de D:\ binnen de **mapstructuur van ContosWebApp.**  
1. Maak een aangepaste Windows-bestandsinvoer door een naam van de instellingsklasse op te geven, herhaling in te schakelen en de bovenste map op te geven met een joker-achtervoegsel (*).

    ![FIM inschakelen in een bestand](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Wijzigingsgegevens ophalen

Gegevens over bestandsintegriteitscontrole bevinden zich in de tabelset Azure Log Analytics / ConfigurationChange.  

 1. Stel een tijdsbereik in om een overzicht van wijzigingen per resource op te halen.
In het volgende voorbeeld halen we alle wijzigingen van de afgelopen veertien dagen op in de categorieën register en bestanden:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Ga als het gaat om details van de registerwijzigingen:

    1. **Bestanden** verwijderen uit de **where-clausule,** 
    1. Verwijder de summarisatieregel en vervang deze door een bestelclausule:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Rapporten kunnen worden geëxporteerd naar CSV voor archivering en/of gekanaliseerd naar een Power BI-rapport.  

![FIM-gegevens](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)