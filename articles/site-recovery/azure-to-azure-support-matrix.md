---
title: Matriz de suporte do Azure Site Recovery para replicação do Azure para o Azure | Microsoft Docs
description: Resume os sistemas operacionais com suporte e as configurações de replicação do Azure Site Recovery de VMs (máquinas virtuais) do Azure de uma região para outra, de acordo com as necessidades de DR (recuperação de desastre).
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: sujayt
ms.openlocfilehash: c2892d51c6eb5e71c0b1af400b78e993742fede0
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173043"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Matriz de suporte para replicação de uma região do Azure para outra



Este artigo resume as configurações e os componentes com suporte quando você replica e recupera máquinas virtuais do Azure de uma região para outra, usando o serviço [Azure Site Recovery](site-recovery-overview.md).

## <a name="user-interface-options"></a>Opções de interface do usuário

**Interface do usuário** |  **Com suporte/Sem suporte**
--- | ---
**Portal do Azure** | Com suporte
**PowerShell** | [Replicação do Azure para o Azure com o PowerShell](azure-to-azure-powershell.md)
**API REST** | Sem suporte no momento
**CLI** | Sem suporte no momento


## <a name="resource-support"></a>Suporte de recurso

**Tipo de movimentação de recursos** | **Detalhes** 
--- | --- | ---
**Mover cofre entre grupos de recursos** | Sem suporte<br/><br/> Você não pode mover um cofre de serviços de recuperação entre grupos de recursos.
**Mover recursos de computação/armazenamento/rede entre os grupos de recursos** | Sem suporte.<br/><br/> Se você mover uma VM ou componentes associados, como armazenamento/rede, após a replicação, precisará desabilitar a replicação e reativar a replicação para a VM.
**Replicar VMs do Azure de uma assinatura para outra para recuperação de desastres** | Sem suporte.
**Migrar VMs entre assinaturas** | Sem suporte.
**Migrar máquinas virtuais na mesma região** | Sem suporte.


## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado

O suporte abaixo é aplicável a qualquer carga de trabalho em execução no sistema operacional mencionado.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core, Servidor com Experiência Desktop)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 com, no mínimo, SP1

>[!NOTE]
>
> \* Não há suporte para o Windows Server 2016 Nano Server.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4, 7.5
- Ubuntu 14.04 LTS Server [ (versões de kernel com suporte)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (versões de kernel com suporte)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (versões do kernel com suporte)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (versões do kernel com suporte)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1,SP2,SP3 [ (versões de kernel com suporte)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4
- Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3)

(Não há suporte para a atualização de computadores de replicação de SLES 11 SP3 até SLES 11 SP4. Se um computador replicado tiver sido atualizado do SLES 11 SP3 para o SLES 11 SP4, você precisará desabilitar a replicação e proteger o computador novamente após a atualização).

>[!NOTE]
>
> A autenticação baseada em senha e logon pode ser desabilitada no failover em servidores Ubuntu que a utilizam junto com o pacote cloud-init para configurar máquinas virtuais de nuvem, pode ter o logon baseado em senha desabilitado no failover (dependendo da configuração de cloudinit). O logon baseado em senha pode ser reabilitado na máquina virtual redefinindo a senha no menu configurações (na seção SUPORTE + SOLUÇÃO DE PROBLEMAS) da máquina virtual de failover no Portal do Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.18 | 3.13.0-24-generic a 3.13.0-151-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-generic a 3.13.0-147-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-generic para 3.13.0-144-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-119-generic, |
14.04 LTS | 9.15 | 3.13.0-24-generic para 3.13.0-143-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-116-generic |
|||
16.04 LTS | 9.18 | 4.4.0-21-generic a 4.4.0-128-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-generic a 4.4.0-124-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-41-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-generic para 4.4.0-119-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-Generic para 4.13.0-38-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1012-azure |
16.04 LTS | 9.15 | 4.4.0-21-generic para 4.4.0-116-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>3.13.0-24-generic para 3.13.0-143-generic,<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Debian para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | 9.17,9.18 | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.15, 9.16 | 3.2.0-4-amd64 para 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 a 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.15, 9.16 | 3.16.0-4-amd64 para 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 para 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel com suporte do SUSE Linux Enterprise Server 12 para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.18 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default a 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | 9.17 | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.88-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default a 4.4.126-94.22-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Sistemas de arquivos com suporte e configurações de armazenamento de convidado em máquinas virtuais do Azure que executam o sistema operacional Linux

* Sistemas de arquivos: ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS
* Gerenciador de volumes: LVM2
* Software Multipath: Mapeador de dispositivos

## <a name="region-support"></a>Suporte de regiões

É possível replicar e recuperar VMs entre duas regiões quaisquer dentro do mesmo cluster geográfico.

**Cluster geográfico** | **Regiões do Azure**
-- | --
América | Leste do Canadá, Canadá Central, Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, EUA Central, Centro-Norte dos EUA
Europa | Oeste do Reino Unido, Sul do Reino Unido, Norte da Europa, Europa Ocidental, França Central, Sul da França
Ásia | Sul da Índia, Índia Central, Sudeste Asiático, Ásia Oriental, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia
Austrália   | Leste da Austrália, Sudeste da Austrália
Azure Government    | US Gov Virginia, US Gov Iowa, US Gov – Arizona, US Gov – Texas, US DoD Leste, US DoD Central
Alemanha | Centro da Alemanha, Nordeste da Alemanha
China | Leste da China, Norte da China

>[!NOTE]
>
> Para a região Sul do Brasil, é possível apenas replicar e fazer failover para uma das regiões Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2 e Centro-Norte dos EUA e fazer failback.

## <a name="support-for-vmdisk-management"></a>Suporte para gerenciamento de VM/disco

**Ação** | **Detalhes**
-- | ---
Redimensionar o disco na VM replicada | Com suporte
Adicionar o disco à VM replicada | Sem suporte. Você precisa desabilitar a replicação para a VM, adicione o disco e, em seguida, habilite a replicação novamente.


## <a name="support-for-compute-configuration"></a>Suporte para configuração de Computação

**Configuração** | **Com suporte/Sem suporte** | **Comentários**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM | Consulte [Tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md)
Conjuntos de disponibilidade | Com suporte | Se você usar a opção padrão durante a etapa “Habilitar replicação” no portal, o conjunto de disponibilidade será automaticamente criado de acordo com a configuração da região de origem. Altere o conjunto de disponibilidade de destino definido em “Item replicado > Configurações > Computação e Rede > Conjunto de disponibilidade” a qualquer momento.
VMs do HUB (Benefício de Uso Híbrido) | Com suporte | Se a VM de origem tiver a licença do HUB habilitada, a VM de Failover de teste ou de Failover também usará a licença do HUB.
conjuntos de escala de máquina virtual | Sem suporte |
Imagens da Galeria do Azure – publicadas pela Microsoft | Com suporte | Com suporte, desde que a VM seja executada em um sistema operacional com suporte pelo Site Recovery
Imagens da Galeria do Azure – publicadas por terceiros | Com suporte | Com suporte, desde que a VM seja executada em um sistema operacional com suporte pelo Site Recovery.
Imagens personalizadas – publicadas por terceiros | Com suporte | Com suporte, desde que a VM seja executada em um sistema operacional com suporte pelo Site Recovery.
VMs migradas com o Site Recovery | Com suporte | Se ela for um computador Físico/VMware migrado para o Azure com o Site Recovery, você precisará desinstalar a versão mais antiga do serviço de mobilidade e reiniciar o computador antes de replicá-lo para outra região do Azure.

## <a name="support-for-storage-configuration"></a>Suporte para configuração de Armazenamento

**Configuração** | **Com suporte/Sem suporte** | **Comentários**
--- | --- | ---
Tamanho máximo do disco do sistema operacional | 2048 GB | Consulte [Discos usados pelas VMs.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Tamanho máximo do disco de dados | 4095 GB | Consulte [Discos usados pelas VMs.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Número de discos de dados | Até 64, com suporte em um tamanho específico de VM do Azure | Consulte [Tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md)
Disco temporário | Sempre excluído da replicação | O disco temporário sempre é excluído da replicação. Você não deve colocar nenhum dado persistente no disco temporário, de acordo com as diretrizes do Azure. Consulte [Disco temporário em VMs do Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) para obter mais detalhes.
Taxa de alteração de dados no disco | Máximo de 10 MBps por disco para armazenamento Premium e 2 MBps por disco para armazenamento Standard | Se a taxa média de alteração de dados no disco for superior a 10 MBps (para Premium) e 2 MBps (para Standard) continuamente, a replicação não será alcançada. No entanto, se for um modo de intermitência de dados ocasional e a taxa de alteração de dados for superior a 10 MBps (para Premium) e 2 MBps (para Standard) por algum tempo e cair, a replicação será alcançada. Nesse caso, talvez você veja os pontos de recuperação um pouco atrasados.
Discos em contas de armazenamento Standard | Com suporte |
Discos em contas de armazenamento Premium | Com suporte | Se uma VM tiver discos distribuídos em contas de armazenamento Standard e Premium, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, a fim de garantir que você tem a mesma configuração de armazenamento na região de destino
Managed Disks Standard | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |  
Managed Disks Premium | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
Espaços de armazenamento | Com suporte |         
Criptografia em repouso (SSE) | Com suporte | SSE é a configuração padrão em contas de armazenamento.   
ADE (Azure Disk Encryption) | Sem suporte |
Adição/remoção de disco a quente | Sem suporte | Se você adicionar ou remover um disco de dados da VM, precisará desabilitar a replicação e habilitá-la novamente na VM.
Exclusão de disco | Sem suporte|   O disco temporário é excluído por padrão.
Espaços de armazenamento Diretos  | Sem suporte|
Servidor de Arquivos de Expansão  | Sem suporte|
LRS | Com suporte |
GRS | Com suporte |
RA-GRS | Com suporte |
ZRS | Sem suporte |  
Armazenamento Frio e Quente | Sem suporte | Não há suporte para discos de máquina virtual no armazenamento frio e quente
Firewalls de armazenamento do Azure para redes virtuais  | Não  | Não há suporte para permitir o acesso a redes virtuais do Azure específicas em contas de armazenamento de cache usadas para armazenar os dados replicados.
Contas de armazenamento V2 de uso geral (camadas Hot e Cool) | Não  | Os custos das transações aumentam substancialmente em comparação com as contas de armazenamento V1 de uso geral

>[!IMPORTANT]
> Certifique-se de que observou os destinos de desempenho e escalabilidade de disco de VM para máquinas virtuais [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../virtual-machines/windows/disk-scalability-targets.md) para evitar qualquer problema de desempenho. Se você seguir as configurações padrão, o Site Recovery criará os discos e as contas de armazenamento necessários com base na configuração de origem. Se você personalizar e selecionar suas próprias configurações, certifique-se de que seguiu os destinos de escalabilidade e desempenho para discos de máquina virtual de origem.

## <a name="support-for-network-configuration"></a>Suporte para configuração de Rede
**Configuração** | **Com suporte/Sem suporte** | **Comentários**
--- | --- | ---
NIC (adaptador de rede) | Até o número máximo de NICs com suporte em um tamanho específico da VM do Azure | As NICs são criadas quando a VM é criada como parte de uma operação de Failover de teste ou de Failover. O número de NICs na VM de failover depende do número de NICs que a VM de origem tem no momento da habilitação da replicação. Se você adicionar ou remover uma NIC depois de habilitar a replicação, isso não afetará a contagem de NICs na VM de failover.
Balanceador de Carga de Internet | Com suporte | Você precisa associar o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Com suporte | Você precisa associar o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
IP público| Com suporte | Você precisa associar um IP público já existente a NIC ou criar um e associá-lo a NIC usando um script de automação do Azure em um plano de recuperação.
NSG na NIC (Resource Manager)| Com suporte | Você precisa associar o NSG a NIC usando um script de automação do Azure em um plano de recuperação.  
NSG na sub-rede (Resource Manager e Clássico)| Com suporte | Você precisa associar o NSG a NIC usando um script de automação em um plano de recuperação.
NSG na VM (Clássico)| Com suporte | Você precisa associar o NSG a NIC usando um script de automação do Azure em um plano de recuperação.
IP Reservado (IP Estático)/Reter o IP de origem | Com suporte | Se a NIC da VM de origem tiver a configuração de IP estático e a sub-rede de destino tiver o mesmo IP disponível, ela será atribuída à VM de failover. Se a sub-rede de destino não tiver o mesmo IP disponível, um dos IPs disponíveis na sub-rede será reservado para essa VM. Você pode especificar um IP fixo de sua escolha em “Item replicado > Configurações > Computação e Rede > Adaptadores de rede”. Você pode selecionar a NIC e especificar a sub-rede e o IP de sua escolha.
IP Dinâmico| Com suporte | Se a NIC da VM de origem tiver a configuração de IP dinâmico, a NIC da VM de failover também será Dinâmica por padrão. Você pode especificar um IP fixo de sua escolha em “Item replicado > Configurações > Computação e Rede > Adaptadores de rede”. Você pode selecionar a NIC e especificar a sub-rede e o IP de sua escolha.
Integração do Gerenciador de Tráfego | Com suporte | Você pode pré-configurar o Gerenciador de Tráfego de tal forma que o tráfego seja encaminhado para o ponto de extremidade na região de origem com regularidade e para o ponto de extremidade na região de destino em caso de failover.
DNS gerenciado do Azure | Com suporte |
DNS Personalizado  | Com suporte |    
Proxy não autenticado | Com suporte | Consulte o [documento de diretrizes de rede.](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticado | Sem suporte | Se a VM estiver usando um proxy autenticado para a conectividade de saída, ela não poderá ser replicada com o Azure Site Recovery.    
VPN Site a Site com local (com ou sem o ExpressRoute)| Com suporte | Verifique se as UDRs e os NSGs são configurados de tal forma que o tráfego do Site Recovery não seja encaminhado para o local. Consulte o [documento de diretrizes de rede.](site-recovery-azure-to-azure-networking-guidance.md)  
Conexão VNET a VNET | Com suporte | Consulte o [documento de diretrizes de rede.](site-recovery-azure-to-azure-networking-guidance.md)  
Pontos de extremidade de serviço de rede virtual | Com suporte | Não há suporte para os firewalls de armazenamento do Azure para redes virtuais. Não há suporte para permitir o acesso a redes virtuais do Azure específicas em contas de armazenamento de cache usadas para armazenar os dados replicados.
Rede Acelerada | Sem suporte | Uma VM com acelerado de rede habilitada pode ser replicada, mas o failover de VM não terá acelerado de rede habilitado. Rede acelerada também será desabilitada para a VM de origem em failback.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [diretrizes de rede para replicação de VMs do Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Inicie a proteção de suas cargas de trabalho [replicando VMs do Azure](site-recovery-azure-to-azure.md)
