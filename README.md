# Simulador de elevador

Trata-se de um simulador de elevador composto pela representão gráfica de um edifício com quatro andares e um poço no centro dentro do qual há retângulo correspondente ao elevador. Cada andar contém um botão que simula os botões encontrados no corredor de cada piso. Além disso, há um painel do lado direito com quatro botões representando uma versão aumentada do painel existente dentro do elevador. Os botões estão numerados do 1 ao 4 representando cada andar.

## Descrição do projeto

Para tirar o elevador do seu estágio inicial o usuário deve utilizar os botões em cor laranja presentes nos andares. Os botões do painel não funcionarão nesse momento.

Enquanto o elevador estiver em movimento, os botões dos andares podem ser acionados para serem atendidos posteriormente na ordem em que foram apertados. Os botões do painel também não funcionarão com o elevador em movimento.

Depois de completar um movimento, o elevador aguardará 5 segundos no andar. Nesse momento, um botão do painel poderá ser acionado e seu movimento começará imediatamente, interrompendo a contagem dos 5 segundos. Os botões dos andares também podem ser acionados durante a contagem dos 5 segundos, mas entrarão em uma fila. Se durante os 5 segundos nenhum botão do painel for acionado e a contagem chegar ao fim, a solicitação feita por um botão do andar que estiver em primeiro na fila será atendida. Se não houver nenhuma solicitação por um botão do andar na fila ao final da contagem, o elevador voltará para o primeiro andar.

Essas instruções também estão presentes na interface do simulador.

## Tecnologia utilizada

Framework Vue.js 3 (Options API).

## Configuração Recomendada da IDE

VSCode + Volar (e desative o Vetur)

## Personalização da Configuração
Consulte Referência de Configuração do Vite.

## Configuração do Projeto
```sh
npm install
```

### Compilação e Recarga Automática para Desenvolvimento
```sh
npm run dev
```

### Compilação e Minificação para Produção
```sh
npm run build
```
