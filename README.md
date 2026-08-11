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

## Serviços AWS utilizados

## Tech Stack & Lab Architecture

> Modern, observable, and automated infrastructure blueprint built on AWS.

| Capability / Service    | Role & Architecture Function |           Modern Implementation                                                            |
| :---                    | :---                         | :---                                                                                       |
| **Amazon EC2**          | Primary Monitored Target     | Ubuntu LTS Node running Apache HTTP Server (`t3.micro` / Auto-Recoverable)                 |
| **Amazon CloudWatch**   | Observability & Telemetry    | Unified Metrics, Dashboards, and Composite Alarms for proactive health tracking            |
| **CloudWatch Logs**     | Log Analytics & Retention    | Centralized stream indexing, JSON parsing, and real-time query insights                    |
| **CloudWatch Agent**    | Edge Telemetry Collector     | Lightweight daemon streaming OS-level metrics and custom log groups                        |
| **AWS Systems Manager** | Secure Fleet Operations      | Session Manager (Zero-SSH architecture), Patch Manager, and Run Command automation         |
| **Amazon SNS**          | Event Distribution Backbone  | Encrypted Topics routing operational alerts to downstream consumers (Email/Slack/Webhooks) |
| **Amazon EventBridge**  | Serverless Event Bus         | Real-time state-change filtering and automated routing rules                               |
| **AWS Config**          | Compliance & Guardrails      | Continuous resource evaluation against security frameworks and best practices              |
| **Amazon EBS**          | Block Storage & Evaluation   | Encrypted `gp3` volumes audited for compliance, backup status, and encryption-at-rest      |
| **IAM**                 | Identity & Access Governance | Least-privilege IAM Roles, Instance Profiles, and granular service policies                |

---

## Quick Architecture Overview

```mermaid
graph TD
    A[Amazon EC2] -->          |CloudWatch Agent         | B[CloudWatch Logs & Metrics]
    A -->                      |Evaluated by             | C [AWS Config & EBS]
    B -->                      |Triggers Alarms          | D[Amazon EventBridge]
    D -->                      |Dispatches               | E[Amazon SNS]
    F[AWS Systems Manager] -.->|Secure Ops / No SSH      | A
    G[IAM] -.->                |Secures Access           | A & B & F

## Objetivos do laboratório

Ao concluir o LAB 186 — Monitorar a Infraestrutura AWS, os principais objetivos alcançados foram:

Monitorar uma instância Amazon EC2, acompanhando seu comportamento e estado operacional.
Utilizar o Amazon CloudWatch para observabilidade e monitoramento da infraestrutura.
Instalar e configurar o CloudWatch Agent para coleta de dados diretamente da instância.
Coletar logs do servidor Apache e enviá-los para o CloudWatch Logs.
Consultar e analisar eventos HTTP, identificando diferentes códigos de resposta e padrões de acesso.
Configurar notificações com Amazon SNS para eventos relevantes da infraestrutura.
Utilizar o Amazon EventBridge / CloudWatch Events para detectar alterações no estado dos recursos.
Avaliar a conformidade dos recursos com AWS Config, utilizando regras gerenciadas.
Verificar requisitos de tags nos recursos AWS.
Avaliar a utilização de volumes Amazon EBS por meio de regras de conformidade.
Analisar permissões IAM durante o processo de configuração e troubleshooting.
Aplicar troubleshooting em ambiente AWS, investigando problemas, identificando causas, realizando correções e validando os resultados.
Principal aprendizado

O laboratório demonstrou que monitoramento não significa apenas visualizar métricas.

Uma infraestrutura realmente observável exige a integração entre métricas, logs, eventos, notificações, gerenciamento e conformidade, permitindo identificar problemas e tomar ações baseadas em evidências.

Problema → Investigação → Correção → Validação

Esse ciclo de troubleshooting foi um dos principais aprendizados práticos desenvolvidos durante o laboratório.
