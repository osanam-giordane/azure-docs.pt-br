---
title: Solucionar problemas de discos anexados às VMs do Azure | Microsoft Docs
description: O armazenamento de Blobs do Azure é projetado para armazenar grandes quantidades de dados de objeto não estruturados, como texto ou dados binários. Os aplicativos podem acessar objetos no armazenamento de Blobs do PowerShell ou da CLI do Azure a partir do código por meio de bibliotecas de cliente de Armazenamento do Azure ou em REST.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.component: disks
ms.openlocfilehash: 0dbd89c28d18d64908d92cd38d8bd1cf3138fd5c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397960"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Solucionar problemas de discos anexados às VMs do Azure 

As Máquinas Virtuais (VMs) do Azure dependem de Discos Rígidos virtuais (VHDs) para o disco do sistema operacional e quaisquer discos de dados anexados. Os VHDs são armazenados como blobs de páginas em uma ou mais contas de Armazenamento do Microsoft Azure. Este artigo descreve como solucionar problemas de conteúdo para problemas comuns que podem surgir com VHDs. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Solucionar problemas de erros de exclusão de armazenamento para uma VM

Em certos casos, você pode encontrar um erro enquanto a exclusão de um recurso de armazenamento quando uma VM em uma implantação do Gerenciador de Recursos contém VHDs anexados. Para obter ajuda na resolução desse problema, consulte um dos seguintes artigos: 

  * Em VMs do Linux: [Erros de exclusão de armazenamento na implantação do Gerenciador de Recursos](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Em VMs do Windows: [Erros de exclusão de armazenamento na implantação do Gerenciador de Recursos](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados

Se você encontrar reinicializações inesperadas de uma VM com um grande número de VHDs anexados, consulte um dos seguintes artigos:

  * Em VMs do Linux: [Reinicializações inesperadas de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Em VMs do Windows: [Reinicializações inesperadas de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
