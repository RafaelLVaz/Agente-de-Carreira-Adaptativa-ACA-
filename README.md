# Agente de Carreira Adaptativa (ACA)

Alunos: Rafael Vaz de Lima RM 566429
Felipe Hui Hattori RM 565169
Lucas Kenzo RM 561325

Este projeto implementa um Agente de Carreira Adaptativa (ACA), um assistente de carreira baseado em IA. O objetivo principal é guiar profissionais através dos desafios e oportunidades do futuro do trabalho, com foco principal em **Upskilling** (aprimoramento de habilidades) e **Reskilling** (aquisição de novas habilidades para transição de carreira).

Este agente foi desenvolvido como parte da **Global Solution da FIAP**, para a disciplina de "Prompt and Artificial Intelligence", centrada no tema "O Futuro do Trabalho".

## Arquitetura e Engenharia de Prompt

A eficácia deste agente não se baseia apenas em um LLM, mas em uma arquitetura de código cuidadosamente estruturada para garantir a assertividade e a relevância das respostas. A lógica combina um agente *stateful* (com memória) com técnicas avançadas de engenharia de prompt.

O código utiliza os seguintes pilares:

1.  **LangGraph (Agente Stateful):** O agente é construído como um gráfico de estados (`StateGraph`). Isso é crucial porque permite que o ACA mantenha um **histórico de conversação** (`chat_history`) e execute um fluxo lógico (um "grafo"). A cada pergunta, ele se move entre nós:
    * **Nó `retrieve`:** Busca informações na base de conhecimento (RAG).
    * **Nó `generate`:** Pega o histórico, as informações do RAG e a nova pergunta, e gera uma resposta.

2.  **Engenharia de Prompt Avançada (Role System):** O comportamento central do ACA é definido no `prompt_inicial_template`. Para garantir a máxima assertividade, este prompt não é apenas uma instrução; ele é um "Role System" detalhado que utiliza várias técnicas:
    * **Definição de Identidade (Identity):** O prompt começa definindo claramente *quem* é o agente (`Você é o "Agente de Carreira Adaptativa (ACA)"...`), qual seu propósito e seu foco (Upskilling e Reskilling).
    * **Instruções Detalhadas (Instruction-Tuning):** O prompt fornece regras explícitas sobre como lidar com cada funcionalidade (F1 a F4), como tratar saudações iniciais (proativamente pedir a profissão) e como formatar saídas (usar Markdown).
    * **Técnicas de Few-Shot Learning:** Esta é a parte mais crítica para a assertividade. O prompt inclui uma seção `## Exemplos (Few-Shot)` que demonstra ao LLM exatamente como se comportar. Para cada exemplo, ele fornece a **`<user_query>` (exemplo de query do usuário)** e a **`<assistant_response>` (resposta adequada e formatada)**. Isso treina o modelo no formato exato de resposta, reduzindo drasticamente a chance de ele "improvisar" ou sair do personagem.

3.  **RAG (Retrieval-Augmented Generation):** O agente não depende apenas do seu conhecimento pré-treinado. A cada nova pergunta do usuário, o nó `retrieve` primeiro busca os trechos mais relevantes da base de conhecimento (detalhada abaixo). Esse contexto é **injetado no prompt** enviado ao LLM, garantindo que as respostas sejam embasadas em dados.

Essa **mistura de RAG com Few-Shot Learning** é o que dá poder ao agente. O RAG fornece o *conhecimento* (o "quê") e a Engenharia de Prompt (com few-shot) ensina o *comportamento* (o "como"), garantindo respostas assertivas, estruturadas e alinhadas ao propósito do ACA.

---

## Funcionalidades Principais

O ACA é projetado para executar quatro funções principais, conforme especificado nos requisitos do projeto:

1.  **F1: Análise de Perfil e Risco de Automação:** O agente analisa a profissão e as tarefas do usuário para estimar o risco de automação e identificar habilidades críticas.
2.  **F2: Plano de Upskilling Personalizado:** Com base na análise, o ACA sugere de 3 a 5 áreas de aprimoramento e recursos de aprendizado.
3.  **F3: Sugestão de Caminho de Reskilling:** Para usuários que desejam mudar de carreira, o agente mapeia habilidades transferíveis e sugere novas competências a serem adquiridas.
4.  **F4: Simulação de Entrevista:** O agente simula uma entrevista de emprego para um cenário de Reskilling, fazendo perguntas e avaliando as respostas do usuário.

---

## Base de Conhecimento (RAG): Aumentando a Assertividade

Uma das características mais importantes deste agente é o uso de **Retrieval-Augmented Generation (RAG)**. Antes de gerar qualquer resposta, o sistema consulta uma base de conhecimento vetorizada.

Isso confere **credibilidade** e **assertividade** às suas respostas, pois elas são "aterradas" (*grounded*) em dados atuais e relevantes sobre o mercado de trabalho, em vez de depender apenas da memória de treinamento do LLM.

As fontes de dados utilizadas para esta base de conhecimento foram:

### 1. World Economic Forum (WEF) - Future of Jobs Report 2025
* **Arquivo:** `WEF_Future_of_Jobs_Report_2025.pdf`
* **Importância:** Esta é uma das fontes de maior credibilidade global sobre o futuro do trabalho. O relatório fornece um panorama detalhado sobre tendências de automação, criação de novos empregos, os impactos da IA e quais habilidades (como pensamento analítico, criatividade e IA) serão mais demandadas. As respostas do ACA sobre risco de automação (F1) são fortemente influenciadas por esta fonte.

### 2. Artigos Especializados (URLs)
O agente também é alimentado com artigos de portais de alta reputação em carreira e negócios, focados especificamente na realidade brasileira de transição de carreira:

* **Blog Nubank:** (`https://blog.nubank.com.br/transicao-de-carreira/`)
* **Exame:** (`https://exame.com/carreira/reskilling-e-upskilling.../`)
* **Forbes Brasil:** (`https://forbes.com.br/carreira/2022/09/7-passos-para-uma-transicao-de-carreira-bem-sucedida/`)

**Função:** Essas fontes fornecem contexto prático e conselhos acionáveis sobre os processos de Upskilling e Reskilling (F2 e F3), garantindo que as sugestões do agente sejam realistas e alinhadas com as melhores práticas do mercado.

---

## Tecnologias Utilizadas

* **Python**
* **OpenAI:** GPT-4o-mini (LLM) e text-embedding-ada-002 (Embeddings)
* **LangChain & LangGraph:** Para orquestração do agente, RAG e gestão de estado.
* **LangChain Community:** Para carregadores de dados (`UnstructuredURLLoader`, `PyPDFLoader`).
* **Google Colab:** Como ambiente de desenvolvimento.

## Como Executar

1.  **Ambiente:** Este código foi projetado para ser executado no Google Colab.
2.  **API Key:** Configure sua `OPENAI_API_KEY` nos "Secrets" do Google Colab.
3.  **Upload do PDF:** Para que o RAG funcione corretamente, você **deve** fazer o upload do arquivo `WEF_Future_of_Jobs_Report_2025.pdf` para o diretório `/content/` do seu ambiente Colab.
4.  **Execução:** Instale as dependências (se necessário) e execute todas as células.
5.  **Interação:** O agente iniciará um loop de chat no final do notebook. Digite suas perguntas para interagir com o ACA ou digite "sair" para encerrar.
