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