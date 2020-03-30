---
title: Azure Analysis Services-modellen vernieuwen met Azure Automation | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u modelvernieuwingen voor Azure Analysis Services codeert met Azure Automation.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572355"
---
# <a name="refresh-with-azure-automation"></a>Vernieuwen met Azure Automation

Met Azure Automation en PowerShell Runbooks u geautomatiseerde bewerkingen voor het vernieuwen van gegevens uitvoeren op uw tabelmodellen voor Azure Analysis.  

Het voorbeeld in dit artikel maakt gebruik van de [PowerShell SqlServer-modules.](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)

Een voorbeeld PowerShell Runbook, waaruit blijkt dat een model wordt vernieuwd, wordt later in dit artikel gegeven.  

## <a name="authentication"></a>Authentication

Alle aanroepen moeten worden geverifieerd met een geldig Azure Active Directory -token (OAuth 2).  In het voorbeeld in dit artikel wordt een Service Principal (SPN) gebruikt om te verifiëren voor Azure Analysis Services.

Zie [Een serviceprincipal maken met Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het maken van een serviceprincipal.

## <a name="prerequisites"></a>Vereisten

> [!IMPORTANT]
> In het volgende voorbeeld wordt ervan uitgegaan dat de firewall van Azure Analysis Services is uitgeschakeld. Als de firewall is ingeschakeld, moet het openbare IP-adres van de initiator van de aanvraag op de witte lijst in de firewall worden weergegeven.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installeer SqlServer-modules vanuit PowerShell-galerie.

1. Klik in uw Azure Automation-account op **Modules**en **blader vervolgens door de galerie**.

2. Zoek in de zoekbalk naar **SqlServer**.

    ![Zoekmodules](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecteer SqlServer en klik op **Importeren**.
 
    ![Module importeren](./media/analysis-services-refresh-azure-automation/2.png)

4. Klik op **OK**.
 
### <a name="create-a-service-principal-spn"></a>Een serviceprincipal (SPN) maken

Zie [Een serviceprincipal maken met Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het maken van een serviceprincipal.

### <a name="configure-permissions-in-azure-analysis-services"></a>Machtigingen configureren in Azure Analysis Services
 
De serviceprincipal die u maakt, moet serverbeheerdersmachtigingen op de server hebben. Zie Een [serviceprincipal toevoegen aan de serverbeheerderrol](analysis-services-addservprinc-admins.md)voor meer informatie.

## <a name="design-the-azure-automation-runbook"></a>De Azure Automation Runbook ontwerpen

1. Maak in het automatiseringsaccount een **credentials-bron** die wordt gebruikt om de Service Principal veilig op te slaan.

    ![Referenties maken](./media/analysis-services-refresh-azure-automation/6.png)

2. Voer de gegevens voor de referentie in.  Voer voor de **gebruikersnaam**de **SPN ClientId**in voor het **wachtwoord**, voer het **SPN-geheim**in .

    ![Referenties maken](./media/analysis-services-refresh-azure-automation/7.png)

3. Het runbook voor automatisering importeren

    ![Runbook importeren](./media/analysis-services-refresh-azure-automation/8.png)

4. Blader naar het **bestand Refresh-Model.ps1,** geef een **naam** en **beschrijving**op en klik op **Maken**.

    ![Runbook importeren](./media/analysis-services-refresh-azure-automation/9.png)

5. Wanneer de Runbook is gemaakt, gaat deze automatisch in de bewerkingsmodus.  Selecteer **Publiceren**.

    ![Runbook publiceren](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > De referentiebron die eerder is gemaakt, wordt opgehaald door de runbook met de opdracht **Get-AutomationPSCredential.**  Deze opdracht wordt vervolgens doorgegeven aan de opdracht **Invoke-ProcessASADatabase** PowerShell om de verificatie uit te voeren naar Azure Analysis Services.

6. Test het runbook door op **Start**te klikken .

    ![Het runbook starten](./media/analysis-services-refresh-azure-automation/11.png)

7. Vul de parameters **DATABASENAME,** **ANALYSISSERVER**en **REFRESHTYPE** in en klik op **OK**. De parameter **WEBHOOKDATA** is niet vereist wanneer de Runbook handmatig wordt uitgevoerd.

    ![Het runbook starten](./media/analysis-services-refresh-azure-automation/12.png)

Als de runbook is uitgevoerd, ontvangt u een uitvoer zoals:

![Succesvolle run](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Een op zichzelf staande Azure Automation Runbook gebruiken

De Runbook kan worden geconfigureerd om de vernieuwing van het Azure Analysis Services-model op een geplande basis te activeren.

Dit kan als volgt worden geconfigureerd:

1. Klik in het runboek automatisering op **Planningen**en **voeg vervolgens een planning toe**.
 
    ![Planning maken](./media/analysis-services-refresh-azure-automation/14.png)

2. Klik **op Een** > **nieuw schema plannen**en vul de details in.

    ![Planning configureren](./media/analysis-services-refresh-azure-automation/15.png)

3. Klik **op Maken**.

4. Vul de parameters voor het schema in. Deze worden elke keer gebruikt wanneer het runbook wordt geactiveerd. De parameter **WEBHOOKDATA** moet leeg blijven bij het uitvoeren via een schema.

    ![Parameters configureren](./media/analysis-services-refresh-azure-automation/16.png)

5. Klik op **OK**.

## <a name="consume-with-data-factory"></a>Verbruiken met gegevensfabriek

Als u de runbook wilt gebruiken met Azure Data Factory, maakt u eerst een **Webhook** voor het runbook. De **Webhook** geeft een URL die kan worden aangeroepen via een Azure Data Factory-webactiviteit.

> [!IMPORTANT]
> Als u een **webhook wilt**maken, moet de status van het runbook worden **gepubliceerd**.

1. Klik in uw automatiseringsrunboek op **Webhooks**en klik vervolgens op **Webhook toevoegen**.

   ![Webhook toevoegen](./media/analysis-services-refresh-azure-automation/17.png)

2. Geef de Webhook een naam en een vervaldatum.  De naam identificeert alleen de Webhook in het Automation Runbook, deze maakt geen deel uit van de URL.

   >[!CAUTION]
   >Zorg ervoor dat u de URL kopieert voordat u de wizard sluit, omdat u deze niet terug krijgen nadat deze is gesloten.
    
   ![Webhook configureren](./media/analysis-services-refresh-azure-automation/18.png)

    De parameters voor de webhook kunnen leeg blijven.  Bij het configureren van de webactiviteit Azure Data Factory kunnen de parameters worden doorgegeven aan de hoofdtekst van het webgesprek.

3. Configureer in Gegevensfabriek een **webactiviteit**

### <a name="example"></a>Voorbeeld

   ![Voorbeeld van webactiviteit](./media/analysis-services-refresh-azure-automation/19.png)

De **URL** is de URL die is gemaakt van de Webhook.

De **body** is een JSON-document dat de volgende eigenschappen moet bevatten:


|Eigenschap  |Waarde  |
|---------|---------|
|**AnalysisServicesDatabase**     |De naam van de Azure Analysis Services-database <br/> Voorbeeld: AdventureWorksDB         |
|**AnalysisServicesServer**     |De naam van de Azure Analysis Services-server. <br/> Voorbeeld: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Het type vernieuwing dat moet worden uitgevoerd. <br/> Voorbeeld: Vol         |

Voorbeeld JSON body:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Deze parameters worden gedefinieerd in het PowerShell-script van runbook.  Wanneer de webactiviteit wordt uitgevoerd, is de DOORgegeven JSON-payload WEBHOOKDATA.

Dit wordt gedeserialiseerd en opgeslagen als PowerShell-parameters, die vervolgens worden gebruikt door de opdracht Invoke-ProcesASDatabase PowerShell.

![Gedeserialiseerde Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Een hybride werker gebruiken met Azure Analysis Services

Een Azure Virtual Machine met een statisch openbaar IP-adres kan worden gebruikt als Azure Automation Hybrid Worker.  Dit openbare IP-adres kan vervolgens worden toegevoegd aan de firewall van Azure Analysis Services.

> [!IMPORTANT]
> Zorg ervoor dat het openbare IP-adres van de virtuele machine is geconfigureerd als statisch.
>
>Zie [Resources automatiseren in uw datacenter of cloud met Hybride Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)voor meer informatie over het configureren van Hybride Werknemers voor Azure Automation.

Zodra een hybride werknemer is geconfigureerd, maakt u een webhook zoals beschreven in de sectie [Verbruiken met gegevensfabriek](#consume-with-data-factory).  Het enige verschil hier is om de optie **Uitvoeren op** > **hybride werknemers** te selecteren bij het configureren van de Webhook.

Voorbeeld van webhook met Hybride Werker:

![Voorbeeld Hybride Werknemer Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Voorbeeld van PowerShell-runbook

Het volgende codefragment is een voorbeeld van het vernieuwen van het Azure Analysis Services-model met behulp van een PowerShell Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Volgende stappen

[Monsters](analysis-services-samples.md)  
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)
