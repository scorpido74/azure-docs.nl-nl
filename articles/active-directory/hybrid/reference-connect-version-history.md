---
title: 'Azure AD Connect: releasegeschiedenis van versies | Microsoft Documenten'
description: In dit artikel worden alle releases van Azure AD Connect en Azure AD Sync weergegeven
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60dca30496aa17ad7add00ca4eca914cb58fddd7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655985"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: releasegeschiedenis versie
Het Azure Active Directory-team (Azure AD) werkt Azure AD Connect regelmatig bij met nieuwe functies en functionaliteit. Niet alle toevoegingen zijn van toepassing op alle doelgroepen.

Dit artikel is ontworpen om u te helpen bij te houden van de versies die zijn vrijgegeven, en om te begrijpen wat de wijzigingen zijn in de nieuwste versie.

Deze tabel is een lijst met gerelateerde onderwerpen:

Onderwerp |  Details
--------- | --------- |
Stappen om te upgraden vanuit Azure AD Connect | Verschillende methoden om te [upgraden van een vorige versie naar de nieuwste](how-to-upgrade-previous-version.md) Azure AD Connect-release.
Vereiste machtigingen | Zie [accounts en machtigingen](reference-connect-accounts-permissions.md#upgrade)voor machtigingen die nodig zijn om een update toe te passen.
Download| [Azure AD Connect downloaden](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Het vrijgeven van een nieuwe versie van Azure AD Connect is een proces dat verschillende kwaliteitscontrolestappen vereist om de werkingsfunctionaliteit van de service te garanderen, en terwijl we dit proces doorlopen, wordt het versienummer van een nieuwe versie en de releasestatus bijgewerkt om de meest recente status weer te geven.
Terwijl we dit proces doorlopen, wordt het versienummer van de release weergegeven met een "X" in de kleine releasenummerpositie, zoals in "1.3.X.0" - dit geeft aan dat de releasenotes in dit document geldig zijn voor alle versies die beginnen met "1.3". Zodra wij het versieproces hebben gefinaliseerd zal het versienummer van de versie aan de onlangs vrijgegeven versie worden bijgewerkt en de versiestatus zal worden bijgewerkt aan "Vrijgegeven voor download en autoverbetering".
Niet alle releases van Azure AD Connect worden beschikbaar gesteld voor een automatische upgrade. De releasestatus geeft aan of een release beschikbaar is voor automatische upgrade of alleen voor download. Als de automatische upgrade is ingeschakeld op uw Azure AD Connect-server, wordt deze server automatisch geüpdatet naar de nieuwste versie van Azure AD Connect die is uitgebracht voor automatische upgrade. Houd er rekening mee dat niet alle Azure AD Connect-configuraties in aanmerking komen voor een automatische upgrade. Volg deze link om meer te lezen over [automatische upgrade](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Vanaf 1 november 2020 starten we met de implementatie van een afschrijvingsproces waarbij versies van Azure AD Connect die meer dan 18 maanden geleden zijn uitgebracht, worden afgeschaft. Op dat moment beginnen we met dit proces door alle releases van Azure AD Connect met versie 1.3.20.0 (die werd uitgebracht op 4/24/2019) en ouder te verwijderen, en we zullen doorgaan met het evalueren van de afschrijving van oudere versies van Azure AD Connect elke keer dat er een nieuwe versie wordt uitgebracht.
>
> U moet ervoor zorgen dat u een recente versie van Azure AD Connect uitvoert om een optimale ondersteuningservaring te ontvangen. 
>
>Als u een afgeschafte versie van Azure AD Connect uitvoert, beschikt u mogelijk niet over de nieuwste beveiligingsoplossingen, prestatieverbeteringen, probleemoplossing en diagnostische hulpprogramma's en serviceverbeteringen, en als u ondersteuning nodig hebt, kunnen we u mogelijk niet het serviceniveau bieden dat uw organisatie nodig heeft.
>
>Als u Azure AD Connect hebt ingeschakeld voor synchronisatie, ontvangt u binnenkort automatisch statusmeldingen die u waarschuwen voor aankomende afschrijvingen wanneer u een van de oudere versies uitvoert.
>
>Raadpleeg [dit artikel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version) voor meer informatie over het upgraden van Azure AD Connect naar de nieuwste versie.


## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Status van de release
04/02/2020: Vrijgegeven voor download

### <a name="functional-changes-adsyncautoupgrade"></a>Functionele wijzigingen ADSyncAutoUpgrade 

- Ondersteuning toegevoegd voor de mS-DS-ConsistencyGuid-functie voor groepsobjecten. Hiermee u groepen verplaatsen tussen forests of groepen opnieuw verbinden in AD naar Azure AD, waar de AD-groepsobjectID is gewijzigd, bijvoorbeeld wanneer een AD-server na een calamiteit opnieuw wordt opgebouwd. Zie Groepen [verplaatsen tussen bossen voor](how-to-connect-migrate-groups.md)meer informatie.
- Het kenmerk mS-DS-ConsistencyGuid wordt automatisch ingesteld op alle gesynchroniseerde groepen en u hoeft niets te doen om deze functie in te schakelen. 
- Verwijderd van de Get-ADSyncRunProfile omdat deze niet meer in gebruik is. 
- Wijzigde de waarschuwing die u ziet wanneer u een ondernemingsbeheerder- of domeinbeheerdersaccount probeert te gebruiken voor het AD DS-connectoraccount om meer context te bieden. 
- Een nieuwe cmdlet toegevoegd om objecten uit de connectorruimte te verwijderen, wordt het oude gereedschap CSDelete.exe verwijderd en wordt vervangen door de nieuwe cmdlet Remove-ADSyncCSObject. De cmdlet Remove-ADSyncCSObject neemt een CsObject als invoer. Dit object kan worden opgehaald met de cmdlet Get-ADSyncCSObject.

>[!NOTE]
>Het oude gereedschap CSDelete.exe is verwijderd en vervangen door de nieuwe cmdlet Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Problemen opgelost

- Fixed a bug in the group writeback forest/OU selector on rerunning the Azure AD Connect wizard after disablof the feature. 
- Introduceerde een nieuwe foutpagina die wordt weergegeven als de vereiste DCOM-registerwaarden ontbreken met een nieuwe helpkoppeling. Informatie is ook geschreven om bestanden te loggen. 
- Fixed an issue with the creation of the Azure Active Directory synchronization account where enabling Directory Extensions or PHS may fail because the account has not propagated across all service replicas before attempted use. 
- Fixed a bug in the sync errors compression utility that was not handling surrogate characters correctly. 
- Fixed a bug in the auto upgrade which left the server in the scheduler suspended state. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Status van de release
12-9-2019: Release te downloaden. Niet beschikbaar via automatische upgrade.
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
- We hebben Password Hash Sync voor Azure AD Domain Services bijgewerkt om goed rekening te houden met opvulling in Kerberos hashes.  Dit biedt een prestatieverbetering tijdens wachtwoordsynchronisatie van AAD naar Azure AD Domain Services.
- We hebben ondersteuning toegevoegd voor betrouwbare sessies tussen de verificatieagent en de servicebus.
- Deze release dwingt TLS 1.2 af voor communicatie tussen authenticatieagent en cloudservices.
- We hebben een DNS-cache toegevoegd voor websocketverbindingen tussen verificatieagent en cloudservices.
- We hebben de mogelijkheid toegevoegd om specifieke agent van de cloud te targeten om te testen op agentconnectiviteit.

### <a name="fixed-issues"></a>Problemen opgelost
- Release 1.4.18.0 had een bug waar de PowerShell cmdlet voor DSSO was met behulp van de login windows geloofsbrieven in plaats van de admin referenties die tijdens het uitvoeren van ps. Hierdoor was het niet mogelijk om DSSO in meerdere forest's in te schakelen via de AADConnect-gebruikersinterface. 
- Er is een oplossing gemaakt om DSSO gelijktijdig in alle bossen in te schakelen via de AADConnect-gebruikersinterface

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Status van de release
11-08-2019: Vrijgegeven voor download. Niet beschikbaar via automatische upgrade.

>[!IMPORTANT]
>Als u een interne schemawijziging in deze versie van Azure AD Connect hebt, moet u uw MSOnline PowerShell-module bijwerken naar versie 1.1.183.57 of hoger als u de configuratievan ad FS-vertrouwensrelatie beheert met MSOnline PowerShell.

### <a name="fixed-issues"></a>Problemen opgelost

Met deze versie wordt een probleem opgelost met bestaande hybride Azure AD-apparaten. Deze release bevat een nieuwe synchronisatieregel voor apparaten die dit probleem corrigeert.
Houd er rekening mee dat deze regelwijziging kan leiden tot het verwijderen van verouderde apparaten uit Azure AD. Dit is geen reden tot bezorgdheid, omdat deze apparaatobjecten niet worden gebruikt door Azure AD tijdens de voorwaardelijke toegangsautorisatie. Voor sommige klanten kan het aantal apparaten dat door deze regelwijziging wordt verwijderd, de verwijderingsdrempel overschrijden. Als u het verwijderen van apparaatobjecten in Azure AD de exportverwijderingsdrempel ziet overschrijden, wordt geadviseerd de verwijderingsdrempel door te laten gaan. [Verwijderen laten stromen wanneer ze de verwijderingsdrempel overschrijden](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Status van de release
9/28/2019: Uitgebracht voor automatische upgrade om huurders te selecteren. Niet beschikbaar om te downloaden.

Deze versie lost een bug op waarbij sommige servers die automatisch zijn geüpgraded van een vorige versie naar 1.4.18.0 en problemen ondervonden met Self-service wachtwoordreset (SSPR) en Password Writeback.

### <a name="fixed-issues"></a>Problemen opgelost

Onder bepaalde omstandigheden hebben servers die automatisch zijn geüpgraded naar versie 1.4.18.0, selfservicewachtwoordreset en Wachtwoordwriteback niet opnieuw ingeschakeld nadat de upgrade was voltooid. Deze automatische upgrade release lost dat probleem op en maakt self-service wachtwoordreset en Password Writeback opnieuw in.

We hebben een bug in de synchronisatie fouten compressie hulpprogramma dat niet was surrogaat tekens correct omgaan.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>We onderzoeken een incident waarbij sommige klanten een probleem ondervinden met bestaande hybride Azure AD-apparaten na een upgrade naar deze versie van Azure AD Connect. We adviseren klanten die Hybrid Azure AD hebben geïmplementeerd om de upgrade naar deze versie uit te stellen totdat de hoofdoorzaak van deze problemen volledig is begrepen en beperkt. Meer informatie zal zo snel mogelijk worden verstrekt.

>[!IMPORTANT]
>Met deze versie van Azure AD Connect zien sommige klanten mogelijk sommige of al hun Windows-apparaten uit Azure AD verdwijnen. Dit is geen reden tot bezorgdheid, omdat deze apparaatidentiteiten niet worden gebruikt door Azure AD tijdens de voorwaardelijke toegangsautorisatie. Zie Azure [AD Connect 1.4.xx.x-apparaat verdwijnen](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Status van de release
9/25-2019: Alleen uitgebracht voor auto-upgrade.

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
- Nieuwe hulpprogramma's voor het oplossen van problemen met de problemen met de problemen met 'gebruiker niet synchroniseren', 'groepniet synchroniseren' of 'groepslid niet synchroniseren'-scenario's.
- Ondersteuning voor nationale clouds toevoegen in aad connect-probleemoplossingsscript 
- Klanten moeten ervan op de hoogte worden gesteld dat de afgeschafte WMI-eindpunten voor MIIS_Service nu zijn verwijderd. Elke WMI-bewerking moet nu worden gedaan via PS-cmdlets.
- Verbetering van de beveiliging door beperkte delegatie op AZUREADSSOACC-object opnieuw in te stellen
- Bij het toevoegen/bewerken van een synchronisatieregel, als er kenmerken zijn die in de regel worden gebruikt die in het connectorschema staan maar niet aan de connector zijn toegevoegd, worden de kenmerken automatisch aan de connector toegevoegd. Hetzelfde geldt voor het objecttype dat de regel beïnvloedt. Als er iets aan de connector wordt toegevoegd, wordt de connector gemarkeerd voor volledige import op de volgende synchronisatiecyclus.
- Een Enterprise- of domeinbeheerder gebruiken als connectoraccount wordt niet langer ondersteund in nieuwe Azure AD Connect-implementaties. Huidige AAD Connect-implementaties met behulp van een Enterprise- of Domeinbeheerder als connectoraccount worden niet beïnvloed door deze release.
- In Synchronisatiebeheer wordt een volledige synchronisatie uitgevoerd op het maken/bewerken/verwijderen van regels. Er verschijnt een pop-up op elke regelwijziging die de gebruiker op de hoogte stelt als volledige import of volledige synchronisatie wordt uitgevoerd.
- Extra mitigatiestappen voor wachtwoordfouten naar pagina 'connectors > eigenschappen > connectiviteit'
- Added a deprecation warning for the sync service manager on the connector properties page. Met deze waarschuwing wordt de gebruiker gewaarschuwd dat wijzigingen moeten worden aangebracht via de wizard Azure AD Connect.
- Nieuwe fout toegevoegd voor problemen met het wachtwoordbeleid van een gebruiker.
- Voorkom een verkeerde configuratie van groepsfiltering op domein- en ou-filters. Groepsfiltering geeft een fout weer wanneer het domein/de vk van de ingevoerde groep al is uitgefilterd en de gebruiker niet verder kan gaan totdat het probleem is opgelost.
- Gebruikers kunnen niet langer een connector maken voor Active Directory Domain Services of Windows Azure Active Directory in de gebruikersinterface van Synchronization Service Manager.
- Vaste toegankelijkheid van aangepaste UI-besturingselementen in de synchronisatieservicebeheer.
- Zes federatiebeheertaken ingeschakeld voor alle aanmeldingsmethoden in Azure AD Connect.  (Voorheen was alleen de taak 'AD FS TLS/SSL-certificaat bijwerken' beschikbaar voor alle aanmeldingen.)
- Added a warning when changing the sign-in method from federation to PHS or PTA that all Azure AD domains and users will be converted to managed authentication.
- Verwijderde certificaten voor tokenondertekening uit de taak Azure AD en AD FS-vertrouwensrelatie opnieuw instellen en een afzonderlijke subtaak toegevoegd om deze certificaten bij te werken.
- Added a new federation management task called "Manage certificates" which has sub-tasks to update the TLS or token-signing certificates for the AD FS farm.
- Added a new federation management sub-task called "Specify primary server" which allows administrators to specify a new primary server for the AD FS farm.
- Added a new federation management task called "Manage servers" which has sub-tasks to deploy an AD FS server, deploy a Web Application Proxy server, and specify primary server.
- Added a new federation management task called "View federation configuration" that displays the current AD FS settings.  (Vanwege deze toevoeging zijn ad FS-instellingen verwijderd van de pagina 'Uw oplossing controleren'.)

### <a name="fixed-issues"></a>Problemen opgelost
- Resolved sync error issue for the scenario where a user object taking over its corresponding contact object has a self-reference (for.v. user is their own manager).
- Help pop-ups nu weer te geven op toetsenbord focus.
- Voor auto-upgrade, als er een conflicterende app wordt uitgevoerd vanaf 6 uur, doden en doorgaan met de upgrade.
- Beperk het aantal kenmerken dat een klant kan selecteren tot 100 per object bij het selecteren van directory-extensies. Dit voorkomt dat de fout optreedt tijdens het exporteren, omdat Azure maximaal 100 extensiekenmerken per object heeft.
- Een bug opgelost om het AD Connectivity-script robuuster te maken
- Fixed a bug to make AADConnect install on a machine using an existing Named Pipes WCF service more robust.
- Verbeterde diagnose en probleemoplossing rond groepsbeleid waardoor de ADSync-service niet kan worden gestart wanneer deze in eerste instantie is geïnstalleerd.
- Fixed a bug where display name for a Windows computer was correct written.
- Los een bug op waarbij het type BE voor een Windows-computer verkeerd is geschreven.
- Fixed a bug where non-Windows 10 computers were syncing unexpectedly. Houd er rekening mee dat het effect van deze wijziging is dat niet-Windows-10-computers die eerder zijn gesynchroniseerd, nu worden verwijderd. Dit heeft geen invloed op alle functies, omdat de synchronisatie van Windows-computers alleen wordt gebruikt voor hybride Azure AD-domeinjoin, dat alleen werkt voor Windows-10-apparaten.
- Verschillende nieuwe (interne) cmdlets toegevoegd aan de ADSync PowerShell module.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Er is een bekend probleem met het upgraden van Azure AD Connect van een eerdere versie naar 1.3.21.0, waarbij de O365-portal de bijgewerkte versie niet weergeeft, ook al is Azure AD Connect geüpgraded.
>
> Om dit op te lossen moet u de`Set-ADSyncDirSyncConfiguration` **AdSync-module** importeren en vervolgens de PowerShell-cmdlet uitvoeren op de Azure AD Connect-server.  U de volgende stappen gebruiken:
>
>1. Open PowerShell in de beheermodus.
>2. Voer `Import-Module "ADSync"` uit.
>3. Voer `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` uit.
 
### <a name="release-status"></a>Status van de release 

05/14/2019: Vrij gegeven voor download

### <a name="fixed-issues"></a>Problemen opgelost 

- Fixed an elevation of privilege vulnerability that exists in Microsoft Azure Active Directory Connect build 1.3.20.0.  Dit beveiligingslek kan een aanvaller onder bepaalde voorwaarden in staat stellen twee PowerShell-cmdlets uit te voeren in de context van een geprivilegieerd account en bevoorrechte acties uit te voeren.  Deze beveiligingsupdate verhelpt het probleem door deze cmdlets uit te schakelen. Zie [beveiligingsupdate](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)voor meer informatie.

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Status van de release 

04/24/2019: Vrij gegeven voor download

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 

- Ondersteuning toevoegen voor domeinvernieuwen 
- Exchange Mail Public Folders functie gaat GA 
- Foutafhandeling van de wizard verbeteren voor servicefouten 
- De waarschuwingskoppeling toegevoegd op de gebruikersinterface van Synchronisatieservicebeheer op de pagina met verbindingseigenschappen. 
- De Unified Groups Writeback-functie is nu GA 
- Improved SSPR error message when the DC is missing an LDAP control Improved SSPR error message when the DC is missing a LDAP control Improved SSPR error message when the DC is missing a LDAP control Improved S 
- Toegevoegd diagnostiek voor DCOM registerfouten tijdens de installatie  
- Verbeterde tracering van PHS RPC-fouten 
- EA-creds toestaan vanuit een onderliggend domein 
- Databasenaam toestaan tijdens de installatie (standaardnaam ADSync)
- Upgraden naar ADAL 3.19.8 om een WS-Trust-oplossing voor ping op te halen en ondersteuning toe te voegen voor nieuwe Azure-exemplaren 
- Groepssynchronisatieregels wijzigen om samAccountName, DomainNetbios en DomainFQDN naar de cloud te laten stromen - die nodig zijn voor claims 
- Gewijzigd Standaard Sync Rule Handling - lees [hier](how-to-connect-fix-default-rules.md)meer .
- Een nieuwe agent toegevoegd die als Windows-service wordt uitgevoerd. Deze agent, genaamd 'Admin Agent', maakt diepere externe diagnose svan de Azure AD Connect-server mogelijk om Microsoft Engineers te helpen problemen op te lossen wanneer u een ondersteuningsaanvraag opent. Deze agent is standaard niet geïnstalleerd en ingeschakeld.  Zie Wat is de Azure AD [Connect-beheeragent voor](whatis-aadc-admin-agent.md)meer informatie over het installeren en inschakelen van de agent? . 
- De licentieovereenkomst voor eindgebruikers bijgewerkt (EULA) 
- Ondersteuning voor automatische upgrade toegevoegd voor implementaties die AD FS als aanmeldingstype gebruiken.  Hierdoor is ook de vereiste verwijderd om de AD FS Azure AD Relying Party Trust bij te werken als onderdeel van het upgradeproces. 
- Added an Azure AD trust management task that provides two options: analyze/update trust and reset trust. 
- Het vertrouwensgedrag ad FS Azure AD Relying Party is zo gewijzigd dat het altijd de -SupportMultipleDomain-switch gebruikt (inclusief vertrouwens- en Azure AD-domeinupdates). 
- Het nieuwe AD FS-farmgedrag hebben gewijzigd, zodat er een .pfx-certificaat voor nodig is door de optie voor het gebruik van een vooraf geïnstalleerd certificaat te verwijderen.
- De installatie van de nieuwe AD FS-farmworkflow bijgewerkt, zodat er alleen 1 AD FS- en 1 WAP-server kan worden geïmplementeerd.  Alle extra servers worden gedaan na de eerste installatie. 

### <a name="fixed-issues"></a>Problemen opgelost 

- De SQL-reconnect-logica voor ADSync-service oplossen 
- Oplossing om schone installatie mogelijk te maken met een lege SQL AOA DB 
- Script voor PS-machtigingen herstellen om GWB-machtigingen te verfijnen 
- VSS-fouten oplossen met LocalDB  
- Misleidende foutbericht oplossen wanneer objecttype niet binnen het bereik valt 
- Een probleem verholpen waarbij de installatie van Azure AD PowerShell op een server mogelijk een conflict met Azure AD Connect kan veroorzaken. 
- Fixed PHS bug on Staging Server when Connector Credentials are updated in the Synchronization Service Manager UI. 
- Een aantal geheugenlekken opgelost 
- Diverse oplossingen voor auto-upgrade 
- Diverse oplossingen voor export en niet-bevestigde importverwerking 
- Een bug opgelost met het verwerken van een backslash in domein- en ou-filtering 
- Probleem opgelost waarbij de ADSync-service meer dan 2 minuten duurt om te stoppen en een probleem veroorzaakt tijdens het upgraden. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Status van de release

12/18/2018: Vrijgegeven om te downloaden

### <a name="fixed-issues"></a>Problemen opgelost

Met deze build worden de niet-standaard connectors (bijvoorbeeld Generieke LDAP-connector en Generieke SQL Connector) bijgewerkt die worden geleverd met Azure AD Connect. Zie versie 1.1.911.0 in de releasegeschiedenis van [connectorversie voor](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history)meer informatie over toepasselijke connectors.


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Status van de release
12/11/2018: Vrijgegeven om te downloaden

### <a name="fixed-issues"></a>Problemen opgelost
Met deze hotfix-build kan de gebruiker binnen het opgegeven forest een doeldomein selecteren voor de container RegisteredDevices wanneer het apparaat opnieuw kan worden teruggeschreven.  In de vorige versies die de nieuwe functionaliteit voor apparaatopties bevatten (1.1.819.0 – 1.2.68.0) was de containerlocatie RegisteredDevices beperkt tot de forestroot en stond de onderliggende domeinen niet toe.  Deze beperking manifesteerde zich alleen op nieuwe implementaties - in-place upgrades werden niet beïnvloed.  

Als een build met de bijgewerkte functionaliteit Apparaatopties is geïmplementeerd op een nieuwe server en de schrijfterugvan het apparaat is ingeschakeld, moet u handmatig de locatie van de container opgeven als u deze niet in de forestroot wilt hebben.  Om dit te doen, moet u het terugschrijven van apparaten uitschakelen en opnieuw inschakelen, zodat u de containerlocatie opgeven op de pagina 'Terugschrijfbos'.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Status van de release 

11/30/2018: Vrijgegeven om te downloaden

### <a name="fixed-issues"></a>Problemen opgelost

Deze hotfix-build lost een conflict op waarbij een verificatiefout kan optreden als gevolg van de onafhankelijke aanwezigheid van de MSOnline PowerShell Gallery-module op de synchronisatieserver.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Status van de release 

11/19/2018: Vrijgegeven om te downloaden

### <a name="fixed-issues"></a>Problemen opgelost

Deze hotfix-build lost een regressie op in de vorige build waarbij Password Writeback mislukt wanneer u een domeincontroller van VOEGT op Windows Server 2008/R2 gebruikt.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Status van de release 

10/25/2018: vrijgegeven voor download

 
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 


- Wijzigde de functionaliteit van het terugschrijven van kenmerken om ervoor te zorgen dat gehoste voicemail werkt zoals verwacht.  Onder bepaalde scenario's overschreef Azure AD het kenmerk msExchUcVoicemailSettings tijdens het terugschrijven met een null-waarde.  Azure AD geeft nu niet langer de on-premises waarde van dit kenmerk vrij als de cloudwaarde niet is ingesteld.
- Diagnostics toegevoegd in de wizard Azure AD Connect om verbindingsproblemen te onderzoeken en te identificeren met Azure AD. Dezelfde diagnostiek kan ook rechtstreeks via PowerShell worden uitgevoerd met behulp van de Test-AdSyncAzureServiceConnectivity Cmdlet. 
- Ondersteuning toegevoegd in de wizard Azure AD Connect om verbindingsproblemen aan AD te onderzoeken en te identificeren. Dezelfde diagnostiek kan ook rechtstreeks via PowerShell worden uitgevoerd met de functie Start-ConnectivityValidation in de ADConnectivityTools PowerShell-module.  Zie Wat [is de ADConnectivityTool PowerShell Module voor](how-to-connect-adconnectivitytools.md) meer informatie?
- Een AD-schemaversie toegevoegd voorcontrole voor Hybride Azure Active Directory Join en apparaatterugschrijven 
- De zoekfunctie directoryextensiepagina is niet-hoofdlettergevoelig.
-   Volledige ondersteuning toegevoegd voor TLS 1.2. Deze release ondersteunt alle andere protocollen die worden uitgeschakeld en alleen TLS 1.2 wordt ingeschakeld op de machine waar Azure AD Connect is geïnstalleerd.  Zie TLS [1.2-handhaving voor Azure AD Connect voor](reference-connect-tls-enforcement.md) meer informatie

 

### <a name="fixed-issues"></a>Problemen opgelost   

- Fixed a bug where Azure AD Connect Upgrade would fail if SQL Always On was being used. 
- Fixed a bug to correct parse OU names that contain a forward slash. 
- Fixed an issue where Pass-Through Authentication would be disabled for a clean install in staging mode. 
- Een bug opgelost waardoor de PowerShell-module niet kon worden geladen tijdens het uitvoeren van de hulpprogramma's voor probleemoplossing 
- Fixed a bug that would block customers from using numeric values in the first character of a host name. 
- Een bug opgelost waarbij Azure AD Connect ongeldige partities en containerselectie mogelijk zou maken 
- Fixed the "Invalid Password" error message when Desktop SSO is enabled. 
- Verschillende bugfixes voor AD FS Trust Management  
- Bij het configureren van Device Writeback - fixed the schema check to look for the msDs-DeviceContainer object class (geïntroduceerd op WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

9/7/2018: vrijgegeven voor download, zal niet worden vrijgegeven voor automatische upgrade 

### <a name="fixed-issues"></a>Problemen opgelost  

Azure AD Connect-upgrade mislukt als SQL Always On Availability is geconfigureerd voor de ADSync DB. Deze hotfix lost dit probleem op en stelt Upgrade in staat om te slagen. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Status van de release

8/21/2018: Vrijgegeven voor download en automatische upgrade. 

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

- De Ping Federate-integratie in Azure AD Connect is nu beschikbaar voor algemene beschikbaarheid. [Meer informatie over hoe je Azure AD castreren met Ping Federate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Azure AD Connect maakt nu de back-up van Azure AD-vertrouwensrelatie in AD FS elke keer dat een update wordt uitgevoerd en wordt deze opgeslagen in een apart bestand voor eenvoudig herstel indien nodig. [Meer informatie over de nieuwe functionaliteit en Azure AD-vertrouwensbeheer in Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Nieuwe hulpprogramma's voor het oplossen van problemen helpen bij het oplossen van het wijzigen van primaire e-mailadres en het verbergen van account uit de algemene adreslijst
- Azure AD Connect is bijgewerkt met de nieuwste SQL Server 2012 Native Client
- Wanneer u zich aanmeldt voor wachtwoordhashsynchronisatie of doorgeefverificatie in de taak 'Aanmelden voor gebruikers wijzigen', is het selectievakje Naadloos eenmalig aanmelden standaard ingeschakeld.
- Ondersteuning toegevoegd voor Windows Server Essentials 2019
- De Azure AD Connect Health-agent is bijgewerkt naar de nieuwste versie 3.1.7.0
- Als het installatieprogramma tijdens een upgrade wijzigingen in de standaardsynchronisatieregels detecteert, wordt de beheerder gevraagd om een waarschuwing voordat de gewijzigde regels worden overschreven. Hierdoor kan de gebruiker corrigerende maatregelen nemen en later hervatten. Oud gedrag: Als er een gewijzigde out-of-box regel was, dan was handmatige upgrade het overschrijven van deze regels zonder enige waarschuwing aan de gebruiker te geven en synchronisatieplanner werd uitgeschakeld zonder de gebruiker te informeren. Nieuw gedrag: de gebruiker wordt gewaarschuwd voordat de gewijzigde out-of-box synchronisatieregels worden overschreven. De gebruiker heeft de keuze om het upgradeproces te stoppen en later te hervatten na het nemen van corrigerende maatregelen.
- Zorg voor een betere afhandeling van een FIPS-nalevingsprobleem, met een foutmelding voor md5-hashgeneratie in een FIPS-compatibele omgeving en een koppeling naar documentatie die een oplossing biedt voor dit probleem.
- UI-update om federatietaken in de wizard te verbeteren, die nu onder een afzonderlijke subgroep voor federatie staan. 
- Alle extra taken van de federatie worden nu gegroepeerd onder één submenu voor gebruiksgemak.
- Een nieuwe vernieuwde ADSyncConfig Posh Module (AdSyncConfig.psm1) met nieuwe AD Permissions functies verplaatst van de oude ADSyncPrep.psm1 (die binnenkort kan worden afgeschaft)

### <a name="fixed-issues"></a>Problemen opgelost 

- Fixed a bug where the AAD Connect server would show high CPU usage after upgrading to .NET 4.7.2 Fixed a bug where the AAD Connect server would show high CPU usage after upgrading to .NET 4.7.2 Fixed a bug where the AAD Connect server would show high CPU usage after upgrading to .NET 4.7.2 Fixed a
- Een bug opgelost die met tussenpozen een foutbericht zou produceren voor een automatisch opgelost SQL-impasseprobleem
- Verschillende toegankelijkheidsproblemen opgelost voor de synchronisatieregelseditor en synchronisatieservicebeheer  
- Een bug opgelost waarbij Azure AD Connect geen informatie over de registerinstelling kan krijgen
- Een bug opgelost die problemen heeft veroorzaakt wanneer de gebruiker vooruit/terug gaat in de wizard
- Fixed a bug to prevent an error happening due to incorrect multi-thread handing in the wizard Fixed a bug to prevent an error happening due to incorrect multi-thread handing in the wizard Fixed a bug to prevent an error happening due to incorrect multi-thread handing in the wizard Fixed a
- Wanneer pagina Voor het filteren van groepssynchronisatie een LDAP-fout optreedt bij het oplossen van beveiligingsgroepen, retourneert Azure AD Connect nu de uitzondering met volledige getrouwheid.  De hoofdoorzaak voor de verwijzingsuitzondering is nog onbekend en zal door een andere bug worden aangepakt.
-  Fixed a bug where permissions for STK and NGC keys (ms-DS-KeyCredentialLink attribute on User/Device objects for WHfB) were not correctly set.     
- Een bug verholpen waarbij 'Set-ADSyncRestrictedPermissions' niet correct werd aangeroepen
-  Ondersteuning toevoegen voor het verlenen van machtigingen op Groepsterugschrijven in de installatiewizard van AADConnect
- Bij het wijzigen van de aanmeldingsmethode van Password Hash Sync naar AD FS is Password Hash Sync niet uitgeschakeld.
- Verificatie toegevoegd voor IPv6-adressen in AD FS-configuratie
- Het meldingsbericht bijgewerkt om te laten weten dat er een bestaande configuratie bestaat.
- Apparaatterugschrijven detecteert geen container in niet-vertrouwd forest. Dit is bijgewerkt om een betere foutmelding en een link naar de juiste documentatie te bieden
- Als u een organisatie-eenheid uitschakelt en vervolgens synchronisatie/writeback die overeenkomt met die organisatie-eenheid, wordt een algemene synchronisatiefout weergegeven. Dit is gewijzigd om een begrijpelijker foutbericht te maken.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Status van de release

5/14/2018: Uitgebracht voor automatische upgrade en download.

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

Nieuwe functies en verbeteringen

- Deze release bevat de openbare preview van de integratie van PingFederate in Azure AD Connect. Met deze release kunnen klanten eenvoudig en betrouwbaar hun Azure Active Directory-omgeving configureren om PingFederate als hun federatieprovider te gebruiken. Ga voor meer informatie over het gebruik van deze nieuwe functie naar onze [online documentatie.](plan-connect-user-signin.md#federation-with-pingfederate) 
- Bijgewerkt de Azure AD Connect Wizard Troubleshooting Utility, waar het nu analyseert meer fout scenario's, zoals Gekoppelde postvakken en AD Dynamische Groepen. Lees hier meer over het hulpprogramma [voor](tshoot-connect-objectsync.md)het oplossen van problemen.
- De configuratie van device Writeback wordt nu alleen beheerd in de wizard Azure AD Connect.
- Er wordt een nieuwe PowerShell-module met de naam ADSyncTools.psm1 toegevoegd die kan worden gebruikt om SQL Connectivity-problemen en diverse andere hulpprogramma's voor het oplossen van problemen op te lossen. Lees [hier](tshoot-connect-tshoot-sql-connectivity.md)meer over de ADSyncTools module . 
- Er is een nieuwe extra taak 'Apparaatopties configureren' toegevoegd. U de taak gebruiken om de volgende twee bewerkingen te configureren: 
  - **Hybride Azure AD join:** Als uw omgeving een on-premises AD-voetafdruk heeft en u ook wilt profiteren van de mogelijkheden die worden geboden door Azure Active Directory, u hybride Azure AD-apparaten implementeren. Dit zijn apparaten die zowel aan uw on-premises Active Directory als aan uw Azure Active Directory zijn gekoppeld.
  - **Apparaatterugschrijven**: Apparaatterugschrijven wordt gebruikt om voorwaardelijke toegang in te schakelen op basis van apparaten met beveiligde APPARATEN (2012 R2 of hoger)

    >[!NOTE] 
    > - De optie om apparaatterugschrijftijd in te schakelen vanuit synchronisatieopties aanpassen, wordt grijs weergegeven. 
    > -  De PowerShell-module voor ADPrep is afgeschaft met deze release.



### <a name="fixed-issues"></a>Problemen opgelost 

- Deze release werkt de SQL Server Express-installatie bij naar SQL Server 2012 SP4, die onder andere oplossingen biedt voor verschillende beveiligingsproblemen.  Zie [hier](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) voor meer informatie over SQL Server 2012 SP4.
- Synchronisatieregelverwerking: uitgaande joinsynchronisatieregels zonder joinvoorwaarde moeten worden uitgeschakeld als de bovenliggende synchronisatieregel niet langer van toepassing is
- Er zijn verschillende toegankelijkheidsoplossingen toegepast op de gebruikersinterface van Synchronisatieservicebeheerbeheer en de editor voor synchronisatieregels
- Wizard Azure AD Connect: fout bij het maken van EEN AD Connector-account wanneer Azure AD Connect zich in een werkgroep bevindt
- Wizard Azure AD Connect: geef op de aanmeldingspagina van Azure AD het selectievakje Verificatie weer wanneer er een mismatch is in AD-domeinen en azure AD-geverifieerde domeinen
- PowerShell-oplossing voor automatische upgrade om de status van automatische upgrade correct in te stellen in bepaalde gevallen nadat de automatische upgrade is geprobeerd.
- Wizard Azure AD Connect: bijgewerkte telemetrie om eerder ontbrekende informatie vast te leggen
- Wizard Azure AD Connect: de volgende wijzigingen zijn aangebracht wanneer u de **aanmeldingstaak van** de gebruiker wijzigen gebruikt om over te schakelen van AD FS naar Verificatie doorstaan:
    - De Pass-through Authentication Agent is geïnstalleerd op de Azure AD Connect-server en de functie Doorgeefverificatie is ingeschakeld voordat we domein(en) converteren van federatief naar beheerd.
    - Gebruikers worden niet langer geconverteerd van federatief naar beheerd. Alleen domein(en) worden geconverteerd.
- Wizard Azure AD Connect: REGex voor meerdere domeinen AD FS is niet correct wanneer de regex-update van het speciale teken van de gebruiker ' heeft om speciale tekens te ondersteunen
- Wizard Azure AD Connect: bericht 'Bronankerkenmerk configureren' verwijderen wanneer er geen wijziging wordt gewijzigd 
- Wizard Azure AD Connect: AD FS-ondersteuning voor het scenario voor dubbele federatie
- Wizard Azure AD Connect: AD FS-claims worden niet bijgewerkt voor toegevoegd domein bij het converteren van een beheerd domein naar federatief
- Wizard Azure AD Connect: Tijdens de detectie van geïnstalleerde pakketten vinden we verouderde Dirsync/Azure AD Sync/Azure AD Connect-gerelateerde producten. We zullen nu proberen om de verouderde producten te verwijderen.
- Wizard Azure AD Connect: foutberichttoewijzing corrigeren wanneer de installatie van de verificatieagent voor doorgeefgegevens mislukt
- Wizard Azure AD Connect: container 'Configuratie' verwijderd van pagina Domein-OU-filtering
- Sync Engine installeren: verwijder onnodige legacy logica die af en toe mislukt van Sync Engine installeren msi
- Wizard Azure AD Connect: Popup-help-tekst herstellen op de pagina Optionele onderdelen voor wachtwoordhashsynchronisatie
- Runtime van synchronisatieengine: los het scenario op waarin een CS-object een geïmporteerde delete heeft en Synchronisatieregels proberen het object opnieuw in te richten.
- Runtime van Synchronisatie-engine: Help-koppeling toevoegen voor online handleiding voor het oplossen van problemen met de verbinding toevoegen aan het gebeurtenislogboek voor een importfout
- Runtime van de synchronisatieengine: minder geheugengebruik van syncscheduler bij het opsommen van connectors
- Wizard Azure AD Connect: een probleem oplossen bij het oplossen van een aangepast synchronisatieserviceaccount zonder AD-leesrechten
- Wizard Azure AD Connect: logboekregistratie van selecties voor domein- en organisatie-gegevens verbeteren
- Wizard Azure AD Connect: AD FS Standaardclaims toevoegen aan federatievertrouwensrelatie die is gemaakt voor MFA-scenario
- Wizard Azure AD Connect: WAP van AD FS-deploy: server toevoegen kan geen nieuw certificaat gebruiken
- Wizard Azure AD Connect: DSSO-uitzondering wanneer onPremCredentials niet zijn geïnitialiseerd voor een domein 
- Het kenmerk AD distinguishedName bij voorkeur doorstromen van het object Actieve gebruiker.
- Fixed a cosmetic bug were the Priorence of the first OOB Sync Rule was set to 99 instead of 100 Fixed a cosmetic bug were the Precedence of the first OOB Sync Rule was set to 99 instead of 100 Fixed a cosmetic bug were the Precedence of the first OOB Sync Rule was set to 99 instead of 100 Fixed a



## <a name="117510"></a>1.1.751.0
Status 4/12/2018: alleen vrijgegeven voor download

>[!NOTE]
>Deze release is een hotfix voor Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Azure AD Connect-synchronisatie
#### <a name="fixed-issues"></a>Problemen opgelost
Gecorrigeerd een probleem waren automatische Azure-instantie detectie voor China tenants was soms niet.  

### <a name="ad-fs-management"></a>AD FS-Management
#### <a name="fixed-issues"></a>Problemen opgelost

Er was een probleem in de configuratie opnieuw proberen logica die zou resulteren in een ArgumentException met vermelding van "een item met dezelfde sleutel is al toegevoegd."  Dit zou ertoe leiden dat alle bewerkingen opnieuw mislukken.

## <a name="117500"></a>1.1.750.0
Status 3/22/2018: Vrijgegeven voor automatisch upgraden en downloaden.
>[!NOTE]
>Wanneer de upgrade naar deze nieuwe versie is voltooid, wordt automatisch een volledige synchronisatie en volledige import voor de Azure AD-connector en een volledige synchronisatie voor de AD-connector geactiveerd. Aangezien dit enige tijd kan duren, afhankelijk van de grootte van uw Azure AD Connect-omgeving, moet u ervoor zorgen dat u de nodige stappen hebt ondernomen om dit te ondersteunen of een upgrade te onthouden totdat u een handig moment hebt gevonden om dit te doen.

>[!NOTE]
>"De functie AutoUpgrade is ten onrechte uitgeschakeld voor sommige tenants die builds later dan 1.1.524.0 hebben geïmplementeerd. Voer de volgende PowerShell-cmdlet in om ervoor te zorgen dat uw Azure AD Connect-exemplaar nog steeds in aanmerking komt voor AutoUpgrade: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemen opgelost

* Cmdlet set-ADSyncAutoUpgrade zou eerder automatisch upgraden blokkeren als de status van automatische upgrade is ingesteld op Opgeschort. Deze functionaliteit is nu veranderd, zodat het niet blokkeert AutoUpgrade van toekomstige builds.
* De optie **Aanmeldingspagina** van de gebruiker gewijzigd in 'Wachtwoordhashsynchronisatie'.  Azure AD Connect synchroniseert wachtwoordhashes, geen wachtwoorden, dus dit komt overeen met wat er daadwerkelijk gebeurt.  Zie Synchronisatie [van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](how-to-connect-password-hash-synchronization.md) voor meer informatie

## <a name="117490"></a>1.1.749.0
Status: vrijgegeven om klanten te selecteren

>[!NOTE]
>Wanneer de upgrade naar deze nieuwe versie is voltooid, wordt automatisch een volledige synchronisatie en volledige import voor de Azure AD-connector en een volledige synchronisatie voor de AD-connector geactiveerd. Aangezien dit enige tijd kan duren, afhankelijk van de grootte van uw Azure AD Connect-omgeving, moet u ervoor zorgen dat u de nodige stappen hebt ondernomen om dit te ondersteunen of een upgrade onthouden totdat u een handig moment hebt gevonden om dit te doen.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemen opgelost
* Fix timing window on background tasks for Partition Filtering page when switching to next page.

* Fixed a bug that caused Access violation during the ConfigDB custom action.

* Fixed a bug to recover from SQL connection timeout.

* Fixed a bug where certificates with SAN wildcards failed a prerequisite check.

* Fixed a bug which causes miiserver.exe to crash during an Azure AD connector export.

* Fixed a bug which bad password attempt logged on DC when running the Azure AD Connect wizard to change configuration.


#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Privacy-instellingen toevoegen voor de Algemene Verordening Gegevensbescherming (AVG).  Voor meer informatie zie het artikel [hier](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* applicatie telemetrie - admin kan deze klasse van gegevens aan / uit schakelen naar eigen tijd

* Azure AD-statusgegevens - beheerder moet de statusportal bezoeken om hun statusinstellingen te beheren.
   Zodra het servicebeleid is gewijzigd, zullen de agents het lezen en afdwingen.

* Toegevoegde configuratieacties voor apparaatafschrijvingen en een voortgangsbalk voor het initialiseren van pagina's

* Verbeterde algemene diagnostiek met HTML-rapport en volledige gegevensverzameling in een ZIP-Text / HTML-rapport

* Verbeterde betrouwbaarheid van de automatische upgrade en extra telemetrie toegevoegd om ervoor te zorgen dat de status van de server kan worden bepaald

* Machtigingen beperken die beschikbaar zijn voor bevoegde accounts in het AD Connector-account

  * Voor nieuwe installaties beperkt de wizard de machtigingen die bevoegde accounts hebben op het MSOL-account nadat het MSOL-account is gemaakt.

De wijzigingen zorgen voor de volgende:
1. Express-installaties
2. Aangepaste installaties met account automatisch maken
3. Het installatieprogramma gewijzigd, zodat er geen SA-bevoegdheid nodig is voor de schone installatie van Azure AD Connect

* Een nieuw hulpprogramma toegevoegd om synchronisatieproblemen voor een specifiek object op te lossen. Het is beschikbaar onder de optie Problemen met objectsynchronisatie van de wizard Azure AD Connect Problemen met extra taken oplossen. Momenteel controleert het nutsbedrijf voor het volgende:

  * UserPrincipalName mismatch tussen gesynchroniseerde gebruikersobject en het gebruikersaccount in Azure AD Tenant.
  * Als het object is gefilterd uit synchronisatie als gevolg van domeinfilter
  * Als het object wordt gefilterd uit synchronisatie als gevolg van het filteren van de organisatie-eenheid (OU)

* Een nieuw hulpprogramma toegevoegd om de huidige wachtwoordhash te synchroniseren die is opgeslagen in de on-premises Active Directory voor een specifiek gebruikersaccount.

Het hulpprogramma vereist geen wachtwoordwijziging. Het is beschikbaar onder 'Problemen met wachtwoordhashsynchronisatie' optie van Azure AD Connect Wizard Problemen met extra taken oplossen.






## <a name="116540"></a>1.1.654.0
Status: 12 december 2017

>[!NOTE]
>Deze release is een beveiligingsgerelateerde hotfix voor Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Er is een verbetering toegevoegd aan Azure AD Connect-versie 1.1.654.0 (en daarna) om ervoor te zorgen dat de aanbevolen machtigingswijzigingen die worden beschreven onder sectie [Toegang tot het AD DS-account vergrendelen](#lock) automatisch worden toegepast wanneer Azure AD Connect het AD DS-account maakt. 

- Bij het instellen van Azure AD Connect kan de installatiebeheerder een bestaand AD DS-account opgeven of Azure AD Connect automatisch het account laten maken. De machtigingswijzigingen worden automatisch toegepast op het AD DS-account dat tijdens de installatie door Azure AD Connect is gemaakt. Ze worden niet toegepast op bestaande AD DS-account die wordt verstrekt door de installatiebeheerder.
- Voor klanten die een upgrade hebben uitgevoerd van een oudere versie van Azure AD Connect naar 1.1.654.0 (of na), worden de machtigingswijzigingen niet met terugwerkende kracht toegepast op bestaande AD DS-accounts die vóór de upgrade zijn gemaakt. Ze worden alleen toegepast op nieuwe AD DS-accounts die na de upgrade zijn gemaakt. Dit gebeurt wanneer u nieuwe AD-forests toevoegt die moeten worden gesynchroniseerd met Azure AD.

>[!NOTE]
>Met deze release wordt alleen het beveiligingslek verwijderd voor nieuwe installaties van Azure AD Connect, waarbij het serviceaccount wordt gemaakt door het installatieproces. Voor bestaande installaties, of in gevallen waarin u het account zelf verstrekt, moet u ervoor zorgen dat dit beveiligingslek niet bestaat.

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a>Toegang tot het AD DS-account vergrendelen
De toegang tot het AD DS-account vergrendelen door de volgende machtigingswijzigingen in het on-premises AD te implementeren:  

*   Overerving op het opgegeven object uitschakelen
*   Verwijder alle AME's op het specifieke object, met uitzondering van AME's die specifiek zijn voor SELF. We willen de standaard machtigingen intact te houden als het gaat om SELF.
*   Wijs deze specifieke machtigingen toe:

Type     | Name                          | Toegang               | Van toepassing op
---------|-------------------------------|----------------------|--------------|
Toestaan    | SYSTEEM                        | Volledig beheer         | Dit object  |
Toestaan    | Ondernemingsadministrators             | Volledig beheer         | Dit object  |
Toestaan    | Domeinadministrators                 | Volledig beheer         | Dit object  |
Toestaan    | Beheerders                | Volledig beheer         | Dit object  |
Toestaan    | Domeincontrollers voor bedrijfsdomeinen | Lijstinhoud        | Dit object  |
Toestaan    | Domeincontrollers voor bedrijfsdomeinen | Alle eigenschappen lezen  | Dit object  |
Toestaan    | Domeincontrollers voor bedrijfsdomeinen | Leesmachtigingen     | Dit object  |
Toestaan    | Geverifieerde gebruikers           | Lijstinhoud        | Dit object  |
Toestaan    | Geverifieerde gebruikers           | Alle eigenschappen lezen  | Dit object  |
Toestaan    | Geverifieerde gebruikers           | Leesmachtigingen     | Dit object  |

Als u de instellingen voor het AD DS-account wilt aanscherpen, u [dit PowerShell-script](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978)uitvoeren. In het PowerShell-script worden de hierboven vermelde machtigingen toegewezen aan het AD DS-account.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-script om een reeds bestaand serviceaccount aan te scherpen

Als u het PowerShell-script wilt gebruiken om deze instellingen toe te passen op een reeds bestaand AD DS-account (ether die door uw organisatie wordt geleverd of gemaakt door een eerdere installatie van Azure AD Connect, downloadt u het script van de bovenstaande koppeling.

##### <a name="usage"></a>Gebruik:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Waar 

**$ObjectDN** = Het Active Directory-account waarvan de machtigingen moeten worden aangescherpt.

**$Credential** = Beheerdersreferenties die de benodigde bevoegdheden hebben om de machtigingen voor het $ObjectDN-account te beperken. Deze bevoegdheden zijn meestal in het bezit van de Enterprise- of Domeinbeheerder. Gebruik de volledig gekwalificeerde domeinnaam van het beheerdersaccount om fouten in het opzoeken van accounts te voorkomen. Voorbeeld: contoso.com\admin.

>[!NOTE] 
>$credential. UserName moet in fqdn\gebruikersnaamformaat zijn. Voorbeeld: contoso.com\admin 

##### <a name="example"></a>Voorbeeld:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Werd dit beveiligingslek gebruikt om ongeautoriseerde toegang te krijgen?

Als u wilt zien of dit beveiligingslek is gebruikt om uw Azure AD Connect-configuratie te compromitteren, moet u de laatste wachtwoordresetdatum van het serviceaccount verifiëren.  Als de tijdstempel in onverwachte, verder onderzoek, via het gebeurtenislogboek, voor die gebeurtenis van het wachtwoord opnieuw instellen, zou moeten worden ondernomen.

Zie [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318) voor meer informatie

## <a name="116490"></a>1.1.649.0
Statuut: 27 oktober 2017

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie Auto-upgrade van Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Probleem  opgelost
* Fixed a version compatibility issue between Azure AD Connect and Azure AD Connect Health Agent (for sync). Dit probleem heeft gevolgen voor klanten die Azure AD Connect in-place upgraden naar versie 1.1.647.0 uitvoeren, maar momenteel Health Agent-versie 3.0.127.0 hebben. Na de upgrade kan de statusagent geen statusgegevens meer over Azure AD Connect Synchronization Service verzenden naar Azure AD Health Service. Met deze oplossing wordt Health Agent-versie 3.0.129.0 geïnstalleerd tijdens de upgrade van Azure AD Connect. Health Agent-versie 3.0.129.0 heeft geen compatibiliteitsprobleem met Azure AD Connect-versie 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Statuut: 19 oktober 2017

> [!IMPORTANT]
> Er is een bekend compatibiliteitsprobleem tussen Azure AD Connect-versie 1.1.647.0 en Azure AD Connect Health Agent (voor synchronisatie) versie 3.0.127.0. Met dit probleem kan de statusagent geen statusgegevens over de Azure AD Connect-synchronisatieservice (inclusief objectsynchronisatiefouten en run-geschiedenisgegevens) verzenden naar Azure AD Health Service. Controleer de huidige versie van Azure AD Connect-statusagent die is geïnstalleerd op uw Azure AD Connect-server voordat u uw Azure AD Connect-implementatie handmatig upgradet naar versie 1.1.647.0. U dit doen door naar het Configuratiescherm te gaan *→ Programma's verwijderen toevoegen* en zoeken naar toepassing Microsoft Azure AD *Connect-statusagent voor synchronisatie*. Als de versie 3.0.127.0 is, wordt aanbevolen dat u wacht tot de volgende Azure AD Connect-versie beschikbaar is voordat u een upgrade uitvoert. Als de Health Agent-versie niet 3.0.127.0 is, is het prima om verder te gaan met de handmatige, in-place upgrade. Houd er rekening mee dat dit probleem geen invloed heeft op swing-upgrade of klanten die de nieuwe installatie van Azure AD Connect uitvoeren.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problemen opgelost
* Fixed an issue with the *Change user sign-in* task in Azure AD Connect wizard:

  * Het probleem treedt op wanneer u een bestaande Azure AD *Connect-implementatie*hebt met wachtwoordsynchronisatie **ingeschakeld**en u probeert de aanmeldingsmethode van de gebruiker in te stellen als verificatie doorgeven . Voordat de wijziging wordt toegepast, geeft de wizard ten onrechte de prompt '*Wachtwoordsynchronisatie uitschakelen*' weergeven. Wachtwoordsynchronisatie blijft echter ingeschakeld nadat de wijziging is toegepast. Met deze correctie geeft de wizard de prompt niet meer weer.

  * De wizard schakelt wachtwoordsynchronisatie niet uit wanneer u de aanmeldingsmethode van de gebruiker bijwerkt met de *aanmeldingstaak voor gebruikers wijzigen.* Dit is om onderbreking te voorkomen voor klanten die wachtwoordsynchronisatie willen behouden, ook al maken ze pass-through-verificatie of federatie mogelijk als hun primaire aanmeldingsmethode voor gebruikers.
  
  * Als u wachtwoordsynchronisatie wilt uitschakelen na het bijwerken van de *aanmeldingsmethode van* de gebruiker, moet u de taak Synchronisatieconfiguratie aanpassen in de wizard uitvoeren. Wanneer u naar de pagina *Optionele functies* navigeert, schakelt u de optie *Wachtwoordsynchronisatie uit.*
  
  * Hetzelfde probleem treedt ook op als u Seamless Single Sign-On probeert in te schakelen/uitschakelen. U hebt met name een bestaande Azure AD Connect-implementatie met wachtwoordsynchronisatie ingeschakeld en de aanmeldingsmethode voor gebruikers is al geconfigureerd als *Pass-through Authentication.* Met de *aanmeldingstaak Voor gebruikers* wijzigen probeert u de optie Naadloos eenmalig aanmelden inschakelen in te *checken/uit* te vinken, terwijl de aanmeldingsmethode van de gebruiker is geconfigureerd als 'Doorgeefverificatie'. Voordat de wijziging wordt toegepast, geeft de wizard ten onrechte de prompt '*Wachtwoordsynchronisatie uitschakelen*' weergeven. Wachtwoordsynchronisatie blijft echter ingeschakeld nadat de wijziging is toegepast. Met deze correctie geeft de wizard de prompt niet meer weer.

* Fixed an issue with the *Change user sign-in* task in Azure AD Connect wizard:

  * Het probleem treedt op wanneer u een bestaande Azure AD *Connect-implementatie*hebt met wachtwoordsynchronisatie **uitgeschakeld**en u probeert de aanmeldingsmethode van de gebruiker in te stellen als verificatie doorgeven . Wanneer de wijziging wordt toegepast, schakelt de wizard zowel pass-through-verificatie als wachtwoordsynchronisatie in. Met deze oplossing schakelt de wizard wachtwoordsynchronisatie niet meer in.

  * Voorheen was wachtwoordsynchronisatie een vereiste voor het inschakelen van Pass-through Authentication. Wanneer u de aanmeldingsmethode van de gebruiker instelt als *Pass-through Authentication,* schakelt de wizard zowel pass-through-verificatie als wachtwoordsynchronisatie in. Onlangs is wachtwoordsynchronisatie als voorwaarde verwijderd. Als onderdeel van Azure AD Connect-versie 1.1.557.0 is een wijziging aangebracht in Azure AD Connect om wachtwoordsynchronisatie niet in te schakelen wanneer u de aanmeldingsmethode van de gebruiker instelt als *Doorgeefverificatie*. De wijziging is echter alleen toegepast op de installatie van Azure AD Connect. Met deze correctie wordt dezelfde wijziging ook toegepast op de *aanmeldingstaak voor* gebruikers wijzigen.
  
  * Hetzelfde probleem treedt ook op als u Seamless Single Sign-On probeert in te schakelen/uitschakelen. U hebt met name een bestaande Azure AD Connect-implementatie met wachtwoordsynchronisatie uitgeschakeld en de aanmeldingsmethode voor gebruikers is al geconfigureerd als *Pass-through Authentication.* Met de *aanmeldingstaak Voor gebruikers* wijzigen probeert u de optie Naadloos eenmalig aanmelden inschakelen in te *checken/uit* te vinken, terwijl de aanmeldingsmethode van de gebruiker is geconfigureerd als 'Doorgeefverificatie'. Wanneer de wijziging wordt toegepast, schakelt de wizard Wachtwoordsynchronisatie in. Met deze oplossing schakelt de wizard wachtwoordsynchronisatie niet meer in. 

* Fixed an issue that caused Azure AD Connect upgrade to fail with error "*Unable to upgrade the Synchronization Service ".* Verder kan de synchronisatieservice niet langer beginnen met gebeurtenisfout "*De service kon niet worden gestart omdat de versie van de database nieuwer is dan de versie van de geïnstalleerde binaire bestanden*". Het probleem treedt op wanneer de beheerder die de upgrade uitvoert, geen sysadmin-bevoegdheden heeft voor de SQL-server die wordt gebruikt door Azure AD Connect. Met deze oplossing vereist Azure AD Connect alleen dat de beheerder tijdens de upgrade db_owner bevoegdheid heeft in de ADSync-database.

* Fixed an Azure AD Connect Upgrade issue that affected customers who have enabled [Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Nadat Azure AD Connect is geüpgraded, wordt Seamless Single Sign-On onjuist weergegeven als uitgeschakeld in de wizard Azure AD Connect, ook al blijft de functie ingeschakeld en volledig functioneel. Met deze oplossing wordt de functie nu correct weergegeven zoals ingeschakeld in de wizard.

* Fixed an issue that caused Azure AD Connect wizard to always show the "*Configure Source Anchor*" prompt on the Ready to *Configure* page, even if no changes related to Source Anchor were made to Source Anchor were last.

* Bij het uitvoeren van handmatige upgrade van Azure AD Connect moet de klant de globale beheerdersreferenties van de bijbehorende Azure AD-tenant verstrekken. Voorheen kon de upgrade doorgaan, ook al behoorden de referenties van de globale beheerder toe aan een andere Azure AD-tenant. Hoewel de upgrade succesvol lijkt te zijn voltooid, worden bepaalde configuraties niet correct gehandhaafd met de upgrade. Met deze wijziging voorkomt de wizard dat de upgrade wordt voortgezet als de verstrekte referenties niet overeenkomen met de Azure AD-tenant.

* Overbodige logica verwijderd die azure AD Connect Health-service onnodig opnieuw heeft opgestart aan het begin van een handmatige upgrade.


#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Logica toegevoegd om de stappen te vereenvoudigen die nodig zijn om Azure AD Connect in te stellen met Microsoft Germany Cloud. Voorheen moest u specifieke registersleutels op de Azure AD Connect-server bijwerken om correct te kunnen werken met Microsoft Germany Cloud, zoals beschreven in dit artikel. Azure AD Connect kan nu automatisch detecteren of uw tenant zich in Microsoft Germany Cloud bevindt op basis van de algemene beheerdersreferenties die tijdens de installatie zijn verstrekt.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Opmerking: De synchronisatieservice heeft een WMI-interface waarmee u uw eigen aangepaste planner ontwikkelen. Deze interface is nu afgeschaft en wordt verwijderd uit toekomstige versies van Azure AD Connect die na 30 juni 2018 zijn verzonden. Klanten die de synchronisatieplanning willen aanpassen, moeten de [ingebouwde planner](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler)gebruiken.

#### <a name="fixed-issues"></a>Problemen opgelost
* Wanneer de wizard Azure AD Connect het AD Connector-account maakt dat nodig is om wijzigingen van on-premises Active Directory te synchroniseren, wordt het account niet correct toegewezen als toestemming om publicfolder-objecten te lezen. Dit probleem heeft gevolgen voor zowel express-installatie als aangepaste installatie. Met deze wijziging wordt het probleem opgelost.

* Fixed an issue that caused the Azure AD Connect Wizard troubleshooting page to not render correct for administrators running from Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Wanneer u problemen oplost met wachtwoordsynchronisatie met de pagina Azure AD Connect-wizardprobleemoplossend bedrijf, retourneert de pagina probleemoplossing nu de domeinspecifieke status.

* Als u voorheen hebt geprobeerd wachtwoordhashsynchronisatie in te schakelen, controleert Azure AD Connect niet of het AD-connector-account machtigingen heeft vereist om wachtwoordhashes van on-premises AD te synchroniseren. De wizard Azure AD Connect controleert en waarschuwt u nu als het AD Connector-account niet over voldoende machtigingen beschikt.

### <a name="ad-fs-management"></a>AD FS-Management
#### <a name="fixed-issue"></a>Probleem  opgelost
* Fixed an issue related to the use of [ms-DS-ConsistencyGuid as Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) feature. Dit probleem heeft gevolgen voor klanten die Federation hebben geconfigureerd *met AD FS* als aanmeldingsmethode voor gebruikers. Wanneer u de taak *Bronanker configureren* uitvoert in de wizard, schakelt Azure AD Connect over naar het gebruik van *ms-DS-ConsistencyGuid als bronkenmerk voor onveranderlijke Id. Als onderdeel van deze wijziging probeert Azure AD Connect de claimregels voor ImmutableId in AD FS bij te werken. Deze stap is echter mislukt omdat Azure AD Connect niet over de beheerdersreferenties beschikte die nodig zijn om AD FS te configureren. Met deze oplossing vraagt Azure AD Connect u nu om de beheerdersreferenties voor AD FS in te voeren wanneer u de taak *Bronanker configureren* uitvoert.



## <a name="116140"></a>1.1.614.0
Statuut: 05 september 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Bekende problemen
* Er is een bekend probleem waardoor de upgrade van Azure AD Connect mislukt met fout *" Kan de synchronisatieservice niet upgraden*". Verder kan de synchronisatieservice niet langer beginnen met gebeurtenisfout "*De service kon niet worden gestart omdat de versie van de database nieuwer is dan de versie van de geïnstalleerde binaire bestanden*". Het probleem treedt op wanneer de beheerder die de upgrade uitvoert, geen sysadmin-bevoegdheden heeft voor de SQL-server die wordt gebruikt door Azure AD Connect. Dbo-machtigingen zijn niet voldoende.

* Er is een bekend probleem met Azure AD Connect-upgrade dat gevolgen heeft voor klanten die [Naadloze aanmelding](how-to-connect-sso.md)hebben ingeschakeld. Nadat Azure AD Connect is bijgewerkt, wordt de functie weergegeven als uitgeschakeld in de wizard, ook al blijft de functie ingeschakeld. Een oplossing voor dit probleem zal worden verstrekt in de toekomstige release. Klanten die zich zorgen maken over dit weergaveprobleem kunnen dit handmatig oplossen door Naadloos aanmelden in de wizard in te schakelen.

#### <a name="fixed-issues"></a>Problemen opgelost
* Fixed an issue that prevented Azure AD Connect from updating the claims rules in on-premises AD FS while enabling the [ms-DS-ConsistencyGuid as Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) feature. Het probleem treedt op als u de functie probeert in te schakelen voor een bestaande Azure AD Connect-implementatie met AD FS geconfigureerd als aanmeldingsmethode. Het probleem treedt op omdat de wizard niet om ADFS-referenties vraagt voordat u probeert de claimregels in AD FS bij te werken.
* Fixed an issue that caused Azure AD Connect to fail installation if the on-premises AD forest has NTLM disabled. Het probleem is te wijten aan azure AD Connect wizard niet volledig gekwalificeerde referenties bij het maken van de beveiligingscontexten die nodig zijn voor Kerberos authenticatie. Hierdoor mislukt Kerberos-verificatie en valt de wizard Azure AD Connect terug naar het gebruik van NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Problemen opgelost
* Fixed an issue where new synchronization rule cannot be created if the Tag attribute isn't populated.
* Fixed an issue that caused Azure AD Connect to connect to on-premises AD for Password Synchronization using NTLM, eventhough Kerberos is available. Dit probleem treedt op als de on-premises AD-topologie een of meer domeincontrollers heeft die zijn hersteld vanuit een back-up.
* Fixed an issue that caused full synchronization steps to occur nonono after upgrade. Over het algemeen is het uitvoeren van volledige synchronisatiestappen vereist na de upgrade als er wijzigingen zijn in de out-of-box synchronisatieregels. Het probleem was te wijten aan een fout in de logica voor wijzigingsdetectie die een wijziging onjuist detecteerde bij het tegenkomen van synchronisatieregelexpressie met nieuwe regeltekens. Newline-tekens worden ingevoegd in synchronisatieregelexpressie om de leesbaarheid te verbeteren.
* Fixed an issue that can cause the Azure AD Connect server to not work correctly after Automatic Upgrade. Dit probleem heeft gevolgen voor Azure AD Connect-servers met versie 1.1.443.0 (of eerder). Raadpleeg artikel [Azure AD Connect werkt niet goed na een automatische upgrade voor](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade)meer informatie over het probleem.
* Probleem opgelost waardoor automatische upgrade elke 5 minuten opnieuw kan worden geprobeerd wanneer er fouten worden aangetroffen. Met de oplossing, Automatische upgrade opnieuw probeert met exponentiële back-off wanneer fouten worden aangetroffen.
* Fixed an issue where password synchronization event 611 is incorrect ly shown in Windows Application Event logs as **informational** instead of **error**. Gebeurtenis 611 wordt gegenereerd wanneer wachtwoordsynchronisatie een probleem ondervindt. 
* Fixed an issue in the Azure AD Connect wizard that allows Group writeback feature to be enabled without selecting an OU required for Group writeback.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Added a Troubleshoot task to Azure AD Connect wizard under Additional Tasks. Klanten kunnen deze taak gebruiken om problemen met betrekking tot wachtwoordsynchronisatie op te lossen en algemene diagnostiek te verzamelen. In de toekomst wordt de taak Problemen oplossen uitgebreid met andere problemen met adreslijstsynchronisatie.
* Azure AD Connect ondersteunt nu een nieuwe installatiemodus genaamd **Bestaande database gebruiken**. Met deze installatiemodus kunnen klanten Azure AD Connect installeren waarmee een bestaande ADSync-database is opgegeven. Zie artikel Een bestaande database [gebruiken](how-to-connect-install-existing-database.md)voor meer informatie over deze functie.
* Voor een betere beveiliging is Azure AD Connect nu standaard ingesteld op het gebruik van TLS1.2 om verbinding te maken met Azure AD voor adreslijstsynchronisatie. Voorheen was de standaardinstelling TLS1.0.
* Wanneer Azure AD Connect Password Synchronization Agent wordt opgestart, probeert deze verbinding te maken met het bekende eindpunt van Azure AD voor wachtwoordsynchronisatie. Na een succesvolle verbinding wordt deze doorgestuurd naar een regiospecifiek eindpunt. Voorheen heeft de wachtwoordsynchronisatieagent het regiospecifieke eindpunt opgeslagen totdat het opnieuw is gestart. Nu wist de agent de cache en probeert opnieuw met het bekende eindpunt als het verbindingsprobleem met het regiospecifieke eindpunt tegenkomt. Deze wijziging zorgt ervoor dat wachtwoordsynchronisatie kan mislukken naar een ander regiospecifiek eindpunt wanneer het regiospecifieke eindpunt in de cache niet meer beschikbaar is.
* Voor het synchroniseren van wijzigingen vanuit een on-premises AD-forest is een AD DS-account vereist. U (i) het AD DS-account zelf maken en de referenties ervan aan Azure AD Connect verstrekken, of (ii) de referenties van een Enterprise Admin verstrekken en Azure AD Connect het AD DS-account voor u laten maken. Voorheen is (i) de standaardoptie in de wizard Azure AD Connect. Nu is (ii) de standaardoptie.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health (Engelstalig)

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Ondersteuning toegevoegd voor Microsoft Azure Government Cloud en Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>AD FS-Management
#### <a name="fixed-issues"></a>Problemen opgelost
* De cmdlet Initialize-ADSyncNGCKeysWriteBack in de PowerShell-module AD prep heeft acl.l.' onjuist toegepast op de apparaatregistratiecontainer en zou daarom alleen bestaande machtigingen overnemen.  Dit is bijgewerkt zodat het synchronisatieserviceaccount de juiste machtigingen heeft.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* De aad connect verify ADFS Login-taak is bijgewerkt, zodat het aanmeldingen controleert tegen Microsoft Online en niet alleen token ophalen van ADFS.
* Bij het instellen van een nieuwe ADFS-farm met AAD Connect is de pagina waarin om ADFS-referenties wordt gevraagd, verplaatst zodat deze nu plaatsvindt voordat de gebruiker wordt gevraagd ADFS- en WAP-servers te leveren.  Hierdoor kan AAD Connect controleren of het opgegeven account de juiste machtigingen heeft.
* Tijdens de AAD Connect-upgrade zullen we niet langer een upgrade mislukken als de ADFS AAD Trust niet wordt bijgewerkt.  Als dat gebeurt, krijgt de gebruiker een passend waarschuwingsbericht te zien en moet de vertrouwensrelatie opnieuw worden ingesteld via de aanvullende taak van AAD Connect.

### <a name="seamless-single-sign-on"></a>Naadloze aanmelding
#### <a name="fixed-issues"></a>Problemen opgelost
* Fixed an issue that caused Azure AD Connect wizard to return an error if you try to enable [Seamless Single Sign-On](how-to-connect-sso.md). Het foutbericht is *'Configuratie van Microsoft Azure AD Connect-verificatieagent is mislukt'.* Dit probleem heeft gevolgen voor bestaande klanten die de preview-versie van de verificatiegemachtigden voor [verificatieverificatie](how-to-connect-sso.md) handmatig hadden geüpgraded op basis van de stappen die in dit [artikel](how-to-connect-pta-upgrade-preview-authentication-agents.md)zijn beschreven.


## <a name="115610"></a>1.1.561.0
Statuut: 23 juli 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Probleem  opgelost

* Fixed an issue that caused the out-of-box synchronization rule "Out to AD - User ImmubareId" to be removed:

  * Het probleem treedt op wanneer Azure AD Connect wordt bijgewerkt of wanneer de *synchronisatieconfiguratie van* de taakoptie Update in de wizard Azure AD Connect wordt gebruikt om de synchronisatieconfiguratie van Azure AD Connect bij te werken.
  
  * Deze synchronisatieregel is van toepassing op klanten die de [functie ms-DS-ConsistencyGuid hebben ingeschakeld als bronankerfunctie.](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) Deze functie werd geïntroduceerd in versie 1.1.524.0 en daarna. Wanneer de synchronisatieregel wordt verwijderd, kan Azure AD Connect niet langer on-premises AD ms-DS-ConsistencyGuid-kenmerk vullen met de kenmerkwaarde ObjectGuid. Het voorkomt niet dat nieuwe gebruikers worden ingericht in Azure AD.
  
  * De oplossing zorgt ervoor dat de synchronisatieregel niet meer wordt verwijderd tijdens de upgrade of tijdens configuratiewijziging, zolang de functie is ingeschakeld. Voor bestaande klanten die door dit probleem zijn getroffen, zorgt de oplossing er ook voor dat de synchronisatieregel wordt toegevoegd na een upgrade naar deze versie van Azure AD Connect.

* Fixed an issue that causes out-of-box synchronization rules to have precedal value that is less than 100:

  * In het algemeen zijn voorrangswaarden 0 - 99 gereserveerd voor aangepaste synchronisatieregels. Tijdens de upgrade worden de prioriteitswaarden voor out-of-box synchronisatieregels bijgewerkt om rekening te houden met wijzigingen in de synchronisatieregel. Als gevolg van dit probleem kunnen kant-en-klare synchronisatieregels worden toegewezen met een voorrangswaarde van minder dan 100.
  
  * De oplossing voorkomt dat het probleem optreedt tijdens de upgrade. Het herstelt echter niet de voorrangswaarden voor bestaande klanten die door het probleem zijn getroffen. In de toekomst zal een aparte oplossing worden verstrekt om te helpen bij de restauratie.

* Fixed an issue where the [Domain and OU Filtering screen](how-to-connect-install-custom.md#domain-and-ou-filtering) in the Azure AD Connect wizard is showing Sync all domains and *OUs* option as selected, even though OU-based filtering is enabled.

*   Fixed an issue that caused the [Configure Directory Partitions screen](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) in the Synchronization Service Manager to return an error if the *Refresh* button is clicked. Het foutbericht is *"Er is een fout opgetreden tijdens het vernieuwen van domeinen: Kan geen object casten van type 'System.Collections.ArrayList' om 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject' te typen.* De fout treedt op wanneer een nieuw AD-domein is toegevoegd aan een bestaand AD-forest en u Azure AD Connect probeert bij te werken met de knop Vernieuwen.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* [De functie Automatische upgrade](how-to-connect-install-automatic-upgrade.md) is uitgebreid om klanten te ondersteunen met de volgende configuraties:
  * U hebt de functie voor het terugschrijven van het apparaat ingeschakeld.
  * U hebt de functie voor groepsafschrijving ingeschakeld.
  * De installatie is geen Express-instellingen of een DirSync-upgrade.
  * Je hebt meer dan 100.000 objecten in de metaverse.
  * U maakt verbinding met meer dan één bos. Express-opstelling maakt slechts verbinding met één forest.
  * Het AD Connector-account is niet meer de standaard MSOL_-account.
  * De server is ingesteld om in de faseringsmodus te staan.
  * U hebt de terugschrijffunctie van de gebruiker ingeschakeld.
  
  >[!NOTE]
  >De scope-uitbreiding van de functie Automatische upgrade heeft gevolgen voor klanten met Azure AD Connect build 1.1.105.0 en daarna. Als u niet wilt dat uw Azure AD Connect-server automatisch wordt geüpgraded, moet `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`u de volgende cmdlet uitvoeren op uw Azure AD Connect-server: . Raadpleeg het artikel [Azure AD Connect: Automatische upgrade](how-to-connect-install-automatic-upgrade.md)voor meer informatie over het in- en uitschakelen van automatische upgrade.

## <a name="115580"></a>1.1.558.0
Status: Wordt niet vrijgegeven. Wijzigingen in deze build zijn opgenomen in versie 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Probleem  opgelost

* Fixed an issue that caused the out-of-box synchronization rule "Out to AD - User ImmubareId" to be removed when OU-based filtering configuration is updated. Deze synchronisatieregel is vereist voor de [functie ms-DS-ConsistencyGuid als bronankerfunctie.](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)

* Fixed an issue where the [Domain and OU Filtering screen](how-to-connect-install-custom.md#domain-and-ou-filtering) in the Azure AD Connect wizard is showing Sync all domains and *OUs* option as selected, even though OU-based filtering is enabled.

*   Fixed an issue that caused the [Configure Directory Partitions screen](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) in the Synchronization Service Manager to return an error if the *Refresh* button is clicked. Het foutbericht is *"Er is een fout opgetreden tijdens het vernieuwen van domeinen: Kan geen object casten van type 'System.Collections.ArrayList' om 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject' te typen.* De fout treedt op wanneer een nieuw AD-domein is toegevoegd aan een bestaand AD-forest en u Azure AD Connect probeert bij te werken met de knop Vernieuwen.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* [De functie Automatische upgrade](how-to-connect-install-automatic-upgrade.md) is uitgebreid om klanten te ondersteunen met de volgende configuraties:
  * U hebt de functie voor het terugschrijven van het apparaat ingeschakeld.
  * U hebt de functie voor groepsafschrijving ingeschakeld.
  * De installatie is geen Express-instellingen of een DirSync-upgrade.
  * Je hebt meer dan 100.000 objecten in de metaverse.
  * U maakt verbinding met meer dan één bos. Express-opstelling maakt slechts verbinding met één forest.
  * Het AD Connector-account is niet meer de standaard MSOL_-account.
  * De server is ingesteld om in de faseringsmodus te staan.
  * U hebt de terugschrijffunctie van de gebruiker ingeschakeld.
  
  >[!NOTE]
  >De scope-uitbreiding van de functie Automatische upgrade heeft gevolgen voor klanten met Azure AD Connect build 1.1.105.0 en daarna. Als u niet wilt dat uw Azure AD Connect-server automatisch wordt geüpgraded, moet `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`u de volgende cmdlet uitvoeren op uw Azure AD Connect-server: . Raadpleeg het artikel [Azure AD Connect: Automatische upgrade](how-to-connect-install-automatic-upgrade.md)voor meer informatie over het in- en uitschakelen van automatische upgrade.

## <a name="115570"></a>1.1.557.0
Status: juli 2017

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie Auto-upgrade van Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Probleem  opgelost
* Fixed an issue with the Initialize-ADSyncDomainJoinedComputerSync cmdlet that caused the verified domain configured on the existing service connection point object to be changed even if it is still a valid domain. Dit probleem treedt op wanneer uw Azure AD-tenant meer dan één geverifieerde domeinen heeft die kunnen worden gebruikt voor het configureren van het serviceverbindingspunt.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Wachtwoordwriteback is nu beschikbaar voor preview met Microsoft Azure Government cloud en Microsoft Cloud Germany. Raadpleeg voor meer informatie over Azure AD Connect-ondersteuning voor de verschillende service-exemplaren artikel [Azure AD Connect: Speciale overwegingen voor instanties](reference-connect-instances.md).

* De cmdlet Initialize-ADSyncDomainJoinedComputerSync heeft nu een nieuwe optionele parameter genaamd AzureADDomain. Met deze parameter u opgeven welk geverifieerd domein moet worden gebruikt voor het configureren van het serviceverbindingspunt.

### <a name="pass-through-authentication"></a>Pass-through-verificatie

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* De naam van de agent die vereist is voor pass-through-verificatie is gewijzigd van *Microsoft Azure AD Application Proxy Connector* naar Microsoft Azure AD Connect Authentication *Agent*.

* Doorstapverificatie inschakelen maakt standaard geen wachtwoordhashsynchronisatie meer mogelijk.


## <a name="115530"></a>1.1.553.0
Statuut: juni 2017

> [!IMPORTANT]
> Er zijn schema- en synchronisatieregelwijzigingen geïntroduceerd in deze build. Azure AD Connect Synchronization Service activeert na de upgrade stappen voor volledige import en volledige synchronisatie. Details van de wijzigingen worden hieronder beschreven. Als u de stappen Volledige import en volledige synchronisatie na de upgrade tijdelijk wilt uitstellen, raadpleegt u het artikel [Volledige synchronisatie na de upgrade uitstellen.](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Bekend probleem
* Er is een probleem dat van invloed is op klanten die [op OU gebaseerde filtering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) gebruiken met Azure AD Connect-synchronisatie. Wanneer u naar de [pagina Domein- en OU-filtering](how-to-connect-install-custom.md#domain-and-ou-filtering) in de wizard Azure AD Connect navigeert, wordt het volgende gedrag verwacht:
  * Als op u gebaseerde filtering is ingeschakeld, wordt de optie **Geselecteerde domeinen en -gegevens synchroniseren** geselecteerd.
  * Anders is de optie **Alle domeinen en OE's synchroniseren** geselecteerd.

Het probleem dat zich voordoet is dat de **optie Alle domeinen en -gegevens synchroniseren** altijd wordt geselecteerd wanneer u de wizard uitvoert.  Dit gebeurt zelfs als op OU gebaseerde filtering eerder is geconfigureerd. Voordat u aad connect-configuratiewijzigingen opslaat, controleert u of de **optie Geselecteerde domeinen en -gegevens synchroniseren is geselecteerd** en controleert u of alle Gegevens die moeten worden gesynchroniseerd, opnieuw zijn ingeschakeld. Anders wordt filteren op ou-gebaseerde filters uitgeschakeld.

#### <a name="fixed-issues"></a>Problemen opgelost

* Fixed an issue with Password writeback that allows an Azure AD Administrator to reset the password of an on-premises AD privileged user account. Het probleem treedt op wanneer Azure AD Connect de machtiging Wachtwoord opnieuw instellen krijgt via het bevoegde account. Het probleem wordt probleem verholpen in deze versie van Azure AD Connect door een Azure AD-beheerder niet toe te staan het wachtwoord van een willekeurig on-premises AD-gebruikersaccount opnieuw in te stellen, tenzij de beheerder de eigenaar van dat account is. Zie voor meer informatie [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Fixed an issue related to the [ms-DS-ConsistencyGuid as Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) feature where Azure AD Connect does not writeback to on-premises AD ms-DS-ConsistencyGuid attribute. Het probleem treedt op wanneer er meerdere on-premises AD-forests zijn toegevoegd aan Azure AD Connect en de *gebruikersidentiteiten voor meerdere mappen zijn* geselecteerd. Wanneer een dergelijke configuratie wordt gebruikt, vullen de resulterende synchronisatieregels het kenmerk sourceAnchorBinary niet in de metaverse. Het kenmerk sourceAnchorBinary wordt gebruikt als bronkenmerk voor het kenmerk ms-DS-ConsistencyGuid. Als gevolg hiervan vindt terugschrijven naar het kenmerk ms-DSConsistencyGuid niet plaats. Om het probleem op te lossen, zijn volgende synchronisatieregels bijgewerkt om ervoor te zorgen dat het kenmerk sourceAnchorBinary in de metaverse altijd wordt ingevuld:
  * Binnenkomen vanaf AD - InetOrgPerson AccountEnabled.xml
  * In van AD - InetOrgPerson Common.xml
  * Binnenkomen vanuit AD - GebruikersaccountEnabled.xml
  * Binnenkomen vanuit AD - User Common.xml
  * In vanuit AD - Gebruiker Word lid van SOAInAAD.xml

* Voorheen is zelfs als de [ms-DS-ConsistencyGuid als bronankerfunctie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) niet is ingeschakeld, de synchronisatieregel 'Uit naar AD – Gebruikersonveranderlijke id' nog steeds toegevoegd aan Azure AD Connect. Het effect is goedaardig en veroorzaakt niet writeback van ms-DS-ConsistencyGuid attribuut optreden. Om verwarring te voorkomen, is logica toegevoegd om ervoor te zorgen dat de synchronisatieregel alleen wordt toegevoegd wanneer de functie is ingeschakeld.

* Fixed an issue that caused password hash synchronization to fail with error event 611. Dit probleem treedt op nadat een of meer domeincontrollers zijn verwijderd uit het on-premises AD. Aan het einde van elke wachtwoordsynchronisatiecyclus bevat de synchronisatiecookie die wordt uitgegeven door on-premises AD aanroep-id's van de verwijderde domeincontrollers met USN-waarde (Update Sequence Number) van 0. De wachtwoordsynchronisatiemanager kan de synchronisatiecookie met USN-waarde van 0 niet blijven gebruiken en mislukt met foutgebeurtenis 611. Tijdens de volgende synchronisatiecyclus gebruikt de Wachtwoordsynchronisatiemanager de laatste aanhoudende synchronisatiecookie die geen USN-waarde van 0 bevat. Hierdoor worden dezelfde wachtwoordwijzigingen opnieuw gesynchroniseerd. Met deze oplossing blijft de synchronisatiecookie correct bestaan door de wachtwoordsynchronisatiemanager.

* Voorheen, zelfs als automatische upgrade is uitgeschakeld met behulp van de cmdlet Set-ADSyncAutoUpgrade, blijft het automatische upgradeproces periodiek controleren op een upgrade en is het afhankelijk van het gedownloade installatieprogramma om de uitschakelen te eren. Met deze oplossing controleert het automatische upgradeproces niet meer periodiek op upgrade. De oplossing wordt automatisch toegepast wanneer het upgrade-installatieprogramma voor deze Azure AD Connect-versie eenmaal wordt uitgevoerd.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Voorheen was de [ms-DS-ConsistencyGuid als Bronanker-functie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) alleen beschikbaar voor nieuwe implementaties. Nu is het beschikbaar voor bestaande implementaties. Met name:
  * Als u toegang wilt krijgen tot de functie, start u de wizard Azure AD Connect en kiest u de optie *Bronanker bijwerken.*
  * Deze optie is alleen zichtbaar voor bestaande implementaties die objectGuid als kenmerk sourceAnchor gebruiken.
  * Wanneer u de optie configureert, valideert de wizard de status van het kenmerk ms-DS-ConsistencyGuid in uw on-premises Active Directory. Als het kenmerk niet is geconfigureerd op een gebruikersobject in de map, gebruikt de wizard het kenmerk ms-DS-ConsistencyGuid als het kenmerk sourceAnchor. Als het kenmerk is geconfigureerd op een of meer gebruikersobjecten in de map, concludeert de wizard dat het kenmerk wordt gebruikt door andere toepassingen en niet geschikt is als bronAnkerkenmerk en de wijziging van het bronanker niet toestaat. Als u zeker weet dat het kenmerk niet wordt gebruikt door bestaande toepassingen, moet u contact opnemen met de ondersteuning voor informatie over het onderdrukken van de fout.

* Azure AD Connect is specifiek voor het kenmerk **userCertificate** op apparaatobjecten en zoekt nu naar certificaten die nodig zijn voor [het verbinden van domeinapparaten met Azure AD voor Windows 10-ervaring](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) en filtert de rest voordat deze wordt gesynchroniseerd met Azure AD. Om dit gedrag mogelijk te maken, is de out-of-box synchronisatieregel 'Uit naar AAD - Device Join SOAInAD' bijgewerkt.

* Azure AD Connect ondersteunt nu het opnieuw schrijven van exchange online **cloudPublicDelegates** kenmerk aan on-premises AD **publicDelegates** attribute. Dit maakt het scenario mogelijk waarin een Exchange Online-postvak SendOnBehalfTo-rechten kan worden verleend aan gebruikers met een on-premises Exchange-postvak. Om deze functie te ondersteunen, is een nieuwe kant-en-klare synchronisatieregel 'Uit naar AD – User Exchange Hybrid PublicDelegates writeback' toegevoegd. Deze synchronisatieregel wordt alleen toegevoegd aan Azure AD Connect wanneer de functie Exchange Hybrid is ingeschakeld.

* Azure AD Connect ondersteunt nu het synchroniseren van het **altRecipient-kenmerk** van Azure AD. Om deze wijziging te ondersteunen, zijn de volgende kant-en-klare synchronisatieregels bijgewerkt om de vereiste kenmerkstroom op te nemen:
  * Binnenkomen vanaf AD – User Exchange
  * Out to AAD – User ExchangeOnline
  
* Het kenmerk **cloudSOAExchMailbox** in de metaverse geeft aan of een bepaalde gebruiker het Exchange Online-postvak heeft of niet. De definitie is bijgewerkt met extra Exchange Online RecipientDisplayTypes als dergelijke apparatuur en vergaderruimte postvakken. Om deze wijziging mogelijk te maken, is de definitie van het kenmerk cloudSOAExchMailbox, dat wordt gevonden onder de out-of-box synchronisatieregel "In van AAD – User Exchange Hybrid", bijgewerkt van:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... op:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Added the following set of X509Certificate2-compatible functions for creating synchronization rule expressions to handle certificate values in the userCertificate attribute:

    ||||
    | --- | --- | --- |
    |CertSubject CertSubject|CertIssuer CertIssuer CertIssuer CertIssuer|CertKeyAlgoritme|
    |CertSubjectNameDN|CertIssuerOid CertIssuerOid CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert (IsCert)|
    |CertFriendlyName CertFriendlyName CertFriendlyName CertFriendly|CertThumbprint|CertExtensionOïden|
    |CertFormat CertFormat CertFormat CertFormat|CertNotAfter|CertPublicKeyoid CertPublicKeyoid CertPublicKeyoid CertPublic|
    |CertSerialNumber|CertNotBefore CertNotBefore CertNotBefore CertNot|CertPublicKeyParametersOid CertPublicKeyParametersOid CertPublicKeyParametersOid CertPublic|
    |CertVersie|CertSignatureAlgorithmOid|Selecteer|
    |CertKeyAlgorithmParams CertKeyAlgorithmParams CertKeyAlgorithmParams CertKey|CertHashString|Waar|
    |||With|

* Er zijn schemawijzigingen ingevoerd om klanten in staat te stellen aangepaste synchronisatieregels te maken om sAMAccountName, domainNetBios en domainFQDN voor groepsobjecten te stromen, evenals distinguishedName voor gebruikersobjecten:

  * De volgende kenmerken zijn toegevoegd aan het MV-schema:
    * Groep: AccountName
    * Groep: domainNetBios
    * Groep: domeinFQDN
    * Persoon: distinguishedName

  * De volgende kenmerken zijn toegevoegd aan het Azure AD Connector-schema:
    * Groep: OnpremisesSamAccountName
    * Groep: NetBiosName
    * Groep: DnsDomainName
    * Gebruiker: OnPremisesDistinguishedName

* Het ADSyncDomainJoinedComputerSync-cmdlet-script heeft nu een nieuwe optionele parameter met de naam AzureEnvironment. De parameter wordt gebruikt om op te geven in welk gebied de bijbehorende Azure Active Directory-tenant wordt gehost. Geldige waarden zijn:
  * AzureCloud (standaard)
  * AzureChinaCloud
  * AzureGermanyCloud
  * Amerikaanse overheid
 
* Bijgewerkte synchronisatieregeleditor om Join (in plaats van Voorziening) te gebruiken als de standaardwaarde van het koppelingstype tijdens het maken van synchronisatieregels.

### <a name="ad-fs-management"></a>AD FS-beheer

#### <a name="issues-fixed"></a>Problemen opgelost

* Volgende URL's zijn nieuwe WS-Federation-eindpunten die door Azure AD zijn geïntroduceerd om de tolerantie tegen verificatieuitval te verbeteren en worden toegevoegd aan de on-premises AD FS-vertrouwensconfiguratie van partijen:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Een probleem opgelost waardoor AD FS een onjuiste claimwaarde voor IssuerID genereerde. Het probleem treedt op als er meerdere geverifieerde domeinen zijn in de Azure AD-tenant en het domeinachtervoegsel van het kenmerk johndoe@us.contoso.comuserPrincipalName wordt gebruikt om de IssuerID-claim te genereren, ten minste 3 niveaus diep (bijvoorbeeld ). Het probleem wordt opgelost door de regex bij te werken die door de claimregels wordt gebruikt.

#### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
* Voorheen kon de ADFS-certificaatbeheerfunctie van Azure AD Connect alleen worden gebruikt met ADFS-farms die worden beheerd via Azure AD Connect. U de functie nu gebruiken met ADFS-farms die niet worden beheerd met Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Uitgebracht: mei 2017

> [!IMPORTANT]
> Er zijn schema- en synchronisatieregelwijzigingen geïntroduceerd in deze build. Azure AD Connect Synchronization Service activeert na de upgrade stappen voor volledige import en volledige synchronisatie. Details van de wijzigingen worden hieronder beschreven.
>
>

**Vaste problemen:**

Azure AD Connect-synchronisatie

* Fixed an issue that Causes Automatic Upgrade to occur on the Azure AD Connect server even if customer has disabled the feature using the Set-ADSyncAutoUpgrade cmdlet. Met deze oplossing controleert het automatische upgradeproces op de server nog steeds periodiek op upgrade, maar het gedownloade installatieprogramma eert de configuratie automatische upgrade.
* Tijdens de dirsync-upgrade maakt Azure AD Connect een Azure AD-serviceaccount dat door de Azure AD-connector moet worden gebruikt voor synchronisatie met Azure AD. Nadat het account is gemaakt, verifieert Azure AD Connect met Azure AD met behulp van het account. Soms mislukt verificatie vanwege tijdelijke problemen, waardoor DirSync op zijn plaats een upgrade mislukt met fout *"Er is een fout opgetreden bij het uitvoeren van AAD-synchronisatietaak configureren: AADSTS50034: Als u zich aanmeldt bij deze toepassing, moet het account worden toegevoegd aan de xxx.onmicrosoft.com directory."* Om de tolerantie van de DirSync-upgrade te verbeteren, probeert Azure AD Connect nu de verificatiestap opnieuw.
* Er was een probleem met build 443 waardoor DirSync in-place upgrade om te slagen, maar voer profielen die nodig zijn voor directory synchronisatie zijn niet gemaakt. Herstellogica is opgenomen in deze build van Azure AD Connect. Wanneer klanten deze build upgraden, detecteert Azure AD Connect ontbrekende runprofielen en maakt deze.
* Fixed an issue that causes Password Synchronization process to fail to start with Event ID 6900 and error *"An item with the same key has been already added".* Dit probleem treedt op als u de filterconfiguratie van de ORGANISATIE bijwerken om AD-configuratiepartitie op te nemen. Om dit probleem op te lossen, synchroniseert het wachtwoordsynchronisatieproces nu alleen wachtwoordwijzigingen van AD-domeinpartities. Niet-domeinpartities zoals configuratiepartitie worden overgeslagen.
* Tijdens de expresinstallatie maakt Azure AD Connect een on-premises AD DS-account dat door de AD-connector kan worden gebruikt om te communiceren met on-premises AD. Voorheen is het account gemaakt met de PASSWD_NOTREQD vlag ingesteld op de user-Account-Control attribuut en een willekeurig wachtwoord is ingesteld op het account. Azure AD Connect verwijdert nu expliciet de PASSWD_NOTREQD vlag nadat het wachtwoord is ingesteld op het account.
* Fixed an issue that causes DirSync upgrade to fail with error *"a deadlock occurred in sql server which trying to acquire an application lock"* when the mailNickname attribute is found in the on-premises AD schema, but is not bounded to the AD User object class.
* Fixed an issue that causes Device writeback feature to automatically disabled when an administrator is updating Azure AD Connect sync configuration using Azure AD Connect wizard. Dit probleem wordt veroorzaakt doordat de wizard een vereiste controle uitvoert op de bestaande apparaatterugschrijfconfiguratie in on-premises AD en de controle mislukt. De oplossing is om de controle over te slaan of device writeback al eerder is ingeschakeld.
* Als u het filteren van u wilt configureren, u de wizard Azure AD Connect of de synchronisatieservicebeheer gebruiken. Als u voorheen de wizard Azure AD Connect gebruikt om het filteren van de organisatie van de ORGANISATIE te configureren, worden nieuwe ok's die achteraf zijn gemaakt, opgenomen voor adreslijstsynchronisatie. Als u niet wilt dat nieuwe ok's worden opgenomen, moet u het filteren van de organisatie van de organisatie configureren met behulp van synchronisatieservicebeheer. U nu hetzelfde gedrag uitvoeren met de wizard Azure AD Connect.
* Fixed an issue that stored procedures required by Azure AD Connect to be created under the schema of the stalling admin, instead of under the dbo schema.
* Fixed an issue that causes the TrackingId attribute returned by Azure AD to be omitted in the AAD Connect Server Event Logs. Het probleem treedt op als Azure AD Connect een omleidingsbericht ontvangt van Azure AD en Azure AD Connect geen verbinding kan maken met het opgegeven eindpunt. De TrackingId wordt gebruikt door Support Engineers om te correleren met service side logs tijdens het oplossen van problemen.
* Wanneer Azure AD Connect largeobjectfout ontvangt van Azure AD, genereert Azure AD Connect een gebeurtenis met EventID 6941 en bericht *'Het ingerichte object is te groot. Knip het aantal kenmerkwaarden op dit object bij."* Tegelijkertijd genereert Azure AD Connect ook een misleidende gebeurtenis met EventID 6900 en bericht *'Microsoft.Online.Coexistence.ProvisionRetryException: kan niet communiceren met de Windows Azure Active Directory-service'.* Om verwarring te minimaliseren, genereert Azure AD Connect niet langer de laatste gebeurtenis wanneer largeobjectfout wordt ontvangen.
* Fixed an issue that causes the Synchronization Service Manager to become unresponsive when trying to update the configuration for Generic LDAP connector.

**Nieuwe functies/verbeteringen:**

Azure AD Connect-synchronisatie
* Wijzigingen in synchronisatieregel : De volgende wijzigingen in de synchronisatieregel zijn doorgevoerd:
  * Bijgewerkte standaardsynchronisatieregel die is ingesteld op het niet exporteren van kenmerken **userCertificate** en **userSMIMECertificate** als de kenmerken meer dan 15 waarden hebben.
  * AD-kenmerken **employeeID** en **msExchBypassModerationLink** zijn nu opgenomen in de standaardsynchronisatieregelset.
  * De **foto** ad-kenmerk is verwijderd uit de standaardsynchronisatieregelset.
  * **PreferredDataLocation toegevoegd** aan het Metaverse-schema en het AAD Connector-schema. Klanten die beide kenmerken in Azure AD willen bijwerken, kunnen hiervoor aangepaste synchronisatieregels implementeren. 
  * **UserType** toegevoegd aan het Metaverse-schema en het AAD Connector-schema. Klanten die beide kenmerken in Azure AD willen bijwerken, kunnen hiervoor aangepaste synchronisatieregels implementeren.

* Azure AD Connect maakt nu automatisch het gebruik van het kenmerk ConsistencyGuid mogelijk als kenmerk Bronanker voor on-premises AD-objecten. Verder vult Azure AD Connect het kenmerk ConsistencyGuid af met de objectGuid-kenmerkwaarde als deze leeg is. Deze functie is alleen van toepassing op nieuwe implementatie. Voor meer informatie over deze functie verwijzen we je door naar artikelsectie [Azure AD Connect: Ontwerpconcepten - Ms-DS-ConsistencyGuid gebruiken als sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Nieuwe probleemoplossing cmdlet Invoke-ADSyncDiagnostics is toegevoegd om te helpen diagnosticeren Password Hash Synchronisatie gerelateerde problemen. Raadpleeg artikel [Problemen met wachtwoordhashsynchronisatie oplossen met Azure AD Connect-synchronisatie](tshoot-connect-password-hash-synchronization.md)voor informatie over het gebruik van de cmdlet.
* Azure AD Connect ondersteunt nu het synchroniseren van openbare mapobjecten met ingeschakelde e-mail van on-premises AD naar Azure AD. U de functie inschakelen met de wizard Azure AD Connect onder Optionele onderdelen. Raadpleeg voor meer informatie over deze functie artikel [Office 365 Directory Based Edge Blocking support for on-premises Mail Enabled Public Folders](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect vereist een AD DS-account om te synchroniseren vanuit on-premises AD. Als u voorheen Azure AD Connect hebt geïnstalleerd met de Express-modus, u de referenties van een Enterprise Admin-account verstrekken en azure AD Connect het vereiste AD DS-account maken. Voor een aangepaste installatie en het toevoegen van forests aan een bestaande implementatie moest u echter in plaats daarvan het AD DS-account opleveren. Nu hebt u ook de mogelijkheid om de referenties van een Enterprise Admin-account op te geven tijdens een aangepaste installatie en Azure AD Connect het vereiste AD DS-account te laten maken.
* Azure AD Connect ondersteunt nu SQL AOA. U moet SQL AOA inschakelen voordat u Azure AD Connect installeert. Tijdens de installatie detecteert Azure AD Connect of de meegeleverde SQL-instantie is ingeschakeld voor SQL AOA of niet. Als SQL AOA is ingeschakeld, zoekt Azure AD Connect verder uit of SQL AOA is geconfigureerd om synchrone replicatie of asynchrone replicatie te gebruiken. Bij het instellen van de listener voor beschikbaarheidsgroepen wordt aanbevolen de eigenschap RegisterAllProvidersIP in te stellen op 0. Deze aanbeveling is omdat Azure AD Connect momenteel SQL Native Client gebruikt om verbinding te maken met SQL en SQL Native Client het gebruik van de eigenschap MultiSubNetFailover niet ondersteunt.
* Als u LocalDB gebruikt als database voor uw Azure AD Connect-server en de limiet van 10 GB heeft bereikt, wordt de synchronisatieservice niet meer gestart. Voorheen moet u de shrinkdatabase-bewerking uitvoeren op de LocalDB om voldoende DB-ruimte terug te winnen om de synchronisatieservice te starten. Daarna u de synchronisatieservicemanager gebruiken om de run-geschiedenis te verwijderen om meer DB-ruimte terug te winnen. U nu de cmdlet Start-ADSyncPurgeRunHistory gebruiken om run-geschiedenisgegevens van LocalDB te verwijderen om DB-ruimte terug te winnen. Verder ondersteunt deze cmdlet een offlinemodus (door de -offline parameter op te geven) die kan worden gebruikt wanneer de synchronisatieservice niet wordt uitgevoerd. Opmerking: De offlinemodus kan alleen worden gebruikt als de synchronisatieservice niet wordt uitgevoerd en de gebruikte database LocalDB is.
* Om de benodigde hoeveelheid opslagruimte te verminderen, comprimeert Azure AD Connect nu synchronisatiefoutgegevens voordat deze worden opgeslagen in LocalDB/SQL-databases. Wanneer u een upgrade uitvoert van een oudere versie van Azure AD Connect naar deze versie, voert Azure AD Connect een eenmalige compressie uit op bestaande synchronisatiefoutdetails.
* Voorheen, na het bijwerken van de configuratie van de O-filtering, moet u handmatig Volledige import uitvoeren om ervoor te zorgen dat bestaande objecten correct zijn opgenomen/uitgesloten van adressynchronisatie. Azure AD Connect activeert nu automatisch Volledige import tijdens de volgende synchronisatiecyclus. Verder wordt volledige import alleen toegepast op de AD-connectors die door de update worden beïnvloed. Opmerking: deze verbetering is alleen van toepassing op u-filterupdates die zijn uitgevoerd met de wizard Azure AD Connect. Het is niet van toepassing op de gb-filterupdate die is gemaakt met de synchronisatieservicebeheer.
* Voorheen ondersteunt filteren op groepen alleen gebruikers, groepen en contactobjecten. Nu ondersteunt groepsfiltering ook computerobjecten.
* Voorheen u connectorruimtegegevens verwijderen zonder azure AD Connect-synchronisatieplanner uit te schakelen. Nu blokkeert de synchronisatieservicemanager het verwijderen van connectorruimtegegevens als wordt gedetecteerd dat de planner is ingeschakeld. Verder wordt een waarschuwing geretourneerd om klanten te informeren over mogelijk gegevensverlies als de verbindingsruimtegegevens worden verwijderd.
* Voorheen moet u de PowerShell-transcriptie uitschakelen voor de wizard Azure AD Connect om correct uit te voeren. Dit probleem is gedeeltelijk opgelost. U PowerShell-transcriptie inschakelen als u de wizard Azure AD Connect gebruikt om synchronisatieconfiguratie te beheren. U moet PowerShell-transcriptie uitschakelen als u de wizard Azure AD Connect gebruikt om de ADFS-configuratie te beheren.



## <a name="114860"></a>1.1.486.0
Uitgebracht: april 2017

**Vaste problemen:**
* Probleem opgelost waarbij Azure AD Connect niet wordt geïnstalleerd op de gelokaliseerde versie van Windows Server.

## <a name="114840"></a>1.1.484.0
Uitgebracht: april 2017

**Bekende problemen:**

* Deze versie van Azure AD Connect wordt niet met succes geïnstalleerd als de volgende voorwaarden zijn:
   1. U voert een dirsync-upgrade uit of een nieuwe installatie van Azure AD Connect.
   2. U gebruikt een gelokaliseerde versie van Windows Server waarbij de naam van de ingebouwde administratorgroep op de server niet 'Administrators' is.
   3. U gebruikt de standaard SQL Server 2012 Express LocalDB die is geïnstalleerd met Azure AD Connect in plaats van uw eigen volledige SQL te leveren.

**Vaste problemen:**

Azure AD Connect-synchronisatie
* Fixed an issue where the sync scheduler skips the entire sync step if one or more connectors are missing run profile for that sync step. U hebt bijvoorbeeld handmatig een connector toegevoegd met behulp van de Synchronisatieservicemanager zonder er een Delta Import-runprofiel voor te maken. Deze oplossing zorgt ervoor dat de synchronisatieplanner Delta Import blijft uitvoeren voor andere connectors.
* Probleem opgelost waarbij de synchronisatieservice onmiddellijk stopt met het verwerken van een run-profiel wanneer het een probleem met een van de uitvoerenstappen tegenkomt. Deze oplossing zorgt ervoor dat de synchronisatieservice deze stap overslaat en de rest blijft verwerken. U hebt bijvoorbeeld een Delta Import-runprofiel voor uw AD-connector met meerdere uitvoerende stappen (één voor elk on-premises AD-domein). De synchronisatieservice voert Delta Import uit met de andere AD-domeinen, zelfs als een van deze domeinen problemen heeft met de netwerkverbinding.
* Fixed an issue that causes the Azure AD Connector update to be skiped during Automatic Upgrade.
* Fixed an issue that causes Azure AD Connect to incorrectly determine whether the server is a domain controller during setup, which turn causes DirSync upgrade to fail.
* Fixed an issue that causes DirSync in-place upgrade to no create any run profile for the Azure AD Connector.
* Probleem opgelost waarbij de gebruikersinterface Synchronisatieservicebeheer niet meer reageert wanneer u generieke LDAP-connector probeert te configureren.

AD FS-beheer
* Fixed an issue where the Azure AD Connect wizard fails if the AD FS primary node has been moved to another server.

Desktop SSO
* Fixed an issue in the Azure AD Connect wizard where the Sign-In screen no let you enable Desktop SSO feature if you chose Password Synchronization as your Sign-In option during new installation.

**Nieuwe functies/verbeteringen:**

Azure AD Connect-synchronisatie
* Azure AD Connect Sync ondersteunt nu het gebruik van Virtual Service Account, Managed Service Account en Group Managed Service Account als serviceaccount. Dit geldt alleen voor de nieuwe installatie van Azure AD Connect. Bij het installeren van Azure AD Connect:
    * De wizard Azure AD Connect maakt standaard een Virtual Service-account en gebruikt het als serviceaccount.
    * Als u een domeincontroller installeert, valt Azure AD Connect terug op eerder gedrag waarbij het een domeingebruikersaccount maakt en het in plaats daarvan als serviceaccount gebruikt.
    * U het standaardgedrag overschrijven door een van de volgende opties op te geven:
      * Een groepsbeheerserviceaccount
      * Een Managed Service-account
      * Een domeingebruikersaccount
      * Een lokaal gebruikersaccount
* Als u eerder een upgrade uitvoert naar een nieuwe build van Azure AD Connect met wijzigingen in de update of synchronisatieregel van connectors, wordt een volledige synchronisatiecyclus geactiveerd. Azure AD Connect activeert nu selectief De stap Volledige invoer alleen voor connectors met update en volledige synchronisatiestap alleen voor connectors met wijzigingen in de synchronisatieregel.
* Voorheen was de exportverwijderingsdrempel alleen van toepassing op exportdie wordt geactiveerd via de synchronisatieplanner. Nu wordt de functie uitgebreid met export die handmatig door de klant wordt geactiveerd met behulp van synchronisatieservicebeheer.
* Op uw Azure AD-tenant is er een serviceconfiguratie die aangeeft of de functie Wachtwoordsynchronisatie is ingeschakeld voor uw tenant of niet. Voorheen is het eenvoudig dat de serviceconfiguratie onjuist is geconfigureerd door Azure AD Connect wanneer u een actieve en een faseringsserver hebt. Azure AD Connect probeert nu alleen de serviceconfiguratie consistent te houden met uw actieve Azure AD Connect-server.
* De wizard Azure AD Connect detecteert en retourneert nu een waarschuwing als ad-prullenbak niet is ingeschakeld.
* Voorheen is exporteren naar Azure AD een keer uiten en mislukt als de gecombineerde grootte van de objecten in de batch bepaalde drempelwaarde overschrijdt. De synchronisatieservice probeert de objecten opnieuw in afzonderlijke, kleinere batches te verzenden als het probleem wordt aangetroffen.
* De toepassing Key Management van synchronisatieservice is verwijderd uit het Startmenu van Windows. Beheer van encryptiesleutel zal blijven worden ondersteund via command-line interface met behulp van miiskmu.exe. Raadpleeg artikel Verlaten van de [Azure AD Connect Sync-versleutelingssleutel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key)voor informatie over het beheren van versleutelingssleutel.
* Als u voorheen het wachtwoord van het Azure AD Connect-synchronisatieserviceaccount wijzigt, kan de synchronisatieservice pas correct worden gestart nadat u de versleutelingssleutel hebt verlaten en het wachtwoord van het Azure AD Connect-synchronisatieserviceaccount opnieuw hebt geïnitialiseerd. Nu is dit proces niet langer nodig.

Desktop SSO

* De wizard Azure AD Connect vereist niet langer dat poort 9090 op het netwerk wordt geopend bij het configureren van Pass-through Authentication en Desktop SSO. Alleen poort 443 is vereist. 

## <a name="114430"></a>1.1.443.0
Uitgebracht: maart 2017

**Vaste problemen:**

Azure AD Connect-synchronisatie
* Fixed an issue which causes Azure AD Connect wizard to fail if the display name of the Azure AD Connector does not contain the initial onmicrosoft.com domain assigned to the Azure AD tenant.
* Fixed an issue which causes Azure AD Connect wizard to fail while making connection to SQL database when the password of the Sync Service Account contains special characters such apostrof, colon and space.
* Fixed an issue which causes the error "The image has an anchor that is different than the image" to re occur on an Azure AD Connect server in staging mode, after you temporarily excludeed an on-premises AD object from syncing and then included it again for syncing.
* Fixed an issue which causes the error "The object located by DN is a phantom" tobewerking op een Azure AD Connect-server in staging-modus, nadat u een on-premises AD-object tijdelijk hebt uitgesloten van synchronisatie en vervolgens opnieuw hebt opgenomen voor synchronisatie.

AD FS-beheer
* Fixed an issue where Azure AD Connect wizard does not update AD FS configuration and set the right claims on the relying party trust after Alternate Login ID is configured.
* Fixed an issue where Azure AD Connect wizard is unable on correct handle AD FS servers whose service accounts are configured using userPrincipalName format instead of sAMAccountName format.

Pass-through-verificatie
* Fixed an issue which causes Azure AD Connect wizard to fail if Pass Through Authentication is selected but registration of the connector fails.
* Fixed an issue which causes Azure AD Connect wizard to bypass validation checks on sign-in method selected when Desktop SSO feature is enabled.

Wachtwoord opnieuw instellen
* Probleem opgelost waardoor de Azure AAD Connect-server niet opnieuw verbinding probeert te maken als de verbinding is verbroken door een firewall of proxy.

**Nieuwe functies/verbeteringen:**

Azure AD Connect-synchronisatie
* Get-ADSyncScheduler cmdlet retourneert nu een nieuwe Booleaanse eigenschap met de naam SyncCycleInProgress. Als de geretourneerde waarde waar is, betekent dit dat er een geplande synchronisatiecyclus aan de gang is.
* De doelmap voor het opslaan van Azure AD Connect-installatie- en installatielogboeken is verplaatst van %localappdata%\AADConnect naar %programdata%\AADConnect om de toegankelijkheid van de logboekbestanden te verbeteren.

AD FS-beheer
* Ondersteuning toegevoegd voor het bijwerken van AD FS Farm TLS/SSL-certificaat.
* Ondersteuning toegevoegd voor het beheren van AD FS 2016.
* U nu bestaande gMSA (Group Managed Service Account) opgeven tijdens de INSTALLATIE van AD FS.
* U SHA-256 nu configureren als het handtekeninghashalgoritme voor Azure AD-vertrouwensrelatie met de partij.

Wachtwoord opnieuw instellen
* Geïntroduceerdverbeteringen om het product te laten functioneren in omgevingen met strengere firewallregels.
* Verbeterde betrouwbaarheid van de verbinding met Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Uitgebracht: december 2016

**Probleem opgelost:**

* Fixed the issue where the issuerid claim rule for Active Directory Federation Services (AD FS) is missing in this build.

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie Auto-upgrade van Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Uitgebracht: december 2016

**Bekend probleem:**

* De issuerid claim regel voor AD FS ontbreekt in deze build. De probleemclaimregel is vereist als u meerdere domeinen met Azure Active Directory (Azure AD) vereert. Als u Azure AD Connect gebruikt om uw on-premises AD FS-implementatie te beheren, verwijdert upgraden naar deze build de bestaande probleemclaimregel uit uw AD FS-configuratie. U het probleem oplossen door de probleemregels na de installatie/upgrade toe te voegen. Raadpleeg dit artikel over [meerdere domeinondersteuning voor het naleven met Azure AD voor](how-to-connect-install-multiple-domains.md)meer informatie over het toevoegen van de regel voor probleemregels.

**Probleem opgelost:**

* Als poort 9090 niet is geopend voor de uitgaande verbinding, mislukt de installatie of upgrade van Azure AD Connect.

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie Auto-upgrade van Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Uitgebracht: december 2016

**Bekende problemen:**

* De issuerid claim regel voor AD FS ontbreekt in deze build. De probleemclaimregel is vereist als u meerdere domeinen met Azure AD vereert. Als u Azure AD Connect gebruikt om uw on-premises AD FS-implementatie te beheren, verwijdert upgraden naar deze build de bestaande probleemclaimregel uit uw AD FS-configuratie. U het probleem oplossen door de probleemregels voor claim toe te voegen na installatie/upgrade. Raadpleeg dit artikel over [meerdere domeinondersteuning voor het naleven met Azure AD voor](how-to-connect-install-multiple-domains.md)meer informatie over het toevoegen van de regel voor probleemregels.
* Poort 9090 moet buitenuitgaand zijn om de installatie te voltooien.

**Nieuwe functies:**

* Verificatie doorgeven (voorbeeld).

>[!NOTE]
>Deze build is niet beschikbaar voor klanten via de functie Auto-upgrade van Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Uitgebracht: november 2016

**Bekend probleem:**

* De issuerid claim regel voor AD FS ontbreekt in deze build. De probleemclaimregel is vereist als u meerdere domeinen met Azure AD vereert. Als u Azure AD Connect gebruikt om uw on-premises AD FS-implementatie te beheren, verwijdert upgraden naar deze build de bestaande probleemclaimregel uit uw AD FS-configuratie. U het probleem oplossen door de probleemregels voor claim toe te voegen na installatie/upgrade. Raadpleeg dit artikel over [meerdere domeinondersteuning voor het naleven met Azure AD voor](how-to-connect-install-multiple-domains.md)meer informatie over het toevoegen van de regel voor probleemregels.

**Vaste problemen:**

* Soms mislukt het installeren van Azure AD Connect omdat het geen lokaal serviceaccount kan maken waarvan het wachtwoord voldoet aan de complexiteit die is opgegeven door het wachtwoordbeleid van de organisatie.
* Fixed an issue where join rules are not rereevaluated when an object in the connector space simultaneous becomes out-of-scope for one join rule and become in-scope for another. Dit kan gebeuren als u twee of meer join regels waarvan de join voorwaarden zijn wederzijds exclusief.
* Fixed an issue where inbound synchronization rules (from Azure AD), which do not contain join rules, are not processed if they have lower precedence values than those containing join rules.

**Verbeteringen**

* Added support for installation Azure AD Connect on Windows Server 2016 Standard or higher.
* Ondersteuning toegevoegd voor het gebruik van SQL Server 2016 als externe database voor Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Uitgebracht: augustus 2016

**Vaste problemen:**

* Wijzigingen in synchronisatie-interval vinden pas plaats nadat de volgende synchronisatiecyclus is voltooid.
* De wizard Azure AD Connect accepteert geen Azure AD-account\_waarvan de gebruikersnaam begint met een underscore ( ).
* De wizard Azure AD Connect kan het Azure AD-account niet verifiëren als het accountwachtwoord te veel speciale tekens bevat. Foutbericht 'Kan referenties niet valideren. Er is een onverwachte fout opgetreden." wordt teruggestuurd.
* Als u de tijdelijke server installeert, wordt wachtwoordsynchronisatie in de Azure AD-tenant uitgeschakeld en wordt wachtwoordsynchronisatie mislukt met actieve server.
* Wachtwoordsynchronisatie mislukt in ongebruikelijke gevallen waarin er geen wachtwoordhash is opgeslagen op de gebruiker.
* Wanneer Azure AD Connect-server is ingeschakeld voor de faseringsmodus, wordt het terugschrijven van wachtwoorden niet tijdelijk uitgeschakeld.
* De wizard Azure AD Connect toont niet de werkelijke wachtwoordsynchronisatie en wachtwoordterugschrijfconfiguratie wanneer de server zich in de faseringsmodus bevindt. Het toont altijd hen als uitgeschakeld.
* Configuratiewijzigingen in wachtwoordsynchronisatie en wachtwoordterugschrijftijd worden niet gehandhaafd door de wizard Azure AD Connect wanneer de server zich in de faseringsmodus bevindt.

**Verbeteringen**

* De cmdlet Start-ADSyncSyncCycle bijgewerkt om aan te geven of het in staat is om een nieuwe synchronisatiecyclus te starten of niet.
* De stop-ADSyncSyncCycle-cmdlet toegevoegd om de synchronisatiecyclus en -bewerking te beëindigen, die momenteel worden uitgevoerd.
* De cmdlet Stop-ADSyncScheduler bijgewerkt om de synchronisatiecyclus en -bewerking te beëindigen, die momenteel worden uitgevoerd.
* Wanneer u [Directory-extensies](how-to-connect-sync-feature-directory-extensions.md) configureert in de wizard Azure AD Connect, kan nu het Kenmerk Azure AD van het type 'Teletex-tekenreeks' worden geselecteerd.

## <a name="111890"></a>1.1.189.0
Uitgebracht: juni 2016

**Vaste problemen en verbeteringen:**

* Azure AD Connect kan nu worden geïnstalleerd op een FIPS-compatibele server.
  * Zie [Wachtwoordhashsynchronisatie en FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips)voor wachtwoordsynchronisatie.
* Probleem opgelost waarbij een NetBIOS-naam niet kon worden opgelost in de FQDN in de Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Uitgebracht: mei 2016

**Nieuwe functies:**

* Waarschuwt en helpt u domeinen te verifiëren als u dit niet hebt gedaan voordat u Azure AD Connect uitvoert.
* Ondersteuning toegevoegd voor [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany).
* Ondersteuning toegevoegd voor de nieuwste [Microsoft Azure Government-cloudinfrastructuur](reference-connect-instances.md#microsoft-azure-government) met nieuwe URL-vereisten.

**Vaste problemen en verbeteringen:**

* Filtering toegevoegd aan de Synchronisatieregeleditor om het gemakkelijk te maken om synchronisatieregels te vinden.
* Verbeterde prestaties bij het verwijderen van een verbindingsruimte.
* Fixed an issue when the same object was both deleted and added in the same run (called delete/add).
* Een uitgeschakelde synchronisatieregel schakelt niet langer opgenomen objecten en kenmerken in bij het vernieuwen van een upgrade of directoryschema.

## <a name="111300"></a>1.1.130.0
Uitgebracht: april 2016

**Nieuwe functies:**

* Added support for multi-valued attributes to [Directory extensions](how-to-connect-sync-feature-directory-extensions.md).
* Added support for more configuration variations for [automatic upgrade](how-to-connect-install-automatic-upgrade.md) to be considered eligible for upgrade.
* Toegevoegd enkele cmdlets voor [aangepaste scheduler](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Uitgebracht: maart 2016

**Vaste problemen:**

* Ervoor zorgen dat express-installatie niet kan worden gebruikt op Windows Server 2008 (pre-R2), omdat wachtwoordsynchronisatie niet wordt ondersteund op dit besturingssysteem.
* Upgraden van DirSync met een aangepaste filterconfiguratie werkte niet zoals verwacht.
* Wanneer u een upgrade uitvoert naar een nieuwere versie en er geen wijzigingen in de configuratie zijn, moet een volledige import/synchronisatie niet worden gepland.

## <a name="111100"></a>1.1.110.0
Uitgebracht: februari 2016

**Vaste problemen:**

* Upgraden van eerdere releases werkt niet als de installatie zich niet in de standaardmap C:\Program Files bevindt.
* Als u **het synchronisatieproces** aan het einde van de installatiewizard installeert en uitdraait, wordt de planningswizard voor de installatie een tweede keer niet ingeschakeld.
* De planner werkt niet zoals verwacht op servers waar de datum/tijd-notatie tussen de VS en de tijd niet wordt gebruikt. Het zal `Get-ADSyncScheduler` ook blokkeren om de juiste tijden terug te keren.
* Als u een eerdere versie van Azure AD Connect met AD FS hebt geïnstalleerd als aanmeldingsoptie en upgrade, u de installatiewizard niet opnieuw uitvoeren.

## <a name="111050"></a>1.1.105.0
Uitgebracht: februari 2016

**Nieuwe functies:**

* [Automatische upgradefunctie](how-to-connect-install-automatic-upgrade.md) voor klanten met Express-instellingen.
* Ondersteuning voor de globale beheerder door Azure Multi-Factor Authentication en Privileged Identity Management te gebruiken in de wizard installatie.
  * U moet toestaan dat uw proxy https://secure.aadcdn.microsoftonline-p.com ook verkeer toestaat als u Multi-Factor Authentication gebruikt.
  * U moet https://secure.aadcdn.microsoftonline-p.com toevoegen aan uw lijst met vertrouwde sites voor multi-factor authenticatie om goed te werken.
* Laat het wijzigen van de aanmeldingsmethode van de gebruiker na de eerste installatie toe.
* [Domein- en organisatie-eenheid-filtering](how-to-connect-install-custom.md#domain-and-ou-filtering) toestaan in de wizard installatie. Dit maakt het ook mogelijk om verbinding te maken met bossen waar niet alle domeinen beschikbaar zijn.
* [Scheduler](how-to-connect-sync-feature-scheduler.md) is ingebouwd in de synchronisatie-engine.

**Functies gepromoot van preview naar GA:**

* [Apparaat terugschrijven](how-to-connect-device-writeback.md).
* [Directory-extensies](how-to-connect-sync-feature-directory-extensions.md).

**Nieuwe preview-functies:**

* Het nieuwe standaardinterval voor synchronisatiecyclus is 30 minuten. Vroeger was het drie uur voor alle eerdere releases. Hiermee voegt u ondersteuning toe om het [gedrag van](how-to-connect-sync-feature-scheduler.md) de planner te wijzigen.

**Vaste problemen:**

* De pagina DNS-domeinen verifiëren herkende de domeinen niet altijd.
* Vraagt om domeinbeheerdersreferenties bij het configureren van AD FS.
* De on-premises AD-accounts worden niet herkend door de installatiewizard als ze zich in een domein bevinden met een andere DNS-structuur dan het hoofddomein.

## <a name="1091310"></a>1.0.9131.0
Uitgebracht: december 2015

**Vaste problemen:**

* Wachtwoordsynchronisatie werkt mogelijk niet wanneer u wachtwoorden wijzigt in Active Directory Domain Services (AD DS), maar werkt wanneer u een wachtwoord instelt.
* Wanneer u een proxyserver hebt, kan de verificatie naar Azure AD mislukken tijdens de installatie of als een upgrade wordt geannuleerd op de configuratiepagina.
* Bijwerken vanaf een eerdere release van Azure AD Connect met een volledig SQL Server-exemplaar mislukt als u geen SQL Server-systeembeheerder (SA) bent.
* Bij het bijwerken van een eerdere release van Azure AD Connect met een externe SQL Server wordt de fout 'Geen toegang tot de ADSync SQL-database' weergegeven.

## <a name="1091250"></a>1.0.9125.0
Uitgebracht: november 2015

**Nieuwe functies:**

* Kan AD FS opnieuw configureren naar Azure AD-vertrouwensrelatie.
* Kan het Active Directory-schema vernieuwen en synchronisatieregels opnieuw genereren.
* Kan een synchronisatieregel uitschakelen.
* Kan 'AuthoritativeNull' definiëren als een nieuw letterlijke regel in een synchronisatieregel.

**Nieuwe preview-functies:**

* [Azure AD Connect-status voor synchronisatie](how-to-connect-health-sync.md).
* Ondersteuning voor wachtwoordsynchronisatie van [Azure AD Domain Services.](../user-help/active-directory-passwords-update-your-own-password.md)

**Nieuw ondersteund scenario:**

* Ondersteunt meerdere on-premises Exchange-organisaties. Zie [Hybride implementaties met meerdere Active Directory-forests](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150))voor meer informatie.

**Vaste problemen:**

* Problemen met wachtwoordsynchronisatie:
  * Een object dat is verplaatst van out-of-scope naar in-scope, wordt het wachtwoord niet gesynchroniseerd. Dit omvat zowel het filteren van de organisatie van de organisatie van de o.a.-ring van de organisatie van de o.a.-
  * Als u een nieuwe organisatie-eenheid selecteert om in synchronisatie op te nemen, is geen volledige wachtwoordsynchronisatie vereist.
  * Wanneer een uitgeschakelde gebruiker is ingeschakeld, wordt het wachtwoord niet gesynchroniseerd.
  * De wachtrij voor het opnieuw proberen van wachtwoorden is oneindig en de vorige limiet van 5.000 objecten die moeten worden verwijderd, is verwijderd.
* Niet in staat om verbinding te maken met Active Directory met windows server 2016 forest-functioneel niveau.
* Kan de groep die wordt gebruikt voor groepsfiltern na de eerste installatie niet wijzigen.
* Maakt niet langer een nieuw gebruikersprofiel op de Azure AD Connect-server voor elke gebruiker die een wachtwoordwijziging doorvoert met wachtwoordterugschrijfinformatie ingeschakeld.
* Niet in staat om lange gehele waarden te gebruiken in synchronisatieregels scopes.
* Het selectievakje 'apparaat terugschrijven' blijft uitgeschakeld als er onbereikbare domeincontrollers zijn.

## <a name="1086670"></a>1.0.8667.0
Uitgebracht: augustus 2015

**Nieuwe functies:**

* De wizard Installatie van Azure AD Connect is nu gelokaliseerd naar alle Windows Server-talen.
* Added support for account unlock when using Azure AD password management.

**Vaste problemen:**

* De wizard installatie van Azure AD Connect loopt vast als een andere gebruiker de installatie voortzet in plaats van de persoon die de installatie voor het eerst heeft gestart.
* Als een eerdere verwijdering van Azure AD Connect de synchronisatie van Azure AD Connect niet netjes verwijdert, is het niet mogelijk om opnieuw te installeren.
* Kan Azure AD Connect niet installeren met express-installatie als de gebruiker zich niet in het hoofddomein van het forest bevindt of als een niet-Engelse versie van Active Directory wordt gebruikt.
* Als de FQDN van het Active Directory-gebruikersaccount niet kan worden opgelost, wordt een misleidend foutbericht 'Kan het schema niet vastleggen' weergegeven.
* Als het account dat in de Active Directory Connector wordt gebruikt, buiten de wizard wordt gewijzigd, mislukt de wizard bij volgende uitvoeringen.
* Azure AD Connect wordt soms niet geïnstalleerd op een domeincontroller.
* Kan de 'Faseringsmodus' niet in- en uitschakelen als extensiekenmerken zijn toegevoegd.
* Het terugschrijven van wachtwoorden mislukt in sommige configuraties vanwege een slecht wachtwoord op de Active Directory Connector.
* DirSync kan niet worden geüpgraded als een gedistingeerde naam (DN) wordt gebruikt in het filteren van kenmerken.
* Overmatig CPU-gebruik bij het gebruik van wachtwoordreset.

**Verwijderde voorbeeldfuncties:**

* De preview-functie [User writeback](how-to-connect-preview.md#user-writeback) is tijdelijk verwijderd op basis van feedback van onze preview-klanten. Het zal later opnieuw worden toegevoegd nadat we de verstrekte feedback hebben behandeld.

## <a name="1086410"></a>1.0.8641.0
Uitgebracht: juni 2015

**Eerste release van Azure AD Connect.**

De naam gewijzigd van Azure AD Sync naar Azure AD Connect.

**Nieuwe functies:**

* [Installatie van express-instellingen](how-to-connect-install-express.md)
* Kan [AD FS configureren](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Kan [upgraden van DirSync](how-to-dirsync-upgrade-get-started.md)
* [Onopzettelijke verwijderingen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Geïntroduceerde [faseringsmodus](how-to-connect-sync-staging-server.md)

**Nieuwe preview-functies:**

* [Terugschrijven van gebruikers](how-to-connect-preview.md#user-writeback)
* [Groep terugschrijven](how-to-connect-preview.md#group-writeback)
* [Apparaat terugschrijven](how-to-connect-device-writeback.md)
* [Uitbreidingen van de directory](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Uitgebracht: mei 2015

**Nieuwe eis:**

* Azure AD Sync vereist nu dat de .NET Framework-versie 4.5.1 wordt geïnstalleerd.

**Vaste problemen:**

* Het terugschrijven van wachtwoorden vanuit Azure AD mislukt met een azure servicebus-verbindingsfout.

## <a name="104910413"></a>1.0.491.0413
Uitgebracht: april 2015

**Vaste problemen en verbeteringen:**

* De Active Directory Connector verwerkt verwijderingen niet correct als de prullenbak is ingeschakeld en er meerdere domeinen in het forest zijn.
* De prestaties van importbewerkingen zijn verbeterd voor de Azure Active Directory Connector.
* Wanneer een groep de lidmaatschapslimiet heeft overschreden (standaard is de limiet ingesteld op 50.000 objecten), is de groep verwijderd in Azure Active Directory. Met het nieuwe gedrag wordt de groep niet verwijderd, wordt er een fout gegenereerd en worden nieuwe lidmaatschapswijzigingen niet geëxporteerd.
* Een nieuw object kan niet worden ingericht als er al een gefaseerde delete met dezelfde DN aanwezig is in de connectorruimte.
* Sommige objecten zijn gemarkeerd voor synchronisatie tijdens een deltasynchronisatie, ook al is er geen wijziging in scène gezet op het object.
* Als u een wachtwoordsynchronisatie afdwingt, wordt ook de gewenste DC-lijst verwijderd.
* CSExportAnalyzer heeft problemen met sommige objecten.

**Nieuwe functies:**

* Een join kan nu verbinding maken met "ANY" objecttype in de MV.

## <a name="104850222"></a>1.0.485.0222
Uitgebracht: februari 2015

**Verbeteringen**

* Verbeterde importprestaties.

**Vaste problemen:**

* Password Sync eert het cloudFiltered-kenmerk dat wordt gebruikt door kenmerkfiltering. Gefilterde objecten zijn niet langer in het bereik van wachtwoordsynchronisatie.
* In zeldzame situaties waar de topologie veel domeincontrollers had, werkt wachtwoordsynchronisatie niet.
* 'Gestopt-server' bij het importeren uit de Azure AD Connector nadat het apparaatbeheer is ingeschakeld in Azure AD/Intune.
* Als u lid wordt van Principals (Foreign Security Principals) uit meerdere domeinen in hetzelfde forest, wordt een dubbelzinnige joinfout veroorzaakt.

## <a name="104751202"></a>1.0.475.1202
Uitgebracht: december 2014

**Nieuwe functies:**

* Wachtwoordsynchronisatie met filtering op basis van kenmerken wordt nu ondersteund. Zie [Wachtwoordsynchronisatie met filteren](how-to-connect-sync-configure-filtering.md)voor meer informatie.
* Het kenmerk ms-DS-ExternalDirectoryObjectID wordt teruggeschreven naar Active Directory. Met deze functie wordt ondersteuning toegevoegd voor Office 365-toepassingen. Het maakt gebruik van OAuth2 om toegang te krijgen tot online en on-premises postvakken in een hybride exchange-implementatie.

**Problemen met de upgrade:**

* Er is een nieuwere versie van de aanmeldingsassistent beschikbaar op de server.
* Er is een aangepast installatiepad gebruikt om Azure AD Sync te installeren.
* Een ongeldig aangepast joincriterium blokkeert de upgrade.

**Andere oplossingen:**

* Fixed the templates for Office Pro Plus.
* Fixed installation issues caused by user names that start with a dash.
* Fixed losing the sourceAnchor setting when running the installation wizard a second time.
* Fixed ETW tracing for password synchronization.

## <a name="104701023"></a>1.0.470.1023
Uitgebracht: oktober 2014

**Nieuwe functies:**

* Wachtwoordsynchronisatie van meerdere on-premises Active Directory naar Azure AD.
* Lokale installatie-gebruikersinterface voor alle Windows Server-talen.

**Upgraden van AADSync 1.0 GA**

Als Azure AD Sync al is geïnstalleerd, is er nog een extra stap die u moet nemen als u een van de out-of-box synchronisatieregels hebt gewijzigd. Nadat u een upgrade hebt uitgevoerd naar de release van 1.0.470.1023, worden de synchronisatieregels die u hebt gewijzigd gedupliceerd. Ga als volgt te werk voor elke gewijzigde synchronisatieregel:

1. Zoek de synchronisatieregel die u hebt gewijzigd en houd rekening met de wijzigingen.
1. Verwijder de synchronisatieregel.
1. Zoek de nieuwe synchronisatieregel die is gemaakt door Azure AD Sync en pas de wijzigingen opnieuw toe.

**Machtigingen voor het Active Directory-account**

Het Active Directory-account moet extra machtigingen krijgen om de wachtwoordhashes uit Active Directory te kunnen lezen. De machtigingen voor het verlenen worden 'Mapwijzigingen repliceren' en 'Mapwijzigingen repliceren' genoemd. Beide machtigingen zijn vereist om de wachtwoordhashes te kunnen lezen.

## <a name="104190911"></a>1.0.419.0911
Uitgebracht: september 2014

**Eerste release van Azure AD Sync.**

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
