<template>
  <div id="instrucoes">
    <p>
      <span id="titulo-instrucoes">INSTRUÇÕES:</span>
      <br><br>
      Para tirar o elevador do seu estágio inicial utilize os botões em cor laranja presentes nos andares.
      Os botões do painel não funcionarão nesse momento.
      <br><br>
      Enquanto o elevador estiver em movimento, os botões dos andares podem ser acionados para serem atendidos
      posteriormente na ordem em que foram apertados. Os botões do painel também não funcionarão com o elevador em
      movimento.
      <br><br>
      Depois de completar um movimento, o elevador aguardará 5 segundos no andar. Nesse momento, um botão do painel
      poderá ser acionado e seu movimento começará imediatamente, interrompendo a contagem dos 5 segundos.
      Os botões dos andares também podem ser acionados durante a contagem dos 5 segundos, mas entrarão em uma fila.
      Se durante os 5 segundos nenhum botão do painel for acionado e a contagem chegar ao fim, a solicitação feita
      por um botão do andar que estiver em primeiro na fila será atendida. Se não houver nenhuma solicitação por um
      botão do andar na fila ao final da contagem, o elevador voltará para o primeiro andar.
    </p>
  </div>
</template>
<style>
#instrucoes {
  height: fit-content;
  width: 380px;
  margin-right: 80px;
  padding: 10px;
  border: 4px double black;
  font-size: 12px;
}

p {
  text-align: center;
}

#titulo-instrucoes {
  font-weight: bold;
}
</style>