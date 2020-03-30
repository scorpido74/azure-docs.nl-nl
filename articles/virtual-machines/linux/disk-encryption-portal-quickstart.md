---
title: Een Linux-VM maken en versleutelen met behulp van Azure Portal
description: In deze quickstart leert u hoe u de Azure-portal gebruiken om een Virtuele Linux-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 9f09f28dc63e7f061946a66beb59bd4c62be70aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970560"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Snelstart: een virtuele machine maken en versleutelen met de Azure-portal

Virtuele Azure-machines (VM's)kunnen worden gemaakt via Azure Portal. De Azure-portal is een gebruikersinterface op basis van een browser voor het maken van VM's en alle verwante resources. In deze quickstart gebruikt u de Azure-portal om een Virtuele Linux-machine (VM) met Ubuntu 18.04 LTS te implementeren, een sleutelkluis te maken voor de opslag van versleutelingssleutels en de VM te versleutelen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.
1. Selecteer op de pagina Nieuw onder Populair **ubuntu Server 18.04 LTS**.
1. Controleer op het tabblad **Basisbeginselen** onder **Projectdetails**of het juiste abonnement is geselecteerd.
1. Selecteer **voor resourcegroep**de resourcegroep die u hebt gemaakt bij het maken van uw sleutelkluis hierboven (bijvoorbeeld **myResourceGroup).**
1. Voer *MyVM*in voor **de naam van de virtuele machine.**
1. Selecteer **bij Regio**dezelfde regio die u hebt gebruikt bij het maken van uw sleutelkluis hierboven (bijvoorbeeld **Oost-VS**).
1. Zorg ervoor dat **de maat** *standaard D2s v3*is.
1. Selecteer Onder **Administrator-account**de optie **Wachtwoord**. Voer een gebruikersnaam en een wachtwoord in.
    ![Scherm ResourceGroup maken](./media/disk-encryption/portal-qs-vm-creation.png)
1. Selecteer het tabblad 'Beheer' en controleer of u een diagnostisch opslagaccount hebt. Als u geen opslagaccounts hebt, selecteert u 'Nieuw maken', geeft ![u uw nieuwe account een naam en selecteert u het scherm 'Ok' ResourceGroup](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Klik op 'Controleren + Maken'.
1. Op de pagina **Een virtuele machine maken** ziet u de details van de virtuele machine die u gaat maken. Wanneer u klaar bent, selecteert u **Maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. Wanneer de implementatie is voltooid, gaat u verder naar de volgende sectie.

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

1. Wanneer de VM-implementatie is voltooid, selecteert u **Ga naar resource**.
1. Selecteer **schijven**op de linkerzijbalk .
1. Selecteer **versleuteling**in het scherm Schijven . 

    ![schijven en versleutelingsselectie](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Kies op het versleutelingsscherm onder **Schijven om te versleutelen**de optie **OS- en gegevensschijven**.
1. Kies **onder Versleutelingsinstellingen**de optie **Een sleutelkluis en sleutel voor versleuteling selecteren.**
1. Selecteer op de **sleutelvan Azure Key Vault in** de optie Nieuw **maken**.

    ![schijven en versleutelingsselectie](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Controleer in het scherm **Sleutelkluis maken** of de resourcegroep dezelfde is als de groep die u hebt gebruikt om de vm te maken.
1. Geef je sleutelkluis een naam.  Elke sleutelkluis in Azure moet een unieke naam hebben.
1. Schakel op het tabblad **Toegangsbeleid** het selectievakje **Azure-schijfversleuteling in voor volumeversleuteling.**

    ![schijven en versleutelingsselectie](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Selecteer **Controleren + maken**.  
1. Nadat de sleutelkluis is gevalideerd, selecteert u **Maken**. Hiermee wordt u teruggeplaatst naar de **selecte toets van het** Azure Key Vault-scherm.
1. Laat het veld **Sleutel** leeg en kies **Selecteren**.
1. Klik boven aan het versleutelingsscherm op **Opslaan**. Een pop-up waarschuwt u dat de VM opnieuw zal worden opgestart. Klik **op Ja**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u Verwijderen. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt die is ingeschakeld voor versleutelingssleutels, een virtuele machine hebt gemaakt en de virtuele machine voor versleuteling hebt ingeschakeld.  

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
