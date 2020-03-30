---
title: Azure Migrate-toestelarchitectuur
description: Biedt een overzicht van het Azure Migrate-toestel dat wordt gebruikt bij serverbeoordeling en -migratie.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 25dc530199cde3408ce3bd6641aeb9bb8595465d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337595"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate-toestelarchitectuur

In dit artikel worden de azure migrate-toestelarchitectuur en -processen beschreven. Het Azure Migrate-toestel is een lichtgewicht toestel dat op locatie wordt geïmplementeerd om VM's en fysieke servers te ontdekken die u wilt beoordelen voor migratie naar Azure. 

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate-toestel wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Gebruikt voor** 
--- | --- | ---
**VMware VM-beoordeling** | Azure Migreren: serverbeoordeling | Ontdek VMware VM's.<br/><br/> Ontdek machine-apps en afhankelijkheden.<br/><br/> Verzamel metagegevens en prestatiemetagegevens van machines en verzend naar Azure.
**VMware VM-migratie (zonder agent)** | Azure Migreren: servermigratie | Ontdek VMware VM's<br/><br/>  VMware VM's repliceren met [agentloze migratie](server-migrate-overview.md).
**Hyper-V VM-beoordeling** | Azure Migreren: serverbeoordeling | Ontdek Hyper-V VM's.<br/><br/> Verzamel metagegevens en prestatiemetagegevens van machines en verzend naar Azure.
**Fysieke machine** |  Azure Migreren: serverbeoordeling |  Ontdek fysieke servers.<br/><br/> Verzamel metagegevens en prestatiemetagegevens van machines en verzend naar Azure.

## <a name="appliance-components"></a>Toestelonderdelen

Het apparaat heeft een aantal onderdelen.

- **Beheer-app**: Dit is een web-app voor gebruikersinvoer tijdens de implementatie van apparaten. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.
- **Detectieagent**: De agent verzamelt machineconfiguratiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure. 
- **Assessment agent**: De agent verzamelt prestatiegegevens. Wordt gebruikt bij het beoordelen van machines voor migratie naar Azure.
- **DRA-agent**: orkestreert VM-replicatie en coördineert de communicatie tussen gerepliceerde machines en Azure. Alleen gebruikt bij het repliceren van VMware VM's naar Azure met behulp van agentless migratie.
- **Gateway:** stuurt gerepliceerde gegevens naar Azure. Alleen gebruikt bij het repliceren van VMware VM's naar Azure met behulp van agentless migratie.
- **Automatische updateservice**: Werkt toestelonderdelen bij (wordt elke 24 uur uitgevoerd).



## <a name="appliance-deployment"></a>Toestelimplementatie

- Het Azure Migrate-toestel kan worden ingesteld met behulp van een sjabloon (alleen Hyper-V of VMware) of een PowerShell-script-installatieprogramma. [Meer informatie](deploy-appliance.md#deployment-options) over de opties. 
- Vereisten voor apparaatondersteuning en implementatievereisten worden samengevat in de [ondersteuningsmatrix voor apparaten.](migrate-appliance.md)


## <a name="appliance-registration"></a>Toestelregistratie

Tijdens het instellen van het toestel registreert u het toestel met Azure Migrate.Tijdens het instellen en registreren vinden de acties die in de tabel zijn samengevat.

**Actie** | **Details** | **Machtigingen**
--- | --- | ---
**Bronproviders registreren** | Deze resourcesproviders worden geregistreerd in het abonnement dat u kiest tijdens het instellen van apparaten: Microsoft.OffAzure, Microsoft.Migrate en Microsoft.KeyVault.<br/><br/> Als u een resourceprovider registreert, configureert u uw abonnement om met de resourceprovider te werken. | Als u de resourceproviders wilt registreren, hebt u een rol inzender of eigenaar bij het abonnement nodig.
**Azure AD-app-communicatie maken** | Azure Migrate maakt een Azure Active Directory-app (Azure AD) voor communicatie (verificatie en autorisatie) tussen de agents die op het toestel worden uitgevoerd en hun respectieve services die op Azure worden uitgevoerd.<br/><br/> Deze app heeft geen bevoegdheden om Azure resource manager calls of RBAC-toegang te geven op een resource. | U hebt [deze machtigingen](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) nodig voor Azure Migrate om de app te maken.
**Kluis Azure AD-apps-Key maken** | Deze app is alleen gemaakt voor agentless migratie van VMware VM's naar Azure.<br/><br/> Het wordt uitsluitend gebruikt om toegang te krijgen tot de sleutelkluis die is gemaakt in het abonnement van de gebruiker voor agentloze migratie.<br/><br/> Het heeft RBAC-toegang op de Azure-sleutelkluis (gemaakt in de tenant van de klant), wanneer detectie wordt gestart vanuit het toestel. | U hebt [deze machtigingen](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) nodig voor Azure Migrate om de app te maken.



## <a name="collected-data"></a>Verzamelde gegevens

Gegevens die door de client worden verzameld voor alle implementatiescenario's, worden volledig vastgelegd in de [ondersteuningsmatrix voor apparaten.](migrate-appliance.md)

## <a name="discovery-and-collection-process"></a>Detectie- en inzamelingsproces

![Architectuur](./media/migrate-appliance-architecture/architecture.png)

Het toestel communiceert met vCenter Servers en Hyper-V hosts/cluster met behulp van het volgende proces.

1. **Start detectie:**
    - Wanneer u de detectie start op het Hyper-V-toestel, communiceert deze met de Hyper-V-hosts op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS).
    - Wanneer u de detectie op het VMware-toestel start, communiceert deze standaard met de vCenter-server op TCP-poort 443. Als de vCenter-server op een andere poort luistert, u deze configureren in de web-app voor toestel.
2. **Metagegevens en prestatiegegevens verzamelen:**
    - Het toestel gebruikt een CIM-sessie (Common Information Model) om Hyper-V VM-gegevens te verzamelen van de Hyper-V-host op poorten 5985 en 5986.
    - Het toestel communiceert standaard met poort 443 om Vm-gegevens van VMware van de vCenter-server te verzamelen.
3. **Gegevens verzenden:** het toestel verzendt de verzamelde gegevens naar Azure Migrate Server Assessment en Azure Migrate Server Migration via SSL-poort 443. Het toestel kan via internet verbinding maken met Azure of ExpressRoute gebruiken met public/Microsoft-peering.
    - Voor prestatiegegevens verzamelt het toestel realtime gebruiksgegevens.
        - Prestatiegegevens worden elke 20 seconden verzameld voor VMware en elke 30 seconden voor Hyper-V voor elke prestatiestatistiek.
        - De verzamelde gegevens worden opgerold om gedurende 10 minuten één gegevenspunt te maken.
        - De piekgebruikswaarde wordt geselecteerd uit alle gegevenspunten van 20/30 seconden en naar Azure verzonden voor beoordeling.
        - Op basis van de percentielwaarde die is opgegeven in de beoordelingseigenschappen (50th/90th/95th/99th), worden de tien minuten punten gesorteerd in oplopende volgorde en wordt de juiste percentielwaarde gebruikt om de beoordeling te berekenen
    - Voor Servermigratie begint het toestel met het verzamelen van VM-gegevens en repliceert deze naar Azure.
4. **Beoordelen en migreren:** U nu beoordelingen maken op basis van de metagegevens die door het toestel zijn verzameld met Azure Migrate Server Assessment. Bovendien u ook beginnen met het migreren van VMware VM's met Azure Migrate Server Migration om agentless VM-replicatie te orkestreren.





## <a name="appliance-upgrades"></a>Toestelupgrades

Het toestel wordt bijgewerkt terwijl de Azure Migrate-agents die op het toestel worden uitgevoerd, worden bijgewerkt. Dit gebeurt automatisch omdat automatisch bijwerken standaard is ingeschakeld op het toestel. U deze standaardinstelling wijzigen om de agents handmatig bij te werken.

U schakelt automatische update in het register uit door HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" sleutel in te stellen op 0 (DWORD). Als u besluit om handmatige updates te gebruiken, is het belangrijk om alle agents op het toestel tegelijkertijd bij te werken met behulp van de knop **Bijwerken** voor elke verouderde agent op het toestel.
 

## <a name="next-steps"></a>Volgende stappen

[Bekijk](migrate-appliance.md) de ondersteuningsmatrix van het apparaat.

