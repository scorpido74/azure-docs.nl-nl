---
title: Resourcetypen van extensies
description: Een lijst met de Azure-resource typen wordt gebruikt om de mogelijkheden van andere resource typen uit te breiden.
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: af60bd648e41532cca364272fb0ded01c0c18239
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484673"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resource typen die de mogelijkheden van andere resources uitbreiden

Een extensie resource is een resource die wordt toegevoegd aan de mogelijkheden van een andere resource. Resource vergrendeling is bijvoorbeeld een uitbreidings resource. U past een resource vergrendeling toe op een andere resource om te voor komen dat deze wordt verwijderd of gewijzigd. Het is niet verstandig een resource vergrendeling zelf te maken. Een extensie resource wordt altijd toegepast op een andere resource.

## <a name="extension-resource-types"></a>Resourcetypen van extensies

- Micro soft. Advisor/configuraties
- Micro soft. Advisor/aanbevelingen
- Micro soft. Advisor/onderdrukkingen
- Micro soft. AlertsManagement/Alerts
- Micro soft. AlertsManagement/alertsSummary
- Micro soft. Authorization/checkAccess
- Micro soft. Authorization/denyAssignments
- Micro soft. autorisatie/vergren delingen
- Micro soft. Authorization/permissions
- Micro soft. Authorization/policyAssignments
- Micro soft. Authorization/policyDefinitions
- Micro soft. Authorization/policySetDefinitions
- Micro soft. Authorization/roleAssignments
- Micro soft. Authorization/roleDefinitions
- Micro soft. facturering/billingPeriods
- Micro soft. facturering/billingPermissions
- Micro soft. facturering/billingRoleAssignments
- Micro soft. facturering/billingRoleDefinitions
- Micro soft. facturering/createBillingRoleAssignment
- Micro soft. blauw druk/blueprintAssignments
- Micro soft. blauw druk/blauw drukken
- Micro soft. verbruik/AggregatedCost
- Micro soft. verbruik/saldi
- Micro soft. verbruik/budgetten
- Micro soft. verbruik/kosten
- Micro soft. verbruik/CostTags
- Micro soft. verbruik/prognoses
- Micro soft. verbruik/markt plaatsen
- Micro soft. verbruik/OperationResults
- Micro soft. verbruik/OperationStatus
- Micro soft. verbruik/Pricesheets
- Micro soft. verbruik/ReservationDetails
- Micro soft. verbruik/ReservationRecommendations
- Micro soft. verbruik/ReservationSummaries
- Micro soft. verbruik/ReservationTransactions
- Micro soft. verbruik/Tags
- Micro soft. verbruik/voor waarden
- Micro soft. verbruik/UsageDetails
- Micro soft. verbruik/tegoed
- Micro soft. verbruik/gebeurtenissen
- Micro soft. verbruik/loten
- Micro soft. verbruik/producten
- Micro soft. verbruik/tenants
- Micro soft. ContainerInstance/serviceAssociationLinks
- Micro soft. CostManagement/Alerts
- Micro soft. CostManagement/budgetten
- Micro soft. CostManagement/Dimensions
- Micro soft. CostManagement/exports
- Micro soft. CostManagement/ExternalSubscriptions
- Micro soft. CostManagement/Forecast
- Micro soft. CostManagement/query
- Micro soft. CostManagement/Reportconfigs
- Micro soft. CostManagement/Reports
- Micro soft. CostManagement/views
- Micro soft. CostManagement/showbackRules
- Micro soft. CustomProviders/Associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Micro soft. GuestConfiguration/configurationProfileAssignments
- Micro soft. GuestConfiguration/guestConfigurationAssignments
- Micro soft. GuestConfiguration/software
- Micro soft. GuestConfiguration/softwareUpdateProfile
- Micro soft. GuestConfiguration/softwareUpdates
- micro soft. Insights/automatedExportSettings
- micro soft. Insights/basis lijn
- micro soft. Insights/calculatebaseline
- micro soft. Insights/diagnosticSettings
- micro soft. Insights/diagnosticSettingsCategories
- micro soft. Insights/eventtypes
- micro soft. Insights/extendedDiagnosticSettings
- micro soft. Insights/guestDiagnosticSettingsAssociation
- micro soft. Insights/logDefinitions
- micro soft. Insights/logboeken
- micro soft. Insights/metricDefinitions
- micro soft. Insights/metricNamespaces
- micro soft. Insights/metricbaselines
- micro soft. Insights/metrische gegevens
- micro soft. Insights/myWorkbooks
- micro soft. Insights/vmInsightsOnboardingStatuses
- Micro soft. KubernetesConfiguration/sourceControlConfigurations
- Micro soft. Maintenance/applyUpdates
- Micro soft. Maintenance/configurationAssignments
- Micro soft. onderhoud/updates
- Micro soft. ManagedIdentity/Identities
- Micro soft. ManagedServices/registrationAssignments
- Micro soft. ManagedServices/registrationDefinitions
- Micro soft. OperationalInsights/storageInsightConfigs
- Micro soft. OperationsManagement/managementassociations
- Micro soft. PolicyInsights/policyEvents
- Micro soft. PolicyInsights/policyStates
- Micro soft. PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Micro soft. Recovery Services/backupProtectedItems
- Micro soft. ResourceHealth/availabilityStatuses
- Micro soft. ResourceHealth/childAvailabilityStatuses
- Micro soft. ResourceHealth/childResources
- Micro soft. ResourceHealth/Events
- Micro soft. ResourceHealth/impactedResources
- Micro soft. ResourceHealth/meldingen
- Micro soft. resources/koppelingen
- Micro soft. resources/Tags
- Micro soft. Security/Compliances
- Micro soft. Security/InformationProtectionPolicies
- Micro soft. Security/adaptiveNetworkHardenings
- Micro soft. Security/advancedThreatProtectionSettings
- Micro soft. Security/assessmentMetadata
- Micro soft. Security/beoordelingen
- Micro soft. Security/complianceResults
- Micro soft. Security/dataCollectionAgents
- Micro soft. Security/dataCollectionResults
- Micro soft. Security/deviceSecurityGroups
- Micro soft. Security/networkData
- Micro soft. Security/serverVulnerabilityAssessments
- Micro soft. SecurityInsights/aggregaties
- Micro soft. SecurityInsights/alertRuleTemplates
- Micro soft. SecurityInsights/alertRules
- Micro soft. SecurityInsights/blad wijzers
- Micro soft. SecurityInsights/cases
- Micro soft. SecurityInsights/dataConnectors
- Micro soft. SecurityInsights/entities
- Micro soft. SecurityInsights/entityQueries
- Micro soft. SecurityInsights/officeConsents
- Micro soft. SecurityInsights/Settings
- Micro soft. SoftwarePlan/hybridUseBenefits
- Micro soft. Subscription/CreateSubscription
- micro soft. support/createsupportticket
- micro soft. support/Supporttickets
- Micro soft. WorkloadMonitor/-onderdelen
- Micro soft. WorkloadMonitor/monitorInstances
- Micro soft. WorkloadMonitor/monitors
- Micro soft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Volgende stappen

- Gebruik de [extensionResourceId](template-functions-resource.md#extensionresourceid)om de resource-id voor een extensie bron in een Azure Resource Manager sjabloon op te halen.
- Zie [Event grid gebeurtenis abonnementen](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)voor een voor beeld van het maken van een extensie bron in een sjabloon.
