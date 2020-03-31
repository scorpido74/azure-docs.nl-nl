---
title: 'Azure AD Connect-synchronisatie: operationele taken en overwegingen | Microsoft Documenten'
description: In dit onderwerp worden operationele taken voor Azure AD Connect-synchronisatie beschreven en hoe u zich voorbereiden op het uitvoeren van dit onderdeel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc88640cdff4f716902a80bb149913b961d40ae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261019"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: staging-server en herstel na noodgevallen
Met een server in de faseringsmodus u wijzigingen aanbrengen in de configuratie en een voorbeeld van de wijzigingen bekijken voordat u de server actief maakt. Hiermee u ook volledige import en volledige synchronisatie uitvoeren om te controleren of alle wijzigingen worden verwacht voordat u deze wijzigingen in uw productieomgeving aanbrengt.

## <a name="staging-mode"></a>Faseringsmodus
De faseringsmodus kan worden gebruikt voor verschillende scenario's, waaronder:

* Hoge beschikbaarheid.
* Nieuwe configuratiewijzigingen testen en implementeren.
* Introduceer een nieuwe server en de ontmanteling van de oude.

Tijdens de installatie u de server selecteren die in **de faseringsmodus staat.** Met deze actie is de server actief voor import en synchronisatie, maar wordt er geen geëxporteerd. Een server in de faseringsmodus voert geen wachtwoordsynchronisatie of wachtwoordterugschrijftijd uit, zelfs niet als u deze functies tijdens de installatie hebt geselecteerd. Wanneer u de faseringsmodus uitschakelt, begint de server te exporteren, schakelt u wachtwoordsynchronisatie in en schakelt u wachtwoordterugschrijven in.

> [!NOTE]
> Stel dat u een Azure AD Connect with Password Hash Synchronization-functie hebt ingeschakeld. Wanneer u de faseringsmodus inschakelt, stopt de server met het synchroniseren van wachtwoordwijzigingen van on-premises AD. Wanneer u de faseringsmodus uitschakelt, hervat de server het synchroniseren van wachtwoordwijzigingen van waar deze voor het laatst was gebleven. Als de server gedurende een langere periode in de faseringsmodus blijft staan, kan het even duren voordat de server alle wachtwoordwijzigingen synchroniseert die tijdens de periode zijn opgetreden.
>
>

U een export nog steeds forceren met behulp van de synchronisatieservicemanager.

Een server in de faseringsmodus blijft wijzigingen ontvangen van Active Directory en Azure AD en kan de verantwoordelijkheden van een andere server snel overnemen in het geval van een storing. Als u configuratiewijzigingen aanbrengt in uw primaire server, is het uw verantwoordelijkheid om dezelfde wijzigingen aan te brengen in de server in de faseringsmodus.

Voor degenen onder u met kennis van oudere synchronisatietechnologieën is de staging-modus anders omdat de server zijn eigen SQL-database heeft. Met deze architectuur kan de staging-modusserver zich in een ander datacenter bevinden.

### <a name="verify-the-configuration-of-a-server"></a>De configuratie van een server verifiëren
Voer de volgende stappen uit om deze methode toe te passen:

1. [Voorbereiden](#prepare)
2. [Configuratie](#configuration)
3. [Importeren en synchroniseren](#import-and-synchronize)
4. [Controleren](#verify)
5. [Actieve server schakelen](#switch-active-server)

#### <a name="prepare"></a>Voorbereiden
1. Installeer Azure AD Connect, selecteer **de faseringsmodus**en schakel **startsynchronisatie** uit op de laatste pagina in de installatiewizard. Met deze modus u de synchronisatie-engine handmatig uitvoeren.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Meld u af/meld u aan en selecteer in het startmenu **Synchronisatieservice**.

#### <a name="configuration"></a>Configuratie
Als u aangepaste wijzigingen in de primaire server hebt aangebracht en de configuratie wilt vergelijken met de faseringsserver, gebruikt u [Azure AD Connect-configuratiedocumenter](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importeren en synchroniseren
1. Selecteer **Connectors**en selecteer de eerste connector met het type **Active Directory Domain Services**. Klik **op Uitvoeren,** selecteer **Volledige import**en **OK**. Doe deze stappen voor alle connectors van dit type.
2. Selecteer de connector met type **Azure Active Directory (Microsoft).** Klik **op Uitvoeren,** selecteer **Volledige import**en **OK**.
3. Controleer of het tabblad Connectors nog steeds is geselecteerd. Klik voor elke connector met type **Active Directory Domain Services**op **Uitvoeren**, selecteer **Deltasynchronisatie**en **OK**.
4. Selecteer de connector met type **Azure Active Directory (Microsoft).** Klik **op Uitvoeren,** selecteer **Deltasynchronisatie**en **OK**.

U hebt nu exportwijzigingen in Azure AD en on-premises AD in scène gezet (als u hybride implementatie van Exchange gebruikt). Met de volgende stappen u controleren wat er gaat veranderen voordat u daadwerkelijk begint met de export naar de mappen.

#### <a name="verify"></a>Verifiëren
1. Start een cmd prompt en ga naar`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Run: `csexport "Name of Connector" %temp%\export.xml /f:x` de naam van de connector is te vinden in de synchronisatieservice. Het heeft een naam die lijkt op 'contoso.com – AAD' voor Azure AD.
3. Uitvoeren: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` u hebt een bestand met de naam export.csv met de naam export.csv dat kan worden onderzocht in Microsoft Excel. Dit bestand bevat alle wijzigingen die op het punt staan te worden geëxporteerd.
4. Breng de nodige wijzigingen aan in de gegevens of configuratie en voer deze stappen opnieuw uit (Importeren en synchroniseren en verifiëren) totdat de wijzigingen worden verwacht die op het punt staan te worden geëxporteerd.

**Inzicht in het bestand export.csv** Het grootste deel van het bestand spreekt voor zich. Enkele afkortingen om de inhoud te begrijpen:
* OMODT – Object Wijziging type. Hiermee geeft u aan of de bewerking op objectniveau een toevoegen, bijwerken of verwijderen is.
* AMODT – Type wijziging sattribute. Hiermee geeft u aan of de bewerking op een kenmerkniveau een toevoegen, bijwerken of verwijderen is.

**Algemene id's ophalen** Het bestand export.csv bevat alle wijzigingen die op het punt staan te worden geëxporteerd. Elke rij komt overeen met een wijziging voor een object in de verbindingsruimte en het object wordt geïdentificeerd door het kenmerk DN. Het kenmerk DN is een unieke id die is toegewezen aan een object in de verbindingsruimte. Wanneer u veel rijen/wijzigingen in de export.csv hebt om te analyseren, kan het voor u moeilijk zijn om erachter te komen voor welke objecten de wijzigingen alleen op basis van het kenmerk DN zijn bedoeld. Gebruik het script csanalyzer.ps1 PowerShell om het proces van het analyseren van de wijzigingen te vereenvoudigen. Het script haalt algemene id's (bijvoorbeeld displayName, userPrincipalName) van de objecten op. Ga als u het script gaat gebruiken:
1. Kopieer het PowerShell-script van de sectie `csanalyzer.ps1` [CSAnalyzer](#appendix-csanalyzer) naar een bestand met de naam .
2. Open een PowerShell-venster en blader naar de map waar u het PowerShell-script hebt gemaakt.
3. Uitvoeren: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. U hebt nu een bestand met de naam **processedusers1.csv** dat kan worden onderzocht in Microsoft Excel. Houd er rekening mee dat het bestand een toewijzing van het kenmerk DN aan algemene id's bevat (bijvoorbeeld displayName en userPrincipalName). Het bevat momenteel niet de werkelijke attribuutwijzigingen die op het punt staan te worden geëxporteerd.

#### <a name="switch-active-server"></a>Actieve server schakelen
1. Schakel op de momenteel actieve server de server uit (DirSync/FIM/Azure AD Sync) zodat deze niet naar Azure AD exporteert of deze niet in de faseringsmodus (Azure AD Connect) instelt.
2. Voer de installatiewizard op de server uit in **de faseringsmodus** en schakel **de faseringsmodus**uit.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Herstel na noodgeval
Een deel van het implementatieontwerp is om te plannen wat te doen in het geval er een ramp is waarbij u de synchronisatieserver verliest. Er zijn verschillende modellen te gebruiken en welke te gebruiken is afhankelijk van verschillende factoren, waaronder:

* Wat is uw tolerantie voor het niet kunnen aanbrengen van wijzigingen in objecten in Azure AD tijdens de downtime?
* Als u wachtwoordsynchronisatie gebruikt, accepteren de gebruikers dan dat ze het oude wachtwoord in Azure AD moeten gebruiken voor het geval ze het on-premises wijzigen?
* Bent u afhankelijk van realtime bewerkingen, zoals het terugschrijven van wachtwoorden?

Afhankelijk van de antwoorden op deze vragen en het beleid van uw organisatie, kan een van de volgende strategieën worden geïmplementeerd:

* Herbouwen wanneer dat nodig is.
* Hebben een reserve stand-by server, bekend als **staging mode**.
* Gebruik virtuele machines.

Als u de ingebouwde SQL Express-database niet gebruikt, moet u ook de sectie [SQL High Availability](#sql-high-availability) controleren.

### <a name="rebuild-when-needed"></a>Herbouwen wanneer dat nodig is
Een haalbare strategie is om te plannen voor een server opnieuw te bouwen wanneer dat nodig is. Meestal kan het installeren van de synchronisatie-engine en het doen van de eerste import en synchronisatie binnen een paar uur worden voltooid. Als er geen reserveserver beschikbaar is, is het mogelijk om tijdelijk een domeincontroller te gebruiken om de synchronisatieengine te hosten.

De synchronisatieserver slaat geen status over de objecten op, zodat de database kan worden herbouwd op basis van de gegevens in Active Directory en Azure AD. Het kenmerk **sourceAnchor** wordt gebruikt om de objecten van on-premises en de cloud aan te sluiten. Als u de server opnieuw opbouwt met bestaande objecten on-premises en de cloud, komt de synchronisatieengine deze objecten bij herinstallatie weer aan elkaar. De dingen die u moet documenteren en opslaan zijn de configuratiewijzigingen die aan de server zijn aangebracht, zoals filter- en synchronisatieregels. Deze aangepaste configuraties moeten opnieuw worden toegepast voordat u begint met synchroniseren.

### <a name="have-a-spare-standby-server---staging-mode"></a>Een reservestand-byserver hebben - faseringsmodus
Als u een complexere omgeving hebt, wordt het hebben van een of meer stand-byservers aanbevolen. Tijdens de installatie u een server inschakelen om zich in **de faseringsmodus**te bevinden.

Zie [staging-modus](#staging-mode)voor meer informatie .

### <a name="use-virtual-machines"></a>Virtuele machines gebruiken
Een veelgebruikte en ondersteunde methode is het uitvoeren van de synchronisatie-engine in een virtuele machine. In het geval dat de host een probleem heeft, kan de afbeelding met de synchronisatieserver worden gemigreerd naar een andere server.

### <a name="sql-high-availability"></a>SQL-hoge beschikbaarheid
Als u de SQL Server Express die wordt geleverd met Azure AD Connect niet gebruikt, moet ook rekening worden gehouden met hoge beschikbaarheid voor SQL Server. De ondersteunde oplossingen voor hoge beschikbaarheid zijn SQL-clustering en AOA (Always On Availability Groups). Niet-ondersteunde oplossingen omvatten spiegelen.

Ondersteuning voor SQL AOA is toegevoegd aan Azure AD Connect in versie 1.1.524.0. U moet SQL AOA inschakelen voordat u Azure AD Connect installeert. Tijdens de installatie detecteert Azure AD Connect of de meegeleverde SQL-instantie is ingeschakeld voor SQL AOA of niet. Als SQL AOA is ingeschakeld, zoekt Azure AD Connect verder uit of SQL AOA is geconfigureerd om synchrone replicatie of asynchrone replicatie te gebruiken. Bij het instellen van de listener voor beschikbaarheidsgroepen wordt aanbevolen de eigenschap RegisterAllProvidersIP in te stellen op 0. Dit komt omdat Azure AD Connect momenteel SQL Native Client gebruikt om verbinding te maken met SQL en SQL Native Client het gebruik van de eigenschap MultiSubNetFailover niet ondersteunt.

## <a name="appendix-csanalyzer"></a>Aanhangsel CSAnalyzer
Zie de sectie [verifiëren](#verify) over het gebruik van dit script.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Volgende stappen
**Overzichtsonderwerpen**  

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)  
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)  
