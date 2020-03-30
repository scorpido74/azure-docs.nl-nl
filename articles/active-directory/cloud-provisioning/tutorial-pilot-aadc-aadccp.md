---
title: Zelfstudie - Azure AD Connect-cloudinrichting voor een bestaand gesynchroniseerd AD-forest
description: Tutorial.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba42e6bd9b11e47d793219c0ff06b9177d609f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298816"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Een proef uitvoeren met cloudinrichting voor een bestaande gesynchroniseerde AD-forest 

In deze zelfstudie u cloudprovisioning uitvoeren voor een test Active Directory-forest dat al is gesynchroniseerd met Azure Active Directory (Azure AD) Connect-synchronisatie.

![Maken](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Overwegingen
Voordat u deze zelfstudie probeert, moet u rekening houden met de volgende items:
1. Zorg ervoor dat u bekend bent met de basisprincipes van cloudprovisioning. 
2. Controleer of u Azure AD Connect-synchronisatieversie 1.4.32.0 of hoger uitvoert en de synchronisatieregels als gedocumenteerd hebt geconfigureerd. Wanneer u wordt getest, verwijdert u een test-ou of -groep uit azure AD Connect-synchronisatiebereik. Het verplaatsen van objecten buiten het bereik leidt tot verwijdering van die objecten in Azure AD. In het geval van gebruikersobjecten worden de objecten in Azure AD zacht verwijderd en kunnen ze worden hersteld. In het geval van groepsobjecten worden de objecten in Azure AD hard verwijderd en kunnen ze niet worden hersteld. Er is een nieuw koppelingstype geïntroduceerd in Azure AD Connect-synchronisatie, waardoor verwijdering in het geval van een scenario voor het uitvoeren van een loods scenario wordt voorkomen. 
3. Zorg ervoor dat de objecten in het pilotbereik ms-ds-consistencyGUID hebben ingevuld, zodat cloudprovisioning hard overeenkomt met de objecten. 

   > [!NOTE]
   > Azure AD Connect-synchronisatie vult *standaard geen MS-ds-consistencyGUID* in voor groepsobjecten.

4. Dit is een geavanceerd scenario. Zorg ervoor dat u de stappen die in deze zelfstudie zijn gedocumenteerd, nauwkeurig volgt.

## <a name="prerequisites"></a>Vereisten
Dit zijn de vereisten voor het voltooien van deze zelfstudie
- Een testomgeving met Azure AD Connect-synchronisatieversie 1.4.32.0 of hoger
- Een organisatie-eenheid of groep die zich in het bereik van de synchronisatie bevindt en de piloot kan gebruiken. We raden u aan te beginnen met een kleine set objecten.
- Een server met Windows Server 2012 R2 of hoger die de inrichtingsagent host.  Dit kan niet dezelfde server zijn als de Azure AD Connect-server.
- Bronanker voor AAD Connect-synchronisatie moet *objectGuid* of *ms-ds-consistencyGUID* zijn

## <a name="update-azure-ad-connect"></a>Azure AD Connect bijwerken

U moet minimaal [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0 hebben. Als u Azure AD Connect-synchronisatie wilt bijwerken, voert u de stappen uit in [Azure AD Connect: Upgrade naar de nieuwste versie](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>De planner stoppen
Azure AD Connect-synchronisatie synchroniseert wijzigingen die plaatsvinden in uw on-premises directory met behulp van een planner. Als u aangepaste regels wilt wijzigen en toevoegen, wilt u de planner uitschakelen, zodat synchronisaties niet worden uitgevoerd terwijl u hieraan werkt.  Voer de volgende stappen uit:

1.  Op de server waarop Azure AD Connect wordt uitgevoerd, wordt powershell gesynchroniseerd met beheerdersbevoegdheden.
2.  Voer `Stop-ADSyncSyncCycle` uit.  Druk op Enter.
3.  Voer `Set-ADSyncScheduler -SyncCycleEnabled $false` uit.

>[!NOTE] 
>Als u uw eigen aangepaste planner voor AAD Connect-synchronisatie uitvoert, schakelt u de planner uit. 

## <a name="create-custom-user-inbound-rule"></a>Aangepaste inkomende gebruikersregel maken

 1. Start de synchronisatie-editor vanuit het toepassingsmenu op het bureaublad zoals hieronder wordt weergegeven:</br>
 ![Editormenu synchronisatieregel](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Selecteer **Binnenkomen d.m.v.** de vervolgkeuzelijst Voor Richting en klik op **Nieuwe regel toevoegen**.
 ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. Voer **op de** pagina Beschrijving het volgende in en klik op **Volgende:**

    **Naam:** Geef de regel een betekenisvolle naam<br>
    **Beschrijving:** Een zinvolle beschrijving toevoegen<br>
    **Aangesloten systeem:** Kies de AD-connector waarvoor u de aangepaste synchronisatieregel schrijft<br>
    **Verbonden systeemobjecttype:** Gebruiker<br>
    **Metaverse objecttype:** Persoon<br>
    **Koppelingstype:** Join<br>
    **Voorrang:** Geef een waarde die uniek is in het systeem<br>
    **Tag:** Laat dit leeg<br>
    ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. Voer op de **filterpagina Scoping** de ou of beveiligingsgroep in waarvan u wilt dat de pilot is gebaseerd.  Als u wilt filteren op de organisatie van de organisatie, voegt u het ou-gedeelte van de gedistingeerde naam toe. Deze regel wordt toegepast op alle gebruikers die zich in die organisatie-eenheid bevinden.  Dus, als DN eindigt met "OU=CPUsers,DC=contoso, DC=com, zou je dit filter toevoegen.  Klik vervolgens op **Volgende**. 

    |Regel|Kenmerk|Operator|Waarde|
    |-----|----|----|-----|
    |Scoping OU|DN (DN)|ENDSWITH|Voorname naam van de OU.|
    |Scoping-groep||ISMEMBEROF|Voorname naam van de beveiligingsgroep.|

    ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Klik op de pagina **Joinregels** op **Volgende**.
 6. Voeg op de pagina **Transformaties** een kenmerk Constant transformation: flow True to cloudNoFlow toe. Klik op**toevoegen**.
 ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user4.png)</br>

Voor alle objecttypen (gebruiker, groep en contactpersoon) moeten dezelfde stappen worden gevolgd. Herhaal stappen per geconfigureerde AD-connector / per AD-forest. 

## <a name="create-custom-user-outbound-rule"></a>Aangepaste uitgaande regel voor gebruikers maken

 1. Selecteer **Uitgaand** in de vervolgkeuzelijst voor Richting en klik op **Regel toevoegen**.
 ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. Voer **op de** pagina Beschrijving het volgende in en klik op **Volgende:**

    **Naam:** Geef de regel een betekenisvolle naam<br>
    **Beschrijving:** Een zinvolle beschrijving toevoegen<br>
    **Aangesloten systeem:** Kies de AAD-connector waarvoor u de aangepaste synchronisatieregel schrijft<br>
    **Verbonden systeemobjecttype:** Gebruiker<br>
    **Metaverse objecttype:** Persoon<br>
    **Koppelingstype:** JoinNoFlow<br>
    **Voorrang:** Geef een waarde die uniek is in het systeem<br>
    **Tag:** Laat dit leeg<br>
    
    ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Kies op de **filterpagina Scoping** **de optie cloudNoFlow** gelijk **true**. Klik vervolgens op **Volgende**.
 ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Klik op de pagina **Joinregels** op **Volgende**.
 5. Klik op de pagina **Transformaties** op **Toevoegen**.

Voor alle objecttypen (gebruiker, groep en contactpersoon) moeten dezelfde stappen worden gevolgd.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>De Azure AD Connect-inrichtingsagent installeren
1. Meld u aan bij de server die u gebruikt met machtigingen voor bedrijfsbeheerders.  Als u de [zelfstudie voor de Basis-AD- en Azure-omgeving](tutorial-basic-ad-azure.md) gebruikt, is dit CP1.
2. Download de Azure AD Connect cloud provisioning agent met behulp van de [hier](how-to-install.md#install-the-agent)beschreven stappen.
3. De Azure AD Connect-cloudprovisioning uitvoeren (AADConnectProvisioningAgent.Installer)
3. **Accepteer** op het welkomstscherm de licentievoorwaarden en klik op **Installeren**.</br>
![Welkomstscherm](media/how-to-install/install1.png)</br>

4. Zodra deze bewerking is voltooid, wordt de wizard configuratie gestart.  Meld u aan met uw azure AD-globale beheerdersaccount.
5. Klik in het scherm **Active Directory verbinden** op Map **toevoegen** en meld u vervolgens aan met uw Active Directory-beheerdersaccount.  Met deze bewerking wordt uw on-premises directory toegevoegd.  Klik op **Volgende**.</br>
![Welkomstscherm](media/how-to-install/install3.png)</br>

6. Klik **op het scherm Configuratie voltooid** op **Bevestigen**.  Deze bewerking registreert en start de agent opnieuw.</br>
![Welkomstscherm](media/how-to-install/install4.png)</br>

7. Zodra deze bewerking is voltooid, ziet u een bericht **dat u met succes is geverifieerd.**  U op **Afsluiten**klikken.</br>
![Welkomstscherm](media/how-to-install/install5.png)</br>
8. Als u nog steeds het eerste welkomstscherm ziet, klikt u op **Sluiten**.

## <a name="verify-agent-installation"></a>Agentinstallatie verifiëren
Agentverificatie vindt plaats in de Azure-portal en op de lokale server waarop de agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Azure-portalagentverificatie
Als u wilt controleren of de agent wordt gezien door Azure, voert u de volgende stappen uit:

1. Meld u aan bij Azure Portal.
2. Selecteer aan de linkerkant **Azure Active Directory**, klik op Azure AD **Connect** en selecteer in het midden **Provisioning beheren (voorbeeld)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Klik in het scherm **Azure AD Provisioning (preview)** op **Alle agents controleren**.
![Azure AD-inrichting](media/how-to-install/install7.png)</br>
 
4. Op het **scherm On-premises provisioning agents** ziet u de agents die u hebt geïnstalleerd.  Controleer of de agent in kwestie aanwezig is en is gemarkeerd **met uitgeschakeld**.  De agent is standaard ![uitgeschakeld provisioning agents](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Op de lokale server
Volg de volgende stappen om te controleren of de agent wordt uitgevoerd:

1.  Inloggen op de server met een beheerdersaccount
2.  Open **Services** door ernaar te navigeren of door naar Start/Run/Services.msc te gaan.
3.  Controleer **onder Services** of Microsoft Azure AD Connect Agent **Updater** en **Microsoft Azure AD Connect Provisioning Agent** aanwezig zijn en dat de status **actief**is .
![Services](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect-cloudinrichting configureren
Gebruik de volgende stappen om de inrichting te configureren:

 1. Meld u aan bij de Azure AD-portal.
 2. Klik **op Azure Active Directory**
 3. Klik **op Azure AD Connect**
 4. Inrichten **beheren selecteren (voorbeeld)**
 ![](media/how-to-configure/manage1.png)</br>
 5.  Klik op **Nieuwe configuratie**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  Voer in het configuratiescherm een **e-mail met melding**in, verplaats de kiezer naar **Inschakelen** en klik op **Opslaan**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. Selecteer **onder Configureren**alle **gebruikers** selecteren om het bereik van de configuratieregel te wijzigen.
 ![](media/how-to-configure/scope2.png)</br>
 8. Wijzig aan de rechterkant het bereik om de specifieke organisatieorganisatie op te nemen die u zojuist hebt gemaakt "OU=CPUsers,DC=contoso,DC=com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Klik **op Gereed** en **opslaan**.
 10. Het toepassingsgebied moet nu worden ingesteld op één organisatie-eenheid. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Controleren of gebruikers zijn ingericht door cloudprovisioning
U controleert nu of de gebruikers die u in onze on-premises directory had, zijn gesynchroniseerd en nu bestaan in de Azure AD-tenant.  Dit synchronisatieproces kan enkele uren duren.  Voer de volgende stappen uit om te controleren of gebruikers zich inrichten via cloudprovisioning:

1. Meld u bij de [Azure Portal](https://portal.azure.com) aan met een account waaraan een Azure-abonnement is gekoppeld.
2. Selecteer links **Azure Active Directory**
3. Klik op **Azure AD Connect**
4. Klik op **Inrichting beheren (voorbeeld)**
5. Klik op de knop **Logboeken**
6. Zoek naar een gebruikersnaam om te bevestigen dat de gebruiker is ingericht door cloud provisioning

Bovendien u controleren of de gebruiker en de groep bestaan in Azure AD.

## <a name="start-the-scheduler"></a>De planner starten
Azure AD Connect-synchronisatie synchroniseert wijzigingen die plaatsvinden in uw on-premises directory met behulp van een planner. Nu u de regels hebt gewijzigd, u de planner opnieuw starten.  Voer de volgende stappen uit:

1.  Op de server waarop Azure AD Connect wordt uitgevoerd, wordt de open PowerShell gesynchroniseerd met beheerdersbevoegdheden
2.  Voer `Set-ADSyncScheduler -SyncCycleEnabled $true` uit.
3.  Voer `Start-ADSyncSyncCycle` uit.  Druk op Enter.  

>[!NOTE] 
>Als u uw eigen aangepaste planner voor AAD Connect-synchronisatie uitvoert, schakelt u de planner in. 

## <a name="something-went-wrong"></a>Er is iets fout gegaan
Als de pilot niet werkt zoals verwacht, u teruggaan naar de synchronisatie-instelling van Azure AD Connect door de onderstaande stappen te volgen:
1.  De inrichtingsconfiguratie uitschakelen in de Azure-portal. 
2.  Schakel alle aangepaste synchronisatieregels uit die zijn gemaakt voor Cloud Provisioning met het hulpprogramma Synchronisatieregeleditor. Uitschakelen moet leiden tot volledige synchronisatie op alle connectoren.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Azure AD Connect-synchronisatie configureren om de pilot-eenheid uit te sluiten
Nadat u hebt geverifieerd dat gebruikers van de pilot-ou met succes worden beheerd door cloudprovisioning, u Azure AD Connect opnieuw configureren om de pilot-ou uit te sluiten die hierboven is gemaakt.  De cloudprovisioning-agent verwerkt de synchronisatie voor deze gebruikers in de toekomst.  Gebruik de volgende stappen om Azure AD Connect te scopen.

 1. Dubbelklik op de server waarop Azure AD Connect wordt uitgevoerd, dubbelklik op het Azure AD Connect-pictogram.
 2. Klik op **Configureren**
 3. Selecteer **Synchronisatieopties aanpassen** en klik op volgende.
 4. Meld u aan bij Azure AD en klik op **Volgende**.
 5. Klik op het scherm **Uw mappen verbinden** op **Volgende**.
 6. Selecteer **op het filterscherm Domein en organisatie van de organisatie** de optie Geselecteerde domeinen en **-gegevens synchroniseren**.
 7. Vouw uw domein uit en **schakel** de **organisatie-eenheid CPUsers uit.**  Klik op **Volgende**.
![Scope](media/tutorial-existing-forest/scope1.png)</br>
 9. Klik in het scherm **Optionele functies** op **Volgende**.
 10. Klik op het scherm **Klaar om te configureren** op **Configureren**.
 11. Zodra dat is voltooid, klikt u op **Afsluiten**. 

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

