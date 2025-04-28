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
