---
title: Gelicentieerde componenten installeren voor runtime van Azure-SSIS-integratie
description: Meer informatie over hoe een ISV betaalde of gelicentieerde aangepaste componenten kan ontwikkelen en installeren voor de runtime azure-SSIS-integratie
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 599b54f8a5d97ee5ed29ce4df16980f456ffb919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74914583"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Betaalde of gelicentieerde aangepaste onderdelen installeren voor Azure-SSIS Integration Runtime

In dit artikel wordt beschreven hoe een ISV betaalde of gelicentieerde aangepaste componenten kan ontwikkelen en installeren voor SQL Server Integration Services (SSIS)-pakketten die in Azure worden uitgevoerd in de runtime van Azure-SSIS-integratie.

## <a name="the-problem"></a>Het probleem

De aard van de runtime azure-SSIS-integratie brengt verschillende uitdagingen met zich mee, waardoor de typische licentiemethoden die worden gebruikt voor de on-premises installatie van aangepaste componenten ontoereikend zijn. Als gevolg hiervan vereist de Azure-SSIS IR een andere aanpak.

-   De knooppunten van de Azure-SSIS IR zijn vluchtig en kunnen op elk gewenst moment worden toegewezen of vrijgegeven. U bijvoorbeeld knooppunten starten of stoppen om de kosten te beheren of op en neer schalen via verschillende knooppuntgroottes. Als gevolg hiervan is het niet langer haalbaar om een componentlicentie van derden aan een bepaald knooppunt te binden met behulp van machinespecifieke informatie zoals MAC-adres of CPU-id.

-   U de Azure-SSIS IR ook in- of uitschalen, zodat het aantal knooppunten op elk gewenst moment kan krimpen of uitbreiden.

## <a name="the-solution"></a>De oplossing

Als gevolg van de beperkingen van traditionele licentiemethoden die in de vorige sectie zijn beschreven, biedt azure-SSIS IR een nieuwe oplossing. Deze oplossing maakt gebruik van Windows-omgevingsvariabelen en SSIS-systeemvariabelen voor het verbinden van licenties en validatie van onderdelen van derden. ISV's kunnen deze variabelen gebruiken om unieke en permanente informatie te verkrijgen voor een Azure-SSIS IR, zoals cluster-id en clusterknooppunttelling. Met deze informatie kunnen ISV's de licentie voor hun component vervolgens binden aan een Azure-SSIS IR *als cluster.* Deze binding maakt gebruik van een id die niet verandert wanneer klanten de Azure-SSIS IR op geen enkele manier starten of stoppen, opschalen of schalen, of opnieuw configureren.

In het volgende diagram worden de typische installatie-, activerings- en licentiebinding en validatiestromen weergegeven voor onderdelen van derden die deze nieuwe variabelen gebruiken:

![Installatie van gelicentieerde onderdelen](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instructies
1. ISV's kunnen hun gelicentieerde componenten aanbieden in verschillende SKU's of lagen (bijvoorbeeld één knooppunt, maximaal 5 knooppunten, maximaal 10 knooppunten, enzovoort). De ISV levert de bijbehorende productcode wanneer klanten een product kopen. De ISV kan ook een Azure Storage blob-container bieden die een ISV Setup-script en bijbehorende bestanden bevat. Klanten kunnen deze bestanden kopiëren naar hun eigen opslagcontainer en deze wijzigen `IsvSetup.exe -pid xxxx-xxxx-xxxx`met hun eigen productcode (bijvoorbeeld door uit te voeren). Klanten kunnen de Azure-SSIS IR vervolgens in- of opnieuw configureren met de SAS URI van hun container als parameter. Zie [Aangepaste instelling voor de Azure-SSIS-integratieruntime](how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie.

2. Wanneer de Azure-SSIS IR is ingericht of opnieuw geconfigureerd, wordt ISV Setup op `SSIS_CLUSTERID` `SSIS_CLUSTERNODECOUNT`elk knooppunt uitgevoerd om de windows-omgevingsvariabelen op te vragen en . Vervolgens verzendt de Azure-SSIS IR zijn cluster-id en de productcode voor het gelicentieerde product naar de ISV-activeringsserver om een activeringssleutel te genereren.

3. Na ontvangst van de activeringssleutel kan ISV Setup de sleutel lokaal opslaan op elk knooppunt (bijvoorbeeld in het register).

4. Wanneer klanten een pakket uitvoeren dat de gelicentieerde component van de ISV gebruikt op een knooppunt van de Azure-SSIS IR, leest het pakket de lokaal opgeslagen activeringssleutel en valideert het deze op de cluster-id van het knooppunt. Het pakket kan ook optioneel het clusterknooppunt tellen rapporteren aan de ISV-activeringsserver.

    Hier is een voorbeeld van code die de activeringssleutel valideert en het aantal clusterknooppunten rapporteert:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV-partners

U vindt een lijst met ISV-partners die hun componenten en extensies hebben aangepast voor de Azure-SSIS IR aan het einde van deze blogpost - [Enterprise Edition, Custom Setup en 3rd Party Extensibility for SSIS in ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Volgende stappen

-   [Aangepaste installatie voor de runtime van Azure-SSIS-integratie](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition van de runtime azure-SSIS-integratie](how-to-configure-azure-ssis-ir-enterprise-edition.md)
