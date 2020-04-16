---
title: Problemen met pakketuitvoering oplossen in de runtime van de SSIS-integratie
description: Dit artikel biedt richtlijnen voor het oplossen van problemen voor de uitvoering van SSIS-pakketten in de runtime van SSIS-integratie
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 8c85a652cde840336c51e1a5b5459f9dc591e0be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414675"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Problemen met pakketuitvoering oplossen in de runtime van de SSIS-integratie

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dit artikel bevat de meest voorkomende fouten die u vinden wanneer u SQL Server Integration Services-pakketten (SSIS)-pakketten uitvoert in de runtime van SSIS-integratie. Het beschrijft de mogelijke oorzaken en acties om de fouten op te lossen.

## <a name="where-to-find-logs-for-troubleshooting"></a>Logboeken vinden voor het oplossen van problemen

Gebruik de Azure Data Factory-portal om de uitvoer van de uitvoeringsactiviteit van het SSIS-pakket te controleren. De uitvoer omvat het uitvoeringsresultaat, foutmeldingen en de bewerkings-ID. Zie De [pijplijn controleren](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)voor meer informatie.

Gebruik de SSIS-catalogus (SSISDB) om de detaillogboeken voor de uitvoering te controleren. Zie Lopende [pakketten en andere bewerkingen controleren](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)voor meer informatie.

## <a name="common-errors-causes-and-solutions"></a>Veelvoorkomende fouten, oorzaken en oplossingen

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Foutbericht: 'Verbindingstime-out verlopen' of 'De service heeft een fout opgelopen bij het verwerken van uw aanvraag.Error message: "Connection Timeout Expired" or "The service has gevonden an error processing your request. Probeer het opnieuw."

Hier volgen mogelijke oorzaken en aanbevolen acties:
* De gegevensbron of -bestemming is overbelast. Controleer de belasting op uw gegevensbron of bestemming en kijk of deze voldoende capaciteit heeft. Als u bijvoorbeeld Azure SQL Database hebt gebruikt, u overwegen op te schalen als de database waarschijnlijk een time-out krijgt.
* Het netwerk tussen de runtime van de SSIS-integratie en de gegevensbron of -bestemming is instabiel, vooral wanneer de verbinding tussen regio's of tussen on-premises en Azure is. Pas het patroon voor opnieuw proberen in het SSIS-pakket toe door de volgende stappen te volgen:
  * Zorg ervoor dat uw SSIS-pakketten kunnen worden uitgevoerd op falen zonder bijwerkingen (bijvoorbeeld gegevensverlies of duplicatie van gegevens).
  * **Interval van** **SSIS-pakketuitvoeren** opnieuw proberen configureren op het tabblad **Algemeen.** ![Eigenschappen instellen op het tabblad Algemeen **Execute SSIS Package**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Voor een bron- of doelcomponent van ADO.NET en OLE DB stelt u **ConnectRetryCount** en **ConnectRetryInterval** in Connection Manager in in het SSIS-pakket of SSIS-activiteit.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Foutmelding: "ADO NET Source heeft de verbinding niet verworven '...'" met "Een netwerkgerelateerde of instantiespecifieke fout is opgetreden bij het tot stand brengen van een verbinding met SQL Server. De server is niet gevonden of niet toegankelijk geweest."

Dit probleem betekent meestal dat de gegevensbron of -bestemming niet toegankelijk is vanuit de SSIS-integratieruntime. De redenen kunnen variëren. Voer de volgende acties uit:
* Zorg ervoor dat u de gegevensbron of bestemmingsnaam/IP correct doorgeeft.
* Zorg ervoor dat de firewall goed is ingesteld.
* Zorg ervoor dat uw virtuele netwerk goed is geconfigureerd als uw gegevensbron of -bestemming on-premises is:
  * U controleren of het probleem afkomstig is van de configuratie van virtuele netwerken door een Azure VM in hetzelfde virtuele netwerk in te richten. Controleer vervolgens of de gegevensbron of -bestemming toegankelijk is via de Azure VM.
  * U vindt meer informatie over het gebruik van een virtueel netwerk met een runtime voor SSIS-integratie in [Runtime voor Azure-SSIS-integratie in een virtueel netwerk.](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Foutmelding: "ADO NET Source heeft de verbinding niet verworven '...'" met 'Kan geen beheerde verbindingsmanager maken'.

De mogelijke oorzaak is dat de ADO.NET provider die in het pakket wordt gebruikt, niet is geïnstalleerd in de runtime van de SSIS-integratie. U de provider installeren met behulp van een aangepaste installatie. Meer informatie over aangepaste installatie vindt u in [De installatie aanpassen voor de runtime azure-SSIS-integratie.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Foutmelding: "De verbinding '...' is niet gevonden"

Een bekend probleem in oudere versies van SQL Server Management Studio (SSMS) kan deze fout veroorzaken. Als het pakket een aangepast onderdeel bevat (bijvoorbeeld SSIS Azure Feature Pack of partneronderdelen) dat niet is geïnstalleerd op de computer waarop SSMS wordt gebruikt om de implementatie uit te voeren, wordt het onderdeel door SSMS verwijderd met de fout als gevolg. Upgrade [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) naar de nieuwste versie waarin het probleem is opgelost.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Foutbericht:"SSIS Executor exit code: -1073741819."

* Mogelijke oorzaak en aanbevolen actie:
  * Deze fout kan te wijten zijn aan de beperking voor Excel-bron en bestemming wanneer meerdere Excel-bronnen of -bestemmingen parallel in meerdere thread's worden uitgevoerd. U deze beperking oplossen door uw Excel-componenten te wijzigen om achter elkaar uit te voeren, of ze in verschillende pakketten te scheiden en te activeren via 'Pakkettaak uitvoeren' met de eigenschap ExecuteOutOfProcess als True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Foutmelding: "Er is niet genoeg ruimte op de schijf"

Deze fout betekent dat de lokale schijf is opgebruikt in het runtime-knooppunt voor SSIS-integratie. Controleer of uw pakket of aangepaste installatie veel schijfruimte verbruikt:
* Als de schijf wordt verbruikt door uw pakket, zal het worden vrijgemaakt nadat het pakket uitvoering is voltooid.
* Als de schijf wordt verbruikt door uw aangepaste installatie, moet u de runtime van de SSIS-integratie stoppen, uw script wijzigen en de runtime van de integratie opnieuw starten. De hele Azure blob-container die u hebt opgegeven voor de aangepaste installatie, wordt gekopieerd naar het runtime-knooppunt voor SSIS-integratie, dus controleer of er onnodige inhoud onder die container zit.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Foutbericht: "Kan de bron niet ophalen van het stramien. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Beschrijving:Laadbestand "***" is mislukt."

* Mogelijke oorzaak en aanbevolen actie:
  * Als de SSIS-activiteit het pakket uitvoert vanuit het bestandssysteem (pakketbestand of projectbestand), treedt deze fout op als het project-, pakket- of configuratiebestand niet toegankelijk is met de pakkettoegangsreferenties die u hebt opgegeven in de SSIS-activiteit
    * Als u Azure-bestand gebruikt:
      * Het bestandspad moet \\ \\ \<beginnen\>met\\\<de naam van het opslagaccount .file.core.windows.net bestandssharepad\>
      * Het domein moet 'Azure' zijn
      * De gebruikersnaam \<moet de naam van het opslagaccount zijn\>
      * Het wachtwoord \<moet opslagtoegangssleutel zijn\>
    * Als u on-premises bestanden gebruikt, controleert u of VNet, pakkettoegangsreferenties en machtigingen correct zijn geconfigureerd, zodat de runtime van uw Azure-SSIS-integratie toegang heeft tot uw on-premises bestandsshare

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Foutbericht: "De bestandsnaam '...' opgegeven in de verbinding niet geldig was"

* Mogelijke oorzaak en aanbevolen actie:
  * Er is een ongeldige bestandsnaam opgegeven
  * Zorg ervoor dat u FQDN (volledig gekwalificeerde domeinnaam) gebruikt in plaats van korte tijd in uw verbindingsbeheer

### <a name="error-message-cannot-open-file-"></a>Foutbericht: "Kan bestand niet openen '...'"

Deze fout treedt op wanneer pakketuitvoering geen bestand kan vinden in de lokale schijf in de runtime van de SSIS-integratie. Voer de volgende acties uit:
* Gebruik het absolute pad niet in het pakket dat wordt uitgevoerd in de runtime van de SSIS-integratie. De huidige uitvoeringswerkmap (.) of de tijdelijke map (%TEMP%) gebruiken in plaats daarvan.
* Als u bepaalde bestanden op runtime-knooppunten voor SSIS-integratie moet blijven gebruiken, bereidt u de bestanden voor zoals beschreven in [De installatie Aanpassen](how-to-configure-azure-ssis-ir-custom-setup.md). Alle bestanden in de werkmap worden opgeschoond nadat de uitvoering is voltooid.
* Gebruik Azure Files in plaats van het bestand op te slaan in het runtime-knooppunt voor SSIS-integratie. Zie [Azure-bestandsshares gebruiken](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)voor meer informatie .

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Foutbericht: "De database 'SSISDB' heeft zijn groottequotum bereikt"

Een mogelijke oorzaak is dat de SSISDB-database die is gemaakt in de Azure SQL-database of een beheerd exemplaar tijdens het maken van een SSIS-integratieruntime, zijn quotum heeft bereikt. Voer de volgende acties uit:
* Overweeg de DTU van uw database te vergroten. U kunt meer informatie vinden in [SQL Database-resourcelimieten voor een Azure SQL-databaseserver](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Controleer of uw pakket een groot aantal logboeken kan genereren. Als dat het geval is, kunt u een elastische taak configureren om deze logboeken op te schonen. Meer informatie kunt u vinden in [SSISDB-logboeken opschonen met taken voor Azure Elastic Database](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Foutmelding: "De aanvraaglimiet voor de database is ... en is bereikt."

Als veel pakketten parallel worden uitgevoerd in de runtime van De SSIS-integratie, kan deze fout optreden omdat SSISDB de aanvraaglimiet heeft bereikt. Overweeg om de DTC van SSISDB te verhogen om dit probleem op te lossen. U kunt meer informatie vinden in [SQL Database-resourcelimieten voor een Azure SQL-databaseserver](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Foutbericht: "SSIS-bewerking is mislukt met een onverwachte bedrijfsstatus: ..."

De fout wordt meestal veroorzaakt door een tijdelijk probleem, dus probeer de uitvoering van het pakket opnieuw uit te voeren. Pas het patroon voor opnieuw proberen in het SSIS-pakket toe door de volgende stappen te volgen:

* Zorg ervoor dat uw SSIS-pakketten kunnen worden uitgevoerd op falen zonder bijwerkingen (bijvoorbeeld gegevensverlies of duplicatie van gegevens).
* **Interval van** **SSIS-pakketuitvoeren** opnieuw proberen configureren op het tabblad **Algemeen.** ![Eigenschappen instellen op het tabblad Algemeen **Execute SSIS Package**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Voor een bron- of doelcomponent van ADO.NET en OLE DB stelt u **ConnectRetryCount** en **ConnectRetryInterval** in Connection Manager in in het SSIS-pakket of SSIS-activiteit.

### <a name="error-message-there-is-no-active-worker"></a>Foutbericht: 'Er is geen actieve werknemer'.

Deze fout betekent meestal dat de runtime van de SSIS-integratie een ongezonde status heeft. Controleer de Azure-portal op de status en gedetailleerde fouten. Zie [runtime azure-SSIS-integratie voor](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)meer informatie .

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Foutbericht: "Uw inburgeringsruntime kan niet worden geüpgraded en werkt uiteindelijk niet meer, omdat we geen toegang hebben tot de Azure Blob-container die u hebt opgegeven voor aangepaste installatie."

Deze fout treedt op wanneer de runtime van de SSIS-integratie geen toegang heeft tot de opslag die is geconfigureerd voor aangepaste installatie. Controleer of de door u opgegeven uri (Shared Access Signature Signature) geldig is en niet is verlopen.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Foutbericht: "Microsoft OLE DB Provider for Analysis Services. "Hresult: 0x80004005 Beschrijving:' COM-fout: COM-fout: mscorlib; Uitzondering is gegooid door het doel van een aanroep"

Een mogelijke oorzaak is dat de gebruikersnaam of het wachtwoord met Azure Multi-Factor Authentication ingeschakeld is voor Azure Analysis Services-verificatie. Deze verificatie wordt niet ondersteund in de runtime van de SSIS-integratie. Probeer een serviceprincipal te gebruiken voor Azure Analysis Services-verificatie:

1. Bereid een serviceprincipal voor zoals beschreven in [Automatisering met serviceprincipals.](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
2. Configureer in Verbindingsbeheer **Een specifieke gebruikersnaam en wachtwoord:** stel **AppID** in als gebruikersnaam en **clientSecret** als wachtwoord.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Foutbericht: "ADONET Source heeft de verbinding {GUID} niet kunnen verkrijgen met het volgende foutbericht: Aanmelden is mislukt voor gebruiker 'NT AUTHORITY\ANONYMOUS LOGON'" bij het gebruik van een beheerde identiteit

Zorg ervoor dat u de verificatiemethode van Connection Manager niet configureert als **Active Directory Password Authentication** wanneer de parameter *ConnectUsingManagedIdentity* **true**is. U het in plaats daarvan configureren als **SQL-verificatie,** die wordt genegeerd als *ConnectUsingManagedIdentity* is ingesteld.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Foutbericht: "0xC020801F at ..., OData Source [...]: Kan geen beheerde verbinding verkrijgen van de run-time connection manager"

Een mogelijke oorzaak is dat de TLS (Transport Layer Security) niet is ingeschakeld in de runtime van SSIS-integratie die vereist is voor uw OData-bron. U tls inschakelen in ssis-integratieruntime met de installatie Aanpassen. Meer details zijn te vinden op [Kan Project Online Odata van SSIS niet verbinden](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) en de installatie aanpassen voor de [runtime azure-SSIS-integratie.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Foutbericht: "Staging taak aanvragen met operatie guid ... mislukt sinds fout: Kan geen faseringsbewerking verzenden met een foutbericht: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Kan de gegevensproxy niet laden."

Zorg ervoor dat de runtime van uw Azure-SSIS-integratie is geconfigureerd met runtime voor zelfgehoste integratie. Meer details zijn te vinden op [Configure Self-Hosted IR als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Foutbericht: "Status van taak met fasering: mislukt. Faseringstaakfout: ErrorCode: 2010, ErrorMessage: The Self-hosted Integration Runtime ... is offline"

Zorg ervoor dat de runtime van uw self-Hosted-integratie is geïnstalleerd en gestart. Meer details zijn te vinden bij Create and configure a [self-hosted integration runtime](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Foutbericht: "Staging task error: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "Error: The requested OLE DB provider ... is niet geregistreerd. Als het 64-bits stuurprogramma niet is geïnstalleerd, voert u het pakket uit in de 32-bits modus..."

Zorg ervoor dat de bijbehorende provider die wordt gebruikt door uw OLE DB-connectors in uw pakket correct is geïnstalleerd op self-hosted integratieruntimemachine. Meer details zijn te vinden op [Configure Self-Hosted IR als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Foutbericht: "Staging task error: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "Error: System.IO.FileLoadException: Could not load file or assembly 'Microsoft.WindowsAzure.Storage, Version=..., Culture=neutral, PublicKeyToken=31bf3856ad364e35' of een van de afhankelijkheden. De manifeste definitie van de gevestigde assemblage komt niet overeen met de assemblagereferentie.". ..."

Een mogelijke oorzaak is dat de runtime voor zelfgehoste integratie niet correct is geïnstalleerd of geüpgraded. Stel voor om de nieuwste self-hosted integratieruntime te downloaden en opnieuw te installeren. Meer details zijn te vinden bij Create and configure a [self-hosted integration runtime](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Foutbericht: "Een verbinding is vereist bij het aanvragen van metagegevens. Als u offline werkt, schakelt u Work Offline uit in het SSIS-menu om de verbinding in te schakelen"

* Mogelijke oorzaak en aanbevolen actie:
  * Als er ook een waarschuwingsbericht "De component ondersteunt geen verbindingbeheer met ConnectByProxy-waardeinstelling true" in het uitvoeringslogboek, betekent dit dat een verbindingsbeheerwordt gebruikt op een component die 'ConnectByProxy' nog niet heeft ondersteund. De ondersteunde componenten zijn te vinden bij [Configure Self-Hosted IR als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Het uitvoeringslogboek is te vinden in [het SSMS-rapport](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) of in de logboekmap die u hebt opgegeven in de uitvoeringsactiviteit van het SSIS-pakket.
  * vNet kan ook worden gebruikt om on-premises gegevens te openen als alternatief. Meer details zijn te vinden op [Join an Azure-SSIS integration runtime to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Foutbericht: "Status van taak met fasering: mislukt. Foutopgave: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "SSIS Executor exit code: -1.\n", "LogLocation": "... \\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue": { "integrationRuntimeQueue": ... }}"

Zorg ervoor dat Visual C++ runtime is geïnstalleerd op self-hosted integratie runtime machine. Meer details zijn te vinden op [Configure Self-Hosted IR als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Meerdere pakketuitvoeringen worden onverwacht geactiveerd

* Mogelijke oorzaak en aanbevolen actie:
  * AdF-opgeslagen procedureactiviteit of opzoekactiviteit worden gebruikt om de uitvoering van SSIS-pakketten te activeren. De t-sql-opdracht kan het tijdelijke probleem raken en de rerun activeren die meerdere pakketuitvoeringen zou veroorzaken.
  * Gebruik in plaats daarvan UitvoerSSISPackage-activiteit die ervoor zorgt dat pakketuitvoering niet opnieuw wordt uitgevoerd, tenzij de gebruiker opnieuw het aantal activiteiten instelt. Details zijn te vinden op[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Verfijn de opdracht t-sql om opnieuw te kunnen worden uitgevoerd door te controleren of een uitvoering al is geactiveerd

### <a name="package-execution-takes-too-long"></a>Pakketuitvoering duurt te lang

Hier volgen mogelijke oorzaken en aanbevolen acties:

* Er zijn te veel pakketuitvoeringen gepland op de runtime van de SSIS-integratie. Al deze executies zullen in de rij staan voor hun beurt.
  * Bepaal het maximum met deze formule:

    Maximale aantal parallelle uitvoeringen per IR = aantal knooppunten * Maximale parallelle uitvoering per knooppunt
  * Zie Runtime van [Azure-SSIS-integratie](create-azure-ssis-integration-runtime.md)maken in Azure Data Factory voor meer informatie over het instellen van het aantal knooppunten en de maximale parallelle uitvoering per knooppunt.
* De runtime van de SSIS-integratie wordt gestopt of heeft een ongezonde status. Zie runtime van [Azure-SSIS-integratie](monitor-integration-runtime.md#azure-ssis-integration-runtime)voor meer informatie over het controleren van de runtimestatus en fouten van de SSIS-integratie.

We raden u ook aan een time-out in te](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)stellen op het tabblad **Algemeen:** ![Eigenschappen instellen op het tabblad Algemeen .

### <a name="poor-performance-in-package-execution"></a>Slechte prestaties bij pakketuitvoering

Voer de volgende acties uit:

* Zorg ervoor dat de runtime van de SSIS-integratie zich in dezelfde regio bevindt als de gegevensbron en -bestemming.

* Stel het logboekniveau van pakketuitvoering in op **Prestaties** om duurgegevens te verzamelen voor elk onderdeel in de uitvoering. Zie [Logboekregistratie integratieservices (SSIS).](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Controleer de prestaties van ir-clients in de Azure-portal:
  * Zie runtime [azure-SSIS-integratie](monitor-integration-runtime.md#azure-ssis-integration-runtime)voor informatie over het controleren van de runtime van de SSIS-integratie.
  * U cpu/geheugengeschiedenis vinden voor de runtime van De SSIS-integratie door de statistieken van de gegevensfabriek in de Azure-portal te bekijken.
    ![Statistieken van de runtime van De Integratie van SSIS controleren](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
