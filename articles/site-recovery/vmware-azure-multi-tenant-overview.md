---
title: VMware VM multi-tenant disaster recovery met Azure Site Recovery
description: Biedt een overzicht van Azure Site Recovery-ondersteuning voor VMWare-disaster recovery naar Azure in een CSP-programma (Multi-Tenant Environment).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083996"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Overzicht van ondersteuning voor multi-tenant voor VMware-noodherstel naar Azure met CSP

[Azure Site Recovery](site-recovery-overview.md) ondersteunt multi-tenantomgevingen voor tenantabonnementen. Het ondersteunt ook multi-tenancy voor tenantabonnementen die worden gemaakt en beheerd via het CSP-programma (Microsoft Cloud Solution Provider).

In dit artikel vindt u een overzicht van het implementeren en beheren van VMware met meerdere tenants naar Azure-replicatie.

## <a name="multi-tenant-environments"></a>Omgevingen met meerdere huurders

Er zijn drie belangrijke multi-tenant modellen:

* **Shared Hosting Services Provider (HSP)**: De partner is eigenaar van de fysieke infrastructuur en gebruikt gedeelde resources (vCenter, datacenters, fysieke opslag, enzovoort) om meerdere tenant VM's op dezelfde infrastructuur te hosten. De partner kan disaster-recovery management bieden als een managed service, of de tenant kan disaster recovery bezitten als een selfserviceoplossing.

* **Dedicated Hosting Services Provider**: De partner is eigenaar van de fysieke infrastructuur, maar maakt gebruik van speciale resources (meerdere vCenters, fysieke datastores, enzovoort) om de VM's van elke tenant op een aparte infrastructuur te hosten. De partner kan disaster-recovery management bieden als een managed service, of de tenant kan het bezitten als een selfservice-oplossing.

* **Managed Services Provider (MSP)**: De klant is eigenaar van de fysieke infrastructuur die de VM's host, en de partner biedt disaster-recovery enablement en beheer.

## <a name="shared-hosting-services-provider-hsp"></a>Aanbieder van shared-hostingservices (HSP)

De andere twee scenario's zijn subsets van het scenario voor gedeelde hosting en ze gebruiken dezelfde principes. De verschillen worden beschreven aan het einde van de shared-hosting begeleiding.

De basisvereiste in een multi-tenant scenario is dat huurders moeten worden geïsoleerd. Een huurder moet niet in staat zijn om te observeren wat een andere huurder heeft gehost. In een door partners beheerde omgeving is deze eis niet zo belangrijk als in een selfserviceomgeving, waar het van cruciaal belang kan zijn. In dit artikel wordt ervan uitgegaan dat tenantisolatie vereist is.

De architectuur wordt weergegeven in het volgende diagram.

![Gedeelde HSP met één vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Gedeelde hosting met één vCenter-server**

In het diagram heeft elke klant een aparte beheerserver. Deze configuratie beperkt de tenanttoegang tot tenantspecifieke VM's en maakt tenantisolatie mogelijk. VMware VM-replicatie gebruikt de configuratieserver om VM's te detecteren en agents te installeren. Dezelfde principes gelden voor multi-tenant omgevingen, met de toevoeging van het beperken van VM-detectie met behulp van vCenter-toegangscontrole.

De vereiste gegevensisolatie houdt in dat alle gevoelige infrastructuurinformatie (zoals toegangsreferenties) niet openbaar blijft gemaakt voor tenants. Daarom raden we aan dat alle onderdelen van de beheerserver onder de exclusieve controle van de partner blijven. De onderdelen van de beheerserver zijn:

* Configuratieserver
* Processerver
* Hoofddoelserver

Een aparte scaled-out processerver staat ook onder controle van de partner.

## <a name="configuration-server-accounts"></a>Configuratieserveraccounts

Elke configuratieserver in het scenario met meerdere tenant's gebruikt twee accounts:

- **vCenter-toegangsaccount**: Dit account wordt gebruikt om tenant-VM's te ontdekken. Het heeft vCenter toegangsmachtigingen toegewezen aan het. Om toegangslekken te voorkomen, raden we partners aan deze referenties zelf in te voeren in de configuratietool.

- **Chataccount:** Dit account wordt gebruikt om de Mobiliteitsserviceagent op tenant VM's te installeren, met een automatische push. Het is meestal een domeinaccount dat een tenant aan een partner kan verstrekken, of een account dat de partner rechtstreeks kan beheren. Als een tenant de gegevens niet rechtstreeks met de partner wil delen, kan deze de referenties invoeren via tijdelijke toegang tot de configuratieserver. Of, met de hulp van de partner, kunnen ze de Mobility service agent handmatig installeren.

## <a name="vcenter-account-requirements"></a>vCenter-accountvereisten

Configureer de configuratieserver met een account waaraan een speciale rol is toegewezen.

- De roltoewijzing moet worden toegepast op het vCenter-toegangsaccount voor elk vCenter-object en niet worden gepropageerd naar de onderliggende objecten. Deze configuratie zorgt voor tenantisolatie, omdat toegangsbeheer kan resulteren in onbedoelde toegang tot andere objecten.

    ![De optie Doorgeven naar onderliggende objecten](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- De alternatieve benadering is om het gebruikersaccount en de rol aan het datacenterobject toe te wijzen en deze te verspreiden naar de onderliggende objecten. Geef het account vervolgens een **no-access** rol voor elk object (zoals VM's die behoren tot andere tenants) die ontoegankelijk moeten zijn voor een bepaalde tenant. Deze configuratie is omslachtig. Het legt onbedoelde toegangsbesturingselementen bloot, omdat elk nieuw onderliggend object ook automatisch toegang krijgt die is overgenomen van de ouder. Daarom raden we u aan de eerste aanpak te gebruiken.

### <a name="create-a-vcenter-account"></a>Een vCenter-account maken

1. Maak een nieuwe rol door de vooraf gedefinieerde *rol alleen-lezen* te klonen en geef deze vervolgens een handige naam (zoals Azure_Site_Recovery, zoals in dit voorbeeld).
2. Wijs de volgende machtigingen toe aan deze rol:

   * **Datastore**: Ruimte toewijzen, Gegevensarchief bladeren, Bestandsbewerkingen op laag niveau, Bestand verwijderen, virtuele machinebestanden bijwerken
   * **Netwerk**: Netwerk toewijzen
   * **Resource:** VM toewijzen aan resourcegroep, VM migreren, migreren op VM
   * **Taken**: Taak maken, taak bijwerken
   * **VM - Configuratie**: Alles
   * **VM - Interactie** > Antwoordvraag, Apparaatverbinding, CD-media configureren, Diskettemedia configureren, Uitschakelen, Inschakelen, VMware-hulpprogramma's installeren
   * **VM - Inventaris** > maken van bestaande, Nieuw maken, Registreren, Uitschrijven
   * **VM - Provisioning** > Virtuele machine downloaden toestaan, virtuele machinebestanden uploaden toestaan
   * **VM - Momentopnamebeheer** > Momentopnamen verwijderen

       ![Het dialoogvenster Rol bewerken](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Wijs toegangsniveaus toe aan het vCenter-account (dat wordt gebruikt in de tenantconfiguratieserver) voor verschillende objecten, als volgt:

>| Object | Rol | Opmerkingen |
>| --- | --- | --- |
>| vCenter | Alleen-lezen | Alleen nodig om vCenter-toegang toe te staan voor het beheren van verschillende objecten. U deze toestemming verwijderen als het account nooit aan een tenant wordt verstrekt of wordt gebruikt voor beheerbewerkingen op het vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Host- en hostcluster | Azure_Site_Recovery | Zorgt er opnieuw voor dat de toegang op objectniveau is, zodat alleen toegankelijke hosts tenant VM's hebben v.v. voor fail-over en na failback. |
>| Datastore- en datastorecluster | Azure_Site_Recovery | Hetzelfde als vorige. |
>| Netwerk | Azure_Site_Recovery |  |
>| Beheerserver | Azure_Site_Recovery | Inclusief toegang tot alle componenten (CS, PS en MT) buiten de CS-machine. |
>| Vm's voor tenant | Azure_Site_Recovery | Hiermee zorgt u ervoor dat nieuwe tenant-VM's van een bepaalde tenant deze toegang ook krijgen, of dat ze niet kunnen worden ontdekt via de Azure-portal. |

De vCenter-accounttoegang is nu voltooid. Met deze stap voldoet aan de vereiste voor minimale machtigingen om failback-bewerkingen te voltooien. U deze toegangsmachtigingen ook gebruiken met uw bestaande beleid. Wijzig gewoon uw bestaande machtigingen die zijn ingesteld om rolmachtigingen op te nemen van stap 2, eerder gedetailleerd.

### <a name="failover-only"></a>Alleen failover
Als u alleen operaties voor noodherstel wilt beperken tot failover (dat wil zeggen zonder failback-mogelijkheden), gebruikt u de vorige procedure, met de volgende uitzonderingen:

- In plaats van de *Azure_Site_Recovery-rol* toe te wijzen aan het vCenter-toegangsaccount, wijst u alleen een *alleen-lezen rol* toe aan dat account. Met deze machtigingsset kunnen VM-replicatie en failover worden toegestaan en is failback mogelijk.
- Al het andere in het voorgaande proces blijft zoals het is. Om de isolatie van tenant te garanderen en vm-detectie te beperken, wordt elke machtiging nog steeds alleen op objectniveau toegewezen en niet doorgegeven aan onderliggende objecten.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Resources implementeren voor het tenantabonnement

1. Maak op de Azure-portal een brongroep en implementeer vervolgens een vault van Recovery Services volgens het gebruikelijke proces.
2. Download de registratiesleutel voor de kluis.
3. Registreer de CS voor de tenant met behulp van de kluisregistratiesleutel.
4. Voer de referenties in voor de twee toegangsaccounts, het account om toegang te krijgen tot de vCenter-server en het account om toegang te krijgen tot de VM.

    ![Serveraccounts voor configuratieserver beheren](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Servers registreren in de kluis

1. Registreer de vCenter-server in de Azure-portal in de kluis die u eerder hebt gemaakt bij de configuratieserver met behulp van het vCenter-account dat u hebt gemaakt.
2. Voltooi het proces 'Infrastructuur voorbereiden' voor siteherstel per het gebruikelijke proces.
3. De VM's zijn nu klaar om te worden gerepliceerd. Controleer of alleen de VM's van de tenant worden weergegeven in virtuele machines **repliceren.** > **Select virtual machines**

## <a name="dedicated-hosting-solution"></a>Speciale hostingoplossing

Zoals in het volgende diagram wordt weergegeven, is het architecturale verschil in een speciale hostingoplossing dat de infrastructuur van elke tenant alleen voor die tenant is ingesteld.

![architectuur-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedicated hostingscenario met meerdere vCenters**

## <a name="managed-service-solution"></a>Managed service-oplossing

Zoals in het volgende diagram wordt weergegeven, is het architecturale verschil in een beheerde serviceoplossing dat de infrastructuur van elke tenant ook fysiek gescheiden is van de infrastructuur van andere tenants. Dit scenario bestaat meestal wanneer de tenant eigenaar is van de infrastructuur en een oplossingsprovider wil om herstel na noodgevallen te beheren.

![architectuur-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Beheerde servicescenario met meerdere vCenters**

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie](site-recovery-role-based-linked-access-control.md) over toegangsbeheer op basis van rollen in Site recovery.
- Meer informatie over het [instellen van noodherstel van VMware VM's op Azure.](vmware-azure-tutorial.md)
- Meer informatie over [multi-tenancy met CSP voor VMWare VMM's.](vmware-azure-multi-tenant-csp-disaster-recovery.md)
