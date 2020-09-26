---
title: Veelgestelde vragen
description: Hier vindt u antwoorden op enkele veelgestelde vragen over de Azure VMware-oplossing.
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: dikamath
ms.openlocfilehash: 4bb713b3d50fa92728baf7b1fff1e6e65c48ed92
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368733"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Veelgestelde vragen over de Azure VMware-oplossing

Antwoorden op veelgestelde vragen over de Azure VMware-oplossing.

## <a name="general"></a>Algemeen

#### <a name="what-is-azure-vmware-solution"></a>Wat is Azure VMware Solution?

Als ondernemingen de strategie voor modernisatie verhogen om de flexibiliteit van het bedrijf te verbeteren, kosten te verlagen en innovatie te versnellen, zijn hybride Cloud platforms opgegroeid als belangrijkste digitale trans formatie van klanten. De oplossing Azure VMware combineert SDDC-software (Software Defined Data Center) van VMware met Microsoft Azure Global Cloud service-ecosysteem. De Azure VMware-oplossing wordt beheerd om te voldoen aan de vereisten voor prestaties, Beschik baarheid, beveiliging en naleving.

## <a name="azure-vmware-solution-service"></a>Azure VMware-oplossings service

#### <a name="where-is-azure-vmware-solution-available-today"></a>Waar is de Azure VMware-oplossing vandaag nog beschikbaar?

De service wordt doorlopend toegevoegd aan nieuwe regio's, dus Bekijk de [meest recente informatie over de service-Beschik baarheid](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) voor meer informatie. 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Kunnen werk belastingen die worden uitgevoerd in een exemplaar van de Azure VMware-oplossing worden gebruikt of worden geïntegreerd met Azure-Services?

Alle Azure-Services zijn beschikbaar voor klanten met Azure VMware-oplossingen. De beperkingen voor de prestaties en de beschik baarheid van specifieke services moeten per geval worden behandeld.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Gebruik ik dezelfde hulp middelen die ik nu gebruik voor het beheren van persoonlijke Cloud bronnen?

Ja. De Azure Portal wordt gebruikt voor implementatie en een aantal beheer bewerkingen. vCenter en NSX Manager worden gebruikt voor het beheren van vSphere-en NSX-T-resources.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Kan ik een privécloud beheren met mijn on-premises vCenter?

Bij het starten ondersteunt de Azure VMware-oplossing geen enkele beheer ervaring in on-premises en particuliere cloud omgevingen. Particuliere cloud clusters worden beheerd met vCenter en NSX Manager lokaal naar een privécloud.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Kan ik vRealize-Suite gebruiken die on-premises wordt uitgevoerd? 

Specifieke integratie-en gebruiks voorbeelden kunnen per geval worden geëvalueerd.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Kan ik vSphere-Vm's migreren van on-premises omgevingen naar persoonlijke Clouds van Azure VMware?

Ja. VM-migratie en vMotion kunnen worden gebruikt om Vm's naar een privécloud te verplaatsen als aan de standaard vereisten voor cross vCenter van [vMotion](https://kb.vmware.com/s/article/210695) wordt voldaan.

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Is een specifieke versie van vSphere vereist in on-premises omgevingen?

Omdat alle Cloud omgevingen zijn geleverd met VMware HCX, vSphere 5,5 of hoger in on-premises omgevingen voor vMotion.

#### <a name="what-does-the-change-control-process-look-like"></a>Wat ziet het wijzigings controle proces eruit?

Updates die in de service zelf zijn aangebracht, volgen het standaard proces voor wijzigings beheer van Microsoft Azure. Klanten zijn verantwoordelijk voor werkbelasting beheer taken en de bijbehorende processen voor wijzigings beheer.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Wat is het verschil tussen de Azure VMware-oplossing en CloudSimple?

Met de nieuwe Azure VMware-oplossing beschikt micro soft en VMware over een direct partnerschap voor de Cloud provider. De nieuwe oplossing is volledig ontworpen, gebouwd en ondersteund door micro soft en goedgekeurd door VMware. In architectuur zijn de oplossingen consistent, met de VMware-technologie stack die wordt uitgevoerd op een toegewezen Azure-infra structuur.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Worden Red Hat-oplossingen ondersteund op de Azure VMware-oplossing?

Micro soft en Red Hat delen een geïntegreerd ondersteunings team met co-locatie dat een uniform contact punt biedt voor Red Hat-ecosystemen die worden uitgevoerd op het Azure-platform.  Net als andere Azure-platform services die met Red Hat Enterprise Linux werken, valt de Azure VMware-oplossing onder de Cloud toegang en het geïntegreerde ondersteunings paraplu, en wordt Red Hat Enterprise Linux ondersteund voor het uitvoeren van een Azure VMware-oplossing in Azure.

#### <a name="is-vmware-hcx-enterprise-edition-available-and-if-so-how-much-does-it-cost"></a>Is VMware HCX Enter prise Edition beschikbaar, en zo ja, hoeveel kost het?

VMware HCX Enter prise Edition (EE) is beschikbaar met de Azure VMware-oplossing als een *Preview* -functie/-service. Hoewel VMware HCX EE voor de Azure VMware-oplossing in preview is, is het een gratis functie/service en onderhevig aan de voor waarden van de preview-versie van de service. Zodra de service VMware HCX EE GA wordt weer gegeven, krijgt u een melding van 30 dagen dat facturering wordt overgeschakeld. U hebt ook de mogelijkheid om de service uit te scha kelen of af te melden.

## <a name="compute-network-and-storage"></a>Compute, netwerk en opslag

#### <a name="is-there-more-than-one-type-of-host-available"></a>Is er meer dan één type host beschikbaar?

Er is slechts één type host beschikbaar.

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Wat zijn de CPU-specificaties in elk type host?

De servers hebben een Dual 18 Core 2,3 GHz Intel Cpu's.

#### <a name="how-much-memory-is-in-each-host"></a>Hoeveel geheugen bevindt zich in elke host?

De servers hebben 576 GB RAM-geheugen.

#### <a name="what-is-the-storage-capacity-of-each-host"></a>Wat is de opslag capaciteit van elke host?

Elke ESXi-host heeft twee vSAN-diskgroups met een capaciteits categorie van 15,2 TB en een NVMe-cache-laag van 3,2-TB (1,6 TB in elke diskgroup).

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Hoeveel netwerk bandbreedte is beschikbaar op elke ESXi-host?

Elke ESXi-host is een Azure VMware-oplossing is geconfigureerd met 4 25-Gbps Nic's, met twee Nic's die zijn ingericht voor het ESXi-systeem verkeer en twee Nic's die zijn ingericht voor werkbelasting verkeer. 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Worden gegevens opgeslagen op de vSAN-data stores die zijn versleuteld op rest?

Ja, alle vSAN-gegevens worden standaard versleuteld met sleutels die zijn opgeslagen in Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters en privéclouds

#### <a name="is-the-underlying-infrastructure-shared"></a>Wordt de onderliggende infra structuur gedeeld?

Nee, hosts en clusters in de privécloud zijn toegewezen en veilig verwijderd voor en na gebruik.

#### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Wat zijn het minimale en maximum aantal hosts per cluster?

Clusters kunnen worden geschaald tussen 3-en 16 ESXi-hosts. Proef clusters zijn beperkt tot drie hosts.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>Kan ik mijn persoonlijke Cloud clusters schalen?

Ja, clusters worden geschaald tussen het minimale en het maximale aantal ESXi-hosts. Proef clusters zijn beperkt tot drie hosts.

#### <a name="what-are-trial-clusters"></a>Wat zijn proef clusters?

Proef clusters zijn drie host-clusters die worden gebruikt voor één maand evaluatie van persoonlijke Clouds van Azure VMware.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Kan ik hoogwaardige hosts gebruiken voor proef clusters?

Nee. High-end ESXi-hosts zijn gereserveerd voor gebruik in productie clusters.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware-oplossing en VMware-software

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Welke versies van VMware-software worden gebruikt in persoonlijke Clouds?

Persoonlijke Clouds gebruiken vSphere 6,7, vSAN 6,7, VMware HCX en versie 2,5 van NSX-T.  

#### <a name="do-private-clouds-use-vmware-nsx"></a>Gebruiken persoonlijke Clouds VMware NSX?

Ja, NSX-T 2,5 wordt gebruikt voor de software gedefinieerde netwerken in azure VMware-oplossingen voor persoonlijke Clouds.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Kan ik VMware NSX-V in een privécloud gebruiken?

Nee. NSX-T is de enige ondersteunde versie van NSX.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Is NSX vereist in on-premises omgevingen of netwerken die verbinding maken met een privécloud?

Nee, u hoeft NSX niet on-premises te gebruiken.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Wat is de upgrade en update planning voor VMware-software in een privécloud?

De software bundel upgrades van de privécloud worden uitgevoerd om de software binnen één versie van de meest recente release van de software bundel uit VMware te laten. De software versies van de privécloud kunnen afwijken van de meest recente versies van de afzonderlijke software onderdelen (ESXi, NSX-T, vCenter, vSAN).

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Hoe vaak wordt de software stack voor de privécloud bijgewerkt?

De privécloud wordt bijgewerkt volgens een schema dat met de release van de software bundel uit VMware wordt getraceerd. Uw privécloud vereist geen uitval tijd voor upgrades.

## <a name="connectivity"></a>Connectiviteit

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Welk netwerk-IP-adres moet er worden gepland om persoonlijke Clouds te integreren met on-premises omgevingen?

Er is een privé netwerk/22 adres ruimte vereist voor het implementeren van een privécloud van Azure VMware-oplossing. Deze privé adres ruimte mag niet overlappen met andere virtuele netwerken in een abonnement of met on-premises netwerken.
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Hoe kan ik verbinding maken vanuit on-premises omgevingen met een privécloud van Azure VMware-oplossing?

U kunt op een van de volgende twee manieren verbinding maken met de service: 

- Met een virtuele machine of toepassings gateway die is geïmplementeerd op een virtueel Azure-netwerk dat is gekoppeld via ExpressRoute aan de privécloud.
- Via ExpressRoute Global Reach van uw on-premises Data Center naar een Azure ExpressRoute-circuit.

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Hoe kan ik een werk belasting-VM met internet of een Azure service-eind punt verbinden?

Schakel in het Azure Portal Internet connectiviteit in voor een privécloud. Maak met NSX-T-Manager een NSX-T T1-router en een logische switch. Vervolgens gebruikt u vCenter om een virtuele machine te implementeren op het netwerk segment dat is gedefinieerd door de logische switch. Die VM heeft netwerk toegang tot het internet en naar Azure-Services.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Moet ik de toegang van Internet tot Vm's op logische netwerken in een privécloud beperken?

Nee. Netwerk verkeer van het Internet rechtstreeks naar persoonlijke Clouds is niet toegestaan.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Moet ik Internet toegang beperken van Vm's op logische netwerken naar het Internet?

Ja. U moet NSX-T-beheer gebruiken om een firewall te maken waarmee de toegang tot internet wordt beperkt.

## <a name="accounts-and-privileges"></a>Accounts en bevoegdheden

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Welke accounts en bevoegdheden krijg ik bij mijn nieuwe Azure VMware-oplossing privécloud?

U kunt referenties opgeven voor een cloudadmin-gebruiker in vCenter en beheerders toegang op NSX-T-beheer. Er is ook een CloudAdmin-groep die kan worden gebruikt om Azure Active Directory op te nemen. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>Kan beheerders toegang hebben tot ESXi-hosts?

Nee, beheerders toegang tot ESXi is beperkt om te voldoen aan de beveiligings vereisten van de oplossing.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Welke bevoegdheden en machtigingen heeft ik in vCenter?

U hebt CloudAdmin-groeps privileges. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Welke bevoegdheden en machtigingen heeft ik op de NSX-T-Manager?

U hebt volledige beheerders bevoegdheden voor NSX-T en kan toegangs beheer op basis van rollen beheren zoals u dat ook zou doen met NSX-T Data Center on-premises. Zie [toegangs-en identiteits concepten](concepts-identity.md)voor meer informatie.

> [!NOTE]
> Er wordt een T0-router gemaakt en geconfigureerd als onderdeel van de implementatie van een privécloud. Elke wijziging van die logische router of de NSX van de virtuele machine van het rand knooppunt kan invloed hebben op de connectiviteit met uw privécloud.

## <a name="billing-and-support"></a>Facturering en ondersteuning

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Hoe worden de prijzen gestructureerd voor de Azure VMware-oplossing?

Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/azure-vmware) voor Azure VMware-oplossingen voor algemene vragen over prijzen. 

#### <a name="who-supports-azure-vmware-solution"></a>Wie ondersteunt de Azure VMware-oplossing?

Ondersteuning voor de Azure VMware-oplossing wordt geleverd door micro soft. U kunt een [ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)indienen.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Welke accounts heb ik nodig om een privécloud van Azure VMware-oplossing te maken?

U hebt een Azure-account in een Azure-abonnement nodig.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Hoe kan ik een toename voor een quotum van een host voor een Azure VMware-oplossing aanvragen?

* U hebt een [Azure Enterprise Agreement (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) met micro soft nodig.
* U hebt een Azure-account in een Azure-abonnement nodig.

Voordat u uw Azure VMware-oplossings resource maakt, moet u een ondersteunings ticket indienen om uw knoop punten toe te wijzen. Zodra het ondersteunings team uw aanvraag heeft ontvangen, duurt het Maxi maal vijf werk dagen om uw aanvraag te bevestigen en uw knoop punten toe te wijzen. Als u een bestaande privécloud van Azure VMware-oplossing hebt en u meer knoop punten wilt toewijzen, gaat u door hetzelfde proces.


1. Maak in uw Azure Portal onder **Help en ondersteuning**een **[nieuwe ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support)** en geef de volgende informatie op voor het ticket:
   - **Type probleem:** Documentatie
   - **Abonnement:** Uw abonnement selecteren
   - **Service:** Alle services > Azure VMware-oplossing
   - **Resource:** Algemene vraag 
   - **Samen vatting:** Capaciteit nodig
   - **Probleem type:** Problemen met capaciteits beheer
   - **Subtype van probleem:** Klant aanvraag voor extra quotum/capaciteit van host

1. Geef in de **Beschrijving** van het ondersteunings ticket op het tabblad **Details** het volgende op:

   - HAALBAARHEIDs-of productie 
   - Naam regio
   - Aantal knooppunten
   - Eventuele andere gegevens

   >[!NOTE]
   >De Azure VMware-oplossing raadt ten minste drie knoop punten aan om uw privécloud te maken en voor redundantie-N + 1-knoop punten. 

1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.

   Het duurt Maxi maal vijf werk dagen voor een ondersteunings medewerker om uw aanvraag te bevestigen.

   >[!IMPORTANT] 
   >Als u al een bestaande Azure VMware-oplossing hebt en u extra knoop punten aanvraagt, moeten we vijf werk dagen hebben om de knoop punten toe te wijzen. 

1. Voordat u uw knoop punten kunt inrichten, moet u ervoor zorgen dat u de resource provider **micro soft. AVS** registreert in de Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer manieren om de Azure Synapse-resourceprovider te registeren.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
