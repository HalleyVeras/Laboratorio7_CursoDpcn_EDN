<div align="center" style="font-family: Arial, sans-serif; font-size: 20px; line-height: 1.5;">

# 🌟 **Curso Dpcn-EDN** 🌟
# 🌟 Solutions Architect Associate  🌟

###
<a href="https://escoladanuvem.org"><a href="https://aws.amazon.com/pt/?nc2=h_lg">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/Amazon_Web_Services_Logo.svg/2560px-Amazon_Web_Services_Logo.svg.png" width="180" alt="AWS Logo">
</a>
<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRUyYcSb419JTRt9roMB682vIBhG-H_OUuvNw&s" width="80" alt="AWS Logo">- <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSpiiBVPv8hPlWDzWtGBtSbBGVmYR9M3v2K1Q&s" width="80" alt="AWS Logo">-<img src="https://files.svgcdn.io/logos/aws-ec2.svg" width="80" alt="AWS Logo">-<img src="https://cdn.worldvectorlogo.com/logos/amazon-s3-simple-storage-service.svg" width="80" alt="AWS Logo">-
<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRRsHozuh-tDR3wCxV5bR-TI04bRbnVmfISpA&s" width="80" alt="AWS Logo">-
    <img src="https://github.com/HalleyVeras/Escola_da_Nuvem/blob/main/Documentos/download%20(4)_processed.png?raw=true" width="210" alt="Second Image">
</a>
</div>

# Laboratório 7: Monitoramento e Auditoria com CloudWatch e CloudTrail
**Turma:** Dpcn 07  
**Aluno:** Halley Veras 

## Objetivos:
- Configurar um **Alarme CloudWatch** para monitorar a CPU de uma instância EC2 e enviar notificações por e-mail (SNS) quando um limite for atingido.
- Habilitar o **CloudTrail** para auditoria (rastreamento de eventos) na sua conta AWS e armazenar os logs em um bucket S3.
- Visualizar os logs do CloudTrail.

## Cenário:
Você precisa monitorar o desempenho de uma instância EC2 e ser alertado sobre alta utilização de CPU. Também precisa manter um registro de auditoria de todas as atividades na sua conta AWS para segurança e conformidade.

## Pré-requisitos:
- **Conta AWS**: Conta ativa com permissões para EC2, CloudWatch, CloudTrail, SNS e S3.
- **Permissões IAM**: `CloudWatchFullAccess`, `AWSCloudTrail_FullAccess`, `AmazonSNSFullAccess`, `AmazonS3FullAccess`, `AmazonEC2FullAccess`.
- **Navegador Web**.

---

## Passo 1: Criar uma Instância EC2

1. **Console EC2**: Acesse o console do EC2.
2. **Instância de lançamento**:
   - **Nome**: `Instancia-Teste-CloudWatch`.
   - **AMI**: `Amazon Linux 2 (HVM) - x86_64`.
   - **Tipo de instância**: `t2.micro`.
   - **Key pair**: Selecione ou crie um par de chaves. (Você precisará acessar a instância, então saiba onde está salvo o seu par de chaves).
   - **Configurações de rede**:
     - **VPC**: Sua VPC padrão.
     - **Subnet**: Qualquer sub-rede pública.
     - **Atribuição automática de IP público**: Habilitar.
   - **Firewall**:
     - Crie um grupo de segurança:
       - **Name**: `SG-Teste-CloudWatch-SeuNome`.
       - **Entrada**: SSH, Fonte: Meu IP.
   - **Configure storage**: Padrão.
   - **Detalhes avançados**: Nada.
3. **Instância de lançamento**.
4. **Anote o Instance ID**.

**Espaço para print 1**: [Adicione um print da instância criada aqui]

---

## Passo 2: Configurar um Alarme no CloudWatch

1. **Console CloudWatch**: Acesse o console do CloudWatch.
2. **Criar Alarme**:
   - **Alarmes** -> **Criar alarme**.
   - **Selecione a métrica**:
     - **Todas as métricas** -> **EC2** -> **Métricas por instância**.
     - Cole o **ID da instância** (anotada do passo anterior) na caixa de pesquisa.
     - Marque **CPUUtilization** da sua instância.
     - **Métricas gráficas Aba (1)**: Verifique.
     - Selecione a métrica.
   - **Especifique a métrica e as condições**:
     - **Estatística**: Média.
     - **Período**: 5 minutos.
     - **Condições**:
       - **Tipo de limite**: Estático.
       - **Sempre que CPUUtilization for...**: Maior que...: 70.
     - **Configuração adicional**:
       - **Pontos de dados para alarme**: 1 de 1.
       - **Tratamento de dados ausentes**: trate os dados ausentes como bons (sem ultrapassar o limite).
   - **Próximo**.
3. **Configurar ações**:
   - **Disparador de estado de alarme**: Em alarme.
   - **Selecione um tópico SNS**: Crie um tópico SNS (confirme a assinatura).
   - **Próximo**.
4. **Adicione um nome e uma descrição**:
   - **Alarm name**: `AlarmeCPU-Instancia-SeuNome`.
   - **Próximo**.
5. **Visualizar e criar**: Revisar e Criar alarme.

**Espaço para print 2**: [Adicione um print do alarme configurado aqui]

---

## Passo 3: Configurar o CloudTrail

1. **Console CloudTrail**: Acesse o console do CloudTrail.
2. **Criar uma Trilha (Trail)**:
   - **Trilhas** -> **Criar trilha**.
3. **Etapa 1: Escolha os atributos da trilha**:
   - **Trail name**: `trilha-auditoria-seunome`.
   - **Local de armazenamento**:
     - Crie um novo bucket S3.
     - **Trail log bucket and folder**: O CloudTrail sugerirá um nome.
     - **Criptografia SSE-KMS do arquivo de log**: Marcado (Habilitado).
     - **Chave AWS KMS gerenciada pelo cliente**: Novo.
     - **AWS KMS alias**: `alias/CloudTrail Key-seunome`.
   - **Configurações adicionais**:
     - **Log file validation**: Deixe habilitado.
     - **SNS notification delivery**: Deixe desabilitado.
     - **CloudWatch Logs**: Deixe desabilitado.
   - **Clique em Next**.
4. **Etapa 2: Escolha os eventos de log**:
   - **Tipo de evento**:
     - **Management events**: Mantenha marcado.
     - **Atividade da API**: Manter Leitura e Escrita.
     - **Eventos de dados**: Desmarque.
     - **Eventos Insights**: Desmarque.
     - **Eventos de atividade de rede**: Desmarque.
   - **Clique em Next**.
5. **Etapa 3: Revise e crie**: Revise e crie trilha.

**Espaço para print 3**: [Adicione um print da trilha criada aqui]

---

## Passo 4: Visualizar Logs do CloudTrail

1. **Logs do CloudTrail (no S3 - Padrão)**:
   - Acesse o console do S3.
   - Localize o bucket criado pelo CloudTrail.
   - Navegue pela estrutura de pastas para encontrar os logs (arquivos .gz JSON).
2. **(Opcional) Enviar Logs do CloudTrail para o CloudWatch Logs**:
   - **Console do CloudTrail** -> **Trails** -> **Sua trilha**.
   - **Seção CloudWatch Logs** -> **Edit**.
   - **CloudWatch Logs**: Marque (Habilitado).
   - **Log group**: Crie um novo (ex: `CloudTrail/Logs-SeuNome`).
   - **IAM Role**: Crie um novo: `CloudTrail RoleForCloudWatch Logs`, clique em Allow.
   - **Salve as alterações**.
3. **Logs no CloudWatch Logs (se configurado)**:
   - **Console CloudWatch**: **Logs** -> **Grupos de logs**.
   - **Visualizar Logs**: Clique no grupo de logs.

**Espaço para print 4**: [Adicione um print dos logs aqui]

---

## Passo 5: Testar o Alarme de CPU

1. **Conectar-se à Instância (SSH)**: Use SSH para acessar a instância.
2. **Instalar o stress**:
   ```bash
   sudo yum update -y
   sudo amazon-linux-extras install epel -y # Amazon Linux 2
   sudo yum install stress -y

3. Execute o **stress test** para simular alta CPU:

```bash
stress --cpu 8 --timeout 600
```

4. Monitore o **CloudWatch** e aguarde o e-mail de alerta.
5. Finalize o stress com `CTRL+C`.

📸 *Inserir print do e-mail de alerta.*

---

✅ **Fim do laboratório!**
