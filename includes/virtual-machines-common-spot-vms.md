---
title: incluir arquivo
description: incluir arquivo
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4063751a71cd9cecc424dfe3daddaecfd9ea4071
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81421748"
---
O uso de VMs pontuais permite que você tire proveito de nossa capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuais. Portanto, as VMs pontuais são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

A quantidade de capacidade disponível pode variar com base no tamanho, região, hora do dia e muito mais. Ao implantar VMs Spot, o Azure alocará as VMs se houver capacidade disponível, mas não haverá SLA para essas VMs. Uma VM Spot não oferece nenhuma garantia de alta disponibilidade. A qualquer momento quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuadas com um aviso de 30 segundos. 


## <a name="eviction-policy"></a>Política de remoção

As VMs podem ser removidas com base na capacidade ou no preço máximo definido. Para máquinas virtuais, a política de remoção é definida como *desalocar* que move suas VMs removidas para o estado parado e desalocado, permitindo que você reimplante as VMs removidas posteriormente. No entanto, a realocação de VMs pontuais dependerá da capacidade Spot disponível. As VMs desalocadas serão contadas em relação à sua cota vCPU Spot e você será cobrado pelos discos subjacentes. 

Os usuários podem optar por receber notificações na VM por meio [do Azure eventos agendados](../articles/virtual-machines/linux/scheduled-events.md). Isso notificará você se suas VMs estiverem sendo removidas e você terá 30 segundos para concluir todos os trabalhos e realizar tarefas de desligamento antes da remoção. 


| Opção | Resultado |
|--------|---------|
| O preço máximo é definido como >= o preço atual. | A VM será implantada se a capacidade e a cota estiverem disponíveis. |
| O preço máximo é definido para < o preço atual. | A VM não está implantada. Você receberá uma mensagem de erro informando que o preço máximo precisa ser >= preço atual. |
| Reiniciar uma VM de parar/desalocar se o preço máximo for >= o preço atual | Se houver capacidade e cota, a VM será implantada. |
| Reiniciar uma VM de parar/desalocar se o preço máximo for < o preço atual | Você receberá uma mensagem de erro informando que o preço máximo precisa ser >= preço atual. | 
| O preço da VM foi concluído e agora está > o preço máximo. | A VM é removida. Você Obtém uma notificação de 30s antes da remoção real. | 
| Após a remoção, o preço da VM volta a ser < o preço máximo. | A VM não será reiniciada automaticamente. Você pode reiniciar a VM por conta própria e ela será cobrada com o preço atual. |
| Se o preço máximo for definido como`-1` | A VM não será removida por motivos de preço. O preço máximo será o preço atual, até o preço das VMs padrão. Você nunca será cobrado acima do preço padrão.| 
| Alterando o preço máximo | Você precisa desalocar a VM para alterar o preço máximo. Desaloque a VM, defina t um novo preço máximo e, em seguida, atualize a VM. |

## <a name="limitations"></a>Limitações

Os seguintes tamanhos de VM não têm suporte para VMs spot:
 - Série B
 - Versões promocionais de qualquer tamanho (como Dv2, NV, NC, tamanhos promocionais de H)

As VMs pontuais não podem usar atualmente discos do sistema operacional efêmero.

As VMs pontuais podem ser implantadas em qualquer região, exceto Microsoft Azure a 21Vianet da China.

## <a name="pricing"></a>Preços

Os preços para VMs pontuais são variáveis, com base na região e SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


Com o preço variável, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0.98765 USD por hora. Se você definir o preço máximo como `-1`, a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.


##  <a name="frequently-asked-questions"></a>Perguntas frequentes

**P:** Depois de criada, é uma VM Spot a mesma que a VM normal padrão?

**R:** Sim, exceto que não há SLA para VMs pontuais e elas podem ser removidas a qualquer momento.


**P:** O que fazer ao ser removido, mas ainda precisa de capacidade?

**R:** Recomendamos que você use VMs padrão em vez de VMs pontuais se precisar de capacidade imediatamente.


**P:** Como a cota é gerenciada para VMs pontuais?

**R:** As VMs pontuais terão um pool de cotas separado. A cota de spot será compartilhada entre as VMs e as instâncias do conjunto de dimensionamento. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**P:** Posso solicitar uma cota adicional para o ponto?

**R:** Sim, você poderá enviar a solicitação para aumentar sua cota de VMs pontuais por meio do [processo de solicitação de cota padrão](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**P:** Quais canais dão suporte a VMs pontuais?

**R:** Consulte a tabela abaixo para encontrar a disponibilidade da VM.

<a name="channel"></a>

| Canais do Azure               | Disponibilidade de VMs de ponto do Azure       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Sim                               |
| Pago Conforme o Uso                | Sim                               |
| Provedor de Serviços de Nuvem (CSP) | [Entre em contato com seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Contrato de Cliente da Microsoft | Sim                               |
| Vantagens                     | Não disponível                     |
| Patrocinado                    | Não disponível                     |
| Avaliação gratuita                   | Não disponível                     |


**P:** Onde posso postar perguntas?

**R:** Você pode postar e marcar sua pergunta com `azure-spot` o [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



