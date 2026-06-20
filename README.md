# ⚡ EV ChargeOps

**Enterprise Challenge 2026 — FIAP × GoodWe**

Plataforma para gestão de recarga de veículos elétricos em infraestrutura compartilhada: sessões por usuário, rateio justo por kWh e inteligência operacional.

### 🌐 Site de apresentação

> **[👉 Acesse a apresentação interativa do EV ChargeOps](https://site-pink-xi-65.vercel.app/)**
>
> Conheça o produto, as 5 fases de construção, o modelo de negócio e a jornada de recarga em um site dedicado.

---

## 👥 Equipe

| Integrante | RM |
|---|---|
| Julia Ramos | RM568988 |
| Matheus Fuchelberguer | RM571321 |
| Carlos Eugenio Andrade | RM570285 |
| Rodrigo Gomes Dias | RM569142 |

**Repositório:** https://github.com/rodrigogmdias/ev-chargeops  
**Apresentação:** https://site-pink-xi-65.vercel.app/

---

## 🎯 1. Problema e contexto

A GoodWe, em parceria com a FIAP (Energy Innovation Lab — Aclimação), opera um carregador **HCA G2** no estacionamento L1. O desafio é transformar sessões de recarga em infraestrutura compartilhada — condomínios, empresas e campus — em **dados estruturados, rateio justo e inteligência acionável**.

Hoje falta, na prática:

- 🔍 Identificar quem carregou e quanto consumiu (kWh)
- 💰 Cobrar de forma transparente
- 📊 Dar visibilidade ao gestor e ao morador
- ⚡ Gerenciar a capacidade elétrica do prédio

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

Entre conectar o veículo e desconectar, o carregador passa por estados (padrão IEC 61851) e gera dados: início/fim, potência (W), energia acumulada (kWh), duração. No HCA G2, esses dados são obtidos pela **API SEMS Portal** (GoodWe).

### 💰 Modelos de cobrança

A cobrança é feita **por kWh consumido** — modelo central do EV ChargeOps, em duas variantes: **rateio condominial** (Grupo A, sem margem) e **por sessão** (Grupo B, com margem e tarifa dinâmica). A Lei 14.874/2024 reforça **medição individualizada**: quem usa paga o que consumiu.

A diferença entre os dois grupos não está na tecnologia (API SEMS, medição e autenticação são iguais) mas nas **regras de precificação e natureza fiscal**:

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

**Lacuna estrutural:** nenhum player entrega de ponta a ponta o fluxo "sessão de recarga → cálculo por usuário → relatório → boleto condominial". O **EV ChargeOps ocupa essa lacuna como camada de software** que integra o carregador via **API SEMS** da GoodWe (sem fabricar hardware). Risco competitivo principal: **NeoCharge** pode lançar módulo de rateio nativo — velocidade de execução é crítica.

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

> **Posicionamento estratégico:** o EV ChargeOps se posiciona deliberadamente como **camada de software de gestão** — **não** como instalador, distribuidora ou comercializadora de energia. As responsabilidades regulatórias mais pesadas (instalação física, segurança contra incêndio, comunicação à distribuidora) permanecem com o condomínio, o profissional habilitado e a administradora. Isso é decisão de escopo, não lacuna.

| Norma | Esfera | Status | O que estabelece | Impacto no produto |
|---|---|---|---|---|
| **ANEEL RN 1.000/2021** (art. 2º, XV) | Federal | Vigente | Define "estação de recarga" e trata recarga como **serviço livre**, sem outorga. Exige comunicação prévia à distribuidora ao instalar; protocolos abertos em uso não exclusivamente privado. | Condomínio **não pode comercializar energia com margem** — só repassa custo do kWh. Plataforma é remunerada como **SaaS**, não como distribuidor. Portal pode gerar automaticamente o comunicado à distribuidora. |
| **Lei 14.874/2024** (federal) | Federal | Vigente | Reforça medição individualizada em condomínios. | Justifica o rateio por kWh por usuário. |
| **CP ANEEL 42/2025** | Federal | **Em formação** | Revisão das regras de **conexão de carregadores à rede de distribuição** (encerrada 10/03/2026, sem texto final). | **Risco regulatório a monitorar** — arquitetura deve acomodar mudança de requisitos técnicos sem redesenho estrutural. |
| **PL 158/2025** | Federal | Tramitação inicial (CCJC) | Tentativa de base federal equivalente à Lei SP. Aguardando relator. | Acompanhar — pode acelerar adoção em todo o país. |
| **Lei Estadual 18.403/2026-SP** (Alesp) | Estadual | Vigente (18/02/2026) | Direito do condômino de instalar carregador às próprias expensas em vaga privativa. Requisitos: compatibilidade elétrica, normas da distribuidora e ABNT, **profissional habilitado (ART/RRT)** e **comunicação formal prévia à administração**. Art. 2º: novos empreendimentos devem prever capacidade elétrica mínima. | **Gatilho regulatório** da demanda. Portal pode registrar o ART/RRT e o comunicado à administração — viraliza adoção em assembleia. |
| **Portaria CCB 003/970/2026** (IT-41) | Estadual | Vigente (17/03/2026) | Segurança contra incêndio para SAVE em garagens. Apenas **Modos 3 (AC) e 4 (DC)** em áreas internas (NBR IEC 61851-1). Responsabilidade técnica integral do instalador; chave de desligamento de emergência; sinalização padronizada. Vistoria CBPMESP a partir da renovação do AVCB. | Elimina concorrência "tomada comum" e justifica HCA G2 (Modo 3). Plataforma pressupõe carregador dedicado e gerenciável remotamente (no MVP, HCA G2 + API SEMS). |
| **RC 31007/2024** (Sefaz-SP) | Estadual | Vigente | Sefaz-SP entende que comercialização de energia em estação de recarga **incide ICMS**. Operador pode se creditar do ICMS pago na conta de energia (art. 59–70 do RICMS/2000). | **Grupo B** (com margem): obrigação de NFS-e + ICMS na cobrança ao consumidor. **Grupo A** (repasse sem margem): tende a não gerar fato gerador. Motor de rateio precisa diferenciar os dois regimes fiscais. |
| **Lei Municipal SP 17.336/2020** | Municipal (capital) | Vigente (30/03/2020) | Obriga **medição individualizada e cobrança por consumo** em novos edifícios residenciais e comerciais da capital com previsão de recarga de VE. | **Endosso direto da tese do produto:** a lei já obriga medição individualizada desde 2020, mas nenhuma plataforma BR entrega esse ciclo integrado. O EV ChargeOps é a ferramenta operacional que faltava. |
| **ABNT NBR 17019/2022** | Federal | Vigente | Norma de instalações elétricas para recarga de VE (referenciada pela IT-41). | Referência técnica para projeto e laudo. |

### ⚖️ Conformidade da proposta

| Norma | Status | Justificativa |
|---|---|---|
| ANEEL RN 1.000/2021 | ✅ Compatível | Plataforma não comercializa energia nem opera como distribuidora — é camada de gestão sobre serviço já permitido a qualquer operador |
| Lei 18.403/2026-SP | 🔶 Parcial — depende do operador | Lei regula relação condômino × condomínio, não o software. Plataforma pode registrar a comunicação prévia exigida; responsabilidade legal permanece do condômino e do profissional habilitado |
| Portaria CCB 003/970/2026 (IT-41) | ✅ Compatível, com premissa | Plataforma pressupõe carregador dedicado e gerenciável remotamente (HCA G2 via API SEMS) — alinhado à proibição de soluções improvisadas. Conformidade da instalação física é do instalador, não do software |
| Lei Municipal SP 17.336/2020 | ✅ Endereça lacuna direta | Plataforma é a ferramenta operacional que faltava para cumprir a exigência legal de medição individualizada — **núcleo da proposta de valor** |
| RC 31007/2024 (ICMS) | ⏳ Requer tratamento diferenciado | Motor de rateio precisa diferenciar ambientes sem fins lucrativos (sem ICMS) de ambientes com fins lucrativos (com ICMS + NFS-e) |

### ⚠️ Lacunas e riscos regulatórios

- **Rateio de obras coletivas indefinido** — Nem a Lei 18.403/2026 nem a Portaria 003/970/2026 definem quem paga por intervenções coletivas (reforço de prumadas, adequações elétricas) necessárias para viabilizar instalações individuais. Fonte provável de disputa em assembleia.
- **CP ANEEL 42/2025 sem texto final** — Regras de conexão à rede podem mudar nos próximos meses.
- **Tratamento fiscal ICMS** — Diferença de incidência entre Grupo A e Grupo B exige modelagem específica no motor de rateio antes do go-live comercial.
- **Lacuna municipal = oportunidade** — Lei 17.336/2020 não detalha como operacionalizar a medição individualizada (sem decreto executivo complementar). É espaço de mercado para o produto.

### 🔌 Carregador GoodWe HCA G2

Interfaces: RS-485, LAN, Wi-Fi, Bluetooth (hardware). Modelos de 7–22 kW, Type 2, balanceamento dinâmico de carga nativo.

**Decisão da equipe:** integração feita **exclusivamente pela API SEMS** da GoodWe. Desbloqueio **somente pelo app** (comando remoto via SEMS Remote Control). RFID não será usado.

### API SEMS Portal (Opção B)

Integração **única e oficial** com o HCA G2. Três APIs disponíveis: **OpenAPI** (dados históricos), **Real-time Monitoring** (telemetria ao vivo) e **Remote Control** (start/stop e ajuste de potência). Autenticação via `CrossLogin`. Toda a camada de conectividade da plataforma é construída sobre esses endpoints.

### APIs complementares (Opção C)

- **Open Charge Map** — mapa de estações
- **ANEEL Open Data** — tarifas e contexto regulatório

### 🔒 Segurança e LGPD

Pagamento e dados pessoais com **rigor de instituição financeira** — confiança é pré-requisito de qualquer transação:

| Camada | Prática |
|---|---|
| **Cartão** | Tokenização via Stripe — dados sensíveis nunca trafegam ou ficam armazenados na infraestrutura do EV ChargeOps |
| **Pré-autorização** | Bloqueio de saldo antes da recarga; captura só ao final — evita cobrança indevida |
| **LGPD — consentimento** | Termo explícito no onboarding com finalidade clara de cada dado (identidade, localização, telemetria, cobrança) |
| **LGPD — minimização** | Coleta apenas o necessário; localização só com app aberto; histórico anonimizado após 24 meses |
| **LGPD — direitos do titular** | Acesso, correção, portabilidade e eliminação via app — fluxos auditáveis |
| **Transparência** | Notificação push em cada evento financeiro (recarga liberada, sessão encerrada, cobrança aplicada, multa iniciada) |
| **Comunicação** | TLS 1.3 obrigatório em todas as integrações (app ↔ backend ↔ API SEMS) |

---

## 🏗️ 4. Frente 3 — Arquitetura, rateio e IA

### Camadas da plataforma

| Camada | Componentes |
|---|---|
| Física | HCA G2, veículo EV |
| Conectividade | API SEMS Portal (GoodWe Cloud) |
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

**Desbloqueio:** morador toca *Iniciar recarga* → backend valida → libera carregador via **API SEMS (Remote Control)** → sessão vinculada ao usuário.

**Pagamentos:**

Antes de qualquer recarga, o app faz **pré-autorização (bloqueio de saldo)** no cartão do morador — garantia de pagamento que elimina inadimplência. A recarga só é liberada se o bloqueio for aceito.

| Modo | Contexto (grupo) | Composição |
|---|---|---|
| 🏢 **Híbrido** | Condomínio (Grupo A) | Cobrança em **duas linhas**: (1) **taxa de acesso/manutenção** mensal por morador habilitado — rateio da infraestrutura (suporta o custo fixo do ponto); (2) **consumo por kWh** repassado a custo, sem margem na energia (ANEEL RN 1.000/2021). |
| 💳 **Pré-pago** | Rede comercial (Grupo B) | Sessão paga via **Stripe** com pré-autorização no início e captura no encerramento. Preço livre, com margem e tarifa dinâmica da IA. NFS-e emitida automaticamente. |

> **Como o EV ChargeOps é remunerado:** **assinatura SaaS por condomínio** (Grupo A) e **assinatura + % da transação** (Grupo B). A plataforma cobra pelo **serviço de gestão** — não pela energia.

**🖥️ Portal do condomínio (gestor):** consumo por unidade/morador, exportação PDF/CSV, fechamento mensal para cobrança na taxa condominial e **painel de capacidade elétrica** (ver abaixo).

### ⚡ Balanceamento de carga e capacidade contratada

O condomínio contrata uma **demanda (kW)** junto à concessionária. Quando vários carregadores operam em paralelo, a soma das potências pode ultrapassar esse limite — então o HCA G2 (balanceamento dinâmico nativo) **reduz a potência entregue por ponto** para proteger a instalação. Resultado: cada morador recebe menos kW e a sessão demora mais. A telemetria do throttling é lida pela API SEMS e refletida no portal.

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

### 🚫 Anti-ociosidade — tolerância e multa por ocupação

A pesquisa apontou que **9/10 motoristas** identificam vaga ocupada como o maior problema. O EV ChargeOps adota uma régua de ocupação transparente e progressiva:

| Etapa | Tempo após carga completa | O que acontece |
|---|---|---|
| 🟢 Tolerância | 0 → 10 min | Janela gratuita para o morador remover o veículo |
| 🔔 Aviso de multa | 10 min | Notificação push: "multa começa agora" |
| 🟡 Multa proporcional | > 10 min | Cobrança por minuto excedente (taxa de ocupação) |
| 🔴 Liberação remota | configurável | Em pontos públicos: gestor pode encerrar sessão remotamente após X min |

Regras: (a) tolerância e tarifa de multa são **configuráveis pelo gestor**; (b) sempre há notificação antes da cobrança; (c) valor da multa é proporcional ao tempo — sem cobrança arbitrária; (d) histórico de ocupação alimenta a IA de precificação dinâmica.

### 🔔 Notificações ao morador

Comunicação é parte do produto. O app dispara push em **três momentos críticos**:

| Momento | Mensagem | Por quê |
|---|---|---|
| **−15 min** | "Sua recarga termina em 15 minutos" | Tempo para o morador se planejar e liberar a vaga |
| **Conclusão** | "Recarga concluída — você tem 10 min de tolerância" | Aciona a régua anti-ociosidade |
| **Início da multa** | "Multa de ocupação iniciada" | Transparência financeira em tempo real |

Complementam: alerta de **sessão interrompida** (kWh parcial registrado), **falha no carregador** e **fim do mês** (extrato disponível).

**Wireframe do app:**

![Wireframe EV ChargeOps](docs/app-wireframe.png)

Telas: mapa com tarifa dinâmica por ponto, sessão ativa (kWh/R$) e histórico de sessões.

### Opção A — Modelo de rateio

Comparados rateio fixo, cobrança por tempo e operador terceirizado. **Adotamos cobrança por kWh medido**, com **tarifa ajustada por IA** conforme uso de cada ponto.

**Fórmulas:**

```
# Grupo B (rede comercial) — preço livre, com margem
tarifa_kWh = tarifa_base × fator_demanda
valor_sessão = kWh × tarifa_kWh + taxa_ocupação

# Grupo A (condomínio) — repasse sem margem + taxa de acesso
custo_energia_sessão = kWh × tarifa_concessionária   # repasse direto
valor_mensal_morador = taxa_acesso + Σ custo_energia_sessão + Σ taxa_ocupação

# IA (comum aos dois grupos)
fator_demanda = IA(ocupação, fila, histórico, horário)
```

**Lógica de precificação:** pontos mais disputados (alta ocupação, fila, horários de pico) recebem tarifa maior; pontos ociosos, tarifa menor — equilibrando oferta e demanda e incentivando uso fora do pico. **No Grupo A**, o fator_demanda **não muda o custo da energia** (proibido por ANEEL) — mas serve como **sinal informativo** ao morador para escolher horários de menor ocupação.

**Variáveis:** kWh (telemetria), tarifa_concessionária (fatura), tarifa_base (Grupo B), fator_demanda (IA), duração, taxa_acesso e taxa_ocupação (deliberadas em assembleia ou contrato).

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

Entidades principais: `usuario`, `unidade`, `condominio`, `ponto` (com `tipo` ∈ {`condominio`, `venda`}), `carregador`, `sessao`, `medicao`, `tarifa_ponto` (base + fator_demanda), `evento_ocupacao` (tolerância, multa), `notificacao`, `pre_autorizacao` (Stripe), `transacao`, `relatorio_mensal`, `relatorio_unidade`.

Relacionamento central: condomínio → pontos → carregadores → sessões → medições + eventos de ocupação → relatório mensal por unidade. O atributo `ponto.tipo` define automaticamente o modelo de cobrança (híbrido vs. pré-pago) e as regras fiscais aplicadas.

---

## 🚀 5. Plano Sprint 02

**Objetivo:** MVP do EV ChargeOps construído em **5 fases incrementais**, cada uma só faz sentido sobre a anterior — primeiro a base de identidade e segurança, depois descoberta, jornada de recarga, regras de ocupação e, por fim, o modelo de negócio.

### 🧱 Fase 1 — Fundação do produto

Backend + banco + identidade do usuário + cadastro do parque.

- Backend · PostgreSQL · Redis · Docker
- Onboarding: registro, login (e-mail/OAuth), confirmação de identidade
- LGPD: termo de consentimento + minimização + tokenização Stripe
- Cadastro de pontos com atributo `tipo` (`condominio` | `venda`) — define o modelo de cobrança
- Cadastro de condomínios, unidades e moradores; vínculo morador ↔ unidade

### 📍 Fase 2 — Localização & descoberta

Mapa de pontos e estratégia de cobertura.

- Integração com API de mapas (Google Maps / Mapbox)
- Mapa do app: pontos próximos, status, tipo (venda/condomínio) e preço estimado
- Critério de localização para pontos de venda: fluxo de veículos + demanda regional + dados públicos (ABVE, ANEEL)

### ⚡ Fase 3 — Jornada de recarga

Sessão completa, do toque do morador ao débito automático.

- Integração ao carregador via **API SEMS Portal** (ou mock para desenvolvimento) — autenticação `CrossLogin`, status, telemetria
- Login → escolha do ponto → **pré-autorização Stripe** → liberação remota (**SEMS Remote Control**)
- Telemetria em tempo real (kWh, potência, tensão, corrente)
- Encerramento com captura automática do bloqueio + recibo no app

### 🚫 Fase 4 — Pós-recarga & ocupação

Régua transparente para girar a vaga com justiça.

- Notificações: **−15 min**, **conclusão**, **início da multa**, sessão interrompida, falha
- Tolerância configurável (padrão 10 min) + multa proporcional por minuto excedente
- Portal: painel de capacidade elétrica (demanda contratada × instantânea + throttling)
- Detecção de anomalias (Isolation Forest)

### 💼 Fase 5 — Modelo de negócio

Cobrança, rateio e inteligência de precificação.

- **Híbrido (Grupo A):** taxa de acesso mensal + repasse de kWh sem margem; portal exporta relatório por unidade para boleto condominial
- **Pré-pago (Grupo B):** sessão cobrada via Stripe + NFS-e automática
- IA: precificação dinâmica (`fator_demanda`) + previsão de capacidade
- Portal: relatório por unidade, exportação PDF/CSV, fechamento mensal

### 🛠️ Stack

Backend · PostgreSQL · Redis · App mobile · Portal web · scikit-learn (IA) · **Stripe** (pagamentos com pré-autorização) · Google Maps/Mapbox · Docker/Railway

### ✅ Critérios de sucesso

- [ ] Onboarding com consentimento LGPD + tokenização de cartão
- [ ] Mapa com carregadores, status, tipo e preço estimado
- [ ] Pré-autorização Stripe antes de liberar a recarga
- [ ] Sessão completa com telemetria em tempo real
- [ ] Notificações: −15 min, conclusão e início da multa
- [ ] Tolerância de 10 min + cobrança proporcional pela ocupação
- [ ] Híbrido (condomínio): taxa de acesso + repasse de kWh + relatório por unidade
- [ ] Pré-pago (rede comercial): captura via Stripe + NFS-e
- [ ] Painel de capacidade elétrica com alerta de upgrade
- [ ] Pelo menos um modelo de IA em produção (precificação ou anomalias)

---

## 📚 6. Referências

**Regulação federal:**
- ANEEL. [Veículos Elétricos](https://www.gov.br/aneel/pt-br/assuntos/veiculos-eletricos) e [RN 1.000/2021](https://www2.aneel.gov.br/cedoc/ren20211000.html) (Cap. V — Estações de recarga; art. 2º, XV)
- ANEEL. [Consulta Pública 42/2025](https://www.gov.br/aneel/pt-br/assuntos/noticias/2025/aneel-abre-consulta-publica-para-aprimorar-regras-de-conexao-de-eletromobilidade-a-rede-eletrica) (regras de conexão de eletromobilidade — em formação)
- Lei nº 14.874/2024 (federal) · ABNT NBR 17019:2022
- [PL 158/2025](https://www.camara.leg.br/proposicoesWeb/fichadetramitacao?idProposicao=2482575) (Câmara dos Deputados — base federal de condomínio, aguardando CCJC)

**Regulação estadual e municipal (SP):**
- Lei Estadual nº [18.403/2026-SP](https://www.al.sp.gov.br/repositorio/legislacao/lei/2026/lei-18403-18.02.2026.html) (Alesp — direito de instalação em condomínio)
- Lei Municipal nº [17.336/2020](https://legislacao.prefeitura.sp.gov.br/leis/lei-17336-de-30-de-marco-de-2020) (Prefeitura de SP — medição individualizada e cobrança por consumo)
- Corpo de Bombeiros SP. [Portaria 003/970/2026](https://doe.sp.gov.br/executivo/secretaria-da-seguranca-publica/portaria-n-003-970-2026-de-17-de-marco-de-2026-20260316113816712141709068) (IT-41 — Modos 3 e 4 em garagens fechadas) · cobertura técnica: [ABVE](https://abve.org.br/nova-regra-para-recarga-em-edificios-em-sp-garante-previsibilidade-e-seguranca-a-eletromobilidade/)
- Sefaz-SP. [RC 31007/2024](https://legislacao.fazenda.sp.gov.br/Paginas/RC31007_2024.aspx) (Resposta de Consulta Tributária — ICMS sobre recarga)
- Migalhas. [Lei 18.403/26 de SP — lacunas](https://www.migalhas.com.br/coluna/migalhas-edilicias/452093/lei-18-403-26-de-sp-recarga-de-veiculos-eletricos-em-condominios) (mar/2026)

**Mercado e dados:**
- ABVE. [ABVE Data](https://abve.org.br/abve-data/) · ANEEL [Dados Abertos](https://dadosabertos.aneel.gov.br)
- McKinsey Brasil. [Recarregar para crescer](https://www.mckinsey.com.br) (dez/2024 — projeções 2030: 490k privados, R$ 6,8 bi)
- ANACON. Pontos de recarga em condomínios (out/2025)

**Tecnologia:**
- GoodWe. [HCA G2](https://en.goodwe.com/hca-g2) · [SEMS Portal](https://semsplus.goodwe.com/) · [pygoodwe](https://github.com/yaleman/pygoodwe)
- IEC 61851 · ISO 15118
- Open Charge Map. [API](https://openchargemap.org/site/develop/api)

**Datasets e pagamentos:**
- Kaggle. [EV Charging Dataset](https://www.kaggle.com/datasets/mexwell/electric-vehicle-charging-dataset/data) · Nature. [EV Sessions](https://www.nature.com/articles/s41597-024-02942-9)
- Stripe. [Documentação de pagamentos](https://docs.stripe.com)
- Nicaira Rodrigues Advocacia. [Carregador em condomínio](https://nicairarodriguesadvocacia.com.br/carregador-de-carro-eletrico-em-condominio-guia-completo-para-sindicos/)

---

⏰ **Sprint 01 — Pesquisa e Documentação · Prazo: 21/06/2026**
