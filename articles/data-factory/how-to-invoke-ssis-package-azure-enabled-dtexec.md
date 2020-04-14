---
title: SQL Server Integration Services -pakketten (SSIS) uitvoeren met het Azure-hulpprogramma dtexec
description: Meer informatie over het uitvoeren van SQL Server Integration Services-pakketten (SSIS) met het Azure-hulpprogramma dtexec.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 006d4fa9ed09170a423e796e893b817e079e861b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261925"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>SQL Server Integration Services-pakketten uitvoeren met het Azure-hulpprogramma dtexec
In dit artikel wordt het azure-enabled dtexec -functie (AzureDTExec) beschreven. Het wordt gebruikt om SQL Server Integration Services (SSIS)-pakketten uit te voeren op de Azure-SSIS Integration Runtime (IR) in Azure Data Factory.

Het traditionele dtexec-hulpprogramma wordt geleverd met SQL Server. Zie [dtexec utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)voor meer informatie. Het wordt vaak aangeroepen door externe orchestrators of planners, zoals ActiveBatch en Control-M, om SSIS-pakketten on-premises uit te voeren. 

Het moderne AzureDTExec-hulpprogramma wordt geleverd met een SQL Server Management Studio -tool (SSMS). Het kan ook worden aangeroepen door externe orchestrators of planners om SSIS-pakketten in Azure uit te voeren. Het vergemakkelijkt het opheffen en verschuiven of migreren van uw SSIS-pakketten naar de cloud. Als u na de migratie orkestortors of planners van derden wilt blijven gebruiken in uw dagelijkse bewerkingen, kunnen ze nu een beroep doen op AzureDTExec in plaats van dtexec.

AzureDTExec voert uw pakketten uit als SSIS-pakketactiviteiten uitvoeren in Data Factory-pijplijnen. Zie [SSIS-pakketten uitvoeren als Azure Data Factory-activiteiten voor](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)meer informatie. 

AzureDTExec kan via SSMS worden geconfigureerd om een Azure Active Directory-toepassing (Azure AD) te gebruiken die pijplijnen genereert in uw gegevensfabriek. Het kan ook worden geconfigureerd om toegang te krijgen tot bestandssystemen, bestandsshares of Azure-bestanden waar u uw pakketten opslaat. Op basis van de waarden die u geeft voor de aanroepopties, genereert en voert AzureDTExec een unieke Data Factory-pijplijn uit met een SSIS-pakketactiviteit uitvoeren. Als u een beroep doet op AzureDTExec met dezelfde waarden voor de opties, wordt de bestaande pijplijn opnieuw uitgevoerd.

## <a name="prerequisites"></a>Vereisten
Als u AzureDTExec wilt gebruiken, downloadt en installeert u de nieuwste versie van SSMS, versie 18.3 of hoger. Download het van [deze website.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

## <a name="configure-the-azuredtexec-utility"></a>Het AzureDTExec-hulpprogramma configureren
Als u SSMS op uw lokale machine installeert, wordt ook AzureDTExec geïnstalleerd. Als u de instellingen wilt configureren, start u SSMS met de optie **Uitvoeren als administrator.** Selecteer vervolgens **Extra** > **migreren naar Azure** > **Configure Azure-enabled DTExec**.

![Dtexec-menu met Azure-ingeschakeld](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Met deze actie wordt een **AzureDTExecConfig-venster** geopend dat moet worden geopend met beheerdersbevoegdheden om in het *azureDTExec.settings-bestand* te schrijven. Als u SSMS niet als beheerder hebt uitgevoerd, wordt een UAC-venster (User Account Control) geopend. Voer uw beheerderswachtwoord in om uw bevoegdheden te verhogen.

![Dtexec-instellingen met Azure-instellingen configureren](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Voer in het venster **AzureDTExecConfig** de volgende configuratie-instellingen in:

- **ApplicationId:** voer de unieke id in van de Azure AD-app die u maakt met de juiste machtigingen om pijplijnen in uw gegevensfabriek te genereren. Zie [Een Azure AD-app en serviceprincipal maken via Azure-portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)voor meer informatie.
- **Verificatiesleutel:** voer de verificatiesleutel in voor uw Azure AD-app.
- **TenantId:** voer de unieke id in van de Azure AD-tenant, waaronder uw Azure AD-app is gemaakt.
- **DataFactory:** Voer de naam in van uw gegevensfabriek waarin unieke pijplijnen met SSIS-pakketactiviteit uitvoeren worden gegenereerd op basis van de waarden van de geboden opties wanneer u AzureDTExec aanroept.
- **IRName**: Voer de naam in van de Azure-SSIS IR in uw gegevensfabriek, waarop de pakketten die zijn opgegeven in hun UNC-pad (Universal Naming Convention) worden uitgevoerd wanneer u AzureDTExec aanroept.
- **PipelineNameHashStrLen:** Voer de lengte in van hashstrings die moeten worden gegenereerd op basis van de waarden van de opties die u opgeeft wanneer u AzureDTExec aanroept. De tekenreeksen worden gebruikt om unieke namen te vormen voor Data Factory-pijplijnen die uw pakketten uitvoeren op de Azure-SSIS IR. Meestal volstaat een lengte van 32 tekens.
- **ResourceGroup:** voer de naam in van de Azure-brongroep waarin uw gegevensfabriek is gemaakt.
- **SubscriptionId**: Voer de unieke id van het Azure-abonnement in, waaronder uw gegevensfabriek is gemaakt.
- **LogAccessDomain:** Voer de domeinreferenties in om toegang te krijgen tot uw logboekmap in het UNC-pad wanneer u logboekbestanden schrijft, wat vereist is wanneer **LogPath** is opgegeven en **LogLevel** niet **null**is.
- **LogAccessPassword:** Voer de wachtwoordreferenties in om toegang te krijgen tot uw logboekmap in het UNC-pad wanneer u logboekbestanden schrijft, wat vereist is wanneer **LogPath** is opgegeven en **LogLevel** niet **null**is.
- **LogAccessUserName:** Voer de gebruikersnaam in om toegang te krijgen tot uw logmap in het UNC-pad wanneer u logboekbestanden schrijft, wat vereist is wanneer **LogPath** is opgegeven en **LogLevel** niet **null**is.
- **LogLevel:** Voer het geselecteerde bereik van logboekregistratie in van vooraf gedefinieerde **null-,** **Basis-,** **Verbose-** of **Prestatieopties** voor uw pakketuitvoeringen op azure-SSIS IR.
- **LogPath:** Voer het UNC-pad van de logboekmap in, waarin logboekbestanden van uw pakketuitvoeringen op de Azure-SSIS IR zijn geschreven.
- **PackageAccessDomain:** Voer de domeinreferenties in om toegang te krijgen tot uw pakketten in hun UNC-pad dat is opgegeven wanneer u AzureDTExec aanroept.
- **PackageAccessPassword:** voer de wachtwoordreferenties in om toegang te krijgen tot uw pakketten in hun UNC-pad dat is opgegeven wanneer u AzureDTExec aanroept.
- **PackageAccessUserName:** Voer de gebruikersnaam in om toegang te krijgen tot uw pakketten in hun UNC-pad dat is opgegeven wanneer u AzureDTExec aanroept.

Als u uw pakketten en logboekbestanden wilt opslaan in bestandssystemen of on-premises bestandsshares, sluit u uw Azure-SSIS IR aan bij een virtueel netwerk dat is aangesloten op uw on-premises netwerk, zodat u uw pakketten kan ophalen en uw logboekbestanden kan schrijven. Zie [Een Azure-SSIS IR deelnemen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)voor meer informatie.

Om te voorkomen dat gevoelige waarden in het bestand *AzureDTExec.settings* in platte tekst worden weergegeven, coderen we ze in tekenreeksen van Base64-codering. Wanneer u AzureDTExec aanroept, worden alle door Base64 gecodeerde tekenreeksen teruggecodeerd naar hun oorspronkelijke waarden. U het bestand *AzureDTExec.settings* verder beveiligen door de accounts te beperken die er toegang toe hebben.

## <a name="invoke-the-azuredtexec-utility"></a>Het AzureDTExec-hulpprogramma aanroepen
U AzureDTExec aanroepen bij de opdrachtregelprompt en de relevante waarden opgeven voor specifieke opties in uw use-case scenario.

Het hulpprogramma is `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`geïnstalleerd op . U het pad toevoegen aan de omgevingsvariabele 'PATH' om het overal aan te roepen.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Een beroep doen op AzureDTExec biedt vergelijkbare opties als een beroep doen op dtexec. Zie [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)voor meer informatie. Dit zijn de opties die momenteel worden ondersteund:

- **/F[ile]**: hiermee wordt een pakket geladen dat is opgeslagen in bestandssysteem, bestandsshare of Azure-bestanden. Als waarde voor deze optie u het UNC-pad voor uw pakketbestand opgeven in bestandssysteem, bestandsshare of Azure-bestanden met de .dtsx-extensie. Als het opgegeven UNC-pad ruimte bevat, plaatst u aanhalingstekens rond het hele pad.
- **/Conf[igFile]**: Hiermee geeft u een configuratiebestand op om waarden uit te extraheren. Met deze optie u een runtime-configuratie voor uw pakket instellen die verschilt van de configuratie die is opgegeven tijdens het ontwerp. U verschillende instellingen opslaan in een XML-configuratiebestand en deze vervolgens laden voordat het pakket wordt uitgevoerd. Zie [SSIS-pakketconfiguraties](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)voor meer informatie. Als u de waarde voor deze optie wilt opgeven, gebruikt u het UNC-pad voor uw configuratiebestand in bestandssysteem, bestandsshare of Azure-bestanden met de dtsConfig-extensie. Als het opgegeven UNC-pad ruimte bevat, plaatst u aanhalingstekens rond het hele pad.
- **/Conn[uitwerping]**: Hiermee geeft u verbindingstekenreeksen op voor bestaande verbindingsbeheerders in uw pakket. Met deze optie u runtime-verbindingstekenreeksen instellen voor bestaande verbindingsbeheerders in uw pakket die verschillen van de tekenreeksen die tijdens het ontwerp zijn opgegeven. Geef de waarde voor deze `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`optie als volgt op: .
- **/Set**: overschrijft de configuratie van een parameter, variabele, eigenschap, container, logprovider, Foreach enumerator of verbinding in uw pakket. Deze optie kan meerdere keren worden opgegeven. Geef de waarde voor deze `property_path;value`optie als volgt op: . Bijvoorbeeld, `\package.variables[counter].Value;1` overschrijft de `counter` waarde van variabele als 1. U de wizard **Pakketconfiguratie** gebruiken om de `property_path` waarde van items in uw pakket waarvan u de waarde wilt overschrijven, te zoeken, te kopiëren en te plakken. Zie [wizard Pakketconfiguratie](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)voor meer informatie .
- **/De[crypt]**: Hiermee stelt u het decryptiewachtwoord in voor uw pakket dat is geconfigureerd met het **encryptAllWithPassword**/**EncryptSensitiveWithPassword-beveiligingsniveau.**

> [!NOTE]
> Als u een beroep doet op AzureDTExec met nieuwe waarden voor de opties, genereert u een nieuwe pijplijn, behalve de optie **/De[cript]**.

## <a name="next-steps"></a>Volgende stappen

Nadat unieke pijplijnen met de activiteit SSIS-pakket uitvoeren erin worden gegenereerd en uitgevoerd wanneer u AzureDTExec aanroept, kunnen ze worden gecontroleerd op de Portal Gegevensfabriek. U er ook Data Factory-triggers aan toewijzen als u ze wilt orkestreren/plannen met Data Factory. Zie [SSIS-pakketten uitvoeren als Data Factory-activiteiten voor](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)meer informatie.

> [!WARNING]
> De gegenereerde pijplijn wordt naar verwachting alleen gebruikt door AzureDTExec. De eigenschappen of parameters kunnen in de toekomst veranderen, dus wijzig of gebruik ze niet voor andere doeleinden. Wijzigingen kunnen AzureDTExec verbreken. Als dit gebeurt, verwijdert u de pijplijn. AzureDTExec genereert een nieuwe pijplijn de volgende keer dat deze wordt aangeroepen.