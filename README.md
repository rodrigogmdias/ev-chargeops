# ⚡ EV ChargeOps

**Enterprise Challenge 2026 — FIAP × GoodWe**

Plataforma para gestão de recarga de veículos elétricos em infraestrutura compartilhada: sessões por usuário, rateio justo por kWh e inteligência operacional.

---

## 👥 Equipe

| Integrante | RM |
|---|---|
| Julia Ramos | RM568988 |
| Matheus Fuchelberguer | RM571321 |
| Carlos Eugenio Andrade | RM570285 |
| Rodrigo Gomes Dias | RM569142 |

**Repositório:** https://github.com/rodrigogmdias/ev-chargeops

---

## 🎯 1. Problema e contexto

A GoodWe, em parceria com a FIAP (Energy Innovation Lab — Aclimação), opera um carregador **HCA G2** no estacionamento L1. O desafio é transformar sessões de recarga em infraestrutura compartilhada — condomínios, empresas e campus — em **dados estruturados, rateio justo e inteligência acionável**.

Hoje falta, na prática:

- 🔍 Identificar quem carregou e quanto consumiu (kWh)
- 💰 Cobrar de forma transparente
- 📊 Dar visibilidade ao gestor e ao morador

**Cenário adotado:** condomínio residencial com carregador compartilhado, extensível a edifícios corporativos e campus.

**Pergunta norteadora:** como estruturar sessões, calcular consumo individual e aplicar rateio justo com experiência digital clara?

---

## 🔬 2. Frente 1 — Contexto e problema

### O que é recarga compartilhada

Um ou poucos carregadores atendem vários usuários em área comum. O desafio não é só energia — é **atribuir sessões, medir kWh e cobrar com justiça**.

Principais dificuldades: ausência de controle por usuário, cobrança opaca, conflito de vaga/horário, falta de relatório para o gestor e sessões interrompidas sem registro parcial.

### 🏢 Ambientes de recarga compartilhada

O produto endereça **dois grupos de ambientes** com perfis jurídicos e operacionais distintos. O cenário-foco é condomínio residencial, mas a arquitetura serve todos:

| Grupo | Ambientes | Característica |
|---|---|---|
| **A — Sem fins lucrativos** | Condomínio residencial · Condomínio comercial / lajes · Edifício corporativo · Campus universitário · Hospital / associação | Entidade gestora **só repassa o custo do kWh** (sem margem na energia) |
| **B — Com fins lucrativos** | Estacionamento de destino (shopping, aeroporto) · Posto / eletroposto · Hotel / resort / coworking | Operador comercial define preço livremente, com margem e precificação dinâmica |

### Sessão de recarga (visão técnica)

Entre conectar o veículo e desconectar, o carregador passa por estados (padrão IEC 61851) e gera dados: início/fim, potência (W), energia acumulada (kWh), duração. No HCA G2, isso é obtido via **OCPP 1.6J**, Modbus ou **API SEMS Portal**.

### 💰 Modelos de cobrança

Cinco modelos estruturam a cobrança em recarga compartilhada: **gratuita, por kWh, por tempo, assinatura mensal** e **rateio condominial** (modelo central do EV ChargeOps). A Lei 14.874/2024 reforça **medição individualizada**: quem usa paga o que consumiu.

A diferença entre os dois grupos não está na tecnologia (OCPP, medição, autenticação são iguais) mas nas **regras de precificação e natureza fiscal**:

| Dimensão | Grupo A (sem fins lucrativos) | Grupo B (com fins lucrativos) |
|---|---|---|
| Markup sobre o kWh | **Proibido** — custo real (ANEEL RN 1.000/2021, art. 2º, XV) | Permitido — preço livre |
| Precificação dinâmica por demanda | Não aplicável à energia | Disponível |
| Documento fiscal | Rateio de despesa (não NFS-e) | NFS-e obrigatória |
| Como o EV ChargeOps é remunerado | **Assinatura SaaS por condomínio** | Assinatura + % da transação |

### 📋 Opção B — Pesquisa com usuários

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
7. **Limite elétrico do condomínio** — quando vários carregadores ligam ao mesmo tempo, a potência por ponto cai; portal precisa mostrar quanto da capacidade contratada está sendo usada e indicar quando aumentar a demanda na concessionária

### 🏁 Opção A — Análise de concorrentes

Cinco soluções mapeadas no contexto da recarga compartilhada. Nenhuma fecha o ciclo de **rateio condominial integrado no Brasil**:

| Solução | Origem | Foco | Limitação para o BR |
|---|---|---|---|
| **Zaptec Pro** | Noruega | Condomínios / garagens coletivas | Sem distribuição/suporte local; sem rateio em boleto condominial |
| **Wallbox Pulsar+** | Espanha | Residencial individual | Power Sharing limitado a 2 unidades; sem rateio multi-usuário |
| **ChargePoint** | EUA | Frotas / redes enterprise | Custo desproporcional para condomínio; sem integração ERP BR |
| **NeoCharge** | Brasil (SP) | Hardware + plataforma | Plataforma de gestão, mas **rateio depende de terceiros** — ciclo não fecha |
| **Copel EV** | Brasil (PR) | Eletrovia BR-277 / Tarifa Mobiflex | Restrito ao PR; sem produto para condomínio |

**Lacuna estrutural:** nenhum player entrega de ponta a ponta o fluxo "sessão OCPP → cálculo por usuário → relatório → boleto condominial". O **EV ChargeOps ocupa essa lacuna como camada de software** que integra hardware existente via OCPP (sem fabricar carregador). Risco competitivo principal: **NeoCharge** pode lançar módulo de rateio nativo — velocidade de execução é crítica.

### 📈 Complemento — Dados públicos (Opção C parcial)

| Indicador | Valor | Fonte |
|---|---|---|
| Eletrificados emplacados em 2025 | **223.912** (+26% a/a) | ABVE |
| Participação no mercado total (abr/2026) | **16%** (dobrou em 7 meses) | ABVE |
| Eletropostos públicos (fev/2026) | **21.061** | ABVE/Tupi Mobilidade |
| Razão VE/ponto público | **19,6** (meta ideal: 10) | ABVE |
| Carregadores privados projetados em 2030 | **490 mil** (vs. 90 mil públicos — 5,4×) | McKinsey BR |
| Oportunidade de infraestrutura até 2030 | **R$ 6,8 bi** (R$ 13,4 bi com energia) | McKinsey BR |
| Concentração geográfica | SP + DF + MG + RJ + PR = **64% das vendas** | ABVE |

São Paulo é mercado prioritário: **30,6% das vendas nacionais** + **única lei estadual de condomínio vigente** (Lei 18.403/2026). Crescimento de carregadores privados 5,4× maior que públicos justifica o foco do EV ChargeOps no mercado privado.

---

## ⚙️ 3. Frente 2 — Tecnologia e regulação

### 📜 Marco regulatório

| Norma | O que estabelece | Impacto no produto |
|---|---|---|
| **ANEEL RN 1.000/2021** (art. 2º, XV) | Define "estação de recarga" e permite recarga comercial com preços livres. Exige **protocolos abertos** em uso não exclusivamente privado. | Condomínio **não pode comercializar energia com margem** — só repassa o custo do kWh. Plataforma é remunerada como **SaaS**, não como distribuidor. |
| **Lei 14.874/2024** (federal) | Reforça medição individualizada em condomínios. | Justifica o rateio por kWh por usuário. |
| **Lei 18.403/2026-SP** (Alesp) | Garante direito de instalação em vaga privativa e autoriza condomínio a estabelecer **rateio ou cobrança individual por consumo**. | **Gatilho regulatório** do produto: novos prédios em SP precisam prever infraestrutura. |
| **PL 158/2025** (Câmara, CCJC) | Tentativa de base federal equivalente à Lei SP. Aguardando relator. | Acompanhar — pode acelerar adoção em todo o país. |
| **Portaria CB-SP 003/970/2026 (IT-41)** | Proíbe carregadores portáteis e tomadas convencionais em garagens fechadas — **apenas Modos 3 e 4** permitidos. | Elimina concorrência "tomada comum"; justifica HCA G2 (Modo 3). |
| **ABNT NBR 17019/2022** | Norma de instalações elétricas para recarga de VE. | Referência técnica para projeto e laudo. |

### 🔌 Carregador GoodWe HCA G2

Interfaces: RS-485, LAN, Wi-Fi, Bluetooth e RFID (hardware). Modelos de 7–22 kW, OCPP 1.6J, Type 2, balanceamento dinâmico de carga.

**Decisão da equipe:** desbloqueio **somente pelo app** (comando remoto OCPP/API). RFID não será usado.

### API SEMS Portal (Opção B)

Três APIs: OpenAPI, Real-time Monitoring e Remote Control. Autenticação via `CrossLogin`. Integração primária planejada; fallback OCPP 1.6J se endpoints de carregador forem restritos.

### APIs complementares (Opção C)

- **Open Charge Map** — mapa de estações
- **ANEEL Open Data** — tarifas e contexto regulatório

---

## 🏗️ 4. Frente 3 — Arquitetura, rateio e IA

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

| Modo | Contexto (grupo) | Fluxo |
|---|---|---|
| 🏢 **Pós-pago** | Condomínio (Grupo A) | Morador recarrega sem pagar no app. No fechamento do mês, gestor exporta relatório por unidade e anexa à conta condominial — valor é **repasse do custo do kWh sem margem** (ANEEL RN 1.000/2021). |
| 💳 **Pré-pago** | Rede comercial (Grupo B) | Morador cadastra cartão na **Stripe**. Ao encerrar a sessão, backend cobra via PaymentIntent (preço livre, com margem) e emite recibo no app. |

> **Como o EV ChargeOps é remunerado:** assinatura **SaaS por condomínio** (Grupo A — não pode haver markup na energia) e **assinatura + % da transação** no Grupo B. A plataforma cobra pelo **serviço de gestão**, não pela energia em si.

**🖥️ Portal do condomínio (gestor):** consumo por unidade/morador, exportação PDF/CSV, fechamento mensal para cobrança na taxa condominial e **painel de capacidade elétrica** (ver abaixo).

### ⚡ Balanceamento de carga e capacidade contratada

O condomínio contrata uma **demanda (kW)** junto à concessionária. Quando vários carregadores operam em paralelo, a soma das potências pode ultrapassar esse limite — então o HCA G2 (balanceamento dinâmico OCPP) **reduz a potência entregue por ponto** para proteger a instalação. Resultado: cada morador recebe menos kW e a sessão demora mais.

O portal expõe esse comportamento ao gestor:

| Indicador | O que mostra |
|---|---|
| Demanda contratada (kW) | Limite acordado com a concessionária |
| Demanda instantânea (kW) | Soma das potências em uso pelos carregadores |
| % de utilização | Demanda instantânea ÷ demanda contratada |
| Potência efetiva por ponto | kW real entregue vs. kW nominal do carregador |
| Eventos de throttling | Quantas vezes/quanto tempo houve redução automática |
| Recomendação de upgrade | Alerta quando a utilização média ultrapassa 80% em horário de pico |

**Exemplo:** 10 carregadores de 7 kW = 70 kW nominais. Se a demanda contratada é 45 kW, o sistema reparte 4,5 kW por ponto e o painel sinaliza **utilização 100% + sugestão de aumentar demanda para 75 kW** com base no histórico de uso simultâneo.

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

### 🤖 Opção B — Papel da IA

A IA é o **motor de precificação dinâmica** da plataforma — principal diferencial do EV ChargeOps.

| Abordagem | Técnica | Resolve |
|---|---|---|
| **Precificação dinâmica** | Regressão + regras de oferta/demanda | Ajustar tarifa por ponto conforme ocupação, fila, histórico e horário |
| Clustering de perfis | K-Means | Mapear padrões de uso por ponto (pico noturno, fim de semana) |
| Detecção de anomalias | Isolation Forest | Sessões atípicas ou uso indevido |
| Previsão de custo | Regressão | Exibir preço estimado no app antes de iniciar a recarga |
| Previsão de capacidade | Séries temporais | Projetar demanda futura e recomendar aumento da carga contratada na concessionária |

**Entradas do modelo de precificação:** taxa de ocupação do ponto, tamanho da fila, horário, dia da semana, histórico de sessões, potência disponível.

**Saída:** `fator_demanda` (ex.: 0,8x em horário ocioso · 1,5x em pico) aplicado sobre a tarifa base.

**Efeito esperado:** deslocar demanda para horários/pontos menos disputados, maximizar uso da infraestrutura e refletir o valor real de cada vaga no preço.

Dados: histórico de sessões + dataset Kaggle (72.856 sessões) para treino inicial.

### 🗄️ Opção C — Esquema de dados (resumo)

Entidades principais: `usuario`, `unidade`, `condominio`, `carregador`, `sessao`, `medicao`, `tarifa_ponto` (base + fator_demanda), `relatorio_mensal`, `relatorio_unidade`, `transacao` (pré-pago).

Relacionamento central: condomínio → carregadores e unidades → usuários → sessões → medições → relatório mensal por unidade.

---

## 🚀 5. Plano Sprint 02

**Objetivo:** MVP com app mobile, portal do condomínio, integração ao carregador (real ou simulado) e motor de rateio.

| Ordem | Entrega |
|---|---|
| 1 | Backend + banco (FastAPI, PostgreSQL) |
| 2 | Integração carregador (SEMS ou OCPP simulado) |
| 3 | Motor de rateio por kWh |
| 4 | App: mapa, sessão ativa, histórico |
| 5 | App: cartão (**Stripe**) + cobrança por sessão (pré-pago) |
| 6 | Portal: relatório por unidade + exportação PDF/CSV |
| 7 | Portal: painel de capacidade elétrica (demanda contratada × instantânea + throttling) |
| 8 | IA: precificação dinâmica + previsão de capacidade + deploy |

**Stack:** Python/FastAPI · PostgreSQL · Redis · React Native (Expo) · React (portal) · scikit-learn · **Stripe** (pagamentos) · Docker/Railway

**Critérios de sucesso:**

- [ ] Mapa com carregadores e status
- [ ] Sessão completa com telemetria
- [ ] Pós-pago: uso sem pagamento no app + exportação no portal
- [ ] Pré-pago: cartão cadastrado via Stripe + cobrança ao encerrar sessão
- [ ] Precificação dinâmica por ponto (tarifa varia conforme demanda)
- [ ] Painel de capacidade elétrica com alerta de upgrade de demanda contratada
- [ ] Ao menos um modelo de IA em produção (precificação ou anomalias)

---

## 📚 6. Referências

**Regulação:**
- ANEEL. [Veículos Elétricos](https://www.gov.br/aneel/pt-br/assuntos/veiculos-eletricos) e [RN 1.000/2021](https://in.gov.br/web/dou/-/resolucao-normativa-aneel-n-1.000-de-7-de-dezembro-de-2021-*-375499427)
- Lei nº 14.874/2024 (federal) · ABNT NBR 17019:2022
- Lei nº [18.403/2026-SP](https://www.al.sp.gov.br) (Alesp — direito de instalação em condomínio)
- [PL 158/2025](https://www.camara.leg.br) (Câmara dos Deputados — base federal de condomínio, aguardando CCJC)
- Corpo de Bombeiros SP. Portaria 003/970/2026 (IT-41 — Modos 3 e 4 em garagens fechadas)

**Mercado e dados:**
- ABVE. [ABVE Data](https://abve.org.br/abve-data/) · ANEEL [Dados Abertos](https://dadosabertos.aneel.gov.br)
- McKinsey Brasil. [Recarregar para crescer](https://www.mckinsey.com.br) (dez/2024 — projeções 2030: 490k privados, R$ 6,8 bi)
- ANACON. Pontos de recarga em condomínios (out/2025)

**Tecnologia:**
- GoodWe. [HCA G2](https://en.goodwe.com/hca-g2) · [SEMS Portal](https://semsplus.goodwe.com/) · [pygoodwe](https://github.com/yaleman/pygoodwe)
- Open Charge Alliance. Especificação OCPP 1.6 e 2.0.1
- IEC 61851 · OCPP 1.6J · ISO 15118
- Open Charge Map. [API](https://openchargemap.org/site/develop/api)

**Datasets e pagamentos:**
- Kaggle. [EV Charging Dataset](https://www.kaggle.com/datasets/mexwell/electric-vehicle-charging-dataset/data) · Nature. [EV Sessions](https://www.nature.com/articles/s41597-024-02942-9)
- Stripe. [Documentação de pagamentos](https://docs.stripe.com)
- Nicaira Rodrigues Advocacia. [Carregador em condomínio](https://nicairarodriguesadvocacia.com.br/carregador-de-carro-eletrico-em-condominio-guia-completo-para-sindicos/)

---

⏰ **Sprint 01 — Pesquisa e Documentação · Prazo: 21/06/2026**
