---
title: Een Linux-VM maken en versleutelen met behulp van Azure Portal
description: In deze Quick Start leert u hoe u de Azure Portal kunt gebruiken om een virtuele Linux-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 6e32bfdf8c4b2dd7ce61393ab545770cafc73cf5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792527"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Snelstartgids: een virtuele machine maken en versleutelen met de Azure Portal

Virtuele Azure-machines (VM's)kunnen worden gemaakt via Azure Portal. De Azure-portal is een gebruikersinterface op basis van een browser voor het maken van VM's en alle verwante resources. In deze Quick Start gebruikt u de Azure Portal voor het implementeren van een virtuele Linux-machine (VM) met Ubuntu 18,04 LTS, het maken van een sleutel kluis voor de opslag van versleutelings sleutels en het versleutelen van de virtuele machine.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.
1. Op de nieuwe pagina, onder populair, selecteert u **Ubuntu Server 18,04 LTS**.
1. Controleer op het tabblad **basis beginselen** onder **Project Details**of het juiste abonnement is geselecteerd.
1. Voor **resource groep**selecteert u de resource groep die u hebt gemaakt bij het maken van uw sleutel kluis (bijvoorbeeld **myResourceGroup**).
1. Voer *MyVM*in voor de naam van de **virtuele machine**.
1. Selecteer voor **regio**dezelfde regio die u hebt gebruikt bij het maken van uw sleutel kluis (bijvoorbeeld **VS-Oost**).
1. Zorg ervoor dat **Size** de grootte *standaard D2s v3*is.
1. Selecteer **wacht woord**onder **Administrator-account**. Voer een gebruikers naam en wacht woord in.

    :::image type="content" source="./media/disk-encryption/portal-qs-vm-creation.png" alt-text="Scherm ResourceGroup maken":::


    > [!WARNING]
    > Het tabblad schijven bevat een veld versleutelings type onder **schijf opties**. Dit veld wordt gebruikt om versleutelings opties op te geven voor [Managed disks](managed-disks-overview.md) + CMK, niet voor Azure Disk Encryption. 
    >
    > Om Verwar ring te voor komen, wordt u aangeraden het tabblad *schijven* helemaal over te slaan en deze zelf studie te volt ooien. 

1. Selecteer het tabblad beheer en controleer of u een diagnostische opslag account hebt. Als u geen opslag accounts hebt, selecteert u nieuwe maken, geeft u het nieuwe account een naam en selecteert u OK.

    :::image type="content" source="./media/disk-encryption/portal-qs-vm-creation-storage.png" alt-text="Scherm ResourceGroup maken":::

1. Klik op ' bekijken + maken '.
1. Op de pagina **Een virtuele machine maken** ziet u de details van de virtuele machine die u gaat maken. Wanneer u klaar bent, selecteert u **Maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. Wanneer de implementatie is voltooid, gaat u verder naar de volgende sectie.

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

1. Wanneer de VM-implementatie is voltooid, selecteert **u naar resource**.
1. Selecteer op de zijbalk links de optie **schijven**.
1. Selecteer op het scherm schijven de optie **versleuteling**. 

    :::image type="content" source="../media/disk-encryption/portal-qs-disks-to-encryption.png" alt-text="schijven en versleutelings selectie":::

1. Kies op het scherm versleuteling onder **schijven die moeten worden versleuteld, het** **besturings systeem en de gegevens schijven**.
1. Kies onder **versleutelings instellingen** **de optie Selecteer een sleutel kluis en sleutel voor versleuteling**.
1. Selecteer **nieuwe maken**op het scherm **sleutel selecteren in azure Key Vault** .

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="schijven en versleutelings selectie":::

1. Zorg ervoor dat in het scherm **sleutel kluis maken** de resource groep gelijk is aan die u hebt gebruikt om de virtuele machine te maken.
1. Geef uw sleutel kluis een naam.  Elke sleutel kluis in azure moet een unieke naam hebben.
1. Schakel op het tabblad **toegangs beleid** het selectie vakje **Azure Disk Encryption voor volume versleuteling** in.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-enable.png" alt-text="schijven en versleutelings selectie":::

1. Selecteer **controleren + maken**.  
1. Nadat de sleutel kluis validatie heeft door gegeven, selecteert u **maken**. Hiermee keert u terug naar het scherm **sleutel selecteren in azure Key Vault** .
1. Laat het **sleutel** veld leeg en kies **selecteren**.
1. Klik boven aan het scherm voor versleuteling op **Opslaan**. Er wordt een waarschuwing weer gegeven dat de VM opnieuw moet worden opgestart. Klik op **Ja**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u Verwijderen. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Key Vault gemaakt die is ingeschakeld voor versleutelings sleutels, een virtuele machine gemaakt en de virtuele machine ingeschakeld voor versleuteling.  

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
