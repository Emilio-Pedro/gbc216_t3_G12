# Laboratório 1 - Amostragem e Quantização

## Reamostragem

A reamostragem altera a quantidade de pixels da imagem.
Ao reduzir o DPI, reduzimos a quantidade de linhas e colunas da matriz da imagem, diminuindo a resolução espacial.

O algoritmo implementado utiliza vizinho mais próximos, mapeando cada pixel da nova imagem para um pixel correspondente da imagem original.

## Quantização

A quantização reduz a quantidade de níveis de cinza da imagem, causando perda de informação tonal. Entretanto, em alguns casos, essa redução pode destacar padrões, bordas ou estruturas específicas, auxiliando determinadas análises visuais.

Uma imagem de 8 bits possui 256 níveis de cinza. Ao reduzir a quantidade de bits, diminuímos a quantidade de tons possíveis.
Número de níveis:

niveis = 2^bits


O passo da quantização foi calculado por:

passo = 256/niveis ou 
passo = 255/ (niveis -1) -- para incluir 0 e 255 preservando o valor max(255)

# Laboratório 2 - Componentes Conexos

## Componentes Conexos

A rotulação de componentes conexos é uma técnica utilizada para identificar e separar objetos presentes em uma imagem binária.

Cada grupo de pixels conectados recebe um rótulo (label) diferente, permitindo identificar individualmente cada objeto da imagem.

---

## Vizinhança

Foram utilizados componentes conexos com conectividade 8.

Na vizinhança 8, um pixel é considerado conectado aos pixels:
- acima
- abaixo
- esquerda
- direita
- diagonais

Isso permite que pixels conectados diagonalmente pertençam ao mesmo objeto.

No OpenCV, a rotulação foi realizada utilizando:

```python
cv2.connectedComponents()
```

e

```python
cv2.connectedComponentsWithStats()
```

---

## Rotulação dos Objetos

Após a rotulação:
- cada objeto recebeu um identificador numérico
- os labels foram visualizados utilizando:
  - níveis de cinza
  - cores aleatórias

A imagem em tons de cinza representa os diferentes labels utilizando intensidades diferentes.

A imagem colorida facilita a visualização individual de cada componente conexo.

---

## Binarização

Para detectar os objetos, foi necessário binarizar a imagem utilizando threshold.

Pixels:
- acima do limiar foram considerados fundo
- abaixo do limiar foram considerados objetos

Exemplo utilizando OpenCV:

```python
_, binaria = cv2.threshold(
    imagem,
    167,
    255,
    cv2.THRESH_BINARY_INV
)
```

---

## Contagem de Objetos

Após a rotulação, foi possível calcular automaticamente a quantidade de objetos presentes na imagem.

O OpenCV retorna:
- quantidade total de labels
- matriz contendo os rótulos de cada pixel

O fundo também recebe um label, portanto:

```python
quantidade_objetos = num_labels - 1
```

---

## Distância Entre Objetos

A distância média entre os objetos foi calculada utilizando os centroides dos componentes conexos.

Os centroides representam o ponto central de cada objeto.

A distância entre dois centroides foi calculada utilizando distância Euclidiana.

Também foi gerado um histograma contendo a distribuição das distâncias entre os objetos.

---

## Transformada Distância

A Transformada Distância calcula, para cada pixel do objeto, a distância até o pixel de fundo mais próximo.

Regiões centrais dos objetos apresentam valores maiores de distância, enquanto regiões próximas às bordas possuem valores menores.

No OpenCV, a transformada foi calculada utilizando:

```python
cv2.distanceTransform()
```

A visualização da transformada permite destacar regiões centrais e estruturas internas dos objetos.

# Laboratório 3 - Equalização de Histogramas

## Histograma de Imagens

O histograma representa a distribuição dos níveis de intensidade de uma imagem.

- eixo X → níveis de cinza (0 a 255)
- eixo Y → quantidade de pixels em cada intensidade

Imagens com baixo contraste normalmente possuem histogramas concentrados em uma pequena faixa de intensidades.

---

## Equalização de Histograma

A equalização de histograma é uma técnica utilizada para melhorar o contraste da imagem.

O objetivo é redistribuir os níveis de intensidade para utilizar melhor toda a faixa disponível de tons de cinza.

Após a equalização:
- regiões escuras ficam mais visíveis
- detalhes escondidos aparecem
- o contraste da imagem aumenta

No OpenCV, a equalização foi realizada utilizando:

```python
cv2.equalizeHist()
```

---

## Funcionamento do Algoritmo

O algoritmo implementado manualmente realizou os seguintes passos:

1. cálculo do histograma
2. normalização dos valores
3. cálculo da soma acumulada (CDF)
4. criação da curva de transformação
5. aplicação da transformação nos pixels

---

## Implementação Vetorizada

A implementação foi feita utilizando operações vetorizadas do NumPy, evitando o uso excessivo de laços `for`.

A transformação foi aplicada diretamente sobre a imagem:

```python
imagem_equalizada = transformacao[imagem]
```

Essa abordagem é mais rápida e eficiente do que percorrer pixel por pixel.

---

## Aplicação Repetida da Equalização

A equalização foi aplicada duas vezes consecutivas na mesma imagem.

Foi observado que:
- a primeira equalização altera bastante o contraste
- a segunda equalização produz poucas mudanças

Isso ocorre porque o histograma já fica relativamente distribuído após a primeira aplicação.

---

## Curva de Transformação

A curva de transformação representa como os níveis de intensidade originais foram convertidos após a equalização.

Ela é baseada na distribuição acumulada dos pixels da imagem.

# Laboratório 4 - Filtragem Espacial

## Filtragem Espacial

Filtros espaciais são operações aplicadas diretamente sobre os pixels da imagem utilizando uma vizinhança local chamada máscara ou kernel.

O objetivo pode ser:

* suavizar ruídos
* reduzir detalhes
* realçar estruturas
* preparar a imagem para etapas posteriores de processamento

Cada pixel da imagem é recalculado com base nos pixels vizinhos definidos pela máscara.

---

## Filtro da Média

O filtro da média realiza a suavização da imagem substituindo cada pixel pela média dos valores presentes em sua vizinhança.

Exemplo de máscara:

```python
3x3
```

ou

```python
7x7
```

No OpenCV foi utilizado:

```python
cv2.blur()
```

---

## Funcionamento

Para cada pixel:

1. seleciona-se a vizinhança definida pela máscara
2. calcula-se a média dos valores
3. o pixel central recebe esse valor médio

Isso reduz variações locais e suaviza a imagem.

---

## Efeito do Tamanho da Máscara

Foram aplicados:

* filtro média 3×3
* filtro média 7×7
* filtro média 3×3 aplicado três vezes consecutivas

Observou-se que:

* máscaras menores preservam mais detalhes
* máscaras maiores produzem maior borramento
* aplicações sucessivas aumentam gradualmente a suavização

O filtro 7×7 apresentou o maior nível de borramento da imagem.

---

## Filtro Gaussiano

O filtro Gaussiano também realiza suavização, porém utiliza pesos diferentes para cada pixel da vizinhança.

Pixels mais próximos do centro possuem maior influência no resultado.

No OpenCV foi utilizado:

```python
cv2.GaussianBlur()
```

---

## Desvio Padrão (σ)

O parâmetro σ (sigma) controla a dispersão da função Gaussiana.

* σ pequeno → suavização leve
* σ grande → suavização intensa

Foram utilizados:

```python
σ = 1.0  → kernel 5×5
σ = 2.0  → kernel 9×9
σ = 4.0  → kernel 15×15
```

Observou-se que o aumento de σ e do tamanho da máscara produz maior borramento e redução de detalhes.

---

## Comparação entre Média e Gaussiano

Ambos os filtros realizam suavização.

Diferença principal:

* Filtro da Média:

  * todos os pixels possuem o mesmo peso
  * produz borramento mais uniforme

* Filtro Gaussiano:

  * pixels centrais possuem maior peso
  * preserva melhor as bordas
  * gera resultados visualmente mais naturais

---

## Ruído Salt and Pepper

O ruído Salt and Pepper é caracterizado pelo aparecimento de pixels isolados muito claros (salt) e muito escuros (pepper).

Esse tipo de ruído afeta significativamente a qualidade visual da imagem.

---

## Filtro da Mediana

O filtro da mediana substitui cada pixel pela mediana dos valores presentes em sua vizinhança.

No OpenCV foi utilizado:

```python
cv2.medianBlur()
```

Foram aplicadas máscaras:

```python
3×3
```

e

```python
7×7
```

---

## Funcionamento do Filtro da Mediana

Para cada pixel:

1. seleciona-se a vizinhança
2. os valores são ordenados
3. calcula-se a mediana
4. o pixel central recebe esse valor

Exemplo:

```text
10  12  15
11 255  14
13  12  11
```

Valores ordenados:

```text
10 11 11 12 12 13 14 15 255
```

Mediana:

```text
12
```

O valor 255, característico do ruído, é removido naturalmente.

---

## Implementação Manual da Mediana

O filtro da mediana também foi implementado manualmente utilizando:

```python
numpy.median()
```

Sem utilizar:

```python
cv2.medianBlur()
```

Para cada pixel:

```python
janela = img_pad[i:i+tamanho, j:j+tamanho]
resultado[i,j] = np.median(janela)
```
