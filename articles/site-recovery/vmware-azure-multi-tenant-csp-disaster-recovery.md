---
title: VMware-noodherstel instellen voor Azure in een multi-tenancy-omgeving met behulp van Site Recovery en het CSP-programma (Cloud Solution Provider) | Microsoft Documenten
description: Beschrijft hoe u VMware-noodherstel in een multi-tenantomgeving instellen met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60460982"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>VMware-noodherstel instellen in een multi-tenancy-omgeving met het CSP-programma (Cloud Solution Provider)

Het [CSP-programma](https://partner.microsoft.com/en-US/cloud-solution-provider) bevordert beter samengaande verhalen voor Microsoft-cloudservices, waaronder Office 365, Enterprise Mobility Suite en Microsoft Azure. Met CSP zijn partners eigenaar van de end-to-end relatie met klanten en worden ze het primaire relatiecontactpunt. Partners kunnen Azure-abonnementen voor klanten implementeren en de abonnementen combineren met hun eigen aangepaste aanbiedingen met toegevoegde waarde.

Met [Azure Site Recovery](site-recovery-overview.md)u als partners noodherstel voor klanten rechtstreeks via CSP beheren. U CSP ook gebruiken om Site Recovery-omgevingen in te stellen en klanten hun eigen noodherstelbehoeften voor rampen op een selfservice-manier te laten beheren. In beide scenario's zijn partners de schakel tussen Site Recovery en hun klanten. Partners onderhouden de klantrelatie en factureren klanten voor siteherstelgebruik.

In dit artikel wordt beschreven hoe u als partner tenantabonnementen maken en beheren via CSP, voor een VMware-replicatiescenario met meerdere tenantn.

## <a name="prerequisites"></a>Vereisten

Als u VMware-replicatie wilt instellen, moet u het volgende doen:

- [Voorbereiden](tutorial-prepare-azure.md) Azure-bronnen, waaronder een Azure-abonnement, een virtueel Azure-netwerk en een opslagaccount.
- [Bereid](vmware-azure-tutorial-prepare-on-premises.md) on-premises VMware-servers en VM's voor.
- Maak voor elke tenant een aparte beheerserver die kan communiceren met de tenant-VM's en uw vCenter-servers. Alleen u als partner moet toegangsrechten hebben tot deze beheerserver. Meer informatie over [omgevingen met meerdere tenant's](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Een tenantaccount maken

1. Meld u via [Microsoft Partner Center](https://partnercenter.microsoft.com/)aan bij uw CSP-account.
2. Selecteer **klanten**in het menu **Dashboard** .
3. Klik op de pagina die wordt geopend op de knop **Klant toevoegen.**
4. Vul in de pagina **Nieuwe klant** de accountgegevens van de tenant in.

    ![De pagina Accountgegevens](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Klik vervolgens op **Volgende: Abonnementen**.
6. Schakel op de selectiepagina voor abonnementen het selectievakje **Microsoft Azure** in. U nu of op een ander moment andere abonnementen toevoegen.
7. Bevestig **op** de pagina Controleren de tenantgegevens en klik op **Verzenden**.
8. Nadat u het tenantaccount hebt gemaakt, wordt een bevestigingspagina weergegeven met de details van het standaardaccount en het wachtwoord voor dat abonnement. Sla de informatie op en wijzig het wachtwoord later indien nodig via de aanmeldingspagina van azure-portal.

U deze informatie delen met de tenant zoals het is, of u indien nodig een apart account aanmaken en delen.

## <a name="access-the-tenant-account"></a>Toegang tot het tenantaccount

U hebt toegang tot het abonnement van de tenant via het Microsoft Partner Center Dashboard.

1. Klik **op** de pagina Klanten op de naam van het tenantaccount.
2. Op de pagina **Abonnementen** van het tenantaccount u de bestaande accountabonnementen controleren en naar behoefte meer abonnementen toevoegen.
3. Als u de beheerbewerking en herstelbewerking van de tenant wilt beheren, selecteert u **Alle resources (Azure-portal)**. Hiermee krijgt u toegang tot de Azure-abonnementen van de tenant.

    ![De koppeling Alle bronnen](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. U de toegang verifiëren door rechtsboven in de Azure-portal op de Azure Active Directory-koppeling te klikken.

    ![Azure Active Directory-koppeling](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

U nu alle siteherstelbewerkingen voor de tenant uitvoeren en beheren in de Azure-portal. Volg het eerder beschreven proces om toegang te krijgen tot het tenantabonnement via CSP voor beheerde disaster recovery.

## <a name="assign-tenant-access-to-the-subscription"></a>Tenanttoegang toewijzen aan het abonnement

1. Zorg ervoor dat de infrastructuur voor noodherstel is ingesteld. Partners hebben toegang tot tenantabonnementen via de CSP-portal, ongeacht of disaster recovery wordt beheerd of selfservice. Stel de kluis in en registreer de infrastructuur voor de tenantabonnementen.
2. Geef de huurder het [account dat u hebt gemaakt.](#create-a-tenant-account)
3. U een nieuwe gebruiker als volgt aan het tenantabonnement toevoegen via de CSP-portal:

    a) Ga naar de CSP-abonnementspagina van de tenant en selecteer de optie **Gebruikers en licenties.**

      ![De CSP-abonnementspagina van de tenant](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Maak nu een nieuwe gebruiker door de relevante details in te voeren en machtigingen te selecteren, of door de lijst met gebruikers in een CSV-bestand te uploaden.
    
    c) Nadat u een nieuwe gebruiker hebt gemaakt, gaat u terug naar de Azure-portal. Selecteer **op** de pagina Abonnement het desbetreffende abonnement.

    d) Selecteer **Toegangsbeheer (IAM)** en klik op **Toewijzingen van rollen**.

    e) Klik **op Roltoewijzing toevoegen** om een gebruiker toe te voegen aan het relevante toegangsniveau. De gebruikers die via de CSP-portal zijn gemaakt, worden weergegeven op het tabblad Roltoewijzingen.

      ![Een gebruiker toevoegen](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Voor de meeste beheeractiviteiten volstaat de rol *van contribuant.* Gebruikers met dit toegangsniveau kunnen alles doen op een abonnement, behalve toegangsniveaus wijzigen (waarvoor toegang op *eigenaarniveau*vereist is).
- Site recovery heeft ook drie [vooraf gedefinieerde gebruikersrollen,](site-recovery-role-based-linked-access-control.md)die kunnen worden gebruikt om toegangsniveaus verder te beperken, indien nodig.

## <a name="multi-tenant-environments"></a>Omgevingen met meerdere huurders

Er zijn drie belangrijke multi-tenant modellen:

* **Shared Hosting Services Provider (HSP)**: De partner is eigenaar van de fysieke infrastructuur en gebruikt gedeelde resources (vCenter, datacenters, fysieke opslag, enzovoort) om meerdere tenant VM's op dezelfde infrastructuur te hosten. De partner kan disaster-recovery management bieden als een managed service, of de tenant kan disaster recovery bezitten als een selfserviceoplossing.

* **Dedicated Hosting Services Provider**: De partner is eigenaar van de fysieke infrastructuur, maar maakt gebruik van speciale resources (meerdere vCenters, fysieke datastores, enzovoort) om de VM's van elke tenant op een aparte infrastructuur te hosten. De partner kan disaster-recovery management bieden als een managed service, of de tenant kan het bezitten als een selfservice-oplossing.

* **Managed Services Provider (MSP)**: De klant is eigenaar van de fysieke infrastructuur die de VM's host, en de partner biedt disaster-recovery enablement en beheer.

Door tenantabonnementen in te stellen zoals beschreven in dit artikel, u klanten snel inschakelen in een van de relevante multi-tenant modellen. Hier vindt u meer informatie over de verschillende multi-tenantmodellen en het inschakelen van on-premises [toegangscontroles.](vmware-azure-multi-tenant-overview.md)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [op rollen gebaseerd toegangsbeheer](site-recovery-role-based-linked-access-control.md) voor azure-siteherstel-implementaties.
- Meer informatie over VMware naar [Azure-replicatiearchitectuur](vmware-azure-architecture.md).
- [Bekijk de zelfstudie](vmware-azure-tutorial.md) voor het repliceren van Vm's vmware naar Azure.
Meer informatie over [multi-tenantomgevingen](vmware-azure-multi-tenant-overview.md) voor het repliceren van Vm's vMware naar Azure.
