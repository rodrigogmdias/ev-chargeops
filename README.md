# ⚡ EV ChargeOps

**Enterprise Challenge 2026 — FIAP × GoodWe**

Plataforma para gestão de recarga de veículos elétricos em infraestrutura compartilhada: sessões por usuário, rateio justo por kWh e inteligência operacional.

---

## 👥 Equipe

| Integrante | RM | Função |
|---|---|---|
| [Nome completo] | [RM000000] | [Papel] |
| [Nome completo] | [RM000000] | [Papel] |
| [Nome completo] | [RM000000] | [Papel] |

**Squad:** [Nome da squad]  
**Repositório:** https://github.com/rodrigogmdias/ev-chargeops

---

## 1. 🎯 Problema e contexto

A GoodWe, em parceria com a FIAP (Energy Innovation Lab — Aclimação), opera um carregador **HCA G2** no estacionamento L1. O desafio é transformar sessões de recarga em infraestrutura compartilhada — condomínios, empresas e campus — em **dados estruturados, rateio justo e inteligência acionável**.

Hoje falta, na prática:

- 🔍 Identificar quem carregou e quanto consumiu (kWh)
- 💰 Cobrar de forma transparente
- 📊 Dar visibilidade ao gestor e ao morador

**Cenário adotado:** condomínio residencial com carregador compartilhado, extensível a edifícios corporativos e campus.

**Pergunta norteadora:** como estruturar sessões, calcular consumo individual e aplicar rateio justo com experiência digital clara?

---

## 2. 🔎 Frente 1 — Contexto e problema

### O que é recarga compartilhada

Um ou poucos carregadores atendem vários usuários em área comum. O desafio não é só energia — é **atribuir sessões, medir kWh e cobrar com justiça**.

Principais dificuldades: ausência de controle por usuário, cobrança opaca, conflito de vaga/horário, falta de relatório para o gestor e sessões interrompidas sem registro parcial.

### Sessão de recarga (visão técnica)

Entre conectar o veículo e desconectar, o carregador passa por estados (padrão IEC 61851) e gera dados: início/fim, potência (W), energia acumulada (kWh), duração. No HCA G2, isso é obtido via **OCPP 1.6J**, Modbus ou **API SEMS Portal**.

### Modelos de negócio

Recarga gratuita, cobrança por kWh, por tempo, assinatura ou rateio fixo entre usuários. No Brasil, a **Lei 14.874/2024** reforça **medição individualizada**: quem usa paga o que consumiu.

### Opção B — Pesquisa com usuários 📋

Formulário aplicado a **10 motoristas de EV/híbridos plug-in** (26/05–09/06/2026).

**Principais resultados:**

- **9/10** citaram fila, vaga ocupada ou conflito de horário como maior problema
- **10/10** preferem cobrança por **kWh**
- Features mais desejadas: histórico e gasto mensal (7), alerta de carga concluída (7), consumo em tempo real (6)
- Fragmentação de apps: cada operador exige cadastro diferente (BYD, Tupi, ChargeOn, etc.)

**Insights para o design:**

1. Rateio por **kWh × tarifa** — consenso total na pesquisa
2. **Reserva + alerta de término** — reduz conflito de vaga
3. **Histórico e consumo em tempo real** — prioridade do MVP
4. **Plataforma única por condomínio** — elimina app por operador
5. **Sessão interrompida** — registrar kWh parcial e notificar o usuário
6. **Previsão de custo e tarifa dinâmica** — exibir preço antes de carregar; IA ajusta tarifa por demanda do ponto

### Complemento — Dados públicos (Opção C parcial) 📈

ABVE: ~600 mil eletrificados em circulação; 223.912 emplacamentos em 2025 (+61%). Crescimento acelerado reforça a necessidade de gestão estruturada antes que condomínios operem carregadores sem controle.

---

## 3. ⚙️ Frente 2 — Tecnologia e regulação

### RN ANEEL 1.000/2021

Permite recarga comercial com preços livres. Exige **protocolos abertos** em equipamentos de uso não exclusivamente privado. Complementos: ABNT NBR 17019/2022 (instalações) e Lei 14.874/2024 (condomínios).

### Carregador GoodWe HCA G2 🔌

Interfaces: RS-485, LAN, Wi-Fi, Bluetooth e RFID (hardware). Modelos de 7–22 kW, OCPP 1.6J, Type 2, balanceamento dinâmico de carga.

**Decisão da equipe:** desbloqueio **somente pelo app** 📱 (comando remoto OCPP/API). RFID não será usado.

### API SEMS Portal (Opção B)

Três APIs: OpenAPI, Real-time Monitoring e Remote Control. Autenticação via `CrossLogin`. Integração primária planejada; fallback OCPP 1.6J se endpoints de carregador forem restritos.

### APIs complementares (Opção C)

- **Open Charge Map** — mapa de estações
- **ANEEL Open Data** — tarifas e contexto regulatório

---

## 4. 🏗️ Frente 3 — Arquitetura, rateio e IA

### Camadas da plataforma

| Camada | Componentes |
|---|---|
| Física | HCA G2, veículo EV |
| Conectividade | OCPP / Gateway Modbus |
| Aplicação | Backend, rateio, IA, PostgreSQL |
| Apresentação | App mobile (morador) + Portal web (condomínio) |

### Diagrama de arquitetura

![Arquitetura EV ChargeOps](docs/arquitetura-ev-chargeops.png)

### Fluxo: sessão → cobrança

1. Morador toca **Iniciar recarga** no app
2. Backend valida e libera o carregador remotamente
3. Telemetria (kWh, potência) flui para o backend
4. 🤖 IA calcula **tarifa dinâmica** do ponto (oferta e demanda)
5. Motor de rateio aplica: `kWh × tarifa_ajustada`
6. **Pós-pago:** relatório mensal no portal · **Pré-pago:** débito via Stripe

### Solução proposta

**📱 App mobile (morador):** mapa de carregadores com **tarifa dinâmica por ponto**, início de sessão pelo app (sem RFID), acompanhamento em tempo real (kWh, potência, valor) e histórico. Antes de iniciar, o app exibe o preço estimado da sessão conforme demanda do momento.

**Desbloqueio:** morador toca *Iniciar recarga* → backend valida → libera carregador via OCPP/API → sessão vinculada ao usuário.

**Pagamentos:**

| Modo | Contexto | Fluxo |
|---|---|---|
| 🏢 **Pós-pago** | Condomínio | Morador recarrega sem pagar no app. No fechamento do mês, gestor exporta relatório por unidade e anexa à conta condominial. |
| 💳 **Pré-pago** | Rede comercial | Morador cadastra cartão na **Stripe**. Ao encerrar a sessão, backend cobra via PaymentIntent e emite recibo no app. |

**🖥️ Portal do condomínio (gestor):** consumo por unidade/morador, exportação PDF/CSV, fechamento mensal para cobrança na taxa condominial.

**Wireframe do app:**

![Wireframe EV ChargeOps](docs/app-wireframe.png)

Telas: mapa com tarifa dinâmica por ponto, sessão ativa (kWh/R$) e histórico de sessões.

### Opção A — Modelo de rateio

Comparados rateio fixo, cobrança por tempo e operador terceirizado. **Adotamos cobrança por kWh medido**, com **tarifa ajustada por IA** conforme uso de cada ponto.

**Fórmula:**

```
tarifa_kWh = tarifa_base × fator_demanda
fator_demanda = IA(ocupação, fila, histórico, horário)
valor_sessão = kWh × tarifa_kWh + taxa_ocupação (se aplicável)
valor_mensal = Σ sessões + taxa_infraestrutura   # pós-pago
```

**Lógica de precificação:** pontos mais disputados (alta ocupação, fila, horários de pico) recebem tarifa maior; pontos ociosos, tarifa menor — equilibrando oferta e demanda e incentivando uso fora do pico.

**Variáveis:** kWh (telemetria), tarifa_base (gestor/ANEEL), fator_demanda (IA), duração, taxa de infraestrutura (assembleia).

**Casos excepcionais:** sessão interrompida (kWh parcial), dois carros na mesma unidade (consolidar), ocupação sem carga (alerta + taxa), visitante (vincular à unidade anfitriã). Tarifa travada no início da sessão — não muda após o morador confirmar.

### Opção B — Papel da IA 🤖

A IA é o **motor de precificação dinâmica** da plataforma — principal diferencial do EV ChargeOps.

| Abordagem | Técnica | Resolve |
|---|---|---|
| **Precificação dinâmica** | Regressão + regras de oferta/demanda | Ajustar tarifa por ponto conforme ocupação, fila, histórico e horário |
| Clustering de perfis | K-Means | Mapear padrões de uso por ponto (pico noturno, fim de semana) |
| Detecção de anomalias | Isolation Forest | Sessões atípicas ou uso indevido |
| Previsão de custo | Regressão | Exibir preço estimado no app antes de iniciar a recarga |

**Entradas do modelo de precificação:** taxa de ocupação do ponto, tamanho da fila, horário, dia da semana, histórico de sessões, potência disponível.

**Saída:** `fator_demanda` (ex.: 0,8x em horário ocioso · 1,5x em pico) aplicado sobre a tarifa base.

**Efeito esperado:** deslocar demanda para horários/pontos menos disputados, maximizar uso da infraestrutura e refletir o valor real de cada vaga no preço.

Dados: histórico de sessões + dataset Kaggle (72.856 sessões) para treino inicial.

### Opção C — Esquema de dados (resumo) 🗄️

Entidades principais: `usuario`, `unidade`, `condominio`, `carregador`, `sessao`, `medicao`, `tarifa_ponto` (base + fator_demanda), `relatorio_mensal`, `relatorio_unidade`, `transacao` (pré-pago).

Relacionamento central: condomínio → carregadores e unidades → usuários → sessões → medições → relatório mensal por unidade.

---

## 5. 🚀 Plano Sprint 02

**Objetivo:** MVP com app mobile, portal do condomínio, integração ao carregador (real ou simulado) e motor de rateio.

| Ordem | Entrega |
|---|---|
| 1 | Backend + banco (FastAPI, PostgreSQL) |
| 2 | Integração carregador (SEMS ou OCPP simulado) |
| 3 | Motor de rateio por kWh |
| 4 | App: mapa, sessão ativa, histórico |
| 5 | App: cartão (**Stripe**) + cobrança por sessão (pré-pago) |
| 6 | Portal: relatório por unidade + exportação PDF/CSV |
| 7 | IA: precificação dinâmica + deploy |

**Stack:** Python/FastAPI · PostgreSQL · Redis · React Native (Expo) · React (portal) · scikit-learn · **Stripe** (pagamentos) · Docker/Railway

**Critérios de sucesso:**

- [ ] Mapa com carregadores e status
- [ ] Sessão completa com telemetria
- [ ] Pós-pago: uso sem pagamento no app + exportação no portal
- [ ] Pré-pago: cartão cadastrado via Stripe + cobrança ao encerrar sessão
- [ ] Precificação dinâmica por ponto (tarifa varia conforme demanda)
- [ ] Ao menos um modelo de IA em produção (precificação ou anomalias)

---

## 6. 📚 Referências

- ANEEL. [Veículos Elétricos](https://www.gov.br/aneel/pt-br/assuntos/veiculos-eletricos) e [RN 1.000/2021](https://in.gov.br/web/dou/-/resolucao-normativa-aneel-n-1.000-de-7-de-dezembro-de-2021-*-375499427)
- Lei nº 14.874/2024 · ABNT NBR 17019:2022
- ABVE. [ABVE Data](https://abve.org.br/abve-data/) · ANEEL [Dados Abertos](https://dadosabertos.aneel.gov.br)
- GoodWe. [HCA G2](https://en.goodwe.com/hca-g2) · [SEMS Portal](https://semsplus.goodwe.com/) · [pygoodwe](https://github.com/yaleman/pygoodwe)
- Open Charge Map. [API](https://openchargemap.org/site/develop/api)
- IEC 61851 · OCPP 1.6J · ISO 15118
- Kaggle. [EV Charging Dataset](https://www.kaggle.com/datasets/mexwell/electric-vehicle-charging-dataset/data) · Nature. [EV Sessions](https://www.nature.com/articles/s41597-024-02942-9)
- Stripe. [Documentação de pagamentos](https://docs.stripe.com)
- Nicaira Rodrigues Advocacia. [Carregador em condomínio](https://nicairarodriguesadvocacia.com.br/carregador-de-carro-eletrico-em-condominio-guia-completo-para-sindicos/)

---

**Sprint 01 — Pesquisa e Documentação · Prazo: 21/06/2026 ⏰**
