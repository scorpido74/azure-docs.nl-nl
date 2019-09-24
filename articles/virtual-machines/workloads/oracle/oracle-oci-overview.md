---
title: Microsoft Azure integreren met Oracle-Cloud infrastructuur | Microsoft Docs
description: Meer informatie over oplossingen voor het integreren van Oracle-apps die worden uitgevoerd op Microsoft Azure met data bases in Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 9947d28cbde7f4804283e03cc07093b9240ca6bf
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241008"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Oracle-toepassings oplossingen die Microsoft Azure en Oracle-Cloud infrastructuur integreren (preview-versie)

Micro soft en Oracle hebben een partnerschap voor de communicatie tussen verschillende Clouds met een lage latentie en een hoge door Voer, zodat u optimaal gebruik kunt maken van beide Clouds. 

Met deze cross-Cloud-verbinding kunt u een toepassing met meerdere lagen partitioneren om uw databaserol uit te voeren op een Oracle Cloud Infrastructure (OCI) en de toepassing en andere lagen op Microsoft Azure. De ervaring is vergelijkbaar met het uitvoeren van de volledige oplossings stack in één Cloud. 

> [!IMPORTANT]
> Deze functionaliteit voor meerdere Clouds is momenteel beschikbaar als preview-versie en er [zijn beperkingen van toepassing](#preview-limitations). Als u verbinding met een lage latentie tussen Azure en OCI wilt inschakelen, moet uw Azure-abonnement eerst wit worden weer gegeven voor deze functie. U moet in de preview-periode inschrijven door dit korte [enquête formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)in te vullen. U ontvangt een e-mailbevestiging zodra uw abonnement is geregistreerd. U kunt de mogelijkheid niet gebruiken totdat u een bevestigings-e-mail ontvangt. U kunt ook contact opnemen met uw micro soft-vertegenwoordiger om in te scha kelen voor deze preview. De toegang tot de preview-functie is onderhevig aan Beschik baarheid en is door micro soft beperkt. Het volt ooien van de enquête biedt geen garantie voor toegang. Deze preview is beschikbaar zonder service level agreement en mag niet worden gebruikt voor werk belastingen voor de productie. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Zie de [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor Microsoft Azure-voor beelden voor meer informatie. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

Als u geïnteresseerd bent in het volledig implementeren van Oracle-oplossingen in de Azure-infra structuur, raadpleegt u [Oracle VM-installatie kopieën en de implementatie ervan op Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Overzicht van scenario's

Connectiviteit tussen de Cloud biedt een oplossing voor het uitvoeren van toonaangevende toepassingen van Oracle en uw eigen aangepaste toepassingen, op virtuele machines van Azure, terwijl u profiteert van de voor delen van gehoste database services in OCI. 

Toepassingen die u kunt uitvoeren in een configuratie met meerdere clouds zijn:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Retail toepassingen van Oracle
* Financieel beheer van Oracle Hyperion

Het volgende diagram is een overzicht op hoog niveau van de verbonden oplossing. Voor het gemak toont het diagram alleen een gegevenslaagtoepassing en een gegevenslaag. Afhankelijk van de toepassings architectuur kan uw oplossing extra lagen bevatten zoals een weblaag in Azure. Zie de volgende secties voor meer informatie.

![Overzicht van de Azure OCI-oplossing](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Preview-beperkingen

* Connectiviteit tussen de cloud in de preview-periode is beperkt tot de regio's Azure-Oost (Oost) & UK-zuid (uksouth) en de OCI Ashburn (VS Oost) & Londen (UK-zuid). Voor UK-zuid, gebruikt u beschik baarheids domein 1 (AD 1) in OCI bij het implementeren van de Inter-Connect voor lagere latenties.

## <a name="networking"></a>Netwerken

Zakelijke klanten kiezen er vaak voor om werk belastingen te spreideneren en te implementeren voor verschillende Clouds en operationele redenen. Klanten verbinden Cloud netwerken via internet, IPSec VPN of via de directe connectiviteits oplossing van de Cloud provider via uw on-premises netwerk naar spreiden. Het interconnectie van Cloud netwerken kan aanzienlijke investeringen in tijd, geld, ontwerp, aankoop, installatie, testen en bewerkingen vergen. 

Voor deze klant uitdagingen heeft Oracle en micro soft een geïntegreerde multi-Cloud ervaring ingeschakeld. Er wordt verbinding tussen Cloud netwerken gemaakt door een [ExpressRoute](../../../expressroute/expressroute-introduction.md) -circuit in Microsoft Azure aan te sluiten met een [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) -circuit in OCI. Deze connectiviteit is mogelijk wanneer een Azure ExpressRoute-peering locatie zich in de buurt van of op dezelfde peering-locatie bevindt als de OCI FastConnect. Met deze installatie kunt u een veilige, snelle connectiviteit tussen de twee Clouds, zonder dat u hiervoor een tussenliggende service provider nodig hebt.

Met ExpressRoute en FastConnect kunnen klanten een virtueel netwerk in azure koppelen aan een virtueel Cloud netwerk in OCI, mits de privé-IP-adres ruimte elkaar niet overlapt. Door de twee netwerken te koppelen, kan een bron in het virtuele netwerk communiceren met een resource in het OCI virtuele Cloud netwerk alsof ze zich in hetzelfde netwerk bevinden.

## <a name="network-security"></a>Netwerkbeveiliging

Netwerk beveiliging is een cruciaal onderdeel van een bedrijfs toepassing en is centraal voor deze oplossing met meerdere clouds. Elk verkeer dat ExpressRoute en FastConnect doorstuurt, wordt via een particulier netwerk verzonden. Met deze configuratie kunt u beveiligde communicatie tussen een virtueel Azure-netwerk en een virtuele Oracle-Cloud netwerk. U hoeft geen openbaar IP-adres op te geven voor virtuele machines in Azure. Op dezelfde manier hebt u geen Internet gateway nodig in OCI. Alle communicatie gebeurt via het privé-IP-adres van de computers.

Daarnaast kunt u [beveiligings lijsten](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) instellen voor uw OCI virtuele Cloud netwerk en beveiligings regels (gekoppeld aan Azure- [netwerk beveiligings groepen](../../../virtual-network/security-overview.md)). Gebruik deze regels voor het beheren van het verkeer tussen computers in de virtuele netwerken. Netwerk beveiligings regels kunnen worden toegevoegd op computer niveau, op subnetniveau, en op het niveau van het virtuele netwerk.
 
## <a name="identity"></a>Identiteit

De identiteit is een van de belangrijkste pijlers van de samen werking tussen micro soft en Oracle. Er is veel werk gedaan om [Oracle Identity Cloud service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) te integreren met [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD is de cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft. Het helpt uw gebruikers zich aan te melden en toegang te krijgen tot verschillende bronnen. Met Azure AD kunt u ook uw gebruikers en hun machtigingen beheren.

Op dit moment kunt u met deze integratie beheren op één centrale locatie, die Azure Active Directory is. Azure AD synchroniseert alle wijzigingen in de directory met de bijbehorende Oracle-map en wordt gebruikt voor eenmalige aanmelding bij Oracle-oplossingen in meerdere clouds.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met een [Cross-Cloud netwerk](configure-azure-oci-networking.md) tussen Azure en OCI. 

Zie de [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) Documentation (Engelstalig) voor meer informatie en witboeken over OCI.
