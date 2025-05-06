# Óculos para *Eye tracking*

## Introdução

Este projeto foi desenvolvido dentro do Laboratório de Sinais, IoT e Imagens Médicas para uso como Trabalho de Conclusão de Curso e utilizado e desenvolvido parcialmente na disciplina de Projeto de Sistemas Ubíquos e Embarcados. O objetivo do projeto é produzir um protótipo para uso de médicos e profissionais de fisioterapia para diagnóstico de doenças intelectuais ou visuais em crianças recém-nascidas, principalmente prematuras através de traçamento ocular (*Eye tracking*).

## Descrição do objeto de desenvolvimento

Óculos feito a partir de modelagem e impressão 3D com câmeras acopladas para filmagem dos olhos e do ponto de vista do usuário para se ter capacidade de traçamento ocular. A câmera dos olhos terá câmeras que captam frequências próximas ao infravermelho (NIR) e a câmera do ponto de vista será uma câmera de ângulo de abertura alto. Estas câmeras, ligadas a um microcontralador ESP32 enviam as imagens em tempo real para um computador por conexão cabeada USB para gravação do procedimento de exame do paciente. Um software desenvolvido no LSIIM então obtém estas gravações e faz o mapeamento da direção do olhar do paciente e de para onde ele está olhando.

## Fluxograma de dados do sistema

![Fluxograma do sistema](/fluxograma_ubiquos.png)

## Materiais e Métodos

Nesta seção será descrito os itens usados para o desenvolvimento do projeto, assim como o processo de configuração destes.

### Itens usados

- **(2x) ESP32-S3**: este microcontrolador foi usado devido a sua versatilidade e facilidade de uso com câmeras e da comunicaçao com o computador externo através de USB ou wi-fi. Poderia ter sido usado também o modelo S2 já que ambos possuem interface USB-OTG, importante para transmissão da imagem da câmera por USB, porém, não foi encontrado nenhuma *dev board* que possuísse entrada para câmeras OV2640, OV5640 ou similares.
- **Câmera OV2640 850nm**: câmera compatível com o ESP32 que capta ondas de luz de 850nm de comprimento, ou seja, próximas ao infravermelho. Esta câmera faz com que as pupilas fiquem consideravelmente mais contrastadas na imagem, o que facilita drasticamente o *eye tracking*.
- **Câmera OV2640 200°**: esta câmera fica apontada para frente em relação ao rosto do usuário e serve para obter imagens dos estímulos visuais usados pelos examinadores. Necessita de grande ângulo de visão (pelo menos 180°) para se ter certeza de que todo o campo de visão (e, possivelmente, até além) da pessoa seja capturado.
- **Cabos USB (Tipo C ou micro, a depender do modelo da placa de desenvolvimento do ESP32)**: cabos para transmitir dados e/ou alimentar eletricamente os ESPs.
- **Computador**: qualquer máquina capaz de interpretar programas em python, rodar navegadores e ter pelo menos 2 portas USB.

Também é possível fazer a transmissão das imagens por wi-fi. Neste caso é necessário que o computador seja capaz de se conectar com redes wi-fi e será necessário utilizar programas que captam streams MJPEG e a transforme em uma câmera virtual, como por exemplo o OBS. Neste caso, terá também de ser incluído uma fonte de energia para os ESPs, que pode ser o próprio computador ou uma bateria. Por enquanto, não foi testado a possibilidade do uso das duas câmeras com wi-fi.

### Procedimentos

#### Utilização do ESP32 como webcam

Para utilizar o ESP32 como webcam USB os seguintes passos:
1. No Visual Studio Code, procure pela extensão ESP-IDF e a instale;
2. Baixe o esp-iot-solution no git: https://github.com/espressif/esp-iot-solution.git ;
3. Abra a pasta baixada no VSC;
4. Uma página de "Welcome" do ESP-IDF deve abrir. Nela, clique em "Configuring the ESP-IDF Extension" e depois em "Configure ESP-IDF Extension";
5. Na página de Setup, escolha a opção EXPRESS;
6. Selecione a versão do ESP-IDF. Neste caso, pode-se baixar uma versão direto do git do ESP-IDF e usar a opção "Find ESP-IDF in your system", ou escolher uma versão para download. No momento da escrita, a última versão é a 5.4.1 e será a usada para este exemplo;
7. Depois que o download e instalação concluir, abra a pasta "esp-iot-solution/examples/usb/device/usb-webcam" (pelo menu "File" e clicando em "Open folder", não pelo navegador de arquivos);
8. Plugue o ESP32 no computador;
9. Na barra inferior do VSC, clique em "COM 1" e escolha a porta em que o seu ESP32 está dentre as opções que aparecerão na parte de cima do VSC;
10. Clique em "esp32" e escolha a versão do seu ESP32 (neste caso "esp32s3") e depois nescolha a opção "ESP32-S3 via JTAG" (primeira opção). Este processo pode demorar alguns segundos até poucos minutos;
11. A configuração deve estar pronta, agora basta clicar no botão com ícone de fogo na barra inferior e então será feito o *build*, *flash* e abrir o monitor.

Para fazer a transmissão da imagem da câmera por wi-fi, o processo é semelhante. Ao invés de abrir a pasta citada no paso 7, deverá ser aberta a pasta "esp-iot-solution/examples/camera/video-stream-server". Há também a opção de baixar o esp-who do git: https://github.com/espressif/esp-who.git. Neste caso, deverá ser aberto a pasta "esp-who/examples/human_face_detect" ou ""esp-who/examples/human-face-detection/web" dependendo da versão do esp-who. Nesta versão, o rosto do usuário aparecerá com um quadrado ao redor para identificá-lo, e portanto, a imagem não será limpa.

#### Programa de Captura Web

Para instalar o Programa de Captura Web, siga os passos do README no repositório: https://github.com/LSIIM/Programa-de-Captura-Web . Também deverá ser instalado a API de backend para a aplicação, disponível em: https://github.com/LSIIM/LSIIM-web-api . Em ambos os casos, recomenda-se o uso de containeres através do Docker. O acesso ao banco de dados pode não ser possível.

## Diagramas e dados dos componentes

A placa de desenvolvimento do ESP32-S3 usado neste projeto tem o seguinte *pinout*:

![Pinout do ESP32-S3](/esp32-s3_pinout.png)

O diagrama de pinos das câmeras OV2640 de 24 pinos pode variar de fabricante para fabricante, porém, usando o diagrama fornecido pela página da loja, temos:

![Pinout do OV2640 de 24 pinos](/ov2640_pinout.png)

Temos também algumas especificidades de operação das câmeras OV2640.

![Dados das câmeras OV2640](/ov2640_specs.png)
