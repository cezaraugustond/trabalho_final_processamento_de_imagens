# Trabalho Final de Processamento de Imagens

**Alunos:**
* Cézar Augusto Nascimento Dias
* Maxwell Alexandre Souza

---

# Detecção e Classificação de Bolas de Gude

Este projeto apresenta uma solução para a detecção, segmentação por cor (vermelho e branco) e contagem automática de bolas de gude em imagens, utilizando técnicas de Processamento Digital de Imagens.

---

## 📋 Descrição do Projeto

O objetivo principal deste desafio é identificar esferas (bolas de gude) dispostas sobre uma superfície plana, classificá-las de acordo com sua coloração e quantificar os elementos presentes na cena. O projeto foi desenvolvido como aplicação prática de conceitos fundamentais de Processamento Digital de Imagens.

Para explorar diferentes paradigmas de implementação, desenvolvemos **duas soluções distintas** para o mesmo problema:

1.  **Solução 1 (Abordagem Low-Level):** Focada na construção manual dos algoritmos (criação de kernels, lógica matemática da Transformada de Hough e manipulação direta de matrizes NumPy).
2.  **Solução 2 (Abordagem High-Level):** Focada na eficiência, utilizando o ecossistema `scikit-image` e filtros otimizados (Sobel/Hough) para um pipeline de produção.

---

## 📂 Acesso Rápido às Soluções
| **Código** | [📁 Acessar Pasta](./solucao_1_manual) | [📁 Acessar Pasta](./solucao_2_skimage) |
| **Vídeo** | [📺 Assistir no YouTube](https://youtu.be/nEgp7ryd_M4?si=ISw-WttdX29g7qhN) | [📺 Assistir no YouTube](INSIRA_SEU_LINK_AQUI) |

---

## 📦 Dataset Utilizado

Ambas as soluções foram validadas utilizando o mesmo conjunto de dados para garantir comparabilidade.
* **Fonte:** [Marble Images Challenge (Roboflow)](https://universe.roboflow.com/zhe-fan/marble-images)
* **Amostragem:** Foram utilizadas **27 imagens** de teste variadas, contendo diferentes configurações de iluminação, sombras, sobreposições parciais e ângulos.

[📁 Ver Imagens do Dataset](./imagens)

---

## 1️⃣ Detalhes da Solução 1:

Esta abordagem priorizou a construção dos algoritmos "do zero", minimizando o uso de funções prontas de processamento de imagem para demonstrar o domínio da lógica matricial.

### Metodologia e Pipeline
O código foca na manipulação direta de matrizes `numpy`.

#### 1. Pré-processamento e Filtragem Espacial
* **Suavização Manual:** Implementamos um filtro de média 3x3 percorrendo a imagem com laços `for` para reduzir a textura do tecido da mesa (ruído de alta frequência).
* **Conversão de Cor** 

#### 2. Morfologia Matemática 
Construímos funções próprias de `erosao()` e `dilatacao()` que verificam a vizinhança de cada pixel.
* Isso foi usado para limpar ruídos binários ("salt and pepper").
* O **Gradiente Morfológico** (Dilatação - Erosão) foi usado para destacar as bordas das esferas para o Hough.

#### 3. Transformada de Hough Circular 
Ao invés de chamar uma função pronta, criamos a lógica de votação:
1.  Para cada pixel de borda $(x, y)$, calculamos possíveis centros $(a, b)$ usando a equação paramétrica do círculo.
2.  Incrementamos votos em uma **Matriz Acumuladora**.
3.  Normalizamos o acumulador e aplicamos um limiar de sensibilidade para encontrar os picos (centros das bolas).

#### 4. Fusão Inteligente e Classificação
Implementamos um algoritmo guloso (*Greedy Algorithm*) para unir os resultados:
* Ordenamos os candidatos por tamanho (Raio Maior $\to$ Menor).
* Aplicamos **Distância Euclidiana** para verificar se um círculo menor está contido em um maior.
* A classificação final (Vermelho vs. Branco) é feita analisando a Saturação e Valor (HSV) da região central da bola detectada.


### Tecnologias
* **Python 3** & **NumPy** (Cálculo matricial pesado).
* **Matplotlib** (Apenas para visualização).

---

## 2️⃣ Detalhes da Solução 2: 

Esta abordagem focou na criação de um *pipeline* utilizando as ferramentas otimizadas da biblioteca `scikit-image`.

#### 1. Pré-processamento e Conversão de Cor (RGB $\to$ HSV)
As imagens são convertidas do espaço RGB para HSV (*Hue, Saturation, Value*). O espaço HSV é preferível pois separa a informação de cor (Matiz) da intensidade luminosa (Valor), facilitando a segmentação em ambientes com sombras ou brilho variável.

#### 2. Segmentação por Limiarização (Thresholding)
Foram criadas máscaras binárias distintas para cada classe:
* **Vermelho:** Devido à natureza circular do canal Hue (o vermelho está no início e no fim do espectro), combinamos duas faixas de valores (0-10° e 350-360°).
* **Branco:** Segmentado através da baixa saturação e alto valor de brilho.

#### 3. Tratamento Morfológico
Para eliminar ruídos (pixels isolados) e suavizar as formas detectadas, aplicou-se a operação de **Abertura Binária** (*Binary Opening*) utilizando um elemento estruturante em forma de disco. Isso garante que apenas regiões com área significativa sejam processadas nas etapas seguintes.

#### 4. Detecção de Bordas (Sobel)
Antes de detectar as formas paramétricas, aplicamos o operador de **Sobel** nas máscaras morfológicas já tratadas. Como as nossas imagens nesta etapa já são binárias (fundo preto e esferas brancas sólidas), o filtro de Sobel atua calculando o gradiente espacial da imagem, o que destaca perfeitamente as áreas de transição. Isso gera mapas de contornos claros e definidos, que servem como a entrada exata e otimizada que a Transformada de Hough exige para funcionar.

#### 5. Transformada de Hough Circular (CHT)
O "coração" do projeto. Utilizamos a função `hough_circle` e `hough_circle_peaks` para encontrar padrões circulares.
* **Varredura de Raios:** Definimos um intervalo dinâmico (`np.arange`) para capturar desde as menores até as maiores esferas.
* **Filtragem de Picos:** Implementamos limiares de distância (`min_distance`) e de votos (`threshold`) para evitar falsos positivos e detecções sobrepostas.

#### 6. Visualização dos Resultados
Os círculos detectados são desenhados sobre a imagem original utilizando vetores gráficos (Matplotlib), permitindo a conferência visual da precisão do algoritmo, com diferenciação clara entre as classes (Vermelho e Branco).

### Tecnologias
* **Python 3**
* **Scikit-Image (skimage)**
* **NumPy**
* **Matplotlib**

---

## 📊 Resultados Gerais

Ambos os sistemas geram visualizações contendo:
* Overlay gráfico (círculos desenhados) sobre a imagem original.
* Classificação cromática (Vermelho vs. Branco).
* Contagem total dos elementos em cena.

---
