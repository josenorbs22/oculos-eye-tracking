# Óculos para *Eye tracking*

## Introdução

Este projeto foi desenvolvido dentro do Laboratório de Sinais, IoT e Imagens Médicas para uso como Trabalho de Conclusão de Curso e utilizado e desenvolvido parcialmente na disciplina de Projeto de Sistemas Ubíquos e Embarcados. O objetivo do projeto é produzir um protótipo para uso de médicos e profissionais de fisioterapia para diagnóstico de doenças intelectuais ou visuais em crianças recém-nascidas, principalmente prematuras através de traçamento ocular (*Eye tracking*).

## Descrição do objeto de desenvolvimento

Óculos feito a partir de modelagem e impressão 3D com câmeras acopladas para filmagem dos olhos e do ponto de vista do usuário para se ter capacidade de traçamento ocular. A câmera dos olhos terá câmeras que captam frequências próximas ao infravermelho (NIR) e a câmera do ponto de vista será uma câmera de ângulo de abertura alto. Estas câmeras, ligadas a um microcontralador ESP32 enviam as imagens em tempo real para um computador por conexão wi-fi para gravação do procedimento de exame do paciente. Um aplicativo Javascript obtém as imagens transmitidas em um Servidor Web fornecido pelo ESP32 através de wi-fi e faz a captura sincronizada do feed das duas câmeras. Um software desenvolvido no LSIIM então obtém estas gravações e faz o mapeamento da direção do olhar do paciente e de para onde ele está olhando.

## Requisitos

A seguir, consta alguns requisitos, separados em requisitos funcionais e não funcionais, referentes ao projeto desenvolvido na disciplina.

### Requisitos Funcionais

- **RF1**: Transmitir as imagens obtidas pelas câmeras, seja de forma síncrona, em tempo real, ou assíncrona, através de arquivo de vídeo;
- **RF2**: O óculos deve ter pelo menos uma câmera capaz de capturar imagens com comprimento de onda próximo ao Infravermelho (NIR) apontada para os olhos do usuário;
- **RF3**: O óculos deve ter uma câmera com ângulo de visão alto para capturar o ponto de vista do usuário;
- **RF4**: O software de traçamento ocular deve informar a direção do olhar do usuário em relação ao tempo.

### Requisitos Não Funcionais

- **RNF1**: A transmissão ou gravação do vídeo deve ter taxa de quadros constante, ou seja, com o mínimo possível de congelamentos;
- **RNF2**: A armação deve ter tamanho e formato adequados para o uso de um bebê recém-nascido prematuro;
- **RNF3**: A relação de direção do olhar por tempo deve ser apresentada de forma clara e legível.

## Materiais e Métodos

Nesta seção será descrito os itens usados para o desenvolvimento do projeto, assim como o processo de configuração destes.

### Itens usados

- **(2x) ESP32-S3**: este microcontrolador foi usado devido a sua versatilidade e facilidade de uso com câmeras e da comunicaçao com o computador externo através de USB ou wi-fi. Poderia ter sido usado também o modelo S2 já que ambos possuem interface USB-OTG, importante para transmissão da imagem da câmera por USB, porém, não foi encontrado nenhuma *dev board* que possuísse entrada para câmeras OV2640, OV5640 ou similares. Além disso, poderia também ter sido usado um ESP32-CAM básico, entretanto, este não possui interface USB-OTG impossibilitando o uso dele como webcam USB e não possui alguns recursos que possibilitariam, no futuro, rodar algoritmos de traçamento ocular.
- **Câmera OV2640 850nm**: câmera compatível com o ESP32 que capta ondas de luz de 850nm de comprimento, ou seja, próximas ao infravermelho. Esta câmera faz com que as pupilas fiquem consideravelmente mais contrastadas na imagem, o que facilita drasticamente o *eye tracking*.
- **Câmera OV2640 200°**: esta câmera fica apontada para frente em relação ao rosto do usuário e serve para obter imagens dos estímulos visuais usados pelos examinadores. Necessita de grande ângulo de visão (pelo menos 180°) para se ter certeza de que todo o campo de visão (e, possivelmente, até além) da pessoa seja capturado.
- **Cabos USB (Tipo C ou micro, a depender do modelo da placa de desenvolvimento do ESP32)**: cabos para transmitir dados e/ou alimentar eletricamente os ESPs.
- **Computador**: qualquer máquina capaz de interpretar programas em python, rodar navegadores Web e ter conexão wi-fi com capacidade de criar hotspot.

### Fluxograma de dados do sistema

Este é o fluxograma do sistema para a troca de dados entre os dispositivos:
![Fluxograma do sistema](/fluxograma_ubiquos_wifi.png)

### Diagrama elétrico

O diagrama elétrico a seguir demonstra apenas como foi montado o sistema para este trabalho, entretanto, os ESPs podem funcionar com qualquer fonte de energia de 5V que forneça corrente mínima para o funcionamento destes (algo em torno de 0,5 A):
![Diagrama Elétrico da montagem do sistema](/diagrama_eletrico.png)

### Procedimentos

#### Programação do ESP32

Para utilizar o ESP32 como webcam USB os seguintes passos:
1. No Visual Studio Code, procure pela extensão ESP-IDF e a instale;
2. Baixe o esp-iot-solution no git: https://github.com/espressif/esp-iot-solution.git ;
3. Abra a pasta baixada no VSC;
4. Uma página de "Welcome" do ESP-IDF deve abrir. Nela, clique em "Configuring the ESP-IDF Extension" e depois em "Configure ESP-IDF Extension";
5. Na página de Setup, escolha a opção EXPRESS;
6. Selecione a versão do ESP-IDF. Neste caso, pode-se baixar uma versão direto do git do ESP-IDF e usar a opção "Find ESP-IDF in your system", ou escolher uma versão para download. No momento da escrita, a última versão é a 5.4.1 e será a usada para este exemplo;
7. Depois que o download e instalação concluir, abra a pasta "esp-iot-solution/examples/camera/video-stream-server" (pelo menu "File" e clicando em "Open folder", não pelo navegador de arquivos);
8. Plugue o ESP32 no computador;
9. Na barra inferior do VSC, clique em "COM 1" e escolha a porta em que o seu ESP32 está dentre as opções que aparecerão na parte de cima do VSC;
10. Clique em "esp32" e escolha a versão do seu ESP32 (neste caso "esp32s3") e depois nescolha a opção "ESP32-S3 via JTAG" (primeira opção). Este processo pode demorar alguns segundos até poucos minutos;
11. Abra um terminal ESP-IDF clicando no ícone de um quadrado com um símbolo de maior que (>) e underline na barra inferior do VSC;
12. No terminal que abrirá, digite "idf.py menuconfig";
13. Depois de alguns segundos abrirá um menu no terminal;
14. Navegue usando as teclas "j" e "k" e "enter" até o menu "Example Connection Configuration" e entre nele;
15. Dentro deste menu, aperte enter no primeiro item e insira o nome para o SSID do hotspot a ser conectado (por padrão o nome utilizado neste exemplo é "EYE-TRACKER");
16. Dentro deste menu, aperte enter no SEGUNDO item e insira a senha para do hotspot a ser conectado (por padrão a senha utilizada neste exemplo é "eyetracker25");
17. Aperte Esc para voltar para o menu principal;
18. Agora navegue até op menu "Camera Pin Configuration";
19. Em "Select Camera Pinout", selecione "ESP-S3-EYE DevKit";
20. Aperte Q para sair do menu de configuração e salvar as configurações;
21. A configuração deve estar pronta, agora basta clicar no botão com ícone de fogo na barra inferior e então será feito o *build*, *flash* e abrir o monitor.

PS.: um erro no momento do flash pode acontecer dependendo do modelo da placa de desenvolvimento. Caso isto ocorra, tente mudar o método de *flash* clicando ao lado do botão onde foi escolhido a porta USB, onde está escrito "UART" ou "JTAG". Neste caso, o UART costuma ser o mais garantido de funcionar.

Para fazer a transmissão da imagem da câmera como webcam USB, o processo é semelhante. Ao invés de abrir a pasta citada no passo 7, deverá ser aberta a pasta "esp-iot-solution/examples/usb/device/usb_webcam". Um ponto importante a se elencar é que, neste caso, como ele usará o USB-OTG para transmitir a imagem, caso o *dev board* não possua conexões USB separadas para OTG e UART, na próxima vez que for feito o *flash* no ESP usado, o botão de *boot* deste deve estar pressionado enquanto acontece a conexão ao computador pelo USB. De outra forma, o computador o identificará como uma interface de câmera e não como dispositivo programável. Além disso, neste caso, os passos do item seguinte não precisam ser seguidos, já que o ESP pode ser usado como uma webcam USB normal, basta plugá-lo no computador pelo USB e este deve identificá-lo para ser usado em qualquer aplicativo de câmera. Há também a opção de baixar o esp-who do git: https://github.com/espressif/esp-who.git. Neste caso, deverá ser aberto a pasta "esp-who/examples/human_face_detect" ou ""esp-who/examples/human-face-detection/web" dependendo da versão do esp-who. Nesta versão, o rosto do usuário aparecerá com um quadrado ao redor para identificá-lo, e portanto, a imagem não será limpa.

#### Conexão do ESP com o computador

Para conectar ao computador através de wi-fi pelo Windows 10:
1. Abra as configurações do Windows;
2. Vá para "Rede e Internet" e depois "Hotspot móvel";
3. Clique em "Editar" e insira o nome SSID e senha criadas nos passos anteriores (o SSID utilizado neste exemplo é "EYE-TRACKER" e a senha é "eyetracker25");
4. Clique em Salvar e depois ative o Hotspot móvel na caia de seleção na parte superior;
5. Conecte ambos os ESPs em um fonte de energia de 5V;
6. Se eles já tiverem programados seguindo os passos do item anterior, ambos devem se conectar automaticamente ao hotspot;
7. O endereço IP deles deve aparecer em uma tabela no menu do Hotspot móvel, anote-os para o próximo item.

Os passos para Windows 11 e Linux devem ser semelhantes. A conexão também pode ser feita conectando-se com a rede wi-fi criada pelos ESPs da mesma forma como se conecta a qualquer rede wi-fi, porém, para usar os dois ESPs ao mesmo tempo será necessário duas interfaces de rede no computador.

#### Aplicativo de captura das imagens

O aplicativo para captura das imagens é apenas um arquivo HTML simples com código Javascript embutido. Este usa um *canvas* com uma tag *img* que captura uma stream MMJPEG e a exibe. Utilizá-la é simples:
1. Para utilizá-lo, basta abrir o arquivo "captura.html";
2. Na página de navegador que abrirá, há dois campos de texto. Insira os endereços anotados anteriormente (os endereços de IP dos ESPs conectados ao computador);
3. O formato do endereço que deve ficar no campo deve ser: "http://192.167.137.xxx:80/stream";
4. Clique então em "Atualizar streams". As imagens das câmeras devem aparecer nos quadrados no meio da tela.

É possível gravar um vídeo com as imagens das câmeras clicando em "Iniciar gravação" e parar a gravação em "Parar gravação". As gravações devem aparecer nos players de vídeo abaixo. Elas também podem ser baixadas no formato WEBM nos links que aparecerão no final da página. Dependendo do navegador, o canvas pode não atualizar 

## Diagramas e dados dos componentes

A placa de desenvolvimento do ESP32-S3 usado neste projeto tem o seguinte *pinout*:

![Pinout do ESP32-S3](/esp32-s3_pinout.png)

O diagrama de pinos das câmeras OV2640 de 24 pinos pode variar de fabricante para fabricante, porém, usando o diagrama fornecido pela página da loja, temos:

![Pinout do OV2640 de 24 pinos](/ov2640_pinout.png)

Temos também algumas especificidades de operação das câmeras OV2640.

![Dados das câmeras OV2640](/ov2640_specs.png)

## Trabalhos Relacionados

Fazendo-se uma pesquisa em bases de dados, foram achados trabalhos relacionados a formas de se fazer traçamento do olhar com vestíveis, como o óculos desenvolvido no presente trabalho. Também encontrou-se trabalhos que indicam como o traçamento ocular pode ser usado para identificar diversas atipicidades da mente e da visão. Em nenhum destes trabalhos, porém, o aparato de *eye tracking* foi desenvolvido específicamente para este tipo de utilidade médica. Além disso, não foi encontrado trabalhos com a modelagem em 3D do modelo do óculos com suporte para os ESPs e câmeras. Outro ponto é o de que a maioria dos trabalhos focam mais no método de traçamento dos olhos, importando menos o *hardware* usado e como ele foi configurado, o que o presente trabalho toma como maior prioridade. 

## Resultados Esperados

Criar um dispositivo que seja capaz de captar imagens para fazer o traçamento ocular do usuário que possa ser usado para testar a presença de atipicidades visuais e neurológicas de bebês prematuros. Este aparato deverá facilitar e sistematizar o diagnóstico que já é realizado de forma manual e menos precisa. Este projeto também visa viabilizar o desenvolvimento de um modelo de Inteligência Artificial que indique de forma automatizada a presença de possíveis doenças ou anomalias do sistema visual e nervoso do paciente.
