<div align="center">
 
## AWS Infrastructure Observability
Subi uma EC2 pra monitorar com CloudWatch. O agente disse que tava ativo. Mentiu.
Esse repo é o que aconteceu depois.

</div>
<p align="center">

 <p align="center">
  <img src="https://img.shields.io/badge/AWS-Cloud-orange?style=for-the-badge&logo=amazonaws&logoColor=white">
  <img src="https://img.shields.io/badge/Amazon%20EC2-Compute-orange?style=for-the-badge&logo=amazonaws&logoColor=white">
  <img src="https://img.shields.io/badge/CloudWatch-Monitoring-blue?style=for-the-badge&logo=amazoncloudwatch&logoColor=white">
  <img src="https://img.shields.io/badge/CloudWatch%20Logs-Observability-blue?style=for-the-badge&logo=amazonaws&logoColor=white">
  <img src="https://img.shields.io/badge/AWS%20Config-Compliance-purple?style=for-the-badge&logo=amazonaws&logoColor=white">
  <img src="https://img.shields.io/badge/Amazon%20SNS-Notifications-red?style=for-the-badge&logo=amazonaws&logoColor=white">
</p>

<p align="center">
  <img src="capa_lab.png"
       alt="AWS Infrastructure Observability"
       width="650">
</p>


## Sobre o Laboratório. O que é isso.

Lab prático feito durante o AWS re/Start. O objetivo era montar um pipeline de observabilidade numa instância EC2 com Apache: métricas no CloudWatch, logs centralizados, alertas por e-mail e verificação de conformidade.
Na teoria, é só ligar uns serviços. Na prática, eu passei 40 minutos descobrindo por que o CloudWatch Agent não enviava nada sendo que o status tava "active".
Spoiler: era permissão de diretório. Coisa boba. Coisa que só descobre na marra.

## Tarefas Executadas

O laboratório foi desenvolvido em cinco etapas, integrando monitoramento, observabilidade, coleta de logs, notificações, gerenciamento e conformidade em um ambiente AWS.

## Etapa 1: A instância e o Apache

Subi uma EC2 na us-east-1 com Amazon Linux e Apache HTTPD rodando. Servidor web básico, gerando logs em /var/log/httpd/access_log.
O que eu verifiquei de cara:
Instância no ar ✅
Apache respondendo requests ✅
Logs sendo gerados ✅
Pensei: "Beleza, parte fácil. Agora vem o CloudWatch."

## Etapa 2: Monitoramento e alertas

A regra foi configurar para identificar alterações nos estados:
Configurei o EventBridge pra detectar mudanças de estado da instância

stopped
terminated

O Amazon SNS foi utilizado como mecanismo de entrega das notificações.

Quando isso acontece, dispara uma notificação via SNS pro meu e-mail.

Teste: Parei a instância manualmente.

Resultado: E-mail chegou em segundos. Funcionou de primeira.
Aí eu me senti confiante demais.

O objetivo foi demonstrar como eventos operacionais podem ser detectados e encaminhados automaticamente para os responsáveis pelo ambiente.


## Etapa 3: O inferno do CloudWatch Agent

Instalei o agente na instância. Verifiquei o status:

sudo systemctl is-active amazon-cloudwatch-agent

### active

"Active". Maravilha. Fui no console AWS abrir o CloudWatch Logs... nada. Nenhuma Log Stream. Nenhum evento no grupo HttpAccessLog.
O agente tava rodando, mas não enviava nada.

Investigação
O agente precisa ler /var/log/httpd/access_log. Fui verificar as permissões:

ls -la /var/log/httpd/

### drwx------ 2 root root ... /var/log/httpd
### -rw-r--r-- 1 root root ... access_log

O arquivo access_log tinha permissão de leitura. Mas o diretório pai (/var/log/httpd) tava com drwx------ e grupo root. O agente roda como usuário cwagent — ele nem conseguia entrar no diretório.

Esta foi uma das principais etapas do laboratório.
O servidor Apache disponibiliza seus registros de acesso no arquivo:

### A correção

### Mudei o grupo do diretório pro usuário do agente
sudo chgrp cwagent /var/log/httpd

### Ajustei permissões para o grupo poder ler/entrar
sudo chmod 750 /var/log/httpd

### Testei se o agente consegue ler agora
sudo -u cwagent tail -n 3 /var/log/httpd/access_log

### Reiniciei o agente
sudo systemctl restart amazon-cloudwatch-agent
sudo systemctl is-active amazon-cloudwatch-agent

### active

Fui no console. Os logs começaram a chegar.
O que eu aprendi: active não significa funcionando. Sempre valida a saída, não só o status do serviço.

### Evidências do pipeline funcionando

| O que tá acontecendo                        | Print                                      |
| ------------------------------------------- | ------------------------------------------ |
| Dashboard do CloudWatch com métricas da EC2 | `images/cloudwatch-dashboard.png`          |
| Fluxo: Apache → Agent → CloudWatch Logs     | `images/apache-to-cloudwatch-pipeline.png` |
| Notificação SNS quando a instância parou    | `images/eventbridge-sns-notification.png`  |
| Log Stream ativa recebendo registros reais  | `images/cloudwatch-log-stream.png`         |



<img src ="apache-cloudwatch-logs-pipeline.png" width="500"> </div>



A validação confirmou a chegada de eventos HTTP reais ao CloudWatch Logs.

Foram observados diferentes códigos de resposta, incluindo:

200 — requisição processada com sucesso;
403 — acesso proibido;
404 — recurso não encontrado;
400 — requisição inválida.


### Etapa 4: O que os logs revelaram

Depois que funcionou, fiquei olhando os logs no CloudWatch. Códigos HTTP normais:

GET /icons/apache_pb2.gif HTTP/1.1" 200
GET / HTTP/1.1" 403
GET /pagina-que-nao-existe HTTP/1.1" 404



## Mas também vi isso:

GET /wp-content/plugins/... HTTP/1.1" 404
GET /admin.php HTTP/1.1" 404
GET /wp-ws68.php HTTP/1.1" 404
GET /img.php HTTP/1.1" 404


Bots. Varredura automatizada. Minha instância tava na internet há menos de 30 minutos e já tinham achado ela.
Isso me fez perceber: monitoramento não é só ver se tá no ar. É saber quem tá batendo na porta, com que frequência, e se sua configuração aguenta o tranco.




<p align="center">
  <img src="eventbridge_sns_flow.png"
       alt="Fluxo de eventos entre Amazon EC2, EventBridge, Amazon SNS e e-mail"
       width="550">
</p>




### Etapa 5: Conformidade com AWS Config

Ativei o AWS Config pra rodar duas regras gerenciadas:

required-tags — os recursos têm as tags obrigatórias?
ec2-volume-inuse-check — os volumes EBS estão realmente anexados a alguma instância?

Funcionou, mas com uma ressalva: o ambiente do lab tinha restrições de IAM. Quando tentei ver os Configuration Recorders, tomei um access denied:

config:DescribeConfigurationRecorders — not authorized
s3:ListAllMyBuckets — explicit deny


## Evidência

A captura abaixo registra a mensagem de negação de permissão observada durante a execução:

<p align="center"> <img src="permissao_negada.png" alt="Restrição de permissão durante a configuração do AWS Config" width="750"> </p>

Observação: a evidência demonstra uma restrição de autorização existente no ambiente de laboratório. Ela não caracteriza, por si só, uma falha do serviço AWS Config.


## Tech Stack

AWS Services

<p align="center">

<img src="https://img.shields.io/badge/Amazon%20EC2-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/Amazon%20CloudWatch-FF4F8B?style=for-the-badge&logo=amazoncloudwatch&logoColor=white"> <img src="https://img.shields.io/badge/CloudWatch%20Logs-FF4F8B?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/CloudWatch%20Agent-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/AWS%20Systems%20Manager-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/Amazon%20SNS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/Amazon%20EventBridge-FF4F8B?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/AWS%20Config-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/Amazon%20EBS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white"> <img src="https://img.shields.io/badge/IAM-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white">


</p>


<p align="center">
  <img src="arquitetura_solucao.png"
       alt="Arquitetura da solução do LAB 186"
       width="700">
</p>



### Technologies & Components

| Component | Application in the Lab |
|---|---|
| Amazon EC2 | Web Server utilizado como recurso principal monitorado |
| Apache HTTPD | Servidor Web responsável pela geração dos logs |
| CloudWatch Agent | Coleta dos logs do Apache diretamente da instância |
| CloudWatch Logs | Armazenamento e análise dos eventos coletados |
| Amazon EventBridge | Detecção de eventos relacionados à infraestrutura |
| Amazon SNS | Envio de notificações |
| AWS Systems Manager | Gerenciamento da instância e troubleshooting |
| AWS Config | Avaliação da conformidade dos recursos |
| Amazon EBS | Volumes avaliados pelas regras de conformidade |
| IAM | Controle de permissões e acesso aos recursos |

O laboratório demonstrou que monitoramento não significa apenas visualizar métricas.

Uma infraestrutura realmente observável exige a integração entre métricas, logs, eventos, notificações, gerenciamento e conformidade, permitindo identificar problemas e tomar ações baseadas em evidências.


## Evidência do troubleshooting


<img src="troubleshooting.png" alt="Troubleshooting do CloudWatch Agent" width="750"> </p>


Resultado:

O problema foi identificado como uma restrição de acesso ao diretório de logs, corrigido por meio do ajuste de grupo e permissões e posteriormente validado no CloudWatch Logs.

Problema → Investigação → Correção → Validação

Esse ciclo de troubleshooting foi um dos principais aprendizados práticos desenvolvidos durante o laboratório.


## O que esse lab realmente me ensinou

Não foi "como usar o CloudWatch". Foi:
Troubleshooting é 80% do trampo. O agente tava "ativo" e não funcionava. O problema era permissão de diretório. Coisa simples, mas que te faz perder tempo se não souber onde olhar.
Logs contam histórias. Além de requests normais, eu vi tentativas de ataque em tempo real. Observabilidade não é luxo — é segurança.
IAM é poder e dor. Uma política Deny invisível me bloqueou no Config. Entender como as policies se sobrepõem é essencial.
Arquitetura no papel ≠ arquitetura na prática. O diagrama é lindo. Fazer o log chegar no CloudWatch é outra história.

## 🚧 Status

[x] Lab concluído
[x] Troubleshooting documentado com comandos reais
[x] Prints e evidências anexados
[ ] Refazer isso tudo em Terraform (em breve)

## 🌐 Vamos conversar?
Se você é recrutador, mentor ou alguém também quebrando a cabeça com CloudWatch Agent, bora trocar ideia.
💼 LinkedIn: linkedin.com/in/eliana-diniz
📧 E-mail: eliana.dinizsilva@gmail.com


## Direção visual e imagens

A identidade visual deste projeto, incluindo a concepção das imagens, composição dos elementos, definição da estética e elaboração dos prompts, foi idealizada por mim para representar visualmente a arquitetura e os conceitos técnicos desenvolvidos durante o laboratório.

As imagens foram geradas e refinadas com auxílio do ChatGPT, a partir de prompts elaborados especificamente para este projeto.
Tecnologia + criatividade + prática = aprendizado aplicado.

<p align="center">

From Infrastructure to Observability.

AWS Cloud Practitioner Journey
</p>  
