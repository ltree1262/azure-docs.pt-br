---
title: Série NDv2-máquinas virtuais do Azure
description: Especificações para as VMs da série NDv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247275"
---
# <a name="updated-ndv2-series"></a>Série NDv2 atualizada

A máquina virtual da série NDv2 é uma nova adição à família de GPU projetada para as necessidades das cargas de trabalho mais exigentes do ia, do aprendizado de máquina, da simulação e do HPC com maior rapidez.

A NDv2 é alimentada por oito GPUs NVIDIA Tesla V100 NVLINK conectadas, cada uma com 32 GB de memória de GPU. Cada VM NDv2 também tem 40 núcleos não hiperthreads Intel Xeon Platinum 8168 (Skylake) e 672 GiB de memória do sistema.

As instâncias de NDv2 fornecem excelente desempenho para cargas de trabalho do HPC e do AI utilizando kernels de computação otimizados para GPU CUDA e as muitas ferramentas de ia, ML e análise que dão suporte à aceleração de GPU "pronta para uso", como TensorFlow, Pytorch, Caffe, RAPIDS e outras estruturas.

De forma crucial, o NDv2 é criado para expansão computacionalmente intensa (aproveitando 8 GPUs por VM) e escalar horizontalmente (aproveitando várias VMs trabalhando juntas) cargas de trabalho. A série NDv2 agora dá suporte à rede de back-end InfiniBand EDR de 100-Gigabit, semelhante àquela disponível na série HB da VM HPC, para permitir o clustering de alto desempenho para cenários paralelos, incluindo treinamento distribuído para ia e ML. Essa rede de back-end dá suporte a todos os principais protocolos InfiniBand, incluindo aqueles empregados pelas bibliotecas NCCL2 da NVIDIA, permitindo um clustering contínuo de GPUs.

> Ao [habilitar a InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na VM ND40rs_v2, use o driver 4.7-1.0.0.1 Mellanox ofed.
>
> Devido à maior memória da GPU, o novo ND40rs_v2 VM requer o uso de [VMs de geração 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) e imagens do Marketplace. 
>
> Observação: o ND40s_v2 incluindo 16 GB de memória por GPU não está mais disponível para visualização e foi substituído pelo ND40rs_v2 atualizado.

<br>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Migração ao Vivo: sem suporte

Atualizações de preservação de memória: sem suporte

InfiniBand: com suporte

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Largura de banda de rede máxima | Máximo de NICs |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

Para aproveitar os recursos de GPU das VMs da série N do Azure, os drivers NVIDIA GPU devem ser instalados.

A [Extensão de Driver de GPU NVIDIA](./extensions/hpccompute-gpu-linux.md) instala drivers CUDA ou GRID NVIDIA apropriados em VMs da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Para obter informações gerais sobre extensões de VM, confira [Recursos e extensões de máquina virtual do Azure](./extensions/overview.md).

Se você optar por instalar os drivers NVIDIA GPU manualmente, consulte [instalação do driver de GPU da série N para Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
