---
title: Een zelf-hostende Integration runtime maken in Azure Data Factory | Microsoft Docs
description: Meer informatie over het maken van een zelf-hostende Integration runtime in Azure Data Factory, waarmee gegevens fabrieken toegang kunnen krijgen tot gegevens archieven in een particulier netwerk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 8ea6a365b0c7bc6c254c1313445bb54231e161ae
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285640"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration runtime maken en configureren
Integration runtime (IR) is de berekenings infrastructuur die Azure Data Factory gebruikt voor het bieden van mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen. Zie [Integration runtime Overview](concepts-integration-runtime.md)(Engelstalig) voor meer informatie over IR.

Een zelf-hostende Integration runtime kan Kopieer activiteiten uitvoeren tussen een gegevens archief in de Cloud en een gegevens archief in een privé netwerk, en kan transformatie activiteiten verzenden met reken bronnen in een on-premises netwerk of een virtueel Azure-netwerk. De installatie van een zelf-hostende Integration runtime heeft een on-premises computer of een virtuele machine (VM) in een particulier netwerk nodig.  

In dit document wordt beschreven hoe u een zelf-hostende IR kunt maken en configureren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Stappen op hoog niveau voor het installeren van een zelf-hostende IR
1. Een zelf-hostende Integration Runtime maken. U kunt de Azure Data Factory gebruikers interface voor deze taak gebruiken. Hier volgt een Power shell-voor beeld:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Down load](https://www.microsoft.com/download/details.aspx?id=39717) en installeer de zelf-hostende Integration runtime op een lokale computer.

3. De verificatie sleutel ophalen en de zelf-hostende Integration runtime registreren bij de sleutel. Hier volgt een Power shell-voor beeld:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Een zelf-hostende IR instellen op een virtuele Azure-machine met behulp van een Azure Resource Manager sjabloon 
U kunt zelf-hostende IR-instellingen voor een virtuele Azure-machine automatiseren met behulp van [deze Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Met deze sjabloon kunt u eenvoudig een volledig functionerende, zelf-hostende IR in een virtueel Azure-netwerk met hoge Beschik baarheid en schaal baarheid instellen (mits u het aantal knoop punten instelt op 2 of hoger).

## <a name="command-flow-and-data-flow"></a>Opdracht stroom en gegevens stroom
Wanneer u gegevens verplaatst tussen on-premises en de Cloud, gebruikt de activiteit een zelf-hostende Integration runtime om de gegevens over te dragen van een on-premises gegevens bron naar de Cloud en omgekeerd.

Hier volgt een gegevens stroom op hoog niveau voor het samen vatting van de stappen voor het kopiëren met een zelf-hostende IR:

![Overzicht](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. De data ontwikkelaar maakt een zelf-hostende Integration runtime binnen een Azure-data factory met behulp van een Power shell-cmdlet. De Azure Portal biedt momenteel geen ondersteuning voor deze functie.
2. De data ontwikkelaar maakt een gekoppelde service voor een on-premises gegevens Archief door het zelf-hostende Integration runtime-exemplaar op te geven dat moet worden gebruikt om verbinding te maken met gegevens archieven.
3. Het knoop punt voor de zelf-hostende Integration runtime versleutelt de referenties met behulp van Windows Data Protection Application Programming Interface (DPAPI) en slaat de referenties lokaal op. Als er meerdere knoop punten zijn ingesteld voor hoge Beschik baarheid, worden de referenties verder gesynchroniseerd tussen andere knoop punten. Elk knoop punt versleutelt de referenties met behulp van DPAPI en slaat ze lokaal op. Synchronisatie van referenties is transparant voor de gegevens ontwikkelaar en wordt verwerkt door de zelf-hostende IR.    
4. De Data Factory-service communiceert met de zelf-hostende Integration runtime voor het plannen en beheren van taken via een *besturings kanaal* dat gebruikmaakt van een gedeelde [Azure service bus relay](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay). Wanneer een activiteiten taak moet worden uitgevoerd, Data Factory wacht rijen de aanvraag samen met eventuele referentie gegevens (in het geval dat referenties niet al zijn opgeslagen in de zelf-hostende Integration runtime). De zelf-hostende Integration runtime start de taak na het navragen van de wachtrij.
5. De zelf-hostende Integration runtime kopieert gegevens van een on-premises Store naar een Cloud opslag, of andersom, afhankelijk van de manier waarop de Kopieer activiteit is geconfigureerd in de gegevens pijplijn. Voor deze stap communiceert de zelf-hostende Integration runtime rechtstreeks met opslag Services op basis van de Cloud, zoals Azure Blob-opslag via een beveiligd kanaal (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Overwegingen voor het gebruik van een zelf-hostende IR

- Eén zelf-hostende Integration runtime kan worden gebruikt voor meerdere on-premises gegevens bronnen. Eén zelf-hostende Integration runtime kan worden gedeeld met een andere data factory binnen dezelfde Azure Active Directory Tenant. Zie [een zelf-hostende Integration runtime delen](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)voor meer informatie.
- U kunt slechts één exemplaar van een zelf-hostende Integration runtime op één computer hebben geïnstalleerd. Als u twee gegevens fabrieken hebt die toegang nodig hebben tot on-premises gegevens bronnen, gebruikt u de [zelf-hostende functie](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories) voor het delen van IR om de zelf-hostende Integration runtime te delen of installeert u de zelf-hostende Integration runtime op twee on-premises computers, één voor elk data factory.  
- De zelf-hostende Integration runtime hoeft zich niet op dezelfde computer als de gegevens bron te bevinden. De zelf-hostende Integration runtime dichter bij de gegevens bron vermindert echter de tijd voor de zelf-hostende Integration runtime om verbinding te maken met de gegevens bron. U wordt aangeraden de zelf-hostende Integration runtime te installeren op een computer die verschilt van de versie die als host fungeert voor een on-premises gegevens bron. Wanneer de zelf-hostende Integration runtime en de gegevens bron zich op verschillende computers bevinden, kan de zelf-hostende Integration runtime geen concurrentie vormen voor bronnen met de gegevens bron.
- U kunt meerdere zelf-hostende Integration Runtimes op verschillende computers hebben die verbinding maken met dezelfde on-premises gegevens bron. U kunt bijvoorbeeld twee zelf-hostende Integration Runtimes hebben die twee gegevens fabrieken gebruiken, maar dezelfde on-premises gegevens bron wordt geregistreerd met de gegevens fabrieken.
- Als u al een gateway op uw computer hebt geïnstalleerd voor een Power BI scenario, installeert u een afzonderlijke zelf-hostende Integration runtime voor Azure Data Factory op een andere computer.
- De zelf-hostende Integration runtime moet worden gebruikt voor de ondersteuning van gegevens integratie binnen een virtueel Azure-netwerk.
- Behandel uw gegevens bron als een on-premises gegevens bron die zich achter een firewall bevindt, zelfs wanneer u Azure ExpressRoute gebruikt. Gebruik de zelf-hostende Integration runtime om connectiviteit tussen de service en de gegevens bron tot stand te brengen.
- U moet de zelf-hostende Integration runtime gebruiken, zelfs als het gegevens archief zich in de Cloud op een virtuele Azure IaaS-machine bevindt.
- Taken kunnen mislukken in een zelf-hostende Integration runtime die is geïnstalleerd op een Windows-Server waarop FIPS-compatibele versleuteling is ingeschakeld. U kunt dit probleem omzeilen door FIPS-compatibele versleuteling op de server uit te scha kelen. Als u FIPS-compatibele versleuteling wilt uitschakelen, wijzigt u de volgende register waarde van 1 (ingeschakeld) in 0 (uitgeschakeld): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Vereisten

- De ondersteunde besturingssysteem versies zijn Windows 7 Service Pack 1, Windows 8,1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 en Windows Server 2019. De installatie van de zelf-hostende Integration runtime op een domein controller wordt niet ondersteund.
- .NET Framework 4.6.1 of hoger is vereist. Als u de zelf-hostende Integration runtime installeert op een computer met Windows 7, installeert u .NET Framework 4.6.1 of hoger. Zie [.NET Framework systeem vereisten](/dotnet/framework/get-started/system-requirements) voor meer informatie.
- De aanbevolen configuratie voor de zelf-hostende Integration runtime-computer is ten minste 2 GHz, vier kernen, 8 GB RAM en een schijf van 80 GB.
- Als de hostmachine in de slaap stand staat, reageert de zelf-hostende Integration runtime niet op gegevens aanvragen. Configureer een geschikt energiebeheer schema op de computer voordat u de zelf-hostende Integration runtime installeert. Als de computer is geconfigureerd voor de slaap stand, wordt door de installatie van de zelf-hostende Integration runtime een bericht gevraagd.
- U moet een beheerder op de computer zijn om de zelf-hostende Integration runtime te kunnen installeren en configureren.
- De uitvoering van de Kopieer activiteit gebeurt op een specifieke frequentie. Resource gebruik (CPU, geheugen) op de machine volgt hetzelfde patroon met piek-en niet-actieve tijden. Het resource gebruik is ook afhankelijk van de hoeveelheid gegevens die wordt verplaatst. Wanneer er meerdere Kopieer taken worden uitgevoerd, ziet u het resource gebruik tijdens piek tijden.
- Taken kunnen mislukken als gegevens worden geëxtraheerd in Parquet-, ORC-of Avro-indelingen. Het bestand wordt gemaakt op de zelf-hostende integratie computer en vereist dat de volgende vereisten werken zoals verwacht (Zie Parquet- [indeling in azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) -pakket (x64)
    - Java runtime (JRE) versie 8 van een JRE-provider, zoals het [aannemen van openjdk](https://adoptopenjdk.net/), om ervoor te zorgen dat de `JAVA_HOME`-omgevings variabele is ingesteld.

## <a name="installation-best-practices"></a>Best practices voor installatie
U kunt de zelf-hostende Integration runtime installeren door een MSI-installatie pakket te downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=39717). Zie [gegevens verplaatsen tussen on-premises en Cloud artikel](tutorial-hybrid-copy-powershell.md) voor stapsgewijze instructies.

- Configureer een energiebeheer schema op de hostmachine voor de zelf-hostende Integration runtime, zodat de computer niet in de sluimer stand wordt gezet. Als de hostcomputer zich in de slaap stand bevindt, gaat de zelf-hostende Integration runtime offline.
- Maak regel matig een back-up van de referenties die zijn gekoppeld aan de zelf-hostende Integration runtime.
- Voor het automatiseren van zelf-gehoste IR-installatie bewerkingen, raadpleegt u de [onderstaande sectie](#automation-support-for-self-hosted-ir-function).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Zelf-hostende IR installeren en registreren vanuit het Download centrum

1. Ga naar de [Download pagina voor micro soft Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Selecteer **downloaden**, selecteer de 64-bits versie (32-bits wordt niet ondersteund) en selecteer **volgende**.
3. Voer het MSI-bestand rechtstreeks uit of sla het op de vaste schijf op en voer het uit.
4. Selecteer op de pagina **Welkom** een taal en selecteer **volgende**.
5. Ga akkoord met de licentie voorwaarden voor micro soft-software en selecteer **volgende**.
6. Selecteer **map** om de zelf-hostende Integration runtime te installeren en selecteer **volgende**.
7. Selecteer **installeren**op de pagina **gereed voor installatie** .
8. Klik op **volt ooien** om de installatie te volt ooien.
9. De verificatie sleutel ophalen met behulp van Azure PowerShell. Hier volgt een Power shell-voor beeld voor het ophalen van de verificatie sleutel:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Voer de volgende stappen uit op de pagina **registreren Integration runtime (zelf-hostend)** van Microsoft Integration Runtime Configuration Manager op uw computer wordt uitgevoerd:

    a. Plak de verificatie sleutel in het tekst gebied.

    b. Selecteer eventueel **verificatie sleutel weer geven** om de sleutel tekst te bekijken.

    c. Selecteer **Registreren**.

## <a name="automation-support-for-self-hosted-ir-function"></a>Automatiserings ondersteuning voor zelf-hostende IR-functie


> [!NOTE]
> Als u van plan bent de zelf-hostende IR te installeren op een virtuele machine van Azure en de installatie met behulp van Azure Resource Manager sjablonen wilt automatiseren, raadpleegt u de [sectie](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template).

U kunt de opdracht regel gebruiken voor het instellen of beheren van een bestaande zelf-hostende IR. Dit kan vooral worden gebruikt voor het automatiseren van de installatie, registratie van zelf-hostende IR-knoop punten. 

**Dmgcmd. exe** is opgenomen in de zelf-hostende installatie, normaal gesp roken: C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ map. Dit ondersteunt verschillende para meters en kan worden aangeroepen via de opdracht prompt met behulp van batch scripts voor automatisering. 

*Belasting* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Details (para meters/eigenschap):* 

| Eigenschap                                                    | Beschrijving                                                  | Verplicht |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Integration Runtime (zelf-hostend) knoop punt registreren met de opgegeven verificatie sleutel | Nee       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Externe toegang inschakelen op het huidige knoop punt voor het instellen van een cluster met hoge Beschik baarheid en/of het inschakelen van de instelling van referenties rechtstreeks op de zelf-hostende IR (zonder gebruik te maken van de ADF-service) met  **Cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** van een externe computer in hetzelfde netwerk. | Nee       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Externe toegang tot het huidige knoop punt inschakelen wanneer het knoop punt wordt uitgevoerd in de container | Nee       |
| DisableRemoteAccess                                         | Externe toegang tot het huidige knoop punt uitschakelen. Externe toegang is vereist voor het instellen van meerdere knoop punten. De Power shell-cmdlet New-**AzDataFactoryV2LinkedServiceEncryptedCredential** werkt nog steeds, zelfs wanneer externe toegang is uitgeschakeld, zolang deze wordt uitgevoerd op dezelfde computer als het zelf-HOSTende IR-knoop punt. | Nee       |
| Sleutel "`<AuthenticationKey>`"                                 | De vorige verificatie sleutel overschrijven/bijwerken. Let erop dat dit kan leiden tot het vorige zelf-hostende IR-knoop punt, als de sleutel een nieuwe Integration runtime is. | Nee       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Het back-upbestand voor het huidige knoop punt genereren, het back-upbestand bevat de referenties van de knooppunt sleutel en het gegevens archief | Nee       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | Het knoop punt terugzetten vanuit een back-upbestand                          | Nee       |
| Opnieuw starten                                                     | De Integration Runtime (zelf-hostend) host-service opnieuw starten   | Nee       |
| Beginnen                                                       | De Integration Runtime (zelf-hostend) host-service starten     | Nee       |
| Stoppen                                                        | Integration Runtime (zelf-hostend)-update service stoppen        | Nee       |
| StartUpgradeService                                         | Integration Runtime (zelf-hostend)-update service starten       | Nee       |
| StopUpgradeService                                          | Integration Runtime (zelf-hostend)-update service stoppen        | Nee       |
| TurnOnAutoUpdate                                            | Automatische updates van Integration Runtime (zelf-hostend) inschakelen        | Nee       |
| TurnOffAutoUpdate                                           | Automatisch bijwerken Integration Runtime (zelf-hostend) uitschakelen       | Nee       |
| SwitchServiceAccount "< domein\gebruiker >" ["wacht woord"]           | Stel DIAHostService in om te worden uitgevoerd als een nieuw account. Leeg wacht woord ("") voor systeem account of virtueel account gebruiken | Nee       |
| Loglevel `<logLevel>`                                       | ETW-logboek niveau instellen (uit, fout, uitgebreid of alle). Wordt doorgaans gebruikt door micro soft ondersteuning bij het opsporen van fouten. | Nee       |

   


## <a name="high-availability-and-scalability"></a>Hoge Beschik baarheid en schaal baarheid
Een zelf-hostende Integration runtime kan worden gekoppeld aan meerdere on-premises machines of Virtual Machines in Azure. Deze computers worden knoop punten genoemd. U kunt Maxi maal vier knoop punten koppelen aan een zelf-hostende Integration runtime. De voor delen van het gebruik van meerdere knoop punten (on-premises machines waarop een gateway is geïnstalleerd) voor een logische gateway zijn:
* Een hogere Beschik baarheid van de zelf-hostende Integration runtime, zodat deze niet langer het Single Point of Failure in uw big data oplossing of integratie van Cloud gegevens met Azure Data Factory, waardoor de continuïteit met Maxi maal vier knoop punten kan worden gegarandeerd.
* Verbeterde prestaties en door Voer tijdens het verplaatsen van gegevens tussen on-premises en gegevens opslag in de Cloud. Meer informatie over [prestatie vergelijkingen](copy-activity-performance.md).

U kunt meerdere knoop punten koppelen door de zelf-hostende Integration runtime-software te installeren vanuit het [Download centrum](https://www.microsoft.com/download/details.aspx?id=39717). Registreer deze vervolgens met behulp van een van de verificatie sleutels die zijn verkregen van de cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey** , zoals beschreven in de [zelf studie](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> U hoeft geen nieuwe zelf-hostende Integration runtime te maken voor het koppelen van elk knoop punt. U kunt de zelf-hostende Integration runtime op een andere computer installeren en registreren met behulp van dezelfde verificatie sleutel. 

> [!NOTE]
> Voordat u een ander knoop punt voor hoge Beschik baarheid en schaal baarheid toevoegt, moet u ervoor zorgen dat de optie **externe toegang tot intranet** is ingeschakeld op het eerste knoop punt (**Microsoft Integration Runtime Configuration Manager** > **instellingen** >  **Externe toegang tot intranet**). 

### <a name="scale-considerations"></a>Schaal overwegingen

#### <a name="scale-out"></a>Uitschalen

Wanneer het beschik bare geheugen op de zelf-hostende IR laag is en het CPU-gebruik hoog is, kunt u een nieuw knoop punt toevoegen om de belasting over machines te schalen. Als er activiteiten mislukken omdat er een time-out optreedt of omdat het zelf-hostende IR-knoop punt offline is, is het handig als u een knoop punt aan de gateway toevoegt.

#### <a name="scale-up"></a>Omhoog schalen

Wanneer het beschik bare geheugen en de CPU niet goed worden gebruikt, maar de uitvoering van gelijktijdige taken de limiet bereikt, moet u omhoog schalen door het aantal gelijktijdige taken dat kan worden uitgevoerd op een knoop punt te verhogen. Het kan ook zijn dat u wilt opschalen wanneer activiteiten uitvallen omdat de zelf-hostende IR overbelast is. Zoals in de volgende afbeelding wordt weer gegeven, kunt u de maximale capaciteit voor een knoop punt verhogen:  

![Gelijktijdige taken verhogen die kunnen worden uitgevoerd op een knoop punt](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Vereisten voor TLS/SSL-certificaat

Hier volgen de vereisten voor het TLS/SSL-certificaat dat wordt gebruikt voor het beveiligen van de communicatie tussen de knoop punten van Integration runtime:

- Het certificaat moet een openbaar vertrouwd x509 v3-certificaat zijn. U wordt aangeraden certificaten te gebruiken die zijn uitgegeven door een open bare (partner) certificerings instantie (CA).
- Elk knoop punt voor Integration runtime moet dit certificaat vertrouwen.
- We raden geen SAN-certificaten (alternatieve naam voor onderwerp) aan omdat alleen het laatste SAN-item wordt gebruikt en alle andere worden genegeerd vanwege huidige beperkingen. Als u bijvoorbeeld een SAN-certificaat hebt waarvan de San's **node1.domain.contoso.com** en **node2.domain.contoso.com**zijn, kunt u dit certificaat alleen gebruiken op een computer waarvan de FQDN **node2.domain.contoso.com**is.
- Het certificaat ondersteunt elke sleutel grootte die wordt ondersteund door Windows Server 2012 R2 voor SSL-certificaten.
- Certificaten die CNG-sleutels gebruiken, worden niet ondersteund.  

> [!NOTE]
> Dit certificaat wordt gebruikt voor het versleutelen van poorten op een zelf-hostend IR-knoop punt, dat wordt gebruikt voor **communicatie tussen** knoop punten (voor status synchronisatie, waarbij de referenties voor de synchronisatie van gekoppelde services zijn opgenomen in knoop punten) en tijdens het **gebruik van de Power shell-cmdlet voor de instelling** van de referentie voor de gekoppelde service vanuit het lokale netwerk. We raden aan om dit certificaat te gebruiken als uw particuliere netwerk omgeving niet is beveiligd of als u de communicatie tussen knoop punten in uw particuliere netwerk ook wilt beveiligen. Gegevens verplaatsing in transit van een zelf-hostende IR naar andere gegevens archieven gebeurt altijd met het versleutelde kanaal, ongeacht of dit certificaat is ingesteld. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>De zelf-hostende Integration runtime delen met meerdere gegevens fabrieken

U kunt een bestaande zelf-hostende Integration runtime-infra structuur die u al in een data factory hebt ingesteld, opnieuw gebruiken. Hierdoor kunt u een *gekoppelde zelf-hostende Integration runtime* in een andere Data Factory maken door te verwijzen naar een bestaande zelf-hostende IR (gedeeld).

Zie [een gedeelde zelf-hostende Integration runtime maken in azure Data Factory met Power shell](create-shared-self-hosted-integration-runtime-powershell.md)om een zelf-hostende Integration runtime te delen met behulp van Power shell.

Bekijk de volgende video voor een inleiding en demonstratie van twaalf minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologie

- **Gedeelde IR**: de oorspronkelijke zelf-hostende IR die wordt uitgevoerd op een fysieke infra structuur.  
- **Gekoppelde IR**: de IR die verwijst naar een andere gedeelde IR. Dit is een logische IR en maakt gebruik van de infra structuur van een andere zelf-hostende IR (gedeeld).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Stappen op hoog niveau voor het maken van een gekoppelde zelf-hostende IR

1. Ken in de zelf-hostende IR te delen machtigingen toe aan de data factory waarin u de gekoppelde IR wilt maken. 

   ![Knop voor het verlenen van machtigingen op het tabblad delen](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Selecties voor het toewijzen van machtigingen](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Noteer de bron-ID van de zelf-hostende IR die moet worden gedeeld.

   ![Locatie van de resource-ID](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. Maak in de data factory waarvoor de machtigingen zijn verleend, een nieuwe zelf-hostende IR (gekoppeld) en voer de resource-ID in.

   ![Knop voor het maken van een gekoppelde zelf-hostende Integration runtime](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Vakken voor naam en Resource-ID](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Controleren 

- **Gedeelde IR**

  ![Selecties voor het zoeken naar een gedeelde Integration runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Tabblad voor bewaking](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Gekoppelde IR**

  ![Selecties voor het zoeken van een gekoppelde Integration runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Tabblad voor bewaking](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Bekende beperkingen van zelf-hostende IR-delen

* Het data factory waarin een gekoppelde IR wordt gemaakt, moet een [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)hebben. De gegevens fabrieken die in de Azure Portal-of Power shell-cmdlets zijn gemaakt, hebben standaard een impliciete MSI gemaakt. Maar wanneer een data factory wordt gemaakt via een Azure Resource Manager sjabloon of SDK, moet de **identiteits** eigenschap expliciet worden ingesteld om ervoor te zorgen dat Azure Resource Manager een Data Factory maakt dat een MSI bevat. 

* De Azure Data Factory .NET SDK die deze functie ondersteunt, is versie 1.1.0 of hoger.

* De gebruiker moet de rol owner of de overgenomen rol eigenaar hebben in de data factory waar de gedeelde IR zich bevindt om machtigingen te verlenen.

* De functie voor delen werkt alleen voor gegevens fabrieken binnen dezelfde Azure Active Directory Tenant.

* Voor Active Directory [gast gebruikers](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)werkt de zoek functie (alle gegevens fabrieken met behulp van een zoek woord) [niet](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits)in de gebruikers interface. Maar zolang de gast gebruiker de eigenaar van de data factory is, kunnen ze de IR delen zonder de zoek functionaliteit, door de MSI van de data factory waarmee de IR moet worden gedeeld, rechtstreeks te typen in het tekstvak **machtiging toewijzen** en **toevoegen** te selecteren in de Azure Data Factory-gebruikers interface. 

  > [!NOTE]
  > Deze functie is alleen beschikbaar in Azure Data Factory v2. 

## <a name="notification-area-icons-and-notifications"></a>Pictogrammen van het systeemvak en meldingen

Als u de cursor over het pictogram of het bericht in het systeemvak beweegt, vindt u informatie over de status van de zelf-hostende Integration runtime.

![Meldingen in het systeemvak](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Poorten en firewall
Er zijn twee firewalls waarmee u rekening moet houden: de firewall van het *bedrijf* die wordt uitgevoerd op de centrale router van de organisatie en de *Windows Firewall* die is geconfigureerd als een daemon op de lokale computer waarop de zelf-hostende Integration runtime is geïnstalleerd.

![Firewall](media/create-self-hosted-integration-runtime/firewall.png)

Op het niveau van de *bedrijfs firewall* moet u de volgende domeinen en uitgaande poorten configureren:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


Op het niveau van de *Windows Firewall* (computer niveau) zijn deze uitgaande poorten normaal gesp roken ingeschakeld. Als dat niet het geval is, kunt u de domeinen en poorten dienovereenkomstig configureren op een zelf-hostende Integration runtime-computer.

> [!NOTE]
> Op basis van uw bron en sinks moet u mogelijk extra domeinen en uitgaande poorten in de firewall van uw bedrijf of Windows Firewall toestaan.
>
> Voor sommige Cloud databases (bijvoorbeeld Azure SQL Database en Azure Data Lake), moet u mogelijk IP-adressen van zelf-hostende Integration runtime-machines toestaan voor de firewall configuratie.

### <a name="copy-data-from-a-source-to-a-sink"></a>Gegevens van een bron naar een Sink kopiëren
Zorg ervoor dat de firewall regels correct zijn ingeschakeld op de firewall van het bedrijf, Windows Firewall op de zelf-hostende Integration runtime-computer en het gegevens archief zelf. Door deze regels in te scha kelen, kan de zelf-hostende Integration runtime verbinding maken met zowel de bron-als de sink. Schakel regels in voor elk gegevens archief dat is betrokken bij de Kopieer bewerking.

Als u bijvoorbeeld van een on-premises gegevens archief wilt kopiëren naar een Azure SQL Database sink of een Azure SQL Data Warehouse-sink, voert u de volgende stappen uit:

1. Uitgaande TCP-communicatie toestaan op poort 1433 voor zowel Windows Firewall als bedrijfs firewall.
2. Configureer de firewall instellingen van de Azure-SQL database om het IP-adres van de zelf-hostende Integration runtime-machine toe te voegen aan de lijst met toegestane IP-adressen.

> [!NOTE]
> Als uw firewall geen uitgaande poort 1433 toestaat, kan de zelf-hostende Integration runtime niet rechtstreeks toegang krijgen tot de Azure-SQL database. In dit geval kunt u een [gefaseerde kopie](copy-activity-performance.md) van Azure SQL Database en Azure SQL Data Warehouse gebruiken. In dit scenario zou u alleen HTTPS (poort 443) nodig hebben voor het verplaatsen van gegevens.


## <a name="proxy-server-considerations"></a>Overwegingen voor de proxy server
Als uw bedrijfs netwerk omgeving gebruikmaakt van een proxy server voor toegang tot internet, configureert u de zelf-hostende Integration runtime om de juiste proxy-instellingen te gebruiken. U kunt de proxy instellen tijdens de eerste registratie fase.

![Proxy opgeven](media/create-self-hosted-integration-runtime/specify-proxy.png)

Als de zelf-hostende Integration runtime de proxy server gebruikt om verbinding te maken met de Cloud service, bron/bestemming (het HTTP/HTTPS-protocol), wordt deze geconfigureerd. U selecteert de **koppeling wijzigen** tijdens de eerste installatie. U ziet het dialoog venster proxy-instelling.

![Proxy instellen](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Er zijn drie configuratie opties:

- **Geen proxy gebruiken**: de zelf-hostende Integration runtime gebruikt geen enkele proxy om verbinding te maken met Cloud Services.
- **Systeem proxy gebruiken**: de zelf-hostende Integration runtime maakt gebruik van de proxy-instelling die is geconfigureerd in diahost. exe. config en diawp. exe. config. Als er geen proxy is geconfigureerd in diahost. exe. config en diawp. exe. config, maakt de zelf-hostende Integration runtime rechtstreeks verbinding met de Cloud service zonder een proxy te passeren.
- **Aangepaste proxy gebruiken**: Configureer de http-proxy-instelling die moet worden gebruikt voor de zelf-hostende Integration runtime, in plaats van configuraties te gebruiken in diahost. exe. config en diawp. exe. config. **Adres** en **poort** zijn vereist. **Gebruikers naam** en **wacht woord** zijn optioneel, afhankelijk van de verificatie-instelling van uw proxy. Alle instellingen worden versleuteld met Windows DPAPI op de zelf-hostende Integration runtime en lokaal opgeslagen op de computer.

De service Integration runtime host wordt automatisch opnieuw opgestart nadat u de bijgewerkte proxy-instellingen hebt opgeslagen.

Als de zelf-hostende Integration runtime is geregistreerd en u de proxy-instellingen wilt weer geven of bijwerken, gebruikt u Integration Runtime Configuration Manager.

1. Open **Microsoft Integration Runtime Configuration Manager**.
2. Schakel over naar het tabblad **Instellingen**.
3. Selecteer de koppeling **wijzigen** in de sectie **http-proxy** om het dialoog venster **http-proxy instellen** te openen.
4. Selecteer **Next**. Vervolgens wordt er een waarschuwing weer gegeven waarin u wordt gevraagd om de proxy instelling op te slaan en de hostservice voor Integration runtime opnieuw op te starten.

U kunt de HTTP-proxy weer geven en bijwerken met behulp van het Configuration Manager-hulp programma.

![Proxy weer geven](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Als u een proxy server met NTLM-verificatie instelt, wordt de service Integration runtime host uitgevoerd onder het domein account. Als u het wacht woord voor het domein account later wijzigt, moet u de configuratie-instellingen voor de service bijwerken en opnieuw opstarten. Vanwege deze vereiste raden we u aan om een toegewezen domein account te gebruiken voor toegang tot de proxy server waarvoor u het wacht woord regel matig niet hoeft bij te werken.

### <a name="configure-proxy-server-settings"></a>Proxyserver instellingen configureren

Als u de instelling **systeem proxy gebruiken** voor de http-proxy selecteert, gebruikt de zelf-hostende Integration runtime de proxy-instelling in diahost. exe. config en diawp. exe. config. Als er geen proxy is opgegeven in diahost. exe. config en diawp. exe. config, maakt de zelf-hostende Integration runtime rechtstreeks verbinding met de Cloud service zonder de proxy te passeren. De volgende procedure bevat instructies voor het bijwerken van het bestand diahost. exe. config:

1. Maak in Verkenner een veilige kopie van C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config om een back-up van het oorspronkelijke bestand te maken.
2. Open Notepad. exe, dat wordt uitgevoerd als Administrator, en open het tekst bestand C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Zoek de standaard code voor system.net, zoals wordt weer gegeven in de volgende code:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    U kunt vervolgens Details van de proxy server toevoegen, zoals wordt weer gegeven in het volgende voor beeld:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Aanvullende eigenschappen zijn toegestaan in het proxy-label om de vereiste instellingen op te geven, zoals `scriptLocation`. Zie [proxy-element (netwerk instellingen)](https://msdn.microsoft.com/library/sa91de1e.aspx) voor de syntaxis.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Sla het configuratie bestand op de oorspronkelijke locatie op. Start vervolgens de zelf-hostende Integration runtime-hostservice, waarmee de wijzigingen worden opgehaald. 

   Als u de service opnieuw wilt starten, gebruikt u het onderdeel Services van het configuratie scherm. Of selecteer de knop **service stoppen** in Integration Runtime Configuration Manager en selecteer vervolgens **service starten**. 
   
   Als de service niet wordt gestart, is het waarschijnlijk dat er een onjuiste syntaxis voor de XML-code is toegevoegd in het configuratie bestand van de toepassing dat is bewerkt.

> [!IMPORTANT]
> Vergeet niet om zowel diahost. exe. config als diawp. exe. config bij te werken.

U moet er ook voor zorgen dat Microsoft Azure zich in de acceptatie lijst van uw bedrijf bevindt. U kunt de lijst met geldige Microsoft Azure IP-adressen downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Mogelijke symptomen voor problemen met de firewall en de proxy server
Als er fouten optreden die vergelijkbaar zijn met de volgende, is het waarschijnlijk een onjuiste configuratie van de firewall of proxy server, waardoor de zelf-hostende Integration runtime geen verbinding kan maken met Data Factory om zichzelf te verifiëren. Raadpleeg de vorige sectie om ervoor te zorgen dat uw firewall en proxy server correct zijn geconfigureerd.

* Wanneer u de zelf-hostende Integration runtime probeert te registreren, wordt het volgende fout bericht weer gegeven: "kan dit knoop punt niet registreren Integration Runtime. Controleer of de verificatie sleutel geldig is en of de service Integration service host op deze computer wordt uitgevoerd.
* Wanneer u Integration Runtime Configuration Manager opent, ziet u de status **losgekoppeld** of verbinding **maken**. Wanneer u Windows-gebeurtenis logboeken bekijkt, worden in **Logboeken** > -**toepassing** > **Microsoft Integration runtime**de volgende fout berichten weer gegeven:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Externe toegang vanaf een intranet inschakelen  
Als u Power shell gebruikt voor het versleutelen van referenties van een andere computer (in het netwerk) dan waar de zelf-hostende Integration runtime is geïnstalleerd, kunt u de optie **externe toegang vanaf intranet** inschakelen. Als u Power shell uitvoert om referenties te versleutelen op dezelfde computer waarop de zelf-hostende Integration runtime is geïnstalleerd, kunt u **externe toegang vanaf intranet**niet inschakelen.

U moet **externe toegang vanaf intranet** inschakelen voordat u een ander knoop punt toevoegt voor hoge Beschik baarheid en schaal baarheid.  

Tijdens de zelf-hostende Integration runtime-installatie (versie 3.3. xxxx. x later), wordt de externe toegang voor de zelf-hostende Integration runtime-computer standaard uitgeschakeld op **basis van het intranet** .

Als u een firewall van derden gebruikt, kunt u hand matig poort 8060 (of de door de gebruiker geconfigureerde poort) openen. Als u een firewall probleem hebt tijdens het instellen van de zelf-hostende Integration runtime, gebruikt u de volgende opdracht om de zelf-hostende Integration runtime te installeren zonder de firewall te configureren:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Als u poort 8060 niet op de zelf-hostende Integration runtime-computer wilt openen, gebruikt u andere mechanismen dan de toepassing voor het instellen van referenties voor het configureren van referenties voor het gegevens archief. U kunt bijvoorbeeld de Power shell **-cmdlet New-AzDataFactoryV2LinkedServiceEncryptCredential** gebruiken.


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende zelf studie voor stapsgewijze instructies: [zelf studie: on-premises gegevens kopiëren naar de Cloud](tutorial-hybrid-copy-powershell.md).
