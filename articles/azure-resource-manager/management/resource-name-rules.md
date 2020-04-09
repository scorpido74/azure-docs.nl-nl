---
title: Beperkingen voor het benoemen van resources
description: Toont de regels en beperkingen voor het benoemen van Azure-bronnen.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f6203f987654b33b32da72bfec030a9d0ab69df8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981525"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Naamgevingsregels en -beperkingen voor Azure-resources

In dit artikel worden naamgevingsregels en beperkingen voor Azure-bronnen samengevat. Zie [Aanbevolen naamgevings- en tagconventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)voor aanbevelingen over het benoemen van resources.

Resourcenamen zijn hoofdletters ongevoelig, tenzij specifiek vermeld in de kolom geldige tekens.

In de volgende tabellen verwijst de term alfanumeriek naar:

* **a** tot **en met z** (kleine letters)
* **A** tot **en met Z** (hoofdletters)
* **0** tot **en met 9** (cijfers)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | resourcegroep | 3-63 | Kleine letters en cijfers.<br><br>Begin met kleine letter. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | service | Globale | 1-50 | Alfanumerieke waarden.<br><br>Begin met de brief. |
> | service / apis | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / apis / problemen | api | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / apis / problemen / bijlagen | Probleem | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / api's / problemen / opmerkingen | Probleem | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / api's / bewerkingen | api | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / api's / bewerkingen / tags | Bewerking | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / api's / releases | api | 1-80 | Alfanumerieke, underscores en koppeltekens.<br><br>Begin en eindig met alfanumerieke of underscore. |
> | service / apis / schema's | api | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / apis / tagBeschrijvingen | api | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / apis / tags | api | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / api-versie-sets | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / autorisatieServers | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / back-ends | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / certificaten | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / diagnostiek | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / groepen | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / groepen / gebruikers | group | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / identiteitProviders | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / loggers | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / meldingen | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / meldingen / ontvangerE-mails | melding | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / openidConnectProviders | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / beleid | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / producten | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / producten / api's | product | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / producten / groepen | product | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / producten / tags | product | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / eigenschappen | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / abonnementen | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / tags | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / sjablonen | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |
> | service / gebruikers | service | 1-256 | Kan het als geen gebruik maken:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | configurationStores | resourcegroep | 5-50 | Alfanumerieke, underscores en koppeltekens. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Sloten | omvang van de toewijzing | 1-90 | Alfanumerieke waarden, perioden, underscores, koppeltekens en haakjes.<br><br>Kan niet eindigen in een periode. |
> | beleidstoewijzingen | omvang van de toewijzing | 1-128 weergavenaam<br><br>1-260 resourcenaam | Weergavenaam kan alle tekens bevatten.<br><br>Resourcenaam kan niet `%` worden opgenomen en kan niet eindigen met periode of ruimte. |
> | beleidsdefinities | definitietje | 1-128 weergavenaam<br><br>1-260 resourcenaam | Weergavenaam kan alle tekens bevatten.<br><br>Resourcenaam kan niet `%` worden opgenomen en kan niet eindigen met periode of ruimte. |
> | policySetDefinities | definitietje | 1-128 weergavenaam<br><br>1-260 resourcenaam | Weergavenaam kan alle tekens bevatten.<br><br>Resourcenaam kan niet `%` worden opgenomen en kan niet eindigen met periode of ruimte.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | automatiseringAccounts | resourcegroep | 6-50 | Alfanumerieke en koppeltekens.<br><br>Begin met de letter en eindig met alfanumeriek. |
> | automationAccounts / certificaten | automatiseringsaccount | 1-128 | Kan het als geen gebruik maken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte.  |
> | automatiseringAccounts / verbindingen | automatiseringsaccount | 1-128 | Kan het als geen gebruik maken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automatiseringAccounts / referenties | automatiseringsaccount | 1-128 | Kan het als geen gebruik maken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automationAccounts / runbooks | automatiseringsaccount | 1-63 | Alfanumerieke, underscores en koppeltekens.<br><br>Begin met de brief.  |
> | automationAccounts / schema's | automatiseringsaccount | 1-128 | Kan het als geen gebruik maken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automatiseringAccounts / variabelen | automatiseringsaccount | 1-128 | Kan het als geen gebruik maken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |
> | automationAccounts / watchers | automatiseringsaccount | 1-63 |  Alfanumerieke, underscores en koppeltekens.<br><br>Begin met de brief. |
> | automationAccounts / webhooks | automatiseringsaccount | 1-128 | Kan het als geen gebruik maken:<br> `<>*%&:\?.+/` <br><br>Kan niet eindigen met ruimte. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | batchAccounts | Regio | 3-24 | Kleine letters en cijfers. |
> | batchAccounts / toepassingen | batchaccount | 1-64 | Alfanumerieke, underscores en koppeltekens. |
> | batchAccounts / certificaten | batchaccount | 5-45 | Alfanumerieke, underscores en koppeltekens. |
> | batchAccounts / pools | batchaccount | 1-64 | Alfanumerieke, underscores en koppeltekens. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | blockchainLeden | Globale | 2-20 | Kleine letters en cijfers.<br><br>Begin met kleine letter. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | botServices | Globale | 2-64 |  Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. |
> | botServices / kanalen | botservice | 2-64 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. |
> | botServices / Verbindingen | botservice | 2-64 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. |
> | enterpriseKanalen | resourcegroep | 2-64 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Redis | Globale | 1-63 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. Opeenvolgende koppeltekens niet toegestaan. |
> | Redis / firewallRegels | Redis | 1-256 | Alfanumerieke waarden |

## <a name="microsoftcdn"></a>Microsoft.Cdn (Microsoft.Cdn)

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Profielen | resourcegroep | 1-260 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |
> | profielen / eindpunten | Globale | 1-50 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | certificaatOrders | resourcegroep | 3-30 | Alfanumerieke waarden. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | resourcegroep | 2-64 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | beschikbaarheidSets | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Eindig met alfanumerieke of underscore. |
> | diskEncryptionSets | resourcegroep | 1-80 | Alfanumerieke en underscores. |
> | Schijven | resourcegroep | 1-80 | Alfanumerieke en underscores. |
> | Galeries | resourcegroep | 1-80 | Alfanumerieke en perioden.<br><br>Begin en einde met alfanumerieke. |
> | galerijen / toepassingen | galerie | 1-80 | Alfanumerieke waarden, koppeltekens en perioden.<br><br>Begin en einde met alfanumerieke. |
> | galerijen / toepassingen/versies | toepassing | 32-bits geheel getal | Nummers en periodes. |
> | galerijen / afbeeldingen | galerie | 1-80 | Alfanumerieke waarden, koppeltekens en perioden.<br><br>Begin en einde met alfanumerieke. |
> | galerijen / afbeeldingen / versies | installatiekopie | 32-bits geheel getal | Nummers en periodes. |
> | images | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Eindig met alfanumerieke of underscore. |
> | momentopnamen | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Eindig met alfanumerieke of underscore. |
> | virtueleMachines | resourcegroep | 1-15 (Windows)<br>1-64 (Linux)<br><br>Zie hieronder opmerking. | Kan het als geen gebruik maken:<br> `\/""[]:|<>+=;,?*@&`<br><br>Kan niet beginnen met underscore. Kan niet eindigen met een punt of koppelteken. |
> | virtualMachineScaleSets | resourcegroep | 1-15 (Windows)<br>1-64 (Linux)<br><br>Zie hieronder opmerking. | Kan het als geen gebruik maken:<br> `\/""[]:|<>+=;,?*@&`<br><br>Kan niet beginnen met underscore. Kan niet eindigen met een punt of koppelteken. |

> [!NOTE]
> Azure virtuele machines hebben twee verschillende namen: resource naam en host naam. Wanneer u een virtuele machine in de portal maakt, wordt voor beide namen dezelfde waarde gebruikt. De beperkingen in de vorige tabel zijn voor de hostnaam. De werkelijke resourcenaam mag niet langer zijn dan 64 tekens.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | containerGroepen | resourcegroep | 1-63 | Kleine letters, cijfers en koppeltekens.<br><br>Kan niet beginnen of eindigen met koppelteken. Opeenvolgende afbreekstreepjes zijn niet toegestaan. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Registers | Globale | 5-50 | Alfanumerieke waarden. |
> | registers / buildTaken | registry | 5-50 | Alfanumerieke waarden. |
> | registers / buildTaken/stappen | taken maken | 5-50 | Alfanumerieke waarden. |
> | registers / replicaties | registry | 5-50 | Alfanumerieke waarden. |
> | registers / scopeMaps | registry | 5-50 | Alfanumerieke, koppeltekens en underscores. |
> | registers / taken | registry | 5-50 | Alfanumerieke, koppeltekens en underscores. |
> | registers / tokens | registry | 5-50 | Alfanumerieke, koppeltekens en underscores. |
> | registers / webhooks | registry | 5-50 | Alfanumerieke waarden. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | managedClusters | resourcegroep | 1-63 | Alfanumerieke, underscores en koppeltekens.<br><br>Begin en einde met alfanumerieke. |
> | openShiftManagedClusters | resourcegroep | 1-30 | Alfanumerieke waarden. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | hubs | resourcegroep | 1-64 | Alfanumerieke waarden.<br><br>Begin met de brief.  |
> | hubs / autorisatieBeleid | hub | 1-50 | Alfanumerieke, underscores en perioden.<br><br>Begin en einde met alfanumerieke. |
> | hubs / connectoren | hub | 1-128 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / connectors/toewijzingen | connector | 1-128 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / interacties | hub | 1-128 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / kpi | hub | 1-512 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / links | hub | 1-512 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / voorspellingen | hub | 1-512 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / profielen | hub | 1-128 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / relatieLinks | hub | 1-512 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / relaties | hub | 1-512 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / rolOpdrachten | hub | 1-128 | Alfanumerieke en underscores.<br><br>Begin met de brief. |
> | hubs / weergaven | hub | 1-512 | Alfanumerieke en underscores.<br><br>Begin met de brief. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Verenigingen | resourcegroep | 1-180 | Kan het als geen gebruik maken:<br>`%&\\?/`<br><br>Kan niet eindigen met periode of ruimte. |
> | resourceProviders | resourcegroep | 3-64 | Kan het als geen gebruik maken:<br>`%&\\?/`<br><br>Kan niet eindigen met periode of ruimte. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Banen | resourcegroep | 3-24 | Alfanumerieke waarden, koppeltekens, underscores en perioden. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | werkruimten | resourcegroep | 3-30 | Alfanumerieke waarden, underscores en koppeltekens |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Fabrieken | Globale | 3-63 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |
> | fabrieken / datastromen | Fabriek | 1-260 | Kan het als geen gebruik maken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken / datasets | Fabriek | 1-260 | Kan het als geen gebruik maken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken / integratieRuntimes | Fabriek | 3-63 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |
> | fabrieken / linkedservices | Fabriek | 1-260 | Kan het als geen gebruik maken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken / pijpleidingen | Fabriek | 1-260 | Kan het als geen gebruik maken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken / triggers | Fabriek | 1-260 | Kan het als geen gebruik maken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |
> | fabrieken / triggers / rerunTriggers | Trigger | 1-260 | Kan het als geen gebruik maken:<br>`<>*#.%&:\\+?/`<br><br>Begin met alfanumeriek. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | Globale | 3-24 | Kleine letters en cijfers. |
> | accounts / computePolicies | account | 3-60 | Alfanumerieke, koppeltekens en underscores. |
> | accounts / dataLakeStoreAccounts | account | 3-24 | Kleine letters en cijfers. |
> | accounts / firewallRegels | account | 3-50 | Alfanumerieke, koppeltekens en underscores. |
> | accounts / storageAccounts | account | 3-60 | Alfanumerieke, koppeltekens en underscores. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | Globale | 3-24 | Kleine letters en cijfers. |
> | accounts / firewallRegels | account | 3-50 | Alfanumerieke, koppeltekens en underscores. |
> | accounts / virtualNetworkRules | account | 3-50 | Alfanumerieke, koppeltekens en underscores. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigratie

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | services | resourcegroep | 2-62 | Alfanumerieke waarden, koppeltekens, perioden en underscores.<br><br>Begin met alfanumeriek. |
> | diensten / projecten | service | 2-57 | Alfanumerieke waarden, koppeltekens, perioden en underscores.<br><br>Begin met alfanumeriek. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | Globale | 3-63 | Kleine letters, koppeltekens en cijfers.<br><br>Kan niet beginnen of eindigen met koppelteken. |
> | servers / databases | Servers | 1-63 | Alfanumerieke en koppeltekens. |
> | servers / firewallRegels | Servers | 1-128 | Alfanumerieke, koppeltekens en underscores. |
> | servers / virtualNetworkRules | Servers | 1-128 | Alfanumerieke en koppeltekens. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | Globale | 3-63 | Kleine letters, koppeltekens en cijfers.<br><br>Kan niet beginnen of eindigen met koppelteken. |
> | servers / databases | Servers | 1-63 | Alfanumerieke en koppeltekens. |
> | servers / firewallRegels | Servers | 1-128 | Alfanumerieke, koppeltekens en underscores. |
> | servers / virtualNetworkRules | Servers | 1-128 | Alfanumerieke en koppeltekens. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Servers | Globale | 3-63 | Kleine letters, koppeltekens en cijfers.<br><br>Kan niet beginnen of eindigen met koppelteken. |
> | servers / databases | Servers | 1-63 | Alfanumerieke en koppeltekens. |
> | servers / firewallRegels | Servers | 1-128 | Alfanumerieke, koppeltekens en underscores. |
> | servers / virtualNetworkRules | Servers | 1-128 | Alfanumerieke en koppeltekens. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | IotHubs | Globale | 3-50 | Alfanumerieke en koppeltekens.<br><br>Kan niet eindigen met koppelteken. |
> | IotHubs / certificaten | IoT-hub | 1-64 | Alfanumerieke waarden, koppeltekens, perioden en underscores. |
> | IotHubs / eventHubEndpunten / ConsumerGroups | eventHubEndpunten | 1-50 | Alfanumerieke waarden, koppeltekens, perioden en underscores. |
> | provisioningServices | resourcegroep | 3-64 | Alfanumerieke en koppeltekens.<br><br>Eindig met alfanumeriek. |
> | provisioningServices /certificaten | provisioningServices | 1-64 | Alfanumerieke waarden, koppeltekens, perioden en underscores. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Labs | resourcegroep | 1-50 | Alfanumerieke, underscores en koppeltekens. |
> | labs / aangepaste afbeeldingen | Lab | 1-80 | Alfanumerieke waarden, underscores, koppeltekens en haakjes. |
> | labs / formules | Lab | 1-80 | Alfanumerieke waarden, underscores, koppeltekens en haakjes. |
> | labs / virtualmachines | Lab | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. Het kunnen niet alle nummers zijn. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | databaseAccounts | Globale | 3-31 | Kleine letters, cijfers en koppeltekens.<br><br>Begin met kleine letter of nummer. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Domeinen | resourcegroep | 3-50 | Alfanumerieke en koppeltekens. |
> | domeinen / onderwerpen | domein | 3-50 | Alfanumerieke en koppeltekens. |
> | eventAbonnementen | resourcegroep | 3-64 | Alfanumerieke en koppeltekens. |
> | Onderwerpen | resourcegroep | 3-50 | Alfanumerieke en koppeltekens. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | resourcegroep | 6-50 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. Eindig met letter of nummer. |
> | Naamruimten | Globale | 6-50 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. Eindig met letter of nummer. |
> | naamruimten / Autorisatieregels | naamruimte | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en eindig met letter of nummer. |
> | naamruimten / rampRecoveryConfigs | naamruimte | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en eindig met letter of nummer. |
> | naamruimten / eventhubs | naamruimte | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en eindig met letter of nummer. |
> | naamruimten / eventhubs / autorisatieRegels | gebeurtenishub | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en eindig met letter of nummer. |
> | naamruimten / eventhubs / consumentengroepen | gebeurtenishub | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en eindig met letter of nummer. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | Globale | 3-59 | Alfanumerieke waarden en koppeltekens<br><br>Begin en eindig met letter of nummer. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Banen | resourcegroep | 2-64 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Onderdelen | resourcegroep | 1-260 | Kan het als geen gebruik maken:<br>`%&\?/` <br><br>Kan niet eindigen met ruimte of periode.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | IoTApps | Globale | 2-63 | Kleine letters, cijfers en koppeltekens.<br><br>Begin met kleine letter of nummer. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Kluizen | Globale | 3-24 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. Eindig met letter of cijfer. Kan geen opeenvolgende koppeltekens bevatten. |
> | kluizen / geheimen | Kluis | 1-127 | Alfanumerieke en koppeltekens. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | Globale | 4-22 | Kleine letters en cijfers.<br><br>Begin met de brief. |
> | /clusters / databases | cluster | 1-260 | Alfanumerieke waarden, koppeltekens, spaties en perioden. |
> | /clusters / databases / dataConnections | database | 1-40 | Alfanumerieke waarden, koppeltekens, spaties en perioden. |
> | /clusters / databases / eventhubverbindingen | database | 1-40 | Alfanumerieke waarden, koppeltekens, spaties en perioden. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | integratieAccounts | resourcegroep | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieAccounts / assemblages | integratieaccount | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieAccounts / batchConfiguraties | integratieaccount | 1-20 | Alfanumerieke waarden. |
> | integratieAccounts / certificaten | integratieaccount | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieAccounts / kaarten | integratieaccount | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieAccounts / partners | integratieaccount | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieAccounts / rosettanetprocessconfiguraties | integratieaccount | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieAccounts / schema's | integratieaccount | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieAccounts / sessies | integratieaccount | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |
> | integratieServiceOmgevingen | resourcegroep | 1-80 | Alfanumerieke waarden, koppeltekens, perioden en underscores. |
> | integrationServiceEnvironments / managedApis | integratieserviceomgeving | 1-80 | Alfanumerieke waarden, koppeltekens, perioden en underscores. |
> | Werkstromen | resourcegroep | 1-80 | Alfanumerieke waarden, koppeltekens, underscores, perioden en haakjes. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | inzetPlannen | resourcegroep | 1-260 | Kan het als geen gebruik maken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een ruimte. |
> | Webservices | resourcegroep | 1-260 | Kan het als geen gebruik maken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een ruimte. |
> | werkruimten | resourcegroep | 1-260 | Kan het als geen gebruik maken:<br>`<>*%&:?+/\\`<br><br>Kan niet eindigen met een ruimte. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | werkruimten | resourcegroep | 3-33 | Alfanumerieke en koppeltekens. |
> | werkruimten / berekent | werkruimte | 2-16 | Alfanumerieke en koppeltekens. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | userAssignedIdentities | resourcegroep | 3-128 | Alfanumerieke waarden, koppeltekens en underscores<br><br>Begin met letter of nummer. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | accounts | resourcegroep | 1-98 (voor de naam van de resourcegroep en de accountnaam) | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | mediaservices | resourcegroep | 3-24 | Kleine letters en cijfers. |
> | mediaservices / liveEvents | Mediaservice | 1-32 | Alfanumerieke en koppeltekens.<br><br>Begin met alfanumeriek. |
> | mediaservices / liveEvents / liveOutputs | Live-evenement | 1-256 | Alfanumerieke en koppeltekens.<br><br>Begin met alfanumeriek. |
> | mediaservices / streamingEndpoints | Mediaservice | 1-24 | Alfanumerieke en koppeltekens.<br><br>Begin met alfanumeriek. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | applicationGateways | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | applicationSecurityGroups | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | azureFirewalls | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Eindig met alfanumerieke of underscore. |
> | bastionHosts | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | Verbindingen | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | dnsZones | resourcegroep | 1-63 tekens<br><br>2 tot 34 etiketten<br><br>Elk label is een set tekens gescheiden door een punt. Contoso.com **heeft** bijvoorbeeld 2 labels. | Elk label kan alfanumerieke waarden, underscores en koppeltekens bevatten.<br><br>Elk label wordt gescheiden door een punt. |
> | expressRouteCircuits | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | FirewallBeleid | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | firewallbeleid / regelGroepen | firewallbeleid | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | frontDoors | Globale | 5-64 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |
> | loadBalancers | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | loadBalancers / inboundNatRules | load balancer | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | localNetworkGateways | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | netwerkInterfaces | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | networkSecurityGroups | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | networkSecurityGroups / securityRules | netwerkbeveiligingsgroep | 1-80 |  Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | networkWatchers | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | privateDnsZones | resourcegroep | 1-63 tekens<br><br>2 tot 34 etiketten<br><br>Elk label is een set tekens gescheiden door een punt. Contoso.com **heeft** bijvoorbeeld 2 labels. | Elk label kan alfanumerieke waarden, underscores en koppeltekens bevatten.<br><br>Elk label wordt gescheiden door een punt. |
> | privateDnsZones / virtualNetworkLinks | privé-DNS-zone | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | publicIPAddresses | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | publicIPPrefixes | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | routeFilters | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | routeFilters / routeFilterregels | routefilter | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | routeTabellen | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | routeTabellen / routes | routetabel | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | serviceEndpointBeleid | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | trafficmanagerprofielen | Globale | 1-63 | Alfanumerieke waarden, koppeltekens en perioden.<br><br>Begin en einde met alfanumerieke. |
> | virtualNetworkGateways | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | virtuelenetwerken | resourcegroep | 2-64 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | virtuele netwerken / subnetten | virtueel netwerk | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | virtualNetworks / virtualNetworkPeerings | virtueel netwerk | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | virtualWans virtualWans | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | vpnGateways | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | vpnGateways / vpnVerbindingen | VPN-gateway | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |
> | vpnSites | resourcegroep | 1-80 | Alfanumerieke waarden, underscores, perioden en koppeltekens.<br><br>Begin met alfanumeriek. Einde alfanumeriek of onderstrepen. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Naamruimten | Globale | 6-50 | Alfanumerieke waarden en koppeltekens<br><br>Begin en einde met alfanumerieke. |
> | naamruimten / Autorisatieregels | naamruimte | 1-256 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Alfanumeriek starten. |
> | naamruimten / notificationHubs | naamruimte | 1-260 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Alfanumeriek starten. |
> | naamruimten / notificationHubs / Autorisatieregels | meldingshub | 1-256 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Alfanumeriek starten. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | resourcegroep | 4-63 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |
> | werkruimten | resourcegroep | 4-63 | Alfanumerieke en koppeltekens.<br><br>Begin en einde met alfanumerieke. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | dashboards | resourcegroep | 3-160 | Alfanumerieke en koppeltekens.<br><br>Als u beperkte tekens wilt gebruiken, voegt u een tag met de naam **verborgen titel** toe met de dashboardnaam die u wilt gebruiken. De portal geeft die naam weer wanneer het dashboard wordt weergegeven. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | werkruimtecollecties | regio | 3-63 | Alfanumerieke en koppeltekens.<br><br>Ik kan niet beginnen met een koppelteken. Kan geen opeenvolgende koppeltekens gebruiken. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Capaciteiten | regio | 3-63 | Letters of cijfers van kleine letters<br><br>Begin met kleine letter. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Kluizen | resourcegroep | 2-50 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. |
> | kluizen / back-upbeleid | kluis | 3-150 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. Kan niet eindigen met koppelteken. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Naamruimten | Globale | 6-50 | Alfanumerieke en koppeltekens.<br><br>Begin met een letter. Eindig met een letter of nummer. |
> | naamruimten / Autorisatieregels | naamruimte | 1-50 |  Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alfanumerieke. |
> | naamruimten / HybridConnections | naamruimte | 1-260 | Alfanumerieke waarden, perioden, koppeltekens, underscores en slashes.<br><br>Begin en einde met alfanumerieke. |
> | naamruimten / HybridConnections/autorisatieRegels | hybride verbinding | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alfanumerieke. |
> | naamruimten / WcfRelays | naamruimte | 1-260 | Alfanumerieke waarden, perioden, koppeltekens, underscores en slashes.<br><br>Begin en einde met alfanumerieke. |
> | naamruimten / WcfRelays / autorisatieRegels | Wcf estafette | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alfanumerieke. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Implementaties | resourcegroep | 1-64 | Alfanumerieke waarden, underscores, haakjes, koppeltekens en perioden. |
> | resourcegroepen | abonnement | 1-90 | Alfanumerieke waarden, underscores, haakjes, koppeltekens, perioden en unicode-tekens die overeenkomen met de [regex-documentatie](/rest/api/resources/resourcegroups/createorupdate).<br><br>Kan niet eindigen met periode. |
> | tagNamen | resource | 1-512 | Kan het als geen gebruik maken:<br>`<>%&\?/` |
> | tagNamen / tagWaarden | tagnaam | 1-256 | Alle personages. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Naamruimten | Globale | 6-50 | Alfanumerieke en koppeltekens.<br><br>Begin met een letter. Eindig met een letter of nummer.<br><br>Zie [Naamruimte maken](/rest/api/servicebus/create-namespace)voor meer informatie . |
> | naamruimten / Autorisatieregels | naamruimte | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alphnumeriek. |
> | naamruimten / rampRecoveryConfigs | Globale | 6-50 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. Eindig met alfanumeriek. |
> | naamruimten / migratieConfiguraties | naamruimte |  | Moet altijd **$default**zijn. |
> | naamruimten / wachtrijen | naamruimte | 1-260 | Alfanumerieke waarden, perioden, koppeltekens, underscores en slashes.<br><br>Begin en einde met alfanumerieke. |
> | naamruimten / wachtrijen / autorisatieRegels | wachtrij | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alphnumeriek. |
> | naamruimten / onderwerpen | naamruimte | 1-260 | Alfanumerieke waarden, perioden, koppeltekens, underscores en slashes.<br><br>Begin en einde met alfanumerieke. |
> | naamruimten / onderwerpen / autorisatieRegels | onderwerp | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alphnumeriek. |
> | naamruimten / onderwerpen / abonnementen | onderwerp | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alphnumeriek. |
> | naamruimten / onderwerpen / abonnementen / regels | abonnement | 1-50 | Alfanumerieke waarden, perioden, koppeltekens en underscores.<br><br>Begin en einde met alphnumeriek. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Clusters | regio | 4-23 | Kleine letters, cijfers en koppeltekens.<br><br>Begin met kleine letter. Eindig met kleine letter of nummer. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | signaalR | Globale | 3-63 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. Eindig met letter of nummer.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | managedInstances | Globale | 1-63 | Kleine letters, cijfers en koppeltekens.<br><br>Kan niet beginnen of eindigen met koppelteken. |
> | Servers | Globale | 1-63 | Kleine letters, cijfers en koppeltekens.<br><br>Kan niet beginnen of eindigen met koppelteken. |
> | servers / beheerders | server |  | Moet `ActiveDirectory`wel. |
> | servers / databases | server | 1-128 | Kan het als geen gebruik maken:<br>`<>*%&:\/?`<br><br>Kan niet eindigen met periode of ruimte. |
> | servers / databases / syncGroups | database | 1-150 | Alfanumerieke, koppeltekens en underscores. |
> | servers / elasticPools | server | 1-128 | Kan het als geen gebruik maken:<br>`<>*%&:\/?`<br><br>Kan niet eindigen met periode of ruimte. |
> | servers / failoverGroepen | Globale | 1-63 | Kleine letters, cijfers en koppeltekens.<br><br>Kan niet beginnen of eindigen met koppelteken. |
> | servers / firewallRegels | server | 1-128 | Kan het als geen gebruik maken:<br>`<>*%&:;\/?`<br><br>Kan niet eindigen met periode. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | storageAccounts | Globale | 3-24 | Kleine letters en cijfers. |
> | storageAccounts / blobServices | opslagaccount |  | Moet `default`wel. |
> | storageAccounts / blobServices / containers | opslagaccount | 3-63 | Kleine letters, cijfers en koppeltekens.<br><br>Begin met kleine letter of nummer. Kan geen opeenvolgende koppeltekens gebruiken. |
> | storageAccounts / fileServices | opslagaccount |  | Moet `default`wel. |
> | storageAccounts / fileServices / aandelen | opslagaccount | 3-63 | Kleine letters, cijfers en koppeltekens.<br><br>Kan niet beginnen of eindigen met koppelteken. Kan geen opeenvolgende koppeltekens gebruiken. |
> | storageAccounts / beheerBeleid | opslagaccount |  | Moet `default`wel. |
> | blob | container | 1-1024 | URL-tekens, hoofdletters |
> | wachtrij | opslagaccount | 3-63 | Kleine letters, cijfers en koppeltekens.<br><br>Kan niet beginnen of eindigen met koppelteken. Kan geen opeenvolgende koppeltekens gebruiken. |
> | tabel | opslagaccount | 3-63 | Alfanumerieke waarden.<br><br>Begin met de brief. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | storageSyncServices | resourcegroep | 1-260 | Alfanumerieke waarden, spaties, perioden, koppeltekens en underscores.<br><br>Kan niet eindigen met periode of ruimte. |
> | storageSyncServices / syncGroups | opslagsynchronisatieservice | 1-260 | Alfanumerieke waarden, spaties, perioden, koppeltekens en underscores.<br><br>Kan niet eindigen met periode of ruimte. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Managers | resourcegroep | 2-50 | Alfanumerieke en koppeltekens.<br><br>Begin met de brief. Eindig met alfanumeriek. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | streamingjobs | resourcegroep | 3-63 | Alfanumerieke, koppeltekens en underscores. |
> | streamingjobs / functies | streamingtaak | 3-63 | Alfanumerieke, koppeltekens en underscores. |
> | streamingjobs / ingangen | streamingtaak | 3-63 | Alfanumerieke, koppeltekens en underscores. |
> | streamingjobs / uitgangen | streamingtaak | 3-63 | Alfanumerieke, koppeltekens en underscores. |
> | streamingjobs / transformaties | streamingtaak | 3-63 | Alfanumerieke, koppeltekens en underscores. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | Omgevingen | resourcegroep | 1-90 | Kan het als geen gebruik maken:<br>`'<>%&:\?/#` |
> | omgevingen / toegangsbeleid | omgeving | 1-90 | Kan het als geen gebruik maken:<br> `'<>%&:\?/#` |
> | omgevingen / eventBronnen | omgeving | 1-90 | Kan het als geen gebruik maken:<br>`'<>%&:\?/#` |
> | omgevingen / referenceDataSets | omgeving | 3-63 | Alfanumerieke waarden |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entiteit | Bereik | Lengte | Geldige tekens |
> | --- | --- | --- | --- |
> | serverfarms | resourcegroep | 1-40 | Alfanumerieke en koppeltekens. |
> | sites | Globale | 2-60 | Bevat alfanumerieke en koppeltekens.<br><br>Kan niet beginnen of eindigen met koppelteken. |
> | sites / slots | site | 2-59 | Alfanumerieke en koppeltekens. |

## <a name="next-steps"></a>Volgende stappen

Zie [Afspraken maken: Aanbevolen naamgeving en tags voor](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)het benoemen van resources.
