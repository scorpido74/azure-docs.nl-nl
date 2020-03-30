---
title: Een zelf-hostende Integration Runtime maken
description: Meer informatie over het maken van een self-hosted integration runtime in Azure Data Factory, waarmee datafabrieken toegang krijgen tot gegevensarchieven in een privénetwerk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/10/2020
ms.openlocfilehash: 6302a7d6ffe7218d339121ec98a624f8e98356f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065599"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime maken en configureren

De integratieruntime (IR) is de rekeninfrastructuur die Azure Data Factory gebruikt om gegevensintegratiemogelijkheden te bieden voor verschillende netwerkomgevingen. Zie Overzicht van [de runtime van integratie](concepts-integration-runtime.md)voor meer informatie over IR.

Een zelfgehoste ingebruikname kan kopieeractiviteiten uitvoeren tussen een cloudgegevensarchief en een gegevensarchief in een privénetwerk. Het kan ook transformatieactiviteiten verzenden tegen rekenbronnen in een on-premises netwerk of een virtueel Azure-netwerk. De installatie van een self-hosted integratie runtime vereist een on-premises machine of een virtuele machine binnen een prive-netwerk.  

In dit artikel wordt beschreven hoe u een zelf gehoste IR maken en configureren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Een zelf gehoste runtime voor integratie instellen

Als u een zelf gehoste nawerkingsruntime voor zelfgehoste integratie wilt maken en instellen, gebruikt u de volgende procedures.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Een zelf gehoste IR maken via Azure PowerShell

1. U Azure PowerShell voor deze taak gebruiken. Hier volgt een voorbeeld:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Download](https://www.microsoft.com/download/details.aspx?id=39717) en installeer de zelf gehoste ingebruikloop van integratie op een lokale machine.

3. Haal de verificatiesleutel op en registreer de zelf gehoste runtime voor integratie met de sleutel. Hier is een PowerShell voorbeeld:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Een zelf gehoste IR maken via de gebruikersinterface van Azure Data Factory

Gebruik de volgende stappen om een zelf gehoste IR te maken met azure data factory-gebruikersinterface.

1. Selecteer op de pagina **Laten beginnen** van de gebruikersinterface van Azure Data Factory het tabblad **Auteur** in het meest linkse deelvenster.

   ![De knop Auteur van startpagina](media/doc-common-process/get-started-page-author-button.png)

1. Selecteer **Verbindingen** onder aan het linkerdeelvenster en selecteer **De werktijd Integratie** in het venster **Verbindingen.** Selecteer **+Nieuw**.

   ![Een Integration Runtime maken](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Selecteer in het **venster Runtime-instelling voor integratie** de optie **Gegevensverplaatsing uitvoeren en activiteiten verzenden naar externe gegevensberekening**en selecteer **Doorgaan**.

1. Voer een naam in voor uw IR en selecteer **Maken**.

1. Selecteer de koppeling onder **optie 1** om de expresinstelling op uw computer te openen. Of volg de stappen onder **optie 2** om handmatig in te stellen. De volgende instructies zijn gebaseerd op handmatige installatie:

   ![Installatie van integratieruntime](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Kopieer en plak de verificatiesleutel. Selecteer **De runtime voor integratie downloaden en installeren**.

    1. Download de zelf-hostende Integration Runtime op een lokale Windows-computer. Voer het installatieprogramma uit.

    1. Plak op de pagina **Runtime (Register Integration Runtime)** de sleutel die u eerder hebt opgeslagen en selecteer **Registreren**.
    
       ![De Integration Runtime registreren](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Selecteer Op de pagina **Nieuwe integratieruntime (Self-hosted) knooppunt** de optie **Voltooien**.

1. Nadat de runtime voor zelfgehoste integratie is geregistreerd, ziet u het volgende venster:

    ![Succesvolle registratie](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Een zelf gehoste IR instellen op een Azure VM via een Azure Resource Manager-sjabloon

U zelf gehoste IR-installatie op een virtuele Azure-machine automatiseren met behulp van de [IR-sjabloon Voor zelfhosten maken.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime) De sjabloon biedt een eenvoudige manier om een volledig functionele zelfgehoste IR in een Virtueel Azure-netwerk te hebben. De IR heeft functies met hoge beschikbaarheid en schaalbaarheid, zolang u het aantal knooppunten instelt op 2 of hoger.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Een bestaande zelfgehoste IR instellen via lokale PowerShell

U een opdrachtregel gebruiken om een bestaande zelf gehoste IR in te stellen of te beheren. Dit gebruik kan vooral helpen bij het automatiseren van de installatie en registratie van zelf gehoste IR-knooppunten.

Dmgcmd.exe is opgenomen in de zelfgehoste installateur. Het bevindt zich meestal in de map C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ Deze toepassing ondersteunt verschillende parameters en kan worden aangeroepen via een opdrachtregel met batchscripts voor automatisering.

Gebruik de toepassing als volgt:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Hier vindt u meer informatie over de parameters en eigenschappen van de toepassing: 

| Eigenschap                                                    | Beschrijving                                                  | Vereist |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode** "`<AuthenticationKey>`"                     | Registreer een self-hosted integration runtime-knooppunt met de opgegeven verificatiesleutel. | Nee       |
| **RegisterNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Registreer een self-hosted integration runtime-knooppunt met de opgegeven verificatiesleutel en knooppuntnaam. | Nee       |
| **RemoteAccess inschakelen** "`<port>`" ["`<thumbprint>`"]            | Externe toegang inschakelen op het huidige knooppunt om een cluster met hoge beschikbaarheid in te stellen. Of schakel inloggegevens rechtstreeks in tegen de zelf gehoste IR zonder azure datafactory te doorlopen. Dat laatste doe je door de **New-AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet te gebruiken vanaf een externe machine in hetzelfde netwerk. | Nee       |
| **RemoteAccessInContainer inschakelen** "`<port>`" ["`<thumbprint>`"] | Externe toegang tot het huidige knooppunt inschakelen wanneer het knooppunt in een container wordt uitgevoerd. | Nee       |
| **RemoteAccess uitschakelen**                                         | Externe toegang tot het huidige knooppunt uitschakelen. Toegang op afstand is nodig voor multinode setup. De **New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet werkt nog steeds, zelfs wanneer externe toegang is uitgeschakeld. Dit gedrag is waar zolang de cmdlet wordt uitgevoerd op dezelfde machine als het zelf gehoste IR-knooppunt. | Nee       |
| **Sleutel** "`<AuthenticationKey>`"                                 | Overschrijven of bijwerken van de vorige verificatiesleutel. Wees voorzichtig met deze actie. Uw vorige zelfgehoste IR-knooppunt kan offline gaan als de sleutel een nieuwe ingebouwde retime voor integratie is. | Nee       |
| **Back-upbestand genereren** "`<filePath>`" "`<password>`"            | Een back-upbestand voor het huidige knooppunt genereren. Het back-upbestand bevat de gegevensbanksleutel en gegevensarchiefreferenties. | Nee       |
| **Back-upbestand importeren** "`<filePath>`" "`<password>`"              | Herstel het knooppunt uit een back-upbestand.                          | Nee       |
| **Opnieuw starten**                                                     | Start de self-hosted integration runtime hostservice opnieuw.   | Nee       |
| **Begin**                                                       | Start de self-hosted integration runtime host service.     | Nee       |
| **Stoppen**                                                        | Stop de self-hosted integratie runtime host service.        | Nee       |
| **StartUpgradeService**                                         | Start de self-hosted integration runtime upgrade service.       | Nee       |
| **StopUpgradeService**                                          | Stop de self-hosted integratie runtime upgrade service.        | Nee       |
| **TurnonAutoUpdate**                                            | Schakel de zelf gehoste runtime-update voor integratie in.        | Nee       |
| **UitschakelenAutoUpdate**                                           | Schakel de zelf gehoste runtime-update voor integratie uit.       | Nee       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | Stel DIAHostService in om als een nieuw account te worden uitgevoerd. Gebruik het lege wachtwoord "" voor systeemaccounts en virtuele accounts. | Nee       |


## <a name="command-flow-and-data-flow"></a>Opdrachtstroom en gegevensstroom

Wanneer u gegevens verplaatst tussen on-premises en de cloud, gebruikt de activiteit een zelf gehoste runtime voor integratie om de gegevens over te dragen tussen een on-premises gegevensbron en de cloud.

Hier volgt een overzicht op hoog niveau van de gegevensstroomstappen voor het kopiëren met een zelfgehoste IR:

![Het overzicht op hoog niveau van de gegevensstroom](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Een gegevensontwikkelaar maakt een self-hosted integration runtime binnen een Azure-gegevensfabriek met behulp van een PowerShell-cmdlet. Momenteel ondersteunt de Azure-portal deze functie niet.
1. De gegevensontwikkelaar maakt een gekoppelde service voor een on-premises gegevensarchief. De ontwikkelaar doet dit door de zelf gehoste runtime-instantie voor integratie op te geven die de service moet gebruiken om verbinding te maken met gegevensarchieven.
1. Het self-hosted integration runtime-knooppunt versleutelt de referenties met behulp van De Windows Data Protection Application Programming Interface (DPAPI) en slaat de referenties lokaal op. Als meerdere knooppunten zijn ingesteld voor hoge beschikbaarheid, worden de referenties verder gesynchroniseerd over andere knooppunten. Elk knooppunt versleutelt de referenties met behulp van DPAPI en slaat ze lokaal op. Referentiesynchronisatie is transparant voor de gegevensontwikkelaar en wordt afgehandeld door de zelf gehoste IR.
1. Azure Data Factory communiceert met de zelf gehoste runtime voor integratie om taken te plannen en te beheren. Communicatie gebeurt via een controlekanaal dat gebruik maakt van een gedeelde [Azure Service Bus Relay-verbinding.](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) Wanneer een activiteitstaak moet worden uitgevoerd, klanget Data Factory de aanvraag samen met eventuele referentiegegevens. Dit gebeurt in het geval referenties nog niet zijn opgeslagen op de zelfgehoste runtime voor integratie. De zelfgehoste runtime voor integratie begint de taak nadat de wachtrij is gepeild.
1. De zelfgehoste runtime voor integratie kopieert gegevens tussen een on-premises winkel en cloudopslag. De richting van de kopie is afhankelijk van de manier waarop de kopieeractiviteit is geconfigureerd in de gegevenspijplijn. Voor deze stap communiceert de self-hosted integration runtime rechtstreeks met cloudgebaseerde opslagservices zoals Azure Blob-opslag via een beveiligd HTTPS-kanaal.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Overwegingen voor het gebruik van een zelf gehoste IR

- U één zelfgehoste implementatieruntime gebruiken voor meerdere on-premises gegevensbronnen. U het ook delen met een andere gegevensfabriek binnen dezelfde Azure Active Directory -tenant (Azure AD). Zie [Een zelf gehoste runtime voor integratie delen](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)voor meer informatie .
- U slechts één exemplaar van een zelf gehoste inlooptijd voor integratie op één machine installeren. Als u twee gegevensfabrieken hebt die toegang moeten krijgen tot on-premises gegevensbronnen, gebruikt u de [zelfgehoste functie voor het delen van IR](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) om de zelfgehoste IR te delen of installeert u de zelf gehoste IR op twee on-premises computers, één voor elke gegevensfabriek.  
- De self-hosted integratie runtime hoeft niet op dezelfde machine als de gegevensbron te staan. Echter, met de self-hosted integratie runtime dicht bij de gegevensbron vermindert de tijd voor de self-hosted integratie runtime om verbinding te maken met de gegevensbron. We raden u aan de zelf gehoste runtime voor integratie te installeren op een machine die verschilt van de machine die de on-premises gegevensbron host. Wanneer de runtime en gegevensbron voor zelfgehoste integratie op verschillende machines staan, concurreert de runtime voor zelfgehoste integratie niet met de gegevensbron voor resources.
- U meerdere zelfgehoste implementatietijden voor integratie hebben op verschillende machines die verbinding maken met dezelfde on-premises gegevensbron. Als u bijvoorbeeld twee zelfgehoste gebruikstijden voor integratie hebt die twee gegevensfabrieken bedienen, kan dezelfde on-premises gegevensbron worden geregistreerd bij beide gegevensfabrieken.
- Als u al een gateway op uw computer hebt geïnstalleerd om een Power BI-scenario te serveren, installeert u een afzonderlijke zelfgehoste instroomruntime voor Gegevensfabriek op een andere machine.
- Gebruik een self-hosted integration runtime ter ondersteuning van gegevensintegratie binnen een Virtueel Azure-netwerk.
- Behandel uw gegevensbron als een on-premises gegevensbron die zich achter een firewall bevindt, zelfs wanneer u Azure ExpressRoute gebruikt. Gebruik de self-hosted integration runtime om de service te verbinden met de gegevensbron.
- Gebruik de runtime voor zelfgehoste integratie, zelfs als het gegevensarchief zich in de cloud bevindt op een Azure Infrastructure as a Service (IaaS) virtuele machine.
- Taken kunnen mislukken in een zelf gehoste nawerking van de integratie die u hebt geïnstalleerd op een Windows-server waarvoor FIPS-compatibele versleuteling is ingeschakeld. Als u dit probleem wilt oplossen, schakelt u FIPS-compatibele versleuteling op de server uit. Als u FIPS-compatibele versleuteling wilt uitschakelen, wijzigt u de waarde van de `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`volgende registersubsleutel van 1 (ingeschakeld) naar 0 (uitgeschakeld): .

## <a name="prerequisites"></a>Vereisten

- De ondersteunde versies van Windows zijn:
  + Windows 7 servicepack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   De installatie van de zelfgehoste runtime voor integratie op een domeincontroller wordt niet ondersteund.
- .NET Framework 4.6.1 of hoger is vereist. Als u de zelf gehoste runtime voor integratie installeert op een Windows 7-machine, installeert u .NET Framework 4.6.1 of hoger. Zie [.NET Framework System Requirements](/dotnet/framework/get-started/system-requirements) for details.
- De aanbevolen minimale configuratie voor de zelf gehoste runtime-machine voor integratie is een 2-GHz-processor met 4 cores, 8 GB RAM en 80 GB beschikbare schijfruimte.
- Als de hostmachine overwintert, reageert de runtime voor zelfgehoste integratie niet op gegevensverzoeken. Configureer een geschikt energiebeheerschema op de computer voordat u de zelf gehoste runtime voor integratie installeert. Als de machine is geconfigureerd om te overwinteren, vraagt het zelf gehoste installatieprogramma voor runtime voor integratie om een bericht.
- U moet een beheerder op de machine zijn om de zelf gehoste runtime voor integratie te installeren en te configureren.
- Copy-activity runs gebeuren met een specifieke frequentie. Processor en RAM-gebruik op de machine volgt hetzelfde patroon met piek- en idletijden. Het gebruik van resources is ook sterk afhankelijk van de hoeveelheid gegevens die wordt verplaatst. Wanneer er meerdere kopieertaken worden uitgevoerd, ziet u het gebruik van resources tijdens piekuren stijgen.
- Taken kunnen mislukken tijdens het extraheren van gegevens in indelingen van parket, ORC of Avro. Zie [Parketindeling in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)voor meer informatie over parket. Het maken van bestanden wordt uitgevoerd op de zelfgehoste integratiemachine. Om te werken zoals verwacht, vereist het maken van bestanden de volgende vereisten:
    - [Visual C++ 2010 Doorroodbaar](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Pakket (x64)
    - Java Runtime (JRE) versie 8 van een JRE provider zoals [Adopt OpenJDK](https://adoptopenjdk.net/). Zorg ervoor `JAVA_HOME` dat de omgevingsvariabele is ingesteld.

## <a name="installation-best-practices"></a>Best practices voor installatie

U de runtime voor zelfgehoste integratie installeren door een managed identity-installatiepakket te downloaden vanuit [het Microsoft Downloadcenter.](https://www.microsoft.com/download/details.aspx?id=39717) Zie het artikel [Gegevens verplaatsen tussen on-premises en cloud](tutorial-hybrid-copy-powershell.md) voor stapsgewijze instructies.

- Configureer een energiebeheerschema op de hostmachine voor de zelf gehoste runtime voor integratie, zodat de machine niet overwintert. Als de hostmachine overwintert, wordt de runtime voor zelfgehoste integratie offline.
- Maak regelmatig een back-up van de referenties die zijn gekoppeld aan de zelfgehoste runtime voor integratie.
- Als u zelf gehoste IR-installatiebewerkingen wilt automatiseren, verwijzen wij u naar [Het instellen van een bestaande zelf gehoste IR via PowerShell.](#setting-up-a-self-hosted-integration-runtime)  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Een zelf gehoste IR installeren en registreren vanuit microsoft downloadcentrum

1. Ga naar de [downloadpagina voor runtime van Microsoft-integratie](https://www.microsoft.com/download/details.aspx?id=39717).
1. Selecteer **Downloaden,** selecteer de 64-bits versie en selecteer **Volgende**. De 32-bits versie wordt niet ondersteund.
1. Voer het bestand Beheerde identiteit rechtstreeks uit of sla het op uw harde schijf op en voer het uit.
1. Selecteer **in het** venster Welkom een taal en selecteer **Volgende**.
1. Accepteer de licentievoorwaarden voor Microsoft-software en selecteer **Volgende**.
1. Selecteer **map** om de zelf gehoste runtime voor integratie te installeren en selecteer **Volgende**.
1. **Selecteer**installeren op de pagina Klaar om **te installeren** .
1. Selecteer **Voltooien** om de installatie te voltooien.
1. Download de verificatiesleutel met PowerShell. Hier is een PowerShell-voorbeeld voor het ophalen van de verificatiesleutel:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. Voer in het venster **Runtime (Register Integration Runtime)** van Microsoft Integration Runtime Configuration Manager dat op uw machine wordt uitgevoerd, de volgende stappen uit:

    1. Plak de verificatiesleutel in het tekstgebied.

    1. Selecteer optioneel **Verificatiesleutel weergeven** om de sleuteltekst weer te geven.

    1. Selecteer **Registreren**.

## <a name="high-availability-and-scalability"></a>Hoge beschikbaarheid en schaalbaarheid

U een zelf gehoste implementatieruntijd koppelen aan meerdere on-premises machines of virtuele machines in Azure. Deze machines worden knooppunten genoemd. U maximaal vier knooppunten hebben die zijn gekoppeld aan een zelf gehoste runtime voor integratie. De voordelen van het hebben van meerdere knooppunten op on-premises machines die een gateway hebben geïnstalleerd voor een logische gateway zijn:

* Hogere beschikbaarheid van de self-hosted integratie runtime, zodat het niet langer het enige punt van falen in uw big data-oplossing of cloud data-integratie met Data Factory. Deze beschikbaarheid zorgt voor continuïteit wanneer u maximaal vier knooppunten gebruikt.
* Verbeterde prestaties en doorvoer tijdens gegevensverplaatsing tussen on-premises en clouddatastores. Meer informatie over [prestatievergelijkingen](copy-activity-performance.md).

U meerdere knooppunten koppelen door de zelf gehoste runtime-software voor integratie te installeren vanuit [Download Center.](https://www.microsoft.com/download/details.aspx?id=39717) Vervolgens, registreer het met behulp van een van de authenticatie sleutels die zijn verkregen uit de **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet, zoals beschreven in de [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> U hoeft geen nieuwe zelf gehoste runtime voor integratie te maken om elk knooppunt te koppelen. U de zelf gehoste runtime voor integratie op een andere machine installeren en deze registreren met dezelfde verificatiesleutel.

> [!NOTE]
> Voordat u een ander knooppunt toevoegt voor hoge beschikbaarheid en schaalbaarheid, moet u ervoor zorgen dat de optie **Externe toegang tot intranet** is ingeschakeld op het eerste knooppunt. Selecteer hiervoor **Microsoft Integration Runtime Configuration Manager** > **Settings** > **Remote access to intranet.**

### <a name="scale-considerations"></a>Schaaloverwegingen

#### <a name="scale-out"></a>Uitschalen

Wanneer het processorgebruik hoog is en het beschikbare geheugen laag is op de zelfgehoste IR, voegt u een nieuw knooppunt toe om de belasting over machines uit te schalen. Als activiteiten mislukken omdat ze een time-out hebben of het zelf gehoste IR-knooppunt offline is, helpt dit als u een knooppunt aan de gateway toevoegt.

#### <a name="scale-up"></a>Omhoog schalen

Wanneer de processor en het beschikbare RAM-geheugen niet goed worden gebruikt, maar de uitvoering van gelijktijdige taken de limieten van een knooppunt bereikt, schaal je op door het aantal gelijktijdige taken te verhogen dat een knooppunt kan uitvoeren. U ook opschalen wanneer activiteiten een time-out hebben omdat de zelfgehoste IR overbelast is. Zoals in de volgende afbeelding wordt weergegeven, u de maximale capaciteit voor een knooppunt verhogen:  

![Het aantal gelijktijdige taken verhogen dat op een knooppunt kan worden uitgevoerd](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>VEREISTEN voor TLS/SSL-certificaten

Hier volgen de vereisten voor het TLS/SSL-certificaat dat u gebruikt om de communicatie tussen runtime-knooppunten voor integratie te beveiligen:

- Het certificaat moet een openbaar vertrouwd X509 v3-certificaat zijn. We raden u aan certificaten te gebruiken die zijn uitgegeven door een openbare certificeringsinstantie voor partners (CA).
- Elk knooppunt voor runtime voor integratie moet dit certificaat vertrouwen.
- We raden SAN-certificaten (Subject Alternative Name) niet aan omdat alleen het laatste SAN-item wordt gebruikt. Alle andere SAN-items worden genegeerd. Als u bijvoorbeeld een SAN-certificaat hebt waarvan de SAN's **node1.domain.contoso.com** en **node2.domain.contoso.com,** u dit certificaat alleen gebruiken op een machine waarvan de volledig gekwalificeerde domeinnaam (FQDN) **node2.domain.contoso.com**is.
- Het certificaat kan elke sleutelgrootte gebruiken die wordt ondersteund door Windows Server 2012 R2 voor TLS/SSL-certificaten.
- Certificaten die CNG-sleutels gebruiken, worden niet ondersteund.  

> [!NOTE]
> Dit certificaat wordt gebruikt:
>
> - Poorten versleutelen op een zelf gehost IR-knooppunt.
> - Voor knooppuntcommunicatie voor statussynchronisatie, waaronder referentiessynchronisatie van gekoppelde services tussen knooppunten.
> - Wanneer een PowerShell-cmdlet wordt gebruikt voor instellingen voor gekoppelde servicereferenties vanuit een lokaal netwerk.
>
> We raden u aan dit certificaat te gebruiken als uw privénetwerkomgeving niet veilig is of als u de communicatie tussen knooppunten binnen uw privénetwerk wilt beveiligen.
>
> Gegevensverplaatsing tijdens het transport van een zelf gehoste IR naar andere gegevensopslag vindt altijd plaats binnen een versleuteld kanaal, ongeacht of dit certificaat is ingesteld.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Een runtime voor gedeelde zelfgehoste integratie maken in Azure Data Factory

U een bestaande self-hosted integration runtime-infrastructuur opnieuw gebruiken die u al in een gegevensfabriek hebt ingesteld. Met dit hergebruik u een gekoppelde self-hosted integration runtime maken in een andere gegevensfabriek door te verwijzen naar een bestaande gedeelde zelf gehoste IR.

Bekijk de volgende video van 12 minuten om een introductie en demonstratie van deze functie te bekijken:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologie

- **Gedeelde IR:** een originele zelf gehoste IR die draait op een fysieke infrastructuur.  
- **Gekoppelde IR:** een IR die verwijst naar een andere gedeelde IR. De gekoppelde IR is een logische IR en maakt gebruik van de infrastructuur van een andere gedeelde self-hosted IR.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Methoden om een zelfgehoste ingebruikloop voor integratie te delen

Zie Runtime voor [gedeelde zelfgehoste integratie](create-shared-self-hosted-integration-runtime-powershell.md) voor meer informatie om een zelfgehoste implementatieruntime te delen met meerdere gegevensfabrieken.

### <a name="monitoring"></a>Bewaking

#### <a name="shared-ir"></a>Gedeelde IR

![Selecties voor het vinden van een runtime voor gedeelde integratie](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Een runtime voor gedeelde integratie bewaken](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Gekoppelde IR

![Selecties om een runtime voor gekoppelde integratie te vinden](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Een runtime voor gekoppelde integratie bewaken](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Bekende beperkingen van zelfgehostir-delen

* De gegevensfabriek waarin een gekoppelde IR wordt gemaakt, moet een [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)hebben. Standaard hebben de gegevensfabrieken die zijn gemaakt in de Azure-portal of PowerShell-cmdlets een impliciet gemaakte beheerde identiteit. Maar wanneer een gegevensfabriek wordt gemaakt via een Azure Resource Manager-sjabloon of SDK, moet u de eigenschap **Identiteit** expliciet instellen. Met deze instelling zorgt u ervoor dat Resource Manager een gegevensfabriek maakt die een beheerde identiteit bevat.

* De Data Factory .NET SDK die deze functie ondersteunt, moet versie 1.1.0 of hoger zijn.

* Als u toestemming wilt verlenen, hebt u de rol Eigenaar of de rol overgenomen eigenaar nodig in de gegevensfabriek waar de gedeelde IR bestaat.

* De functie delen werkt alleen voor gegevensfabrieken binnen dezelfde Azure AD-tenant.

* Voor Azure [AD-gastgebruikers](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)werkt de zoekfunctionaliteit in de gebruikersinterface, die alle gegevensfabrieken met behulp van een zoekwoord weergeeft, [niet.](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits) Maar zolang de gastgebruiker de eigenaar van de gegevensfabriek is, u de IR delen zonder de zoekfunctionaliteit. Voer voor de beheerde identiteit van de gegevensfabriek die de IR moet delen, dat beheerde identiteit in het vak **Machtigingen toewijzen** in en selecteer **Toevoegen** in de gebruikersinterface van de gegevensfabriek.

  > [!NOTE]
  > Deze functie is alleen beschikbaar in Data Factory V2.

## <a name="notification-area-icons-and-notifications"></a>Pictogrammen en meldingen van het meldingsgebied

Als u de cursor over het pictogram of bericht in het meldingsgebied beweegt, ziet u details over de status van de zelf gehoste runtime voor integratie.

![Meldingen in het meldingsgebied](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Poorten en firewalls

Er zijn twee firewalls te overwegen:

- De *bedrijfsfirewall* die op de centrale router van de organisatie draait
- De *Windows-firewall* die is geconfigureerd als een daemon op de lokale machine waar de zelfgehoste runtime voor integratie is geïnstalleerd

![De firewalls](media/create-self-hosted-integration-runtime/firewall.png)

Op het niveau van de bedrijfsfirewall moet u de volgende domeinen en uitgaande poorten configureren:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

Op Windows-firewall- of machineniveau zijn deze uitgaande poorten normaal gesproken ingeschakeld. Als dit niet het zo is, u de domeinen en poorten configureren op een zelf gehoste runtime-machine voor integratie.

> [!NOTE]
> Op basis van uw bron en sinks moet u mogelijk extra domeinen en uitgaande poorten toestaan in uw bedrijfsfirewall of Windows-firewall.
>
> Voor sommige clouddatabases, zoals Azure SQL Database en Azure Data Lake, moet u mogelijk IP-adressen van zelf gehoste runtime-machines voor integratie toestaan op hun firewallconfiguratie.

### <a name="copy-data-from-a-source-to-a-sink"></a>Gegevens van een bron naar een gootsteen kopiëren

Zorg ervoor dat u firewallregels op de bedrijfsfirewall, de Windows-firewall van de zelf gehoste runtime-machine voor integratie en het gegevensarchief zelf correct inschakelt. Als u deze regels inschakelt, kan de zelf gehoste nalooptijd voor integratie met succes verbinding maken met zowel bron als gootsteen. Regels inschakelen voor elk gegevensarchief dat betrokken is bij de kopieerbewerking.

Als u bijvoorbeeld wilt kopiëren vanuit een on-premises gegevensarchief naar een SQL Database-sink of een Azure SQL Data Warehouse-sink, neemt u de volgende stappen:

1. Laat uitgaande TCP-communicatie op poort 1433 toe voor zowel de Windows-firewall als de bedrijfsfirewall.
1. Configureer de firewall-instellingen van de SQL-database om het IP-adres van de zelfgehoste runtime-machine voor integratie toe te voegen aan de lijst met toegestane IP-adressen.

> [!NOTE]
> Als uw firewall uitgaande poort 1433 niet toestaat, heeft de runtime voor zelfgehoste integratie geen directe toegang tot de SQL-database. In dit geval u een [gefaseerde kopie](copy-activity-performance.md) gebruiken naar SQL Database en SQL Data Warehouse. In dit scenario hebt u alleen HTTPS (poort 443) nodig voor de gegevensverplaatsing.

## <a name="proxy-server-considerations"></a>Overwegingen proxyserver

Als uw bedrijfsnetwerkomgeving een proxyserver gebruikt om toegang te krijgen tot internet, configureert u de runtime voor zelfgehoste integratie om de juiste proxy-instellingen te gebruiken. U de proxy instellen tijdens de eerste registratiefase.

![De proxy opgeven](media/create-self-hosted-integration-runtime/specify-proxy.png)

Wanneer geconfigureerd, gebruikt de self-hosted integratieruntime de proxyserver om verbinding te maken met de bron en bestemming van de cloudservice (die het HTTP- of HTTPS-protocol gebruiken). Daarom selecteert u **Koppeling wijzigen** tijdens de eerste installatie.

![De proxy instellen](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Er zijn drie configuratieopties:

- **Gebruik geen proxy:** de self-hosted integration runtime gebruikt niet expliciet een proxy om verbinding te maken met cloudservices.
- **Systeemproxy gebruiken:** de zelfgehoste nawerking van integratie maakt gebruik van de proxy-instelling die is geconfigureerd in diahost.exe.config en diawp.exe.config. Als deze bestanden geen proxyconfiguratie opgeven, maakt de zelf gehoste integratieruntime rechtstreeks verbinding met de cloudservice zonder een proxy te doorlopen.
- **Aangepaste proxy**gebruiken: Configureer de HTTP-proxy-instelling die u wilt gebruiken voor de runtime voor zelfgehoste integratie, in plaats van configuraties te gebruiken in diahost.exe.config en diawp.exe.config. **Adres-** en **poortwaarden** zijn vereist. **Waarden voor gebruikersnaam** en **wachtwoord** zijn optioneel, afhankelijk van de verificatie-instelling van uw proxy. Alle instellingen worden versleuteld met Windows DPAPI op de zelf gehoste runtime voor integratie en lokaal op de machine opgeslagen.

De integratieruntime-hostservice wordt automatisch opnieuw opgestart nadat u de bijgewerkte proxy-instellingen hebt opgeslagen.

Nadat u de zelf gehoste runtime voor integratie hebt geregistreerd, gebruikt u Microsoft Integration Runtime Configuration Manager als u proxy-instellingen wilt bekijken of bijwerken.

1. Open **Microsoft Integration Runtime Configuration Manager**.
1. Selecteer het tabblad **Instellingen**.
1. Selecteer **onder HTTP-proxy**de koppeling **Wijzigen** om het dialoogvenster **HTTP-proxy instellen** te openen.
1. Selecteer **Volgende**. U ziet dan een waarschuwing waarin om uw toestemming wordt gevraagd om de proxy-instelling op te slaan en de integratieruntime-hostservice opnieuw te starten.

U het hulpprogramma voor configuratiebeheer gebruiken om de HTTP-proxy weer te geven en bij te werken.

![De proxy weergeven en bijwerken](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Als u een proxyserver met NTLM-verificatie instelt, wordt de statusservice integratieruntime uitgevoerd onder het domeinaccount. Als u later het wachtwoord voor het domeinaccount wijzigt, moet u de configuratie-instellingen voor de service bijwerken en de service opnieuw starten. Vanwege deze vereiste raden we u aan de proxyserver te openen met behulp van een speciaal domeinaccount waarvoor u het wachtwoord niet vaak hoeft bij te werken.

### <a name="configure-proxy-server-settings"></a>Proxyserverinstellingen configureren

Als u de optie **Systeemproxy gebruiken** voor de HTTP-proxy selecteert, gebruikt de runtime voor zelfgehoste integratie de proxy-instellingen in diahost.exe.config en diawp.exe.config. Wanneer deze bestanden geen proxy opgeven, maakt de self-hosted integration runtime rechtstreeks verbinding met de cloudservice zonder een proxy te doorlopen. De volgende procedure bevat instructies voor het bijwerken van het bestand diahost.exe.config:

1. Maak in Verkenner een veilige kopie van C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config als back-up van het oorspronkelijke bestand.
1. Open Kladblok dat als beheerder wordt uitgevoerd.
1. Open in Kladblok het tekstbestand C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config.
1. Zoek de **standaardtag system.net** zoals weergegeven in de volgende code:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    U vervolgens proxyservergegevens toevoegen zoals in het volgende voorbeeld wordt weergegeven:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Met de proxytag kunnen extra `scriptLocation`eigenschappen de vereiste instellingen opgeven, zoals . Zie [ \<\> proxy-element (netwerkinstellingen)](https://msdn.microsoft.com/library/sa91de1e.aspx) voor syntaxis.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Sla het configuratiebestand op de oorspronkelijke locatie op. Start vervolgens de zelf gehoste runtime-hostservice voor integratie opnieuw, die de wijzigingen oppikt.

   Als u de service opnieuw wilt starten, gebruikt u de services applet van het Configuratiescherm. Of selecteer in Integration Runtime Configuration Manager de knop **Service stoppen** en selecteer **Service starten**.

   Als de service niet wordt gestart, hebt u waarschijnlijk een onjuiste XML-tagsyntaxis toegevoegd in het configuratiebestand van de toepassing dat u hebt bewerkt.

> [!IMPORTANT]
> Vergeet niet om zowel diahost.exe.config als diawp.exe.config bij te werken.

U moet er ook voor zorgen dat Microsoft Azure in de lijst met toegestane bedrijven staat. U de lijst met geldige Azure IP-adressen downloaden van [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=41653)

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Mogelijke symptomen voor problemen met betrekking tot de firewall en proxyserver

Als u foutberichten zoals de volgende ziet, is de waarschijnlijke reden een onjuiste configuratie van de firewall of proxyserver. Een dergelijke configuratie voorkomt dat de zelf gehoste runtime voor integratie verbinding maakt met Data Factory om zichzelf te verifiëren. Raadpleeg de vorige sectie om ervoor te zorgen dat uw firewall en proxyserver goed zijn geconfigureerd.

* Wanneer u de zelf gehoste runtime voor integratie probeert te registreren, ontvangt u het volgende foutbericht: "Kan dit integratieruntimeknooppunt niet registreren! Controleer of de verificatiesleutel geldig is en dat de hostservice voor integratieservice op deze machine wordt uitgevoerd."
* Wanneer u Integration Runtime Configuration Manager opent, ziet u de status **Verbroken** of **Verbinding maken.** Wanneer u Windows-gebeurtenislogboeken bekijkt, ziet u onder **Logboeken** > **microsoft-integratieruntime**van**Logboeken** > Microsoft-integratie:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Externe toegang vanaf een intranet inschakelen

Als u PowerShell gebruikt om referenties te versleutelen van een andere machine in een netwerk dan waar u de zelfgehoste runtime voor integratie hebt geïnstalleerd, u de optie **Rastoegang vanaf intranet** inschakelen. Als u PowerShell uitvoert om referenties te versleutelen op de machine waar u de zelfgehoste runtime voor integratie hebt geïnstalleerd, u Rastoegang niet inschakelen **vanaf intranet.**

Externe **toegang vanaf intranet** inschakelen voordat u een ander knooppunt toevoegt voor hoge beschikbaarheid en schaalbaarheid.  

Wanneer u de zelf gehoste runtime-installatieversie 3.3 of hoger voor zelfgehoste integratie uitvoert, schakelt het zelfgehoste installatieprogramma voor de uitvoering van de integratie **standaard Rastoegang van intranet** uit op de zelf gehoste runtime-machine voor integratie.

Wanneer u een firewall van een partner of anderen gebruikt, u poort 8060 of de door de gebruiker geconfigureerde poort handmatig openen. Als u een firewallprobleem hebt tijdens het instellen van de runtime voor zelfgehoste integratie, gebruikt u de volgende opdracht om de zelf gehoste runtime voor integratie te installeren zonder de firewall te configureren:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Als u ervoor kiest poort 8060 niet te openen op de zelf gehoste runtime-machine voor integratie, gebruikt u andere mechanismen dan de toepassing Referenties instellen om referenties voor gegevensopslag te configureren. U bijvoorbeeld de **cmdlet Nieuw-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Zelfstudie: On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-powershell.md)voor stapsgewijze instructies.
