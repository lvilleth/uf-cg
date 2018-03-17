# Rasterização
Projeto realizado para a disciplina de Introdução a Computação Gráfica. 
Um framework OpenGL simulando acesso direto à memória de vídeo foi fornecido. Sobre este foi desenvolvido o código.

## Objetivo
Criar uma solução para rasterizar linhas e pontos. Através da rasterização de linhas serão desenhadas formas geométricas. A cor de cada linha será calculada através da interpolação linear entre as cores de seu vértice inicial e final.

## Concluído
- [X] Pontos
- [X] Linhas
- [X] Interpolação de cores
- [X] Triângulos
- [X] Retângulos
- [X] Círculos

## Desenvolvimento
  A principal técnica utilizada foi o algoritmo de Bresenham para linhas e generalizado para rasterizar círculos. O sistema de cores usado é o RGBA, com cada canal representado por 8 bits. 

### Ponto
  Com acesso direto à memória, o pixel desejado pode ter a intensidade de seus canais de cor alterados através de suas coordenadas x e y. Seja W a largura total da janela, com 4 canais de cores o primeiro byte de cada pixel é encontrado através da fórmula:

<p align="center">  <img src="http://bit.ly/2Dpmgv2"></p>

###### Exemplo de código:
 ``` C
  void PutPixel(tPonto p){
    FBptr[p.x*4 + p.y*IMAGE_WIDTH*4 + 0] = p.cor.R;
    FBptr[p.x*4 + p.y*IMAGE_WIDTH*4 + 1] = p.cor.G;
    FBptr[p.x*4 + p.y*IMAGE_WIDTH*4 + 2] = p.cor.B;
    FBptr[p.x*4 + p.y*IMAGE_WIDTH*4 + 3] = p.cor.A;
}
```
###### Resultado:
<p align="center">  <img width="512" height="80" src="img/pontos.png"></p>

### Linha  
  No sistema de coordenadas desta aplicação, a posição (0,0) encontra-se no canto superior esquerdo. A aplicação direta do algoritmo de Bresenham nestas condições, com `0 <= dy/dx <= 1`, produz retas apenas no 1º quadrante:
  
  <p align="center">  <img width="362" height="328" src="img/linha-bres-1.png"></p>
  
  Para permitir a rasterização de linhas em outras direções foram realizadas operações de simetria:
  
###### Exemplo de código:
  ```C
  if(dx < 0){
      DrawLine(p1, p0);
      return;
    }
  if(dy < 0){
    incremento_y = -1;
    dy = -dy;
  }
  ```
  ###### Resultado:
  
  <p align="center">  <img width="313" height="346" src="img/linha-bres-2.png"></p>
  
  Como já dito, todas as operações anteriores só funcionam para dy < dx. A solução é novamente operações de simetria, desta vez feitas usando Y como referência. O resultado são linhas que podem ser rasterizadas em todas as direções.
  
Em verde:    `dy > dx.` <br>
Em vermelho: `dy <= dx.`

  <p align="center">  <img width="313" height="350" src="img/linha-bres-3.png"></p>
  
### Interpolação de Cor
  A transição de cor entre os vértices foi feito através da interpolação linear dos canais de cor. 

<p align="center"> <img src="img/lerp-cor.jpg">,  <img src="img/lerp-lambda.jpg"> </p>

###### Resultado
<p align="center">  <img width="514" height="162" src="img/cor.png"></p>

### Triângulo e Retângulo
  Fornecidos os respectivos vértices do polígono, utiliza-se o algoritmo de rasterização de linhas para formar suas arestas. 

<p align="center">  <img width="353" height="348" src="img/triangulo.png"> <img width="390" height="348" src="img/retangulo.png"> </p>

### Círculo
A rasterização do círculo a partir de Bresenham baseia-se na simetria da forma. Em um arco de 45º, a escolha do pixel é feita checando qual pixel (N ou S) melhor se aproxima do círculo real de raio R. O restante é rasterizado por reflexão do pixel escolhido em todas as 8 regiões do círculo. 

<p align="center"> <img src="img/canvas3.png"> <img width="403" height="329" src="img/circ-div.png"></p>

###### Resultado:
<p align="center">  <img width="334" height="329" src="img/circulos.png"> <img width="334" height="329" src="img/circulos-2.png"></p>

## Discussão e Problemas Encontrados





