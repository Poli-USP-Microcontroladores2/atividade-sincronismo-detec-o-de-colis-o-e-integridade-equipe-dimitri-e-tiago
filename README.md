# PSI-Microcontroladores2-Aula12
Atividade: Sincronismo, Detecção de Colisão e Integridade

## Introdução

Na atividade anterior, vocês desenvolveram um código de comunicação serial que utiliza filas e interrupção para operar em dois modos: recepção por 5 segundos e transmissão por 5 segundos.

Nesta atividade, o objetivo é realizar em duplas a comunicação entre duas placas e refinar o protocolo de comunicação com sincronismo, detecção de colisão e verificação de integridade.

_Lembrete_: o código-base para a atividade anterior está disponível em: https://github.com/zephyrproject-rtos/zephyr/tree/main/samples/drivers/uart/echo_bot

## Etapa 1: Modelagem e Planejamento de Testes

Para o projeto, dedicamos duas placas, placa do Dimitri(placa A) e a placa do Tiago(Placa B), uma transmissora e outra receptora, enviando mensagerns a cada 5 segundos. A idéia é que o usuário digite uma mensagem, essa mensagem será enviada para Placa A, e depois enviada para Placa B. Após isso, os papéis se invertem e Placa B vira transmissora e envia a mensagem de volta para placa A, que é receptora. Quando a placa A receber a mensagem de volta, irá reescrever ela no terminal. Para casos onde não há comunicação com o computador, as placas informam seu estado por meio de leds, informando seus estados(transmissor e receptor) pelos LEDS vermelho e verde. Já para quando mensagem for recebida pela Placa A, um led azul é ativado.

### 1.1. Sincronismo por Botão

Nossa idéia inicial é de ambos os códigos serem extremamente parecidos, buscando modificar apenas o necessário. Para implementar o botão, fizemos com que a placa A se tornasse transmissor independente de seu estado e a placa B se tornasse receptor independente de seu estado atual. Para isso, ligamos um pino de cada placa na saída de um botão que, quando pressionado, envia ao mesmo tempo um sinal para ambas as placas.


_Elabore um diagrama de transição de estados inicial para modelar como as duas placas irão interagir com o sincronismo por botão, considerando os diversos estados possíveis e os eventos que determinam as transições de estados (vocês podem utilizar o D2 diagrams visto em atividade anterior: https://play.d2lang.com/)_. 

# Teste para verificação de funcionamento do sincronismo

Adicionar logs que informam o estado da placa(transmissor ou receptor)
Pressione o botão quando Placa A é transmissora. Quando for pressionado, Placa A não deve mudar seu estado e placa B deve ser receptora.

Agora pressione quando Placa A é receptora. Quando for pressionado, Placa A deve mudar seu estado e placa B deve ser receptora.

Tudo será informado por leds, sendo:

LED vermelho: Transmissora
LED verde: receptora

### 1.2. Detecção de Colisão

Percebe-se a necessidade de detectar e evitar colisões pois o sincronismo por botão não é perfeito, ou seja, com o passar do tempo as chances de gerar colisão aumenta gradativamente.

Reflita inicialmente se vocês consideram o sincronismo feito por botão algo perfeito, ou se ele pode falhar.
_Será que é necessário fazer um sincronismo periódico?_

Nos casos em que há problemas de sincronismo, podemos ter o cenário de colisão: quando as duas placas tentam transmitir ao mesmo tempo.
Para lidar com este problema, a proposta é elaborar uma detecção de colisão: logo antes de transmitir a mensagem completa, ou após transmitir cada caractere, podemos ouvir o canal (modo de recepção) para verificar se não há alguém já transmitindo, e não iniciar a transmissão caso o canal de comunicação esteja ocupado.

_Elabore um diagrama de transição de estados (versão 2) para modelar como as duas placas irão interagir com o sincronismo por botão e a detecção de colisão, considerando os diversos estados possíveis e os eventos que determinam as transições de estados (vocês podem utilizar o D2 diagrams visto em atividade anterior: https://play.d2lang.com/)_.

_Descreva um teste para verificação de correto funcionamento do sistema considerando este requisito de detecção de colisão, contemplando pré-condição, etapas do teste e pós-condição, de forma similar ao realizado em atividades anteriores (Dica: é possível mapear os estados mais relevantes a comportamentos do led da placa para observar o seu funcionamento?)_.

### 1.3. Verificação de Integridade

Reflita inicialmente o que ocorre com as mensagens transmitidas e recebidas em caso de colisão.

Nos casos em que há problemas de colisão, as mensagens podem não ser recebidas de forma completa.
Para lidar com este problema, a proposta é elaborar uma verificação de integridade: no início da mensagem, podemos enviar um hash da mensagem ou pelo menos o tamanho total da mansagem em caracteres, para que o receptor possa verificar se recebeu todos os caracteres de forma íntegra.
Questão para reflexão: _a verificação de integridade de conteúdo é suportada pela verificação de tamanho da mensagem recebida em caracteres?_

_Elabore um diagrama de transição de estados (versão 3) para modelar como as duas placas irão interagir com o sincronismo por botão, a detecção de colisão e a verificação de integridade, considerando os diversos estados possíveis e os eventos que determinam as transições de estados (vocês podem utilizar o D2 diagrams visto em atividade anterior: https://play.d2lang.com/)_.

_Descreva um teste para verificação de correto funcionamento do sistema considerando este requisito de verificação de integridade, contemplando pré-condição, etapas do teste e pós-condição, de forma similar ao realizado em atividades anteriores (Dica: podemos mapear a correta verificação de integridade a comportamentos da placa?)_.

## Etapa 2: Desenvolvimento Orientado a Testes

A partir da modelagem realizada e dos testes planejados, faça o desenvolvimento da solução para contemplar os 3 requisitos e passar nos 3 testes descritos.

O uso de IA Generativa é incentivado: _veja a diferença entre fazer prompts sem fornecer os requisitos e testes planejados, ou usar prompts com os diagramas e testes planejados_.

Além dos testes de cada requisito em cada etapa, faça **testes de regressão** também, para garantir que os requisitos das etapas anteriores estão funcionando (Dica: podemos ter modos de operação diferentes para testar diferentes features e não nos confundirmos com os comportamentos dos leds em cada situação).
Isto é: se o sincronismo continua funcionando após a integração da detecção de colisão, e se o sincronismo e a detecção continuam funcionando após a adição da verificação de integridade.

_Faça o upload de todos os códigos no repositório_ (pode ser em branches diferentes, ou até organizar em pull requests as diferentes features).

_Vocês devem adicionar todas as evidências de funcionamento (como por exemplo capturas de tela e fotos) dos testes realizados, mostrando todos os testes realizados no README.
As imagens e outras evidências de funcionamento devem estar descritas no README e devem estar em uma pasta chamada "results" no repositório._

### 2.1. Sincronismo por Botão

Insira aqui as descrições dos resultados e referencie as fotos e capturas de tela que mostram o funcionamento.

### 2.2. Detecção de Colisão

Insira aqui as descrições dos resultados e referencie as fotos e capturas de tela que mostram o funcionamento.

### 2.3. Verificação de Integridade

Insira aqui as descrições dos resultados e referencie as fotos e capturas de tela que mostram o funcionamento.
