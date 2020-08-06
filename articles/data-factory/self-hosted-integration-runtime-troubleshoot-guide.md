---
title: Problemen met een zelf-hostende Integration runtime in Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met zelf-hostende Integration runtime in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: abnarain
ms.openlocfilehash: 49d173e0d0f2b96c385b4325335483d25e9a7c2d
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800592"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problemen met zelf-hostende Integration runtime oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor zelf-hostende Integration runtime in Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Zelf-hostende IR-logboeken van Azure Data Factory verzamelen

Azure Data Factory ondersteunt het weer geven en uploaden van fouten logboeken voor mislukte activiteiten die worden uitgevoerd op zelf-hostende IR/gedeelde IR. U kunt de onderstaande stappen volgen om de fout rapport-ID op te halen en vervolgens de rapport-ID in te voeren om verwante bekende problemen op te sporen.

1. Ga naar de pagina **activiteiten uitvoeren** .

1. Klik onder de kolom **fout** op de knop onder.

    ![Pagina uitvoeringen van activiteit](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. U ziet gerelateerde logboeken voor de uitvoering van de mislukte activiteit. Klik op de knop **Logboeken verzenden** voor verdere ondersteuning.

    ![Logboeken verzenden](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. U kunt Logboeken kiezen die u wilt verzenden. Voor *zelf-hostende IR*kunt u Logboeken uploaden die betrekking hebben op mislukte activiteiten of voor alle logboeken op zelf-hostend IR-knoop punt. Voor *gedeelde IR*kunt u alleen logboeken uploaden die betrekking hebben op mislukte activiteiten.

    ![Logboeken kiezen](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Wanneer de logboeken worden geüpload, kunt u een record van de rapport-ID bijhouden als u meer hulp nodig hebt om het probleem op te lossen.

    ![Logboeken uploaden](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Logboek weer geven en upload aanvragen worden uitgevoerd op alle online zelf-hostende IR-exemplaren. Zorg ervoor dat alle zelf-hostende IR-instanties online zijn in het geval van eventuele ontbrekende Logboeken. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Zelf-hostende IR-algemene fout of-fout

### <a name="tlsssl-certificate-issue"></a>Probleem met TLS/SSL-certificaat

#### <a name="symptoms"></a>Symptomen

Bij het inschakelen van TLS/SSL-certificaat (Geavanceerd) van een **zelf-hostend IR-Configuration Manager**  ->  **externe toegang vanaf intranet**, na het selecteren van een TLS/SSL-certificaat, wordt de volgende fout weer gegeven:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

In het bovenstaande geval gebruikt de gebruiker certificaat met ' microsoft.com ' als laatste item.

#### <a name="cause"></a>Oorzaak

Dit is een bekend probleem in WCF: alleen de laatste DNSName in SAN worden gecontroleerd door WCF TLS/SSL-validatie. 

#### <a name="resolution"></a>Oplossing

Een Joker teken certificaat wordt ondersteund in Azure Data Factory v2 zelf-hostende IR. Dit probleem treedt normaal op omdat het SSL-certificaat niet juist is. De laatste DNSName in SAN moet geldig zijn. Volg de onderstaande stappen om dit te controleren. 
1.  Open de beheer console, dubbel Controleer of het *onderwerp* en de *alternatieve naam* van het onderwerp in de certificaat gegevens. In het bovenstaande geval is het laatste item in *alternatieve naam voor onderwerp*, dat ' DNS-naam = Microsoft.com.com ' is, niet legitiem.
2.  Neem contact op met het certificaat van het probleem met het bedrijf om de onjuiste DNS-naam te verwijderen.

### <a name="concurrent-jobs-limit-issue"></a>Probleem met limiet voor gelijktijdige taken

#### <a name="symptoms"></a>Symptomen

Wanneer u probeert de limiet van gelijktijdige taken van de Azure Data Factory-gebruikers interface te verhogen, wordt het vastlopen als *bijwerken* voor altijd.
De maximum waarde van gelijktijdige taken is ingesteld op 24 en u wilt het aantal verhogen zodat taken sneller kunnen worden uitgevoerd. De minimum waarde die u kunt invoeren is 3 en de maximum waarde die u kunt invoeren is 32. U hebt de waarde van 24 tot en met 32 verhoogd en op de knop *bijwerken* geklikt, in de gebruikers interface die de update heeft *ontvangen zoals hieronder* wordt weer gegeven. Na het vernieuwen is de waarde van de klant nog steeds 24 gezien en is deze nooit bijgewerkt naar 32.

![Status bijwerken](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Oorzaak

Er is een beperking voor de instelling als de waarde afhankelijk is van de logicCore en het geheugen van de computer, u kunt deze gewoon aanpassen naar een kleinere waarde, bijvoorbeeld 24 en het resultaat bekijken.

> [!TIP] 
> - Raadpleeg [dit artikel](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)voor meer informatie over wat het Logic core-aantal is en hoe u het Logic core-aantal van de computer kunt vinden.
> - Zie [dit artikel](https://www.rapidtables.com/calc/math/Log_Calculator.html)voor meer informatie over het berekenen van Math. log.


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Zelf-hostend SSL-certificaat probleem van IR-HA

#### <a name="symptoms"></a>Symptomen

Het zelf-hostende IR-werk knooppunt heeft de volgende fout gerapporteerd:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Oorzaak

Wanneer er cases worden verwerkt die zijn gerelateerd aan SSL/TLS-Handshake, kunnen er problemen optreden met betrekking tot verificatie van de certificaat keten. 

#### <a name="resolution"></a>Oplossing

- Hier volgt een snelle en intuïtieve manier om problemen met de X. 509-certificaat keten op te lossen.
 
    1. Exporteer het certificaat dat moet worden geverifieerd. Ga naar computer certificaat beheren en zoek het certificaat dat u wilt controleren en klik met de rechter muisknop op **alle taken**  ->  **exporteren**.
    
        ![Taken exporteren](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Kopieer het geëxporteerde certificaat naar de client computer. 
    3. Voer de opdracht onder in CMD uit aan de client zijde. Zorg ervoor dat u hieronder *\<certificate path>* en *\<output txt file path>* tijdelijke aanduidingen met gerelateerde paden hebt vervangen.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Bijvoorbeeld:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Controleer of er een fout is opgetreden in het uitvoer-txt-bestand. U vindt de samen vatting van de fout aan het einde van het txt-bestand.

        Bijvoorbeeld: 

        ![Fout samenvatting](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Als u geen fout ziet aan het einde van het logboek bestand, zoals hieronder wordt weer gegeven, kunt u overwegen de certificaat keten op de client computer op te bouwen.
        
        ![Er is geen fout in het logboek bestand](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Als er AIA, CDP en OCSP zijn geconfigureerd in het certificaat bestand. We kunnen deze op een intuïtieverere manier controleren.
 
    1. U kunt deze informatie opvragen door de details van een certificaat te controleren.
    
        ![Details van certificaat](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Voer de onderstaande opdracht uit. Zorg ervoor dat u *\<certificate path>* de tijdelijke aanduiding vervangen door het bijbehorende pad van het certificaat.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Vervolgens wordt het **hulp programma voor het ophalen van url's** geopend. U kunt certificaten van AIA, CDP en OCSP controleren door te klikken op de knop **ophalen** .

        ![Ophaal knop](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        De certificaat keten kan worden opgebouwd als het certificaat van AIA ' geverifieerd ' is en het certificaat van CDP of OCSP ' geverifieerd ' is.

        Als er een fout optreedt bij het ophalen van AIA, CDP, werk met netwerk team om de client computer gereed te maken voor verbinding met de doel-URL. Het is voldoende als het HTTP-pad of het LDAP-pad kan worden geverifieerd.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Zelf-hostende IR kan bestand of assembly niet laden

#### <a name="symptoms"></a>Symptomen

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Bijvoorbeeld: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Oorzaak

Als u proces monitor neemt, ziet u het volgende resultaat:

[![Proces monitor](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> U kunt het filter instellen, zoals in de onderstaande scherm afbeelding wordt weer gegeven.
> Hiermee wordt aangegeven dat het dll **System. ValueTuple** zich niet bevindt in de map die is gerelateerd aan de GAC of in *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway*, of in de map *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
> In principe wordt het dll-bestand van de map *GAC* eerst geladen en vervolgens van *gedeeld* en ten slotte vanuit de map *Gateway* . Daarom kunt u het dll-bestand op een wille keurig pad plaatsen dat nuttig kan zijn.

![Filters instellen](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Oplossing

U kunt vinden dat de **System.ValueTuple.dll** zich bevindt in de map *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* . Kopieer het **System.ValueTuple.dll** naar de map *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* om het probleem op te lossen.

U kunt dezelfde methode gebruiken om een ander bestand of assembly ontbrekende problemen op te lossen.

#### <a name="more-information"></a>Meer informatie

De reden waarom u de System.ValueTuple.dll onder *%windir%\Microsoft.NET\assembly* en *%windir%\assembly* ziet, is dat het een .net-gedrag is. 

In de onderstaande fout ziet u duidelijk het assemblage *systeem. ValueTuple* is niet aanwezig. Dit probleem treedt dus op wanneer de toepassing probeert de assembly *System.ValueTuple.dll*te controleren.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Zie [dit artikel](https://docs.microsoft.com/dotnet/framework/app-domains/gac)voor meer informatie over GAC.


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Controleren of de zelf-hostende IR-sleutel ontbreekt

#### <a name="symptoms"></a>Symptomen

De zelf-hostende Integration runtime gaat plotseling naar offline zonder sleutel, onder fout bericht wordt weer gegeven in het gebeurtenis logboek:`Authentication Key is not assigned yet`

![De verificatie sleutel ontbreekt](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Oorzaak

- Het zelf-hostende IR-knoop punt of de logische zelf-hostende IR in de portal wordt verwijderd.
- Een schone verwijdering is voltooid.

#### <a name="resolution"></a>Oplossing

Als geen van de bovenstaande oorzaken van toepassing is, kunt u naar de map gaan: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway*en controleren of het bestand met de naam **configuraties** is verwijderd. Als de app wordt verwijderd, volgt u de instructies [hier](https://www.netwrix.com/how_to_detect_who_deleted_file.html) om te controleren wie het bestand verwijdert.

![Controleer het configuratie bestand](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Kan zelf-hostende IR niet gebruiken om twee on-premises gegevens archieven te bridgen

#### <a name="symptoms"></a>Symptomen

Na het maken van een zelf-hostende IRs voor zowel bron-als doel gegevens archieven, wilt u de twee IRs samen met elkaar verbinden om een kopie te volt ooien. Als de gegevens archieven zijn geconfigureerd in verschillende VNETs of als ze het gateway mechanisme niet begrijpen, raakt u fouten als: *het stuur programma van de bron is niet gevonden in de doel-IR*. *de bron is niet toegankelijk voor de doel-IR*.
 
#### <a name="cause"></a>Oorzaak

De zelf-hostende IR is ontworpen als een centraal knoop punt van een Kopieer activiteit, niet een client agent die voor elk gegevens archief moet worden geïnstalleerd.
 
In het bovenstaande geval moet de gekoppelde service voor elk gegevens archief met dezelfde IR worden gemaakt en moet de IR toegang hebben tot beide gegevens archieven via het netwerk. Ongeacht de IR wordt geïnstalleerd met het brongegevens archief, het doel gegevens archief of op een derde computer, als twee gekoppelde services worden gemaakt met een andere IRs, maar in dezelfde Kopieer activiteit worden gebruikt, wordt de doel-IR gebruikt en moeten de Stuur Programma's voor beide gegevens archieven worden geïnstalleerd op de doel-IR-computer.

#### <a name="resolution"></a>Oplossing

Installeer Stuur Programma's voor zowel de bron als de bestemming op de doel-IR, en zorg ervoor dat deze toegang heeft tot de brongegevens opslag.
 
Als het verkeer niet via het netwerk kan worden door gegeven tussen twee gegevens archieven (bijvoorbeeld als ze zijn geconfigureerd in twee VNETs), kunt u de kopie niet in één activiteit volt ooien, zelfs niet als er IR is geïnstalleerd. In dat geval kunt u twee Kopieer activiteiten maken met twee IRs, elk in een ventilator: 1 IR om te kopiëren van gegevens archief 1 naar Azure Blob Storage, een andere om te kopiëren van Azure Blob Storage naar gegevens archief 2. Dit kan de vereiste voor het gebruik van de IR simuleren om een brug te maken die twee niet-verbonden gegevens archieven verbindt.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Bij het synchroniseren van referenties is de referenties van HA verloren gegaan

#### <a name="symptoms"></a>Symptomen

De gegevens bron referentie "XXXXXXXXXX" wordt verwijderd uit het huidige Integration Runtime knoop punt met Payload "wanneer u de koppelings service op Azure Portal verwijdert of als de taak de verkeerde Payload heeft, maakt u opnieuw een nieuwe koppeling service met uw referenties.

#### <a name="cause"></a>Oorzaak

Uw zelf-hostende IR is gebouwd in de modus HA met twee knoop punten, maar deze hebben niet de synchronisatie status referenties, wat betekent dat de referenties die zijn opgeslagen in het knoop punt voor dispatcher niet worden gesynchroniseerd met andere werk knooppunten. Als er een failover optreedt van het knoop punt van de verzender naar het worker-knoop punt, maar de referenties zich alleen in het vorige dispatcher-knoop punt bevonden, mislukt de taak wanneer er wordt geprobeerd om toegang te krijgen tot referenties.

#### <a name="resolution"></a>Oplossing

De enige manier om dit probleem te voor komen, is om ervoor te zorgen dat twee knoop punten de synchronisatie status van referenties hebben. Anders moet u de referenties voor de nieuwe dispatcher opnieuw invoeren.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Kan het certificaat niet kiezen vanwege een ontbrekende persoonlijke sleutel

#### <a name="symptoms"></a>Symptomen

1.  Een PFX-bestand importeren in het certificaat archief.
2.  Wanneer u het certificaat selecteert via de IR-Configuration Manager gebruikers interface, hebt u de volgende fout melding:

    ![Persoonlijke sleutel ontbreekt](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Oorzaak

- Het gebruikers account heeft een beperkte bevoegdheid en heeft geen toegang tot de persoonlijke sleutel.
- Het certificaat is gegenereerd als hand tekening, maar niet als sleutel uitwisseling.

#### <a name="resolution"></a>Oplossing

1.  Gebruik een privileged account dat toegang heeft tot de persoonlijke sleutel om de gebruikers interface te kunnen gebruiken.
2.  Voer de onderstaande opdracht uit om het certificaat te importeren:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Zelf-hostende IR-installatie

### <a name="the-integration-runtime-registration-error"></a>De Integration Runtime registratie fout 

#### <a name="symptoms"></a>Symptomen

Soms willen we zelf-hostende IR uitvoeren in een ander account om de volgende redenen:
- Het service account wordt niet toegestaan door het bedrijfs beleid.
- Sommige verificatie is vereist.

Nadat u het service account in het deel venster service hebt gewijzigd, kunt u zien dat de Integration Runtime niet meer werkt.

![IR-registratie fout](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Oorzaak

Er zijn veel resources die alleen aan het service account worden verleend. Wanneer het service account wordt gewijzigd in een ander account, blijft de machtiging van alle afhankelijke resources hetzelfde.

#### <a name="resolution"></a>Oplossing

Ga naar het gebeurtenis logboek van Integration Runtime om de fout te controleren.

![IR-gebeurtenis logboek](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Als de fout wordt weer gegeven zoals boven *UnauthorizedAccessException*, volgt u de onderstaande instructies:


1. Controleer *DIAHostService* Logon service-account in het venster van de Windows-service.

    ![Aanmeldings service account](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Controleer of het aanmeldings service account de machtiging R/W heeft via de map: *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway*.

    - Als het account voor de service aanmelding niet is gewijzigd, moet dit standaard de machtiging R/W hebben.

        ![Service machtiging](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Als u het account voor aanmelding bij de service hebt gewijzigd, volgt u de onderstaande stappen om het probleem te verhelpen:
        1. Verwijder de huidige zelf-hostende IR.
        1. Installeer de zelf-hostende IR-bits.
        1. Volg de onderstaande instructies om het service account te wijzigen: 
            1. Ga naar de installatiemap van selfhosted IR, ga naar de map: *micro soft Integration Runtime\4.0\Shared*.
            1. Start een opdracht regel met verhoogde bevoegdheden. Vervang *\<user>* en *\<password>* door uw eigen gebruikers naam en wacht woord en voer de volgende opdracht uit:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Als u wilt overschakelen naar de LocalSystem-account, moet u ervoor zorgen dat u de juiste indeling voor dit account gebruikt. Hieronder ziet u een voor beeld van de juiste indeling:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Gebruik **niet** de indeling zoals hieronder wordt weer gegeven:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. Omdat het lokale systeem een hogere bevoegdheid heeft dan de beheerder, kunt u dit ook rechtstreeks wijzigen in ' Services '.
            1. U kunt de lokale/domein-gebruiker voor het aanmeldings account van de IR-service gebruiken.            
        1. Registreer de Integration Runtime.

Als de fout er als volgt uitziet: de *service ' Integration runtime service ' (DIAHostService) is niet gestart. Controleer of u voldoende rechten hebt om systeem services te starten*. Volg de onderstaande instructies:

1. Controleer *DIAHostService* Logon service-account in het venster van de Windows-service.
   
    ![Aanmeldings service account](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Controleer of het aanmeldings service account de machtiging **Aanmelden als service** heeft om Windows-service te starten:

    ![Aanmelden als service](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Meer informatie

Als er in uw geval geen van beide twee patronen in de oplossing van toepassing zijn, probeert u de onderstaande Windows-gebeurtenis logboeken te verzamelen: 
- Logboeken toepassingen en services-> Integration Runtime
- Windows-logboeken-> toepassing

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Kan de registratie knop niet vinden om een zelf-hostende IR te registreren    

#### <a name="symptoms"></a>Symptomen

De **registratie** knop is niet gevonden in de Configuration Manager gebruikers interface bij het registreren van een zelf-hostende IR.

![Geen registratie knop](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Oorzaak

Sinds de release van de *Integration Runtime 3,0*is de knop **registreren** op een bestaand Integration runtime knooppunt verwijderd om een schone en veiligere omgeving mogelijk te maken. Als er een knoop punt is geregistreerd bij een aantal Integration Runtime (of dit nu online is of niet), moet u het vorige knoop punt verwijderen en vervolgens het knoop punt installeren en registreren om het opnieuw te registreren bij een andere Integration Runtime.

#### <a name="resolution"></a>Oplossing

1. Ga naar het configuratie scherm om de bestaande Integration Runtime te verwijderen.

    > [!IMPORTANT] 
    > Selecteer in het onderstaande proces de optie Ja. Bewaar geen gegevens tijdens het verwijderen van het proces.

    ![Gegevens verwijderen](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Als u het MSI-installatie programma voor Integration runtime niet hebt, gaat u naar het [Download centrum](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) om de nieuwste Integration runtime te downloaden.
1. Installeer het MSI-bestand en registreer de Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Kan de zelf-hostende IR niet registreren vanwege localhost    

#### <a name="symptoms"></a>Symptomen

Kan de zelf-hostende IR niet registreren op een nieuwe machine wanneer get_LoopbackIpOrName.

**Fout opsporing:** Er is een runtime fout opgetreden.
De type-initialisatie functie voor micro soft. DataTransfer. DIAgentHost. DataSourceCache heeft een uitzonde ring veroorzaakt.
Er is een onherstelbare fout opgetreden tijdens het zoeken in de data base.
 
**Uitzonderings Details:** System. TypeInitializationException: de type initialisatie functie voor micro soft. DataTransfer. DIAgentHost. DataSourceCache heeft een uitzonde ring veroorzaakt. ---> System .net. sockets. SocketException: er is een niet-herstel bare fout opgetreden tijdens het zoeken in de Data Base op het systeem .net. DNS. GetAddrInfo (String name).

#### <a name="cause"></a>Oorzaak

Het probleem treedt meestal op bij het oplossen van localhost.

#### <a name="resolution"></a>Oplossing

Gebruik localhost 127.0.0.1 om het bestand te hosten en los het probleem op.


### <a name="self-hosted-setup-failed"></a>Zelf-hostende installatie mislukt    

#### <a name="symptoms"></a>Symptomen

Kan geen bestaande IR verwijderen of een nieuwe IR installeren of een bestaande IR upgraden naar een nieuwe IR.

#### <a name="cause"></a>Oorzaak

De installatie is afhankelijk van de Windows Installer-service. Er zijn varianten die een installatie probleem kunnen veroorzaken:
- Onvoldoende schijf ruimte
- Geen machtigingen
- De NT-service is om een of andere reden vergrendeld
- Het CPU-gebruik is te hoog
- MSI-bestand wordt gehost op een trage netwerk locatie
- Sommige systeem bestanden of registers zijn onbedoeld gerakend


## <a name="self-hosted-ir-connectivity-issues"></a>Zelf-hostende IR-verbindings problemen

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Zelf-hostende Integration runtime kan geen verbinding maken met de Cloud service

#### <a name="symptoms"></a>Symptomen

![Probleem met de zelf-hosted IR-verbinding](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Oorzaak 

De zelf-hostende Integration runtime kan geen verbinding maken met de Data Factory Service (back-end). Dit probleem wordt meestal veroorzaakt door netwerk instellingen in de firewall.

#### <a name="resolution"></a>Oplossing

1. Controleer of de service Integration runtime actief is.
    
   ![Zelf-hostende status van de IR-service](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Als de service wordt uitgevoerd, gaat u naar stap 3.

1. Als er geen proxy is geconfigureerd voor de zelf-hostende Integration runtime (dit is de standaard instelling), voert u de volgende Power shell-opdracht uit op de computer waarop de zelf-hostende Integration runtime is geïnstalleerd:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > De service-URL kan variëren, afhankelijk van de locatie van uw Data Factory. U kunt de service-URL vinden onder **ADF UI**  >  **Connections**  >  **Integration Runtimes**  >  **bewerken zelf-hostende IR**-  >  **knoop punten**  >  **bekijken service-url's**.
            
    Hier volgt de verwachte reactie:
            
    ![Power shell-opdracht antwoord](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Als u het verwachte antwoord niet ontvangt, gebruikt u een van de volgende methoden, afhankelijk van uw situatie:
            
    * Als er een bericht wordt weer gegeven dat de externe naam niet kan worden omgezet, is er een probleem met de Domain Name System (DNS). Neem contact op met uw netwerk team om dit probleem op te lossen.
    * Als er een bericht wordt weer gegeven dat het SSL/TLS-certificaat niet wordt vertrouwd, controleert u of het certificaat voor https://wu2.frontend.clouddatahub.net/ wordt vertrouwd op de computer en installeert u vervolgens het open bare certificaat met behulp van certificaat beheer. Deze actie moet het probleem verminderen.
    * Ga naar **Windows**logboeken  >  toepassingen en services van Windows **(Logboeken)**  >  **Applications and Services Logs**  >  **Integration runtime** en controleer op fouten die worden veroorzaakt door DNS, een firewall regel of instellingen van het bedrijfs netwerk. (Als u een dergelijke fout vindt, sluit u de verbinding af.) Omdat elk bedrijf aangepaste netwerk instellingen heeft, neemt u contact op met uw netwerk team om deze problemen op te lossen.

1. Als "proxy" is geconfigureerd op de zelf-hostende Integration runtime, controleert u of de proxy server toegang heeft tot het service-eind punt. Zie [Power shell, webaanvragen en proxy's](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)voor een voor beeld van een opdracht.    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Hier volgt de verwachte reactie:
            
![Power shell-opdracht antwoord 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Overwegingen voor de proxy:
> *    Controleer of de proxy server moet worden geplaatst in de lijst met veilige geadresseerden. Als dit het geval is, moet u ervoor zorgen dat [deze domeinen](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) zich in de lijst met veilige ontvangers bevinden.
> *    Controleer of het TLS/SSL-certificaat "wu2.frontend.clouddatahub.net/" wordt vertrouwd op de proxy server.
> *    Als u Active Directory verificatie gebruikt op de proxy, wijzigt u het service account in het gebruikers account dat toegang heeft tot de proxy als ' Integration Runtime-service.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Fout bericht: zelf-hostende Integration runtime-knoop punt/logische SHIR is in inactief/' wordt uitgevoerd (beperkt) '

#### <a name="cause"></a>Oorzaak 

Het zelf-hostende geïntegreerde runtime knooppunt heeft mogelijk een **inactieve** status, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Niet-actief zelf-Hostend IR-knoop punt](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Dit gedrag treedt op wanneer knoop punten niet met elkaar kunnen communiceren.

#### <a name="resolution"></a>Oplossing

1. Meld u aan bij de door het knoop punt gehoste VM. Open in de **Logboeken toepassingen en services**  >  **Integration runtime**logboeken en filter alle fouten Logboeken.

1. Controleer of een fouten logboek de volgende fout bevat: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Als u deze fout ziet, voert u het volgende uit op de opdracht regel: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Als het volgende fout bericht wordt weer gegeven, neemt u contact op met uw IT-afdeling voor hulp bij het oplossen van dit probleem. Wanneer u met succes kunt Telnet, neemt u contact op met Microsoft Ondersteuning als u nog steeds problemen ondervindt met de status van het integrale runtime-knoop punt.
        
   ![Fout met opdracht regel](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Controleer of het fouten logboek het volgende bevat:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Probeer een of beide van de volgende methoden om het probleem op te lossen:
    - Plaats alle knoop punten in hetzelfde domein.
    - Voeg het IP-adres toe aan de host toewijzing in alle host-bestanden van de gehoste VM.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Connectiviteits probleem tussen zelf-hostende IR en Data Factory of zelf-hostende IR en gegevens bron/Sink

Als u het probleem met de netwerk verbinding wilt oplossen, moet u weten hoe u [de netwerk tracering kunt verzamelen](#how-to-collect-netmon-trace), hoe u deze kunt gebruiken en hoe u [de netmon-tracering moet analyseren](#how-to-analyze-netmon-trace) voordat u de hulpprogram ma's voor netmon in real cases van zelf-hostende IR toepast.

#### <a name="symptoms"></a>Symptomen

Soms kunnen de verbindings problemen, zoals hieronder, worden opgelost tussen een zelf-hostende IR en Data Factory: 

![HTTP-aanvraag is mislukt](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Of de ene tussen zelf-hostende IR en gegevens bron/sink, zal de volgende fouten tegen komen:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Oplossing:

Raadpleeg de volgende instructies als u meer problemen ondervindt:

Neem de netmon-tracering en analyseer verder.
- Ten eerste kunt u het filter zo instellen dat er een opnieuw instellen wordt weer geven van de server naar de client zijde. In het onderstaande voor beeld ziet u dat de server is Data Factory server.

    ![Data Factory-server](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Wanneer u het pakket opnieuw instellen krijgt, kunt u het gesprek vinden door TCP te volgen.

    ![Gesprek zoeken](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Daarna kunt u de conversie tussen client en Data Factory server hieronder ophalen door het filter te verwijderen.

    ![Gesprek ontvangen](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Op basis van de verzamelde netmon-tracering kunnen we zien dat het TTL-totaal (TimeToLive) 64 is. Volgens de **standaard waarden voor TTL en hops** die in [dit artikel](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) worden genoemd (zoals hieronder geëxtraheerd), kunnen we zien dat het het Linux-systeem is dat het pakket opnieuw instelt, waardoor de verbinding wordt verbroken.

    De waarden voor de standaard-TTL en-hop variëren tussen de verschillende besturings systemen, en dit zijn de standaard instellingen voor enkele:
    - Linux kernel 2,4 (circa 2001): 255 voor TCP, UDP en ICMP
    - Linux kernel 4,10 (2015): 64 voor TCP, UDP en ICMP
    - Windows XP (2001): 128 voor TCP, UDP en ICMP
    - Windows 10 (2015): 128 voor TCP, UDP en ICMP
    - Windows Server 2008:128 voor TCP, UDP en ICMP
    - Windows Server 2019 (2018): 128 voor TCP, UDP en ICMP
    - macOS (2001): 64 voor TCP, UDP en ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Het wordt echter weer gegeven als 61 in plaats van 64 in het bovenstaande voor beeld, omdat wanneer het netwerk pakket naar de bestemming bereikt, de verschillende hops moeten door lopen, zoals routers/netwerk apparaten. Het aantal routers/netwerk apparaten wordt in mindering gebracht in de laatste TTL.
    In dit geval kunnen we zien dat opnieuw instellen kan worden verzonden vanuit Linux-systeem met TTL 64.

- We moeten de vierde hop van de zelf-hostende IR controleren om te bevestigen dat het apparaat opnieuw wordt ingesteld.
 
    *Netwerk pakket van Linux-systeem A met TTL 64-> B TTL 64 min 1 = 63-> C TTL 63 min 1 = 62-> TTL 62 min 1 = 61 zelf-hostende IR*

- In ideale situatie is de TTL 128, wat betekent dat Windows System de Data Factory uitvoert. Zoals u in het onderstaande voor beeld ziet, *128 – 107 = 21 hops*, wat betekent dat er 21 hops voor het pakket zijn verzonden van Data Factory naar een zelf-hostende IR tijdens de TCP 3-handshake.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Daarom moet u het netwerk team om te controleren wat de vierde hop is van zelf-hostende IR. Als dit de firewall is als Linux-systeem, controleert u de logboeken op de reden dat het apparaat het pakket opnieuw instelt na de TCP 3-handshake. Als u echter niet zeker weet waar u moet onderzoeken, probeert u de netmon-tracering van de zelf-hostende IR en firewall samen te halen tijdens de problematische tijd om te achterhalen op welk apparaat dit pakket kan worden teruggezet en wordt de verbinding verbroken. In dit geval moet u ook uw netwerk team laten door lopen.

### <a name="how-to-collect-netmon-trace"></a>Netmon-tracering verzamelen

1.  Down load de Hulpprogram Ma's voor netmon van [deze website](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)en installeer deze op uw server computer (ongeacht welke server het probleem heeft) en de client (zoals zelf-hostende IR).

2.  Maak een map, bijvoorbeeld in het volgende pad: *D:\netmon*. Zorg ervoor dat er voldoende ruimte is om het logboek op te slaan.

3.  Leg de IP-en poort gegevens vast. 
    1. Start een opdracht prompt.
    2. Selecteer als administrator uitvoeren en voer de volgende opdracht uit:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Leg de netmon-tracering (netwerk pakket) vast.
    1. Start een opdracht prompt.
    2. Selecteer als administrator uitvoeren en voer de volgende opdracht uit:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. U kunt drie verschillende opdrachten gebruiken om de netwerk pagina vast te leggen:
        - Optie A: RoundRobin-bestands opdracht (Hiermee wordt slechts één bestand vastgelegd en worden oude logboeken overschreven).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Optie B: geketende bestands opdracht (Hiermee wordt een nieuw bestand gemaakt als 200 MB is bereikt).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Optie C: opdracht gepland bestand.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Druk op **CTRL + C** om te stoppen met het vastleggen van de netmon-tracering.
 
> [!NOTE]
> Als u de netmon-tracering op de client computer alleen kunt verzamelen, moet u het IP-adres van de server ophalen om de tracering te analyseren.

### <a name="how-to-analyze-netmon-trace"></a>Netmon-tracering analyseren

Wanneer u probeert Telnet **8.8.8.8 888** te maken met de bovenstaande netmon-tracering, moet u de onderstaande tracering zien:

![netmon-tracering 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon-tracering 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Dit betekent dat u geen TCP-verbinding met de **8.8.8.8** -server kunt maken op basis van de poort **888**, zodat u daar twee extra pakketten van **SynReTransmit** ziet. Omdat de **Self-HOST2** van de bron geen verbinding kan maken met **8.8.8.8** bij het eerste pakket, blijft deze verbinding maken.

> [!TIP]
> - U kunt klikken op standaard filter voor **Load filter**  ->  **Standard Filter**  ->  **adressen**  ->  **IPv4-adressen**.
> - Voer **IPv4. Address = = 8.8.8.8** in als filter en klik op **Toep assen**. Daarna ziet u alleen de communicatie van de lokale computer naar de doel- **8.8.8.8**.

![filter adressen 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filter adressen 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

In het onderstaande voor beeld ziet u hoe een geschikt scenario eruit zou zien. 

- Als Telnet **8.8.8.8 53** zonder problemen werkt, kunt u de TCP 3-Handshake bekijken en vervolgens de sessie met TCP 4-Handshake volt ooien.

    ![goed scenario-voor beeld 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![goed scenario-voor beeld 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Op basis van de bovenstaande TCP 3-Handshake kunt u de onderstaande werk stroom zien:

    ![TCP 3 Handshake-werk stroom](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- De TCP 4-Handshake voor het volt ooien van de sessie en de werk stroom worden als volgt weer gegeven:

    ![TCP 4-Handshake](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4-Handshake-werk stroom](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="self-hosted-ir-sharing"></a>Zelf-hostende IR-deling

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Zelf-hostende IR van een andere Tenant delen wordt niet ondersteund 

#### <a name="symptoms"></a>Symptomen

U kunt andere gegevens fabrieken (op verschillende tenants) zien tijdens het delen van de zelf-hostende IR vanuit Azure Data Factory gebruikers interface, maar u kunt de zelf-hostende IR niet delen voor gegevens fabrieken die zich op verschillende tenants bevinden.

#### <a name="cause"></a>Oorzaak

De zelf-hostende IR kan geen gedeelde cross-tenants zijn.


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids gegevens stromen toewijzen](concepts-data-flow-performance.md)
