---
title: VMware-Vm's voorbereiden voor evaluatie/migratie met Azure Migrate
description: Meer informatie over het voorbereiden van de evaluatie/migratie van VMware-Vm's met Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 34bc62a9cb7e5d1358322500a8929b6f8b36d422
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454558"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware-Vm's voorbereiden voor evaluatie en migratie naar Azure

Dit artikel helpt u bij het voorbereiden van de evaluatie en/of migratie van on-premises virtuele VMware-machines naar Azure met behulp van [Azure migrate](migrate-services-overview.md).

[Azure migrate](migrate-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden.


Deze zelf studie is de eerste in een serie die laat zien hoe u virtuele VMware-machines kunt beoordelen en migreren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bereid Azure voor op het werken met Azure Migrate.
> * VMware voorbereiden voor de VM-evaluatie.
> * VMware voorbereiden voor de migratie van VM'S.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Ze zijn handig als u informatie wilt over het instellen van een implementatie en een snelle proef afdruk van een concept. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de How-Tos voor VMware-evaluatie en-migratie voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

U hebt deze machtigingen nodig.

**Taak** | **Machtigingen**
--- | ---
**Een Azure Migrate-project maken** | Uw Azure-account heeft machtigingen nodig om een project te maken.
**Het Azure Migrate apparaat registreren** | Azure Migrate maakt gebruik van een licht gewicht Azure Migrate apparaat om virtuele VMware-machines te beoordelen met de evaluatie van Azure Migrate server, en voor het uitvoeren van [agentloze migratie](server-migrate-overview.md) van virtuele VMware-machines met Azure migrate server migratie. Dit apparaat detecteert Vm's en verzendt de meta gegevens en prestatie gegevens van de virtuele machine naar Azure Migrate.<br/><br/>Tijdens de registratie worden door Azure Migrate twee Azure Active Directory-apps (Azure AD) gemaakt waarmee het apparaat uniek wordt geïdentificeerd, en moeten machtigingen voor het maken van deze apps zijn vereist.<br/> -De eerste app communiceert met Azure Migrate service-eind punten.<br/> -De tweede app heeft toegang tot een Azure Key Vault die tijdens de registratie is gemaakt voor het opslaan van Azure AD-App-gegevens en configuratie-instellingen voor het apparaat.
**Een Key Vault maken** | Als u virtuele VMware-machines wilt migreren met Azure Migrate server-migratie, Azure Migrate maakt u een Key Vault voor het beheren van toegangs sleutels voor het replicatie-opslag account in uw abonnement. Als u de kluis wilt maken, moet u machtigingen voor roltoewijzing hebben voor de resource groep waarin het Azure Migrate-project zich bevindt.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.

### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het apparaat te registreren

Als u het apparaat wilt registreren, wijst u machtigingen toe voor Azure Migrate om de Azure AD-apps te maken tijdens de registratie van het apparaat. De machtigingen kunnen worden toegewezen via een van de volgende methoden:

- Een Tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de Tenant om Azure AD-apps te maken en registreren.
- Een Tenant/globale beheerder kan de rol van toepassings ontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

> [!NOTE]
> - De apps hebben geen andere toegangs machtigingen voor het abonnement dan de hierboven beschreven.
> - U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Account machtigingen verlenen

De Tenant/globale beheerder kan machtigingen als volgt verlenen

1. In azure AD moet de Tenant/globale beheerder naar **Azure Active Directory** > **gebruikers** > **gebruikers instellingen**navigeren.
2. De beheerder moet **app-registraties** instellen op **Ja**. Dit is een standaard instelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-machtigingen](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Rol toepassings ontwikkelaar toewijzen

De Tenant/globale beheerder kan de rol van toepassings ontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-role-assignment-permissions"></a>Toewijzings machtigingen voor rollen toewijzen

Als u Azure Migrate wilt inschakelen om een Key Vault te maken, wijst u toewijzings machtigingen voor rollen als volgt toe:

1. Selecteer in de resource groep in de Azure Portal **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.

    - Voor het uitvoeren van server evaluatie zijn **Inzender** machtigingen voldoende.
    - Als u migratie zonder agent wilt uitvoeren, moet u de machtigingen **eigenaar** (of **Inzender** en **gebruikers toegang beheerder**) hebben.

3. Als u niet over de vereiste machtigingen beschikt, vraagt u deze aan bij de eigenaar van de resource groep.



## <a name="prepare-for-vmware-vm-assessment"></a>Voor bereiding voor VMware VM-evaluatie

Voor de voor bereiding op de VMware VM-evaluatie moet u het volgende doen:

- **Controleer de VMware-instellingen**. Zorg ervoor dat de vCenter Server en Vm's die u wilt migreren, voldoen aan de vereisten.
- **Stel een evaluatie account**in. Azure Migrate moet toegang hebben tot de vCenter Server om Vm's te ontdekken voor evaluatie.
- **Controleer de vereisten**van het apparaat. Controleer de implementatie vereisten voor het Azure Migrate apparaat dat wordt gebruikt voor de evaluatie.

### <a name="verify-vmware-settings"></a>VMware-instellingen verifiëren

1. [Controleren](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Vereisten voor de VMware-Server voor evaluatie.
2. [Zorg ervoor](migrate-support-matrix-vmware.md#assessment-port-requirements) dat de poorten die u nodig hebt, zijn geopend op vCenter Server.


### <a name="set-up-an-account-for-assessment"></a>Een account instellen voor evaluatie

Azure Migrate moet toegang hebben tot de vCenter Server om Vm's te ontdekken voor de evaluatie en migratie zonder agent.

- Als u van plan bent toepassingen te detecteren of afhankelijkheden te visualiseren, moet u een vCenter Server account met alleen-lezen toegang maken naast de bevoegdheden die zijn ingeschakeld voor **virtuele machines** > **gast bewerkingen**.

  ![machtigingen voor vCenter Server-account](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Als u niet van plan bent om toepassings detectie en de visualisatie van de afhankelijkheid van agents uit te voeren, stelt u een alleen-lezen-account in voor de vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>De instellingen van het toestel controleren op evaluatie

Controleer de vereisten van het apparaat voordat u het apparaat implementeert.

1. [Controleer](migrate-support-matrix-vmware.md#assessment-appliance-requirements) de vereisten en beperkingen van het apparaat.
2. Als u een firewall proxy op basis van een URL gebruikt, [controleert](migrate-support-matrix-vmware.md#assessment-url-access-requirements) u de Azure-url's die het apparaat nodig heeft voor toegang. Zorg ervoor dat de proxy alle CNAME-records die zijn ontvangen, verhelpt tijdens het opzoeken van de Url's.
3. Controleer de [prestatie gegevens](migrate-appliance.md#collected-performance-data-vmware) en [meta gegevens](migrate-appliance.md#collected-metadata-vmware) die het apparaat tijdens detectie en evaluatie verzamelt.
4. [Noteer](migrate-support-matrix-vmware.md#assessment-port-requirements) de poorten die door het apparaat worden gebruikt.
5. Zorg er op vCenter Server voor dat uw account machtigingen heeft voor het maken van een virtuele machine met behulp van een bestand met eicellen. U implementeert het Azure Migrate-apparaat als een VMware-VM met behulp van een bestand met eicellen.

Als u een firewall op basis van een URL gebruikt, verleent u toegang tot de vereiste [Azure-url's](migrate-support-matrix-vmware.md#assessment-url-access-requirements).




## <a name="prepare-for-agentless-vmware-migration"></a>Voorbereiden van VMware-migratie zonder agent

Bekijk de vereisten voor de migratie van virtuele VMware-machines in agents.

1. [Controleren](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) Vereisten voor VMware-Server.
2. Stel een account in met de [vereiste machtigingen](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions), zodat Azure migrate toegang heeft tot de vCenter Server voor migratie zonder agents met behulp van de migratie van Azure migrate server.
3. [Bekijk](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) de vereisten voor VMware-vm's die u wilt migreren naar Azure met migratie zonder agent.
4. [Bekijk](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) de vereisten voor het gebruik van het Azure migrate apparaat voor migratie zonder agent.
5. Noteer de [URL-toegang](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) en [poort toegang](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) die het Azure migrate apparaat nodig heeft voor de migratie zonder agent.


## <a name="prepare-for-agent-based-vmware-migration"></a>Voorbereiden voor VMware-migratie op basis van agents

Bekijk de vereisten voor de migratie van virtuele VMware [-machines op basis](server-migrate-overview.md) van een agent.

1. [Controleren](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Vereisten voor VMware-Server.
2. Stel een account in met de [vereiste machtigingen](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions). zodat Azure Migrate toegang krijgt tot de vCenter Server voor migratie op basis van een agent met behulp van de migratie van Azure Migrate server.
3. [Bekijk](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) de vereisten voor VMware-vm's die u wilt migreren naar Azure met migratie op basis van een agent, inclusief de installatie van de Mobility-service op elke virtuele machine die u wilt migreren.
4. Let op [URL-toegang](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Bekijk [poort toegang](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) die Azure migrate onderdelen nodig hebben voor toegang op basis van een agent.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Stel Azure-machtigingen in.
> * VMware is voor bereid voor evaluatie en migratie.


Ga door naar de tweede zelf studie voor het instellen van een Azure Migrate project en evalueer VMware-Vm's voor migratie naar Azure.

> [!div class="nextstepaction"]
> [Virtuele VMware-machines beoordelen](./tutorial-assess-vmware.md)
