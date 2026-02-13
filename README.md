# Trabalho Final de Processamento de Imagens

# ALUNOS:
- Cézar Augusto Nascimento Dias
- Maxwell Alexandre Souza

# Detecção e Classificação de Bolas de Gude (Implementação Manual)

Este projeto apresenta uma solução para a detecção, segmentação por cor (vermelho e branco) e contagem automática de bolas de gude em imagens. 

**Diferencial do Projeto:** Ao invés de utilizar funções de alto nível (como `HoughCircles` do OpenCV ou `canny` do Scikit-Image), nós **reimplementamos os algoritmos fundamentais do zero** utilizando apenas matemática matricial com NumPy. O objetivo foi demonstrar o domínio da lógica por trás das operações de Visão Computacional.

---

## 📋 Descrição do Projeto

O desafio consiste em identificar esferas dispostas sobre uma superfície, classificá-las entre vermelhas e brancas e realizar a contagem. O sistema deve ser robusto a variações de iluminação, sombras e sobreposição parcial.

### Principais Algoritmos Implementados "From Scratch":
1.  **Conversão de Cores:** Fórmulas manuais de RGB para Escala de Cinza (Luminância) e RGB para HSV.
2.  **Operações Morfológicas:** Implementação manual de **Erosão** e **Dilatação** percorrendo a matriz da imagem com janelas deslizantes (*kernels*).
3.  **Detecção de Bordas:** Utilização do **Gradiente Morfológico** (Diferença entre Dilatação e Erosão) ao invés do detector Canny.
4.  **Transformada de Hough Circular (CHT):** Criação do algoritmo de votação e matriz acumuladora para detecção de centros e raios.
5.  **Fusão de Dados (NMS):** Lógica geométrica para eliminar falsos positivos (sobreposições e ruídos internos).

### 🔗 Links Úteis
* **Vídeo de Apresentação:** [Clique aqui para assistir](INSIRA_SEU_LINK_AQUI)
* **Dataset Original (Roboflow):** [Marble Images Challenge](https://universe.roboflow.com/zhe-fan/marble-images)

---

## 📂 Dataset

Foram utilizadas **27 imagens** de teste variadas, contendo diferentes configurações de bolas, iluminação e ângulos. O algoritmo foi validado processando este lote completo para garantir robustez.

[📁 Acessar Pasta de Imagens](./imagens)

---

## 🛠️ Implementação e Metodologia

O código foi desenvolvido focando na manipulação direta de matrizes `numpy`. Abaixo, o pipeline detalhado:

[🚀 Acessar Código Fonte](./codigo.py)

### Etapas do Pipeline:

#### 1. Pré-processamento e Filtragem Espacial
* **Suavização Manual:** Implementamos um filtro de média 3x3 percorrendo a imagem com laços `for` para reduzir a textura do tecido da mesa (ruído de alta frequência).
* **Conversão de Cor:** * Para bolas brancas: `Luminância = 0.299*R + 0.587*G + 0.114*B`.
    * Para bolas vermelhas: Extração baseada na subtração de canais (`Red - Green`).

#### 2. Morfologia Matemática (Sem bibliotecas prontas)
Construímos funções próprias de `erosao()` e `dilatacao()` que verificam a vizinhança de cada pixel.
* Isso foi usado para limpar ruídos binários ("salt and pepper").
* O **Gradiente Morfológico** (Dilatação - Erosão) foi usado para destacar as bordas das esferas para o Hough.

#### 3. Transformada de Hough Circular (Voting System)
Ao invés de chamar uma função pronta, criamos a lógica de votação:
1.  Para cada pixel de borda $(x, y)$, calculamos possíveis centros $(a, b)$ usando a equação paramétrica do círculo.
2.  Incrementamos votos em uma **Matriz Acumuladora**.
3.  Normalizamos o acumulador e aplicamos um limiar de sensibilidade para encontrar os picos (centros das bolas).

#### 4. Fusão Inteligente e Classificação
Implementamos um algoritmo guloso (*Greedy Algorithm*) para unir os resultados:
* Ordenamos os candidatos por tamanho (Raio Maior $\to$ Menor).
* Aplicamos **Distância Euclidiana** para verificar se um círculo menor está contido em um maior (problema da "Matrioska" ou reflexo de luz).
* A classificação final (Vermelho vs. Branco) é feita analisando a Saturação e Valor (HSV) da região central da bola detectada.

---

## 📊 Resultados e Visualização

O sistema gera uma visualização final contendo:
1.  **Overlay Gráfico:** Círculos desenhados sobre as bolas (Ciano para Brancas, Preto/Vermelho para Vermelhas).
2.  **Etiquetas:** Identificação textual ("V" ou "B") no centro de cada objeto.
3.  **Placar Automático:** Um quadro no canto da imagem exibindo a contagem total de cada classe.

---

## 💻 Tecnologias Utilizadas

* **Python 3**
* **NumPy:** Para todas as operações matriciais e matemáticas.
* **Matplotlib:** Apenas para visualização final (plotagem) dos resultados.
* *(Nota: O Scikit-Image/OpenCV foi utilizado apenas para leitura de arquivos, não para o processamento).*

---

*Projeto desenvolvido para a disciplina de Processamento Digital de Imagens.*

*Prof. Dr. Leonardo Nogueira Matos*
