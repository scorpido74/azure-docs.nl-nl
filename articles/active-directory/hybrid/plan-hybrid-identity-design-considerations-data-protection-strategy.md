---
title: Hybride identiteitsontwerp - strategie voor gegevensbescherming Azure | Microsoft Documenten
description: U definieert de strategie voor gegevensbescherming voor uw hybride identiteitsoplossing om te voldoen aan de bedrijfsvereisten die u hebt gedefinieerd.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0186d862968259aae73071cfecd7d62443d0256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109359"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definieer de strategie voor gegevensbescherming voor uw hybride identiteitsoplossing
In deze taak definieert u de strategie voor gegevensbescherming voor uw hybride identiteitsoplossing om te voldoen aan de bedrijfsvereisten die u hebt gedefinieerd in:

* [Vereisten voor gegevensbescherming bepalen](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Vereisten voor inhoudsbeheer vaststellen](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Vereisten voor toegangsbeheer vaststellen](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Vereisten voor reageren op incidenten vaststellen](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Opties voor gegevensbescherming definiëren
Zoals uitgelegd in [Adreslijstsynchronisatievereisten bepalen,](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)kan Microsoft Azure AD synchroniseren met uw on-premises Active Directory Domain Services (AD DS). Met deze integratie kunnen organisaties Azure AD gebruiken om de referenties van gebruikers te verifiëren wanneer ze toegang proberen te krijgen tot bedrijfsbronnen. U dit doen voor beide scenario's: gegevens in rust on-premises en in de cloud. Toegang tot gegevens in Azure AD vereist gebruikersverificatie via een security token service (STS).

Na verificatie wordt de gebruikersnaam (UPN) voorgelezen uit het verificatietoken. Vervolgens bepaalt het autorisatiesysteem de gerepliceerde partitie en container die overeenkomen met het domein van de gebruiker. Informatie over het bestaan, de ingeschakelde status en de rol van de gebruiker helpt vervolgens het autorisatiesysteem te bepalen of toegang tot de doeltenant is geautoriseerd voor de gebruiker in die sessie. Bepaalde geautoriseerde acties (met name het opnieuw instellen van gebruikers en wachtwoorden) maken een controlespoor dat een tenantbeheerder vervolgens gebruikt om nalevingsinspanningen of -onderzoeken te beheren.

Het verplaatsen van gegevens van uw on-premises datacenter naar Azure Storage via een internetverbinding is mogelijk niet altijd haalbaar vanwege het gegevensvolume, de beschikbaarheid van bandbreedte of andere overwegingen. De [Azure Storage Import/Export Service](../../storage/common/storage-import-export-service.md) biedt een hardwaregebaseerde optie voor het plaatsen/ophalen van grote hoeveelheden gegevens in blob-opslag. Hiermee u [bitlocker-versleutelde](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) harde schijven rechtstreeks naar een Azure-datacenter verzenden waar cloudoperators de inhoud uploaden naar uw opslagaccount, of ze kunnen uw Azure-gegevens downloaden naar uw schijven om naar u terug te keren. Alleen versleutelde schijven worden geaccepteerd voor dit proces (met behulp van een BitLocker-sleutel gegenereerd door de service zelf tijdens de taakinstelling). De BitLocker-sleutel wordt afzonderlijk aan Azure geleverd, waardoor de bandsleutel wordt gedeeld.

Aangezien gegevens in transit in verschillende scenario's kunnen plaatsvinden, is het ook relevant om te weten dat Microsoft Azure [virtuele netwerken](https://azure.microsoft.com/documentation/services/virtual-network/) gebruikt om het verkeer van tenants van elkaar te isoleren, waarbij maatregelen worden genomen zoals firewalls op host- en gastniveau, IP-pakketfiltering, poortblokkering en HTTPS-eindpunten. De meeste interne communicatie van Azure, waaronder infrastructuur-infrastructuur en infrastructuur-naar-klant (on-premises), zijn echter ook versleuteld. Een ander belangrijk scenario is de communicatie binnen Azure-datacenters; Microsoft beheert netwerken om ervoor te zorgen dat geen enkele VM zich kan voordoen of afluisteren op het IP-adres van een andere. TLS/SSL wordt gebruikt bij toegang tot Azure Storage of SQL-databases of bij het maken van verbinding met Cloud Services. In dit geval is de klantbeheerder verantwoordelijk voor het verkrijgen van een TLS/SSL-certificaat en het implementeren ervan naar hun tenant-infrastructuur. Gegevensverkeer dat zich verplaatst tussen virtuele machines in dezelfde implementatie of tussen tenants in één implementatie via Microsoft Azure Virtual Network, kan worden beveiligd via versleutelde communicatieprotocollen zoals HTTPS, SSL/TLS of anderen.

Afhankelijk van hoe u de vragen hebt beantwoord in [De vereisten voor gegevensbescherming bepalen,](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)moet u kunnen bepalen hoe u uw gegevens wilt beschermen en hoe de hybride identiteitsoplossing u kan helpen bij dat proces. In de volgende tabel worden de opties weergegeven die door Azure worden ondersteund en die beschikbaar zijn voor elk scenario voor gegevensbescherming.

| Opties voor gegevensbescherming | In rust in de cloud | In rust op locatie | In-transit |
| --- | --- | --- | --- |
| BitLocker-stationsversleuteling |X |X | |
| SQL Server om databases te versleutelen |X |X | |
| VM-naar-VM-versleuteling | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Lees [Compliance by Feature](https://azure.microsoft.com/support/trust-center/services/) in Microsoft Azure Trust [Center](https://azure.microsoft.com/support/trust-center/) voor meer informatie over de certificeringen waaraan elke Azure-service voldoet.
> Aangezien de opties voor gegevensbescherming een meerlaagse benadering gebruiken, is vergelijking tussen deze opties niet van toepassing op deze taak. Zorg ervoor dat u gebruikmaakt van alle opties die beschikbaar zijn voor elke status van de gegevens.
>
>

## <a name="define-content-management-options"></a>Opties voor inhoudsbeheer definiëren

Een voordeel van het gebruik van Azure AD om een hybride identiteitsinfrastructuur te beheren, is dat het proces volledig transparant is vanuit het perspectief van de eindgebruiker. De gebruiker probeert toegang te krijgen tot een gedeelde bron, de bron vereist verificatie, de gebruiker moet een verificatieverzoek naar Azure AD sturen om het token te verkrijgen en toegang te krijgen tot de bron. Dit hele proces gebeurt op de achtergrond, zonder interactie van de gebruiker. 

Organisaties die zich zorgen maken over gegevensprivacy vereisen meestal gegevensclassificatie voor hun oplossing. Als hun huidige on-premises infrastructuur al gebruik maakt van gegevensclassificatie, is het mogelijk om Azure AD te gebruiken als de belangrijkste opslagplaats voor de identiteit van de gebruiker. Een veelgebruikte tool die on-premises wordt gebruikt voor gegevensclassificatie heet [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) voor Windows Server 2012 R2. Met deze tool u gegevens op bestandsservers in uw privécloud identificeren, classificeren en beveiligen. Het is ook mogelijk om de [automatische bestandsclassificatie](https://technet.microsoft.com/library/hh831672.aspx) in Windows Server 2012 te gebruiken om deze taak uit te voeren.

Als uw organisatie geen gegevensclassificatie heeft, maar gevoelige bestanden moet beschermen zonder dat er on-premises servers worden toegevoegd, kunnen ze Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx)gebruiken.  Azure RMS maakt gebruik van versleutelings-, identiteits- en autorisatiebeleid om uw bestanden en e-mail te beveiligen en werkt op meerdere apparaten: telefoons, tablets en pc's. Omdat Azure RMS een cloudservice is, hoeft u geen vertrouwensrelaties expliciet te configureren met andere organisaties voordat u beveiligde inhoud met hen delen. Als ze al een Office 365- of een Azure AD-adreslijst hebben, wordt samenwerking tussen verschillende organisaties automatisch ondersteund. U ook alleen de directorykenmerken synchroniseren die Azure RMS nodig heeft om een algemene identiteit voor uw on-premises Active Directory-accounts te ondersteunen, met behulp van Azure Active Directory Synchronization Services (AAD Sync) of Azure AD Connect.

Een essentieel onderdeel van content management is om te begrijpen wie toegang heeft tot welke bron, daarom is een rijke logging mogelijkheid belangrijk voor de identity management oplossing. Azure AD biedt logboek over 30 dagen, waaronder:

* Wijzigingen in het lidmaatschap van rollen (bijvoorbeeld de gebruiker toegevoegd aan de rol Globale beheerder)
* Referentie-updates (bijvoorbeeld wachtwoordwijzigingen)
* Domeinbeheer (bijvoorbeeld een aangepast domein verifiëren, een domein verwijderen)
* Toepassingen toevoegen of verwijderen
* Gebruikersbeheer (bijvoorbeeld toevoegen, verwijderen, bijwerken van een gebruiker)
* Licenties toevoegen of verwijderen

> [!NOTE]
> Lees [Microsoft Azure Security and Audit Log Management](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) voor meer informatie over beheermogelijkheden voor logboekregistratie in Azure.
> Afhankelijk van hoe u de vragen hebt beantwoord in [Vereisten voor inhoudsbeheer](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)bepalen, moet u kunnen bepalen hoe u de inhoud wilt beheren in uw hybride identiteitsoplossing. Hoewel alle opties die in tabel 6 worden weergegeven, kunnen worden geïntegreerd met Azure AD, is het belangrijk om te definiëren welke beter geschikt is voor uw bedrijfsbehoeften.
>
>

| Opties voor inhoudsbeheer | Voordelen | Nadelen |
| --- | --- | --- |
| Gecentraliseerd on-premises (Active Directory Rights Management Server) |Volledige controle over de serverinfrastructuur die verantwoordelijk is voor het classificeren van de gegevens <br> Ingebouwde mogelijkheden in Windows Server, geen extra licentie of abonnement nodig <br> Kan worden geïntegreerd met Azure AD in een hybride scenario <br> Ondersteunt IRM-mogelijkheden (Information Rights Management) in Microsoft Online-services zoals Exchange Online en SharePoint Online en Office 365 <br> Ondersteunt on-premises Microsoft-serverproducten, zoals Exchange Server, SharePoint Server en bestandsservers waarop De Windows Server en bestandsclassificatie-infrastructuur (FCI) worden uitgevoerd. |Hoger onderhoud (blijf op de hoogte van updates, configuratie en potentiële upgrades), omdat IT eigenaar is van de server <br> On-premises een serverinfrastructuur vereisen<br> Maakt geen gebruik van Azure-mogelijkheden native |
| Gecentraliseerd in de cloud (Azure RMS) |Eenvoudiger te beheren in vergelijking met de on-premises oplossing <br> Kan worden geïntegreerd met AD DS in een hybride scenario <br>  Volledig geïntegreerd met Azure AD <br> Er is geen server on-premises nodig om de service te implementeren <br> Ondersteunt on-premises Microsoft-serverproducten zoals Exchange Server, SharePoint, Server en bestandsservers waarop Windows Server- en bestandsclassificatie, Infrastructuur (FCI) wordt uitgevoerd <br> IT, kan volledige controle over de sleutel van hun tenant met BYOK-mogelijkheid. |Uw organisatie moet een cloudabonnement hebben dat RMS ondersteunt <br> Uw organisatie moet beschikken over een Azure AD-map ter ondersteuning van gebruikersverificatie voor RMS |
| Hybride (Azure RMS geïntegreerd met, On-Premises Active Directory Rights Management Server) |Dit scenario ververzamelt de voordelen van beide, gecentraliseerde on-premises en in de cloud. |Uw organisatie moet een cloudabonnement hebben dat RMS ondersteunt <br> Uw organisatie moet beschikken over een Azure AD-map ter ondersteuning van gebruikersverificatie voor RMS, <br> Vereist een verbinding tussen Azure-cloudservice en on-premises infrastructuur |

## <a name="define-access-control-options"></a>Opties voor toegangscontrole definiëren
Door gebruik te maken van de verificatie-, autorisatie- en toegangscontrolemogelijkheden die beschikbaar zijn in Azure AD, u uw bedrijf een centrale identiteitsopslagplaats laten gebruiken, terwijl gebruikers en partners eenmalige aanmelding (SSO) kunnen gebruiken, zoals in de volgende afbeelding wordt weergegeven:

![gecentraliseerd beheer](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Gecentraliseerd beheer en volledige integratie met andere mappen

Azure Active Directory biedt één aanmelding voor duizenden SaaS-toepassingen en on-premises webtoepassingen. Zie de [compatibiliteitslijst van azure Active Directory-federatie: identiteitsproviders van derden die kunnen worden gebruikt om eenenkel aanmeldingsartikel te implementeren](how-to-connect-fed-compatibility.md) voor meer informatie over de sso-derde partij die door Microsoft is getest. Deze mogelijkheid stelt organisatie in staat om verschillende B2B-scenario's te implementeren en tegelijkertijd controle te houden over het identiteits- en toegangsbeheer. Tijdens het B2B-ontwerpproces is het echter belangrijk om de verificatiemethode te begrijpen die door de partner wordt gebruikt en om te valideren of deze methode wordt ondersteund door Azure. Momenteel worden de volgende methoden ondersteund door Azure AD:

* Security Assertion Markup Language (SAML)
* Oauth
* Kerberos
* Tokens
* Certificaten

> [!NOTE]
> lees [Azure Active Directory Authentication Protocols](https://msdn.microsoft.com/library/azure/dn151124.aspx) om meer informatie te weten over elk protocol en de mogelijkheden ervan in Azure.
>
>

Met behulp van de Azure AD-ondersteuning kunnen mobiele bedrijfstoepassingen dezelfde eenvoudige verificatie-ervaring voor Mobiele Services gebruiken, zodat werknemers zich kunnen aanmelden bij hun mobiele toepassingen met hun zakelijke Active Directory-referenties. Met deze functie wordt Azure AD ondersteund als identiteitsprovider in Mobiele services naast de andere identiteitsproviders die al worden ondersteund (waaronder Microsoft-accounts, Facebook ID, Google ID en Twitter ID). Als de on-premises apps de gebruikersreferentie gebruiken die zich bij de AD DS van het bedrijf bevindt, moet de toegang van partners en gebruikers die uit de cloud komen transparant zijn. U de conditional access-controle van de gebruiker beheren naar (cloudgebaseerde) webtoepassingen, web-API, Microsoft-cloudservices, SaaS-toepassingen van derden en native (mobiele) clienttoepassingen, en de voordelen hebben van beveiliging, auditing, rapportage in één Plaats. Het wordt echter aanbevolen om de implementatie te valideren in een niet-productieomgeving of met een beperkt aantal gebruikers.

> [!TIP]
> het is belangrijk om te vermelden dat Azure AD geen groepsbeleid heeft zoals AD DS heeft. Als u het beleid voor apparaten wilt afdwingen, hebt u een oplossing voor het beheer van mobiele apparaten nodig, zoals [Microsoft Intune.](https://technet.microsoft.com/library/jj676587.aspx)
>
>

Zodra de gebruiker is geverifieerd met Azure AD, is het belangrijk om het toegangsniveau dat de gebruiker heeft te evalueren. Het toegangsniveau dat de gebruiker over een resource heeft, kan variëren. Hoewel Azure AD een extra beveiligingslaag kan toevoegen door de toegang tot bepaalde bronnen te beheren, moet u er rekening mee houden dat de bron zelf ook afzonderlijk een eigen lijst met toegangsbeheer kan hebben, zoals het toegangscontrolebeheer voor bestanden in een bestandsserver. In de volgende afbeelding wordt een overzicht gegeven van de niveaus van toegangscontrole die u hebben in een hybride scenario:

![toegangsbeheer](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Elke interactie in het diagram in figuur X vertegenwoordigt één toegangscontrolescenario dat kan worden gedekt door Azure AD. Hieronder vindt u een beschrijving van elk scenario:

1. Voorwaardelijke toegang tot toepassingen die on-premises worden gehost: u geregistreerde apparaten gebruiken met toegangsbeleid voor toepassingen die zijn geconfigureerd om AD FS te gebruiken met Windows Server 2012 R2.

2. Toegangsbeheer tot de Azure-portal: azure u ook de toegang tot de portal beheren met behulp van rbac (Role-based access control).Access Control to the Azure portal: Azure also lets you be controle access to the portal by using role-based access control (RBAC)). Met deze methode kan het bedrijf het aantal bewerkingen beperken dat een individu kan uitvoeren in de Azure-portal. Door RBAC te gebruiken om de toegang tot de portal te beheren, kunnen IT-beheerders toegang delegeren met behulp van de volgende benaderingen voor toegangsbeheer:

   - Roltoewijzing op basis van groepen: u toegang toewijzen tot Azure AD-groepen die kunnen worden gesynchroniseerd vanuit uw lokale Active Directory. Hiermee u gebruikmaken van de bestaande investeringen die uw organisatie heeft gedaan in tooling en processen voor het beheren van groepen. U ook de functie gedelegeerd groepsbeheer van Azure AD Premium gebruiken.
   - Gebruik ingebouwde rollen in Azure: u drie rollen gebruiken : Eigenaar, inzender en lezer, om ervoor te zorgen dat gebruikers en groepen toestemming hebben om alleen de taken uit te voeren die ze nodig hebben om hun werk te doen.
   -  Gedetailleerde toegang tot bronnen: u rollen toewijzen aan gebruikers en groepen voor een bepaald abonnement, resourcegroep of een afzonderlijke Azure-bron, zoals een website of database. Op deze manier u ervoor zorgen dat gebruikers toegang hebben tot alle resources die ze nodig hebben en geen toegang hebben tot bronnen die ze niet hoeven te beheren.

   > [!NOTE]
   > Als u toepassingen bouwt en het toegangscontrolevoor hen wilt aanpassen, is het ook mogelijk om Azure AD-toepassingsrollen te gebruiken voor autorisatie. Bekijk dit [voorbeeld webapp-roleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) over het bouwen van uw app om deze mogelijkheid te gebruiken.


3. Voorwaardelijke toegang voor Office 365-toepassingen met Microsoft Intune: IT-beheerders kunnen apparaatbeleid voor voorwaardelijke toegang inrichten om bedrijfsbronnen te beveiligen, terwijl informatiewerkers op compatibele apparaten tegelijkertijd toegang krijgen tot de services. 
  
4. Voorwaardelijke toegang voor Saas-apps: met [deze functie](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) u toegangsregels voor multi-factorauthenticatie per toepassing configureren en de mogelijkheid om toegang te blokkeren voor gebruikers die niet op een vertrouwd netwerk zijn. U de regels voor meervoudige verificatie toepassen op alle gebruikers die aan de toepassing zijn toegewezen, of alleen voor gebruikers binnen bepaalde beveiligingsgroepen. Gebruikers kunnen worden uitgesloten van de vereiste voor meervoudige verificatie als ze toegang hebben tot de toepassing vanaf een IP-adres dat zich binnen het netwerk van de organisatie bevindt.

Aangezien de opties voor toegangscontrole een meerlaagse benadering gebruiken, is vergelijking tussen deze opties niet van toepassing op deze taak. Zorg ervoor dat u gebruik maakt van alle opties die beschikbaar zijn voor elk scenario waarvoor u de toegang tot uw bronnen moet beheren.

## <a name="define-incident-response-options"></a>Opties voor incidentrespons definiëren
Azure AD kan IT helpen bij het identificeren van potentiële beveiligingsrisico's in de omgeving door de activiteit van gebruikers te controleren. IT kan azure AD Access- en Gebruiksrapporten gebruiken om inzicht te krijgen in de integriteit en beveiliging van de directory van uw organisatie. Met deze informatie kan een IT-beheerder beter bepalen waar mogelijke beveiligingsrisico's kunnen liggen, zodat ze adequaat kunnen plannen om die risico's te beperken.  [Het Azure AD Premium-abonnement](../fundamentals/active-directory-get-started-premium.md) heeft een reeks beveiligingsrapporten waarmee IT deze informatie kan verkrijgen. [Azure AD-rapporten](../reports-monitoring/overview-reports.md) worden als volgt gecategoriseerd:

* **Anomalierapporten**: Bevatten aanmeldingsgebeurtenissen die afwijkend bleken te zijn. Het doel is om u bewust te maken van dergelijke activiteiten en u in staat stellen om een beslissing te nemen over de vraag of een gebeurtenis verdacht is.
* **Integrated Application report**: Geeft inzicht in hoe cloudapplicaties worden gebruikt in uw organisatie. Azure Active Directory biedt integratie met duizenden cloudtoepassingen.
* **Foutrapporten:** geef fouten aan die kunnen optreden bij het inrichten van accounts op externe toepassingen.
* **Gebruikersspecifieke rapporten**: Weergave van activiteitsgegevens voor een specifieke gebruiker.
* **Activiteitslogboeken:** Bevat een record van alle gecontroleerde gebeurtenissen in de afgelopen 24 uur, de afgelopen 7 dagen of de laatste 30 dagen, evenals wijzigingen in groepsactiviteiten en wachtwoordreset- en registratieactiviteit.

> [!TIP]
> Een ander rapport dat ook kan helpen het Incident Response team werken aan een aanvraag is de [gebruiker met gelekte referenties](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) rapport. In dit rapport worden overeenkomsten weergegeven tussen de lijst met gelekte referenties en uw tenant.
>


Andere belangrijke ingebouwde rapporten in Azure AD die kunnen worden gebruikt tijdens een incidentresponseonderzoek en:

* **Activiteit voor het opnieuw instellen van**wachtwoorden: geef de beheerder inzicht in hoe actief het opnieuw instellen van wachtwoorden wordt gebruikt in de organisatie.
* **Registratieactiviteit voor het opnieuw instellen van wachtwoorden**: geeft inzicht in welke gebruikers hun methoden voor het opnieuw instellen van wachtwoorden hebben geregistreerd en welke methoden ze hebben geselecteerd.
* **Groepsactiviteit:** biedt een geschiedenis van wijzigingen in de groep (bijvoorbeeld gebruikers die zijn toegevoegd of verwijderd) die zijn gestart in het toegangspaneel.

Naast de kernrapportagemogelijkheden van Azure AD Premium die u gebruiken tijdens een incidentresponseonderzoeksproces, kan IT ook gebruik maken van het auditrapport om informatie te verkrijgen, zoals:

* Wijzigingen in het lidmaatschap van rollen (bijvoorbeeld de gebruiker toegevoegd aan de rol Globale beheerder)
* Referentie-updates (bijvoorbeeld wachtwoordwijzigingen)
* Domeinbeheer (bijvoorbeeld het verifiëren van een aangepast domein, het verwijderen van een domein)
* Toepassingen toevoegen of verwijderen
* Gebruikersbeheer (bijvoorbeeld het toevoegen, verwijderen, bijwerken van een gebruiker)
* Licenties toevoegen of verwijderen

Aangezien de opties voor incidentrespons een meerlaagse benadering gebruiken, is vergelijking tussen deze opties niet van toepassing op deze taak. Zorg ervoor dat u gebruikmaakt van alle opties die beschikbaar zijn voor elk scenario waarvoor u azure AD-rapportagemogelijkheden moet gebruiken als onderdeel van het incidentresponsproces van uw bedrijf.

## <a name="next-steps"></a>Volgende stappen
[Hybride identiteitsbeheertaken bepalen](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)
