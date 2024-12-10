# Detecção e Contagem de Objetos com OpenCV

Esse projeto processa duas imagens diferente e tem como objetivo segmentar os objetos da imagem e realizar a contagem de ambos.

## Descrição

Este projeto tem como objetivo demonstrar como realizar o processamento de imagens utilizando a biblioteca OpenCV em Python. O foco principal é identificar, segmentar, contar e marcar objetos específicos em uma imagem, especificamente M&Ms, utilizando técnicas de visão computacional. Durante o processo, a imagem original pode conter "ruídos" (como milho e feijão) que são tratados para garantir a contagem precisa dos M&Ms.

## Tecnologias Utilizadas

- **Python**: Linguagem de programação.
- **OpenCV**: Biblioteca utilizada para processamento de imagens e visão computacional.
- **NumPy**: Biblioteca usada para manipulação de matrizes, necessária para algumas operações morfológicas.

## Funcionalidades

- **Carregamento de imagem**: A imagem a ser processada é carregada do sistema de arquivos.
- **Redimensionamento da imagem**: A imagem é redimensionada para melhorar a performance.
- **Conversão de BGR para HSV**: A imagem é convertida para o espaço de cores HSV para facilitar a identificação de objetos.
- **Criação de Máscaras**: Máscaras são criadas para identificar as cores dos objetos desejados (M&Ms) na imagem.
- **Operações Morfológicas**: Dilatação e erosão para tratar os ruídos e melhorar a segmentação dos objetos.
- **Contagem e Contornos**: A contagem dos objetos é feita utilizando a detecção de contornos e a área dos objetos identificados.
- **Exibição de Resultado**: Exibição da imagem com os objetos detectados e a contagem de M&Ms por cor.

## Passos para Execução

1. **Instalação das Bibliotecas**:
   
   Primeiro, instale as dependências necessárias utilizando o seguinte comando:
   
   ```bash
   pip install opencv-python numpy

2. **Abrindo a imagem que vai ser processada**:
   
   Esse é o processo mais simples de todo o projeto, consistindo somente na chamada da função cv2.imread, conforme o exemplo da imagem a seguir:
   
   ```bash
   #leitura da imagem
   img = cv2.imread("./imagem_original.jpeg")

3. **Redimensionando a imagem que vai ser processada**:
   
   Para o redimensionamento, foram criadas duas variáveis onde cada uma receberá os valores respectivamente da altura e largura desejada e após isso, estes mesmos valores são os parâmetros da função cv2.resize que mudará de fato a imagem para o tamanho correto.
   
   ```bash
   #redimensionamento da imagem, proporcao 4:3
   new_width = 640
   new_heigth = 480
   new_size = (new_width,new_heigth)

   resize = cv2.resize(img,new_size)

4. **Convertendo a imagem de BGR para HSV e achando as cores**:
   
   A biblioteca OpenCV segue por padrões o sistema de cor BGR que consiste em três canais de cores que são: B — Blue, G — Green, R — Red, canais estes que são encontrados dentro de cada pixel, e assim as cores das imagens são armazenadas com este sistema, seguindo este padrão, o pixel pode variar dentro de cada canal o valor de 0 a 255.

   Porém, para um melhor processamento, é possível passar do sistema BGR (cores primárias) para o HSV, este usa os parâmetros: H — Matriz (tonalidade) que varia de 0 a 179 no OpenCV, S — Saturação (intensidade) que varia de 0 a 255 no OpenCV e V — Valor para identificação da cor que também varia de 0 a 255 no OpenCV. Alguns outros softwares podem adotar outros formatos como por exemplo: S e V variando entre 0 e 100 e H variando até 360 e não 180.
   
   ```bash
   # conversao da imagem de BGR para HSV
   img_hsv = cv2.cvtColor(resize, cv2.COLOR_BGR2HSV)

5. **Criação das Máscaras**:
   
   Como citado no passo anterior, precisamos de uma máscara para usarmos como parâmetro para conseguir segmentar a nossa imagem e acharmos nosso objeto desejado. A máscara é um tipo de filtro que irá filtrar somente as cores passadas como parâmetro e todo o restante será ignorado.

   Após acharmos as cores no passo anterior, usaremos a função cv2.inRange, como demonstrado na imagem abaixo:
   
   ```bash
   #criacao das mascaras
   blue_mask = cv2.inRange(img_hsv,(90,66,153),(130,255,255))
   green_mask = cv2.inRange(img_hsv,(57,50,140),(75,255,255))
   yellow_mask = cv2.inRange(img_hsv,(22,30,190),(30,255,255))
   first_brow_mask= cv2.inRange(img_hsv,(140,15,80),(180,91,215))
   second_brow_mask= cv2.inRange(img_hsv,(0,15,75),(9,91,170))
   red_mask = cv2.inRange(img_hsv,(150,40,130),(180,255,255))
   orange_mask = cv2.inRange(img_hsv,(3,70,100),(13,255,255))

   or_mask_examples = cv2.bitwise_or(blue_mask,green_mask)
   or_mask_brow = cv2.bitwise_or(first_brow_mask,second_brow_mask)

5. **Sobreposição de máscaras com lógicas AND ou OR para análise das mesmas**:
   
   Podemos verificar se a nossa máscara está bem feita antes de iniciarmos os próximos passos do processamento, para isso podemos fazer uma sobreposição dessa máscara na nossa imagem.
   
   ```bash
   and_mask_examples = cv2.bitwise_and(resize,resize,mask = orange_mask)

6. **Dilatação e erosão**:
   
   Chegamos agora em um dos pontos mais importantes de todo o processo, que é quando vamos tratar pixels indesejados na nossa imagem, separar objetos entre si ou até mesmo “melhorar” nossos objetos, todos esses ruídos nas máscaras citados ao longo do artigo serão retirados e/ou minimizados nessa etapa, faremos algumas operações morfológicas.

   Para começar esse tópico, precisamos falar sobre o parâmetro chamado de Kernel:

   O kernel é como se fosse um objeto que criaremos e a dilatação e/ou erosão acontecerá com base no seu formato (quadrado, circunferência, etc…), podemos chamar ele de elemento estruturante.
   
   ```bash
   # kernel para usar na erosao e dilatacao
   kernel = numpy.ones((3,3), numpy.uint8)
   ```
   Para criar o kernel, usaremos a biblioteca numpy, nela conseguimos criar, modificar e acessar vetores e matrizes otimizadamente.

   No código acima é criado uma matriz de formato 3 por 3, ou seja, 3 linhas e 3 colunas formada por valores de número 1. A dimensão 3 por 3, será o formato que será utilizado para erodir e dilatar nossos objetos.

   Sabendo disso, podemos então dilatar ou erodir os objetos.

   Erosão: Diminui o objeto conforme o seu kernel, usaremos a erosão para eliminar os pixels indesejados que podemos considerar ruídos, aplicado conforme o trecho a seguir:

   ```bash
   #filtros erosao nas mascaras, pois todas elas tem ruidos (pixels que nao se enquandram com o que queremos)
   blue_erode= cv2.erode(blue_mask,kernel,iterations=1)
   yellow_erode = cv2.erode(yellow_mask,kernel,iterations=5)
   brow_erode= cv2.erode(or_mask_brow,kernel,iterations=2)
   red_erode = cv2.erode(red_mask,kernel,iterations=1)
   orange_erode = cv2.erode(orange_mask,kernel,iterations=5)
   ```

   No código acima, temos a função cv2.erode, ela é a responsável por erodir nossa imagem conforme precisamos, o primeiro parâmetro recebe nesse caso a mascara de cada cor que achamos, o segundo é o nosso kernel, ou seja, estamos declarando o formato que queremos que ocorra essa erosão na imagem e por fim o terceiro parâmetro é a quantidade de interações que queremos que a erosão aconteça na imagem, lembrando que quanto mais interações mais erodida, ou seja, menores/mais finos os objetos serão.

   Dilatação: A dilatação aumenta o objeto, usaremos para preencher os espaços onde é encontrados algumas falhas de pixels nos objetos achados, ou até mesmo para preencher possíveis objetos que sofreram mais erosão do que o necessário em uma tentativa de retirar os ruídos dos objetos/imagens, aplicado conforme o trecho a seguir:

   ```bash
   #filtros de dilatacao nas mascaras
   blue_dilate = cv2.dilate(blue_erode,kernel,iterations=1)
   gree_dilate = cv2.dilate(green_mask,kernel,iterations=1)
   yellow_dilate = cv2.dilate(yellow_erode,kernel,iterations=6)
   brow_dilate = cv2.dilate(brow_erode,kernel,iterations=4)
   red_dilate = cv2.dilate(red_erode,kernel,iterations=1)
   orange_dilate = cv2.dilate(orange_erode,kernel,iterations=6)
   ```

   O código para executarmos uma dilatação é o mesmo usado para a erosão (explicado logo acima) a única diferença é que ao invés de chamar a função cv2.erode, é chamado a função cv2.dilate, os parâmetros seguem a mesma regra da função da erosão.

7. **Achando os objetos e contornando os mesmos**:
   
   Agora chegamos de fato, na etapa final do nosso processamento onde usaremos a função cv2.findContours para achar os contornos de todos os M&M’s conforme as respectivas máscaras, a função cv2.countourArea é usada para obter a área dos objetos e fazer uma possível filtragem por tamanho, a função cv2.drawContours irá desenhar em volta de cada M&M achado e a função cv2.putText irá escrever um texto na nossa imagem final, como na imagem abaixo:

   ```bash
   #achando os contornos dos mms conforme as mascaras de cores e escrita na imagem da quantidade de mms de cada cor foram contados
   cont = 0
   name = ["azul","verde","amarelo","marrom","vermelho","laranja"]
   color = [(255,0,0),(0,255,0),(0,255,255),(0,75,150),(0,0,255),(0,165,255)]
   array_masks = [blue_dilate,gree_dilate,yellow_dilate,brow_dilate,red_dilate,orange_dilate]
   for i in range(len(array_masks)):
    qtd = 0
    contornos,hierarquia = cv2.findContours(array_masks[i],cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_SIMPLE)
    for x in range(len(contornos)):
        area = cv2.contourArea(contornos[x])
        if(area>400):
            #desenhando os contornos
            desenho_contorno = cv2.drawContours(resize,contornos,x,color[i],2)
            qtd +=1
    cv2.putText(resize,name[i] + ": "+ str(qtd), (10+cont,20), cv2.FONT_HERSHEY_SIMPLEX, 0.5,(0,0,255),1, cv2.LINE_AA)
    cont+=110
   ```

   Inicialmente, foram criados dois vetores, o primeiro contendo as strings com o nome das cores a serem inscritas na imagem e o segundo contendo a última parte do processamento de cada cor, no caso, a dilatação dos objetos achados com o filtro de máscara é a última etapa.

   O FOR irá percorrer essas máscaras já processadas e em cada uma será chamado a função cv2.findContours que recebe como parâmetros a máscara processada e os  outros dois respectivamente o modo a qual será feito o contorno e o quanto ele deve ser preciso, existem diversos valores para serem usados nesses parâmetros mas não cabe a esse artigo a explicação.

   Nós usaremos um dos retorno do cv2.findContours, que foi declarado como contornos, ele será usado como argumento da função cv2.counterArea que contabiliza o tamanho da área de cada contorno (objeto), assim caso ainda depois da dilatação e erosão sobre alguma possibilidade de o milho ou feijão serem encontrados, faremos essa exceção do tamanho pois é evidente que estes objetos tem tamanho diferente dos M&Ms .

   Já a função cv2.drawContours irá de fato desenhar os contornos nos nossos objetos e após desenhar todos os objetos de uma imagem chamamos a função cv2.putText que irá escrever a cada execução do FOR principal, a quantidade de M&Ms por cor na imagem final processada.

8. **Mostrando a imagem final e salvando-a**:
   
   Chegamos no final desse percurso que foi o nosso processamento, agora é só chamar a função cv2.imshow que vai nos mostrar a imagem final abaixo quando executarmos este código.

   ![image](/processamentoComRuido/imagem_apos_processamento.jpeg)

## Imagens de Entrada

### Células
![image](/processamento_CelulasSanguineas/img_original.jpg)

### MM's
![image](/processamento_mm_separado_coresMisturadas/imagem_redimensionada.jpeg)
![image](/processamentoComRuido/imagem_redimensionada.jpeg)


## Imagens de Saída

### Células
![image](/processamento_CelulasSanguineas/celulas_processadas.jpeg)

### MM's
![image](processamento_mm_separado_coresMisturadas/imagem_final_processada.jpeg)
![image](/processamentoComRuido/imagem_apos_processamento.jpeg)




