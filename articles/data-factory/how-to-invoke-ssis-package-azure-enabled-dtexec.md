---
title: Voer SQL Server Integration Services SSIS-pakketten uit met het met Azure ingeschakelde dtexec-hulp programma | Microsoft Docs
description: Meer informatie over het uitvoeren van SQL Server Integration Services (SSIS)-pakketten met het dtexec-hulp programma Azure ingeschakeld.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4bd2f4f9ea4c451f312f05fd42fbc1ba433ab
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181325"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>Run SQL Server Integration Services (SSIS)-pakketten met Azure-dtexec-hulp programma
In dit artikel wordt het opdracht prompt hulpprogramma voor Azure- **dtexec** (**AzureDTExec**) beschreven.  Het wordt gebruikt om SSIS-pakketten uit te voeren op Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF).

Het traditionele **dtexec** -hulp programma wordt geleverd met SQL Server. Zie [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) Documentation (Engelstalig) voor meer informatie.  Het wordt vaak opgeroepen door Orchestrator/planners van derden, zoals actieve batch, Control-M, enzovoort, om SSIS-pakketten op locatie uit te voeren.  Het hulp programma modern **AzureDTExec** wordt geleverd met het hulp programma SQL Server Management Studio (SSMS).  Het kan ook worden opgeroepen door Orchestrator/planners van derden om SSIS-pakketten uit te voeren in Azure.  Het vereenvoudigt de hijs & verschuiving/migratie van uw SSIS-pakketten naar de Cloud.  Als u na de migratie wilt blijven gebruikmaken van Orchestrator/planners van derden in uw dagelijkse activiteiten, kan deze nu **AzureDTExec** aanroepen in plaats van **dtexec**.

**AzureDTExec** voert uw pakketten uit als EXECUTe SSIS-pakket activiteiten in ADF-pijp lijnen, Zie [SSIS-pakketten uitvoeren als ADF-activiteiten](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) artikel voor meer informatie.  Het kan via SSMS worden geconfigureerd voor het gebruik van een Azure Active Directory (AAD)-toepassing die pijp lijnen in uw ADF genereert.  Het kan ook worden geconfigureerd voor toegang tot bestands systemen/bestands shares/Azure Files waar u uw pakketten opslaat.  Op basis van de waarden die u geeft voor de aanroep opties, genereert **AzureDTExec** een unieke ADF-pijp lijn met de activiteit voor het uitvoeren van SSIS-pakketten.  Als u **AzureDTExec** aanroept met dezelfde waarden voor de opties, wordt de bestaande pijp lijn opnieuw uitgevoerd.  Als u **AzureDTExec** aanroept met nieuwe waarden voor de opties, wordt een nieuwe pijp lijn gegenereerd.

## <a name="prerequisites"></a>Vereisten
Als u **AzureDTExec**wilt gebruiken, downloadt en installeert u de meest recente SSMS (versie 18,3 of hoger [).](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

## <a name="configure-azuredtexec-utility"></a>Het hulp programma AzureDTExec configureren
Als SSMS wordt geïnstalleerd op uw lokale machine, wordt ook **AzureDTExec**geïnstalleerd.  Als u de instellingen wilt configureren, start u SSMS met de optie **als administrator uitvoeren** en selecteert u de trapsgewijze vervolg keuzelijst menu **-item-> naar Azure te migreren-> Azure-DTExec configureren**.

![Het dtexec-menu voor Azure ingeschakeld configureren](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Met deze actie wordt het **AzureDTExecConfig** -venster weer gegeven dat moet worden geopend met beheerders bevoegdheden zodat het kan worden geschreven naar het bestand **AzureDTExec. settings** .  Als u SSMS niet als beheerder hebt uitgevoerd, wordt het venster Gebruikersaccountbeheer (UAC) weer gegeven, zodat u uw beheerders wachtwoord kunt invoeren om uw bevoegdheden uit te breiden.

![Instellingen voor Azure enabled dtexec configureren](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

In het venster **AzureDTExecConfig** kunt u de configuratie-instellingen als volgt opgeven:

- **ApplicationId**: Voer de unieke id in van de AAD-app die u maakt met de juiste machtigingen voor het genereren van pijp lijnen in uw ADF. Zie [een Aad-app en service-principal maken via Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) artikel voor meer informatie.

- **AuthenticationKey**: Voer de verificatie sleutel voor uw AAD-app in.

- **TenantId**: Voer de unieke id in van de AAD-Tenant, waaronder uw AAD-app is gemaakt.

- **SubscriptionId**: Voer de unieke id van het Azure-abonnement in, waaronder de ADF is gemaakt.

- **ResourceGroup**: Voer de naam in van de Azure-resource groep waarin uw ADF is gemaakt.

- **DataFactory**:  Voer de naam van de ADF in, waarbij unieke pijp lijnen met de activiteit Execute SSIS-pakket worden gegenereerd op basis van de waarden van de opties die worden opgegeven bij het aanroepen van **AzureDTExec**.

- **IRName**: Voer de naam van Azure-SSIS IR in uw ADF in, waarbij de pakketten die zijn opgegeven in het UNC-pad (Universal Naming Convention) bij het aanroepen van **AzureDTExec** worden uitgevoerd.

- **PackageAccessDomain**: Voer de referenties van het domein in om toegang te krijgen tot uw pakketten in het UNC-pad dat is opgegeven bij het aanroepen van **AzureDTExec**.

- **PackageAccessUserName**:  Voer de gebruikers naam referentie in om toegang te krijgen tot uw pakketten in het UNC-pad dat is opgegeven bij het aanroepen van **AzureDTExec**.

- **PackageAccessPassword**: Voer de wachtwoord referentie in om toegang te krijgen tot uw pakketten in het UNC-pad dat is opgegeven bij het aanroepen van **AzureDTExec**.

- **LogPath**:  Voer het UNC-pad van de logboekmap in, waarin de logboek bestanden van de pakket uitvoeringen op Azure-SSIS IR worden geschreven.

- **LogLevel**:  Voer het geselecteerde bereik van logboek registratie in **op basis van vooraf gedefinieerde**//,**uitgebreide**/**prestatie** opties voor uw pakket uitvoeringen op Azure-SSIS IR.

- **LogAccessDomain**: Voer de referenties van het domein in voor toegang tot uw Logboekmap in het UNC-pad bij het schrijven van logboek bestanden, vereist wanneer **logPath** is opgegeven en **LogLevel** niet **Null**is.

- **LogAccessUserName**: Voer de gebruikers naam referentie in voor toegang tot uw Logboekmap in het UNC-pad tijdens het schrijven van logboek bestanden, vereist wanneer **logPath** is opgegeven en **LogLevel** niet **Null**is.

- **LogAccessPassword**: Voer de wachtwoord referentie in voor toegang tot uw Logboekmap in het UNC-pad bij het schrijven van logboek bestanden, vereist wanneer **logPath** is opgegeven en **LogLevel** niet **Null**is.

- **PipelineNameHashStrLen**: Voer de lengte van de hash-teken reeksen in die moeten worden gegenereerd op basis van de waarden van de opties die u opgeeft bij het aanroepen van **AzureDTExec**.  De teken reeksen worden gebruikt voor het vormen van unieke namen voor ADF-pijp lijnen die uw pakketten op Azure-SSIS IR uitvoeren.  Een lengte van 32 tekens is voldoende.

Als u van plan bent om uw pakketten en logboek bestanden op te slaan in bestands systemen/bestands shares op locatie, moet u uw Azure-SSIS IR toevoegen aan een VNet dat is verbonden met uw on-premises netwerk, zodat het uw pakketten kan ophalen en uw logboek bestanden kan schrijven, Zie [Azure-SSIS IR toevoegen aan een VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) artikel voor meer informatie.

Om te voor komen dat gevoelige waarden worden weer gegeven in **AzureDTExec. settings** -bestand in tekst zonder opmaak, worden ze gecodeerd in teken reeksen met base64-code ring.  Wanneer u **AzureDTExec**aanroept, worden alle teken reeksen met base64-code ring teruggezet in hun oorspronkelijke waarden.  U kunt het bestand **AzureDTExec. settings** verder beveiligen door de accounts die toegang hebben tot de account te beperken.

## <a name="invoke-azuredtexec-utility"></a>Hulp programma AzureDTExec aanroepen
U kunt **AzureDTExec** aanroepen bij de opdracht regel prompt en de relevante waarden opgeven voor specifieke opties in uw use-case scenario, bijvoorbeeld:

`AzureDTExec.exe
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"
  /Set \package.variables[MyVariable].Value;MyValue
  /De MyEncryptionPassword`

Het aanroepen van **AzureDTExec** biedt vergelijk bare opties als het aanroepen van **dtexec**, Zie [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) -documentatie voor meer informatie.  Dit zijn de opties die momenteel worden ondersteund:

- **/F [estand]** : Laadt een pakket dat is opgeslagen in bestands systeem/bestands share/Azure Files.  Als de waarde voor deze optie kunt u het UNC-pad voor het pakket bestand opgeven in bestands systeem/bestands share/Azure Files met de dtsx-extensie.  Als het UNC-pad is opgegeven, moet u tussen het hele pad aanhalings tekens plaatsen.

- **/Conf [igFile]** : Hiermee geeft u een configuratie bestand waaruit waarden moeten worden opgehaald.  Met deze optie kunt u een runtime-configuratie voor uw pakket instellen die afwijkt van de versie die tijdens de ontwerp fase is opgegeven.  U kunt verschillende instellingen in een XML-configuratie bestand opslaan en deze vervolgens laden vóór de uitvoering van het pakket.  Zie het artikel over [SSIS-pakket configuraties](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) voor meer informatie.  Als de waarde voor deze optie kunt u het UNC-pad voor uw configuratie bestand opgeven in bestands systeem/bestands share/Azure Files met de dtsConfig-extensie.  Als het UNC-pad is opgegeven, moet u tussen het hele pad aanhalings tekens plaatsen.

- **/Conn [eveiliging]** : Hiermee geeft u verbindings reeksen voor bestaande verbindings beheer in uw pakket.  Met deze optie kunt u run-time verbindings reeksen instellen voor bestaande verbindings beheer in uw pakket dat verschilt van het aantal dat tijdens de ontwerp fase is opgegeven.  Als de waarde voor deze optie kunt u deze als volgt opgeven: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **/Set**: Onderdrukt de configuratie van een para meter, variabele, eigenschap, container, logboek provider, foreach-enumerator of verbinding in uw pakket.  Deze optie kan meerdere keren worden opgegeven.  Als de waarde voor deze optie kunt u deze als volgt opgeven: `property_path;value`de waarde van `counter` de `\package.variables[counter].Value;1` variabele wordt bijvoorbeeld vervangen door 1.  U kunt de wizard pakket configuratie gebruiken om de waarde te zoeken, kopiëren en plakken `property_path` van items in uw pakket waarvan u de waarde wilt overschrijven. Zie de documentatie over de [wizard pakket configuratie](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) voor meer informatie.

- **/De [crypt]** : Hiermee stelt u het wacht woord voor ontsleuteling in voor uw pakket dat is geconfigureerd met het beveiligings niveau **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** .

## <a name="next-steps"></a>Volgende stappen
Zodra er unieke pijp lijnen met de activiteit Execute SSIS-pakket worden gegenereerd en worden uitgevoerd na het aanroepen van **AzureDTExec**, kunnen ze worden bewerkt en opnieuw worden gestart op de ADF-Portal. Zie [SSIS-pakketten uitvoeren als ADF-activiteiten](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) artikel voor meer informatie.
