---
title: Certificaten configureren voor toepassingen op Linux
description: Certificaten voor uw app configureren met de runtime van Service Fabric op een Linux-cluster
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 802e76614f51e1f6479a311e61a49d83b8125546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282573"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificaten en beveiliging op Linux-clusters

In dit artikel vindt u informatie over het configureren van X.509-certificaten op Linux-clusters.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Locatie en indeling van X.509-certificaten op Linux-knooppunten

Service Fabric verwacht over het algemeen dat X.509-certificaten aanwezig zullen zijn in de */var/lib/sfcerts-map* op Linux-clusterknooppunten. Dit geldt voor clustercertificaten, clientcertificaten, enz. In sommige gevallen u een andere locatie opgeven dan de *map var/lib/sfcerts* voor certificaten. Met Betrouwbare services die zijn gebouwd met de Service Fabric Java SDK, u bijvoorbeeld een andere locatie opgeven via het configuratiepakket (Settings.xml) voor sommige toepassingsspecifieke certificaten. Zie [Certificaten waarnaar wordt verwezen in het configuratiepakket (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml)voor meer informatie.

Voor Linux-clusters verwacht Service Fabric dat certificaten aanwezig zijn als een .pem-bestand dat zowel de certificaat- als privésleutel bevat, ofwel als een .crt-bestand dat het certificaat bevat en een .key-bestand dat de privésleutel bevat. Alle bestanden moeten in PEM-formaat zijn. 

Als u uw certificaat installeert vanuit Azure Key Vault met behulp van een [Resource Manager-sjabloon](./service-fabric-cluster-creation-create-template.md) of [PowerShell-opdrachten,](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0) wordt het certificaat in de juiste indeling geïnstalleerd in de map */var/lib/sfcerts* op elk knooppunt. Als u een certificaat via een andere methode installeert, moet u ervoor zorgen dat het certificaat correct is geïnstalleerd op clusterknooppunten.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificaten waarnaar in het aanvraagmanifest wordt verwezen

Certificaten die in het aanvraagmanifest zijn opgegeven, bijvoorbeeld via de elementen [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) of [**EndpointCertificate,**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) moeten aanwezig zijn in de map */var/lib/sfcerts.* De elementen die worden gebruikt om certificaten op te geven in het toepassingsmanifest, nemen geen padkenmerk aan, dus de certificaten moeten aanwezig zijn in de standaardmap. Deze elementen hebben een optioneel **kenmerk X509StoreName.** De standaardinstelling is "Mijn", wat verwijst naar de */var/lib/sfcerts* directory op Linux nodes. Elke andere waarde is niet gedefinieerd op een Linux-cluster. We raden u aan het kenmerk **X509StoreName** weg te laten voor apps die op Linux-clusters worden uitgevoerd. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificaten waarnaar wordt verwezen in het configuratiepakket (Settings.xml)

Voor sommige services u X.509-certificaten configureren in het [ConfigPackage](./service-fabric-application-and-service-manifests.md) (standaard Instellingen.xml). Dit is bijvoorbeeld het geval wanneer u certificaten declareert die worden gebruikt om RPC-kanalen te beveiligen voor Betrouwbare Services-services die zijn gebouwd met de Service Fabric .NET Core of Java SDKs. Er zijn twee manieren om naar certificaten te verwijzen in het configuratiepakket. Ondersteuning varieert tussen de .NET Core en Java SDKs.

### <a name="using-x509-securitycredentialstype"></a>X509 SecurityCredentialsType gebruiken

WIth de .NET of Java SDKs, u **X509** opgeven voor de **SecurityCredentialsType**. `X509Credentials` Dit komt overeen met het ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) type ( `SecurityCredentials` [.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

Met de **X509-verwijzing** wordt het certificaat in een certificaatarchief gevonden. In de volgende XML worden de parameters weergegeven die worden gebruikt om de locatie van het certificaat op te geven:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Voor een service die draait op Linux, **verwijst LocalMachine**/**My** naar de standaardlocatie voor certificaten, de */var/lib/sfcerts* directory. Voor Linux zijn alle andere combinaties van **CertificateStoreLocation** en **CertificateStoreName** niet gedefinieerd. 

Geef **LocalMachine** altijd op voor de parameter **CertificateStoreLocation.** Het is niet nodig om de parameter **CertificateStoreName** op te geven omdat deze standaard 'Mijn' is. Met een **X509-verwijzing** moeten de certificaatbestanden zich bevinden in de */var/lib/sfcerts-map* op het clusterknooppunt.  

In de volgende XML wordt een sectie **TransportInstellingen** weergegeven op basis van deze stijl:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>X509_2 SecurityCredentialsType gebruiken

Met de Java SDK u **X509_2** opgeven voor het **SecurityCredentialsType**. Dit `X509Credentials2` komt overeen met het `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) type ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Met een **X509_2** referentie geeft u een padparameter op, zodat u het certificaat vinden in een andere map dan */var/lib/sfcerts.*  In de volgende XML worden de parameters weergegeven die worden gebruikt om de locatie van het certificaat op te geven: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

In de volgende XML wordt een sectie **TransportInstellingen** weergegeven op basis van deze stijl.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Het certificaat is opgegeven als een .crt-bestand in de voorgaande XML. Dit houdt in dat er ook een .key-bestand is met de privésleutel op dezelfde locatie.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Een app Betrouwbare Services configureren om op Linux-clusters te draaien

Met de Service Fabric SDKs u communiceren met de Service Fabric runtime API's om het platform te gebruiken. Wanneer u een toepassing uitvoert die deze functionaliteit gebruikt op beveiligde Linux-clusters, moet u uw toepassing configureren met een certificaat dat kan worden gebruikt om te valideren met de runtime van Service Fabric. Toepassingen die Service Fabric Reliable Service-services bevatten die zijn geschreven met de .NET Core- of Java-SDK's vereisen deze configuratie. 

Als u een toepassing wilt configureren, voegt u een [**element SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) toe onder de tag **Certificaten,** dat zich onder de tag **ApplicationManifest** in het bestand *ApplicationManifest.xml* bevindt. In de volgende XML wordt een certificaat weergegeven waarnaar wordt verwezen door de duimafdruk: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

U verwijzen naar het clustercertificaat of een certificaat dat u op elk clusterknooppunt installeert. Op Linux moeten de certificaatbestanden aanwezig zijn in de */var/lib/sfcerts* directory. Zie [Locatie en formaat van X.509-certificaten op Linux-knooppunten](#location-and-format-of-x509-certificates-on-linux-nodes)voor meer informatie.



