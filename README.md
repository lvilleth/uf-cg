# Pipeline Gráfico

## 1. Introdução
O pipeline gráfico consiste em mapear na tela a descrição do modelo geometricamente por meio de uma sequência de transformações lineares. A imagem final é obtida por meio da rasterização das primitivas projetadas na tela (passo realizado no trabalho anterior).
Neste trabalho o objetivo é implementar todas as transformações necessárias para a construção de um pipeline gráfico completo. Cada passo do pipeline descreve uma transformação geométrica de um sistema de coordenadas (espaço) para outro, sendo eles:

<p align="center"><img src="/img/pipelinegrafico.png"> </p>

Para entender um pouco sobre o que ocorre em cada um destes espaços traz-se aqui rápidos conceitos de cada um:

<b>Espaço do Objeto:</b> Espaço em que faz-se a modelagem de um objeto ou uma cena a partir das primitivas gráficas.

<b>Espaço do Universo:</b> Espaço em que ocorre a transição do sistema de coordenadas do objeto para o sistema de coordenadas 3D do ambiente em que será posicionado o objeto. 

<b>Espaço da Câmera:</b> Espaço em que todas as configurações da câmera a ser utilizada são definidas (posicionamento, alcance, projeção).

<b>Espaço de Recorte:</b> Espaço em que ocorre o descarte de objetos a partir do recorte de primitivas gráficas que não se deseja ver ao levar pra tela, aumentando desempenho com a diminuição do tempo de processamento gráfico.

<b>Espaço Canônico:</b> Espaço em que realiza-se o mapeamento dos pontos para centralizar o volume gerado na etapa anterior (recorte) para o espaço canônico. Neste espaço garante-se o que será exibido na tela.

<b>Espaço da Tela:</b> Espaço em que trabalha-se com as coordenadas no espaço canônico e ocorre o processo de rasterização implementado no trabalho anterior.

## 2. Transformações
As transformações que ocorrem ao longo do pipeline gráfico são implementadas utilizando-se coordenadas homogêneas, o que permite a adoção de matrizes para representar cada processo. 
A representação destas por matrizes facilita a manipulação, homogeniza o processo e agiliza a realização das operações algébricas necessárias a cada etapa do pipeline. 

### 2.1 Espaço do Objeto para Espaço do Universo
Esta é a primeira etapa do pipeline gráfico e consiste em multiplicar cada vértice do objeto descrito em seu espaço por uma matriz de modelagem passando estes vértices para o espaço do universo.
A matriz de modelagem (model matrix) consiste na combinação de uma ou mais transformações geométricas como por exemplo translações, escalas e rotações. Todas também representadas através de matrizes. 
Abaixo temos a implementação da primeira matriz de transformação do pipeline.

<p align="center"><img src="/img/matrizmodel.png"> </p>

Como podemos ver na imagem a matriz de modelagem usada é o conjunto das transformações geométricas de escala (dobrada as dimensões) e de rotação.

### 2.2 Espaço do Universo para Espaço da Câmera
Esta é a segunda etapa do pipeline gráfico e consiste em multiplicar cada vértice do objeto descrito no espaço do universo por uma matriz de panorama passando estes vértices para o espaço da câmera.
A matriz de panorama (view matrix) consiste na transformação do objeto para o sistema de coordenadas da câmera. Isto ocorre fazendo inicialmente a definição da posição da câmera, para onde ela está olhando e, ainda, de um vetor "up", que representa que direção será considerado acima da câmera. Logo após estas definições tem-se a construção da base do espaço da câmera com vetores ortonormais.
Utilizando os conceitos de álgebra linear temos que um vetor descrito numa base para ser descrito em outra é levado por uma matriz de transformação, portanto, o processo inverso é feito com a matriz inversa da transformação. Neste caso foi usada a matriz transposta uma vez que temos vetores ortonormais e isto se torna possível.
Abaixo temos a implementação da segunda matriz de transformação do pipeline.

<p align="center"><img src="/img/matrizview.png"> </p>

Como podemos ver na imagem a matriz view é formada pela multiplicação da matriz transposta da base do espaço da câmera e, de translação.

### 2.3 Espaço da Câmera para Espaço de Recorte
Esta é a terceira etapa do pipeline gráfico e consiste em multiplicar cada vértice do objeto descrito no espaço da câmera por uma matriz de projeção passando estes vértices para o espaço de recorte.
A matriz de projeção (projection matrix) consiste inicialmente na definição da distância focal da câmera dando profundidade à visão desta.
A profundidade controla como o objeto irá aparecer na tela de modo que objetos mais próximos aparecem maiores e objetos mais distantes, menores.
Abaixo temos a implementação da terceira matriz de transformação do pipeline.

<p align="center"><img src="/img/matrizprojecao.png"> </p>

Como podemos ver na imagem a matriz de projeção altera a matriz identidade apenas na terceira e quarta colunas adicionando transformações para projeção perspectiva.

### 2.4 Espaço de Recorte para Espaço Canônico
Esta é a penúltima etapa do pipeline gráfico e consiste em eliminar os vértices que não estão na visão da câmera. Realiza-se uma homogeneização dos vértices dividindo-os por sua coordenada homogênea que retorna ao seu valor igual a 1.

### 2.5 Espaço Canônico para Espaço da Tela
Esta é a última etapa do pipeline gráfico (sendo a finalização a rasterização do objeto na tela) e consiste em multiplicar cada vértice do objeto descrito no espaço canônico, explicado no 2.5, por uma matriz específica que envolve translações e escalas relacionadas as dimensões da tela (largura e altura).
Abaixo temos a implementação da quarta matriz de transformação do pipeline.

<p align="center"><img src="/img/matrizviewport.png"> </p>

Como podemos ver na imagem a matriz de projeção altera a matriz identidade de modo a espelhar em y o objeto para que não fique de cabeça para baixo, escalar para ocupar o espaço da tela e transladar para tornar os vértices maiores que zero.

### 2.6 Pipeline Completo
Abaixo temos a implementação do pipeline completo incluindo a divisão pela coordenada homogênea que leva ao espaço canônico e as matrizes implementadas anteriormente.

<p align="center"><img src="/img/espacoderecorte-espacocanonico.png"> </p>

## 3. Resultados
Concluindo o trabalho foi utilizado arquivos do tipo OBJ contendo as faces e os vértices dos objetos a serem tratados. Os objetos utilizados são sempre centrados na origem e a rotação foi feita em torno do eixo Y. Abaixo temos um video com a rotação do objeto com a implementação do nosso pipeline e a comparação com o produzido pelo openGL.
O carregamento dos objetos foi realizado por meio de um LOADER disponibilizado pelo professor da disciplina o qual implementa a estrutura de dados de uma lista para manipulação do arquivo.

Nosso Pipeline | OpenGL
---------------|-------------
<img width="334" height="329" src="img/monkey_pipeline_500.gif"> | <img width="334" height="329" src="img/img/monkey_opengl_500.gif">

## 4. Dificuldades
Entre as dificuldades encontradas esta o carregamento do objeto para o software uma vez que o OpenGL não impleta isto e inicialmente foram feitas tentativas falhas com o Assimp, após o envio do loader pelo professor Christian fez-se progresso nesta etapa. Além disto tivemos dificuldade encontrar os valores compatíveis para o fator de escala, o ângulo para a matriz de rotação e demais valores para os elementos das matrizes.
Como é possível perceber a projeção perspectiva alterou a cena de modo que os vértices mais próximos da tela se tornassem maior. A correção disto está na utilização da projeção ortogonal nas matrizes de modelagem e projeção.

<p align="center"><img src="/img/matrizmodel-projecaoortogonal.png"> </p>

A diferença entre a matriz de modelagem de projeção perspectiva para a de projeção ortogonal está no fato de que a primeira multiplica a matriz identidade por um fator 2 enquanto que a segunda divide por este mesmo fator.

<p align="center"><img src="/img/matrizprojecao-ortogonal.png"> </p>

A diferença entre a matriz de projeção perspectiva para a de projeção ortogonal está no fato de que a segunda utiliza a matriz identidade.

### Correção
Abaixo temos o vídeo comparativo esperado deste trabalho.

<img width="334" height="329" src="img/monkey_pipeline_orto_500.gif">
