---
title: Het bereik voor VMware-VM-detectie instellen met Azure Migrate
description: Hierin wordt beschreven hoe u het detectie bereik voor VMware VM-evaluatie en-migratie instelt met Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337634"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Detectie bereik instellen voor VMware-Vm's

In dit artikel wordt beschreven hoe u het bereik van detectie kunt beperken voor VMware-Vm's die worden gedetecteerd door het [Azure migrate apparaat](migrate-appliance-architecture.md).

Het Azure Migrate-apparaat detecteert on-premises VMware-Vm's wanneer u: 

- Gebruik het hulp programma [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) om virtuele VMware-machines te evalueren voor migratie naar Azure.
- Gebruik de [Azure migrate:](migrate-services-overview.md#azure-migrate-server-migration-tool) hulp programma voor server migratie voor de [migratie](server-migrate-overview.md) van virtuele VMware-machines naar Azure.

## <a name="set-discovery-scope"></a>Detectie bereik instellen


Nadat u het Azure Migrate apparaat hebt ingesteld voor de evaluatie of migratie van VMware-Vm's, start het apparaat het detecteren van Vm's en het verzenden van VM-meta gegevens naar Azure. Voordat u het apparaat verbindt met vCenter Server voor detectie, kunt u het detectie bereik instellen om de detectie te beperken tot vCenter Server Data Centers, clusters, een map met clusters, hosts, een map van hosts of afzonderlijke Vm's.

Als u het bereik wilt instellen, wijst u machtigingen toe voor het account dat door Azure Migrate wordt gebruikt voor toegang tot de vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Een vCenter-gebruikers account maken

Als u nog geen vCenter-gebruikers account hebt ingesteld dat het Azure Migrate apparaat gebruikt om virtuele VMware-machines te detecteren, te evalueren en te migreren, doet u dat eerst.

1.    Meld u aan bij de vSphere-webclient als de vCenter Server beheerder.
2.    Selecteer **beheer** > -**SSO-gebruikers en-groepen**en klik op het tabblad **gebruikers** .
3.    Selecteer het pictogram **nieuwe gebruiker** .
4.    Vul de gegevens van de nieuwe gebruiker in > **OK**.

De account machtigingen zijn afhankelijk van wat u implementeert.

**Functie** | **Account machtigingen**
--- | ---
[Vast](tutorial-assess-vmware.md)| Het account heeft alleen-lezen toegang nodig.
[Apps/rollen/functies detecteren](how-to-discover-applications.md) | Het account heeft alleen-lezen toegang nodig, met bevoegdheden ingeschakeld voor virtuele machines >-gast bewerkingen.
[Afhankelijkheden analyseren (zonder agent)](how-to-create-group-machine-dependencies-agentless.md) | Het account heeft alleen-lezen toegang nodig, met bevoegdheden ingeschakeld voor virtuele machines >-gast bewerkingen.
[Migreren (zonder agent)](tutorial-migrate-vmware.md) | U hebt een rol nodig waaraan de juiste machtigingen zijn toegewezen.<br/><br/> Als u een rol wilt maken, meldt u zich aan bij vSphere-webclient als vCenter Server beheerder. Selecteer de vCenter Server instantie > **rol maken**. Geef een rolnaam op, bijvoorbeeld <em>Azure_Migrate</em>, en wijs de [vereiste machtigingen](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) toe aan de rol.


## <a name="assign-permissions-on-vcenter-objects"></a>Machtigingen toewijzen voor vCenter-objecten

U kunt op een van de volgende twee manieren machtigingen toewijzen aan inventaris objecten:

- Wijs een rol met de vereiste machtigingen toe aan het account dat u gebruikt, voor alle bovenliggende objecten die virtuele machines hosten die u wilt detecteren/migreren.
- U kunt ook de rol en het gebruikers account toewijzen op het niveau van het Data Center en deze door geven aan de onderliggende objecten. Geef vervolgens voor elk object dat u niet wilt detecteren/migreren het account een rol voor **toegang** . We raden u aan deze benadering niet aan te bevelen, omdat het lastig is en mogelijk toegangs beheer beschikbaar stellen, omdat aan elk nieuw onderliggend object automatisch toegang wordt verleend die wordt overgenomen van de bovenliggende map.

Ga als volgt te werk om een rol toe te wijzen aan het account dat u gebruikt voor alle relevante objecten:

- **Voor evaluatie**: voor de evaluatie van de VM, past u de rol **alleen-lezen** toe op het vCenter-gebruikers account voor alle bovenliggende objecten die als host dienen voor vm's die u wilt detecteren. Bovenliggende objecten zijn opgenomen: host, map van hosts, cluster en map van clusters in de hiërarchie, tot het Data Center. Deze machtigingen door geven aan onderliggende objecten in de hiërarchie.

    ![Machtigingen toewijzen](./media/tutorial-assess-vmware/assign-perms.png)

- **Voor migratie zonder agent**: pas voor migratie zonder agent een door de gebruiker gedefinieerde rol toe met de [vereiste machtigingen](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) voor het gebruikers account voor alle bovenliggende objecten die als host dienen voor vm's die u wilt detecteren. U kunt de rol een naam <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Bereik ondersteuning

Het hulp programma Server assessment kan op dit moment geen Vm's detecteren als het vCenter-account toegang heeft verleend op het niveau van de vCenter-VM-map. Mappen van hosts en clusters worden ondersteund.

Als u uw detectie op basis van VM-mappen wilt beperken, voert u de volgende procedure uit om ervoor te zorgen dat het vCenter-account alleen-lezen toegang heeft die is toegewezen op een VM-niveau.

1. Wijs alleen-lezen-machtigingen toe op alle virtuele machines in de VM-mappen die u wilt bereiken voor detectie.
2. Ken alleen-lezen toegang toe aan alle bovenliggende objecten die virtuele machines hosten.
    - Alle bovenliggende objecten (host, map van hosts, cluster, map met clusters) in de hiërarchie tot aan het Data Center zijn opgenomen.
    - U hoeft de machtigingen niet door te geven aan alle onderliggende objecten.
3. Gebruik de referenties voor detectie door het Data Center als **verzamelings bereik**te selecteren. Het installatie programma voor toegangs beheer op basis van rollen zorgt ervoor dat het bijbehorende vCenter-gebruikers account toegang heeft tot alleen Tenant-specifieke Vm's.


## <a name="next-steps"></a>Volgende stappen

[Stel het apparaat](how-to-set-up-appliance-vmware.md) in en [Start doorlopende detectie](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
