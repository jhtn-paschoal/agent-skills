---
name: integracao-webhooks
description: Desacoplamento assíncrono de payloads e imposição de tempo de resposta imediato (HTTP 200).
---

# Padrão Arquitetural: Resiliência em Integrações e Webhooks

Esta skill impõe o desacoplamento de responsabilidades na recepção e processamento de dados externos para evitar falhas de timeout.

1. Retorno HTTP Imediato:
- Rotas (Route Handlers ou Express Controllers) desenhadas para capturar webhooks de plataformas de automação (como n8n) devem validar a assinatura do payload e retornar status HTTP 200 (OK) em menos de 3 segundos.
- O processamento da carga útil deve ser isolado do ciclo de resposta da requisição.

2. Desacoplamento de Processamento:
- Tarefas de longa duração engatilhadas por webhooks, como cruzamento de dados de assinaturas ativas, cálculo de churn e sincronização de registros com listas do ClickUp, não podem bloquear a thread principal. O agente deve projetar essas operações para rodar em background workers, filas (message queues) ou invocar endpoints de processamento assíncrono.

3. Imutabilidade e Tratamento de Payload:
- O agente deve tratar a carga recebida como imutável e gerar logs transacionais em caso de falha de parsing. A ausência de tipagem na entrada do webhook resultará em rejeição imediata da proposta de código.