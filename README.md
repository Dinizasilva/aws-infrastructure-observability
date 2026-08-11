# aws-cloudwatch-infrastructure-monitoring
AWS infrastructure monitoring, log management, event-driven notifications, compliance and troubleshooting with CloudWatch, EC2, SNS, EventBridge and AWS Config.

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

## Sobre o laboratório

O LAB 186 — Monitorar a Infraestrutura AWS apresenta, na prática, como utilizar serviços da AWS para monitorar recursos computacionais, coletar e analisar logs, identificar eventos operacionais, gerar notificações e avaliar a conformidade da infraestrutura.

Durante o laboratório, uma instância Amazon EC2 Web Server foi utilizada como recurso principal para demonstrar um cenário integrado de monitoramento e observabilidade.

A atividade envolveu a integração de diferentes serviços da AWS, permitindo acompanhar o ambiente desde a geração dos eventos no servidor até sua coleta, análise, notificação e avaliação de conformidade.

## Principais componentes

Amazon EC2 — recurso computacional monitorado
Amazon CloudWatch — monitoramento e observabilidade
CloudWatch Logs — coleta, armazenamento e análise de logs
CloudWatch Agent — coleta de logs diretamente da instância
AWS Systems Manager — gerenciamento e troubleshooting da instância
Amazon SNS — envio de notificações
Amazon EventBridge / CloudWatch Events — detecção de eventos operacionais
AWS Config — avaliação de conformidade
Amazon EBS — volumes avaliados pelas regras de conformidade
IAM — gerenciamento de permissões

## Objetivo

O laboratório teve como foco desenvolver uma visão prática de Cloud Operations e Observabilidade na AWS, demonstrando não apenas a configuração dos serviços, mas também a capacidade de investigar problemas, identificar causas, aplicar correções e validar tecnicamente os resultados.

## Arquitetura da Solução

A arquitetura abaixo representa a integração dos principais serviços AWS utilizados no laboratório, desde o monitoramento da instância EC2 e coleta dos logs do Apache até o processamento de eventos, notificações e avaliação de conformidade.

<p align="center">
  <img src="arquitetura_solucao.png"
       alt="Arquitetura da solução do LAB 186"
       width="650">
</p>
