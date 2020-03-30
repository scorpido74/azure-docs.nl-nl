---
title: Aangepaste beleidsregels implementeren met Azure Pipelines
titleSuffix: Azure AD B2C
description: Meer informatie over het implementeren van aangepast Azure AD B2C-beleid in een CI/CD-pijplijn met Azure Pipelines in Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188746"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Aangepaste beleidsregels implementeren met Azure Pipelines

Door gebruik te maken van een CI/CD-pijplijn (continuous integration and delivery) die u in [Azure Pipelines][devops-pipelines]hebt ingesteld, u uw aangepaste Azure AD B2C-beleid opnemen in uw softwareweergave en codebeheerautomatisering. Terwijl u implementeert naar verschillende Azure AD B2C-omgevingen, bijvoorbeeld dev, test en productie, raden we u aan handmatige processen te verwijderen en geautomatiseerde tests uit te voeren met Azure Pipelines.

Er zijn drie primaire stappen vereist om Azure Pipelines in te schakelen om aangepaste beleidsregels binnen Azure AD B2C te beheren:

1. Een webtoepassingsregistratie maken in uw Azure AD B2C-tenant
1. Een Azure Repo configureren
1. Een Azure-pijplijn configureren

> [!IMPORTANT]
> Het beheren van azure AD B2C aangepaste beleidsregels met een `/beta` Azure Pipeline maakt momenteel gebruik **van preview-bewerkingen** die beschikbaar zijn op het Microsoft Graph API-eindpunt. Het gebruik van deze API's in productie-apps wordt niet ondersteund. Zie de verwijzing naar het eindpunt van de [Microsoft Graph REST API-bèta.](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)

## <a name="prerequisites"></a>Vereisten

* [Azure AD B2C-tenant](tutorial-create-tenant.md)en referenties voor een gebruiker in de map met de rol [B2C IEF-beleidsbeheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Aangepast beleid](custom-policy-get-started.md) geüpload naar uw tenant
* [Beheer-app](microsoft-graph-get-started.md) geregistreerd in uw tenant met het Microsoft Graph *API-machtigingsbeleid.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)en toegang tot een [Azure DevOps Services-project][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Subsidiestroom clientreferenties

Het hier beschreven scenario maakt gebruik van service-to-service-gesprekken tussen Azure Pipelines en Azure AD B2C met behulp van de [subsidiestroom](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)voor OAuth 2.0-clientreferenties . Met deze subsidiestroom kan een webservice zoals Azure Pipelines (de vertrouwelijke client) zijn eigen referenties gebruiken in plaats van zich voor te doen als een gebruiker om te verifiëren wanneer een andere webservice wordt aangeroepen (in dit geval de Microsoft Graph API). Azure Pipelines verkrijgt een token niet-interactief en doet vervolgens aanvragen voor de Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Een toepassing registreren voor beheertaken

Zoals vermeld in [Voorwaarden](#prerequisites), hebt u een toepassingsregistratie nodig die uw PowerShell-scripts - uitgevoerd door Azure Pipelines - kunnen gebruiken voor toegang tot de bronnen in uw tenant.

Als u al een toepassingsregistratie hebt die u gebruikt voor automatiseringstaken, moet u ervoor zorgen dat de microsoft > **graph-beleidsbeleidsmedewerker** > is**verleend.ReadWrite.TrustFramework-toestemming** in de **API-machtigingen** van de app-registratie. **Microsoft Graph**

Zie [Azure AD B2C beheren met Microsoft Graph](microsoft-graph-get-started.md)voor instructies voor het registreren van een beheertoepassing.

## <a name="configure-an-azure-repo"></a>Een Azure Repo configureren

Als een beheertoepassing is geregistreerd, bent u klaar om een opslagplaats voor uw beleidsbestanden te configureren.

1. Meld u aan bij uw Azure DevOps Services-organisatie.
1. [Maak een nieuw project][devops-create-project] of selecteer een bestaand project.
1. Navigeer in uw project naar **Repo's** en selecteer de pagina **Bestanden.** Selecteer een bestaande opslagplaats of maak er een voor deze oefening.
1. Maak een map met de naam *B2CAssets*. Geef de vereiste tijdelijke aanduiding README.md *en* **verbind** het bestand. U dit bestand later verwijderen, als u dat wilt.
1. Voeg uw Azure AD B2C-beleidsbestanden toe aan de map *B2CAssets.* Dit omvat de *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*en alle andere beleidsregels die u hebt gemaakt. Neem de bestandsnaam van elk Azure AD B2C-beleidsbestand op voor gebruik in een latere stap (ze worden gebruikt als PowerShell-scriptargumenten).
1. Maak een map met de naam *Scripts* in de hoofdmap van de repository, geef een naam aan het tijdelijke bestand *DeployToB2c.ps1*. Bega het bestand op dit moment niet, dat doe je in een latere stap.
1. Plak het volgende PowerShell-script in *DeployToB2c.ps1*en voer het bestand **vast.** Het script verwerft een token van Azure AD en roept de Microsoft Graph API aan om het beleid binnen de *map B2CAssets* te uploaden naar uw Azure AD B2C-tenant.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Uw Azure-pijplijn configureren

Als uw opslagplaats is geïnitialiseerd en gevuld is met uw aangepaste beleidsbestanden, bent u klaar om de releasepijplijn in te stellen.

### <a name="create-pipeline"></a>Pijplijn maken

1. Meld u aan bij uw Azure DevOps Services-organisatie en navigeer naar uw project.
1. Selecteer in uw project de optie Nieuwe pijplijn **voor pijplijnen** > **Releases** > **.**
1. Selecteer **onder Een sjabloon selecteren**de optie Taak **leegmaken**.
1. Voer een **werkgebiedsnaam**in, bijvoorbeeld *DeployCustomPolicies*, en sluit het deelvenster.
1. Selecteer **Een artefact toevoegen**en selecteer onder **Brontype** **Azure Repository**.
    1. Kies de bronopslagplaats met de map *Scripts* die u hebt ingevuld met het PowerShell-script.
    1. Kies een **standaardvertakking**. Als u in de vorige sectie een nieuwe opslagplaats hebt gemaakt, is de standaardtak *stramien.*
    1. Verlaat de **standaardversieinstelling** *van Laatste van de standaardvertakking*.
    1. Voer een **bronalias** in voor de opslagplaats. Bijvoorbeeld, *policyRepo*. Neem geen spaties op in de aliasnaam.
1. Selecteer **Toevoegen**
1. Wijzig de naam van de pijplijn om de intentie ervan weer te geven. Implementeren van *aangepaste beleidspijplijn*.
1. Selecteer **Opslaan** om de pijplijnconfiguratie op te slaan.

### <a name="configure-pipeline-variables"></a>Pijplijnvariabelen configureren

1. Selecteer het tabblad **Variabelen.**
1. Voeg de volgende variabelen toe onder **Pijplijnvariabelen** en stel hun waarden in zoals opgegeven:

    | Name | Waarde |
    | ---- | ----- |
    | `clientId` | **Sollicitatie (client) ID** van de applicatie die u eerder hebt geregistreerd. |
    | `clientSecret` | De waarde van het **clientgeheim** dat u eerder hebt gemaakt. <br /> Wijzig het variabele type in **het geheim** (selecteer het vergrendelingspictogram). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, waarbij *uw b2c-tenant* de naam is van uw Azure AD B2C-tenant. |

1. Selecteer **Opslaan** om de variabelen op te slaan.

### <a name="add-pipeline-tasks"></a>Pijplijntaken toevoegen

Voeg vervolgens een taak toe om een beleidsbestand te implementeren.

1. Selecteer het tabblad **Taken.**
1. Selecteer **de taak Agent**en selecteer**+** vervolgens het plusteken ( ) om een taak toe te voegen aan de agenttaak.
1. PowerShell zoeken **PowerShell**en selecteren . Selecteer niet 'Azure PowerShell', 'PowerShell op doelmachines' of een andere PowerShell-vermelding.
1. Selecteer de nieuw toegevoegde **PowerShell Script-taak.**
1. Voer de volgende waarden in voor de PowerShell Script-taak:
    * **Taakversie**: 2.*
    * **Weergavenaam:** de naam van het beleid dat deze taak moet uploaden. Bijvoorbeeld *B2C_1A_TrustFrameworkBase*.
    * **Tekst:** bestandspad
    * **Scriptpad:** Selecteer de ellips (***...***), navigeer naar de map *Scripts* en selecteer vervolgens het bestand *DeployToB2C.ps1.*
    * **Argumenten:**

        Voer de volgende waarden in voor **Argumenten**. Vervang `{alias-name}` de alias die u in de vorige sectie hebt opgegeven.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Als de alias die u hebt opgegeven bijvoorbeeld *policyRepo*is, moet de argumentregel zijn:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selecteer **Opslaan** om de agenttaak op te slaan.

De taak die u zojuist hebt toegevoegd, uploadt *één* beleidsbestand naar Azure AD B2C. Voordat u verdergaat, activeert u de taak handmatig (**Release maken)** om ervoor te zorgen dat deze met succes wordt voltooid voordat u extra taken maakt.

Als de taak is voltooid, voegt u implementatietaken toe door de voorgaande stappen uit te voeren voor elk van de aangepaste beleidsbestanden. Wijzig `-PolicyId` de `-PathToFile` argumentwaarden en argumentwaarden voor elk beleid.

Het `PolicyId` is een waarde die wordt gevonden aan het begin van een XML-beleidsbestand binnen het knooppunt TrustFrameworkPolicy. Xml `PolicyId` is bijvoorbeeld in het volgende beleid *B2C_1A_TrustFrameworkBase:*

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Wanneer u de agents uitvoert en de beleidsbestanden uploadt, moet u ervoor zorgen dat ze in deze volgorde worden geüpload:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfielBewerken.xml*
1. *PasswordReset.xml*

Het Identity Experience Framework dwingt deze volgorde af omdat de bestandsstructuur is gebouwd op een hiërarchische keten.

## <a name="test-your-pipeline"></a>Test uw pijplijn

Ga als volgende over op de testpijplijn:

1. Selecteer **Pijplijnen** en vervolgens **Releases**.
1. Selecteer de pijplijn die u eerder hebt gemaakt, bijvoorbeeld *DeployCustomPolicies*.
1. Selecteer **Release maken**en selecteer Vervolgens **Maken** om de release in de wachtrij te plaatsen.

U ziet een meldingsbanner waarin staat dat een release in de wachtrij is geplaatst. Als u de status wilt weergeven, selecteert u de koppeling in de meldingsbanner of selecteert u deze in de lijst op het tabblad **Releases.**

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

* [Service-to-service-gesprekken met behulp van clientreferenties](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
