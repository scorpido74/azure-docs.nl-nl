---
title: Veelgestelde vragen
description: Hier vindt u antwoorden op enkele veelgestelde vragen over de Azure VMware-oplossing (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: c318a17e433f40b17e3dd9e3e95a655ecb48a160
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873307"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Veelgestelde vragen over de preview-versie van Azure VMware Solution (AVS)

Antwoorden op veelgestelde vragen over de Azure VMware-oplossing (AVS).

## <a name="general"></a>Algemeen

**Wat is de Azure VMware-oplossing (AVS)?**

Als ondernemingen de strategie voor modernisatie verhogen om de flexibiliteit van het bedrijf te verbeteren, kosten te verlagen en innovatie te versnellen, zijn hybride Cloud platforms opgegroeid als belangrijkste digitale trans formatie van klanten. AVS combineert SDDC-software (Software Defined Data Center) van VMware met Microsoft Azure Global Cloud service-ecosysteem. De AVS-oplossing wordt beheerd om te voldoen aan de vereisten voor prestaties, Beschik baarheid, beveiliging en naleving.

## <a name="avs-service"></a>AVS-service

**Waar is de AVS vandaag beschikbaar?**

Tijdens de preview is het beschikbaar in VS Oost in Noord-Amerika en in Amsterdam in West-Europa.

**Kunnen werk belastingen die worden uitgevoerd in een exemplaar van de Azure VMware-oplossing (AVS) worden gebruikt of worden geïntegreerd met Azure-Services?**

Alle Azure-Services zijn beschikbaar voor klanten van AVS-oplossingen. De beperkingen voor de prestaties en de beschik baarheid van specifieke services moeten per geval worden behandeld.

**Gebruik ik dezelfde hulp middelen die ik nu gebruik voor het beheren van persoonlijke Cloud bronnen?**

Ja. De Azure Portal wordt gebruikt voor implementatie en een aantal beheer bewerkingen. vCenter en NSX Manager worden gebruikt voor het beheren van vSphere-en NSX-T-resources.

**Kan ik een privécloud beheren met mijn on-premises vCenter?**

Bij het starten ondersteunt AVS geen enkele beheer ervaring in on-premises en particuliere cloud omgevingen. Particuliere cloud clusters worden beheerd met vCenter en NSX Manager lokaal naar een privécloud.

**Kan ik vRealize-Suite gebruiken die on-premises wordt uitgevoerd?** 

Specifieke integratie-en gebruiks voorbeelden kunnen per geval worden geëvalueerd.

**Kan ik vSphere-Vm's migreren uit on-premises omgevingen om persoonlijke Clouds te AVS?**

Ja. VM-migratie en vMotion kunnen worden gebruikt om virtuele machines naar een privécloud te verplaatsen als aan de standaard-cross vCenter [vMotion-vereisten] [ https://kb.vmware.com/s/article/210695 ] wordt voldaan.

**Is een specifieke versie van vSphere vereist in on-premises omgevingen?**

Omdat alle Cloud omgevingen worden geleverd met HCX, vSphere 5,5 of hoger in on-premises omgevingen voor vMotion.

**Wat ziet het wijzigings controle proces eruit?**

Updates die in de service zelf zijn aangebracht, volgen het standaard proces voor wijzigings beheer van Microsoft Azure. Klanten zijn verantwoordelijk voor werkbelasting beheer taken en de bijbehorende processen voor wijzigings beheer.

**Wat is het verschil tussen de Azure VMware-oplossing en CloudSimple?**

Met de nieuwe Azure VMware-oplossing beschikt micro soft en VMware over een direct partnerschap voor de Cloud provider. De nieuwe oplossing is volledig ontworpen, gebouwd en ondersteund door micro soft en goedgekeurd door VMware. In architectuur zijn de oplossingen consistent, met de VMware-technologie stack die wordt uitgevoerd op een toegewezen Azure-infra structuur.

**Als ik een bestaande Azure VMware-oplossings klant ben, wat betekent dit voor beeld voor mij?**

Er is geen wijziging in de bestaande Azure VMware-oplossing door CloudSimple. We blijven de oplossing in azure ondersteunen. De Azure VMware-oplossing wordt ondersteund door onze service level agreement [(Sla)](https://aka.ms/CSVMwareSLA). Klanten moeten de service blijven gebruiken voor productie werkbelastingen; Dit is een beschik bare oplossing die wordt beheerd door [de service voorwaarden van micro soft](https://azure.microsoft.com/support/legal/).

**Kan ik migreren van een Azure VMware-oplossing door CloudSimple naar deze nieuwe oplossing?**

Ja, de Azure VMware-oplossing ondersteunt migratie met behulp van vertrouwde VMware-hulpprogram ma's, zoals HCX. Voor klanten die willen migreren naar de nieuwe oplossing, kunt u het beste contact met uw Microsoft-account team vinden om opties en beschik bare ondersteuning te verkennen.

<a name="how-to-request-a-quota-increase-for-existing-avs"></a>**Hoe kan ik een toename van een host-quotum voor een bestaande Azure VMware-oplossing aanvragen?**

U kunt een quotum verhoging aanvragen door [een ondersteunings aanvraag](..\azure-portal\supportability\how-to-create-azure-support-request.md)in te dienen. Het quotum beheer team evalueert de aanvraag en keurt deze binnen drie werk dagen goed.  

> [!IMPORTANT]
> Voordat u een quotum verhoging kunt aanvragen, moet u ervoor zorgen dat u [de resource provider **micro soft. AVS** registreert](tutorial-create-private-cloud.md) in de Azure Portal.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Zie [Azure-resource providers en-typen](https://review.docs.microsoft.com/en-us/azure/azure-resource-manager/management/resource-providers-and-types)voor meer manieren om de resource provider te registreren.

1. Maak in uw Azure Portal onder **Help en ondersteuning**een **nieuwe ondersteunings aanvraag** en geef de volgende informatie op voor het ticket:
   - **Type probleem:** Documentatie
   - **Abonnement:** Uw abonnements-ID
   - **Service:**  Azure VMware-oplossing 
   - **Samen vatting:** Quotum verhoging
   - **Probleem type:** Problemen met capaciteits beheer
   - **Subtype van probleem:** Klant aanvraag voor extra quotum/capaciteit van host

1. Geef op het tabblad Details van het ondersteunings ticket de volgende informatie op:
   - Aantal extra knoop punten   
   - Node-SKU
   - Regio

   > [!NOTE] 
   > Standaard worden er mini maal vier knoop punten verleend.

1. Klik op **beoordeling + maken** om de aanvraag in te dienen.

## <a name="compute-network-and-storage"></a>Compute, netwerk en opslag

**Is er meer dan één type host beschikbaar?**

Er is slechts één type host beschikbaar.

**Wat zijn de CPU-specificaties in elk type host?**

De servers hebben een Dual 18 Core 2,3 GHz Intel Cpu's.

**Hoeveel geheugen bevindt zich in elke host?**

De servers hebben 576 GB RAM-geheugen.

**Wat is de opslag capaciteit van elke host?**

Elke ESXi-host heeft twee VSAN-diskgroups met een capaciteits laag van 15,2 TB en een cache-laag van 3,2 TB NVMe (1,6 TB in elke diskgroup).

**Hoeveel netwerk bandbreedte is beschikbaar op elke ESXi-host?**

ESXi-hosts ondersteunen connectiviteits bandbreedte van Maxi maal 25 Gbps.

**Worden gegevens opgeslagen op de VSAN-data stores die zijn versleuteld op rest?**

Ja, alle VSAN-gegevens worden standaard versleuteld met sleutels die zijn opgeslagen in Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters en privé-Clouds

**Wordt de onderliggende infra structuur gedeeld?**

Nee, hosts en clusters in de privécloud zijn toegewezen en veilig verwijderd voor en na gebruik.

**Wat zijn het minimale en maximum aantal hosts per cluster?**

Clusters kunnen worden geschaald tussen 3-en 16 ESXi-hosts. Proef clusters zijn beperkt tot drie hosts.

**Kan ik mijn persoonlijke Cloud clusters schalen?**

Ja, clusters worden geschaald tussen het minimale en het maximale aantal ESXi-hosts. Proef clusters zijn beperkt tot drie hosts.

**Wat zijn proef clusters?**

Proef clusters zijn drie host-clusters die worden gebruikt voor één maand evaluaties van de AVS-persoonlijke Clouds.

**Kan ik hoogwaardige hosts gebruiken voor proef clusters?**

Nee. High-end ESXi-hosts zijn gereserveerd voor gebruik in productie clusters.

## <a name="avs-and-vmware-software"></a>AVS-en VMware-software

**Welke versies van VMware-software worden gebruikt in persoonlijke Clouds?**

Persoonlijke Clouds gebruiken vSphere 6,7, vSAN 6,7, HCX en versie 2,5 van NSX-T.  

**Gebruiken persoonlijke Clouds VMware NSX?**

Ja, NSX-T 2,5 wordt gebruikt voor het software-gedefinieerde netwerk in de automatische AVS-Clouds.

**Kan ik VMware NSX-V in een privécloud gebruiken?**

Nee. NSX-T is de enige ondersteunde versie van NSX.

**Is NSX vereist in on-premises omgevingen of netwerken die verbinding maken met een privécloud.**

Nee, u hoeft NSX niet on-premises te gebruiken.

**Wat is de upgrade en update planning voor VMware-software in een privécloud?**

De software bundel upgrades van de privécloud worden uitgevoerd om de software binnen één versie van de meest recente release van de software bundel formulier VMware te laten staan. De software versies van de privécloud kunnen afwijken van de meest recente versies van de afzonderlijke software onderdelen (ESXi, NSX-T, vCenter, VSAN).

**Hoe vaak wordt de software stack voor de privécloud bijgewerkt?**

De privécloud wordt bijgewerkt volgens een schema dat met de release van de software bundel uit VMware wordt getraceerd. Uw privécloud vereist geen uitval tijd voor upgrades.

## <a name="connectivity"></a>Connectiviteit

**Welk netwerk-IP-adres moet er worden gepland om persoonlijke Clouds te integreren met on-premises omgevingen?**

Een particulier netwerk/22 adres ruimte is vereist voor het implementeren van een automatische AVS-Cloud. Deze privé adres ruimte mag niet overlappen met andere virtuele netwerken in een abonnement of met on-premises netwerken.
 
**Hoe kan ik verbinding maken vanuit on-premises omgevingen met een nieuwe AVS-Cloud?**

U kunt op een van de volgende twee manieren verbinding maken met de service: 

- Met een virtuele machine of toepassings gateway die is geïmplementeerd op een virtueel Azure-netwerk dat is gekoppeld via ExpressRoute aan de privécloud.
- Via ExpressRoute Global Reach van uw on-premises Data Center naar een Azure ExpressRoute-circuit.

**Hoe kan ik een werk belasting-VM met internet of een Azure service-eind punt verbinden?**

Schakel in het Azure Portal Internet connectiviteit in voor een privécloud. Maak met NSX-T-Manager een NSX-T T1-router en een logische switch. Vervolgens gebruikt u vCenter om een virtuele machine te implementeren op het netwerk segment dat is gedefinieerd door de logische switch. Die VM heeft netwerk toegang tot het internet en naar Azure-Services.

**Moet ik de toegang van Internet tot Vm's op logische netwerken in een privécloud beperken?**

Nee. Netwerk verkeer van het Internet rechtstreeks naar persoonlijke Clouds is niet toegestaan.

**Moet ik Internet toegang beperken van Vm's op logische netwerken naar het Internet?**

Ja. U moet NSX-T-beheer gebruiken om een firewall te maken waarmee de toegang tot internet wordt beperkt.

## <a name="accounts-and-privileges"></a>Accounts en bevoegdheden

**Welke accounts en bevoegdheden krijgt u bij mijn nieuwe AVS-privécloud?**

U kunt referenties opgeven voor een cloudadmin-gebruiker in vCenter en beheerders toegang op NSX-T-beheer. Er is ook een CloudAdmin-groep die kan worden gebruikt om Azure Active Directory op te nemen. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

**Kan beheerders toegang hebben tot ESXi-hosts?**

Nee, beheerders toegang tot ESXi is beperkt om te voldoen aan de beveiligings vereisten van de oplossing.

**Welke bevoegdheden en machtigingen heeft ik in vCenter?**

U hebt CloudAdmin-groeps privileges. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

**Welke bevoegdheden en machtigingen heeft ik op de NSX-T-Manager?**

U hebt volledige beheerders bevoegdheden voor NSX-T en kan toegangs beheer op basis van rollen beheren zoals u dat ook zou doen met NSX-T Data Center on-premises. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

> [!NOTE]
> Er wordt een T0-router gemaakt en geconfigureerd als onderdeel van de implementatie van een privécloud. Elke wijziging van die logische router of de NSX van de virtuele machine van het rand knooppunt kan invloed hebben op de connectiviteit met uw privécloud.

## <a name="billing-and-support"></a>Facturering en ondersteuning

**Hoe wordt er gefactureerd tijdens de preview-versie van AVS**

Facturering voor AVS tijdens de preview is maandelijks op basis van betalen naar gebruik. Er zijn extra opties beschikbaar voor algemene Beschik baarheid.

**Hoe worden de prijzen gestructureerd tijdens de preview-versie van AVS?**

Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/azure-vmware) voor Azure VMware-oplossingen voor algemene vragen over prijzen. Preview-prijzen zijn beschikbaar op aanvraag. Neem contact op met uw account team of volg de koppeling op de pagina met prijzen om contact op te nemen met de verkoop.

**Wie ondersteunt AVS?**

Ondersteuning voor AVS wordt geleverd door micro soft. Houd er rekening mee dat we per onze preview-richt lijnen ondersteuning bieden tijdens een PST-werk periode van 9 tot 5 uur, maandag t/m vrijdag. U kunt via [deze koppeling](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) een ondersteunings ticket genereren

**Welke accounts heb ik nodig om een privécloud te maken?**

U hebt een Azure-account in een Azure-abonnement nodig.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
