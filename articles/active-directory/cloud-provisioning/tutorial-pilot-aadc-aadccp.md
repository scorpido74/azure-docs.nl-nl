---
title: Zelf studie-pilot Azure AD Connect Cloud inrichting voor een bestaand gesynchroniseerd AD-forest
description: vind.
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298816"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Een proef uitvoeren met cloudinrichting voor een bestaande gesynchroniseerde AD-forest 

In deze zelf studie wordt u begeleid bij het inrichten van Cloud inrichting voor een test Active Directory forest dat al is gesynchroniseerd met behulp van Azure Active Directory (Azure AD) Connect Sync.

![Maken](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Overwegingen
Voordat u deze zelf studie probeert, moet u rekening houden met de volgende items:
1. Zorg ervoor dat u bekend bent met de basis principes van het inrichten van Clouds. 
2. Zorg ervoor dat u Azure AD Connect synchronisatie versie 1.4.32.0 of hoger uitvoert en de synchronisatie regels hebt geconfigureerd zoals beschreven. Bij het testen van de test wordt een organisatie-eenheid of-groep uit Azure AD Connect synchronisatie bereik verwijderd. Het verplaatsen van objecten buiten het bereik leidt tot het verwijderen van die objecten in azure AD. In het geval van gebruikers objecten worden de objecten in azure AD zacht verwijderd en kunnen ze worden hersteld. In het geval van groeps objecten worden de objecten in azure AD permanent verwijderd en kunnen ze niet worden hersteld. Er is een nieuw koppelings type geïntroduceerd in Azure AD Connect Sync, waardoor het verwijderen in het geval van een pilot scenario wordt voor komen. 
3. Zorg ervoor dat de objecten in het test bereik MS-DS-consistencyGUID hebben gevuld, zodat de inrichting van de Cloud moeilijk overeenkomt met de objecten. 

   > [!NOTE]
   > Bij Azure AD Connect Sync worden standaard geen *MS-DS-consistencyGUID* gevuld voor groeps objecten.

4. Dit is een geavanceerd scenario. Zorg ervoor dat u de stappen in deze zelf studie nauw keurig volgt.

## <a name="prerequisites"></a>Vereisten
Dit zijn de vereisten voor het voltooien van deze zelfstudie
- Een test omgeving met Azure AD Connect Sync-versie 1.4.32.0 of hoger
- Een OE of groep die zich binnen het bereik van de synchronisatie bevindt en kan worden gebruikt voor de pilot. U kunt het beste beginnen met een klein aantal objecten.
- Een server met Windows Server 2012 R2 of hoger die als host fungeert voor de inrichtings agent.  Dit kan niet dezelfde server zijn als de Azure AD Connect-server.
- Het bron anker voor de AAD Connect-synchronisatie moet *objectGuid* of *MS-DS-consistencyGUID* zijn

## <a name="update-azure-ad-connect"></a>Azure AD Connect bijwerken

Ten minste moet u [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0 hebben. Volg de stappen in Azure AD Connect om Azure AD Connect synchronisatie bij te werken: Voer een [upgrade uit naar de nieuwste versie](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>De planner stoppen
Met Azure AD Connect Sync worden wijzigingen die in uw on-premises Directory optreden, gesynchroniseerd met een scheduler. Als u aangepaste regels wilt wijzigen en toevoegen, wilt u de scheduler uitschakelen zodat synchronisaties niet worden uitgevoerd terwijl u aan de slag gaat.  Voer de volgende stappen uit:

1.  Open Power shell op de server met Azure AD Connect sync met Administrator bevoegdheden.
2.  Voer `Stop-ADSyncSyncCycle` uit.  Druk op ENTER.
3.  Voer `Set-ADSyncScheduler -SyncCycleEnabled $false` uit.

>[!NOTE] 
>Als u uw eigen aangepaste planner uitvoert voor AAD Connect-synchronisatie, moet u de scheduler uitschakelen. 

## <a name="create-custom-user-inbound-rule"></a>Een aangepaste regel voor binnenkomende gebruikers maken

 1. Start de synchronisatie-editor vanuit het menu toepassing in het bureau blad, zoals hieronder wordt weer gegeven:</br>
 ](media/how-to-cloud-custom-user-rule/user8.png) menu ![synchronisatie regel</br>
 
 2. Selecteer **binnenkomend** in de vervolg keuzelijst voor richting en klik op **nieuwe regel toevoegen**.
 Aangepaste regel](media/how-to-cloud-custom-user-rule/user1.png) ![</br>
 
 3. Voer het volgende in op de pagina **Beschrijving** en klik op **volgende**:

    **Naam:** Geef een beschrijvende naam op voor de regel<br>
    **Beschrijving:** Een zinvolle beschrijving toevoegen<br>
    **Verbonden systeem:** Kies de AD-connector waarvoor u de aangepaste synchronisatie regel voor hebt geschreven<br>
    **Type verbonden systeem object:** Gebruiker<br>
    **Omgekeerd object type:** Gelaedeerde<br>
    **Type koppeling:** Jointypen<br>
    **Prioriteit:** Geef een waarde op die uniek is in het systeem<br>
    **Label:** Laat dit leeg<br>
    Aangepaste regel](media/how-to-cloud-custom-user-rule/user2.png) ![</br>
 
 4. Voer op de pagina **bereik filteren** de OE of beveiligings groep in die u op basis van de pilot wilt.  Als u wilt filteren op OE, voegt u het OE-gedeelte van de DN-naam toe. Deze regel wordt toegepast op alle gebruikers in die organisatie-eenheid.  Als DN eindigt op ' OE = Cpu's, DC = contoso, DC = com, voegt u dit filter toe.  Klik op **Volgende**. 

    |Regel|Kenmerk|Operator|Waarde|
    |-----|----|----|-----|
    |Organisatie-eenheid bereik|DN-NAAM|ENDSWITH|DN-naam van de organisatie-eenheid.|
    |Groep bereik||ISMEMBEROF|DN-naam van de beveiligings groep.|

    ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Klik op **volgende**op de pagina regels voor **samen voegen** .
 6. Voeg op de pagina **trans formaties** een constante trans formatie toe: flow True to cloudNoFlow-kenmerk. Klik op **Toevoegen**.
 Aangepaste regel](media/how-to-cloud-custom-user-rule/user4.png) ![</br>

Dezelfde stappen moeten worden gevolgd voor alle object typen (gebruiker, groep en contact). Herhaal de stappen per geconfigureerde AD-connector/per AD-forest. 

## <a name="create-custom-user-outbound-rule"></a>Uitgaande regel voor een aangepaste gebruiker maken

 1. Selecteer **uitgaand** in de vervolg keuzelijst voor richting en klik op **regel toevoegen**.
 Aangepaste regel](media/how-to-cloud-custom-user-rule/user5.png) ![</br>
 
 2. Voer het volgende in op de pagina **Beschrijving** en klik op **volgende**:

    **Naam:** Geef een beschrijvende naam op voor de regel<br>
    **Beschrijving:** Een zinvolle beschrijving toevoegen<br>
    **Verbonden systeem:** Kies de AAD-connector waarvoor u de aangepaste synchronisatie regel voor hebt geschreven<br>
    **Type verbonden systeem object:** Gebruiker<br>
    **Omgekeerd object type:** Gelaedeerde<br>
    **Type koppeling:** JoinNoFlow<br>
    **Prioriteit:** Geef een waarde op die uniek is in het systeem<br>
    **Label:** Laat dit leeg<br>
    
    ![Aangepaste regel](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Kies op de pagina **bereik filteren** de **waarde** **cloudNoFlow** gelijk aan. Klik op **Volgende**.
 Aangepaste regel](media/how-to-cloud-custom-user-rule/user7.png) ![</br>
 
 4. Klik op **volgende**op de pagina regels voor **samen voegen** .
 5. Klik op de pagina **trans formaties** op **toevoegen**.

Dezelfde stappen moeten worden gevolgd voor alle object typen (gebruiker, groep en contact).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>De Azure AD Connect-inrichtings agent installeren
1. Meld u aan bij de server die u wilt gebruiken met beheerders machtigingen voor de onderneming.  Als u de eenvoudige zelf studie over [AD en Azure-omgeving](tutorial-basic-ad-azure.md) gebruikt, wordt deze CP1.
2. Down load de Azure AD Connect Cloud-inrichtings agent met behulp van de stappen die [hier](how-to-install.md#install-the-agent)worden beschreven.
3. Voer de Azure AD Connect Cloud inrichting (AADConnectProvisioningAgent. installer) uit
3. **Accepteer** de licentie voorwaarden in het welkomst scherm en klik op **installeren**.</br>
![Welkomstscherm](media/how-to-install/install1.png)</br>

4. Zodra deze bewerking is voltooid, wordt de configuratie wizard gestart.  Meld u aan met uw Azure AD Global Administrator-account.
5. Klik in het scherm **verbinding maken Active Directory** op **map toevoegen** en meld u vervolgens aan met uw Active Directory beheerders account.  Met deze bewerking wordt uw on-premises Directory toegevoegd.  Klik op **Volgende**.</br>
![Welkomstscherm](media/how-to-install/install3.png)</br>

6. Klik in het scherm **configuratie voltooid** op **bevestigen**.  Met deze bewerking wordt de agent geregistreerd en opnieuw gestart.</br>
![Welkomstscherm](media/how-to-install/install4.png)</br>

7. Zodra deze bewerking is voltooid, ziet u een melding dat **uw verificatie is geslaagd.**  U kunt op **Afsluiten**klikken.</br>
![Welkomstscherm](media/how-to-install/install5.png)</br>
8. Als het eerste openings scherm nog steeds wordt weer gegeven, klikt u op **sluiten**.

## <a name="verify-agent-installation"></a>Agent installatie verifiëren
Verificatie van de agent vindt plaats in de Azure Portal en op de lokale server waarop de-agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent
Voer de volgende stappen uit om te controleren of de agent wordt gezien door Azure:

1. Meld u aan bij Azure Portal.
2. Selecteer aan de linkerkant **Azure Active Directory**, klik op **Azure AD Connect** en selecteer in het midden de optie **inrichting beheren (preview)** .</br>
![Azure-portal](media/how-to-install/install6.png)</br>

3.  Klik in het scherm **Azure AD inrichten (preview)** op **Alle agents controleren**.
![Azure AD-inrichtings](media/how-to-install/install7.png)</br>
 
4. Op het **scherm on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd.  Controleer of de agent in kwestie is en is gemarkeerd als **uitgeschakeld**.  De agent is standaard uitgeschakeld ![inrichtings agenten](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Op de lokale server
Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd:

1.  Meld u aan bij de server met een beheerders account
2.  Open **Services** door ernaar te navigeren of door naar start/uitvoeren/services. msc te gaan.
3.  Zorg ervoor dat bij **Services** **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** zijn, en de status wordt **uitgevoerd**.
![Services](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect Cloud inrichting configureren
Gebruik de volgende stappen voor het configureren van inrichting:

 1. Meld u aan bij de Azure AD-Portal.
 2. Klik op **Azure Active Directory**
 3. Klik op **Azure AD Connect**
 4. Selecteer **inrichtings beheer (preview)** 
 ![](media/how-to-configure/manage1.png)</br>
 5.  Klik op **nieuwe configuratie**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  Voer in het scherm configuratie een **e-mail melding**in, verplaats de selector naar **in en klik** op **Opslaan**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. Selecteer onder **configureren** **alle gebruikers** om het bereik van de configuratie regel te wijzigen.
 ![](media/how-to-configure/scope2.png)</br>
 8. Wijzig aan de rechter kant het bereik voor de specifieke organisatie-eenheid die u zojuist hebt gemaakt: OE = Cpu's, DC = contoso, DC = com.
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Klik op **gereed** en **Sla**het bestand op.
 10. Het bereik moet nu worden ingesteld op één organisatie-eenheid. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Controleren of gebruikers zijn ingericht met Cloud inrichting
U gaat nu controleren of de gebruikers die u in onze on-premises Directory had, zijn gesynchroniseerd en nu aanwezig zijn in de Azure AD-Tenant.  Dit synchronisatieproces kan enkele uren duren.  Voer de volgende stappen uit om te controleren of gebruikers zijn ingericht door het inrichten van de Cloud:

1. Meld u bij de [Azure Portal](https://portal.azure.com) aan met een account waaraan een Azure-abonnement is gekoppeld.
2. Selecteer links **Azure Active Directory**
3. Klik op **Azure AD Connect**
4. Klik op **inrichting beheren (preview-versie)**
5. Klik op de knop **logs**
6. Zoek naar een gebruikers naam om te bevestigen dat de gebruiker is ingericht met het inrichten van de Cloud

Daarnaast kunt u controleren of de gebruiker en groep bestaan in azure AD.

## <a name="start-the-scheduler"></a>De scheduler starten
Met Azure AD Connect Sync worden wijzigingen die in uw on-premises Directory optreden, gesynchroniseerd met een scheduler. Nu u de regels hebt gewijzigd, kunt u de Scheduler opnieuw starten.  Voer de volgende stappen uit:

1.  Open Power shell op de server met Azure AD Connect sync met beheerders bevoegdheden
2.  Voer `Set-ADSyncScheduler -SyncCycleEnabled $true` uit.
3.  Voer `Start-ADSyncSyncCycle` uit.  Druk op ENTER.  

>[!NOTE] 
>Als u uw eigen aangepaste planner uitvoert voor AAD Connect-synchronisatie, moet u de Scheduler inschakelen. 

## <a name="something-went-wrong"></a>Er is iets verkeerd gegaan
Als de pilot niet werkt zoals verwacht, kunt u terugkeren naar de Azure AD Connect synchronisatie-instellingen door de volgende stappen uit te voeren:
1.  De inrichtings configuratie uitschakelen in de Azure Portal. 
2.  Schakel alle aangepaste synchronisatie regels die zijn gemaakt voor Cloud inrichting uit met het hulp programma synchronisatie regel Editor. Uitschakelen moet volledige synchronisatie op alle connectors veroorzaken.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Azure AD Connect synchronisatie configureren om de test-OE uit te sluiten
Nadat u hebt gecontroleerd of gebruikers van de test-OE zijn beheerd door Cloud inrichting, kunt u Azure AD Connect opnieuw configureren om de bovenliggende organisatie-eenheid uit te sluiten die hierboven is gemaakt.  De Cloud Provisioning agent verwerkt de synchronisatie voor deze gebruikers.  Gebruik de volgende stappen om Azure AD Connect te bereiken.

 1. Op de server met Azure AD Connect dubbelklikt u op het pictogram Azure AD Connect.
 2. Klik op **Configureren**
 3. Selecteer **synchronisatie opties aanpassen** en klik op volgende.
 4. Meld u aan bij Azure AD en klik op **volgende**.
 5. Klik in het scherm **verbinding maken met uw mappen** op **volgende**.
 6. Selecteer op het scherm **domein en OE filteren** de optie **geselecteerde domeinen en organisatie-eenheden synchroniseren**.
 7. Vouw uw domein uit en **Selecteer** de OE van **cpu's** .  Klik op **Volgende**.
![bereik](media/tutorial-existing-forest/scope1.png)</br>
 9. Klik op het scherm **optionele functies** op **volgende**.
 10. Klik in het scherm **Gereed om te configureren** op **Configureren**.
 11. Als dat is voltooid, klikt u op **Afsluiten**. 

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)

