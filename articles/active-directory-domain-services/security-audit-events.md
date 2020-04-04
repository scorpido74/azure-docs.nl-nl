---
title: Beveiligingsaudits inschakelen voor Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over hoe u beveiligingsaudits inschakelen om de logboekregistratie van gebeurtenissen voor analyses en waarschuwingen in Azure AD Domain Services te centraliseren
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ce910b553e14d09eefa35efc5f2973337dfa1309
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654662"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Beveiligingsaudits inschakelen voor Azure Active Directory Domain Services

Met azure Active Directory Domain Services (Azure AD DS)-beveiligingsaudits kunnen Azure beveiligingsgebeurtenissen streamen naar gerichte resources. Deze bronnen omvatten Azure Storage, Azure Log Analytics-werkruimten of Azure Event Hub. Nadat u beveiligingscontrolegebeurtenissen hebt ingeschakeld, verzendt Azure AD DS alle gecontroleerde gebeurtenissen voor de geselecteerde categorie naar de beoogde bron.

U gebeurtenissen archiveren in Azure-opslag en gebeurtenissen streamen naar SIEM-software (of gelijkwaardig) voor beveiligingsgegevens en gebeurtenisbeheer (siem) met Azure Event Hubs, of uw eigen analyse uitvoeren en Azure Log Analytics-werkruimten gebruiken vanuit de Azure-portal.

> [!IMPORTANT]
> Azure AD DS-beveiligingsaudits zijn alleen beschikbaar voor exemplaren op basis van Azure Resource Manager. Zie [Azure AD DS migreren van het virtuele netwerkmodel Classic naar Resource Manager voor][migrate-azure-adds]informatie over het migreren van Azure AD DS van het virtuele netwerkmodel Classic .

## <a name="security-audit-destinations"></a>Bestemmingen voor beveiligingscontrole

U Azure Storage, Azure Event Hubs of Azure Log Analytics-werkruimten gebruiken als doelbron voor Azure AD DS-beveiligingsaudits. Deze bestemmingen kunnen worden gecombineerd. U bijvoorbeeld Azure Storage gebruiken voor het archiveren van beveiligingscontrolegebeurtenissen, maar een Azure Log Analytics-werkruimte om de informatie op korte termijn te analyseren en te rapporteren.

In de volgende tabel worden scenario's beschreven voor elk doelbrontype.

> [!IMPORTANT]
> U moet de doelbron maken voordat u Azure AD DS-beveiligingsaudits inschakelt. U deze bronnen maken met behulp van de Azure-portal, Azure PowerShell of azure cli.

| Doelbron | Scenario |
|:---|:---|
|Azure Storage| Dit doel moet worden gebruikt wanneer uw primaire behoefte is om beveiligingscontrolegebeurtenissen op te slaan voor archiveringsdoeleinden. Andere doelen kunnen worden gebruikt voor archiveringsdoeleinden, maar deze doelen bieden mogelijkheden die verder gaan dan de primaire behoefte aan archivering. <br /><br />Voordat u Azure AD DS-beveiligingscontrolegebeurtenissen inschakelt, [maakt u eerst een Azure Storage-account](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Dit doel moet worden gebruikt wanneer uw primaire behoefte is om beveiligingsauditgebeurtenissen te delen met aanvullende software, zoals gegevensanalysesoftware of beveiligingsinformatie & SIEM-software (Event Management).<br /><br />Voordat u Azure AD DS-beveiligingscontrolegebeurtenissen inschakelt, [maakt u een gebeurtenishub met Azure-portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics-werkruimte| Dit doel moet worden gebruikt wanneer het in de eerste plaats is om beveiligde audits van de Azure-portal rechtstreeks te analyseren en te controleren.<br /><br />Voordat u Azure AD DS-beveiligingscontrolegebeurtenissen inschakelt, [maakt u een werkruimte Logboekanalyse in de Azure-portal.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Beveiligingscontrolegebeurtenissen inschakelen met de Azure-portal

Voer de volgende stappen uit om azure AD DS-beveiligingscontrolegebeurtenissen in te schakelen met behulp van de Azure-portal.

> [!IMPORTANT]
> Azure AD DS-beveiligingsaudits zijn niet met terugwerkende kracht. U gebeurtenissen uit het verleden niet ophalen of opnieuw afspelen. Azure AD DS kan alleen gebeurtenissen verzenden die plaatsvinden nadat beveiligingsaudits zijn ingeschakeld.

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Zoek en selecteer Azure AD Domain Services boven aan de **Azure-portal.** Kies uw beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer **diagnostische instellingen** aan de linkerkant in het Azure AD DS-venster.
1. Er zijn standaard geen diagnostische gegevens geconfigureerd. Selecteer **Diagnostische instelling toevoegen**om aan de slag te gaan .

    ![Een diagnostische instelling voor Azure AD Domain Services toevoegen](./media/security-audit-events/add-diagnostic-settings.png)

1. Voer een naam in voor de diagnostische configuratie, zoals *aadds-auditing*.

    Schakel het selectievakje in voor de gewenste bestemming voor beveiligingscontrole. U kiezen uit een Azure Storage-account, een Azure-gebeurtenishub of een Log Analytics-werkruimte. Deze doelbronnen moeten al bestaan in uw Azure-abonnement. U de doelbronnen in deze wizard niet maken.

    ![De vereiste bestemming en het type controlegebeurtenissen instaat stellen om vast te leggen](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure-opslag**
        * Selecteer **Archiveren naar een opslagaccount**en kies **Configureren**.
        * Selecteer het **abonnement** en het **opslagaccount** dat u wilt gebruiken om beveiligingscontrolegebeurtenissen te archiveren.
        * Kies OK wanneer u klaar **bent.**
    * **Azure-gebeurtenishubs**
        * Selecteer **Streamen naar een gebeurtenishub**en kies **Configureren**.
        * Selecteer de **naamruimte van** de abonnementshub en de **gebeurtenishub**. Kies indien nodig ook de naam van de **gebeurtenishub** en vervolgens **de beleidsnaam van de gebeurtenishub.**
        * Kies OK wanneer u klaar **bent.**
    * **Azure Log Analytic workspaces**
        * Selecteer **Verzenden naar logboekanalyse**en kies vervolgens de **werkruimte Voor abonnement** en **Logboekanalyse** die u wilt gebruiken om beveiligingscontrolegebeurtenissen op te slaan.

1. Selecteer de logboekcategorieën die u wilt opnemen voor de specifieke doelbron. Als u de controlegebeurtenissen naar een Azure Storage-account verzendt, u ook een bewaarbeleid configureren dat het aantal dagen definieert om gegevens te bewaren. Een standaardinstelling van *0* behoudt alle gegevens en roteert gebeurtenissen niet na een bepaalde periode.

    U verschillende logboekcategorieën selecteren voor elke gerichte resource binnen één configuratie. Met deze mogelijkheid u kiezen welke logboekcategorieën u wilt behouden voor Log Analytics en welke logboekcategorieën u bijvoorbeeld wilt archiveren.

1. Selecteer **Opslaan** om uw wijzigingen te maken als u klaar bent. De doelbronnen beginnen azure AD DS-beveiligingscontrolegebeurtenissen te ontvangen kort nadat de configuratie is opgeslagen.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Beveiligingscontrolegebeurtenissen inschakelen met Azure PowerShell

Voer de volgende stappen uit om azure AD DS-beveiligingscontrolegebeurtenissen in te schakelen met Azure PowerShell. Installeer indien nodig [eerst de Azure PowerShell-module en maak verbinding met uw Azure-abonnement.](/powershell/azure/install-az-ps)

> [!IMPORTANT]
> Azure AD DS-beveiligingsaudits zijn niet met terugwerkende kracht. U gebeurtenissen uit het verleden niet ophalen of opnieuw afspelen. Azure AD DS kan alleen gebeurtenissen verzenden die plaatsvinden nadat beveiligingsaudits zijn ingeschakeld.

1. Verifieer naar uw Azure-abonnement met de cmdlet [Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) Voer desgevraagd uw accountgegevens in.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Maak de doelbron voor de beveiligingscontrolegebeurtenissen.

    * **Azure-opslag** - [Een opslagaccount maken met Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure-gebeurtenishubs** - [Maak een gebeurtenishub met Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Mogelijk moet u ook de cmdlet [Nieuw-AzEventHubAutorisatieregel](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) gebruiken om een autorisatieregel te maken die Azure AD DS-machtigingen verleent aan de naamruimte van de *gebeurtenishub.* De autorisatieregel moet de rechten **beheren,** **Luisteren**en **Verzenden** bevatten.

        > [!IMPORTANT]
        > Zorg ervoor dat u de autorisatieregel instelt op de naamruimte van de gebeurtenishub en niet op de gebeurtenishub zelf.

    * **Azure Log Analytic workspaces** - [Maak een Log Analytics-werkruimte met Azure PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

1. Download de bron-id voor uw door Azure AD DS beheerde domein met de cmdlet [Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Maak een variabele met de naam *$aadds. ResourceId* om de waarde vast te houden:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Configureer de Azure Diagnostic-instellingen met de cmdlet [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) om de doelbron voor beveiligingscontrolegebeurtenissen van Azure AD Domain Services te gebruiken. In de volgende voorbeelden wordt de variabele *$aadds. ResourceId* wordt gebruikt vanaf de vorige stap.

    * **Azure-opslag** - *StorageAccountId* vervangen door de naam van uw opslagaccount:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure-gebeurtenishubs** - Vervang *eventHubName* door de naam van uw gebeurtenishub en *eventHubRuleId* door uw autorisatieregel-id:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Analytic workspaces** - *WorkspaceId* vervangen door de id van de Werkruimte Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Beveiligingscontrolegebeurtenissen opvragen en weergeven met Azure Monitor

Met loganalytische werkruimten u de beveiligingscontrolegebeurtenissen weergeven en analyseren met Azure Monitor en de Kusto-querytaal. Deze querytaal is ontworpen voor alleen-lezen gebruik met krachtige analytische mogelijkheden met een eenvoudig te lezen syntaxis. Zie de volgende artikelen voor meer informatie om aan de slag te gaan met kusto-querytalen:

* [Documentatie voor Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Aan de slag met Log Analytics in Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Aan de slag met logboekquery's in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Dashboards van Log Analytics-gegevens maken en delen](../azure-monitor/learn/tutorial-logs-dashboards.md)

De volgende voorbeeldquery's kunnen worden gebruikt om beveiligingscontrolegebeurtenissen van Azure AD DS te analyseren.

### <a name="sample-query-1"></a>Voorbeeldquery 1

Bekijk alle gebeurtenis op het vergrendelen van een account van de afgelopen zeven dagen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Voorbeeldquery 2

Bekijk alle account lockout events (*4740*) tussen 3 februari 2020 om 9 uur. en 10 februari 2020 middernacht, gesorteerd op lopend naar datum en tijd:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Voorbeeldquery 3

Bekijk aanmeldingsgebeurtenissen voor accounts zeven dagen geleden (vanaf nu) voor de gebruiker met een account:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Voorbeeldquery 4

Bekijk accountaanmeldingsgebeurtenissen zeven dagen geleden vanaf nu voor de gebruiker met de naam account die zich probeerde aan te melden met een slecht wachtwoord *(0xC000006a):*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Voorbeeldquery 5

Bekijk accountaanmeldingsgebeurtenissen zeven dagen geleden vanaf nu voor de gebruiker met de naam account die zich probeerde aan te melden terwijl het account is vergrendeld (*0xC0000234):*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Voorbeeldquery 6

Bekijk het aantal aanmeldingsgebeurtenissen voor het account zeven dagen geleden vanaf nu voor alle aanmeldingspogingen die zijn uitgevoerd voor alle vergrendelde gebruikers:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Categorieën auditgebeurtenissen

Azure AD DS-beveiligingsaudits sluiten aan bij traditionele controle voor traditionele AD DS-domeincontrollers. In hybride omgevingen u bestaande controlepatronen opnieuw gebruiken, zodat dezelfde logica kan worden gebruikt bij het analyseren van de gebeurtenissen. Afhankelijk van het scenario dat u moet oplossen of analyseren, moeten de verschillende categorieën auditgebeurtenissen worden getarget.

De volgende categorieën van auditgebeurtenissen zijn beschikbaar:

| Naam van controlerubriek | Beschrijving |
|:---|:---|
| Aanmelding bij account|Audits pogingen om accountgegevens te verifiëren op een domeincontroller of op een lokale Security Accounts Manager (SAM).</p>Beleidsinstellingen en gebeurtenissen aanmelden en afmelden houden pogingen om toegang te krijgen tot een bepaalde computer bij. Instellingen en gebeurtenissen in deze categorie richten zich op de accountdatabase die wordt gebruikt. Deze categorie omvat de volgende subcategorieën:<ul><li>[Validatie van referenties controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[De Kerberos-authenticatieservice controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos-serviceticketbewerkingen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Andere aanmeldings-/afmeldingsgebeurtenissen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Accountbeheer|Controleert wijzigingen in gebruikers- en computeraccounts en -groepen. Deze categorie omvat de volgende subcategorieën:<ul><li>[Beheer van toepassingsgroepen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Beheer van computeraccounts controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Beheer van distributiegroepen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Controle ander accountbeheer](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Beheer van beveiligingsgroepen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Gebruikersaccountbeheer controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Detailtracking|Controleert activiteiten van individuele toepassingen en gebruikers op die computer en om te begrijpen hoe een computer wordt gebruikt. Deze categorie omvat de volgende subcategorieën:<ul><li>[DPAPI-activiteiten controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Audit PNP-activiteit](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Het maken van processen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Het beëindigen van processen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC-gebeurtenissen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Toegang tot Directory-services|Audits proberen objecten te openen en te wijzigen in Ad DS (Active Directory Domain Services). Deze controlegebeurtenissen worden alleen geregistreerd op domeincontrollers. Deze categorie omvat de volgende subcategorieën:<ul><li>[Gedetailleerde directoryservice-replicatie controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Directoryservice-toegang controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Directoryservice-wijzigingen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Directoryservice-replicatie controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Aanmelding|Audits probeert zich interactief of via een netwerk op een computer aan te melden. Deze gebeurtenissen zijn handig voor het bijhouden van gebruikersactiviteiten en het identificeren van mogelijke aanvallen op netwerkbronnen. Deze categorie omvat de volgende subcategorieën:<ul><li>[Accountvergrendeling controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Gebruikers-/apparaatclaims controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec uitgebreide modus controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Lidmaatschap van auditgroepen](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec-hoofdmodus controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec snelle modus controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Afmelden controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Aanmelden controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Network Policy Server controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Andere aanmeldings-/afmeldingsgebeurtenissen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Speciale aanmelding controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Objecttoegang| Audits probeert toegang te krijgen tot specifieke objecten of typen objecten op een netwerk of computer. Deze categorie omvat de volgende subcategorieën:<ul><li>[Gegenereerde toepassing controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Gegenereerde toepassing controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Gedetailleerde bestandsshare controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Bestandsshare controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Controlebestandssysteem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Filterplatform-verbinding controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Verloren gegane pakketten van filterplatform controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulatie van controlegrepen](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Object Auditkernel](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Andere gebeurtenissen van objecttoegang controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Controleregister](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Verwisselbare opslag controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Centrale toegangsbeleidsfase controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Beleidswijziging|Controleert wijzigingen in belangrijk beveiligingsbeleid op een lokaal systeem of netwerk. Beleidsregels worden meestal opgesteld door beheerders om netwerkbronnen te beveiligen. Het bewaken van wijzigingen of pogingen om dit beleid te wijzigen kan een belangrijk aspect van beveiligingsbeheer voor een netwerk zijn. Deze categorie omvat de volgende subcategorieën:<ul><li>[Wijziging van controlebeleid controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Wijziging in authenticatiebeleid controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Wijziging in autorisatiebeleid controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Wijziging in filterplatformbeleid controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Wijziging in beleid MPSSVC op regelniveau controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Andere beleidswijziging controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Gebruik van bevoegdheden| Controleert het gebruik van bepaalde machtigingen op een of meer systemen. Deze categorie omvat de volgende subcategorieën:<ul><li>[Gebruik van niet-gevoelige bevoegdheid controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Gebruik van gevoelige bevoegdheid controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Gebeurtenissen voor gebruik van andere bevoegdheden controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Systeem| Audits systeemniveau wijzigingen aan een computer niet opgenomen in andere categorieën en die potentiële gevolgen voor de veiligheid hebben. Deze categorie omvat de volgende subcategorieën:<ul><li>[IPsec-stuurprogramma controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Andere systeemgebeurtenissen controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Wijziging in beveiligingsstatus controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Uitbreiding van beveiligingssysteem controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Systeemintegriteit controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Gebeurtenis-iDs per categorie

 Azure AD DS-beveiligingsaudits registreren de volgende gebeurtenis---iD's wanneer de specifieke actie een controleerbare gebeurtenis activeert:

| Naam van gebeurtenisrubriek | Gebeurtenis-iD's |
|:---|:---|
|Accountaanmeldingsbeveiliging|4767, 4774, 4775, 4776, 4777|
|Accountbeheerbeveiliging|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Beveiliging detailtracking|Geen|
|DS Access-beveiliging|5136, 5137, 5138, 5139, 5141|
|Beveiliging van afmelden|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objecttoegangsbeveiliging|Geen|
|Beveiliging van beleidswijzigingen|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Beveiliging voor bevoegdheden gebruik|4985|
|Systeembeveiliging|4612, 4621|

## <a name="next-steps"></a>Volgende stappen

Zie voor specifieke informatie over Kusto de volgende artikelen:

* [Overzicht](/azure/kusto/query/) van de Kusto-querytaal.
* [Kusto tutorial](/azure/kusto/query/tutorial) om u vertrouwd te maken met query basics.
* [Voorbeeldquery's](/azure/kusto/query/samples) waarmee u nieuwe manieren vinden om uw gegevens te bekijken.
* Kusto [best practices](/azure/kusto/query/best-practices) om uw zoekopdrachten te optimaliseren voor succes.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
