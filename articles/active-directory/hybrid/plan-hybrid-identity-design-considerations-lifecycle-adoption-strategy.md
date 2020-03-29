---
title: Hybride identiteitsontwerp - strategie voor levenscyclusacceptatie Azure | Microsoft Documenten
description: Helpt bij het definiëren van de hybride identiteitsbeheertaken op basis van de opties die beschikbaar zijn voor elke levenscyclusfase.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85f600c8bd46e699e80bf7b596574dc01467ef79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109316"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Bepalen van de adoptiestrategie voor hybride identiteitlevenscyclus
In deze taak definieert u de strategie voor identiteitsbeheer voor uw hybride identiteitsoplossing om te voldoen aan de bedrijfsvereisten die u hebt gedefinieerd in [Het bepalen van hybride identiteitsbeheertaken.](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

Als u de hybride identiteitsbeheertaken wilt definiëren op basis van de end-to-end-levenscyclus van identiteiten die eerder in deze stap wordt gepresenteerd, moet u de opties overwegen die beschikbaar zijn voor elke levenscyclusfase.

## <a name="access-management-and-provisioning"></a>Toegangsbeheer en -inrichting
Met een goede oplossing voor accounttoegangsbeheer kan uw organisatie precies bijhouden wie toegang heeft tot welke informatie in de hele organisatie.

Toegangscontrole is een kritieke functie van een gecentraliseerd, single-point provisioning systeem. Naast het beschermen van gevoelige informatie, geven toegangscontroles bestaande accounts bloot die niet-goedgekeurde autorisaties hebben of die niet langer nodig zijn. Om verouderde accounts te beheren, koppelt het inrichtingssysteem accountgegevens samen met gezaghebbende informatie over de gebruikers die eigenaar zijn van de accounts. Gezaghebbende gebruikersidentiteitsinformatie wordt meestal bewaard in de databases en mappen van human resources.

Accounts in geavanceerde IT-ondernemingen bevatten honderden parameters die de autoriteiten definiëren, en deze gegevens kunnen worden gecontroleerd door uw inrichtingssysteem. Nieuwe gebruikers kunnen worden geïdentificeerd met de gegevens die u opgeeft aan de gezaghebbende bron. De goedkeuringsmogelijkheid voor toegangsaanvragen initieert de processen die resourcevoorzieningen voor deze processen goedkeuren (of weigeren).

| Fase van levenscyclusbeheer | Ter plaatse | Cloud | Hybride |
| --- | --- | --- | --- |
| Rekeningbeheer en -voorzieningen |Met behulp van de serverfunctie AD DS (Active Directory® Domain Services) kunt u een schaalbare, veilige en beheerbare infrastructuur maken voor gebruikers- en resourcebeheer, en ondersteuning bieden voor Active Directory-toepassingen zoals Microsoft® Exchange Server. <br><br> [U groepen in AD DS inrichten via een identiteitsmanager](https://technet.microsoft.com/library/ff686261.aspx) <br>[U gebruikers inrichten in AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Beheerders kunnen toegangsbeheer gebruiken om gebruikerstoegang tot gedeelde bronnen te beheren voor beveiligingsdoeleinden. In Active Directory wordt het toegangscontrolebeheer beheerd op objectniveau door verschillende toegangsniveaus of machtigingen in te stellen voor objecten, zoals Volledig beheer, Schrijven, Lezen of Geen toegang. Toegangsbesturingselement in Active Directory definieert hoe verschillende gebruikers Active Directory-objecten kunnen gebruiken. Machtigingen voor objecten in Active Directory zijn standaard ingesteld op de veiligste instelling. |U moet een account aanmaken voor elke gebruiker die toegang heeft tot een Microsoft-cloudservice. U ook gebruikersaccounts wijzigen of verwijderen wanneer ze niet meer nodig zijn. Gebruikers hebben standaard geen beheerdersmachtigingen, maar u ze optioneel toewijzen. <br><br> In Azure Active Directory is een van de belangrijkste functies de mogelijkheid om toegang tot bronnen te beheren. Deze resources kunnen deel uitmaken van de adreslijst, zoals in het geval van machtigingen voor het beheren van objecten door middel van rollen in de adreslijst, of resources die zich buiten de adreslijst bevinden, zoals SaaS-toepassingen, Azure-services, en SharePoint-sites of on-premises resources. <br><br> In het centrum van de toegangsbeheeroplossing van Azure Active Directory bevindt zich de beveiligingsgroep. De resource-eigenaar (of de beheerder van de adreslijst of directory) kan een groep toewijzen om een bepaald toegangsrecht in te stellen voor de resources waarvan ze eigenaar zijn. De leden van de groep krijgen de toegang en de eigenaar van de resource kan het recht om de ledenlijst van een groep te beheren delegeren aan iemand anders, zoals een afdelingsmanager of een helpdeskbeheerder<br> <br> De sectie Groepen beheren in Azure AD biedt meer informatie over het beheren van toegang via groepen. |Active Directory-identiteiten uitbreiden naar de cloud via synchronisatie en federatie |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer
RBAC (Role-based access control) gebruikt rollen en inrichtingsbeleid om uw bedrijfsprocessen en regels te evalueren, te testen en af te dwingen voor het verlenen van toegang aan gebruikers. Belangrijke beheerders maken inrichtingsbeleid en wijzen gebruikers toe aan rollen en definiëren sets van rechten op resources voor deze rollen. RBAC breidt de oplossing voor identiteitsbeheer uit om softwaregebaseerde processen te gebruiken en handmatige interactie van gebruikers in het inrichtingsproces te verminderen.
Azure AD RBAC stelt het bedrijf in staat om het aantal bewerkingen te beperken dat een individu kan uitvoeren zodra hij toegang heeft tot de Azure-portal. Door RBAC te gebruiken om de toegang tot de portal te beheren, delegeren IT-beheerders de toegang door de volgende benaderingen voor toegangsbeheer te gebruiken:

* **Op groepen gebaseerde roltoewijzing:** u toegang toewijzen tot Azure AD-groepen die kunnen worden gesynchroniseerd vanuit uw lokale Active Directory. Dit stelt u in staat om gebruik te maken van de bestaande investeringen die uw organisatie heeft gedaan in tooling en processen voor het beheren van groepen. U ook de functie gedelegeerd groepsbeheer van Azure AD Premium gebruiken.
* **Maak gebruik van ingebouwde rollen in Azure:** u drie rollen gebruiken : Eigenaar, inzender en lezer, om ervoor te zorgen dat gebruikers en groepen toestemming hebben om alleen de taken uit te voeren die ze nodig hebben om hun werk te doen.
* **Gedetailleerde toegang tot bronnen**: U rollen toewijzen aan gebruikers en groepen voor een bepaald abonnement, resourcegroep of een afzonderlijke Azure-bron, zoals een website of database. Op deze manier u ervoor zorgen dat gebruikers toegang hebben tot alle resources die ze nodig hebben en geen toegang hebben tot bronnen die ze niet hoeven te beheren.

## <a name="provisioning-and-other-customization-options"></a>Inrichting en andere aanpassingsopties
Uw team kan bedrijfsplannen en vereisten gebruiken om te bepalen hoeveel de identiteitsoplossing moet worden aangepast. Een grote onderneming kan bijvoorbeeld een gefaseerd uitrolplan voor werkstromen en aangepaste adapters vereisen dat is gebaseerd op een tijdlijn voor het stapsgewijs inrichten van toepassingen die op grote schaal worden gebruikt in verschillende regio's. Een ander aanpassingsplan kan ervoor zorgen dat twee of meer toepassingen in een hele organisatie worden ingericht, na succesvolle tests. Interactie tussen gebruiker en toepassing kan worden aangepast en procedures voor het inrichten van resources kunnen worden gewijzigd om rekening te houden met geautomatiseerde inrichting.

U deprovision om een service of component te verwijderen. Als u bijvoorbeeld een account deprovisionert, wordt het account uit een bron verwijderd.

Het hybride model van het inrichten van resources combineert aanvraag- en op rollen gebaseerde benaderingen, die beide worden ondersteund door Azure AD. Voor een subset van werknemers of beheerde systemen wil een bedrijf mogelijk toegang automatiseren met een op rollen gebaseerde toewijzing. Een bedrijf kan ook alle andere toegangsaanvragen of uitzonderingen verwerken via een op aanvragen gebaseerd model. Sommige bedrijven kunnen beginnen met handmatige toewijzing en evolueren naar een hybride model, met de bedoeling om op een toekomstig moment volledig op rollen gebaseerde implementatie te maken.

Andere bedrijven kunnen het om zakelijke redenen onpraktisch vinden om volledige op rollen gebaseerde inrichting te bereiken en een hybride aanpak als een gewenst doel te targeten. Nog andere bedrijven kunnen tevreden zijn met alleen op aanvragen gebaseerde provisioning, en willen geen extra inspanning investeren om op rollen gebaseerde, geautomatiseerde inrichtingsbeleid te definiëren en te beheren.

## <a name="license-management"></a>Licentiebeheer
Met groepslicentiebeheer in Azure AD kunnen beheerders gebruikers toewijzen aan een beveiligingsgroep en Azure AD wijst automatisch licenties toe aan alle leden van de groep. Als een gebruiker vervolgens wordt toegevoegd aan of uit de groep wordt verwijderd, wordt een licentie automatisch toegewezen of verwijderd.

U groepen gebruiken die u synchroniseert vanuit on-premises AD of beheren in Azure AD. Door dit te koppelen aan Azure AD premium Self-Service Group Management u licentietoewijzing eenvoudig delegeren aan de juiste besluitvormers. U er zeker van zijn dat problemen zoals licentieconflicten en ontbrekende locatiegegevens automatisch worden opgelost.

## <a name="self-regulating-user-administration"></a>Zelfregulerende gebruikersadministratie
Wanneer uw organisatie resources begint in te richten voor alle interne organisaties, implementeert u de zelfregulerende gebruikersbeheercapaciteit. U de voordelen en voordelen van het inrichten van gebruikers over de grenzen van de organisatie realiseren. In deze omgeving wordt een wijziging in de status van een gebruiker automatisch weerspiegeld in toegangsrechten over de grenzen van de organisatie en regio's heen. U de inrichtingskosten verlagen en de toegangs- en goedkeuringsprocessen stroomlijnen. De implementatie realiseert het volledige potentieel van het implementeren van role-based access control voor end-to-end access management in uw organisatie. U de administratieve kosten verlagen door geautomatiseerde procedures voor het regelen van de gebruikersvoorziening. U de beveiliging verbeteren door de handhaving van beveiligingsbeleid te automatiseren en het beheer van de levenscyclus van gebruikers en het inrichten van resources voor grote gebruikerspopulaties stroomlijnen en centraliseren.

> [!NOTE]
> Zie Azure AD instellen voor selfservice-toepassingsbeheer voor meer informatie
> 
> 

Azure AD-services op basis van licenties (op basis van rechten) werken door een abonnement te activeren in uw Azure AD-directory/servicetenant. Zodra het abonnement actief is, kunnen de servicemogelijkheden worden beheerd door directory-/servicebeheerders en worden gebruikt door gelicentieerde gebruikers. 

## <a name="integration-with-other-3rd-party-providers"></a>Integratie met andere externe providers

Azure Active Directory biedt single-sign on en verbeterde toegang tot toepassingen beveiliging voor duizenden SaaS-toepassingen en on-premises webtoepassingen. Zie [Toepassingen integreren met Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md) voor meer informatie

## <a name="define-synchronization-management"></a>Synchronisatiebeheer definiëren
Wanneer u uw on-premises directory's integreert met Azure AD, worden uw gebruikers productiever omdat zij één identiteit hebben voor toegang tot zowel cloud als on-premises resources. Met deze integratie kunnen gebruikers en organisaties profiteren van het volgende:

* Organisaties kunnen gebruikers een gemeenschappelijke hybride identiteit bieden voor on-premises of cloudservices die gebruikmaken van Windows Server Active Directory en vervolgens verbinding maken met Azure Active Directory.
* Beheerders kunnen voorwaardelijke toegang bieden op basis van toepassingsbron, apparaat- en gebruikersidentiteit, netwerklocatie en meervoudige verificatie.
* Gebruikers kunnen hun gemeenschappelijke identiteit gebruiken via accounts in Azure AD voor Office 365, Intune, SaaS-apps en toepassingen van derden.
* Ontwikkelaars kunnen toepassingen bouwen die gebruikmaken van het algemene identiteitsmodel en toepassingen integreren in Active Directory on-premises of Azure voor cloudtoepassingen

De volgende figuur heeft een voorbeeld van een weergave op hoog niveau van identiteitssynchronisatieproces.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Identiteitssynchronisatieproces

Bekijk de volgende tabel om de synchronisatieopties te vergelijken:

| Optie synchronisatiebeheer | Voordelen | Nadelen |
| --- | --- | --- |
| Op basis van synchronisatie (via DirSync of AADConnect) |Gebruikers en groepen gesynchroniseerd vanuit on-premises en cloud <br>  **Beleidsbeheer:** Accountbeleid kan worden ingesteld via Active Directory, waarmee de beheerder wachtwoordbeleid, werkstation, beperkingen, vergrendelingsbesturingselementen en meer kan beheren zonder extra taken in de cloud uit te voeren.  <br>  **Toegangscontrole:** Kan de toegang tot de cloudservice beperken, zodat de services toegankelijk zijn via de bedrijfsomgeving, via online servers of beide. <br>  Minder ondersteuningsoproepen: als gebruikers minder wachtwoorden hebben om te onthouden, zullen ze deze minder snel vergeten. <br>  Beveiliging: Gebruikersidentiteiten en -informatie worden beschermd omdat alle servers en services die in één aanmelding worden gebruikt, on-premises worden gemasterd en gecontroleerd. <br>  Ondersteuning voor sterke verificatie: U sterke verificatie (ook wel tweestapsverificatie genoemd) gebruiken met de cloudservice. Als u echter sterke verificatie gebruikt, moet u één aanmelding gebruiken. | |
| Federatie-based (via AD FS) |Ingeschakeld door Security Token Service (STS). Wanneer u een STS configureert om één aanmeldingstoegang te bieden met een Microsoft-cloudservice, creëert u een federatieve vertrouwensrelatie tussen uw on-premises STS en het federatieve domein dat u hebt opgegeven in uw Azure AD-tenant. <br> Hiermee kunnen eindgebruikers dezelfde set referenties gebruiken om toegang te krijgen tot meerdere bronnen <br>eindgebruikers hoeven niet meerdere sets referenties te onderhouden. Toch moeten de gebruikers hun referenties verstrekken aan elk van de deelnemende resources.,B2B- en B2C-scenario's ondersteund. |Vereist gespecialiseerd personeel voor de implementatie en het onderhoud van speciale on-premises AD FS-servers. Er zijn beperkingen op het gebruik van sterke verificatie als u van plan bent AD FS voor uw STS te gebruiken. Zie [Geavanceerde opties configureren voor AD FS 2.0 voor](https://go.microsoft.com/fwlink/?linkid=235649)meer informatie. |

> [!NOTE]
> Zie [Uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor meer informatie.
> 
> 

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

