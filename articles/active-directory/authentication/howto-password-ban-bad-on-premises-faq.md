---
title: Veelgestelde vragen over azure AD-wachtwoordbeveiliging
description: Veelgestelde vragen voor Azure AD-wachtwoordbeveiliging bekijken in een on-premises Active Directory Domain Services-omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: abef6e52e37cfa2faeb426bc59bb0de5a52a6658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671666"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>On-premises veelgestelde vragen over Azure AD-wachtwoordbeveiliging

In deze sectie vindt u antwoorden op veelgestelde vragen over Azure AD Password Protection.

## <a name="general-questions"></a>Algemene vragen

**V: Welke richtlijnen moeten gebruikers krijgen voor het selecteren van een veilig wachtwoord?**

De huidige richtlijnen van Microsoft over dit onderwerp zijn te vinden op de volgende link:

[Richtlijnen voor Microsoft-wachtwoorden](https://www.microsoft.com/research/publication/password-guidance)

**V: Wordt on-premises Azure AD Password Protection ondersteund in niet-openbare clouds?**

Nee: on-premises Azure AD Password Protection wordt alleen ondersteund in de openbare cloud. Er is geen datum aangekondigd voor niet-openbare cloudbeschikbaarheid.

De Azure AD-portal staat wel wijzigingen toe van de on-premises specifieke "Password protection for Windows Server Active Directory"-configuratie, zelfs in niet-openbare clouds; dergelijke veranderingen zullen worden volgehouden, maar anders zal nooit van kracht worden. Registratie van on-premises proxy agents of forests wordt niet ondersteund wanneer niet-openbare cloudreferenties worden gebruikt, en dergelijke registratiepogingen zullen altijd mislukken.

**V: Hoe kan ik azure AD Password Protection-voordelen toepassen op een subset van mijn on-premises gebruikers?**

Wordt niet ondersteund. Eenmaal geïmplementeerd en ingeschakeld, maakt Azure AD Password Protection geen onderscheid: alle gebruikers ontvangen gelijke beveiligingsvoordelen.

**V: Wat is het verschil tussen een wachtwoordwijziging en een wachtwoordset (of reset)?**

Een wachtwoordwijziging is wanneer een gebruiker een nieuw wachtwoord kiest nadat hij heeft bewezen dat hij kennis heeft van het oude wachtwoord. Een wachtwoordwijziging is bijvoorbeeld wat er gebeurt wanneer een gebruiker zich aanmeldt bij Windows en vervolgens wordt gevraagd een nieuw wachtwoord te kiezen.

Een wachtwoordset (ook wel een wachtwoordreset genoemd) is wanneer een beheerder het wachtwoord van een account vervangt door een nieuw wachtwoord, bijvoorbeeld door het beheerprogramma Active Directory Voor gebruikers en computers te gebruiken. Deze bewerking vereist een hoog niveau van bevoegdheden (meestal domeinbeheerder) en de persoon die de bewerking uitvoert, heeft meestal geen kennis van het oude wachtwoord. Helpdeskscenario's voeren vaak wachtwoordsets uit, bijvoorbeeld wanneer u een gebruiker bijstaat die zijn wachtwoord is vergeten. U ziet ook gebeurtenissen in wachtwoordsets wanneer een gloednieuw gebruikersaccount voor het eerst wordt gemaakt met een wachtwoord.

Het wachtwoordvalidatiebeleid gedraagt zich hetzelfde, ongeacht of er een wachtwoordwijziging of set wordt uitgevoerd. De Azure AD Password Protection DC Agent-service registreert verschillende gebeurtenissen om u te informeren of er een wachtwoordwijziging of ingestelde bewerking is uitgevoerd.  Zie [Azure AD Password Protection monitoring and logging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**V: Waarom worden dubbele wachtwoordafwijzingsgebeurtenissen geregistreerd wanneer u een zwak wachtwoord probeert in te stellen met de module Active Directory- gebruikers en computersbeheer?**

De module Active Directory-beheergebruikers en computers probeert eerst het nieuwe wachtwoord in te stellen met behulp van het Kerberos-protocol. Na het mislukken doet de module een tweede poging om het wachtwoord in te stellen met behulp van een verouderd (SAM RPC)-protocol (de specifieke protocollen die worden gebruikt zijn niet belangrijk). Als het nieuwe wachtwoord door Azure AD Password Protection als zwak wordt beschouwd, leidt dit modulegedrag tot twee sets van afwijzingsgebeurtenissen voor het opnieuw instellen van wachtwoorden.

**V: Waarom worden wachtwoordvalidatiegebeurtenissen voor Azure AD Password Protection geregistreerd met een lege gebruikersnaam?**

Active Directory ondersteunt de mogelijkheid om een wachtwoord te testen om te zien of het voldoet aan de huidige vereisten voor wachtwoordcomplexiteit van het domein, bijvoorbeeld met behulp van de [NetValidatePasswordPolicy-api.](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) Wanneer een wachtwoord op deze manier wordt gevalideerd, bevat de test ook validatie door op een wachtwoord filter-dll gebaseerde producten zoals Azure AD Password Protection, maar de gebruikersnamen die worden doorgegeven aan een bepaald wachtwoordfilter, zijn leeg. In dit scenario valideert Azure AD Password Protection het wachtwoord nog steeds met het momenteel geldende wachtwoordbeleid en geeft het een gebeurtenislogboekbericht af om de uitkomst vast te leggen, maar het gebeurtenislogboekbericht heeft lege gebruikersnaamvelden.

**V: Wordt azure AD Password Protection naast andere producten op basis van wachtwoordfilter geïnstalleerd?**

Ja. Ondersteuning voor meerdere geregistreerde wachtwoordfilterdlls is een belangrijke Windows-functie en niet specifiek voor Azure AD Password Protection. Alle geregistreerde wachtwoordfilterdlls moeten akkoord gaan voordat een wachtwoord wordt geaccepteerd.

**V: Hoe kan ik Azure AD-wachtwoordbeveiliging implementeren en configureren in mijn Active Directory-omgeving zonder Azure te gebruiken?**

Wordt niet ondersteund. Azure AD Password Protection is een Azure-functie die ondersteunt dat deze wordt uitgebreid naar een on-premises Active Directory-omgeving.

**V: Hoe kan ik de inhoud van het beleid op Active Directory-niveau wijzigen?**

Wordt niet ondersteund. Het beleid kan alleen worden beheerd met de Azure AD-portal. Zie ook eerdere vraag.

**V: Waarom is DFSR vereist voor sysvol-replicatie?**

FRS (de voorganger technologie voor DFSR) heeft veel bekende problemen en wordt volledig niet ondersteund in nieuwere versies van Windows Server Active Directory. Het nultesten van Azure AD Password Protection wordt uitgevoerd op FRS-geconfigureerde domeinen.

Zie voor meer informatie de volgende artikelen:

[De aanvraag voor het migreren van sysvol-replicatie naar DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Het einde is nabij voor FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Als uw domein DFSR nog niet gebruikt, moet u migreren om DFSR te gebruiken voordat u Azure AD-wachtwoordbeveiliging installeert. Zie voor meer informatie de volgende link:

[SYSVOL-replicatiemigratiehandleiding: FRS naar DFS-replicatie](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> De Azure AD Password Protection DC Agent-software wordt momenteel geïnstalleerd op domeincontrollers in domeinen die FRS nog steeds gebruiken voor sysvol-replicatie, maar de software werkt niet goed in deze omgeving. Extra negatieve bijwerkingen zijn afzonderlijke bestanden die niet worden gerepliceerd en sysvol-herstelprocedures lijken te slagen, maar slagen er in stilte niet in om alle bestanden te repliceren. U moet uw domein zo snel mogelijk migreren om DFSR te gebruiken, zowel voor de inherente voordelen van DFSR als om de implementatie van Azure AD Password Protection te deblokkeren. Toekomstige versies van de software worden automatisch uitgeschakeld wanneer ze worden uitgevoerd in een domein dat nog steeds FRS gebruikt.

**V: Hoeveel schijfruimte heeft de functie nodig op het domein sysvol delen?**

Het precieze ruimtegebruik varieert omdat het afhankelijk is van factoren zoals het aantal en de lengte van de verboden tokens in de microsoft-lijst met verboden extensies en de aangepaste lijst per tenant, plus versleuteling overhead. De inhoud van deze lijsten zal in de toekomst waarschijnlijk groeien. Met dat in gedachten, een redelijke verwachting is dat de functie ten minste vijf (5) megabytes aan ruimte op het domein sysvol delen nodig zal hebben.

**V: Waarom is een reboot nodig om de DC-agentsoftware te installeren of te upgraden?**

Deze vereiste wordt veroorzaakt door het belangrijkste Windows-gedrag.

**V: Is er een manier om een DC-agent te configureren om een specifieke proxyserver te gebruiken?**

Nee. Aangezien de proxyserver stateloos is, is het niet belangrijk welke specifieke proxyserver wordt gebruikt.

**V: Is het goed om de Azure AD Password Protection Proxy-service naast andere services zoals Azure AD Connect te implementeren?**

Ja. De Azure AD Password Protection Proxy-service en Azure AD Connect mogen nooit rechtstreeks met elkaar in conflict komen.

Helaas is er een onverenigbaarheid gevonden tussen de versie van de Microsoft Azure AD Connect Agent Updater-service die is geïnstalleerd door de Azure AD Password Protection Proxy-software en de versie van de service die is geïnstalleerd door de [Azure Active Directory Application Proxy-software.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Deze onverenigbaarheid kan ertoe leiden dat de Agent Updater-service geen contact kan opnemen met Azure voor software-updates. Het wordt afgeraden om Azure AD Password Protection Proxy en Azure Active Directory Application Proxy op dezelfde machine te installeren.

**V: In welke volgorde moeten de DC-agenten en proxy's worden geïnstalleerd en geregistreerd?**

Elke bestelling van proxy agent installatie, DC agent installatie, forest registratie, en Proxy registratie wordt ondersteund.

**V: Moet ik me zorgen maken over de prestatiehit op mijn domeincontrollers van het implementeren van deze functie?**

De Azure AD Password Protection DC Agent-service mag geen aanzienlijke invloed hebben op de prestaties van de domeincontroller in een bestaande gezonde Active Directory-implementatie.

Voor de meeste Active Directory-implementaties zijn wachtwoordwijzigingsbewerkingen een klein deel van de totale werkbelasting op een bepaalde domeincontroller. Stel je bijvoorbeeld een Active Directory-domein voor met 10000 gebruikersaccounts en een MaxPasswordAge-beleid dat is ingesteld op 30 dagen. Gemiddeld ziet dit domein elke dag 10000/30=~ 333 wachtwoordwijzigingsbewerkingen, wat een klein aantal bewerkingen is voor zelfs één domeincontroller. Overweeg een potentieel worst case scenario: stel dat die ~ 333 wachtwoord wijzigingen op een enkele DC werden gedaan over een enkel uur. Dit scenario kan zich bijvoorbeeld voordoen wanneer veel werknemers allemaal op een maandagochtend komen werken. Zelfs in dat geval zijn we nog steeds op zoek naar ~ 333/60 minuten = zes wachtwoordwijzigingen per minuut, wat opnieuw geen significante belasting is.

Als uw huidige domeincontrollers echter al op prestatiebeperkte niveaus werken (bijvoorbeeld maximaal cpu, schijfruimte, schijf-I/O, enz.), is het raadzaam om extra domeincontrollers toe te voegen of de beschikbare schijfruimte uit te breiden voordat u deze functie implementeert. Zie ook de vraag hierboven over sysvol schijfruimte gebruik hierboven.

**V: Ik wil Azure AD Password Protection testen op slechts een paar DC's in mijn domein. Is het mogelijk om wijzigingen in het wachtwoord van gebruikers te dwingen om die specifieke DC's te gebruiken?**

Nee. Het Windows-clientbesturingssysteem bepaalt welke domeincontroller wordt gebruikt wanneer een gebruiker zijn wachtwoord wijzigt. De domeincontroller is geselecteerd op basis van factoren zoals Active Directory-site- en subnettoewijzingen, omgevingsspecifieke netwerkconfiguratie, enz. Azure AD Password Protection heeft geen controle over deze factoren en kan geen invloed hebben op welke domeincontroller is geselecteerd om het wachtwoord van een gebruiker te wijzigen.

Een manier om dit doel gedeeltelijk te bereiken, is door Azure AD Password Protection te implementeren op alle domeincontrollers in een bepaalde Active Directory-site. Deze aanpak biedt een redelijke dekking voor de Windows-clients die zijn toegewezen aan die site, en dus ook voor de gebruikers die inloggen op die clients en het wijzigen van hun wachtwoorden.

**V: Als ik de Azure AD Password Protection DC Agent-service installeer op alleen de primaire domeincontroller (PDC), worden alle andere domeincontrollers in het domein dan ook beveiligd?**

Nee. Wanneer het wachtwoord van een gebruiker wordt gewijzigd op een bepaalde niet-PDC-domeincontroller, wordt het wachtwoord met duidelijke tekst nooit naar de PDC verzonden (dit idee is een veelvoorkomende misvatting). Zodra een nieuw wachtwoord is geaccepteerd op een bepaalde DC, dat DC gebruikt dat wachtwoord om de verschillende authenticatie-protocol-specifieke hashes van dat wachtwoord te maken en vervolgens blijft die hashes in de directory. Het wachtwoord met duidelijke tekst blijft niet bestaan. De bijgewerkte hashes worden vervolgens gerepliceerd naar de PDC. Gebruikerswachtwoorden kunnen in sommige gevallen rechtstreeks op de PDC worden gewijzigd, opnieuw afhankelijk van verschillende factoren, zoals netwerktopologie en Active Directory-siteontwerp. (Zie de vorige vraag.)

Samengevat is de implementatie van de Azure AD Password Protection DC Agent-service op de PDC vereist om 100% beveiligingsdekking van de functie in het hele domein te bereiken. Het implementeren van de functie op de PDC biedt alleen geen beveiligingsvoordelen voor Azure AD Password Protection voor andere DC's in het domein.

**V: Waarom werkt aangepaste smart lockout niet, zelfs nadat de agents zijn geïnstalleerd in mijn on-premises Active Directory-omgeving?**

Aangepaste smart lock-out wordt alleen ondersteund in Azure AD. Wijzigingen in de aangepaste instellingen voor slimme uitsluiting in de Azure AD-portal hebben geen effect op de on-premises Active Directory-omgeving, zelfs niet met de geïnstalleerde agents.

**V: Is er een Beheerpakket voor System Center Operations Manager beschikbaar voor Azure AD Password Protection?**

Nee.

**V: Waarom weigert Azure AD nog steeds zwakke wachtwoorden, ook al heb ik het beleid geconfigureerd om in de auditmodus te zijn?**

De controlemodus wordt alleen ondersteund in de on-premises Active Directory-omgeving. Azure AD staat impliciet altijd in de 'afdwingen'-modus wanneer wachtwoorden worden geëvalueerd.

**V: Mijn gebruikers zien het traditionele Windows-foutbericht wanneer een wachtwoord wordt geweigerd door Azure AD Password Protection. Is het mogelijk om deze foutmelding aan te passen, zodat gebruikers weten wat er echt is gebeurd?**

Nee. De foutmelding die gebruikers zien wanneer een wachtwoord wordt geweigerd door een domeincontroller, wordt beheerd door de clientmachine, niet door de domeincontroller. Dit gebeurt of een wachtwoord wordt geweigerd door het standaard beleid voor Active Directory-wachtwoord of door een oplossing op basis van een wachtwoordfilter, zoals Azure AD-wachtwoordbeveiliging.

## <a name="additional-content"></a>Aanvullende inhoud

De volgende koppelingen maken geen deel uit van de belangrijkste azure AD-wachtwoordbeveiliging-documentatie, maar kunnen een nuttige bron van aanvullende informatie over de functie zijn.

[Azure AD Password Protection is nu algemeen beschikbaar!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Handleiding voor phishingbescherming e-mail – Deel 15: Implementeer de Microsoft Azure AD Password Protection Service (ook voor On-Premises!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection en Smart Lockout staan nu in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified-ondersteuningstraining beschikbaar

Als u meer wilt weten over Azure AD Password Protection en deze wilt implementeren in uw omgeving, u profiteren van een proactieve microsoft-service die beschikbaar is voor klanten met een Premier- of Unified-ondersteuningscontract. De service heet Azure Active Directory: Password Protection. Neem contact op met uw Technisch Accountmanager voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u een on-premises Azure AD Password Protection-vraag hebt die hier niet wordt beantwoord, dient u hieronder een feedback-item in - bedankt!

[Wachtwoordbeveiliging in Azure AD implementeren](howto-password-ban-bad-on-premises-deploy.md)
