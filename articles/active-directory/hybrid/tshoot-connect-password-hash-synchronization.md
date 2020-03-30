---
title: Problemen met wachtwoordhashsynchronisatie oplossen met Azure AD Connect-synchronisatie | Microsoft Documenten
description: In dit artikel vindt u informatie over het oplossen van problemen met wachtwoordhashsynchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253544"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Problemen met wachtwoord-hashsynchronisatie oplossen met Azure AD Connect-synchronisatie

In dit onderwerp vindt u stappen voor het oplossen van problemen met wachtwoordhashsynchronisatie. Als wachtwoorden niet worden gesynchroniseerd zoals verwacht, kan dit zowel voor een subset van gebruikers als voor alle gebruikers zijn.

Gebruik de taak voor probleemoplossing in de wizard om problemen met het synchroniseren van wachtwoordhashproblemen op te lossen voor Azure Active Directory (Azure AD) Verbinding maken met versie 1.1.614.0 of daarna:

* Als u een probleem hebt waarbij geen wachtwoorden worden gesynchroniseerd, raadpleegt u de [no-wachtwoorden worden gesynchroniseerd: problemen oplossen met behulp van de](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) taaksectie probleemoplossing.

* Als u een probleem hebt met afzonderlijke objecten, verwijst u naar het [één object is het niet synchroniseren van wachtwoorden: problemen oplossen met behulp van de taaksectie voor het oplossen van problemen.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task)

Voor implementatie met versie 1.1.524.0 of hoger is er een diagnostische cmdlet die u gebruiken om problemen met wachtwoordhashsynchronisatie op te lossen:

* Als u een probleem hebt waarbij geen wachtwoorden worden gesynchroniseerd, raadpleegt u de [no-wachtwoorden worden gesynchroniseerd: problemen oplossen met behulp van de diagnostische cmdletsectie.](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)

* Als u een probleem hebt met afzonderlijke objecten, raadpleegt u het [one-object niet om wachtwoorden te synchroniseren: problemen oplossen met behulp van de diagnostische cmdlet-sectie.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet)

Voor oudere versies van Azure AD Connect-implementatie:

* Als u een probleem hebt waarbij geen wachtwoorden worden gesynchroniseerd, raadpleegt u de [sectie Geen wachtwoorden worden gesynchroniseerd: sectie handmatige probleemoplossingsstappen.](#no-passwords-are-synchronized-manual-troubleshooting-steps)

* Als u een probleem hebt met afzonderlijke objecten, raadpleegt u het one-object niet om wachtwoorden te synchroniseren: sectie [handmatige probleemoplossingsstappen.](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Er worden geen wachtwoorden gesynchroniseerd: problemen oplossen met de taak voor het oplossen van problemen

U de taak voor het oplossen van problemen gebruiken om erachter te komen waarom er geen wachtwoorden worden gesynchroniseerd.

> [!NOTE]
> De probleemoplossingstaak is alleen beschikbaar voor Azure AD Connect-versie 1.1.614.0 of hoger.

### <a name="run-the-troubleshooting-task"></a>De taak voor het oplossen van problemen uitvoeren

Ga als instellen voor het oplossen van problemen waarbij geen wachtwoorden worden gesynchroniseerd:

1. Open een nieuwe Windows PowerShell-sessie op uw Azure AD Connect-server met de optie **Uitvoeren als administrator.**

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`of .

3. Start de wizard Azure AD Connect.

4. Navigeer naar de pagina **Extra taken,** selecteer **Problemen oplossen**en klik op **Volgende**.

5. Klik op de pagina Probleemoplossing op **Starten** om het menu probleemoplossing in PowerShell te starten.

6. Selecteer in het hoofdmenu **Problemen met wachtwoordhashsynchronisatie**.

7. Selecteer In het submenu de optie **Wachtwoordhashsynchronisatie werkt helemaal niet.**

### <a name="understand-the-results-of-the-troubleshooting-task"></a>De resultaten van de probleemoplossingstaak begrijpen

De taak voor het oplossen van problemen voert de volgende controles uit:

* Valideert dat de functie voor wachtwoordhashsynchronisatie is ingeschakeld voor uw Azure AD-tenant.

* Valideert dat de Azure AD Connect-server zich niet in de faseringsmodus bevindt.

* Voor elke bestaande on-premises Active Directory-connector (die overeenkomt met een bestaand Active Directory-forest):

   * Hiermee wordt gevalideerd dat de functie wachtwoordhashsynchronisatie is ingeschakeld.
   
   * Hiermee wordt gezocht naar heartbeatgebeurtenissen voor wachtwoordhashin de logboeken van Windows Application Event.

   * Voor elk Active Directory-domein onder de on-premises Active Directory-connector:

      * Valideert dat het domein bereikbaar is vanaf de Azure AD Connect-server.

      * Hiermee wordt gevalideerd dat de AD DS-accounts (Active Directory Domain Services) die worden gebruikt door de on-premises Active Directory-connector de juiste gebruikersnaam, wachtwoord en machtigingen hebben die nodig zijn voor synchronisatie van wachtwoordhash.

In het volgende diagram worden de resultaten van de cmdlet voor één domein, on-premises Active Directory-topologie geïllustreerd:

![Diagnostische uitvoer voor wachtwoordhashsynchronisatie](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

De rest van deze sectie beschrijft specifieke resultaten die worden geretourneerd door de taak en bijbehorende problemen.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>wachtwoordhashsynchronisatiefunctie is niet ingeschakeld

Als u synchronisatie van wachtwoordhash niet hebt ingeschakeld met de wizard Azure AD Connect, wordt de volgende fout geretourneerd:

![synchronisatie van wachtwoordhash is niet ingeschakeld](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect-server bevindt zich in de faseringsmodus

Als de Azure AD Connect-server zich in de faseringsmodus bevindt, wordt wachtwoordhashsynchronisatie tijdelijk uitgeschakeld en wordt de volgende fout geretourneerd:

![Azure AD Connect-server bevindt zich in de faseringsmodus](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Geen heartbeatgebeurtenissen voor wachtwoordhash

Elke on-premises Active Directory-connector heeft zijn eigen wachtwoordhashsynchronisatiekanaal. Wanneer het wachtwoordhashsynchronisatiekanaal is ingesteld en er geen wachtwoordwijzigingen moeten worden gesynchroniseerd, wordt een heartbeatgebeurtenis (EventId 654) eenmaal per 30 minuten gegenereerd onder het Gebeurtenislogboek van Windows-toepassingen. Voor elke on-premises Active Directory-connector zoekt de cmdlet naar bijbehorende heartbeatgebeurtenissen in de afgelopen drie uur. Als er geen heartbeatgebeurtenis wordt gevonden, wordt de volgende fout geretourneerd:

![Geen wachtwoord hash synchronisatie hartslag gebeurtenis](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS-account heeft geen juiste machtigingen

Als het AD DS-account dat wordt gebruikt door de on-premises Active Directory-connector om wachtwoordhashes te synchroniseren niet over de juiste machtigingen beschikt, wordt de volgende fout geretourneerd:

![Onjuiste referenties](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Gebruikersnaam of wachtwoord voor een onjuist AD DS-account

Als het AD DS-account dat wordt gebruikt door de on-premises Active Directory-connector om wachtwoordhashes te synchroniseren een onjuiste gebruikersnaam of wachtwoord heeft, wordt de volgende fout geretourneerd:

![Onjuiste referenties](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Eén object synchroniseert geen wachtwoorden: problemen oplossen met de taak voor probleemoplossing

U de taak voor het oplossen van problemen gebruiken om te bepalen waarom één object geen wachtwoorden synchroniseert.

> [!NOTE]
> De probleemoplossingstaak is alleen beschikbaar voor Azure AD Connect-versie 1.1.614.0 of hoger.

### <a name="run-the-diagnostics-cmdlet"></a>De diagnosecmdlet uitvoeren

Ga als lid van het probleem van een specifiek gebruikersobject als een oplossing voor problemen voor een specifiek gebruikersobject:

1. Open een nieuwe Windows PowerShell-sessie op uw Azure AD Connect-server met de optie **Uitvoeren als administrator.**

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`of .

3. Start de wizard Azure AD Connect.

4. Navigeer naar de pagina **Extra taken,** selecteer **Problemen oplossen**en klik op **Volgende**.

5. Klik op de pagina Probleemoplossing op **Starten** om het menu probleemoplossing in PowerShell te starten.

6. Selecteer in het hoofdmenu **Problemen met wachtwoordhashsynchronisatie**.

7. Selecteer Wachtwoord wordt in het submenu **niet gesynchroniseerd voor een specifiek gebruikersaccount.**

### <a name="understand-the-results-of-the-troubleshooting-task"></a>De resultaten van de probleemoplossingstaak begrijpen

De taak voor het oplossen van problemen voert de volgende controles uit:

* Onderzoekt de status van het Active Directory-object in de Active Directory-connectorruimte, Metaverse en Azure AD-connectorruimte.

* Hiermee wordt gevalideerd dat er synchronisatieregels zijn waarbij wachtwoordhashsynchronisatie is ingeschakeld en toegepast op het Active Directory-object.

* Pogingen om de resultaten van de laatste poging om het wachtwoord voor het object te synchroniseren op te halen en weer te geven.

In het volgende diagram worden de resultaten van de cmdlet weergegeven bij het oplossen van wachtwoordhashsynchronisatie voor één object:

![Diagnostische uitvoer voor wachtwoordhashsynchronisatie - één object](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

De rest van deze sectie beschrijft specifieke resultaten geretourneerd door de cmdlet en de bijbehorende problemen.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Het Active Directory-object wordt niet geëxporteerd naar Azure AD

synchronisatie van wachtwoordhash voor dit on-premises Active Directory-account mislukt omdat er geen bijbehorend object in de Azure AD-tenant is. De volgende fout wordt geretourneerd:

![Azure AD-object ontbreekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Gebruiker heeft een tijdelijk wachtwoord

Momenteel biedt Azure AD Connect geen ondersteuning voor het synchroniseren van tijdelijke wachtwoorden met Azure AD. Een wachtwoord wordt als tijdelijk beschouwd als de **optie Wachtwoord wijzigen bij de volgende aanmelding** is ingesteld op de on-premises Active Directory-gebruiker. De volgende fout wordt geretourneerd:

![Tijdelijk wachtwoord wordt niet geëxporteerd](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Resultaten van laatste poging om wachtwoord te synchroniseren zijn niet beschikbaar

Azure AD Connect slaat standaard zeven dagen lang de resultaten van wachtwoordhashsynchronisatiepogingen op. Als er geen resultaten beschikbaar zijn voor het geselecteerde Active Directory-object, wordt de volgende waarschuwing geretourneerd:

![Diagnostische uitvoer voor één object - geen wachtwoordsynchronisatiegeschiedenis](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Er worden geen wachtwoorden gesynchroniseerd: problemen oplossen met behulp van de diagnostische cmdlet

U `Invoke-ADSyncDiagnostics` de cmdlet gebruiken om erachter te komen waarom er geen wachtwoorden worden gesynchroniseerd.

> [!NOTE]
> De `Invoke-ADSyncDiagnostics` cmdlet is alleen beschikbaar voor Azure AD Connect-versie 1.1.524.0 of hoger.

### <a name="run-the-diagnostics-cmdlet"></a>De diagnosecmdlet uitvoeren

Ga als instellen voor het oplossen van problemen waarbij geen wachtwoorden worden gesynchroniseerd:

1. Open een nieuwe Windows PowerShell-sessie op uw Azure AD Connect-server met de optie **Uitvoeren als administrator.**

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`of .

3. Voer `Import-Module ADSyncDiagnostics` uit.

4. Voer `Invoke-ADSyncDiagnostics -PasswordSync` uit.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Eén object synchroniseert geen wachtwoorden: problemen oplossen met behulp van de diagnostische cmdlet

U `Invoke-ADSyncDiagnostics` de cmdlet gebruiken om te bepalen waarom één object geen wachtwoorden synchroniseert.

> [!NOTE]
> De `Invoke-ADSyncDiagnostics` cmdlet is alleen beschikbaar voor Azure AD Connect-versie 1.1.524.0 of hoger.

### <a name="run-the-diagnostics-cmdlet"></a>De diagnosecmdlet uitvoeren

Ga als lid van het werk om problemen op te lossen waarbij geen wachtwoorden voor een gebruiker worden gesynchroniseerd:

1. Open een nieuwe Windows PowerShell-sessie op uw Azure AD Connect-server met de optie **Uitvoeren als administrator.**

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`of .

3. Voer `Import-Module ADSyncDiagnostics` uit.

4. Voer de volgende cmdlet uit:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Bijvoorbeeld:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Er worden geen wachtwoorden gesynchroniseerd: handmatige stappen voor het oplossen van problemen

Volg deze stappen om te bepalen waarom er geen wachtwoorden worden gesynchroniseerd:

1. Is de [Connect-server in de faseringsmodus?](how-to-connect-sync-staging-server.md) Een server in de faseringsmodus synchroniseert geen wachtwoorden.

2. Voer het script uit in de sectie [De status van wachtwoordsynchronisatieinstellingen opdoen.](#get-the-status-of-password-sync-settings) Het geeft u een overzicht van de wachtwoordsynchronisatieconfiguratie.  

    ![PowerShell-scriptuitvoer vanuit instellingen voor wachtwoordsynchronisatie](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Als de functie niet is ingeschakeld in Azure AD of als de status van het synchronisatiekanaal niet is ingeschakeld, voert u de wizard Installatie verbinden uit. Selecteer **Synchronisatieopties aanpassen**en schakel wachtwoordsynchronisatie uit. Met deze wijziging wordt de functie tijdelijk uitgeschakeld. Voer vervolgens de wizard opnieuw uit en schakel wachtwoordsynchronisatie opnieuw in. Voer het script opnieuw uit om te controleren of de configuratie correct is.

4. Kijk in het gebeurtenislogboek naar fouten. Zoek naar de volgende gebeurtenissen, die op een probleem zouden wijzen:
    * Bron: "Directory synchronization" ID: 0, 611, 652, 655 Als u deze gebeurtenissen ziet, hebt u een verbindingsprobleem. Het gebeurtenislogboekbericht bevat forestinformatie waar u een probleem hebt. Zie [Verbindingsprobleem](#connectivity problem)voor meer informatie .

5. Als u geen hartslag ziet of als er niets anders heeft gewerkt, voert u [Trigger een volledige synchronisatie van alle wachtwoorden](#trigger-a-full-sync-of-all-passwords)uit. Voer het script slechts één keer uit.

6. Zie de sectie Problemen met één object dat geen wachtwoorden synchroniseert.

### <a name="connectivity-problems"></a>Verbindingsproblemen

Heeft u connectiviteit met Azure AD?

Heeft het account machtigingen nodig om de wachtwoordhashes in alle domeinen te lezen? Als u Connect hebt geïnstalleerd met behulp van Express-instellingen, moeten de machtigingen al correct zijn. 

Als u aangepaste installatie hebt gebruikt, stelt u de machtigingen handmatig in door het volgende te doen:
    
1. Als u het account wilt zoeken dat wordt gebruikt door de Active Directory-connector, start u **Synchronisatieservicebeheer**. 
 
2. Ga naar **Connectors**en zoek vervolgens naar het on-premises Active Directory-forest dat u probleemoplossing hebt. 
 
3. Selecteer de connector en klik op **Eigenschappen**. 
 
4. Ga naar **Verbinding maken met Active Directory Forest**.  
    
    ![Account dat wordt gebruikt door Active Directory-connector](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Let op de gebruikersnaam en het domein waar het account zich bevindt.
    
5. Start **Active Directory: gebruikers en computers**en controleer vervolgens of het account dat u eerder hebt gevonden, de follow-machtigingen heeft ingesteld bij de hoofdmap van alle domeinen in uw forest:
    * Mapwijzigingen repliceren
    * Mapwijzigingen repliceren Alles

6. Zijn de domeincontrollers bereikbaar via Azure AD Connect? Als de Connect-server geen verbinding kan maken met alle domeincontrollers, configureert u **Alleen de voorkeursdomeincontroller**.  
    
    ![Domeincontroller die wordt gebruikt door Active Directory-connector](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Ga terug naar **Synchronisatieservicebeheer** en **Mappartitie configureren**. 
 
8. Schakel uw domein in **Mappartities selecteren**in, schakel het selectievakje **Alleen het gewenste domeincontrollers gebruiken** in en klik op **Configureren**. 

9. Voer in de lijst de domeincontrollers in die Connect moet gebruiken voor wachtwoordsynchronisatie. Dezelfde lijst wordt ook gebruikt voor import en export. Doe deze stappen voor al uw domeinen.

10. Als uit het script blijkt dat er geen hartslag is, voert u het script uit in [Trigger een volledige synchronisatie van alle wachtwoorden.](#trigger-a-full-sync-of-all-passwords)

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Eén object synchroniseert geen wachtwoorden: handmatige stappen voor het oplossen van problemen

U problemen met wachtwoordhashsynchronisatie eenvoudig oplossen door de status van een object te controleren.

1. Zoek in **Active Directory Gebruikers en computers**naar de gebruiker en controleer vervolgens of het selectievakje Gebruiker bij de volgende **aanmelding wachtwoord moet wijzigen.**  

    ![Active Directory productieve wachtwoorden](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Als het selectievakje is ingeschakeld, vraagt u de gebruiker zich aan te melden en het wachtwoord te wijzigen. Tijdelijke wachtwoorden worden niet gesynchroniseerd met Azure AD.

2. Als het wachtwoord er correct uitziet in Active Directory, volgt u de gebruiker in de synchronisatieengine. Door de gebruiker te volgen van on-premises Active Directory naar Azure AD, u zien of er een beschrijvende fout op het object is.

    a. Start de [Synchronisatieservicebeheer](how-to-connect-sync-service-manager-ui.md).

    b. Klik **op Connectors**.

    c. Selecteer de **Active Directory Connector** waar de gebruiker zich bevindt.

    d. Selecteer **zoekconnectorruimte**.

    e. Selecteer **DN** **of Anchor**in het vak Scope en voer de volledige DN in van de gebruiker die u probleemoplossing oplost.

    ![Zoeken naar gebruiker in connectorruimte met DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Zoek de gewenste gebruiker en klik op **Eigenschappen** om alle kenmerken weer te geven. Als de gebruiker zich niet in het zoekresultaat bevindt, controleert u uw [filterregels](how-to-connect-sync-configure-filtering.md) en controleert u of u [Wijzigingen uitvoert en wijzigt](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) voordat de gebruiker in Verbinding wordt weergegeven.

    g. Als u de wachtwoordsynchronisatiegegevens van het object van de afgelopen week wilt bekijken, klikt u op **Aanmelden**.  

    ![Details van objectlogboeken](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Als het objectlogboek leeg is, kan Azure AD Connect de wachtwoordhash van Active Directory niet lezen. Doorgaan met het oplossen van problemen met verbindingsfouten. Als u een andere waarde dan **succes**ziet, raadpleegt u de tabel in [het logboek Wachtwoordsynchronisatie](#password-sync-log).

    h. Selecteer het **tabblad regelen** en controleer of ten minste één synchronisatieregel in de kolom **PasswordSync** **Waar**is. In de standaardconfiguratie is de naam van de synchronisatieregel **In van AD - Gebruikersaccountingeschakeld**.  

    ![Regelinformatie over een gebruiker](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klik **op Metaverse objecteigenschappen** om een lijst met gebruikerskenmerken weer te geven.  

    ![Metaverse informatie](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Controleer of er geen **cloudFiltered-kenmerk** aanwezig is. Zorg ervoor dat de domeinkenmerken (domainFQDN en domainNetBios) de verwachte waarden hebben.

    j. Klik op het tabblad **Connectors.** Controleer of er connectors voor zowel on-premises Active Directory als Azure AD worden weergegeven.

    ![Metaverse informatie](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Selecteer de rij die Azure AD vertegenwoordigt, klik op **Eigenschappen**en klik op het tabblad **Regel.** Het object connectorruimte moet een regel uitgaan in de kolom **PasswordSync** die is ingesteld op **True**. In de standaardconfiguratie is de naam van de synchronisatieregel **Uit bij AAD - Gebruiker Join**.  

    ![Dialoogvenster Eigenschappen van objectverbindingsruimte](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Logboek voor wachtwoordsynchronisatie

De statuskolom kan de volgende waarden hebben:

| Status | Beschrijving |
| --- | --- |
| Geslaagd |Wachtwoord is gesynchroniseerd. |
| FilteredByTarget |Wachtwoord is ingesteld op **Gebruiker moet wachtwoord wijzigen bij volgende aanmelding**. Wachtwoord is niet gesynchroniseerd. |
| NoTargetConnection |Geen object in de metaverse of in de Azure AD-connectorruimte. |
| SourceConnectorniet aanwezig |Er is geen object gevonden in de on-premises Active Directory-connectorruimte. |
| TargetNotExportedToDirectory TargetNotExportedToDirectory TargetNotExportedToDirectory TargetNot |Het object in de Azure AD-connectorruimte is nog niet geëxporteerd. |
| MigratedCheckDetailsForMoreInfo |Logboekinvoer is gemaakt vóór build 1.0.9125.0 en wordt weergegeven in de oude status. |
| Fout |Service heeft een onbekende fout geretourneerd. |
| Onbekend |Er is een fout opgetreden tijdens het verwerken van een batch wachtwoordhashes.  |
| Ontbrekend attribuut |Specifieke kenmerken (bijvoorbeeld Kerberos-hash) die zijn vereist door Azure AD Domain Services, zijn niet beschikbaar. |
| RetryRequestedByTarget |Specifieke kenmerken (bijvoorbeeld Kerberos-hash) die zijn vereist door Azure AD Domain Services, waren niet eerder beschikbaar. Er wordt geprobeerd om de wachtwoordhash van de gebruiker opnieuw te synchroniseren. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts om probleemoplossing te helpen

### <a name="get-the-status-of-password-sync-settings"></a>De status van instellingen voor wachtwoordsynchronisatie opdoen

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Een volledige synchronisatie van alle wachtwoorden activeren

> [!NOTE]
> Voer dit script slechts één keer uit. Als je nodig hebt om het meer dan eens uit te voeren, iets anders is het probleem. Neem contact op met Microsoft-ondersteuning om het probleem op te lossen.

U een volledige synchronisatie van alle wachtwoorden activeren met behulp van het volgende script:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Volgende stappen

* [Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](how-to-connect-password-hash-synchronization.md)
* [Synchronisatie van Azure AD Connect: synchronisatieopties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)