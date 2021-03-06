---
title: Fazer backup e restaurar VMs do Azure criptografadas
description: Descreve como fazer backup e restaurar VMs do Azure criptografadas com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: ea4d2830fb9db9f95ba8ab87626a79d94aaecb8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187929"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Fazer backup e restaurar a VM do Azure criptografada

Este artigo descreve como fazer backup e restaurar VMs (máquinas virtuais) do Azure do Windows ou Linux com discos criptografados usando o serviço de [backup do Azure](backup-overview.md) .

Se você quiser saber mais sobre como o backup do Azure interage com as VMs do Azure antes de começar, examine estes recursos:

- [Examine](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup da VM do Azure.
- [Saiba mais](backup-azure-vms-introduction.md) Backup de VM do Azure e a extensão de backup do Azure.

## <a name="encryption-support"></a>Suporte à criptografia

O backup do Azure dá suporte ao backup de VMs do Azure que têm seus discos de sistema operacional/dados criptografados com o Azure Disk Encryption (ADE). O ADE usa o BitLocker para criptografia de VMs do Windows e o recurso DM-cript para VMs do Linux. O ADE integra-se com Azure Key Vault para gerenciar chaves e segredos de criptografia de disco. Key Vault KEKs (chaves de criptografia de chave) podem ser usadas para adicionar uma camada adicional de segurança, criptografando segredos de criptografia antes de gravá-los no Key Vault.

O backup do Azure pode fazer backup e restaurar VMs do Azure usando ADE com e sem o aplicativo do Azure AD, conforme resumido na tabela a seguir.

**Tipo de disco da VM** | **ADE (BEK/DM-cript.)** | **ADE e KEK**
--- | --- | ---
**Não gerenciados** | Sim | Sim
**Gerenciados**  | Sim | Sim

- Saiba mais sobre [Ade](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/general/overview.md)e [KEKs](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Leia as [perguntas frequentes](../security/azure-security-disk-encryption-faq.md) sobre a criptografia de disco de VM do Azure.

### <a name="limitations"></a>Limitações

- Você pode fazer backup e restaurar VMs criptografadas dentro da mesma assinatura e região.
- O backup do Azure dá suporte a VMs criptografadas usando chaves autônomas. Atualmente, não há suporte para qualquer chave que faça parte de um certificado usado para criptografar uma VM.
- Você pode fazer backup e restaurar VMs criptografadas dentro da mesma assinatura e região que o cofre de backup dos serviços de recuperação.
- As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você precisa recuperar toda a VM para restaurar arquivos e pastas.
- Ao restaurar uma VM, você não pode usar a opção [substituir VM existente](backup-azure-arm-restore-vms.md#restore-options) para VMs criptografadas. Essa opção só tem suporte para discos gerenciados não criptografados.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, faça o seguinte:

1. Verifique se você tem uma ou mais VMs do [Windows](../security/azure-security-disk-encryption-windows.md) ou [Linux](../virtual-machines/linux/disk-encryption-overview.md) com o Ade habilitado.
2. [Examinar a matriz de suporte para o](backup-support-matrix-iaas.md) backup de VM do Azure
3. [Crie](backup-azure-arm-vms-prepare.md#create-a-vault) um cofre de backup dos serviços de recuperação se você não tiver um.
4. Se você habilitar a criptografia para VMs que já estão habilitadas para backup, bastará fornecer backup com permissões para acessar o Key Vault para que os backups possam continuar sem interrupções. [Saiba mais](#provide-permissions) sobre como atribuir essas permissões.

Além disso, há algumas coisas que você pode precisar fazer em algumas circunstâncias:

- **Instalar o agente de VM na VM: o**backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se sua VM foi criada a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se você criar uma VM personalizada ou migrar um computador local, talvez seja necessário [instalar o agente manualmente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

1. Se você ainda não criou um cofre de backup dos serviços de recuperação, siga [estas instruções](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Abra o cofre no portal e selecione **backup** na seção **introdução** .

    ![Folha Backup](./media/backup-azure-vms-encryption/select-backup.png)

3. Em **meta** > de backup**em que sua carga de trabalho está em execução?** selecione **Azure**.
4. Em **o que você deseja fazer backup?** selecione a **máquina** > virtual**OK**.

      ![Folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Em **política** > de backup,**escolha política de backup**, selecione a política que você deseja associar ao cofre. Em seguida, clique em **OK**.
    - Uma política de backup especifica quando os backups são feitos e por quanto tempo eles são armazenados.
    - Os detalhes da política padrão estão listados no menu suspenso.

    ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se você não quiser usar a política padrão, selecione **criar nova**e [criar uma política personalizada](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Escolha as VMs criptografadas que você deseja fazer backup usando a política selecionar e selecione **OK**.

      ![Selecionar VMs criptografadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se estiver usando Azure Key Vault, na página do cofre, você verá uma mensagem informando que o backup do Azure precisa de acesso somente leitura às chaves e aos segredos no Key Vault.

    - Se você receber essa mensagem, nenhuma ação será necessária.

        ![Acesso OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se você receber essa mensagem, precisará definir permissões conforme descrito no [procedimento abaixo](#provide-permissions).

        ![Aviso de acesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Clique em **habilitar backup** para implantar a política de backup no cofre e habilite o backup para as VMs selecionadas.

## <a name="trigger-a-backup-job"></a>Disparar um trabalho de backup

O backup inicial será executado de acordo com o agendamento, mas você poderá executá-lo imediatamente da seguinte maneira:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **itens de backup**, clique em **máquina virtual do Azure**.
3. Na lista **itens de backup** , clique nas reticências (...).
4. Clique em **Fazer backup agora**.
5. Em **fazer backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser retido. Em seguida, clique em **OK**.
6. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **trabalhos** > **de backup em andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.

## <a name="provide-permissions"></a>Fornecer permissões

O backup do Azure precisa de acesso somente leitura para fazer backup das chaves e dos segredos, juntamente com as VMs associadas.

- Seu Key Vault está associado ao locatário do Azure AD da assinatura do Azure. Se você for um **usuário membro**, o backup do Azure adquire acesso ao key Vault sem ação adicional.
- Se você for um **usuário convidado**, deverá fornecer permissões para o backup do Azure acessar o cofre de chaves.

Para definir permissões:

1. Na portal do Azure, selecione **todos os serviços**e procure por **cofres de chaves**.
2. Selecione o cofre de chaves associado à VM criptografada que você está fazendo backup.
3. Selecione **políticas** > de acesso**Adicionar novo**.
4. Selecione **selecionar entidade de segurança**e digite **Gerenciamento de backup**.
5. Selecione **serviço** > de gerenciamento de backup**selecionar**.

    ![Seleção de serviço de backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Em **Adicionar política** > **de acesso configurar do modelo (opcional)**, selecione **backup do Azure**.
    - As permissões necessárias para **Permissões de chave** e **Permissões de segredo** são preenchidas.
    - Se sua VM for criptografada usando **apenas Bek**, remova a seleção de **permissões de chave** , pois você só precisa de permissões para segredos.

    ![Seleção de backup do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Clique em **OK**. O **serviço de gerenciamento de backup** é adicionado às políticas de **acesso**.

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Clique em **salvar** para fornecer ao Azure o backup com as permissões.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM criptografada

Você restaura as VMs criptografadas da seguinte maneira:

1. [Restaure o disco da VM](backup-azure-arm-restore-vms.md#restore-disks).
2. Recrie a instância de máquina virtual seguindo um destes procedimentos:
    1. Use o modelo gerado durante a operação de restauração para personalizar as configurações da VM e disparar a implantação da VM. [Saiba mais](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Crie uma nova VM com base nos discos restaurados usando o PowerShell. [Saiba mais](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Para VMs do Linux, reinstale a extensão ADE para que os discos de dados sejam abertos e montados.

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas, leia estes artigos:

- [Erros comuns](backup-azure-vms-troubleshoot.md) ao fazer backup e restaurar VMs do Azure criptografadas.
- Problemas de [extensão de backup/agente de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) .
