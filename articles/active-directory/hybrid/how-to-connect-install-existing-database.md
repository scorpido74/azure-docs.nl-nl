---
title: Azure AD Connect installeren met behulp van een bestaande ADSync-database | Microsoft Documenten
description: In dit onderwerp wordt beschreven hoe u een bestaande ADSync-database gebruiken.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245128"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Azure AD Connect installeren met behulp van een bestaande ADSync-database
Azure AD Connect vereist een SQL Server-database om gegevens op te slaan. U de standaard SQL Server 2012 Express LocalDB gebruiken die is geïnstalleerd met Azure AD Connect of uw eigen volledige versie van SQL gebruiken. Voorheen werd bij het installeren van Azure AD Connect altijd een nieuwe database met de naam ADSync gemaakt. Met Azure AD Connect-versie 1.1.613.0 (of daarna) hebt u de optie om Azure AD Connect te installeren door deze aan te wijzen naar een bestaande ADSync-database.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Voordelen van het gebruik van een bestaande ADSync-database
Door te wijzen op een bestaande ADSync-database:

- Behalve voor referentiesinformatie wordt de synchronisatieconfiguratie die is opgeslagen in de ADSync-database (inclusief aangepaste synchronisatieregels, connectors, filtering en optionele functiesconfiguratie) automatisch hersteld en gebruikt tijdens de installatie . Referenties die door Azure AD Connect worden gebruikt om wijzigingen te synchroniseren met on-premises AD en Azure AD, zijn versleuteld en kunnen alleen worden geopend door de vorige Azure AD Connect-server.
- Alle identiteitsgegevens (gekoppeld aan verbindingsruimten en metaverse) en synchronisatiecookies die zijn opgeslagen in de ADSync-database, worden ook hersteld. De nieuw geïnstalleerde Azure AD Connect-server kan blijven synchroniseren vanaf de plek waar de vorige Azure AD Connect-server is gebleven, in plaats van dat de noodzaak is om een volledige synchronisatie uit te voeren.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenario's waarbij het gebruik van een bestaande ADSync-database gunstig is
Deze voordelen zijn nuttig in de volgende scenario's:


- U hebt een bestaande Azure AD Connect-implementatie. Uw bestaande Azure AD Connect-server werkt niet meer, maar de SQL-server met de ADSync-database werkt nog steeds. U een nieuwe Azure AD Connect-server installeren en deze naar de bestaande ADSync-database wijzen. 
- U hebt een bestaande Azure AD Connect-implementatie. Uw SQL-server met de ADSync-database werkt niet meer. U hebt echter een recente back-up van de database. U de ADSync-database eerst herstellen naar een nieuwe SQL-server. Daarna u een nieuwe Azure AD Connect-server installeren en deze naar de herstelde ADSync-database wijzen.
- U hebt een bestaande Azure AD Connect-implementatie die LocalDB gebruikt. Vanwege de limiet van 10 GB die door LocalDB is opgelegd, wilt u migreren naar volledige SQL. U een back-up maken van de ADSync-database van LocalDB en deze herstellen naar een SQL-server. Daarna u een nieuwe Azure AD Connect-server opnieuw installeren en deze naar de herstelde ADSync-database wijzen.
- U probeert een stagingserver in te stellen en wilt ervoor zorgen dat de configuratie overeenkomt met die van de huidige actieve server. U een back-up maken van de ADSync-database en deze herstellen naar een andere SQL-server. Daarna u een nieuwe Azure AD Connect-server opnieuw installeren en deze naar de herstelde ADSync-database wijzen.

## <a name="prerequisite-information"></a>Vereiste informatie

Belangrijke notities om er rekening mee te houden voordat u verder gaat:

- Controleer de vereisten voor het installeren van Azure AD Connect op hardware en vereisten en account- en machtigingen die nodig zijn voor het installeren van Azure AD Connect. De machtigingen die nodig zijn voor het installeren van Azure AD Connect met de modus 'Bestaande database gebruiken' zijn hetzelfde als 'aangepaste' installatie.
- Het implementeren van Azure AD Connect tegen een bestaande ADSync-database wordt alleen ondersteund met volledige SQL. Het wordt niet ondersteund met SQL Express LocalDB. Als u een bestaande ADSync-database in LocalDB hebt die u wilt gebruiken, moet u eerst een back-up maken van de ADSync-database (LocalDB) en deze herstellen naar volledige SQL. Vervolgens u Azure AD Connect met deze methode implementeren tegen de herstelde database.
- De versie van de Azure AD Connect die voor de installatie wordt gebruikt, moet aan de volgende criteria voldoen:
    - 1.1.613.0 of hoger, AND
    - Hetzelfde of hoger als de versie van de Azure AD Connect die voor het laatst is gebruikt met de ADSync-database. Als de Azure AD Connect-versie die voor de installatie wordt gebruikt hoger is dan de versie die voor het laatst wordt gebruikt met de ADSync-database, is een volledige synchronisatie vereist.  Volledige synchronisatie is vereist als er schema- of synchronisatieregelwijzigingen zijn tussen de twee versies. 
- De gebruikte ADSync-database moet een synchronisatiestatus bevatten die relatief recent is. De laatste synchronisatieactiviteit met de bestaande ADSync-database moet in de afgelopen drie weken plaatsvinden.
- Wanneer u Azure AD Connect installeert met de methode 'Bestaande database gebruiken' blijft de aanmeldingsmethode die is geconfigureerd op de vorige Azure AD Connect-server, niet behouden. Verder u de aanmeldingsmethode niet configureren tijdens de installatie. U de aanmeldingsmethode alleen configureren nadat de installatie is voltooid.
- U niet meerdere Azure AD Connect-servers dezelfde ADSync-database laten delen. Met de methode 'bestaande database gebruiken' u een bestaande ADSync-database opnieuw gebruiken met een nieuwe Azure AD Connect-server. Het ondersteunt delen niet.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Stappen voor het installeren van Azure AD Connect met de modus Bestaande database gebruiken
1.  Download Azure AD Connect-installatieprogramma (AzureADConnect.MSI) naar de Windows-server. Dubbelklik op het azure AD Connect-installatieprogramma om Azure AD Connect te installeren.
2.  Zodra de MSI-installatie is voltooid, wordt de wizard Azure AD Connect gestart met de Express-installatiemodus. Sluit het scherm door op het pictogram Afsluiten te klikken.
![Welkom](./media/how-to-connect-install-existing-database/db1.png)
3.  Start een nieuwe opdrachtprompt of PowerShell-sessie. Navigeer naar map "C:\Program Files\Microsoft Azure Active Directory Connect". Voer de opdracht .\AzureADConnect.exe /useexistingdatabase uit om de wizard Azure AD Connect te starten in de installatiemodus Bestaande database gebruiken.

> [!NOTE]
> Gebruik de switch **/UseExistingDatabase** alleen wanneer de database al gegevens bevat van een eerdere Azure AD Connect-installatie. Wanneer u bijvoorbeeld van een lokale database naar een volledige SQL Server-database gaat of wanneer de Azure AD Connect-server opnieuw is opgebouwd en u een SQL-back-up van de ADSync-database hebt hersteld vanuit een eerdere installatie van Azure AD Connect. Als de database leeg is, dat wil zeggen dat deze geen gegevens bevat van een eerdere Azure AD Connect-installatie, slaat u deze stap over.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. U wordt verwelkomd met het scherm Welkom bij Azure AD Connect. Nadat u akkoord bent gegaan met de licentievoorwaarden en privacyverklaring, klikt u op **Doorgaan**.
   ![Welkom](./media/how-to-connect-install-existing-database/db3.png)
1. Op het scherm **Vereiste onderdelen installeren** is de optie **Een bestaande SQL-server gebruiken** ingeschakeld. Geef de naam op van de SQL-server waarop de ADSync-database wordt gehost. Als het SQL Engine-exemplaar dat wordt gebruikt om de ADSync-database te hosten, niet het standaardexemplaar is op de SQL-server, moet u de naam van het SQL Engine-exemplaar opgeven. Daarnaast moet u, indien bladeren in SQL niet is ingeschakeld, ook het poortnummer voor het SQL Engine-exemplaar opgeven. Bijvoorbeeld:         
   ![Welkom](./media/how-to-connect-install-existing-database/db4.png)           

1. Op het scherm **Verbinding maken met Azure AD** moet u de referenties opgeven van een globale beheerder van de Azure AD-adreslijst. U wordt aangeraden om een account te gebruiken in het standaarddomein onmicrosoft.com. Dit account wordt alleen gebruikt om een serviceaccount in Azure AD aan te maken en wordt niet gebruikt wanneer de wizard is voltooid.
   ![Verbinding maken](./media/how-to-connect-install-existing-database/db5.png)
 
1. Op het scherm **Verbinding maken met uw adreslijsten** wordt het bestaande AD-forest dat is geconfigureerd voor adreslijstsynchronisatie, weergegeven met een rood kruis ernaast. Voor het synchroniseren van wijzigingen vanuit een on-premises AD-forest is een AD DS-account vereist. Met de wizard Azure AD Connect kunnen de referenties van het AD DS-account dat is opgeslagen in de ADsync-database, niet worden opgehaald, omdat deze referenties zijn versleuteld en alleen kunnen worden ontsleuteld met de vorige Azure AD Connect-server. Klik op **Referenties wijzigen** om het AD DS-account voor het AD-forest op te geven.
   ![Adreslijsten](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. In het pop-updialoogvenster kunt u (i) de referenties van een ondernemingsadministrator opgeven en het AD DS-account voor u laten maken in Azure AD Connect of (ii) zelf het AD DS-account maken en de bijbehorende referenties opgeven in Azure AD Connect. Zodra u een optie hebt geselecteerd en de benodigde referenties hebt opgegeven, klikt u op **OK** om het pop-updialoogvenster te sluiten.
   ![Welkom](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. Zodra de referenties zijn opgegeven, verandert het rode kruis in een groen vinkje. Klik op **Volgende**.
   ![Welkom](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. Klik op het scherm **Klaar om te configureren** op **Installeren**.
   ![Welkom](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. Zodra de installatie is voltooid, wordt de Azure AD Connect-server automatisch ingeschakeld voor de faseringsmodus. U wordt aangeraden om de serverconfiguratie en exports die in behandeling zijn, te controleren op onverwachte wijzigingen, voordat u de faseringsmodus uitschakelt. 

## <a name="post-installation-tasks"></a>Taken na de installatie
Wanneer u een databaseback-up herstelt die is gemaakt door een versie van Azure AD Connect vóór 1.2.65.0, selecteert de staging-server automatisch een aanmeldingsmethode van **Niet configureren**. Terwijl uw wachtwoordhashsynchronisatie en wachtwoordschrijfvoorkeuren worden hersteld, moet u vervolgens de aanmeldingsmethode wijzigen om aan te sluiten bij het andere beleid dat van kracht is voor uw actieve synchronisatieserver.  Als u deze stappen niet voltooit, kunnen gebruikers zich niet aanmelden als deze server actief wordt.  

Gebruik de onderstaande tabel om eventuele extra stappen te verifiëren die nodig zijn.

|Functie|Stappen|
|-----|-----|
|Synchronisatie van wachtwoordhash| de instellingen voor wachtwoordhashsynchronisatie en wachtwoordterugschrijfinstellingen zijn volledig hersteld voor versies van Azure AD Connect vanaf 1.2.65.0.  Als u herstelt met een oudere versie van Azure AD Connect, controleert u de instellingen voor synchronisatieopties voor deze functies om te controleren of deze overeenkomen met uw actieve synchronisatieserver.  Er zijn geen andere configuratiestappen nodig.|
|Federatie met AD FS|Azure-verificaties blijven het AD FS-beleid gebruiken dat is geconfigureerd voor uw actieve synchronisatieserver.  Als u Azure AD Connect gebruikt om uw AD FS-farm te beheren, u de aanmeldingsmethode optioneel wijzigen in AD FS-federatie ter voorbereiding van het feit dat uw stand-byserver de actieve synchronisatie-instantie wordt.   Als apparaatopties zijn ingeschakeld op de actieve synchronisatieserver, configureert u deze opties op deze server door de taak Apparaatopties configureren uit te voeren.|
|Doorreisverificatie en één aanmelding voor desktops|Werk de aanmeldingsmethode bij om de configuratie op uw actieve synchronisatieserver te matchen.  Als dit niet wordt gevolgd voordat de server wordt gepromoot naar primaire, wordt pass-through-verificatie samen met Seamless Single Sign-aan uitgeschakeld en wordt uw tenant mogelijk buitengesloten als u geen wachtwoordhashsynchronisatie hebt als aanmeldingsoptie voor back-up. Houd er ook rekening mee dat wanneer u pass-through-verificatie in de faseringsmodus inschakelt, een nieuwe verificatieagent wordt geïnstalleerd, geregistreerd en wordt uitgevoerd als een agent met hoge beschikbaarheid die aanmeldingsverzoeken accepteert.|
|Federatie met PingFederate|Azure-verificaties blijven het pingfederate-beleid gebruiken dat is geconfigureerd voor uw actieve synchronisatieserver.  U optioneel de aanmeldingsmethode wijzigen in PingFederate ter voorbereiding van uw stand-byserver die de actieve synchronisatie-instantie wordt.  Deze stap kan worden uitgesteld totdat u extra domeinen moet federatemet PingFederate.|

## <a name="next-steps"></a>Volgende stappen

- Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](how-to-connect-post-installation.md).
- Meer informatie over deze functies, die tijdens de installatie zijn ingeschakeld: [Prevent accidental deletes](how-to-connect-sync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](how-to-connect-health-sync.md).
- Lees meer over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](how-to-connect-sync-feature-scheduler.md).
- Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
