---
title: Aanbevolen procedures voor gegevensbeveiliging en versleuteling - Microsoft Azure
description: In dit artikel vindt u een reeks aanbevolen procedures voor gegevensbeveiliging en versleuteling met behulp van ingebouwde Azure-mogelijkheden.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: c5bf62f434b2095f7200b5562c38c252a0195c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243495"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Best practices voor gegevensbeveiliging en -versleuteling in Azure
In dit artikel worden aanbevolen procedures voor gegevensbeveiliging en versleuteling beschreven.

De aanbevolen procedures zijn gebaseerd op een consensus van mening en werken met de huidige Azure-platformmogelijkheden en functiesets. Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regelmatig bijgewerkt om deze veranderingen weer te geven.

## <a name="protect-data"></a>Gegevens beveiligen
Om gegevens in de cloud te beschermen, moet u rekening houden met de mogelijke status waarin uw gegevens kunnen optreden en welke besturingselementen beschikbaar zijn voor die status. Aanbevolen procedures voor Azure-gegevensbeveiliging en -versleuteling hebben betrekking op de volgende gegevensstatussen:

- In rust: Dit omvat alle informatieopslagobjecten, containers en typen die statisch op fysieke media bestaan, of het nu magnetische of optische schijf is.
- Onderweg: wanneer gegevens worden overgedragen tussen onderdelen, locaties of programma's, is deze onderweg. Voorbeelden hiervan zijn overdracht via het netwerk, over een servicebus (van on-premises naar cloud en vice versa, inclusief hybride verbindingen zoals ExpressRoute), of tijdens een invoer-/uitvoerproces.

## <a name="choose-a-key-management-solution"></a>Kies een belangrijke beheeroplossing

Het beschermen van uw sleutels is essentieel om uw gegevens in de cloud te beschermen.

Met [Azure Key Vault](/azure/key-vault/key-vault-overview) kunt u de cryptografische sleutels en geheimen beveiligen die cloudtoepassingen en -services gebruiken. Key Vault stroomlijnt het beheerproces voor sleutels en zorgt dat u de controle houdt over de sleutels waarmee uw gegevens toegankelijk zijn en worden versleuteld. Ontwikkelaars kunnen binnen enkele minuten sleutels voor ontwikkel- en testdoeleinden maken en deze vervolgens migreren naar productiesleutels. Beveiligingsadministrator kunnen wanneer dit nodig is machtigingen aan sleutels verlenen (en intrekken).

U Key Vault gebruiken om meerdere beveiligde containers te maken, genaamd kluizen. Deze kluizen worden ondersteund door HSM's. Kluizen verminderen de kans op onbedoeld verlies van beveiligingsinformatie door de opslag van toepassingsgeheimen te centraliseren. Sleutelkluizen regelen en registreren ook de toegang tot alles wat erin is opgeslagen. Azure Key Vault kan TLS-certificaten (Transport Layer Security) aanvragen en vernieuwen. Het biedt functies voor een robuuste oplossing voor certificaatlevenscyclusbeheer.

Azure Key Vault is ontworpen om toepassingssleutels en -geheimen te ondersteunen. Key Vault is niet bedoeld als winkel voor gebruikerswachtwoorden.

Hieronder volgen de aanbevolen beveiligingsprocedures voor het gebruik van Key Vault.

**Aanbevolen procedures**: geef gebruikers, groepen en toepassingen toegang op een specifiek bereik.   
**Detail:** Gebruik de vooraf gedefinieerde rollen van RBAC. Als u bijvoorbeeld toegang wilt verlenen aan een gebruiker om sleutelkluizen te beheren, wijst u de vooraf gedefinieerde rol [Sleutelvault-bijdrager](/azure/role-based-access-control/built-in-roles) aan deze gebruiker toe aan een specifiek bereik. Het bereik in dit geval zou een abonnement, een resourcegroep of slechts een specifieke sleutelkluis zijn. Als de vooraf gedefinieerde rollen niet aan uw behoeften voldoen, u [uw eigen rollen definiëren.](/azure/role-based-access-control/custom-roles)

**Aanbevolen procedures**: Bepaal waargebruikers toegang toe hebben.   
**Detail**: De toegang tot een sleutelkluis wordt geregeld via twee afzonderlijke interfaces: beheervlak en gegevensvlak. Het toegangsbeheer van de beheerlaag en het toegangsbeheer van de gegevenslaag werken onafhankelijk van elkaar.

Gebruik RBAC om te bepalen waartoe gebruikers toegang hebben. Als u bijvoorbeeld een toepassing toegang wilt verlenen tot het gebruik van sleutels in een sleutelkluis, hoeft u alleen toegangsmachtigingen voor gegevensvlak toe te kennen met behulp van toegangsbeleid voor belangrijke kluizen en is er geen toegang tot het beheervlak nodig voor deze toepassing. Als u echter wilt dat een gebruiker eigenschappen en tags van de kluis kan lezen, maar geen toegang heeft tot de sleutels, geheimen of certificaten, kunt u deze gebruiker leestoegang geven met RBAC. Hiervoor is geen toegang tot de gegevenslaag vereist.

**Aanbevolen procedures**: Sla certificaten op in uw sleutelkluis. Uw certificaten zijn van hoge waarde. In de verkeerde handen kan de beveiliging van uw toepassing of de beveiliging van uw gegevens in het gedrang komen.   
**Detail:** Azure Resource Manager kan certificaten die zijn opgeslagen in Azure Key Vault veilig implementeren in Azure Vm's wanneer de VM's worden geïmplementeerd. Door het juiste toegangsbeleid voor de sleutelkluis in te stellen, bepaalt u ook wie toegang tot uw certificaat krijgt. Een ander voordeel is dat u al uw certificaten op één plek beheert in Azure Key Vault. Zie [Certificaten implementeren voor VM's vanuit door de klant beheerde Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) voor meer informatie.

**Aanbevolen procedures:** zorg ervoor dat u het verwijderen van sleutelkluizen of sleutelkluisobjecten herstellen.   
**Detail:** Verwijdering van sleutelkluizen of sleutelkluisobjecten kan onbedoeld of kwaadaardig zijn. Schakel de beveiliging voor voorlopig verwijderen en opschonen van Key Vault in, met name voor sleutels die worden gebruikt voor het versleutelen van data-at-rest. Het verwijderen van deze sleutels is gelijk aan gegevensverlies, dus u kunt zo nodig verwijderde kluizen en kluisobjecten herstellen. Oefen regelmatig key vault recovery s.

> [!NOTE]
> Als een gebruiker inzendersmachtigingen (RBAC) heeft voor de beheerlaag van een sleutelkluis, kan deze gebruiker zichzelf toegang geven tot de gegevenslaag door toegangsbeleid voor de sleutelkluis in te stellen. We raden u aan om goed te bepalen wie toegang heeft tot uw sleutelkluizen, om ervoor te zorgen dat alleen geautoriseerde personen toegang hebben tot uw sleutelkluizen, sleutels, geheimen en certificaten en deze beheren.
>
>

## <a name="manage-with-secure-workstations"></a>Beheren met beveiligde werkstations

> [!NOTE]
> De abonnementsbeheerder of -eigenaar moet een werkstation voor beveiligde toegang of een geprivilegieerd toegangswerkstation gebruiken.
>
>

Omdat de overgrote meerderheid van de aanvallen zich richten op de eindgebruiker, wordt het eindpunt een van de belangrijkste aanvalspunten. Een aanvaller die het eindpunt compromitteert, kan de referenties van de gebruiker gebruiken om toegang te krijgen tot de gegevens van de organisatie. De meeste endpoint-aanvallen maken gebruik van het feit dat gebruikers beheerders zijn in hun lokale werkstations.

**Aanbevolen procedures**: Gebruik een veilig beheerwerkstation om gevoelige accounts, taken en gegevens te beschermen.   
**Detail:** Gebruik een [geprivilegieerd toegangswerkstation](https://technet.microsoft.com/library/mt634654.aspx) om het aanvalsoppervlak in werkstations te verkleinen. Deze veilige beheerwerkstations kunnen u helpen sommige van deze aanvallen te beperken en ervoor te zorgen dat uw gegevens veiliger zijn.

**Beste praktijken**: Zorg voor bescherming van eindpunten.   
**Detail:** Beveiligingsbeleid afdwingen op alle apparaten die worden gebruikt om gegevens te verbruiken, ongeacht de gegevenslocatie (cloud of on-premises).

## <a name="protect-data-at-rest"></a>Data-at-rest beschermen

[Gegevensversleuteling in rust](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) is een verplichte stap in de richting van gegevensprivacy, naleving en gegevenssoevereiniteit.

**Aanbevolen procedures**: Schijfversleuteling toepassen om uw gegevens te beveiligen.   
**Detail:** Azure [Disk Encryption gebruiken](/azure/security/azure-security-disk-encryption-overview). Het stelt IT-beheerders in staat om Windows- en Linux IaaS VM-schijven te versleutelen. Schijfversleuteling combineert de industriestandaard Windows BitLocker-functie en de Linux dm-crypt-functie om volumeversleuteling te bieden voor het besturingssysteem en de gegevensschijven.

Azure Storage en Azure SQL Database versleutelen gegevens standaard in rust en veel services bieden versleuteling als optie. U kunt Azure Key Vault gebruiken om controle te houden over de codes die toegang tot uw gegevens hebben en deze versleutelen. Zie [ondersteuning voor versleutelingsmodelvoor Azure-bronproviders voor meer informatie](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Aanbevolen procedures**: Gebruik versleuteling om risico's in verband met ongeautoriseerde toegang tot gegevens te beperken.   
**Detail:** Versleutel uw schijven voordat u gevoelige gegevens naar deze gegevens schrijft.

Organisaties die gegevensversleuteling niet afdwingen, worden meer blootgesteld aan problemen met de vertrouwelijkheid van gegevens. Onbevoegde of malafide gebruikers kunnen bijvoorbeeld gegevens in gecompromitteerde accounts stelen of ongeautoriseerde toegang krijgen tot gegevens die zijn gecodeerd in Clear Format. Bedrijven moeten ook bewijzen dat ze ijverig zijn en de juiste beveiligingscontroles gebruiken om hun gegevensbeveiliging te verbeteren om te voldoen aan de industrieregelgeving.

## <a name="protect-data-in-transit"></a>Gegevens tijdens de overdracht beveiligen

Het beschermen van gegevens tijdens de overdracht moet een essentieel onderdeel zijn van uw strategie om gegevens te beschermen. Omdat gegevens tussen veel locaties worden verplaatst, adviseren we over het algemeen om SSL-/TLS-protocollen te gebruiken om gegevens tussen verschillende locaties uit te wisselen. In sommige omstandigheden wilt u mogelijk het gehele communicatiekanaal tussen uw lokale infrastructuur en de cloud isoleren met behulp van een VPN.

U kunt gepaste beschermingsmaatregelen gebruiken, zoals HTTPS of VPN, voor gegevens die worden verplaatst tussen uw lokale infrastructuur en Azure. Gebruik [Azure VPN Gateway](../../vpn-gateway/index.yml)bij het verzenden van versleuteld verkeer tussen een virtueel Azure-netwerk en een on-premises locatie via het openbare internet.

Hieronder volgen best practices die specifiek zijn voor het gebruik van Azure VPN Gateway, SSL/TLS en HTTPS.

**Aanbevolen procedures**: Beveiligde toegang vanaf meerdere werkstations die on-premises tot een virtueel Azure-netwerk zijn gevestigd.   
**Detail**: Gebruik [site-to-site VPN](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Aanbevolen procedures**: Beveiligde toegang vanaf een afzonderlijk werkstation dat on-premises is gevestigd tot een virtueel Azure-netwerk.   
**Detail:** Gebruik [point-to-site VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Aanbevolen procedures**: Verplaats grotere gegevenssets over een speciale wanverbinding met hoge snelheid.   
**Detail**: [ExpressRoute gebruiken](/azure/expressroute/expressroute-introduction). Als u ervoor kiest om ExpressRoute te gebruiken, kunt u de gegevens ook in de toepassing zelf versleutelen door SSL/TLS of andere protocollen te gebruiken voor extra bescherming.

**Aanbevolen procedures**: Interactie met Azure Storage via de Azure-portal.   
**Detail**: Alle transacties vinden plaats via HTTPS. U [de Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) ook via HTTPS gebruiken om te communiceren met [Azure Storage.](https://azure.microsoft.com/services/storage/)

Organisaties die niet om gegevens te beschermen tijdens het transport zijn meer vatbaar voor [man-in-the-middle-aanvallen](https://technet.microsoft.com/library/gg195821.aspx), [afluisteren](https://technet.microsoft.com/library/gg195641.aspx), en sessie kaping. Deze aanvallen kunnen de eerste stap zijn om toegang te krijgen tot vertrouwelijke gegevens.

## <a name="secure-email-documents-and-sensitive-data"></a>Beveiligde e-mail, documenten en gevoelige gegevens

U wilt e-mail, documenten en gevoelige gegevens die u buiten uw bedrijf deelt, beheren en beveiligen. [Azure Information Protection](/azure/information-protection/) is een cloudoplossing die een organisatie helpt bij het classificeren, labelen en beveiligen van documenten en e-mails. Dit kan automatisch worden gedaan door beheerders die regels en voorwaarden definiëren, handmatig door gebruikers, of een combinatie waarbij gebruikers aanbevelingen krijgen.

Classificatie is te allen tijde identificeerbaar, ongeacht waar de gegevens worden opgeslagen of met wie deze worden gedeeld. De labels bevatten visuele markeringen, zoals een koptekst, voettekst of watermerk. Metagegevens worden als ongecodeerde tekst aan bestanden en koppen van e-mails toegevoegd. De duidelijke tekst zorgt ervoor dat andere diensten, zoals oplossingen om gegevensverlies te voorkomen, de classificatie kunnen identificeren en passende maatregelen kunnen nemen.

De beveiligingstechnologie maakt gebruik van Azure Rights Management (Azure RMS). Deze technologie is geïntegreerd met andere Microsoft-cloudservices en -toepassingen, zoals Office 365 en Azure Active Directory. Deze beveiligingstechnologie maakt gebruik van beleidsregels voor versleuteling, identiteit en verificatie. Beveiliging die wordt toegepast via Azure RMS blijft bij de documenten en e-mails, onafhankelijk van de locatie, binnen of buiten uw organisatie, netwerken, bestandsservers en toepassingen.

Deze oplossing voor informatiebescherming houdt u de controle over uw gegevens, zelfs wanneer deze worden gedeeld met andere mensen. U Azure RMS ook gebruiken met uw eigen bedrijfssoftware en oplossingen voor informatiebescherming van softwareleveranciers, of deze toepassingen en oplossingen nu on-premises of in de cloud zijn.

U wordt aangeraden dat u:

- [Azure Information Protection](/azure/information-protection/deployment-roadmap) voor uw organisatie implementeren.
- Pas labels toe die aansluiten bij uw bedrijfsvereisten. Bijvoorbeeld: Pas een label met de naam "zeer vertrouwelijk" toe op alle documenten en e-mails die topgeheime gegevens bevatten, om deze gegevens te classificeren en te beschermen. Vervolgens hebben alleen geautoriseerde gebruikers toegang tot deze gegevens, met eventuele beperkingen die u opgeeft.
- Configureer [gebruikslogboekregistratie voor Azure RMS,](/azure/information-protection/log-analyze-usage) zodat u controleren hoe uw organisatie de beveiligingsservice gebruikt.

Organisaties die zwak zijn op het niveau [van gegevensclassificatie](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) en bestandsbeveiliging, zijn mogelijk gevoeliger voor gegevenslekken of misbruik van gegevens. Met de juiste bestandsbescherming u gegevensstromen analyseren om inzicht te krijgen in uw bedrijf, riskant gedrag te detecteren en corrigerende maatregelen te nemen, de toegang tot documenten bij te houden, enzovoort.

## <a name="next-steps"></a>Volgende stappen

Zie [aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging die u gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met Azure.

De volgende bronnen zijn beschikbaar om meer algemene informatie te verstrekken over Azure-beveiliging en gerelateerde Microsoft-services:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - voor up-to-date informatie over de nieuwste azure security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - waar beveiligingsproblemen van Microsoft, waaronder problemen met Azure, kunnen worden gemeld of via e-mail naarsecure@microsoft.com
