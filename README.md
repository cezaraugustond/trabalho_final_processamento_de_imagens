# Solução 2 - Trabalho Final de Processamento de Imagens

# ALUNOS:
- Cézar Augusto Nascimento Dias
- Maxwell Alexandre Souza

# Detecção e Classificação de Bolas de Gude

Este projeto apresenta uma solução para a detecção, segmentação por cor (vermelho e branco) e contagem automática de bolas de gude em imagens, utilizando técnicas de Processamento de Imagens.

---

## 📋 Descrição do Projeto

O objetivo principal deste desafio é identificar esferas (bolas de gude) dispostas sobre uma superfície plana, classificá-las de acordo com sua coloração e quantificar os elementos presentes na cena. O projeto foi desenvolvido como aplicação prática de conceitos fundamentais de Processamento de Imagens.

As principais técnicas aplicadas incluem:
1.  **Espaços de Cores:** Conversão e manipulação nos espectros **RGB** e **HSV** para segmentação cromática robusta.
2.  **Morfologia Matemática:** Utilização de operações de abertura para redução de ruído e separação de objetos.
3.  **Detecção de Bordas e Formas:**  Uso do filtro de Sobel e da Circle Hough Transform (CHT) para identificação paramétrica de círculos.

### 🔗 Links Úteis
* **Vídeo de Apresentação:** [Clique aqui para assistir](https://youtu.be/4oBKUnbY8Pw)
* **Dataset Original (Roboflow):** [Marble Images Challenge](https://universe.roboflow.com/zhe-fan/marble-images)

---

## 📂 Dataset

Para a validação do algoritmo, foram selecionadas **27 imagens** do conjunto de dados original, especificamente do diretório de testes. As imagens apresentam variações de tamanho e disposição dos objetos.

[📁 Acessar Pasta de Imagens](./imagens)

---

### 🔄 Abordagens de Implementação

Diferenças das soluções implementadas:

1.  **Implementação com apenas NumPy:**
    Focada na demonstração teórica e matemática. Nesta abordagem, todos os algoritmos — desde a criação de kernels morfológicos até a lógica de votação da Transformada de Hough — foram construídos manualmente utilizando apenas manipulação matricial com `numpy`, sem o uso de funções prontas de processamento de imagens.

2.  **Implementação usando o Scikit-Image:**
    Focada na eficiência. Nesta abordagem, utilizamos o ecossistema da biblioteca `skimage` para construir um pipeline otimizado. Foram aplicado filtro (`sobel`) e funções morfológicas (`binary_opening`) e de transformações (`hough_circle`) e (`hough_circle_peaks`) .

## 🛠️ Implementação e Metodologia

O desenvolvimento foi estruturado em um *pipeline* sequencial de processamento. Abaixo, detalhamos as etapas cruciais do algoritmo.

[🚀 Acessar Código da Implementação](./codigo)

### Etapas:

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

---

## 💻 Tecnologias Utilizadas

* Python
* Scikit-Image (skimage)
* NumPy
* Matplotlib

---

*Projeto desenvolvido para a disciplina de Processamento de Imagens.*

*Prof. Dr. Leonardo Nogueira Matos*
