---
title: Cloudservices en beheercertificaten | Microsoft Documenten
description: Meer informatie over het maken en gebruiken van certificaten met Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 173f5c698ab44ea269995665bcbc33c726d4f03a
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811456"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Overzicht van certificaten voor Azure Cloud Services
Certificaten worden gebruikt in Azure voor cloudservices[(servicecertificaten)](#what-are-service-certificates)en voor het verifiëren met de beheer-API[(beheercertificaten).](#what-are-management-certificates) In dit onderwerp vindt u een algemeen overzicht van beide certificaattypen, hoe u [deze maken](#create) en implementeren in Azure.

Certificaten die in Azure worden gebruikt, zijn x.509 v3-certificaten en kunnen worden ondertekend door een ander vertrouwd certificaat of kunnen zelf worden ondertekend. Een zelfondertekend certificaat wordt ondertekend door de eigen maker, daarom wordt het niet standaard vertrouwd. In de meeste browsers kan dit probleem worden genegeerd. U mag alleen zelfondertekende certificaten gebruiken bij het ontwikkelen en testen van uw cloudservices. 

Certificaten die door Azure worden gebruikt, kunnen een privé- of een openbare sleutel bevatten. Certificaten hebben een duimafdruk die een middel biedt om ze op een eenduidige manier te identificeren. Deze duimafdruk wordt gebruikt in het [Azure-configuratiebestand](cloud-services-configure-ssl-certificate-portal.md) om te bepalen welk certificaat een cloudservice moet gebruiken. 

>[!Note]
>Azure Cloud Services accepteert geen AES256-SHA256 versleuteld certificaat.

## <a name="what-are-service-certificates"></a>Wat zijn servicecertificaten?
Servicecertificaten zijn gekoppeld aan cloudservices en bieden beveiligde communicatie van en naar de service. Als u bijvoorbeeld een webrol hebt geïmplementeerd, wilt u een certificaat leveren waarmee een blootgesteld HTTPS-eindpunt kan worden geverifieerd. Servicecertificaten, gedefinieerd in uw servicedefinitie, worden automatisch geïmplementeerd op de virtuele machine waarop een instantie van uw rol wordt uitgevoerd. 

U kunt servicecertificaten uploaden naar Azure met behulp van Azure Portal of het klassieke implementatiemodel. Servicecertificaten zijn gekoppeld aan een specifieke cloudservice. De certificaten worden in het servicedefinitiebestand toegewezen aan een implementatie.

Servicecertificaten kunnen afzonderlijk van uw services worden beheerd en kunnen door verschillende personen worden beheerd. Een ontwikkelaar kan bijvoorbeeld een servicepakket uploaden dat verwijst naar een certificaat dat een IT-manager eerder naar Azure heeft geüpload. Een IT-beheerder kan dat certificaat beheren en vernieuwen (de configuratie van de service wijzigen) zonder dat er een nieuw servicepakket hoeft te worden geüpload. Updaten zonder een nieuw servicepakket is mogelijk omdat de logische naam, winkelnaam en locatie van het certificaat zich in het servicedefinitiebestand bevinden en terwijl de duimafdruk van het certificaat is opgegeven in het serviceconfiguratiebestand. Als u het certificaat wilt bijwerken, hoeft u alleen een nieuw certificaat te uploaden en de waarde voor de vingerafdruk te wijzigen in het serviceconfiguratiebestand.

>[!Note]
>De [veelgestelde vragen over cloudservices - Configuratie en beheer](cloud-services-configuration-and-management-faq.md) bevatten nuttige informatie over certificaten.

## <a name="what-are-management-certificates"></a>Wat zijn beheercertificaten?
Met beheercertificaten kunt u het klassieke implementatiemodel gebruiken voor verificatie. Veel programma's en hulpprogramma's (zoals Visual Studio of de Azure SDK) gebruiken deze certificaten om de configuratie en implementatie van verschillende Azure-services te automatiseren. Deze zijn niet echt gerelateerd aan cloudservices. 

> [!WARNING]
> Wees voorzichtig! Met deze typen certificaten kan iedereen die zich met hen verifieert, het abonnement beheren waaraan hij of zij is gekoppeld. 
> 
> 

### <a name="limitations"></a>Beperkingen
Er is een limiet van 100 beheercertificaten per abonnement. Er is ook een limiet van 100 beheercertificaten voor alle abonnementen onder de gebruikers-id van een specifieke servicebeheerder. Als de gebruikersnaam voor de accountbeheerder al is gebruikt om 100 beheercertificaten toe te voegen en er meer certificaten nodig zijn, u een co-beheerder toevoegen om de extra certificaten toe te voegen. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Een nieuw zelfondertekend certificaat maken
U elk hulpprogramma gebruiken dat beschikbaar is om een zelfondertekend certificaat te maken, zolang ze zich aan deze instellingen houden:

* Een X.509 certificaat.
* Bevat een privésleutel.
* Gemaakt voor sleuteluitwisseling (.pfx-bestand).
* De onderwerpnaam moet overeenkomen met het domein dat wordt gebruikt om toegang te krijgen tot de cloudservice.

    > U geen TLS/SSL-certificaat aanschaffen voor de cloudapp.net (of voor een Azure-gerelateerd) domein; de onderwerpnaam van het certificaat moet overeenkomen met de aangepaste domeinnaam die wordt gebruikt om toegang te krijgen tot uw toepassing. Bijvoorbeeld, **contoso.net**, niet **contoso.cloudapp.net**.

* Minimaal 2048-bits versleuteling.
* **Alleen servicecertificaat**: Client-side certificaat moet zich in het *persoonlijke* certificaatarchief bevinden.

Er zijn twee eenvoudige manieren om een `makecert.exe` certificaat op Windows te maken, met het hulpprogramma of IIS.

### <a name="makecertexe"></a>Makecert.exe
Dit nut is afgeschaft en is hier niet meer gedocumenteerd. Zie voor meer informatie [dit ARTIKEL MSDN.](/windows/desktop/SecCrypto/makecert)

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Als u het certificaat wilt gebruiken met een IP-adres in plaats van een domein, gebruikt u het IP-adres in de parameter DnsName.


Als u dit [certificaat met de beheerportal](../azure-api-management-certs.md)wilt gebruiken, exporteert u het naar een **.cer-bestand:**

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Er zijn veel pagina's op het internet die betrekking hebben op hoe dit te doen met IIS. [Hier](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) is een grote vond ik dat ik denk dat verklaart het goed. 

### <a name="linux"></a>Linux
[In](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dit artikel wordt beschreven hoe u certificaten maakt met SSH.

## <a name="next-steps"></a>Volgende stappen
[Upload uw servicecertificaat naar de Azure-portal.](cloud-services-configure-ssl-certificate-portal.md)

Upload een [beheer-API-certificaat](../azure-api-management-certs.md) naar de Azure-portal.




