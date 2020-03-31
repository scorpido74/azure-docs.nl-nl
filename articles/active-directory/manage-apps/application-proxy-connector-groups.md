---
title: Apps publiceren op afzonderlijke netwerken via verbindingsgroepen - Azure AD
description: Hiermee u groepen connectors maken en beheren in Azure AD-toepassingsproxy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275569"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Toepassingen publiceren op afzonderlijke netwerken en locaties met behulp van verbindingsgroepen

Klanten gebruiken de toepassingsproxy van Azure AD voor steeds meer scenario's en toepassingen. Daarom hebben we App Proxy nog flexibeler gemaakt door meer topologieën mogelijk te maken. U connectorgroepen voor toepassingsproxy maken, zodat u specifieke connectors toewijzen om specifieke toepassingen te serveren. Deze mogelijkheid geeft u meer controle en manieren om uw implementatie van toepassingsproxy te optimaliseren.

Elke toepassingsproxyconnector is toegewezen aan een connectorgroep. Alle connectoren die tot dezelfde connectorgroep behoren, fungeren als een aparte eenheid voor hoge beschikbaarheid en taakverdeling. Alle connectoren behoren tot een connectorgroep. Als u geen groepen maakt, bevinden al uw connectors zich in een standaardgroep. Uw beheerder kan nieuwe groepen maken en er connectoren aan toewijzen in de Azure-portal.

Alle toepassingen worden toegewezen aan een verbindingsgroep. Als u geen groepen maakt, worden al uw toepassingen toegewezen aan een standaardgroep. Maar als u uw connectors in groepen ordeeert, u instellen dat elke toepassing met een specifieke connectorgroep werkt. In dit geval dienen alleen de connectors in die groep de toepassing op verzoek. Deze functie is handig als uw toepassingen op verschillende locaties worden gehost. U verbindingsgroepen maken op basis van locatie, zodat toepassingen altijd worden bediend door connectoren die fysiek dicht bij hen staan.

> [!TIP]
> Als u een grote implementatie van toepassingsproxy hebt, wijst u geen toepassingen toe aan de standaardconnectorgroep. Op die manier ontvangen nieuwe connectors geen live verkeer totdat u ze aan een actieve connectorgroep toewijst. Met deze configuratie u connectors ook in een niet-actieve modus plaatsen door ze terug te verplaatsen naar de standaardgroep, zodat u onderhoud uitvoeren zonder dat dit gevolgen heeft voor uw gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u uw connectors wilt groeperen, moet u ervoor zorgen dat u [meerdere connectoren hebt geïnstalleerd.](application-proxy-add-on-premises-application.md) Wanneer u een nieuwe connector installeert, wordt deze automatisch lid van de **groep Standaardconnector.**

## <a name="create-connector-groups"></a>Verbindingsgroepen maken

Gebruik deze stappen om zoveel verbindingsgroepen te maken als u wilt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer de proxy van **Azure Active Directory** > **Enterprise-toepassingen** > **.**
1. Selecteer **Nieuwe verbindingsgroep**. Het mes van de nieuwe connectorgroep wordt weergegeven.

   ![Toont het scherm om een nieuwe verbindingsgroep te selecteren](./media/application-proxy-connector-groups/new-group.png)

1. Geef uw nieuwe connectorgroep een naam en gebruik vervolgens het vervolgkeuzemenu om te selecteren welke connectoren in deze groep thuishoren.
1. Selecteer **Opslaan**.

## <a name="assign-applications-to-your-connector-groups"></a>Toepassingen toewijzen aan uw verbindingsgroepen

Gebruik deze stappen voor elke toepassing die u hebt gepubliceerd met Application Proxy. U een toepassing toewijzen aan een verbindingsgroep wanneer u deze voor het eerst publiceert, of u deze stappen gebruiken om de toewijzing te wijzigen wanneer u maar wilt.

1. Selecteer in het beheerdashboard voor uw directory **Enterprise-toepassingen** > **Alle toepassingen** > de toepassing die u wilt toewijzen aan een connectorgroep > Application **Proxy.**
1. Gebruik het vervolgkeuzemenu **Connectorgroep** om de groep te selecteren die de toepassing moet gebruiken.
1. Selecteer **Opslaan** om de wijziging toe te passen.

## <a name="use-cases-for-connector-groups"></a>Gebruiksaanvragen voor verbindingsgroepen

Connectorgroepen zijn handig voor verschillende scenario's, waaronder:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Sites met meerdere onderling verbonden datacenters

Veel organisaties hebben een aantal onderling verbonden datacenters. In dit geval wilt u zoveel mogelijk verkeer binnen het datacenter houden omdat cross-datacenterkoppelingen duur en traag zijn. U connectors in elk datacenter implementeren om alleen de toepassingen te bedienen die zich in het datacenter bevinden. Deze aanpak minimaliseert cross-datacenter koppelingen en biedt uw gebruikers een volledig transparante ervaring.

### <a name="applications-installed-on-isolated-networks"></a>Toepassingen die op geïsoleerde netwerken zijn geïnstalleerd

Toepassingen kunnen worden gehost in netwerken die geen deel uitmaken van het belangrijkste bedrijfsnetwerk. U verbindingsgroepen gebruiken om speciale connectors op geïsoleerde netwerken te installeren om ook toepassingen aan het netwerk te isoleren. Dit gebeurt meestal wanneer een externe leverancier een specifieke toepassing voor uw organisatie onderhoudt.

Met connectorgroepen u speciale connectors installeren voor netwerken die alleen specifieke toepassingen publiceren, waardoor het eenvoudiger en veiliger wordt om toepassingsbeheer uit te besteden aan externe leveranciers.

### <a name="applications-installed-on-iaas"></a>Toepassingen geïnstalleerd op IaaS

Voor toepassingen die op IaaS zijn geïnstalleerd voor cloudtoegang, bieden connectorgroepen een gemeenschappelijke service om de toegang tot alle apps te beveiligen. Connectorgroepen maken geen extra afhankelijkheid van uw bedrijfsnetwerk of fragmenteren de app-ervaring. Connectors kunnen op elk clouddatacenter worden geïnstalleerd en alleen toepassingen weergeven die zich in dat netwerk bevinden. U verschillende connectors installeren om een hoge beschikbaarheid te bereiken.

Neem als voorbeeld een organisatie die verschillende virtuele machines heeft aangesloten op hun eigen IaaS gehostvirtueel netwerk. Om werknemers in staat te stellen deze toepassingen te gebruiken, zijn deze privénetwerken verbonden met het bedrijfsnetwerk via site-to-site VPN. Dit biedt een goede ervaring voor medewerkers die zich on-premises bevinden. Maar het is misschien niet ideaal voor externe werknemers, omdat het extra on-premises infrastructuur vereist om toegang tot de route te leiden, zoals u zien in het onderstaande diagram:

![Diagram dat het Azure AD IaaS-netwerk illustreert](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Met Azure AD Application Proxy-connectorgroepen u een algemene service inschakelen om de toegang tot alle toepassingen te beveiligen zonder extra afhankelijkheid van uw bedrijfsnetwerk te creëren:

![Azure AD IaaS Meerdere Cloud-leveranciers](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Multi-forest – verschillende verbindingsgroepen voor elk forest

De meeste klanten die Application Proxy hebben geïmplementeerd, gebruiken de mogelijkheden voor single-sign-on (SSO) door Kerberos Constrained Delegation (KCD) uit te voeren. Om dit te bereiken, moeten de machines van de connector worden samengevoegd met een domein dat de gebruikers kan delegeren aan de toepassing. KCD ondersteunt cross-forest mogelijkheden. Maar voor bedrijven die verschillende multi-forest omgevingen hebben zonder vertrouwen tussen hen, kan een enkele connector niet worden gebruikt voor alle bossen. 

In dit geval kunnen specifieke connectors per forest worden geïmplementeerd en worden ingesteld op toepassingen die zijn gepubliceerd om alleen de gebruikers van dat specifieke forest te bedienen. Elke verbindingsgroep vertegenwoordigt een ander forest. Hoewel de tenant en het grootste deel van de ervaring is verenigd voor alle forests, kunnen gebruikers worden toegewezen aan hun foresttoepassingen met Azure AD-groepen.

### <a name="disaster-recovery-sites"></a>Disaster Recovery-sites

Er zijn twee verschillende benaderingen die u volgen met een DR-site (Disaster Recovery), afhankelijk van hoe uw sites worden geïmplementeerd:

* Als uw DR-site is gebouwd in de actieve modus waarin deze precies hetzelfde is als de hoofdsite en dezelfde netwerk- en AD-instellingen heeft, u de connectors op de DR-site maken in dezelfde connectorgroep als de hoofdsite. Hierdoor kan Azure AD failovers voor u detecteren.
* Als uw DR-site gescheiden is van de hoofdsite, u een andere verbindingsgroep maken op de DR-site en een back-uptoepassing hebben of 2) de bestaande toepassing handmatig doorleiden naar de DR-connectorgroep als dat nodig is.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Meerdere bedrijven bedienen vanuit één tenant

Er zijn veel verschillende manieren om een model te implementeren waarin één serviceprovider Azure AD-gerelateerde services implementeert en onderhoudt voor meerdere bedrijven. Connectorgroepen helpen de beheerder de connectors en toepassingen te scheiden in verschillende groepen. Een manier, die geschikt is voor kleine bedrijven, is om een enkele Azure AD-tenant te hebben, terwijl de verschillende bedrijven hun eigen domeinnaam en netwerken hebben. Dit geldt ook voor M&A-scenario's en situaties waarin één IT-afdeling meerdere bedrijven bedient om regelgevende of zakelijke redenen.

## <a name="sample-configurations"></a>Voorbeeldconfiguraties

Enkele voorbeelden die u implementeren, zijn de volgende verbindingsgroepen.

### <a name="default-configuration--no-use-for-connector-groups"></a>Standaardconfiguratie – geen gebruik voor connectorgroepen

Als u geen verbindingsgroepen gebruikt, ziet uw configuratie er als volgt uit:

![Voorbeeld Azure AD No-connectorgroepen](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Deze configuratie is voldoende voor kleine implementaties en tests. Het zal ook goed werken als uw organisatie een platte netwerktopologie heeft.

### <a name="default-configuration-and-an-isolated-network"></a>Standaardconfiguratie en een geïsoleerd netwerk

Deze configuratie is een evolutie van de standaard, waarin er een specifieke app die wordt uitgevoerd in een geïsoleerd netwerk, zoals IaaS virtueel netwerk:

![Voorbeeld Azure AD No-connectorgroepen en een geïsoleerd netwerk](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Aanbevolen configuratie : verschillende specifieke groepen en een standaardgroep voor inactieven

De aanbevolen configuratie voor grote en complexe organisaties is om de standaardconnectorgroep te hebben als een groep die geen toepassingen bedient en wordt gebruikt voor inactieve of nieuw geïnstalleerde connectors. Alle toepassingen worden bediend met behulp van aangepaste connectorgroepen. Dit maakt alle complexiteit van de hierboven beschreven scenario's mogelijk.

In het onderstaande voorbeeld heeft het bedrijf twee datacenters, A en B, met twee connectors die elke site bedienen. Elke site heeft verschillende toepassingen die erop worden uitgevoerd.

![Voorbeeld van bedrijf met 2 datacenters en 2 connectoren](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Volgende stappen

* [Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)
* [Eenmalige aanmelding inschakelen](what-is-single-sign-on.md)
