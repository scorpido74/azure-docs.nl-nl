---
title: Aanmeldingslogboekschema in Azure Monitor | Microsoft Documenten
description: Het azure AD-aanmeldingsschema beschrijven voor gebruik in Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748659"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Het azure AD-aanmeldingslogboekenschema interpreteren in Azure Monitor

In dit artikel wordt het azure active directory-aanmeldingslogboekschema (Azure AD) in Azure Monitor beschreven. De meeste informatie die betrekking heeft op aanmeldingen, *Properties* wordt weergegeven `records` onder het kenmerk Eigenschappen van het object.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Veldbeschrijvingen

| Veldnaam | Beschrijving |
|------------|-------------|
| Time | De datum en tijd, in UTC. |
| ResourceId | Deze waarde is niet in kaart gebracht en u dit veld veilig negeren.  |
| OperationName | Voor aanmeldingen is deze waarde altijd *aanmeldingsactiviteit*. |
| OperationVersion (OperationVersion) | De REST API-versie die door de client wordt aangevraagd. |
| Categorie | Voor aanmeldingen is deze waarde altijd *SignIn*. | 
| TenantId | De tenant-GUID die is gekoppeld aan de logboeken. |
| ResultType | Het resultaat van de aanmeldingsbewerking kan *succes* of *falen*zijn. | 
| ResultSignature | Bevat de eventuele foutcode voor de aanmeldingsbewerking. |
| ResultaatBeschrijving | Geeft de foutbeschrijving voor de aanmeldingsbewerking. |
| risicoDetail | risicoDetail | Geeft de 'reden' achter een specifieke status van een riskante gebruiker, aanmelding of een risicodetectie. De mogelijke waarden `none` `adminGeneratedTemporaryPassword`zijn: `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser` `userPerformedSecuredPasswordChange` `adminConfirmedSigninCompromised`, `unknownFutureValue`, , , , , . De `none` waarde betekent dat er tot nu toe geen actie is uitgevoerd op de gebruiker of aanmelding. <br>**Let op:** Voor details voor deze eigenschap is een Azure AD Premium P2-licentie vereist. Andere licenties geven `hidden`de waarde terug. |
| riskEventTypes | riskEventTypes | Risicodetectietypen die zijn gekoppeld aan de aanmelding. De mogelijke waarden `unlikelyTravel` `anonymizedIPAddress`zijn: `unfamiliarFeatures` `malwareInfectedIPAddress`, `suspiciousIPAddress` `leakedCredentials`, `investigationsThreatIntelligence` `generic`, `maliciousIPAddress` `unknownFutureValue`, , , , en . |
| risico'sGeaggregateerd | risicoNiveau | Geaggregeerd risiconiveau. De mogelijke waarden `none` `low`zijn: `high` `hidden`, `unknownFutureValue`, `medium`, , en . De `hidden` waarde betekent dat de gebruiker of aanmelding niet is ingeschakeld voor Azure AD-identiteitsbeveiliging. **Let op:** Details voor deze eigenschap zijn alleen beschikbaar voor Azure AD Premium P2-klanten. Alle andere klanten `hidden`worden teruggestuurd. |
| riskLevelDuringSignIn | risicoNiveau | Risiconiveau tijdens het aanmelden. De mogelijke waarden `none` `low`zijn: `high` `hidden`, `unknownFutureValue`, `medium`, , en . De `hidden` waarde betekent dat de gebruiker of aanmelding niet is ingeschakeld voor Azure AD-identiteitsbeveiliging. **Let op:** Details voor deze eigenschap zijn alleen beschikbaar voor Azure AD Premium P2-klanten. Alle andere klanten `hidden`worden teruggestuurd. |
| risicoStaat | risicoStaat | Rapporteert de status van de riskante gebruiker, aanmelding of een risicodetectie. De mogelijke waarden `none`zijn: `dismissed` `atRisk`, `confirmedCompromised` `unknownFutureValue`, `confirmedSafe` `remediated`, , , , . |
| DurationMs |  Deze waarde is niet in kaart gebracht en u dit veld veilig negeren. |
| CallerIpAddress | Het IP-adres van de klant die het verzoek heeft ingediend. | 
| CorrelationId | De optionele GUID die door de client wordt doorgegeven. Deze waarde kan helpen bij het correleren van client-side operations met server-side operations, en het is handig wanneer u logboeken bijhoudt die services overspannen. |
| Identiteit | De identiteit van het token dat werd gepresenteerd toen u het verzoek indiende. Het kan een gebruikersaccount, systeemaccount of serviceprincipal zijn. |
| Niveau | Geeft het type bericht. Voor audit is het altijd *informatief.* |
| Locatie | Geeft de locatie van de aanmeldingsactiviteit weer. |
| Eigenschappen | Hier worden alle eigenschappen weergegeven die zijn gekoppeld aan aanmeldingen. Zie [Microsoft Graph API Reference voor](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)meer informatie. Dit schema gebruikt dezelfde kenmerkenals de aanmeldingsbron voor leesbaarheid.

## <a name="next-steps"></a>Volgende stappen

* [Controlelogboekenschema interpreteren in Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Lees meer over Diagnostische logboeken voor Azure](../../azure-monitor/platform/platform-logs-overview.md)
