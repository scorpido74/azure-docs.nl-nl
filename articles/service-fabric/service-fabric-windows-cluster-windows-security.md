---
title: Een cluster beveiligen dat op Windows wordt uitgevoerd met Windows-beveiliging
description: Meer informatie over het configureren van beveiliging tussen knooppunt en knooppunt op een zelfstandig cluster dat op Windows wordt uitgevoerd met Windows-beveiliging.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774451"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Een zelfstandig cluster beveiligen op Windows met Windows-beveiliging
Om ongeautoriseerde toegang tot een Service Fabric-cluster te voorkomen, moet u het cluster beveiligen. Beveiliging is vooral belangrijk wanneer het cluster productieworkloads uitvoert. In dit artikel wordt beschreven hoe u beveiliging tussen knooppunt en client-to-node configureert met Windows-beveiliging in het bestand *ClusterConfig.JSON.*  Het proces komt overeen met de beveiligingsstap configureren van [Een zelfstandig cluster maken dat op Windows wordt uitgevoerd.](service-fabric-cluster-creation-for-windows-server.md) Zie [Clusterbeveiligingsscenario's](service-fabric-cluster-security.md)voor meer informatie over hoe Service Fabric Windows-beveiliging gebruikt.

> [!NOTE]
> U moet de selectie van knooppuntbeveiliging zorgvuldig overwegen, omdat er geen clusterupgrade is van de ene beveiligingskeuze naar de andere. Als u de beveiligingsselectie wilt wijzigen, moet u het volledige cluster opnieuw opbouwen.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Windows-beveiliging configureren met gMSA  
Het voorbeeld *clusterconfig.gMSA.Windows.MultiMachine.JSON-configuratiebestand* is gedownload met het [Microsoft.Azure.ServiceFabric.WindowsServer.\< versie>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) standalone clusterpakket bevat een sjabloon voor het configureren van Windows-beveiliging met [group managed service account (gMSA):](https://technet.microsoft.com/library/hh831782.aspx)  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Configuratie-instelling** | **Beschrijving** |
| --- | --- |
| ClusterCredentialType |Stel in op *Windows* om Windows-beveiliging in te schakelen voor knooppuntcommunicatie.  | 
| ServerCredentialType |Stel in op *Windows* om Windows-beveiliging in te schakelen voor communicatie met clientknooppunten. |
| WindowsIdentiteiten |Bevat de cluster- en clientidentiteiten. |
| ClustergMSAIdentity |Hiermee configureert u beveiliging van knooppunt tot knooppunt. Een groepsbeheeraccount. |
| ClusterSPN |Geregistreerde SPN voor gMSA-account|
| ClientIdentiteiten |Hiermee configureert u beveiliging van client-naar-node. Een scala aan clientgebruikersaccounts. |
| Identiteit |Voeg de domeingebruiker, domein\gebruikersnaam, toe voor de clientidentiteit. |
| IsAdmin (IsAdmin) |Stel in dat de domeingebruiker toegang heeft tot de client van beheerders of false voor gebruikersclienttoegang. |

> [!NOTE]
> ClustergMSAIdentity-waarde moet inmysfgmsa@mydomainformaat " "zijn.

[Beveiliging van knooppunt tot knooppunt](service-fabric-cluster-security.md#node-to-node-security) wordt geconfigureerd door **ClustergMSAIdentity** in te stellen wanneer de servicestructuur onder gMSA moet worden uitgevoerd. Om vertrouwensrelaties tussen knooppunten op te bouwen, moeten ze van elkaar op de hoogte worden gebracht. Dit kan op twee verschillende manieren: geef het groepsbeheerserviceaccount op dat alle knooppunten in het cluster bevat of Geef de domeinmachinegroep op die alle knooppunten in het cluster bevat. We raden ten zeerste aan om de [groepsbeheerserviceaccount (gMSA)-benadering te](https://technet.microsoft.com/library/hh831782.aspx) gebruiken, met name voor grotere clusters (meer dan 10 knooppunten) of voor clusters die waarschijnlijk zullen groeien of krimpen.  
Deze benadering vereist niet dat er een domeingroep wordt gemaakt waarvoor clusterbeheerders toegangsrechten hebben gekregen om leden toe te voegen en te verwijderen. Deze accounts zijn ook handig voor automatisch wachtwoordbeheer. Zie [Aan de slag met groepsbeheerserviceaccounts](https://technet.microsoft.com/library/jj128431.aspx)voor meer informatie .  
 
[Beveiliging client-to-node](service-fabric-cluster-security.md#client-to-node-security) is geconfigureerd met **clientidentiteiten.** Om vertrouwen te creëren tussen een client en het cluster, moet u het cluster configureren om te weten welke clientidentiteiten het kan vertrouwen. Dit kan op twee verschillende manieren: geef de domeingroepgebruikers op die verbinding kunnen maken of het domeinknooppunt kunnen opgeven dat gebruikers kunnen verbinden die verbinding kunnen maken. Service Fabric ondersteunt twee verschillende typen toegangscontrole voor clients die zijn verbonden met een Service Fabric-cluster: beheerder en gebruiker. Toegangsbeheer biedt de clusterbeheerder de mogelijkheid om de toegang tot bepaalde typen clusterbewerkingen voor verschillende groepen gebruikers te beperken, waardoor het cluster veiliger wordt.  Beheerders hebben volledige toegang tot beheermogelijkheden (inclusief lees-/schrijfmogelijkheden). Gebruikers hebben standaard alleen leestoegang tot beheermogelijkheden (bijvoorbeeld querymogelijkheden) en de mogelijkheid om toepassingen en services op te lossen. Zie Op rollen gebaseerd [toegangscontrole voor Service Fabric-clients voor](service-fabric-cluster-security-roles.md)meer informatie over toegangsbesturingselementen.  
 
In de volgende **voorbeeldbeveiligingssectie** configureert u Windows-beveiliging met behulp van gMSA en geeft u op dat de machines in *ServiceFabric.clusterA.contoso.com* gMSA deel uitmaken van het cluster en dat *CONTOSO\usera* beheerdersclienttoegang heeft:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Windows-beveiliging configureren met een machinegroep  
Dit model wordt afgeschaft. De aanbeveling is om gMSA te gebruiken zoals hierboven beschreven. Het voorbeeld *clusterconfig.Windows.MultiMachine.JSON-configuratiebestand* is gedownload met het [Microsoft.Azure.ServiceFabric.WindowsServer.\< versie>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) standalone clusterpakket bevat een sjabloon voor het configureren van Windows-beveiliging.  Windows-beveiliging is geconfigureerd in de sectie **Eigenschappen:** 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Configuratie-instelling** | **Beschrijving** |
| --- | --- |
| ClusterCredentialType |Stel in op *Windows* om Windows-beveiliging in te schakelen voor knooppuntcommunicatie.  |
| ServerCredentialType |Stel in op *Windows* om Windows-beveiliging in te schakelen voor communicatie met clientknooppunten. |
| WindowsIdentiteiten |Bevat de cluster- en clientidentiteiten. |
| ClusterIdentiteit |Gebruik een naam van de machinegroep, domein\machinegroep, om beveiliging van knooppunt tot knooppunt te configureren. |
| ClientIdentiteiten |Hiermee configureert u beveiliging van client-naar-node. Een scala aan clientgebruikersaccounts. |  
| Identiteit |Voeg de domeingebruiker, domein\gebruikersnaam, toe voor de clientidentiteit. |  
| IsAdmin (IsAdmin) |Stel in dat de domeingebruiker toegang heeft tot de client van beheerders of false voor gebruikersclienttoegang. |  

[Beveiliging van knooppunt tot knooppunt](service-fabric-cluster-security.md#node-to-node-security) wordt geconfigureerd door **clusteridentiteit** in te stellen als u een machinegroep binnen een Active Directory-domein wilt gebruiken. Zie [Een machinegroep maken in Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx)voor meer informatie.

[Client-to-node beveiliging](service-fabric-cluster-security.md#client-to-node-security) wordt geconfigureerd met behulp van **ClientIdentities**. Als u de vertrouwensrelatie tussen een client en het cluster wilt herstellen, moet u het cluster configureren om de clientidentiteiten te kennen die het cluster kan vertrouwen. U vertrouwen op twee verschillende manieren opbouwen:

- Geef de domeingroepgebruikers op die verbinding kunnen maken.
- Geef het domeinknooppunt op dat gebruikers kunnen verbinden.

Service Fabric ondersteunt twee verschillende typen toegangscontrole voor clients die zijn verbonden met een Service Fabric-cluster: beheerder en gebruiker. Met toegangsbeheer kan de clusterbeheerder de toegang tot bepaalde typen clusterbewerkingen voor verschillende groepen gebruikers beperken, waardoor het cluster veiliger wordt.  Beheerders hebben volledige toegang tot beheermogelijkheden (inclusief lees-/schrijfmogelijkheden). Gebruikers hebben standaard alleen leestoegang tot beheermogelijkheden (bijvoorbeeld querymogelijkheden) en de mogelijkheid om toepassingen en services op te lossen.  

In de volgende **voorbeeldbeveiligingssectie** configureert u Windows-beveiliging, geeft u aan dat de machines in *ServiceFabric/clusterA.contoso.com* deel uitmaken van het cluster en geeft u op dat *CONTOSO\usera* beheerdersclienttoegang heeft:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric mag niet worden geïmplementeerd op een domeincontroller. Zorg ervoor dat ClusterConfig.json niet het IP-adres van de domeincontroller bevat wanneer u een machinegroep of groepsManaged Service Account (gMSA) gebruikt.
>
>

## <a name="next-steps"></a>Volgende stappen
Nadat u Windows-beveiliging hebt geconfigureerd in het *clusterbestandClusterConfig.JSON,* hervat u het proces voor het maken van een cluster in [Een zelfstandig cluster maken dat op Windows wordt uitgevoerd.](service-fabric-cluster-creation-for-windows-server.md)

Zie [Clusterbeveiligingsscenario's](service-fabric-cluster-security.md)voor meer informatie over de beveiliging van knooppunten, beveiliging van client-naar-node en op rollen gebaseerde toegangsbeheer.

Zie [Verbinding maken met een beveiligd cluster](service-fabric-connect-to-secure-cluster.md) voor voorbeelden van verbinding maken met PowerShell of FabricClient.
