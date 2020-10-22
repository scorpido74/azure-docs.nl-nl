---
title: Een cluster bijwerken voor het gebruik van de algemene naam van het certificaat
description: Meer informatie over het converteren van een Service Fabric cluster certificaat van declaraties op basis van vinger afdruk naar algemene namen.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368057"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Cluster certificaten van declaraties op basis van vinger afdruk naar algemene namen converteren
De hand tekening van een certificaat (colloquially ' vinger afdruk ') is uniek, wat betekent dat een cluster certificaat dat is gedeclareerd door de vinger afdruk, verwijst naar een specifiek exemplaar van een certificaat. Op die manier wordt het overschakelen van certificaten en het beheer, in algemeen/moeilijk en expliciet: elke wijziging vereist het organiseren van upgrades van het cluster en de onderliggende computing hosts. Het converteren van de certificaat declaraties van een Service Fabric-cluster van een vinger afdruk op basis van-declaraties op basis van de algemene naam van het certificaat vereenvoudigen het beheer van een certificaat dat niet langer vereist is voor een cluster upgrade. In dit artikel wordt beschreven hoe u een bestaand cluster converteert naar algemene declaraties op basis van namen zonder uitval tijd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Door de certificerings instantie (CA) ondertekende certificaten verplaatsen
De beveiliging van een cluster waarvan het certificaat door de vinger afdruk wordt gedeclareerd, is niet mogelijk, of het berekenen van een certificaat met dezelfde hand tekening als een ander is niet haalbaar. In dit geval is de herkomst van het certificaat minder belang rijk en zijn zelfondertekende certificaten voldoende. Daarentegen wordt de beveiliging van een cluster met certificaten die zijn gedeclareerd door de algemene naam stromen van de open bare-sleutel infrastructuur service (PKI) die het certificaat heeft uitgegeven, inclusief aspecten zoals hun certificerings procedures, of hun operationele beveiliging wordt gecontroleerd en vele andere. Daarom is de keuze van een PKI belang rijk, Intimate kennis van de verleners (certificerings instantie of CA) is vereist, en zelfondertekende certificaten zijn in feite worthless. Een certificaat dat is gedeclareerd met common name (CN) wordt doorgaans als geldig beschouwd als de keten kan worden gebouwd, het onderwerp heeft het verwachte CN-element en de verlener (direct of hoger in de keten) wordt vertrouwd door de agent die de validatie uitvoert. Service Fabric ondersteunt het declareren van certificaten door CN met impliciete verlener (de keten moet eindigen op een vertrouwens anker) of met verleners die zijn gedeclareerd door de vinger afdruk ("verlener vastmaken"); Raadpleeg dit  [artikel](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) voor meer informatie. Voor het converteren van een cluster met een zelfondertekend certificaat dat is gedeclareerd door de vinger afdruk voor de algemene naam, het doel, het CA-ondertekende certificaat moet eerst worden geïntroduceerd in het cluster via de vinger afdruk. de conversie van TP naar CN kan alleen worden uitgevoerd.

Voor test doeleinden kan een zelfondertekend certificaat worden gedeclareerd door CN, de uitgever vastmaken aan een eigen vinger afdruk; uit veiligheids oogpunt is dit bijna gelijk aan het declareren van hetzelfde certificaat door TP. Houd er echter rekening mee dat bij een geslaagde conversie van dit type geen geslaagde conversie van TP naar CN met een CA-ondertekend certificaat wordt gegarandeerd. Daarom is het raadzaam om de conversie te testen met een goed CA-ondertekend certificaat (gratis opties bestaan).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Upload het certificaat en installeer het in de schaalset
In azure bestaat het aanbevolen mechanisme voor het verkrijgen en inrichten van certificaten uit de Azure Key Vault-service en het bijbehorende hulp programma. Een certificaat dat overeenkomt met de certificaat declaratie van het cluster moet worden ingericht op elk knoop punt van de schaal sets voor virtuele machines waaruit het cluster bestaat. Raadpleeg [geheimen op de virtuele-machine schaal sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) voor meer informatie. Het is belang rijk dat zowel het huidige als het doel cluster certificaat is geïnstalleerd op de virtuele machines van elk knooppunt type van het cluster voordat u wijzigingen aanbrengt in de certificaat verklaringen van het cluster. Het traject van certificaat uitgifte tot inrichting op een service Fabric-knoop punt wordt [hier](cluster-security-certificate-management.md#the-journey-of-a-certificate)uitgebreid besproken.

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Cluster naar een optimale start status brengen
Het omzetten van een certificaat declaratie van een vinger afdruk op basis van gevolgen voor algemene namen beide:

- Hoe elk knoop punt in het cluster de referenties voor andere knoop punten zoekt en presenteert
- Hoe elk knoop punt de referenties van het bijbehorende exemplaar valideert bij het tot stand brengen van een beveiligde verbinding  

Controleer de [presentatie-en validatie regels voor beide configuraties](cluster-security-certificates.md#certificate-configuration-rules) voordat u doorgaat. De belangrijkste overweging bij het uitvoeren van een vinger afdruk-naar-algemene naam conversie is dat er een upgrade is uitgevoerd naar knoop punten die niet zijn bijgewerkt (dat wil zeggen, knoop punten die tot verschillende upgrade domeinen behoren), op elk gewenst moment tijdens de upgrade een geslaagde wederzijdse verificatie kunnen uitvoeren. De aanbevolen manier om dit te bereiken is het declareren van het doel/doel certificaat door de vinger afdruk in een eerste upgrade en de overgang naar de algemene naam in een volgende stap te volt ooien. Als het cluster al een aanbevolen start status heeft, kan deze sectie worden overgeslagen.

Er zijn meerdere geldige start statussen voor een conversie. de invariantie is dat het cluster al het doel certificaat gebruikt (aangegeven door de vinger afdruk) aan het begin van de upgrade naar algemene naam. We overwegen `GoalCert` , `OldCert1` ,, `OldCert2` :

#### <a name="valid-starting-states"></a>Geldige start statussen
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, waarbij `GoalCert` een latere `NotAfter` datum is dan die van `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, waarbij `GoalCert` een latere `NotAfter` datum is dan die van `OldCert1`

Als uw cluster zich niet in een van de geldige statussen bevindt die hierboven worden beschreven, raadpleegt u de bijlage over het bereiken van deze status aan het einde van dit artikel.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Selecteer het gewenste certificaat validatie schema op basis van een algemene naam
Zoals eerder beschreven, biedt Service Fabric ondersteuning voor het declareren van certificaten door CN met een impliciet vertrouwens anker of het expliciet vastmaken van de uitgevers vingerafdrukken. Raadpleeg [dit artikel](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) voor meer informatie en zorg ervoor dat u een goed inzicht hebt in de verschillen en wat de gevolgen zijn van het kiezen van een van beide mechanismen. Met syntactisch wordt dit verschil/keuze bepaald door de waarde van de `certificateIssuerThumbprintList` para meter: empty houdt in dat een vertrouwde basis certificerings instantie (vertrouwens anker) wordt vertrouwd, terwijl een set met vinger afdrukken de toegestane directe verleners van cluster certificaten beperkt.

   > [!NOTE]
   > In het veld ' certificateIssuerThumbprint ' kunnen de verwachte directe verleners van certificaten worden opgegeven die zijn gedefinieerd door de algemene naam van het onderwerp. Acceptabele waarden zijn een of meer door komma's gescheiden SHA1-vinger afdrukken. Houd er rekening mee dat dit een versterking van de certificaat validatie is. als er geen verleners zijn opgegeven of de lijst leeg is, wordt het certificaat geaccepteerd voor verificatie als de keten kan worden samengesteld en wordt deze in een hoofd niveau die wordt vertrouwd door de validator, beëindigd. Als er een of meer verleners vingerafdrukten zijn opgegeven, wordt het certificaat geaccepteerd als de vinger afdruk van de directe verlener, zoals geëxtraheerd uit de keten, overeenkomt met een van de waarden die zijn opgegeven in dit veld, ongeacht of de basis wordt vertrouwd of niet. Houd er rekening mee dat een PKI mogelijk verschillende certificerings instanties (' emittenten ') gebruikt voor het ondertekenen van certificaten met een bepaald onderwerp. Daarom is het belang rijk om alle verwachte uitgevers vingerafdrukken voor dat onderwerp op te geven. Met andere woorden, het vernieuwen van een certificaat is niet gegarandeerd door dezelfde verlener als het certificaat dat wordt vernieuwd.
   >
   > Het opgeven van de uitgever wordt beschouwd als een best practice; Als u weglaat, blijft het werken voor certificaten die zijn gekoppeld aan een vertrouwde basis. dit gedrag heeft beperkingen en kan in de nabije toekomst worden gespreid. Clusters die zijn geïmplementeerd in Azure en die zijn beveiligd met x509-certificaten die zijn uitgegeven door een persoonlijke PKI en die zijn gedeclareerd door het onderwerp, kunnen mogelijk niet worden gevalideerd door de Azure Service Fabric-service (voor communicatie tussen services), als het certificaat beleid van de PKI niet detecteerbaar, beschikbaar en toegankelijk is. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>De ARM-sjabloon (Azure Resource Management) van het cluster bijwerken en implementeren
Het is raadzaam om Azure Service Fabric-clusters te beheren met ARM-sjablonen. een alternatief, ook met behulp van JSON-artefacten, is de [Azure resource Explorer (preview)](https://resources.azure.com). Er is op dit moment geen equivalente ervaring beschikbaar in de Azure Portal. Als de oorspronkelijke sjabloon die overeenkomt met een bestaand cluster niet beschikbaar is, kunt u in de Azure Portal een gelijkwaardige sjabloon verkrijgen door te navigeren naar de resource groep met het cluster, **sjabloon exporteren** te selecteren in het menu links van **automatisering** en vervolgens de gewenste resources te selecteren. ten minste de virtuele-machine schaalset en cluster bronnen moeten worden geëxporteerd. De gegenereerde sjabloon kan ook worden gedownload. Opmerking Deze sjabloon kan wijzigingen vereisen voordat het volledig kan worden geïmplementeerd en komt mogelijk niet overeen met de oorspronkelijke status van de cluster resource. Dit is een reflectie van de huidige staat van de bron.

De benodigde wijzigingen zijn als volgt:
    - de definitie van de extensie van het Service Fabric knooppunt (onder de resource van de virtuele machine) bijwerken. Als in het cluster meerdere knooppunt typen worden gedefinieerd, moet u de definitie van elke overeenkomende schaalset voor virtuele machines bijwerken
    - de cluster bron definitie bijwerken

Hieronder vindt u meer gedetailleerde voor beelden.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>De resource (s) voor de schaalset van de virtuele machine bijwerken
Van
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Tot
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="updating-the-cluster-resource"></a>De cluster bron bijwerken
Voeg in de resource **micro soft. ServiceFabric/clusters** een eigenschap **certificateCommonNames** met de instelling **commonNames** toe en verwijder de **certificaat** eigenschap helemaal (alle instellingen):

Van
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Tot
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Zie [een service Fabric cluster implementeren dat de algemene naam van een certificaat gebruikt in plaats van een vinger afdruk](./service-fabric-create-cluster-using-cert-cn.md) voor meer informatie.

## <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Implementeer de bijgewerkte sjabloon opnieuw nadat u de wijzigingen hebt aangebracht.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Bijlage: een geldige start status voor het converteren van een cluster naar CN-gebaseerde certificaat declaraties

| Begin status | Upgrade 1 | Upgrade 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` en `GoalCert` heeft een latere `NotAfter` datum dan `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` en `OldCert1` heeft een latere `NotAfter` datum dan `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, waarbij `OldCert1` een latere `NotAfter` datum is dan `GoalCert` | Upgrade uitvoeren naar `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, waarbij `OldCert1` een latere `NotAfter` datum is dan `GoalCert` | Upgrade uitvoeren naar `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Verwijder een van `OldCert1` of `OldCert2` om de status te verkrijgen `Thumbprint: OldCertx, ThumbprintSecondary: None` | Door gaan vanaf de nieuwe begin status |

Zie [dit document](service-fabric-cluster-security-update-certs-azure.md)voor instructies over het uitvoeren van een van deze upgrades.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [cluster beveiliging](service-fabric-cluster-security.md).
* Meer informatie over het [overschakelen van een cluster certificaat op basis van een algemene naam](service-fabric-cluster-rollover-cert-cn.md)
* Meer informatie over het [configureren van een cluster voor automatische rollover](cluster-security-certificate-management.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
