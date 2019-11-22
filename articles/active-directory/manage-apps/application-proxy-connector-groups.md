---
title: Apps op afzonderlijke netwerken publiceren via connector groepen-Azure AD
description: Bevat informatie over het maken en beheren van groepen van connectors in Azure AD-toepassingsproxy.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fa1de0a0e3bb91480212381e07b17875bf0bf4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275569"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publiceren van toepassingen op afzonderlijke netwerken en locaties met behulp van connectorgroepen

Klanten gebruikmaken van Azure AD-toepassingsproxy voor steeds meer scenario's en toepassingen. Dus hebben we aangebracht App Proxy ook meer flexibiliteit doordat meer topologieën. U kunt Application Proxy connectorgroepen kunt maken, zodat u specifieke connectors voor het bieden van specifieke toepassingen kunt toewijzen. Deze mogelijkheid biedt u meer controle en manieren om uw Application Proxy-implementatie te optimaliseren.

Elke Application Proxy-connector is toegewezen aan een connectorgroep. Alle connectors die deel uitmaken van dezelfde groep verbindingslijn fungeren als een eenheid voor hoge beschikbaarheid en taakverdeling. Alle connectors deel uitmaken van een connectorgroep. Als u geen groepen maken, klikt u vervolgens zijn alle uw connectors in een standaard-groep. De beheerder kan nieuwe groepen maken en connectors aan ze toewijzen in Azure portal.

Alle toepassingen zijn toegewezen aan een connectorgroep. Als u geen groepen maken, zijn klikt u vervolgens al uw toepassingen toegewezen aan een standaardgroep. Maar als u uw connectors in groepen delen, kunt u elke toepassing werkt met een specifieke connectorgroep instellen. In dit geval fungeren alleen verbindingslijnen in die groep voor de toepassing op verzoek. Deze functie is handig als uw toepassingen worden gehost in verschillende locaties. Kunt u connectorgroepen op basis van locatie, zodat toepassingen altijd door connectors die zich fysiek dicht bij ze worden verwerkt.

> [!TIP]
> Als u een grote Application Proxy-implementatie hebt, niet alle toepassingen aan de standaard-connector-groep toewijzen. Op die manier kunnen nieuwe connectors niet live verkeer niet ontvangen totdat u ze aan een connectorgroep actieve toewijzen. Deze configuratie kunt u connectors plaatsen in een niet-actieve modus door terug verplaatsen naar de standaard-groep, zodat u onderhoud uitvoeren kunt zonder gevolgen voor uw gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u wilt groeperen uw connectors, die u hebt om te controleren of u [geïnstalleerd meerdere connectors](application-proxy-add-on-premises-application.md). Wanneer u een nieuwe connector installeert, wordt automatisch lid wordt van de **standaard** connectorgroep.

## <a name="create-connector-groups"></a>Connectorgroepen maken

Gebruik deze stappen om zoveel connectorgroepen als u wilt maken.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory** > **bedrijfstoepassingen** > **toepassingsproxy**.
1. Selecteer **nieuwe connectorgroep**. De blade nieuwe Connectorgroep wordt weergegeven.

   ![Hiermee wordt het scherm weer gegeven om een nieuwe connector groep te selecteren](./media/application-proxy-connector-groups/new-group.png)

1. Geef een naam op voor uw nieuwe connectorgroep en vervolgens de vervolgkeuzelijst gebruiken om te selecteren welke connectors deel uitmaken van deze groep.
1. Selecteer **Opslaan**.

## <a name="assign-applications-to-your-connector-groups"></a>Toepassingen toewijzen aan uw connectorgroepen

Volg deze stappen voor elke toepassing die u hebt gepubliceerd met toepassingsproxy. Wanneer u deze publiceert, of kunt u deze stappen om te wijzigen van de toewijzing wanneer u wilt, kunt u een toepassing aan een connectorgroep toewijzen.

1. Selecteer in het dashboard management voor uw directory **bedrijfstoepassingen** > **alle toepassingen** > de toepassing die u wilt toewijzen aan een connectorgroep > **Toepassingsproxy**.
1. Gebruik de **Connectorgroep** in het vervolgkeuzemenu om te selecteren van de groep die u wilt dat de toepassing te gebruiken.
1. Selecteer **opslaan** de wijziging toepassen.

## <a name="use-cases-for-connector-groups"></a>Use cases voor connectorgroepen

Connectorgroepen kunnen nuttig zijn voor verschillende scenario's, met inbegrip van:

### <a name="sites-with-multiple-interconnected-datacenters"></a>-Sites met meerdere onderling verbonden datacenters

Veel organisaties beschikken over een aantal onderling verbonden datacenters. In dit geval wilt u zo veel verkeer binnen het datacenter mogelijk houden, omdat meerdere datacenters koppelingen duur en traag zijn. U kunt connectors in elk datacenter om alleen de toepassingen die zich in het datacenter bevinden implementeren. Deze aanpak minimaliseert de cross-datacenter-koppelingen en biedt een volledig transparant-ervaring aan uw gebruikers.

### <a name="applications-installed-on-isolated-networks"></a>Toepassingen worden geïnstalleerd op de geïsoleerde netwerken

Toepassingen kunnen worden gehost in netwerken die geen deel uitmaken van de belangrijkste bedrijfsnetwerk. U kunt connectorgroepen gebruiken voor het installeren van toegewezen connectors in geïsoleerde netwerken ook om toepassingen te isoleren met het netwerk. Dit gebeurt meestal wanneer een leverancier van derden een bepaalde toepassing voor uw organisatie onderhoudt.

Connectorgroepen kunnen u voor het installeren van toegewezen connectors voor netwerken die alleen specifieke toepassingen publiceren waardoor het eenvoudiger en veiliger dan uitbesteden Toepassingsbeheer op externe leveranciers.

### <a name="applications-installed-on-iaas"></a>Toepassingen worden geïnstalleerd op IaaS

Connectorgroepen bieden voor toepassingen die zijn geïnstalleerd op IaaS voor toegang tot de cloud, een algemene service voor het beveiligen van de toegang tot alle apps. Connectorgroepen geen extra afhankelijkheden maken in uw bedrijfsnetwerk of fragment van de app-ervaring. Connectors kunnen worden geïnstalleerd op elk clouddatacenter en alleen de toepassingen die zich in dat netwerk bevinden fungeren. U kunt meerdere connectors voor het bereiken van hoge beschikbaarheid installeren.

Neem bijvoorbeeld een organisatie met meerdere virtuele machines die zijn verbonden met hun eigen IaaS gehost virtueel netwerk. Als u wilt toestaan dat werknemers deze toepassingen, zijn particuliere netwerk verbonden met het bedrijfsnetwerk met behulp van site-naar-site VPN. Dit biedt een goede ervaring voor werknemers die zich op locatie zijn. Maar mogelijk niet ideaal voor externe werknemers, omdat hiervoor extra on-premises infrastructuur voor het routeren van toegang, zoals u in het onderstaande diagram kunt zien:

![Diagram dat het Azure AD IaaS-netwerk illustreert](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Met Azure AD Application Proxy connectorgroepen, kunt u een algemene service voor het beveiligen van de toegang tot alle toepassingen zonder dat er extra afhankelijkheid gemaakt in uw bedrijfsnetwerk inschakelen:

![Azure AD IaaS meerdere Cloud leveranciers](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Meerdere forests: verschillende connectorgroepen voor elk forest

De meeste klanten die Application Proxy hebben geïmplementeerd zijn met behulp van de eenmalige aanmelding (SSO) mogelijk door het uitvoeren van Kerberos-beperkte delegatie (KCD). Om dit te bereiken, moeten van de connector-machines worden toegevoegd aan een domein dat de gebruikers naar de toepassing kan delegeren. Forest-overschrijdende mogelijkheden biedt ondersteuning voor KCD. Maar voor bedrijven die beschikken over verschillende omgevingen voor meerdere forests zonder vertrouwensrelatie tussen één connector kan niet worden gebruikt voor alle forests. 

In dit geval kunnen specifieke connectors worden geïmplementeerd per forest en ingesteld op het leveren van toepassingen die zijn gepubliceerd om te fungeren, alleen de gebruikers van dit specifieke forest. Elke connectorgroep vertegenwoordigt een ander forest. Terwijl de tenant en het merendeel van de ervaring is unified voor alle forests, kunnen gebruikers worden toegewezen aan hun forest-toepassingen met behulp van Azure AD-groepen.

### <a name="disaster-recovery-sites"></a>Disaster Recovery sites

Er zijn twee verschillende methoden die u met een herstelsite na een noodgeval (DR uitvoeren kunt), afhankelijk van hoe uw sites worden geïmplementeerd:

* Als uw DR-site is ingebouwd in de modus actief-actief waar het precies hetzelfde als de primaire site en heeft de dezelfde netwerk- en AD-instellingen, kunt u de connectors maken op de DR-site in de dezelfde connectorgroep als de primaire site. Hierdoor kan Azure AD voor het detecteren van failovers voor u.
* Als uw DR-site gescheiden van de primaire site is, u een andere connectorgroep in de DR-site maken kunt en een (1) back-uptoepassingen hebt of 2) handmatig de bestaande toepassing aan de DR-connectorgroep omleiden naar behoefte.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Meerdere bedrijven dienen van één tenant

Er zijn veel verschillende manieren voor het implementeren van een model waarin een één-provider wordt geïmplementeerd en onderhoudt de Azure AD-gerelateerde services voor meerdere bedrijven. Connectorgroepen zodat de beheerder de connectors en toepassingen in verschillende groepen te scheiden. Er is een manier die geschikt is voor kleine bedrijven, dat één Azure AD tenant terwijl de andere bedrijven hun eigen domeinnaam en netwerken hebben. Dit geldt ook voor scenario's voor M & A en situaties waarbij een enkel IT-afdeling fungeert voor meerdere bedrijven vanwege regelgeving of zakelijke redenen.

## <a name="sample-configurations"></a>Voorbeelden van configuraties

Enkele voorbeelden die u kunt implementeren, zijn de volgende connectorgroepen.

### <a name="default-configuration--no-use-for-connector-groups"></a>Standaardconfiguratie – geen nut voor connectorgroepen

Als u connectorgroepen niet gebruikt, wordt de configuratie eruit als volgt:

![Voor beelden van Azure AD geen connector groepen](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Deze configuratie is voldoende voor kleine implementaties en tests. Het werkt ook goed als uw organisatie een platte netwerktopologie heeft.

### <a name="default-configuration-and-an-isolated-network"></a>Standaardconfiguratie en een geïsoleerd netwerk

Deze configuratie is een verbetering van de standaard één, waarin een specifieke app die wordt uitgevoerd in een geïsoleerd netwerk zoals IaaS virtuele netwerk is:

![Voor beelden van Azure AD geen connector groepen en een geïsoleerd netwerk](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Aanbevolen configuratie – verschillende specifieke groepen en een standaard-groep voor inactiviteit

De aanbevolen configuratie voor grote en complexe organisaties is dat de standaard-connector-groep als een groep die geen nut heeft voor alle toepassingen en wordt gebruikt voor niet-actieve of geïnstalleerde connectors. Alle toepassingen die worden geleverd met behulp van connectorgroepen van de aangepaste. Hierdoor kunnen de complexiteit van de scenario's die hierboven worden beschreven.

In het volgende voorbeeld wordt heeft het bedrijf twee datacentra, A en B, met twee connectors die u beschikbaar elke site maakt. Elke site heeft verschillende toepassingen die erop worden uitgevoerd.

![Voor beeld van een bedrijf met 2 data centers en twee connectors](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure AD Application Proxy connectors](application-proxy-connectors.md)
* [Eenmalige aanmelding inschakelen](what-is-single-sign-on.md)
