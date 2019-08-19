---
title: VMware-Vm's voorbereiden voor evaluatie en migratie naar Azure met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u de evaluatie en migratie van on-premises virtuele VMware-machines naar Azure voorbereidt met behulp van Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 16c0354466d5c2a1207873a1e83e209da9339705
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509994"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware-Vm's voorbereiden voor evaluatie en migratie naar Azure

In dit artikel wordt beschreven hoe u de evaluatie en migratie van on-premises virtuele VMware-machines naar Azure voorbereidt met behulp van [Azure migrate](migrate-services-overview.md).

[Azure migrate](migrate-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden. 


Deze zelf studie is de eerste in een serie die laat zien hoe u virtuele VMware-machines kunt beoordelen en migreren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Stel machtigingen in voor uw Azure-account en-resources om met Azure Migrate te werken.
> * On-premises VMware-servers en virtuele machines voorbereiden voor de VM-evaluatie.
> * On-premises VMware-servers en virtuele machines voorbereiden voor de migratie van vm's.

> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de uitleg bij VMware-evaluatie en-migratie voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

U hebt de volgende Azure-machtigingen nodig:

- Uw Azure-account moet machtigingen hebben om een Azure Migrate project te maken voor evaluatie en migratie. 
- Voor evaluatie en agentloze migratie van virtuele VMware-machines voert Azure Migrate een licht gewicht-apparaat uit dat Vm's detecteert en de meta gegevens en prestatie gegevens van de virtuele machine naar Azure Migrate verzendt. In azure hebt u machtigingen nodig om het Azure Migrate apparaat te registreren.
- Als u virtuele VMware-machines wilt migreren met Azure Migrate server migratie, maakt Azure Migrate een Key Vault in de resource groep, om toegangs sleutels te beheren voor het opslag account voor replicatie in uw abonnement. Als u de kluis wilt maken, moet u machtigingen voor roltoewijzing hebben voor de resource groep waarin het Azure Migrate-project zich bevindt. 


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.

### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het apparaat te registreren

Als u het Azure Migrate apparaat implementeert om een migratie van Vm's zonder agent te evalueren of uit te voeren, moet u dit registreren.

- Tijdens de registratie van het apparaat maakt Azure Migrate twee Azure Active Directory (Azure AD)-apps waarmee het apparaat op unieke wijze wordt geïdentificeerd
    - De eerste app communiceert met Azure Migrate service-eind punten.
    - De tweede app heeft toegang tot een Azure Key Vault die tijdens de registratie is gemaakt voor het opslaan van Azure AD-App-gegevens en configuratie-instellingen voor het apparaat.
- U kunt machtigingen voor Azure Migrate toewijzen om deze Azure AD-apps te maken met behulp van een van de volgende methoden:
    - Een Tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de Tenant om Azure AD-apps te maken en registreren.
    - Een Tenant/globale beheerder kan de rol van toepassings ontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

Het is een goed idee dat:

- De apps hebben geen andere toegangs machtigingen voor het abonnement dan de hierboven beschreven.
- U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld. 


#### <a name="grant-account-permissions"></a>Account machtigingen verlenen

De Tenant/globale beheerder kan machtigingen als volgt verlenen

1. In azure AD moet de Tenant/globale beheerder navigeren naar **Azure Active Directory** > **gebruikers** > **instellingen**.
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Dit is een standaard instelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Rol toepassings ontwikkelaar toewijzen 

De Tenant/globale beheerder kan de rol van toepassings ontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Toewijzings machtigingen voor rollen toewijzen

Wijs als volgt machtigingen voor roltoewijzing toe voor de resource groep waarin het Azure Migrate project zich bevindt:

1. Selecteer in de resource groep in de Azure Portal **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.

    - Voor het uitvoeren van server evaluatie zijn **Inzender** machtigingen voldoende.
    - Als u migratie zonder agent wilt uitvoeren, moet u de machtigingen **eigenaar** (of **Inzender** en **gebruikers toegang beheerder**) hebben.

3. Als u niet over de vereiste machtigingen beschikt, vraagt u deze aan bij de eigenaar van de resource groep. 



## <a name="prepare-for-vmware-vm-assessment"></a>Voor bereiding voor VMware VM-evaluatie

Voor de voor bereiding op de implementatie van een VMware-VM moet u de instellingen voor de VMware-host en de VM controleren en de instellingen voor het apparaat controleren.

### <a name="verify-vmware-settings"></a>VMware-instellingen verifiëren

1. [Verifiëren](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Vereisten voor de VMware-Server voor de VM-evaluatie.
2. [Zorg ervoor](migrate-support-matrix-vmware.md#assessment-port-requirements) dat de vereiste poorten zijn geopend op vCenter-servers.


### <a name="set-up-an-account-for-assessment"></a>Een account instellen voor evaluatie

Azure Migrate moet toegang hebben tot de vCenter Server om Vm's te ontdekken voor de evaluatie en migratie zonder agent. Alleen voor beoordeling hebt u een alleen-lezen-account nodig voor de vCenter Server.

Als u een firewall op basis van een URL gebruikt, verleent u toegang tot de vereiste [Azure-url's](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Zorg ervoor dat de proxy alle CNAME-records die zijn ontvangen, verhelpt tijdens het opzoeken van de Url's.


### <a name="verify-appliance-settings-for-assessment"></a>De instellingen van het toestel controleren op evaluatie

Voordat u het Azure Migrate apparaat instelt en de evaluatie begint in de volgende zelf studie, moet u de implementatie van het apparaat voorbereiden.

1. [Controleer](migrate-support-matrix-vmware.md#assessment-appliance-requirements) de vereisten voor het instellen van het Azure migrate apparaat in VMware.
2. [Bekijk](migrate-support-matrix-vmware.md#assessment-url-access-requirements) de Azure-url's die het apparaat nodig heeft om toegang te krijgen.
3. Bekijk de gegevens die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-vmware.md#assessment-port-requirements) de toegangs vereisten voor poorten voor het apparaat.
5. U implementeert het Azure Migrate-apparaat als een virtuele VMware-machine met behulp van een bestand van de eicellen. Zorg er op vCenter Server voor dat uw account machtigingen heeft voor het maken van een virtuele machine met behulp van een bestand met eicellen.


## <a name="prepare-for-agentless-vmware-migration"></a>Voorbereiden van VMware-migratie zonder agent

Bekijk de vereisten voor de migratie van virtuele VMware-machines in agents.

1. [Controleren](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) VMware-Server vereisten voor migratie zonder agent.
2. Stel een account in voor toegang tot de vCenter Server met de [vereiste machtigingen](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) voor de migratie zonder agent.
3. [Let](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) op de vereisten voor VMware-vm's die u wilt migreren naar Azure met migratie zonder agent.
4. [Controleer](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) de vereisten voor apparaten voor migratie zonder agent.
5. Opmerking toegang tot de toestel- [URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) en toegangs vereisten voor [poorten](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) voor migratie zonder agent.


## <a name="prepare-for-agent-based-vmware-migration"></a>Voorbereiden voor VMware-migratie op basis van agents

Bekijk de vereisten voor de migratie van virtuele VMware-machines op basis van een agent.

1. [Controleren](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware-Server vereisten voor migratie zonder agent. 
2. Stel een account in voor toegang tot de vCenter Server met de [vereiste machtigingen](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) voor de migratie zonder agent.
3. [Let](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) op de vereisten voor VMware-vm's die u wilt migreren naar Azure met migratie op basis van een agent, inclusief de installatie van de Mobility-service op elke virtuele machine die u wilt migreren.
4. Let op [URL-toegang](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Controleer de toegangs vereisten voor de [poort](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) voor de Mobility-service die wordt uitgevoerd op elke virtuele machine en voor de Azure migrate configuratie server.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:
 
> [!div class="checklist"] 
> * Stel Azure-machtigingen in.
> * VMware is voor bereid voor evaluatie en migratie.


Ga door naar de tweede zelf studie voor het instellen van een Azure Migrate project en evalueer VMware-Vm's voor migratie naar Azure.

> [!div class="nextstepaction"] 
> [Virtuele VMware-machines beoordelen](./tutorial-migrate-vmware.md) 

