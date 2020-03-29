---
title: Hoge beschikbaarheid met datamanagementgateway in Azure Data Factory
description: In dit artikel wordt uitgelegd hoe u een gateway voor gegevensbeheer uitschalen door meer knooppunten toe te voegen en op te schalen door het aantal gelijktijdige taken te verhogen dat op een knooppunt kan worden uitgevoerd.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4ee89f4bba70bb5e81eef21247d556f65a2a1f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065197"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway - hoge beschikbaarheid en schaalbaarheid (Preview)
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [zelf gehoste runtime voor integratie in](../create-self-hosted-integration-runtime.md). 


In dit artikel u een oplossing voor hoge beschikbaarheid en schaalbaarheid configureren met Data Management Gateway/ Integratie.    

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u al bekend bent met de basisprincipes van Integration Runtime (Earlier Data Management Gateway). Als u dit niet bent, raadpleegt u [Data Management Gateway](data-factory-data-management-gateway.md).
> 
> **Deze preview-functie wordt officieel ondersteund op Data Management Gateway versie 2.12.xxxx.x en hoger**. Zorg ervoor dat u versie 2.12.xxxx.x of hoger gebruikt. Download [hier](https://www.microsoft.com/download/details.aspx?id=39717)de nieuwste versie van Data Management Gateway.

## <a name="overview"></a>Overzicht
U datamanagementgateways die op meerdere on-premises machines zijn geïnstalleerd, koppelen aan één logische gateway van de portal. Deze machines worden **knooppunten**genoemd. U maximaal **vier knooppunten** hebben die zijn gekoppeld aan een logische gateway. De voordelen van het hebben van meerdere knooppunten (on-premises machines met gateway geïnstalleerd) voor een logische gateway zijn:  

- Verbeter de prestaties van gegevensverplaatsingen tussen on-premises en clouddatastores.  
- Als een van de knooppunten om de een of andere reden naar beneden gaat, zijn er nog steeds andere knooppunten beschikbaar voor het verplaatsen van de gegevens. 
- Als een van de knooppunten offline moet worden gehaald voor onderhoud, zijn er nog steeds andere knooppunten beschikbaar voor het verplaatsen van de gegevens.

U ook het aantal **gelijktijdige gegevensverplaatsingstaken** configureren dat op een knooppunt kan worden uitgevoerd om de mogelijkheid op te schalen om gegevens te verplaatsen tussen on-premises en cloudgegevensopslag. 

Met behulp van de Azure-portal u de status van deze knooppunten controleren, zodat u beslissen of u een knooppunt wilt toevoegen of verwijderen uit de logische gateway. 

## <a name="architecture"></a>Architectuur 
Het volgende diagram biedt het architectuuroverzicht van de schaalbaarheids- en beschikbaarheidsfunctie van de Data Management Gateway: 

![Data Management Gateway - Hoge beschikbaarheid en schaalbaarheid](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Een **logische gateway** is de gateway die u toevoegt aan een gegevensfabriek in de Azure-portal. Eerder kon u slechts één on-premises Windows-machine koppelen aan Data Management Gateway die is geïnstalleerd met een logische gateway. Deze on-premises gateway machine wordt een knooppunt genoemd. Nu u maximaal **vier fysieke knooppunten** koppelen aan een logische gateway. Een logische gateway met meerdere knooppunten wordt een **multi-node gateway**genoemd.  

Al deze knooppunten zijn **actief.** Ze kunnen allemaal gegevensverplaatsingstaken verwerken om gegevens te verplaatsen tussen on-premises en clouddatastores. Een van de knooppunten fungeren als zowel dispatcher als werknemer. Andere knooppunten in de groepen zijn werkknooppunten. Een **dispatcherknooppunt** haalt taken/taken voor gegevensverkeer uit de cloudservice en verzendt deze naar werknemersknooppunten (inclusief zichzelf). Een **werknemersknooppunt** voert taken voor gegevensverplaatsing uit om gegevens te verplaatsen tussen on-premises en cloudgegevensopslag. Alle knooppunten zijn werknemers. Slechts één knooppunt kan zowel verzending als werknemer zijn.    

U meestal beginnen met één knooppunt en **uitschalen** om meer knooppunten toe te voegen, omdat de bestaande knooppunt(s) worden overweldigd door de belasting van gegevensverplaatsingen. U ook de gegevensverplaatsingsmogelijkheid van een gatewayknooppunt **opschalen** door het aantal gelijktijdige taken te verhogen dat op het knooppunt mag worden uitgevoerd. Deze mogelijkheid is ook beschikbaar met één node gateway (zelfs als de schaalbaarheids- en beschikbaarheidsfunctie niet is ingeschakeld). 

Een gateway met meerdere knooppunten houdt de gegevensarchiefreferenties gesynchroniseerd op alle knooppunten. Als er een probleem is met de verbindingsverbinding tussen knooppunt en knooppunt, kunnen de referenties niet synchroon lopen. Wanneer u referenties instelt voor een on-premises gegevensarchief dat een gateway gebruikt, worden referenties opgeslagen op het verzender-/werknemersknooppunt. Het verzendknooppunt synchroniseert met andere werkknooppunten. Dit proces staat bekend als **referenties synchroniseren**. Het communicatiekanaal tussen knooppunten kan worden **versleuteld** met een openbaar SSL/TLS-certificaat. 

## <a name="set-up-a-multi-node-gateway"></a>Een gateway met meerdere nodes instellen
In deze sectie wordt ervan uitgegaan dat u de volgende twee artikelen hebt doorlopen of bekend bent met concepten in deze artikelen: 

- [Data Management Gateway](data-factory-data-management-gateway.md) - biedt een gedetailleerd overzicht van de gateway.
- [Gegevens verplaatsen tussen on-premises en clouddatastores](data-factory-move-data-between-onprem-and-cloud.md) - bevat een walkthrough met stapsgewijze instructies voor het gebruik van een gateway met één knooppunt.  

> [!NOTE]
> Voordat u een datamanagementgateway installeert op een on-premises Windows-machine, raadpleegt u vereisten in [het hoofdartikel](data-factory-data-management-gateway.md#prerequisites).

1. Schakel in de [walkthrough](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)een logische gateway in, schakel de functie **Hoge beschikbaarheid & schaalbaarheid** in. 

    ![Data Management Gateway - maak hoge beschikbaarheid en schaalbaarheid mogelijk](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Gebruik op de pagina **Configureren** een koppeling **Express Setup** of **Handmatig instellen** om een gateway te installeren op het eerste knooppunt (een on-premises Windows-machine).

    ![Data Management Gateway - express of handmatig instellen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Als u de optie expresinstellingen gebruikt, wordt de communicatie tussen knooppunt en knooppunt uitgevoerd zonder versleuteling. De naam van het knooppunt is hetzelfde als de naam van de machine. Gebruik handmatige installatie als de knooppuntcommunicatie moet worden versleuteld of als u een knooppuntnaam van uw keuze wilt opgeven. Namen van knooppunts kunnen later niet worden bewerkt.
3. Als u kiest voor **express setup**
    1. U ziet het volgende bericht nadat de gateway is geïnstalleerd:

        ![Data Management Gateway - express setup succes](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Start Data Management Configuration Manager voor de gateway door deze instructies op [te volgen.](data-factory-data-management-gateway.md#configuration-manager) U ziet de naam van de gateway, knooppuntnaam, status, enz.

        ![Data Management Gateway - installatie geslaagd](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Als u kiest voor **handmatige installatie:**
    1. Download het installatiepakket van het Microsoft Download Center en voer het uit om de gateway op uw machine te installeren.
    2. Gebruik de **verificatiesleutel** van de pagina **Configureren** om de gateway te registreren.
    
        ![Data Management Gateway - installatie geslaagd](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. In de pagina **Nieuw gatewayknooppunt** u een aangepaste **naam** aan het gatewayknooppunt opgeven. Standaard is de naam van het knooppunt hetzelfde als de naam van de machine.    

        ![Data Management Gateway - naam opgeven](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Op de volgende pagina u kiezen of **u versleuteling wilt inschakelen voor knooppuntcommunicatie**. Klik **op Overslaan** om versleuteling uit te schakelen (standaard).

        ![Data Management Gateway - versleuteling inschakelen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Het wijzigen van de versleutelingsmodus wordt alleen ondersteund wanneer u één gatewayknooppunt in de logische gateway hebt. Als u de versleutelingsmodus wilt wijzigen wanneer een gateway meerdere knooppunten heeft, voert u de volgende stappen uit: verwijder alle knooppunten behalve één knooppunt, wijzig de versleutelingsmodus en voeg de knooppunten opnieuw toe.
        > 
        > Zie de sectie [VEREISTEN voor TLS/SSL-certificaten](#tlsssl-certificate-requirements) voor een lijst met vereisten voor het gebruik van een TLS/SSL-certificaat. 
    5. Nadat de gateway is geïnstalleerd, klikt u op Configuratiebeheer starten:
    
        ![Handmatiginstellen - configuratiebeheer starten](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. u ziet Data Management Gateway Configuration Manager op het knooppunt (on-premises Windows-machine), waarin de verbindingsstatus, **de naam van de gateway**en de naam van het knooppunt worden **weergegeven.**  

        ![Data Management Gateway - installatie geslaagd](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Als u de gateway indient op een Azure VM, u [deze azure resource manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway)gebruiken. Dit script creëert een logische gateway, stelt VM's in met datamanagementgatewaysoftware geïnstalleerd en registreert deze met de logische gateway. 
6. Start in Azure-portal de **gatewaypagina:** 
    1. Klik op de startpagina van de gegevensfabriek in de portal op **Gekoppelde services**.
    
        ![Startpagina van de gegevensfactory](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. selecteer de **gateway** om de **gatewaypagina** te bekijken:
    
        ![Startpagina van de gegevensfactory](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. U ziet de **gatewaypagina:**   

        ![Gateway met één knooppuntweergave](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klik **op Knooppunt toevoegen** op de werkbalk om een knooppunt toe te voegen aan de logische gateway. Als u van plan bent om express setup te gebruiken, doe deze stap vanaf de on-premises machine die zal worden toegevoegd als een knooppunt aan de gateway. 

    ![Data Management Gateway - knooppuntmenu toevoegen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Stappen zijn vergelijkbaar met het instellen van het eerste knooppunt. Met de gebruikersinterface voor Configuratiebeheer u de naam van het knooppunt instellen als u de optie handmatige installatie kiest: 

    ![Configuratiebeheer - tweede gateway installeren](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Nadat de gateway op het knooppunt is geïnstalleerd, wordt het volgende scherm weergegeven met het gereedschap Configuratiebeheer:  

    ![Configuratiebeheer - installeer tweede gateway succesvol](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Als u de **gatewaypagina** in de portal opent, ziet u nu twee gatewayknooppunten: 

    ![Gateway met twee knooppunten in de portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Als u een gatewayknooppunt wilt verwijderen, klikt u op **Knooppunt verwijderen** op de werkbalk, selecteert u het knooppunt dat u wilt verwijderen en klikt u vervolgens op **Verwijderen** vanaf de werkbalk. Met deze actie wordt het geselecteerde knooppunt uit de groep verwijderd. Houd er rekening mee dat deze actie de software voor gegevensbeheergateways niet van het knooppunt verwijdert (on-premises Windows-machine). Gebruik **Programma's toevoegen of verwijderen** in het Configuratiescherm op de on-premises om de gateway te verwijderen. Wanneer u de gateway van het knooppunt verwijdert, wordt deze automatisch verwijderd in de portal.   

## <a name="upgrade-an-existing-gateway"></a>Een bestaande gateway upgraden
U een bestaande gateway upgraden om de functie voor hoge beschikbaarheid en schaalbaarheid te gebruiken. Deze functie werkt alleen met knooppunten met de datamanagementgateway van versie >= 2.12.xxxx. U de versie van de gegevensbeheergateway zien die op een machine is geïnstalleerd op het tabblad **Help** van het Configuratiebeheer voor gegevensbeheergateway. 

1. Werk de gateway op de on-premises machine bij naar de nieuwste versie door een MSI-installatiepakket te downloaden en uit te voeren vanuit het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=39717) Zie [installatiesectie](data-factory-data-management-gateway.md#installation) voor meer informatie.  
2. Ga naar Azure Portal. Start de **pagina Data Factory** voor uw gegevensfabriek. Klik op De tegel Gekoppelde services om de **pagina gekoppelde services**te starten . Selecteer de gateway om de **gatewaypagina**te starten. Klik op de **voorbeeldfunctie** en schakel deze in zoals in de volgende afbeelding: 

    ![Data Management Gateway - preview-functie inschakelen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Zodra de voorbeeldfunctie is ingeschakeld in de portal, sluit u alle pagina's. Open de **gatewaypagina** opnieuw om de nieuwe gebruikersinterface (UI) te bekijken.
 
    ![Data Management Gateway - preview-functiesucces inschakelen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway - voorbeeld van gebruikersinterface](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Tijdens de upgrade is de naam van het eerste knooppunt de naam van de machine. 
3. Voeg nu een knooppunt toe. Klik **op** de pagina Gateway op **Knooppunt toevoegen**.  

    ![Data Management Gateway - knooppuntmenu toevoegen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Volg de instructies uit de vorige sectie om het knooppunt in te stellen. 

### <a name="installation-best-practices"></a>Best practices voor installatie

- Configureer het energiebeheerschema op de hostmachine voor de gateway, zodat de machine niet overwintert. Als de hostmachine overwintert, reageert de gateway niet op gegevensverzoeken.
- Een back-up maken van het certificaat dat aan de gateway is gekoppeld.
- Zorg ervoor dat alle knooppunten een vergelijkbare configuratie hebben (aanbevolen) voor ideale prestaties. 
- Voeg ten minste twee knooppunten toe om een hoge beschikbaarheid te garanderen.  

### <a name="tlsssl-certificate-requirements"></a>VEREISTEN voor TLS/SSL-certificaten
Hier volgen de vereisten voor het TLS/SSL-certificaat dat wordt gebruikt voor het beveiligen van communicatie tussen runtime-knooppunten voor integratie:

- Het certificaat moet een openbaar vertrouwd X509 v3-certificaat zijn. We raden u aan certificaten te gebruiken die zijn uitgegeven door een openbare (externe) certificeringsinstantie (CA).
- Elk knooppunt voor nalooptijd van de integratie moet dit certificaat vertrouwen, evenals de clientmachine waarop de toepassing voor referentiebeheer wordt uitgevoerd. 
  > [!NOTE]
  > De toepassing Credential manager wordt gebruikt terwijl de referenties veilig worden ingesteld vanuit Wizard Kopiëren/ Azure Portal. En dit kan worden afgevuurd vanuit elke machine binnen hetzelfde netwerk als de on-premises / private data store.
- Wild card certificaten worden ondersteund. Als uw FQDN-naam **node1.domain.contoso.com**is, u ***.domain.contoso.com** gebruiken als onderwerpnaam van het certificaat.
- SAN-certificaten worden niet aanbevolen omdat alleen het laatste item van de alternatieve namen van het onderwerp wordt gebruikt en alle andere worden genegeerd vanwege de huidige beperking. Bijvoorbeeld u beschikt over een SAN-certificaat waarvan de SAN **node1.domain.contoso.com** en **node2.domain.contoso.com,** u dit cert alleen gebruiken op een machine waarvan de FQDN **node2.domain.contoso.com**is.
- Ondersteunt elke sleutelgrootte die wordt ondersteund door Windows Server 2012 R2 voor TLS/SSL-certificaten.
- Certificaat met CNG-sleutels wordt niet ondersteund.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>FAQ: Wanneer zou ik deze versleuteling niet inschakelen?
Het inschakelen van versleuteling kan bepaalde kosten toevoegen aan uw infrastructuur (eigenaar van een openbaar certificaat), zodat u versleuteling in de onderstaande gevallen overslaan:
- Wanneer de inburgeringsruntime wordt uitgevoerd op een vertrouwd netwerk of een netwerk met transparante versleuteling zoals IP/SEC. Aangezien deze kanaalcommunicatie slechts beperkt is binnen uw vertrouwde netwerk, hebt u mogelijk geen extra versleuteling nodig.
- Wanneer de inlooptijd van de integratie niet wordt uitgevoerd in een productieomgeving. Dit kan helpen bij het verlagen van tls/ SSL-certificaatkosten.


## <a name="monitor-a-multi-node-gateway"></a>Een gateway met meerdere node's bewaken
### <a name="multi-node-gateway-monitoring"></a>Gatewaybewaking met meerdere nodes
In de Azure-portal u bijna realtime momentopnamevan resourcegebruik (CPU, geheugen, netwerk(in/uit), enz.) op elk knooppunt bekijken, samen met statussen van gatewayknooppunten. 

![Data Management Gateway - bewaking van meerdere nodes](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

U kunt **geavanceerde instellingen** op de **gatewaypagina** inschakelen om geavanceerde statistieken te bekijken, zoals **Netwerk**(in/uit), Functie **&-referentiestatus**, wat handig is bij het opsporen van gatewayproblemen en gelijktijdige **taken** (lopend/ limiet) die kunnen worden gewijzigd/ dienovereenkomstig kan worden gewijzigd tijdens het afstemmen van de prestaties. In de volgende tabel vindt u beschrijvingen van kolommen in de lijst **Gatewayknooppunten:**  

Controle eigenschap | Beschrijving
:------------------ | :---------- 
Name | Naam van de logische gateway en knooppunten die zijn gekoppeld aan de gateway.  
Status | Status van de logische gateway en de gatewayknooppunten. Voorbeeld: Online/Offline/Limited/etc. Zie sectie [Gatewaystatus](#gateway-status) voor informatie over deze statussen. 
Versie | Toont de versie van de logische gateway en elk gatewayknooppunt. De versie van de logische gateway wordt bepaald op basis van de versie van de meerderheid van de knooppunten in de groep. Als er knooppunten met verschillende versies in de logische gateway-instelling zijn, zijn alleen de knooppunten met hetzelfde versienummer als de logische gatewayfunctie naar behoren. Anderen bevinden zich in de beperkte modus en moeten handmatig worden bijgewerkt (alleen voor het geval de automatische update mislukt). 
Beschikbaar geheugen | Beschikbaar geheugen op een gatewayknooppunt. Deze waarde is een bijna realtime momentopname. 
CPU-gebruik | CPU-gebruik van een gatewayknooppunt. Deze waarde is een bijna realtime momentopname. 
Netwerken (In/Uit) | Netwerkgebruik van een gatewayknooppunt. Deze waarde is een bijna realtime momentopname. 
Gelijktijdige taken (lopend/limiet) | Aantal taken of taken die op elk knooppunt worden uitgevoerd. Deze waarde is een bijna realtime momentopname. Limit betekent de maximale gelijktijdige taken voor elk knooppunt. Deze waarde wordt gedefinieerd op basis van de grootte van de machine. U de limiet verhogen om gelijktijdige taakuitvoering op te schalen in geavanceerde scenario's, waarbij cpu/geheugen/ netwerk onderbenut is, maar activiteiten worden uitgevoerd. Deze mogelijkheid is ook beschikbaar met één node gateway (zelfs als de schaalbaarheids- en beschikbaarheidsfunctie niet is ingeschakeld). Zie [sectie schaaloverwegingen](#scale-considerations) voor meer informatie. 
Rol | Er zijn twee soorten rollen : Dispatcher en werknemer. Alle knooppunten zijn werknemers, wat betekent dat ze allemaal kunnen worden gebruikt om taken uit te voeren. Er is slechts één dispatcherknooppunt, dat wordt gebruikt om taken/taken uit cloudservices te halen en deze naar verschillende werknemersknooppunten (inclusief zichzelf) te verzenden. 

![Data Management Gateway - geavanceerde bewaking van meerdere nodes](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Gatewaystatus

De volgende tabel biedt mogelijke statussen van een **gatewayknooppunt:** 

Status  | Opmerkingen/scenario's
:------- | :------------------
Online | Knooppunt verbonden met de Data Factory-service.
Offline | Knooppunt is offline.
Upgraden | Het knooppunt wordt automatisch bijgewerkt.
Beperkt | Vanwege het probleem met de connectiviteit. Mogelijk als gevolg van het probleem met de HTTP-poort 8050, het probleem met de verbindingsverbinding van de servicebus of het synchronisatieprobleem van de referentie. 
Niet-actief | Node is in een andere configuratie dan de configuratie van andere meerderheidsknooppunten.<br/><br/> Een knooppunt kan inactief zijn wanneer het geen verbinding kan maken met andere knooppunten. 


De volgende tabel biedt mogelijke statussen van een **logische gateway.** De status van de gateway is afhankelijk van de status van de gatewayknooppunten. 

Status | Opmerkingen
:----- | :-------
Registratie nodig | Er is nog geen knooppunt geregistreerd op deze logische gateway
Online | Gatewayknooppunten zijn online
Offline | Geen knooppunt in online status.
Beperkt | Niet alle knooppunten in deze gateway zijn in gezonde staat. Deze status is een waarschuwing dat een knooppunt mogelijk naar beneden is! <br/><br/>Kan te wijten zijn aan de referentie synchronisatie probleem op dispatcher / worker node. 

### <a name="pipeline-activities-monitoring"></a>Monitoring van pijpleidingen/activiteiten
De Azure-portal biedt een ervaring voor het bewaken van de pijplijn met gedetailleerde details op nodeniveau. Het laat bijvoorbeeld zien welke activiteiten op welk knooppunt zijn uitgevoerd. Deze informatie kan nuttig zijn bij het begrijpen van prestatieproblemen op een bepaald knooppunt, bijvoorbeeld als gevolg van netwerkbeperking. 

![Data Management Gateway - bewaking van meerdere nodes voor pijplijnen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway - pijplijndetails](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Schaaloverwegingen

### <a name="scale-out"></a>Uitschalen
Wanneer het **beschikbare geheugen laag is** en het **CPU-gebruik hoog is,** helpt het toevoegen van een nieuw knooppunt de belasting over machines uit te schalen. Als activiteiten mislukken omdat een time-out of gatewayknooppunt offline is, helpt dit als u een knooppunt aan de gateway toevoegt.
 
### <a name="scale-up"></a>Omhoog schalen
Wanneer het beschikbare geheugen en de CPU niet goed worden gebruikt, maar de niet-actieve capaciteit 0 is, moet u opschalen door het aantal gelijktijdige taken te verhogen dat op een knooppunt kan worden uitgevoerd. U ook opschalen wanneer activiteiten worden getimed omdat de gateway overbelast is. Zoals in de volgende afbeelding wordt weergegeven, u de maximale capaciteit voor een knooppunt verhogen. We raden aan om het te verdubbelen.  

![Data Management Gateway - schaaloverwegingen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Bekende problemen/wijzigingen doorbreken

- Momenteel u maximaal vier fysieke gatewayknooppunten hebben voor één logische gateway. Als u meer dan vier knooppunten nodig hebt [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)om prestatieredenen, stuurt u een e-mail naar .
- U een gatewayknooppunt niet opnieuw registreren met de verificatiesleutel van een andere logische gateway om over te schakelen van de huidige logische gateway. Als u de gateway opnieuw wilt registreren vanaf het knooppunt, installeert u de gateway opnieuw en registreert u deze met de verificatiesleutel voor de andere logische gateway. 
- Als HTTP-proxy vereist is voor al uw gatewayknooppunten, stelt u de proxy in diahost.exe.config en diawp.exe.config in en gebruikt u de servermanager om ervoor te zorgen dat alle knooppunten dezelfde diahost.exe.config en diawip.exe.config hebben. Zie sectie [proxy-instellingen configureren](data-factory-data-management-gateway.md#configure-proxy-server-settings) voor meer informatie. 
- Als u de versleutelingsmodus voor knooppuntcommunicatie wilt wijzigen in Gateway Configuration Manager, verwijdert u alle knooppunten in de portal, op één na. Voeg vervolgens knooppunten terug nadat u de versleutelingsmodus hebt gewijzigd.
- Gebruik een officieel TLS-certificaat als u ervoor kiest om het communicatiekanaal van knooppunt tot knooppunt te versleutelen. Zelfondertekend certificaat kan verbindingsproblemen veroorzaken, omdat hetzelfde certificaat mogelijk niet wordt vertrouwd in de lijst met certificerende autoriteiten op andere machines. 
- U een gatewayknooppunt niet registreren naar een logische gateway wanneer de nodeversie lager is dan de logische gatewayversie. Verwijder alle knooppunten van de logische gateway van portal, zodat u een lager versieknooppunt (downgrade) registreren. Als u alle knooppunten van een logische gateway verwijdert, installeert en registreert u handmatig nieuwe knooppunten voor die logische gateway. Express setup wordt in dit geval niet ondersteund.
- U geen expresinstellingen gebruiken om knooppunten te installeren op een bestaande logische gateway, die nog steeds cloudreferenties gebruikt. U controleren waar de referenties zijn opgeslagen via Gateway Configuration Manager op het tabblad Instellingen.
- U geen express-setup gebruiken om knooppunten te installeren op een bestaande logische gateway, waarvoor node-to-node-versleuteling is ingeschakeld. Aangezien het instellen van de versleutelingsmodus het handmatig toevoegen van certificaten omvat, is express-installatie geen optie meer. 
- Voor een bestandskopie van de on-premises \\omgeving mag u geen localhost- of C:\-bestanden meer gebruiken, omdat localhost of lokaal station mogelijk niet toegankelijk is via alle knooppunten. Gebruik \\in plaats daarvan ServerName\files om de locatie van bestanden op te geven.


## <a name="rolling-back-from-the-preview"></a>Terugdraaien van de preview 
Als u terug wilt draaien uit de preview, verwijdert u alle knooppunten op één knooppunt na. Het maakt niet uit welke knooppunten u verwijdert, maar zorg ervoor dat u ten minste één knooppunt in de logische gateway hebt. U een knooppunt verwijderen door de gateway op de machine te verwijderen of door de Azure-portal te gebruiken. Klik in de Azure-portal op de pagina **Gegevensfabriek** op Gekoppelde services om de pagina **Gekoppelde services** te starten. Selecteer de gateway om de **gatewaypagina** te starten. Op de gatewaypagina ziet u de knooppunten die aan de gateway zijn gekoppeld. Op de pagina u een knooppunt van de gateway verwijderen.
 
Klik na het verwijderen op **voorbeeldfuncties** op dezelfde Azure-portalpagina en schakel de voorbeeldfunctie uit. U hebt uw gateway opnieuw ingesteld op een gateway voor de algemene beschikbaarheid van een knooppunt.


## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen:
- [Data Management Gateway](data-factory-data-management-gateway.md) - biedt een gedetailleerd overzicht van de gateway.
- [Gegevens verplaatsen tussen on-premises en clouddatastores](data-factory-move-data-between-onprem-and-cloud.md) - bevat een walkthrough met stapsgewijze instructies voor het gebruik van een gateway met één knooppunt. 
