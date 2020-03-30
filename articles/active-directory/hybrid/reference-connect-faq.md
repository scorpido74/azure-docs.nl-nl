---
title: Veelgestelde vragen over Azure Active Directory Connect - | Microsoft Documenten
description: In dit artikel worden veelgestelde vragen over Azure AD Connect beantwoord.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149810"
---
# <a name="azure-active-directory-connect-faq"></a>Veelgestelde vragen over Azure Active Directory Connect

## <a name="general-installation"></a>Algemene installatie

**V: Hoe kan ik mijn Azure AD Connect-server verharden om het oppervlak van de beveiligingsaanval te verlagen?**

Microsoft raadt aan om uw Azure AD Connect-server te verharden om het beveiligingsaanvalsoppervlak voor dit kritieke onderdeel van uw IT-omgeving te verkleinen.  Als u de onderstaande aanbevelingen volgt, worden de beveiligingsrisico's voor uw organisatie verlaagd.

* Azure AD Connect implementeren op een domeinverbonden server en beheerderstoegang beperken tot domeinbeheerders of andere streng gecontroleerde beveiligingsgroepen

Voor meer informatie zie: 

* [Beheerdersgroepen beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Ingebouwde beheerdersaccounts beveiligen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Verbetering en ondersteuning van de veiligheid door het verminderen van aanvalsoppervlakken](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Het oppervlak van de Active Directory-aanval verkleinen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**V: Werkt de installatie als de globale beheerder van Azure Active Directory (Azure AD) tweestapsverificatie (2FA) heeft ingeschakeld?**  
Met betrekking tot de builds van februari 2016 wordt dit scenario ondersteund.

**V: Is er een manier om Azure AD Connect onbeheerd te installeren?**  
Azure AD Connect-installatie wordt alleen ondersteund wanneer u de wizard Installatie gebruikt. Een onbewaakte, stille installatie wordt niet ondersteund.

**V: Ik heb een forest waar geen contact met één domein kan worden opgenomen. Hoe installeer ik Azure AD Connect?**  
Met betrekking tot de builds van februari 2016 wordt dit scenario ondersteund.

**V: Werkt de azure active directory domain services (Azure AD DS)-status op serverkern?**  
Ja. Nadat u de agent hebt geïnstalleerd, u het registratieproces voltooien met behulp van de volgende PowerShell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**V: Ondersteunt Azure AD Connect synchronisatie van twee domeinen naar een Azure AD?**  
Ja, dit scenario wordt ondersteund. Raadpleeg [meerdere domeinen](how-to-connect-install-multiple-domains.md).
 
**V: u meerdere connectors hebben voor hetzelfde Active Directory-domein in Azure AD Connect?**  
Nee, meerdere connectors voor hetzelfde AD-domein worden niet ondersteund. 

**V: Kan ik de Azure AD Connect-database verplaatsen van de lokale database naar een extern SQL Server-exemplaar?**   
Ja, de volgende stappen bieden algemene richtlijnen over hoe u dit doen. We werken momenteel aan een gedetailleerder document.
1. Een back-up van de ADSync-database van LocalDB.
De eenvoudigste manier om dit te doen is door SQL Server Management Studio te gebruiken die is geïnstalleerd op dezelfde machine als Azure AD Connect. Maak verbinding met *(LocalDb).\ADSync*en maak een back-up van de ADSync-database.

2. De ADSync-database herstellen naar uw externe SQL Server-exemplaar.

3. Installeer Azure AD Connect tegen de bestaande [externe SQL-database.](how-to-connect-install-existing-database.md)
   Het artikel laat zien hoe u migreren naar het gebruik van een lokale SQL-database. Als u migreert naar het gebruik van een externe SQL-database, moet u in stap 5 van het proces ook een bestaand serviceaccount invoeren dat de Windows Sync-service als wordt uitgevoerd. Deze sync engine service account wordt hier beschreven:
   
      **Een bestaand serviceaccount gebruiken:** Azure AD Connect gebruikt standaard een virtueel serviceaccount voor de te gebruiken synchronisatieservices. Als u een extern SQL Server-exemplaar gebruikt of een proxy gebruikt waarvoor verificatie vereist is, gebruikt u een beheerde serviceaccount of een serviceaccount in het domein en kent u het wachtwoord. Voer in dat geval het te gebruiken account in. Zorg ervoor dat gebruikers die de installatie uitvoeren systeembeheerders zijn in SQL, zodat inloggegevens voor het serviceaccount kunnen worden gemaakt. Zie [Azure AD Connect-accounts en -machtigingen](reference-connect-accounts-permissions.md#adsync-service-account)voor meer informatie . 
   
      In de laatste versie kan de inrichting van de database out-of-band worden uitgevoerd door de SQL-beheerder en vervolgens worden geïnstalleerd door de Azure AD Connect-beheerder met eigendomsrechten voor de database. Zie [Azure AD Connect installeren met SQL-machtigingen voor gedelegeerde beheerders](how-to-connect-install-sql-delegation.md)voor meer informatie.

Om het simpel te houden, raden we gebruikers die Azure AD Connect installeren aan om systeembeheerders in SQL te zijn. Met recente builds u nu echter gedelegeerde SQL-beheerders gebruiken, zoals beschreven in [Azure AD Connect installeren met SQL-machtigingen voor gedelegeerde beheerders.](how-to-connect-install-sql-delegation.md)

**V: Wat zijn enkele van de beste praktijken uit het veld?**  

Het volgende is een informatief document dat enkele van de beste praktijken presenteert die engineering, support en onze consultants in de loop der jaren hebben ontwikkeld.  Dit wordt gepresenteerd in een lijst met opsommingstekens waarnaar snel kan worden verwezen.  Hoewel deze lijst probeert te zijn uitgebreid, kunnen er aanvullende best practices die misschien niet hebben gemaakt op de lijst nog niet.

- Als u Full SQL gebruikt, moet het lokaal versus op afstand blijven
    - Minder hop
    - Gemakkelijker om problemen op te lossen
    - Minder complexiteit
    - Resources aanwijzen voor SQL en overhead toestaan voor Azure AD Connect en OS
- Bypass Proxy indien mogelijk, als u niet in staat bent om de proxy te omzeilen dan moet je ervoor zorgen dat de time-out waarde groter is dan 5 minuten.
- Als proxy vereist is, moet u de proxy toevoegen aan het machine.config-bestand
- Wees je bewust van lokale SQL-taken en -onderhoud en hoe deze van invloed zijn op Azure AD Connect - met name opnieuw indexeren
- Ervoor zorgen dat DNS extern kan oplossen
- Ervoor zorgen dat [serverspecificaties](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) per aanbeveling zijn, ongeacht of u fysieke of virtuele servers gebruikt
- Ervoor zorgen dat als u een virtuele server gebruikt die resources nodig hebben,
- Zorg ervoor dat u de schijf- en schijfconfiguratie hebt die voldoet aan aanbevolen procedures voor SQL Server
- Azure AD Connect-status installeren en configureren voor bewaking
- Gebruik de verwijderdrempel die is ingebouwd in Azure AD Connect.
- Zorgvuldig controleren release updates worden voorbereid op alle wijzigingen en nieuwe attributen die kunnen worden toegevoegd
- Back-up maken van alles
    - Back-upsleutels
    - Regels voor back-upsynchronisatie
    - Configuratie van back-upserver
    - SQL-database back-upmaken
- Zorg ervoor dat er geen back-upagents van derden zijn die een back-up maken van SQL zonder sql VSS-schrijver (veelvoorkomende in virtuele servers met snapshots van derden)
- Beperk de hoeveelheid aangepaste synchronisatieregels die worden gebruikt als ze complexiteit toevoegen
- Azure AD Connect-servers behandelen als tier 0-servers
- Wees wantrouwig van het wijzigen van cloudsynchronisatieregels zonder goed inzicht in de impact en de juiste zakelijke drivers
- Controleer of de juiste URL-poorten en firewallpoorten zijn geopend voor ondersteuning van Azure AD Connect en Azure AD Connect Health
- Gebruik het gefilterde kenmerk in de cloud om fantoomobjecten op te lossen en te voorkomen
- Met de Staging Server u ervoor zorgen dat u de Configuratiedocumenter Azure AD Connect gebruikt voor consistentie tussen servers
- Staging Servers moeten zich in afzonderlijke datacenters bevinden (fysieke locaties
- Staging servers zijn niet bedoeld als een oplossing voor hoge beschikbaarheid, maar u meerdere staging servers hebben
- De invoering van een "Lag" Staging Servers kan enige potentiële downtime in geval van fout te beperken
- Alle upgrades op de stagingserver eerst testen en valideren
- Valideer de export altijd voordat u overschakelt naar de stagingserver.  Maak gebruik van de staging server voor volledige import en volledige synchronisaties om de impact van het bedrijf te verminderen
- Houd de consistentie van de versie tussen Azure AD Connect-servers zoveel mogelijk 

**V: Kan ik Azure AD Connect toestaan om het Azure AD Connector-account op de werkgroepmachine te maken?**
Nee.  Om Azure AD Connect toe te staan het Azure AD Connector-account automatisch te maken, moet de machine zijn verbonden met het domein.  

## <a name="network"></a>Netwerk
**V: Ik heb een firewall, netwerkapparaat of iets anders dat de tijd beperkt dat verbindingen open kunnen blijven op mijn netwerk. Wat moet mijn time-outdrempel aan de clientzijde zijn wanneer ik Azure AD Connect gebruik?**  
Alle netwerksoftware, fysieke apparaten of iets anders dat de maximale tijd beperkt dat verbindingen open blijven, moet een drempelwaarde van ten minste vijf minuten (300 seconden) gebruiken voor connectiviteit tussen de server waar de Azure AD Connect-client is geïnstalleerd en Azure Active Directory. Deze aanbeveling is ook van toepassing op alle eerder uitgebrachte Microsoft Identity-synchronisatietools.

**V: Worden single label domains (SLDs) ondersteund?**  
Hoewel we deze netwerkconfiguratie ten zeerste aanbevelen[(zie artikel),](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)wordt azure AD Connect-synchronisatie met één labeldomein ondersteund, zolang de netwerkconfiguratie voor het domein met één niveau correct functioneert.

**V: Worden forests met onsamenhangende AD-domeinen ondersteund?**  
Nee, Azure AD Connect biedt geen ondersteuning voor on-premises forests die onsamenhangende naamruimten bevatten.

**V: Worden "gestippelde" NetBIOS-namen ondersteund?**  
Nee, Azure AD Connect biedt geen ondersteuning voor on-premises forests of domeinen waarbij de NetBIOS-naam een punt bevat (.).

**V: Wordt de pure IPv6-omgeving ondersteund?**  
Nee, Azure AD Connect ondersteunt geen pure IPv6-omgeving.

**Q:I hebben een multi-forest omgeving en het netwerk tussen de twee bossen is met behulp van NAT (Network Address Translation). Wordt het gebruik van Azure AD Connect tussen deze twee forests ondersteund?**</br>
Nee, het gebruik van Azure AD Connect via NAT wordt niet ondersteund. 

## <a name="federation"></a>Federatie
**V: Wat moet ik doen als ik een e-mail ontvang waarin ik mijn Office 365-certificaat wil verlengen?**  
Zie [Certificaten verlengen](how-to-connect-fed-o365-certs.md)voor richtlijnen voor het vernieuwen van het certificaat.

**V: Ik heb 'Relying party automatisch bijwerken' ingesteld voor de office 365-relying party. Moet ik actie ondernemen wanneer mijn certificaat voor tokenondertekening automatisch wordt overgevoegt?**  
Gebruik de richtlijnen die in het artikel [certificaten vernieuwen .](how-to-connect-fed-o365-certs.md)

## <a name="environment"></a>Omgeving
**V: Wordt de server opnieuw vermeld nadat Azure AD Connect is geïnstalleerd?**  
Nee. Als u de servernaam wijzigt, kan de synchronisatieengine geen verbinding maken met de SQL-databaseinstantie en kan de service niet worden gestart.

**V: Worden ngc-synchronisatieregels (Next Generation Cryptographic) ondersteund op een fips-machine?**  
Nee.  Dit wordt niet ondersteund.

**V. Als ik een gesynchroniseerd apparaat (bijvoorbeeld HAADJ) in de Azure-portal heb uitgeschakeld, waarom is het dan opnieuw ingeschakeld?**<br>
Gesynchroniseerde apparaten kunnen op locatie worden geschreven of gemasterd. Als een gesynchroniseerd apparaat on-premises is ingeschakeld, kan het opnieuw worden ingeschakeld in de Azure-portal, zelfs als het eerder door een beheerder is uitgeschakeld. Als u een gesynchroniseerd apparaat wilt uitschakelen, gebruikt u de on-premises Active Directory om het computeraccount uit te schakelen.

**V. Als ik de aanmelding van gebruikers blokkeer bij de Office 365- of Azure AD-portal voor gesynchroniseerde gebruikers, waarom wordt deze geblokkeerd wanneer u zich opnieuw aanmeldt?**<br>
Gesynchroniseerde gebruikers kunnen worden geschreven of onder de knie op locatie. Als het account op locatie is ingeschakeld, kan het het aanmeldingsblok dat door de beheerder is geplaatst, worden gedeblokkeerd.

## <a name="identity-data"></a>Identiteitsgegevens
**V: Waarom komt het kenmerk USERPrincipalName (UPN) in Azure AD niet overeen met het on-premises UPN?**  
Zie voor meer informatie de volgende artikelen:

* [Gebruikersnamen in Office 365, Azure of Intune komen niet overeen met de on-premises UPN- of alternatieve inlog-id](https://support.microsoft.com/kb/2523192)
* [Wijzigingen worden niet gesynchroniseerd met het Azure Active Directory-synchronisatiehulpprogramma nadat u de UPN van een gebruikersaccount hebt gewijzigd om een ander federatief domein te gebruiken](https://support.microsoft.com/kb/2669550)

U Azure AD ook configureren zodat de synchronisatieengine de UPN kan bijwerken, zoals beschreven in [azure AD Connect-synchronisatieservicefuncties.](how-to-connect-syncservice-features.md)

**V: Wordt het ondersteund om een on-premises Azure AD-groep of contactobject op een andere overeenkomst te brengen met een bestaande Azure AD-groep of contactobject?**  
Ja, deze soft match is gebaseerd op de proxyAddress. Soft matching wordt niet ondersteund voor groepen die niet zijn ingeschakeld voor e-mail.

**V: Wordt het kenmerk ImmutableId handmatig ingesteld op een bestaande Azure AD-groep of contactobject om het object hard te koppelen aan een on-premises Azure AD-groep of contactobject?**  
Nee, het kenmerk ImmutableId handmatig instellen op een bestaande Azure AD-groep of contactobject op hard-match wordt momenteel niet ondersteund.

## <a name="custom-configuration"></a>Aangepaste configuratie
**V: Waar zijn de PowerShell-cmdlets voor Azure AD Connect gedocumenteerd?**  
Met uitzondering van de cmdlets die op deze site zijn gedocumenteerd, worden andere PowerShell-cmdlets in Azure AD Connect niet ondersteund voor klantgebruik.

**V: Kan ik de optie 'Server exporteren/server importeren' gebruiken die is gevonden in Synchronisatieservicebeheer om de configuratie tussen servers te verplaatsen?**  
Nee. Met deze optie worden niet alle configuratie-instellingen opgehaald en mag deze niet worden gebruikt. Gebruik in plaats daarvan de wizard om de basisconfiguratie op de tweede server te maken en gebruik de synchronisatieregeleditor om PowerShell-scripts te genereren om een aangepaste regel tussen servers te verplaatsen. Zie [Swing migratie voor](how-to-upgrade-previous-version.md#swing-migration)meer informatie.

**V: Kunnen wachtwoorden in de cache worden opgeslagen voor de Azure-aanmeldingspagina en kan deze caching worden voorkomen omdat het een wachtwoordinvoerelement bevat met het *kenmerk automatisch aanvullen = 'false'?***  
Momenteel wordt het wijzigen van de HTML-kenmerken van het veld **Wachtwoord,** inclusief de tag automatisch aanvullen, niet ondersteund. We werken momenteel aan een functie waarmee aangepaste JavaScript mogelijk is, waarmee u elk kenmerk toevoegen aan het veld **Wachtwoord.**

**V: Op de aanmeldingspagina van Azure worden de gebruikersnamen weergegeven van gebruikers die zich eerder met succes hebben aangemeld. Kan dit gedrag worden uitgeschakeld?**  
Momenteel wordt het wijzigen van de HTML-kenmerken van het invoerveld **Wachtwoord,** inclusief de tag automatisch aanvullen, niet ondersteund. We werken momenteel aan een functie waarmee aangepaste JavaScript mogelijk is, waarmee u elk kenmerk toevoegen aan het veld **Wachtwoord.**

**V: Is er een manier om gelijktijdige sessies te voorkomen?**  
Nee.

## <a name="auto-upgrade"></a>Automatische upgrade

**V: Wat zijn de voordelen en gevolgen van het gebruik van automatische upgrade?**  
We adviseren alle klanten om automatische upgrade in te schakelen voor hun Azure AD Connect-installatie. Het voordeel is dat u altijd de nieuwste patches ontvangt, waaronder beveiligingsupdates voor kwetsbaarheden die zijn gevonden in Azure AD Connect. Het upgradeproces is pijnloos en gebeurt automatisch zodra er een nieuwe versie beschikbaar is. Vele duizenden Azure AD Connect-klanten gebruiken automatische upgrade bij elke nieuwe release.

Het proces voor automatische upgrade bepaalt altijd eerst of een installatie in aanmerking komt voor automatische upgrade. Als deze in aanmerking komt, wordt de upgrade uitgevoerd en getest. Het proces omvat ook het zoeken naar aangepaste wijzigingen in regels en specifieke omgevingsfactoren. Als uit de tests blijkt dat een upgrade niet succesvol is, wordt de vorige versie automatisch hersteld.

Afhankelijk van de grootte van de omgeving kan het proces een paar uur duren. Terwijl de upgrade aan de gang is, wordt er niet gesynchroniseerd tussen Windows Server Active Directory en Azure AD.

**V: Ik heb een e-mail ontvangen waarin staat dat mijn automatische upgrade niet meer werkt en dat ik een nieuwe versie moet installeren. Waarom moet ik dit doen?**  
Vorig jaar hebben we een versie van Azure AD Connect uitgebracht die onder bepaalde omstandigheden mogelijk de functie voor automatisch bijwerken op uw server heeft uitgeschakeld. We hebben het probleem opgelost in Azure AD Connect-versie 1.1.750.0. Als u door het probleem bent getroffen, u dit beperken door een PowerShell-script uit te voeren om het te repareren of door handmatig te upgraden naar de nieuwste versie van Azure AD Connect. 

Als u het PowerShell-script wilt uitvoeren, [downloadt u het script](https://aka.ms/repairaadconnect) en voert u het uit op uw Azure AD Connect-server in een beheerd PowerShell-venster. Bekijk deze korte video [voor](https://aka.ms/repairaadcau)meer informatie over het uitvoeren van het script.

Als u handmatig wilt upgraden, moet u de nieuwste versie van het AADConnect.msi-bestand downloaden en uitvoeren.
 
-  Als uw huidige versie ouder is dan 1.1.750.0, [downloadt en upgradet u naar de nieuwste versie.](https://www.microsoft.com/download/details.aspx?id=47594)
- Als uw Azure AD Connect-versie 1.1.750.0 of hoger is, is er geen verdere actie vereist. U gebruikt al de versie die de oplossing voor automatische upgrade bevat. 

**V: Ik heb een e-mail ontvangen waarin staat dat ik moest upgraden naar de nieuwste versie om de automatische upgrade opnieuw in te schakelen. Ik gebruik versie 1.1.654.0. Moet ik upgraden?**  
Ja, u moet upgraden naar versie 1.1.750.0 of hoger om de automatische upgrade opnieuw in te schakelen. [Download en upgrade naar de nieuwste versie.](https://www.microsoft.com/download/details.aspx?id=47594)

**V: Ik heb een e-mail ontvangen waarin staat dat ik moest upgraden naar de nieuwste versie om de automatische upgrade opnieuw in te schakelen. Als ik PowerShell heb gebruikt om een automatische upgrade in te schakelen, moet ik dan nog steeds de nieuwste versie installeren?**  
Ja, u moet nog steeds upgraden naar versie 1.1.750.0 of hoger. Het inschakelen van de auto-upgradeservice met PowerShell vermindert het probleem met automatische upgrade dat is gevonden in versies vóór 1.1.750.0 niet.

**V: Ik wil upgraden naar een nieuwere versie, maar ik weet niet zeker wie Azure AD Connect heeft geïnstalleerd en we hebben de gebruikersnaam en het wachtwoord niet. Hebben we dit nodig?**
U hoeft de gebruikersnaam en het wachtwoord niet te kennen die in eerste instantie werden gebruikt om Azure AD Connect te upgraden. Gebruik een Azure AD-account met de functie Globale beheerder.

**V: Hoe kan ik vinden welke versie van Azure AD Connect ik gebruik?**  
Als u wilt controleren welke versie van Azure AD Connect op uw server is geïnstalleerd, gaat u naar het Configuratiescherm en zoekt u de geïnstalleerde versie van Microsoft Azure AD Connect op door >  **Programma's****en -onderdelen te**selecteren, zoals hier wordt weergegeven:

![Azure AD Connect-versie in configuratiescherm](./media/reference-connect-faq/faq1.png)

**V: Hoe upgrade ik naar de nieuwste versie van Azure AD Connect?**  
Zie [Azure AD Connect: Upgrade van een vorige versie naar de laatste versie voor](how-to-upgrade-previous-version.md)meer informatie over het upgraden naar de nieuwste versie. 

**V: We hebben vorig jaar al een upgrade uitgevoerd naar de nieuwste versie van Azure AD Connect. Moeten we weer upgraden?**  
Het Azure AD Connect-team werkt regelmatig bij aan de service. Om te profiteren van bugfixes en beveiligingsupdates en nieuwe functies, is het belangrijk om uw server up-to-date te houden met de nieuwste versie. Als u een automatische upgrade inschakelt, wordt uw softwareversie automatisch bijgewerkt. Zie [Azure AD Connect: Releasegeschiedenis](reference-connect-version-history.md)van de versie voor het vinden van de releasegeschiedenis van Azure AD Connect.

**V: Hoe lang duurt het om de upgrade uit te voeren en wat is de impact op mijn gebruikers?**  
De tijd die nodig is om te upgraden is afhankelijk van de grootte van uw tenant. Voor grotere organisaties is het misschien het beste om de upgrade 's avonds of in het weekend uit te voeren. Tijdens de upgrade vindt er geen synchronisatieactiviteit plaats.

**V: Ik geloof dat ik een upgrade heb uitgevoerd naar Azure AD Connect, maar de Office-portal vermeldt nog steeds DirSync. Waarom is dit?**  
Het Office-team werkt eraan om de updates van de Office-portal de huidige productnaam weer te geven. Het geeft niet aan welke synchronisatietool u gebruikt.

**V: In mijn status voor automatische upgrade staat:'Suspended'. Waarom is het geschorst? Moet ik het inschakelen?**  
Een bug is geïntroduceerd in een vorige versie die, onder bepaalde omstandigheden, laat de auto-upgrade status ingesteld op "Geschorst." Handmatig inschakelen is technisch mogelijk, maar vereist verschillende complexe stappen. Het beste wat u doen, is de nieuwste versie van Azure AD Connect installeren.

**V: Mijn bedrijf heeft strenge vereisten voor change-management en ik wil bepalen wanneer het wordt weggeduwd. Kan ik bepalen wanneer de automatische upgrade wordt gestart?**  
Nee, er is geen dergelijke functie vandaag. De functie wordt geëvalueerd voor een toekomstige release.

**V: Krijg ik een e-mail als de automatische upgrade is mislukt? Hoe weet ik dat het gelukt is?**  
U wordt niet op de hoogte gesteld van het resultaat van de upgrade. De functie wordt geëvalueerd voor een toekomstige release.

**V: Publiceert u een tijdlijn voor wanneer u van plan bent om automatische upgrades uit te duwen?**  
Automatische upgrade is de eerste stap in het releaseproces van een nieuwere versie. Wanneer er een nieuwe release is, worden upgrades automatisch gepusht. Nieuwere versies van Azure AD Connect zijn vooraf aangekondigd in de [Azure AD Roadmap.](../fundamentals/whats-new.md)

**V: Upgradet automatische upgrade ook Azure AD Connect Health?**  
Ja, automatische upgrade upgradet ook Azure AD Connect Health.

**V: Bent u ook een upgrade van Azure AD Connect-servers in de faseringsmodus?**  
Ja, u een Azure AD Connect-server die zich in de faseringsmodus bevindt, automatisch upgraden.

**V: Wat moet ik doen als de automatische upgrade mislukt en mijn Azure AD Connect-server niet wordt gestart?**  
In zeldzame gevallen wordt de Azure AD Connect-service niet gestart nadat u de upgrade hebt uitgevoerd. In deze gevallen lost het opnieuw opstarten van de server meestal het probleem op. Als de Azure AD Connect-service nog steeds niet wordt gestart, opent u een ondersteuningsticket. Zie [Een serviceaanvraag maken om contact op te nemen met Office 365-ondersteuning](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)voor meer informatie. 

**V: Ik weet niet zeker wat de risico's zijn wanneer ik upgrade naar een nieuwere versie van Azure AD Connect. Kun je me bellen om me te helpen met de upgrade?**  
Als u hulp nodig hebt bij het upgraden naar een nieuwere versie van Azure AD Connect, opent u een ondersteuningsticket bij [Een serviceaanvraag maken om contact op te nemen met office 365-ondersteuning.](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)

## <a name="troubleshooting"></a>Problemen oplossen
**V: Hoe kan ik hulp krijgen met Azure AD Connect?**

[Zoek in de Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Zoek in de KB naar technische oplossingen voor veelvoorkomende probleemoplossingsproblemen met betrekking tot ondersteuning voor Azure AD Connect.

[Azure Active Directory-forums](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Zoek naar technische vragen en antwoorden of stel uw eigen vragen door naar [de Azure AD-community](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)te gaan.

[Ondersteuning voor Azure AD vragen](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**V: Waarom zie ik gebeurtenissen 6311 en 6401 optreden na synchronisatiestapfouten?**

De gebeurtenissen 6311 - **De server heeft een onverwachte fout ondervonden tijdens het uitvoeren van een callback** en 6401 - De **beheeragentcontroller heeft een onverwachte fout ondervonden** - worden altijd geregistreerd na een synchronisatiestapfout. Als u deze fouten wilt oplossen, moet u de synchronisatiestapfouten opschonen.  Zie [Fouten oplossen tijdens synchronisatie](tshoot-connect-sync-errors.md) en [problemen met objectsynchronisatie met Azure AD Connect-synchronisatie voor](tshoot-connect-objectsync.md) meer informatie.
