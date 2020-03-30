---
title: Het bereik voor Vm-detectie van VMware instellen met Azure Migreren
description: Beschrijft hoe u het detectiebereik voor VMware VM-beoordeling en -migratie instellen met Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337634"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Detectiebereik instellen voor VMware VM's

In dit artikel wordt beschreven hoe u het detectiebereik voor VMware VM's beperken die worden gedetecteerd door het [Azure Migrate-toestel.](migrate-appliance-architecture.md)

Het Azure Migrate-toestel detecteert on-premises Vm's vMware wanneer u: 

- Gebruik het hulpprogramma [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) om VMware VM's te beoordelen op migratie naar Azure.
- Gebruik het hulpprogramma [Azure Migreren:Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) voor [agentloze migratie](server-migrate-overview.md) van VMware VM's naar Azure.

## <a name="set-discovery-scope"></a>Detectiebereik instellen


Nadat u het Azure Migrate appliance voor VMware VM-beoordeling of migratie hebt ingesteld, begint het toestel vm's te ontdekken en vm-metagegevens naar Azure te verzenden. Voordat u het toestel aansluit op vCenter Server voor detectie, u het detectiebereik instellen om detectie te beperken tot vCenter Server-datacenters, clusters, een map met clusters, hosts, een map met hosts of afzonderlijke VM's.

Als u het bereik wilt instellen, wijst u machtigingen toe voor het account dat Azure Migrate gebruikt om toegang te krijgen tot de vCenterServer.

## <a name="create-a-vcenter-user-account"></a>Een vCenter-gebruikersaccount maken

Als u nog geen vCenter-gebruikersaccount hebt ingesteld dat het Azure Migrate-toestel gebruikt om VMware VM's te ontdekken, te beoordelen en te migreren, doet u dat eerst.

1.    Meld u aan bij vSphere Web Client als vCenter Server-beheerder.
2.    Selecteer Gebruikers en groepen **beheer sso** > **SSO users and Groups**en klik op het tabblad **Gebruikers.**
3.    Selecteer het pictogram **Nieuwe gebruiker.**
4.    Vul de nieuwe gebruikersgegevens in > **OK**.

De accountmachtigingen zijn afhankelijk van wat u implementeert.

**Functie** | **Accountmachtigingen**
--- | ---
[Beoordelen](tutorial-assess-vmware.md)| Het account heeft alleen-lezen toegang nodig.
[Apps/rollen/functies detecteren](how-to-discover-applications.md) | Het account heeft alleen-lezen toegang nodig, met bevoegdheden die zijn ingeschakeld voor virtuele machines > Gastbewerkingen.
[Afhankelijkheden analyseren (zonder agent)](how-to-create-group-machine-dependencies-agentless.md) | Het account heeft alleen-lezen toegang nodig, met bevoegdheden die zijn ingeschakeld voor virtuele machines > Gastbewerkingen.
[Migreren (zonder agent)](tutorial-migrate-vmware.md) | Je hebt een rol nodig die de juiste machtigingen heeft toegewezen.<br/><br/> Als u een rol wilt maken, meldt u zich aan bij vSphere-webclient als vCenter Server-beheerder. Selecteer de vCenter Server-instantie > **rol Maken**. Geef bijvoorbeeld een rolnaam <em>op Azure_Migrate</em>en wijs de [vereiste machtigingen](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) toe aan de rol.


## <a name="assign-permissions-on-vcenter-objects"></a>Machtigingen toewijzen aan vCenter-objecten

U machtigingen toewijzen aan voorraadobjecten met een van de twee methoden:

- Wijs een rol met de vereiste machtigingen toe aan het account dat u gebruikt voor alle bovenliggende objecten die VM's hosten die u wilt ontdekken/migreren.
- U de rol en het gebruikersaccount op datacenterniveau toewijzen en deze doorgeven aan de onderliggende objecten. Geef het account vervolgens een **no access-rol** voor elk object dat u niet wilt ontdekken/migreren. We raden deze aanpak niet aan omdat deze omslachtig is en toegangsbesturingselementen kunnen blootstellen, omdat elk nieuw onderliggend object automatisch toegang krijgt die is overgenomen van de ouder.

Ga als volgt te werk om een rol toe te wijzen aan het account dat u voor alle relevante objecten gebruikt:

- **Voor beoordeling:** Pas voor **VM-beoordeling de alleen-lezenrol** toe op het vCenter-gebruikersaccount voor alle bovenliggende objecten die vm's hosten die u wilt ontdekken. Bovenliggende objecten inbegrepen: host, map met hosts, cluster en map met clusters in de hiërarchie, tot aan het datacenter. Deze machtigingen doorgeven aan onderliggende objecten in de hiërarchie.

    ![Machtigingen toewijzen](./media/tutorial-assess-vmware/assign-perms.png)

- **Voor agentloze migratie:** Pas voor agentloze migratie een door de gebruiker gedefinieerde rol met [vereiste machtigingen](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) toe op het gebruikersaccount, voor alle bovenliggende objecten die VM's hosten die u wilt ontdekken. U de rol <em>Azure_Migrate</em>noemen.

### <a name="scope-support"></a>Scope-ondersteuning

Momenteel kan het hulpprogramma Serverbeoordeling vm's niet detecteren als het vCenter-account toegang heeft op vCenter VM-mapniveau. Mappen met hosts en clusters worden ondersteund.

Als u uw detectie wilt scopen op VM-mappen, voert u de volgende procedure uit om ervoor te zorgen dat het vCenter-account alleen-lezen toegang heeft toegewezen op VM-niveau.

1. Alleen-lezenmachtigingen toewijzen aan alle VM's in de VM-mappen die u wilt gebruiken voor detectie.
2. Alleen-lezen toegang verlenen tot alle bovenliggende objecten die VM's hosten.
    - Alle bovenliggende objecten (host, map met hosts, cluster, map met clusters) in de hiërarchie tot aan het datacenter zijn opgenomen.
    - U hoeft de machtigingen voor alle onderliggende objecten niet te propageren.
3. Gebruik de referenties voor detectie door het datacenter te selecteren als **verzamelingsbereik**. De op rollen gebaseerde toegangscontrole-instelling zorgt ervoor dat het bijbehorende vCenter-gebruikersaccount alleen toegang heeft tot alleen tenantspecifieke VM's.


## <a name="next-steps"></a>Volgende stappen

[Stel het apparaat in](how-to-set-up-appliance-vmware.md) en [start continue detectie.](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)
