---
title: Usar os laboratórios de sala de aula para treinamentos – Azure Lab Services
description: Este artigo descreve como usar o Azure DevTest Labs para criar laboratórios no Azure para cenários de treinamento.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 26caebafc7c147452decbb28e313513072d7511b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592025"
---
# <a name="use-classroom-labs-for-trainings"></a>Usar os laboratórios de sala de aula para treinamentos
O Azure Lab Services permite que educadores (professores, instrutores ou professores assistentes etc.) criem, de maneira rápida e fácil, um laboratório online para provisionar ambientes de aprendizado pré-configurados para os estagiários. Cada estagiário pode usar ambientes idênticos e isolados para o treinamento. É possível aplicar políticas para garantir que os ambientes de treinamento estejam disponíveis para cada estagiário apenas quando necessário e que contenham recursos suficientes, como máquinas virtuais, para o treinamento. 

![Laboratório de sala de aula](../media/classroom-labs-scenarios/classroom.png)

Os laboratórios de sala de aula atendem aos seguintes requisitos necessários para realizar treinamento em qualquer ambiente virtual: 

- Os estagiários podem provisionar seus ambientes de treinamento rapidamente
- Todas as máquinas de treinamento devem ser idênticas
- Os estagiários não conseguem ver VMs criadas por outros estagiários
- Controle os custos fazendo com que os estagiários não possam obter mais VMs do que o necessário para o treinamento e desligando VMs quando não estiverem em uso
- Compartilhe facilmente o laboratório de treinamento com os estagiários
- Reutilize o laboratório de treinamento várias vezes

Neste artigo, você aprenderá sobre os diversos recursos do Azure Lab Services, que podem ser usados para atender aos requisitos de treinamento descritos anteriormente, e sobre as etapas detalhadas a seguir para configurar um laboratório para treinamento.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Criar a conta de laboratório como um administrador da conta de laboratório
A primeira etapa no uso do Azure Lab Services é criar uma conta de laboratório no portal do Azure. Depois de criar a conta de laboratório, o administrador da conta de laboratório adiciona os usuários que desejam criar laboratórios à função **Criador de laboratório**. Os educadores criam laboratórios com máquinas virtuais para que os alunos façam exercícios do curso que estão ministrando. Para obter detalhes, confira [Criar e gerenciar conta de laboratório](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Criar e gerenciar laboratórios de sala de aula
Um educador, que é membro da função de criador de laboratório em uma conta de laboratório, pode criar um ou mais laboratórios nessa conta. Você cria e configura um modelo de VM (máquina virtual) com todo o software necessário para fazer exercícios no seu curso. Você escolhe uma imagem pronta entre as imagens disponíveis para criar um laboratório de sala de aula e, em seguida, o personaliza instalando o software necessário para o laboratório. Para obter detalhes, confira [Criar e gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurar políticas e configurações de uso
O criador pode adicionar ou remover usuários do laboratório, obter o link de registro para enviar aos usuários, configurar políticas (como cotas individuais por usuário), atualizar o número de VMs disponíveis no laboratório e muito mais. Para obter detalhes, confira [Configurar políticas e configurações de uso](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Criar e gerenciar agendas
Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Para obter detalhes, confira [Criar e gerenciar agendas para laboratórios de sala de aula](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurar e publicar um modelo de VM
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure o modelo de VM com tudo o que você deseja fornecer aos participantes do treinamento. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Em seguida, publique o modelo para tornar as instâncias do modelo de VM disponíveis para os usuários do laboratório. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. Para obter detalhes, confira [Configurar e publicar modelos de máquina virtual](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Usar VMs no laboratório de sala de aula
Um aluno ou um participante do treinamento registra-se no laboratório e se conecta à VM para fazer os exercícios do curso. Para obter detalhes, confira [Como acessar um laboratório de sala de aula](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Próximas etapas
Comece com a criação de uma conta de laboratório nos laboratórios de sala de aula, seguindo as instruções no artigo: [Tutorial: Configurar uma conta de laboratório com o Azure Lab Services](tutorial-setup-lab-account.md).