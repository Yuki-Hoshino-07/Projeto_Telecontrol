# 📄 Product Requirement Document (PRD) — SmartPick Platform

---

## 1. Informações do Documento e Histórico

| Campo | Detalhe |
| :--- | :--- |
| **Nome do Produto** | SmartPick Platform |
| **Parceria/Iniciativa** | Telecontrol x Unimar |
| **Versão** | 1.0.0 (MVP) |
| **Data de Criação** | Outubro de 2026 |
| **Status** | Em Desenvolvimento / Aprovado |
| **Tech Lead** | Leonardo Portaluppi |
| **Equipe de Desenvolvimento** | Luana Portelinha, Pedro Henrique, João Celso, Kendy Hashimoto, Raissa Kaori |

---

## 2. Visão Geral e Visão de Produto

### 2.1 Problema
Operações físicas em Centros de Distribuição (CDs) frequentemente sofrem com ineficiências no processo de coleta de produtos (*picking*). O percurso não otimizado dos operadores resulta em:
* Deslocamentos desnecessários e longos tempos de ciclo por pedido.
* Altos custos operacionais por hora/trabalho.
* Baixa visibilidade e falta de acompanhamento em tempo real dos indicadores de produtividade logísticos (KPIs).

### 2.2 Visão do Produto
O **SmartPick** é uma plataforma logística computacional para roteirização interna e otimização de *picking*, combinando uma arquitetura web leve para gestão operacional com um motor de otimização matemática dedicado.

### 2.3 Proposta de Valor
Entregar rotas ordenadas de coleta lógica para operadores no chão de fábrica, minimizando a distância percorrida e validando limites físicos de carga (peso e volume), garantindo observabilidade total para a supervisão.

---

## 3. Objetivos de Negócio e KPIs de Sucesso

### 3.1 Objetivos de Negócio
1. Reduzir o tempo médio de execução de rotas de *picking* nos CDs simulados.
2. Garantir 100% de precisão no cálculo de sequenciamento de coleta via algoritmo.
3. Validar a viabilidade do modelo descentralizado (Web Core em PHP + Engine em Python/Flask).

### 3.2 KPIs do MVP
* **Redução na distância percorrida:** Redução mínima teórica de 15% a 30% em relação a sequenciamentos lineares/nao otimizados.
* **Tempo de resposta da API de otimização:** Resp $T_{resp} < 2.0\text{s}$ para rotas contendo até 50 itens.
* **Acurácia volumétrica:** 0% de ocorrência de rotas geradas com excesso de peso ou volume suportado.

---

## 4. Personas e Atores do Sistema

| Persona | Papel | Principais Dores / Necessidades |
| :--- | :--- | :--- |
| **Administrador** | Gestão de Infraestrutura e Cadastros | Precisa cadastrar a estrutura lógica do armazém, importar listas de pedidos/produtos e gerenciar usuários de forma centralizada. |
| **Supervisor Logístico** | Acompanhamento e Controle de Operação | Precisa acompanhar o status das rotas em tempo real, monitorar a produtividade dos operadores e visualizar relatórios consolidados. |
| **Operador de Picking** | Execução no Chão de Fábrica | Precisa de uma interface clara, direta e sem distrações visuais (lista em sequência exata de coleta) para executar seu trabalho rapidamente. |

---

## 5. Histórias de Usuário (User Stories)

* **US01 [Admin]:** Como administrador, quero cadastrar e mapear as coordenadas lógicas das prateleiras e corredores do armazém para que o motor de otimização entenda a disposição espacial.
* **US02 [Admin]:** Como administrador, quero importar ou cadastrar listas de produtos e pedidos para disponibilizar demandas para otimização.
* **US03 [Operador]:** Como operador, quero visualizar minha lista de tarefas em uma ordem sequencial de percurso para que eu possa coletar os produtos sem idas e voltas desnecessárias.
* **US04 [Operador]:** Como operador, quero marcar cada item como "coletado" para atualizar o status do pedido em tempo real.
* **US05 [Supervisor]:** Como supervisor, quero visualizar um dashboard com os indicadores da operação (pedidos concluídos, tempo médio e rotas pendentes) para tomar decisões operacionais rápidas.

---

## 6. Requisitos do Sistema

### 6.1 Requisitos Funcionais (RF)

| ID | Descrição do Requisito | Prioridade |
| :--- | :--- | :--- |
| **RF01** | **Gestão de Acesso e Autenticação:** Permite login de usuários (Admin, Supervisor, Operador) com controle de sessão e permissões por perfil. | Alta (Must Have) |
| **RF02** | **Mapeamento Lógico do Armazém:** Permite o cadastro e visualização textual de corredores, prateleiras e coordenadas lógicas. | Alta (Must Have) |
| **RF03** | **Gestão de Produtos e Pedidos:** Cadastro manual ou via arquivo CSV contendo SKU, peso, volume e localização física do produto. | Alta (Must Have) |
| **RF04** | **Validação Volumétrica Lógica:** Cálculo automático pré-rota verificando se a soma dos itens atende às restrições do equipamento de coleta. | Alta (Must Have) |
| **RF05** | **Integração com Motor de Otimização:** Envio de payload com coordenadas e itens para a API Python (Flask) e recepção da sequência ordenada. | Alta (Must Have) |
| **RF06** | **Interface Operacional de Picking:** Exibição estilo *checklist* sequencial mobile-friendly para a coleta item a item. | Alta (Must Have) |
| **RF07** | **Painel de Monitoramento (Supervisor):** Visualização de pedidos em andamento, concluídos e operários ativos. | Média (Should Have) |
| **RF08** | **Relatórios Operacionais:** Exibição de relatórios sintéticos de produtividade e histórico de rotas finalizadas. | Média (Should Have) |

### 6.2 Requisitos Não Funcionais (RNF)

| ID | Categoria | Descrição |
| :--- | :--- | :--- |
| **RNF01** | Usabilidade / Interface | Interface web altamente responsiva com foco na facilidade de uso em dispositivos móveis (*Mobile-First* para operadores). |
| **RNF02** | Arquitetura | Arquitetura orientada a serviços via contrato de dados REST/JSON entre a aplicação principal (PHP) e a API matemática (Flask). |
| **RNF03** | Desempenho | O processamento do algoritmo de otimização de rotas via OR-Tools não deve exceder 3 segundos por lote padrão de pedido. |
| **RNF04** | Persistência de Dados | Banco de dados MySQL modelado na 3ª Forma Normal (3FN), garantindo integridade referencial e velocidade de consulta. |

---

## 7. Regras de Negócio (RN)

### RN01 — Isolamento de Tarefas por Operador
O acesso às listas de *picking* é estritamente individual. O operador visualizará apenas as rotas que foram explicitamente atribuídas a ele pelo sistema ou pela supervisão.

### RN02 — Minimização do Deslocamento Total
O motor de roteirização deve calcular o menor caminho percorrido resolvendo o Caixeiro Viajante (*Traveling Salesperson Problem - TSP*) ou o Problema de Roteamento de Veículos (*VRP*), garantindo a menor distância global $D_{total}$:

$$D_{total} = \sum_{i=1}^{n-1} d(x_i, x_{i+1})$$

Onde $d(x_i, x_j)$ representa a distância euclidiana ou de Manhattan entre a localização $x_i$ e $x_j$ do armazém.

### RN03 — Limite de Capacidade Volumétrica e Peso
A geração da rota é negada ou fragmentada se o peso acumulado $P_{total}$ ou volume acumulado $V_{total}$ exceder a capacidade máxima $P_{max}$ e $V_{max}$ do equipamento atribuído:

$$\sum_{k=1}^{m} p_k \le P_{max} \quad \text{e} \quad \sum_{k=1}^{m} v_k \le V_{max}$$

### RN04 — Condicional de Liberação da Rota
A tarefa de coleta só poderá ser exibida ao operador no formato de checklist após o recebimento com sucesso do status `200 OK` e dados estruturados provenientes da API de Otimização.

---

## 8. Arquitetura Técnica e Contrato de Integração

```
  +-----------------------+              HTTP REST / JSON             +-------------------------+
  |                       |  -------------------------------------->  |                         |
  |  Sistema Web (PHP)    |                                           |  API Otimização (Python)|
  |  - Regras de Negócio  |                                           |  - Flask Framework      |
  |  - Gestão de CRUDs    |  <--------------------------------------  |  - Google OR-Tools      |
  |  - Interface Usuário  |              JSON Resposta Rota            |                         |
  +-----------------------+                                           +-------------------------+
              |
              | SQL Query
              v
  +-----------------------+
  |  Banco MySQL          |
  |  - Dados Persistidos  |
  +-----------------------+
```

### 8.1 Contrato de API (Exemplo de Payload)

**Endpoint:** `POST /api/v1/optimize-route`

**Payload de Requisição (Sistema Web -> API Python):**
```json
{
  "cart_capacity": {
    "max_weight_kg": 150.0,
    "max_volume_m3": 1.2
  },
  "start_point": {"x": 0, "y": 0},
  "items": [
    {
      "item_id": "PROD-101",
      "name": "Parafuso Sextavado Aço",
      "location": {"corridor": "A", "shelf": "03", "x": 2, "y": 15},
      "weight_kg": 12.5,
      "volume_m3": 0.05
    },
    {
      "item_id": "PROD-204",
      "name": "Chave de Fenda Chata",
      "location": {"corridor": "B", "shelf": "01", "x": 8, "y": 4},
      "weight_kg": 1.2,
      "volume_m3": 0.01
    }
  ]
}
```

**Payload de Resposta (API Python -> Sistema Web):**
```json
{
  "status": "success",
  "total_distance_meters": 42.5,
  "estimated_time_minutes": 8.0,
  "optimized_sequence": [
    {
      "step": 1,
      "item_id": "PROD-101",
      "location_code": "A-03",
      "instruction": "Coletar no Corredor A, Prateleira 03"
    },
    {
      "step": 2,
      "item_id": "PROD-204",
      "location_code": "B-01",
      "instruction": "Coletar no Corredor B, Prateleira 01"
    }
  ]
}
```

---

## 9. Escopo do Projeto

### 9.1 Dentro do Escopo (MVP)
* Cadastro de Armazém, Localizações e Produtos.
* Validação lógica de restrições volumétricas/peso.
* Algoritmo de otimização de rotas (*TSP*) via Google OR-Tools em Flask.
* Interface responsiva de checklist ordenada para o Operador.
* Painel sintético para o Supervisor com KPIs básicos.

### 9.2 Fora do Escopo (MVP)
* Renderização e simulação gráfica em 2D ou 3D do mapa do armazém.
* Integração nativa via API com ERPs de mercado (ex: SAP, TOTVS).
* Machine Learning para predição de demanda ou reposição dinâmica.

### 9.3 Evoluções Futuras
* Suporte a múltiplos armazéns (multi-CD).
* Leitura de código de barras / QR Code via câmera do dispositivo móvel.
* Algoritmos avançados de agrupamento de pedidos (*Batch Picking*).

---

## 10. Organização da Equipe e Responsabilidades

| Integrante | Papel Principal | Atribuições Chave |
| :--- | :--- | :--- |
| **Leonardo Portaluppi** | Tech Lead & Integration | Definição da arquitetura, integração PHP $\leftrightarrow$ Flask, revisão de código e liderança técnica. |
| **Luana Portelinha** | Developer Python (Data/OR) | Modelagem matemática do TSP/VRP e parametrização do pacote OR-Tools. |
| **Pedro Henrique** | Backend Developer (Python) | Construção e otimização dos endpoints REST na API Flask. |
| **João Celso** | Database Manager & PHP Core | Modelagem/administração do MySQL e regras transacionais no PHP. |
| **Kendy Hashimoto** | Frontend & Integration | Desenvolvimento HTML/CSS/JS, criação de formulários e consumo de rotas. |
| **Raissa Kaori** | UI/UX Designer & Frontend | Prototipação no Figma (mobile-first), design de navegação e layouts das listas. |

---

## 11. Planejamento de Execução (9 Semanas)

```
[Semana 1-2] Modelagem de BD, Setup Flask e Protótipos UI
   └── [Semana 3-4] CRUDs Web e Endpoints Básicos da API
          └── [Semana 5-6] Integração Core PHP <-> API Flask & Testes OR-Tools
                 └── [Semana 7-8] Interfaces de Picking Operacional e Painel de KPIs
                        └── [Semana 9] Testes End-to-End, Refinamento e Validação
```

* **Semanas 1-2:** Setup do ambiente, criação do banco MySQL (João/Pedro), estrutura base Flask/OR-Tools (Leonardo/Luana/Pedro) e prototipagem no Figma (Raíssa/Kendy).
* **Semanas 3-4:** Desenvolvimento dos módulos CRUD no PHP (João/Kendy/Raíssa) e consolidação da API Flask (Leonardo/Luana).
* **Semanas 5-6:** Comunicação HTTP REST entre serviços e validações matemáticas de acurácia de rota (Leonardo/Pedro/Luana).
* **Semanas 7-8:** Finalização do front-end operacional e construção das views do Supervisor (Raíssa/Kendy/João).
* **Semana 9:** Testes integrados (E2E), homologação do MVP e documentação final de entrega.

---

## 12. Matriz de Riscos e Contingência

| Risco Identificado | Impacto | Probabilidade | Plano de Mitigação / Contingência |
| :--- | :--- | :--- | :--- |
| **Falha ou lentidão na comunicação entre PHP e Flask** | Alto | Média | Definição antecipada e rigorosa do contrato JSON com mocks no Postman/Insomnia desde a Semana 2. |
| **Complexidade excessiva na configuração do OR-Tools** | Alto | Alta | Adotar uma solução heurística baseada no Vizinho Mais Próximo (*Nearest Neighbor*) como plano B imediato. |
| **Modelagem de dados insuficiente ou truncada** | Médio | Baixa | Foco estrito apenas nas entidades essenciais do MVP (Usuários, Produtos, Pedidos, Locais e Rotas). |
| **Conflitos no versionamento do código** | Crítico | Alta | Adoção de convenção de ramificação (*GitFlow*) simples com Pull Requests obrigatoriamente revisados pelo Tech Lead. |