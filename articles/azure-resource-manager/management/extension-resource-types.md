---
title: Resourcetypen van extensies
description: Lijsten met de Azure-brontypen worden gebruikt om de mogelijkheden van andere resourcetypen uit te breiden.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3a3fbc531750bec4b16e38f1fe79f613c1b94f5e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754870"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resourcetypen die de mogelijkheden van andere resources uitbreiden

Een extensiebron is een resource die bijdraagt aan de mogelijkheden van een andere resource. Resourcevergrendeling is bijvoorbeeld een extensiebron. U past een resourcevergrendeling toe op een andere resource om te voorkomen dat deze wordt verwijderd of gewijzigd. Het heeft geen zin om zelf een resourcelock te maken. Een extensiebron wordt altijd toegepast op een andere resource.

## <a name="extension-resource-types"></a>Resourcetypen van extensies

- Microsoft.Advisor/configuraties
- Microsoft.Advisor/aanbevelingen
- Microsoft.Advisor/onderdrukkingen
- Microsoft.AlertsBeheer/waarschuwingen
- Samenvatting van Microsoft.AlertsManagement/waarschuwingen
- Microsoft.Authorization/checkAccess
- Microsoft.Authorization/denyOpdrachten
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Authorization/sloten
- Microsoft.Authorization/machtigingen
- Microsoft.Autorisatie/policyToewijzingen
- Microsoft.Authorization/policyDefinities
- Microsoft.Authorization/policySetDefinities
- Microsoft.Autorisatie/roleToewijzingen
- Microsoft.Authorization/roleAssignmentsUsageMetrics
- Microsoft.Authorization/roleDefinities
- Microsoft.Facturerings-/factureringsperioden
- Microsoft.Facturering/factureringMachtigingen
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinities
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintOpdrachten
- Microsoft.Blueprint/blauwdrukken
- Microsoft.Verbruik/Geaggregeerde kosten
- Microsoft.Verbruik/saldi
- Microsoft.Verbruik/Budgetten
- Microsoft.Verbruik/kosten
- Microsoft.Consumption/CostTags
- Microsoft.Verbruik/prognoses
- Microsoft.Verbruik/Marktplaatsen
- Microsoft.Consumption/OperationResults
- Microsoft.Consumption/OperationStatus
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumption/ReserveringSGegevens
- Aanbevelingen voor Microsoft.Consumption/Reservering
- Samenvattingen van Microsoft.Consumption/Reservering
- Microsoft.Consumption/Reserveringstransacties
- Microsoft.Consumption/Tags
- Microsoft.Verbruik/Voorwaarden
- Microsoft.Consumption/UsageDetails
- Microsoft.Verbruik/credits
- Microsoft.Verbruik/gebeurtenissen
- Microsoft.Verbruik/partijen
- Microsoft.Verbruik/producten
- Microsoft.Verbruik/tenants
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Waarschuwingen
- Microsoft.CostManagement/Budgetten
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Export
- Microsoft.CostManagement/Externe abonnementen
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Rapporten
- Microsoft.CostManagement/Weergaven
- Microsoft.CostManagement/showbackRegels
- Microsoft.CustomProviders/koppelingen
- Microsoft.EventGrid/eventAbonnementen
- Microsoft.EventGrid/extensionOnderwerpen
- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareUpdates
- microsoft.insights/baseline
- microsoft.insights/calculatebaseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinities
- microsoft.insights/logs
- microsoft.insights/metricDefinities
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topologie
- microsoft.insights/transacties
- microsoft.insights/vmInsightsOnboardingStatussen
- Microsoft.KubernetesConfiguratie/sourceControlConfigurations
- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments Microsoft.Maintenance/configurationAssignments Microsoft.Maintenance/configurationAssignments Microsoft.
- Microsoft.Maintenance/updates
- Microsoft.ManagedIdentity/Identiteiten
- Microsoft.ManagedServices/registratieToewijzingen
- Microsoft.ManagedServices/registratieDefinities
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsManagement/managementorganisaties
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicatieGeschiktheidResultaten    
- Microsoft.ResourceHealth/availabilityStatussen
- Microsoft.ResourceHealth/childAvailabilityStatussen
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/gebeurtenissen
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/meldingen
- Microsoft.Resources/koppelingen
- Microsoft.Resources/tags
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/complianceResultaten
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.SecurityInsights/aggregaties
- Microsoft.SecurityInsights/waarschuwingRuleTemplates
- Microsoft.SecurityInsights/alertRegels
- Microsoft.SecurityInsights/bladwijzers
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/entiteiten
- Microsoft.SecurityInsights/entityQueries
- Microsoft.SecurityInsights/incidenten
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/instellingen
- Microsoft.SoftwarePlan/hybridUseBenefits
- Microsoft.Subscription/Abonnement maken
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/componenten
- Microsoft.WorkloadMonitor/monitorInstanties
- Microsoft.WorkloadMonitor/monitors
- Microsoft.WorkloadMonitor/notificationInstellingen

## <a name="next-steps"></a>Volgende stappen

- Als u de bron-id voor een extensiebron in een Azure Resource Manager-sjabloon wilt krijgen, gebruikt u de [extensieResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Zie [Event Grid Event Event Subscriptions](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)voor een voorbeeld van het maken van een extensiebron in een sjabloon.
