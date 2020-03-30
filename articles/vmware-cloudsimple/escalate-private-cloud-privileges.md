---
title: Privécloudrechten escaleren
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschrijft hoe u bevoegdheden op uw private cloud voor beheerfuncties in vCenter escaleren
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d06f3e8be449e7050c65c75339a0cff6efe19e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025313"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escaleer de vCenter-rechten voor Private Cloud vanuit de CloudSimple-portal

Voor administratieve toegang tot uw Private Cloud vCenter u uw CloudSimple-bevoegdheden tijdelijk escaleren.  Met verhoogde bevoegdheden u VMware-oplossingen installeren, identiteitsbronnen toevoegen en gebruikers beheren.

Nieuwe gebruikers kunnen worden gemaakt op het vCenter SSO-domein en krijgen toegang tot vCenter.  Wanneer u nieuwe gebruikers maakt, voegt u deze toe aan de ingebouwde groepen CloudSimple voor toegang tot vCenter.  Zie [CloudSimple Private Cloud-machtigingsmodel van VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/)voor meer informatie.

> [!CAUTION]
> Breng geen configuratiewijzigingen aan voor beheeronderdelen. Acties die tijdens de geëscaleerde bevoorrechte status worden uitgevoerd, kunnen een negatieve invloed hebben op uw systeem of kunnen ertoe leiden dat uw systeem niet meer beschikbaar is.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="escalate-privileges"></a>Bevoegdheden escaleren

1. Toegang tot de [CloudSimple-portal](access-cloudsimple-portal.md).

2. Open de pagina **Resources** en selecteer de private cloud waarvoor u bevoegdheden wilt escaleren.

3. Klik onder aan de pagina Overzicht onder **VSphere-bevoegdheden wijzigen**op **Escaleren**.

    ![VSphere-bevoegdheid wijzigen](media/escalate-private-cloud-privilege.png)

4. Selecteer het vSphere-gebruikerstype.  Alleen `CloudOwner@cloudsimple.local` de lokale gebruiker kan worden geëscaleerd.

5. Selecteer het tijdsinterval escaleren in de vervolgkeuzelijst. Kies de kortste periode waarmee u de taak voltooien.

6. Schakel het selectievakje in om te bevestigen dat u de risico's begrijpt.

    ![Dialoogvenster Bevoegdheden escaleren](media/escalate-private-cloud-privilege-dialog.png)

7. Klik op **OK**.

8. Het escalatieproces kan een paar minuten duren. Na het voltooien klikt u op **OK**.

De escalatie van de bevoegdheid begint en duurt tot het einde van het geselecteerde interval.  U zich aanmelden bij uw private cloud vCenter om administratieve taken uit te voeren.

> [!IMPORTANT]
> Slechts één gebruiker kan bevoegdheden hebben geëscaleerd.  U moet de bevoegdheden van de gebruiker de-escaleren voordat u de bevoegdheden van een andere gebruiker escaleren.

> [!CAUTION]
> Nieuwe gebruikers mogen alleen worden toegevoegd aan *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* of *Cloud-Global-VM-Admin-Group*.  Gebruikers die zijn toegevoegd aan de groep *Administrators,* worden automatisch verwijderd.  Alleen serviceaccounts mogen worden toegevoegd aan de groep *Administrators* en serviceaccounts mogen niet worden gebruikt om zich aan te melden bij de vSphere-webgebruikersinterface.

## <a name="extend-privilege-escalation"></a>Escalatie van bevoegdheden uitbreiden

Als u extra tijd nodig hebt om uw taken te voltooien, u de escalatieperiode voor bevoegdheden verlengen.  Kies het extra tijdsinterval escaleren waarmee u de administratieve taken voltooien.

1. Selecteer op de **Resources** > **Private Clouds** in de CloudSimple-portal de Private Cloud waarvoor u de escalatie van bevoegdheden wilt uitbreiden.

2. Klik onder aan het tabblad Overzicht op **Escalatie van bevoegdheden uitbreiden**.

    ![Escalatie van bevoegdheden uitbreiden](media/de-escalate-private-cloud-privilege.png)

3. Selecteer een tijdsinterval escaleren in de vervolgkeuzelijst. Bekijk de nieuwe escalatieeindtijd.

4. Klik **op Opslaan** om het interval uit te breiden.

## <a name="de-escalate-privileges"></a>Bevoegdheden de-escaleren

Zodra uw administratieve taken zijn voltooid, moet u uw bevoegdheden de-escaleren.  

1. Selecteer op de **Resources** > **Private Clouds** in de CloudSimple-portal de Private Cloud waarvoor u bevoegdheden wilt de-escaleren.

2. Klik **op De-escaleren**.

3. Klik op **OK**.

> [!IMPORTANT]
> Als u fouten wilt voorkomen, meldt u zich af bij vCenter en meldt u zich opnieuw aan na het de-escaleren van bevoegdheden.

## <a name="next-steps"></a>Volgende stappen

* [VCenter-identiteitsbronnen instellen om Active Directory te gebruiken](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Back-upoplossing installeren om [back-upworkloads virtuele machines](https://docs.azure.cloudsimple.com/backup-workloads-veeam/) te maken