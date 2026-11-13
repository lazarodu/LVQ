# Simulador Didático de LVQ e Redes Counter-Propagation

Aplicação interativa em **Single File HTML/JS** desenvolvida para simulação visual, análise matemática e exploração pedagógica de algoritmos de **Quantização Vetorial por Aprendizado** (*Learning Vector Quantization* — **LVQ**) e redes **Counter-Propagation** (CPN).

O simulador é integralmente fundamentado no **Capítulo 9** do livro:
> **Redes Neurais Artificiais para Engenharia e Ciências Aplicadas**  
> Ivan Nunes da Silva, Danilo Hernane Spatti & Rogério Andrade Flauzino.

---

## 🎯 Visão Geral e Recursos

O aplicativo é executado de forma 100% *client-side* no navegador web (sem necessidade de backend, banco de dados ou dependências externas instaladas) e inclui:

1. **Topologia da Rede e Espaço de Decisão:**
   - **Diagrama de Voronoi Interativo:** Pintura em tempo real das fronteiras de decisão por partição de Voronoi no espaço bidimensional de atributos projetados.
   - **Trajetória dos Protótipos:** Rastro visual dos vetores de pesos/protótipos $\mathbf{w}_j$ ao longo das épocas de treinamento, permitindo observar como se posicionam nas médias das classes ou nas fronteiras de separação.
   - **Arquitetura da Rede:** Visualização esquemática da camada de entrada ($n$ neurônios), camada competitiva de Kohonen ($n_1$ protótipos) e camada de saída de Grossberg (*Outstar*, com $n_2$ saídas para Counter-Propagation).

2. **Presets de Aplicação e Benchmarks:**
   - **★ Livro: Demanda de Potência (Tabelas 9.1 e 9.2):** Estudo de caso clássico de previsão/classificação da demanda horária de potência elétrica medida ao longo de 6 horários (7h às 12h, $x_1 \dots x_6$) em 4 tipos/classes de dias (Dias de Semana, Sábados, Domingos e Feriados). Inclui os dados de teste da Tabela 9.2 com avaliação automática de taxa de acerto.
   - **2 Classes Sobrepostas:** Demonstração do comportamento de atração e repulsão em distribuições gaussianas com intersecção de fronteira.
   - **Classes Concêntricas (Exercício 6 do livro):** Problema não-linear separável circularmente, ilustrando a distribuição de múltiplos protótipos por classe.
   - **4 Grupos em XOR:** Problema não-linearmente separável clássico demonstrando a flexibilidade da quantização vetorial supervisionada.

3. **Algoritmos Implementados (Conforme as Seções do Capítulo 9):**
   - **LVQ-1 (Equação 9.2):**
     Determina o neurônio vencedor $v = \arg\min_j \|\mathbf{x} - \mathbf{w}_j\|$ (Equação 9.1).
     - Se a classe do protótipo vencedor confere com o rótulo da amostra ($C_v = C_{\mathbf{x}}$), atrai:
       $$\mathbf{w}_v(t+1) = \mathbf{w}_v(t) + \eta(t) \cdot (\mathbf{x} - \mathbf{w}_v(t))$$
     - Se a classe diverge ($C_v \ne C_{\mathbf{x}}$), repele:
       $$\mathbf{w}_v(t+1) = \mathbf{w}_v(t) - \eta(t) \cdot (\mathbf{x} - \mathbf{w}_v(t))$$
   - **LVQ-2 (Equações 9.3 e 9.4):**
     Atualiza o vencedor $v$ e o vice-vencedor $m$ simultaneamente quando um deles possui a classe correta e o outro incorreta, empurrando a fronteira de Voronoi para a posição de separação ideal de Bayes.
   - **LVQ-2.1 com Janela (Equação 9.5):**
     Aplica o ajuste do LVQ-2 apenas se a amostra cair dentro da janela de simetria definida por:
     $$\min\left(\frac{d_v}{d_m}, \frac{d_m}{d_v}\right) > \frac{1 - \lambda}{1 + \lambda}$$
   - **Rede Counter-Propagation (CPN - Equação 9.6):**
     Treinamento em dois estágios:
     1. *Estágio 1 (Não-Supervisionado):* Ajuste competitivo dos protótipos de Kohonen ($\mathbf{W}_1$).
     2. *Estágio 2 (Supervisionado - Grossberg Outstar):* Ajuste dos pesos da camada de saída ($\mathbf{W}_2$) associados ao neurônio vencedor:
        $$\mathbf{W}_{2,j} \leftarrow \mathbf{W}_{2,j} + \eta \cdot (\mathbf{d} - \mathbf{W}_{2,j})$$

4. **Operação e Testes (Inferência):**
   - Classificação das amostras de teste da Tabela 9.2 do livro texto.
   - Sonda interativa: clique direto em qualquer ponto do canvas para testar e ver o protótipo mais próximo, as distâncias euclidianas e a classe atribuída.

5. **Métricas e Monitoramento:**
   - Erro de classificação no conjunto de treinamento e teste (% de acertos/erros).
   - Erro Médio de Quantização ($QE$).
   - Gráfico de convergência da taxa de erro e taxa de aprendizado ao longo das épocas.

---

## 🚀 Como Executar

Por ser uma aplicação autocontida em HTML5, CSS3 e JavaScript puro:

1. Abra diretamente o arquivo `simulador_lvq.html` em qualquer navegador moderno:
   ```bash
   # No Linux / WSL:
   xdg-open simulador_lvq.html
   ```
   *Ou execute um duplo clique sobre o arquivo no gerenciador de pastas.*

2. Não requer servidores locais, nem instalação de dependências ou passos de compilação.

---

## 🕹️ Guia de Uso Passo a Passo

### 1. Seleção do Estudo de Caso e Algoritmo
- No painel lateral esquerdo (**Parâmetros do Modelo**), selecione o conjunto de dados desejado (ex.: `★ Livro: demanda de potência`).
- Escolha o algoritmo de treinamento: **LVQ-1**, **LVQ-2**, **LVQ-2.1** ou **Counter-Propagation**.
- Defina o número de protótipos por classe ($K$), a taxa de aprendizado inicial $\eta_0$, a largura de janela $\lambda$ (para LVQ-2.1) e o critério de normalização dos vetores.

### 2. Treinamento da Rede
- Clique em **Passo a Passo** para inspecionar amostra por amostra:
  - Verifique o neurônio vencedor e o vice-vencedor calculados por distância euclidiana.
  - Veja na caixa de fórmulas se o protótipo atraiu ou repeliu a amostra.
- Clique em **Executar Época** ou **Treinar** para convergência contínua com parada automática por limite de épocas ou tolerância $\epsilon$.

### 3. Validação e Teste
- No painel direito (**Equações Conforme o Livro**), verifique a tabela de distâncias euclidianas para o vencedor e vice.
- Na seção **Operação · Classificar amostra**, teste as amostras dos dias de teste da Tabela 9.2 ou clique no plano do gráfico para classificar qualquer coordenada arbitrária.

---

## 📁 Estrutura do Arquivo

- `simulador_lvq.html`: Arquivo único integrado contendo:
  - Interface moderna dark mode responsiva.
  - Conjunto de dados completo das Tabelas 9.1 e 9.2 do livro texto.
  - Motor de álgebra vetorial e simulação estocástica reproduzível (`mulberry32`).
  - Renderizador Canvas para partição de Voronoi dinâmica e rede neural interativa.
  - Painel de equações matemáticas vinculadas ao Capítulo 9 do livro texto.

---

## 📚 Referências

- **Silva, I. N., Spatti, D. H., & Flauzino, R. A. (2010).** *Redes Neurais Artificiais para Engenharia e Ciências Aplicadas*. Editora Artliber, São Paulo. (Capítulo 9: *Redes LVQ e Counter-Propagation*).
- **Kohonen, T. (1995).** *Self-Organizing Maps*. Springer Series in Information Sciences, vol. 30. Berlin: Springer.
- **Hecht-Nielsen, R. (1987).** *Counterpropagation networks*. Applied Optics, 26(23), 4979-4984.

