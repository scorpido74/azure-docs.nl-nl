---
title: Cloud Services Role config XPath cheat sheet | Microsoft Documenten
description: De verschillende XPath-instellingen die u gebruiken in de functieconfig voor cloudservices om instellingen als omgevingsvariabele bloot te leggen.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 380b0be4e4e4b19d16cb611b0b472294339f2199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386082"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Functieconfiguratie-instellingen blootstellen als een omgevingsvariabele met XPath
In het definitiebestand voor de definitie van de cloudserviceservice u de configuratiewaarden voor runtime weergeven als omgevingsvariabelen. De volgende XPath-waarden worden ondersteund (die overeenkomen met API-waarden).

Deze XPath-waarden zijn ook beschikbaar via de [Microsoft.WindowsAzure.ServiceRuntime-bibliotheek.](/previous-versions/azure/reference/ee773173(v=azure.100)) 

## <a name="app-running-in-emulator"></a>App draait in emulator
Geeft aan dat de app wordt uitgevoerd in de emulator.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Code |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Implementatie-id
Hiermee haalt u de implementatie-id voor de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/Deployment/@id" |
| Code |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Rol-id
Hiermee haalt u de huidige rol-id voor de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Code |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Domein bijwerken
Hiermee haalt u het updatedomein van de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Code |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Foutdomein
Hiermee haalt u het foutdomein van de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Code |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Rolnaam
Hiermee haalt u de rolnaam van de instanties op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Code |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Config-instelling
Hiermee haalt u de waarde op van de opgegeven configuratie-instelling.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Code |var-instelling = RoleEnvironment.GetConfigurationSettingValue("Instelling1"); |

## <a name="local-storage-path"></a>Lokaal opslagpad
Hiermee haalt u het lokale opslagpad voor de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Code |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). Wortelpad; |

## <a name="local-storage-size"></a>Lokale opslaggrootte
Hiermee haalt u de grootte van de lokale opslag voor de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Code |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). maximumSizeInmegabytes; |

## <a name="endpoint-protocol"></a>Eindpuntprotocol
Hiermee haalt u het eindpuntprotocol voor de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Code |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. protocol; |

## <a name="endpoint-ip"></a>Eindpunt-IP
Hier krijgt u het opgegeven IP-adres van het eindpunt op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Code |var-adres = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Adres |

## <a name="endpoint-port"></a>Eindpuntpoort
Hiermee haalt u de eindpuntpoort voor de instantie op.

| Type | Voorbeeld |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Code |var-poort = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Voorbeeld
Hier volgt een voorbeeld van een werkrol waarmee een `TestIsEmulated` opstarttaak wordt gemaakt met een omgevingsvariabele met de naam ingesteld op de [ @emulated xpath-waarde.](#app-running-in-emulator) 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [bestand ServiceConfiguration.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)

Maak een [ServicePackage.cspkg-pakket.](cloud-services-model-and-package.md#servicepackagecspkg)

Extern [bureaublad inschakelen](cloud-services-role-enable-remote-desktop-new-portal.md) voor een rol.




