---
title: Cloud Services-en beheer certificaten | Microsoft Docs
description: Meer informatie over het maken en gebruiken van certificaten met Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 783343dd8877bdf18e783494960c3052c293cc7c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355885"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Overzicht van certificaten voor Azure Cloud Services
Certificaten worden gebruikt in azure voor Cloud Services ([service certificaten](#what-are-service-certificates)) en voor de verificatie met behulp van de beheer-API ([beheer certificaten](#what-are-management-certificates)). In dit onderwerp vindt u een algemeen overzicht van beide certificaat typen, hoe u deze kunt [maken](#create) en implementeren in Azure.

Certificaten die worden gebruikt in azure, zijn x. 509 v3-certificaten en kunnen worden ondertekend door een ander vertrouwd certificaat of ze kunnen zelf worden ondertekend. Een zelfondertekend certificaat is ondertekend door een eigen Maker, daarom wordt het niet standaard vertrouwd. De meeste browsers kunnen dit probleem negeren. Gebruik zelfondertekende certificaten alleen bij het ontwikkelen en testen van uw Cloud Services. 

Certificaten die worden gebruikt door Azure kunnen een persoonlijke of een open bare sleutel bevatten. Certificaten hebben een vinger afdruk waarmee ze op een ondubbelzinnige manier kunnen worden geïdentificeerd. Deze vinger afdruk wordt in het Azure- [configuratie bestand](cloud-services-configure-ssl-certificate-portal.md) gebruikt om te bepalen welk certificaat een Cloud service moet gebruiken. 

>[!Note]
>Azure Cloud Services accepteert geen versleuteld AES256-SHA256-certificaat.

## <a name="what-are-service-certificates"></a>Wat zijn service certificaten?
Service certificaten zijn gekoppeld aan Cloud Services en bieden beveiligde communicatie van en naar de service. Als u bijvoorbeeld een webrole hebt geïmplementeerd, wilt u een certificaat opgeven dat een weer gegeven HTTPS-eind punt kan verifiëren. Service certificaten die zijn gedefinieerd in uw service definitie, worden automatisch geïmplementeerd op de virtuele machine waarop een exemplaar van uw rol wordt uitgevoerd. 

U kunt Service certificaten uploaden naar Azure met behulp van de Azure Portal of door gebruik te maken van het klassieke implementatie model. Service certificaten zijn gekoppeld aan een specifieke Cloud service. Ze worden toegewezen aan een implementatie in het service definitie bestand.

Service certificaten kunnen afzonderlijk van uw services worden beheerd en kunnen door verschillende personen worden beheerd. Een ontwikkelaar kan bijvoorbeeld een service pakket uploaden dat verwijst naar een certificaat dat een IT-beheerder eerder naar Azure heeft geüpload. Een IT-beheerder kan dat certificaat beheren en vernieuwen (de configuratie van de service wijzigen) zonder dat er een nieuw service pakket hoeft te worden geüpload. Het bijwerken zonder een nieuw service pakket is mogelijk omdat de logische naam, de archief naam en de locatie van het certificaat zich in het service definitie bestand bevindt en terwijl de vinger afdruk van het certificaat in het service configuratie bestand is opgegeven. Als u het certificaat wilt bijwerken, is het alleen nodig om een nieuw certificaat te uploaden en de vingerafdruk waarde in het service configuratie bestand te wijzigen.

>[!Note]
>Het artikel [Veelgestelde vragen over Cloud Services-configuratie en beheer](cloud-services-configuration-and-management-faq.md) bevat een aantal nuttige informatie over certificaten.

## <a name="what-are-management-certificates"></a>Wat zijn beheer certificaten?
Met beheer certificaten kunt u verifiëren met het klassieke implementatie model. Veel Program ma's en hulpprogram ma's (zoals Visual Studio of de Azure SDK) gebruiken deze certificaten om de configuratie en implementatie van verschillende Azure-Services te automatiseren. Deze zijn niet echt gerelateerd aan Cloud Services. 

> [!WARNING]
> Wees voorzichtig! Met deze typen certificaten kunnen gebruikers die met hen worden geverifieerd, het abonnement beheren waaraan ze zijn gekoppeld. 
> 
> 

### <a name="limitations"></a>Beperkingen
Er geldt een limiet van 100 beheer certificaten per abonnement. Er is ook een limiet van 100 beheer certificaten voor alle abonnementen onder een specifieke gebruikers-ID van de service beheerder. Als de gebruikers-ID voor de account beheerder al is gebruikt om 100-beheer certificaten toe te voegen en er meer certificaten nodig zijn, kunt u een mede beheerder toevoegen om de extra certificaten toe te voegen. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Een nieuw zelfondertekend certificaat maken
U kunt elk hulp programma dat beschikbaar is voor het maken van een zelfondertekend certificaat, gebruiken zolang ze voldoen aan deze instellingen:

* Een X. 509-certificaat.
* Bevat een persoonlijke sleutel.
* Gemaakt voor sleutel uitwisseling (PFX-bestand).
* De naam van het onderwerp moet overeenkomen met het domein dat wordt gebruikt voor toegang tot de Cloud service.

    > U kunt geen SSL-certificaat verkrijgen voor het cloudapp.net (of voor een Azure-gerelateerd) domein. de onderwerpnaam van het certificaat moet overeenkomen met de aangepaste domein naam die wordt gebruikt voor toegang tot uw toepassing. Bijvoorbeeld **contoso.net**, niet **contoso.cloudapp.net**.

* Mini maal 2048 bits versleuteling.
* **Alleen service certificaat**: certificaat aan client zijde moet zich in het *persoonlijke* certificaat archief bevinden.

Er zijn twee eenvoudige manieren om een certificaat te maken in Windows, met het hulp programma `makecert.exe` of IIS.

### <a name="makecertexe"></a>Makecert.exe
Dit hulp programma is afgeschaft en wordt hier niet meer gedocumenteerd. Zie [dit MSDN-artikel](/windows/desktop/SecCrypto/makecert)voor meer informatie.

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Als u het certificaat wilt gebruiken met een IP-adres in plaats van een domein, gebruikt u het IP-adres in de para meter-DnsName.


Als u dit certificaat wilt gebruiken [met de beheer Portal](../azure-api-management-certs.md), exporteert u het naar een **. CER** -bestand:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Er zijn veel pagina's op Internet waarmee u dit kunt doen met IIS. [Hier](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) vindt u een geweldig onderwerp dat ik goed zie. 

### <a name="linux"></a>Linux
In [Dit](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artikel wordt beschreven hoe u certificaten maakt met SSH.

## <a name="next-steps"></a>Volgende stappen
[Upload uw service certificaat naar de Azure Portal](cloud-services-configure-ssl-certificate-portal.md).

Upload een [API-certificaat voor beheer](../azure-api-management-certs.md) naar het Azure Portal.




