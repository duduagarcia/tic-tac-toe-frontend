<script setup>
const squares = ref([null, null, null, null, null, null, null, null, null]);

const handleSquareClick = (i) => {
  if (squares.value[i] !== null) {
    return;
  }

  squares.value[i] = 'X';

  if (squares.value.every((square) => square !== null)) {
    return;
  }

  randomNumber();
};

function randomNumber() {
  let random = Math.floor(Math.random() * 9);
  if (squares.value[random] === null) {
    squares.value[random] = 'O';
  } else {
    randomNumber();
  }
  callIA();
}

function restartGame() {
  squares.value = [null, null, null, null, null, null, null, null, null];
}

function callIA() {
  const squaresCopy = squares.value.slice();
  squaresCopy.map((square, index) => {
    if (square === 'X') {
      squaresCopy[index] = 1;
    } else if (square === 'O') {
      squaresCopy[index] = 2;
    } else {
      squaresCopy[index] = 0;
    }
  });

  const url = 'http://127.0.0.1:8000/check';
  const stringGame = squaresCopy.join(', ');

  fetch(url, {
    method: 'POST',
    headers: {
      'Access-Control-Allow-Origin': "*",
      'Access-Control-Allow-Headers': "*",
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ status: stringGame }),
  })
    .then((response) => response.json())
    .then((data) => {
      console.log('Success:', data);
    })
    .catch((error) => {
      console.error('Error:', error);
    });
}
</script>

<template>
  <div
    class="min-w-full w-screen h-screen flex flex-col items-center p-5 text-center"
  >
    <h1 class="text-4xl my-5">T1 - Tic Tac Toe com ML</h1>
    <p>Alunos: Eduardo Garcia e Matheus Fernandes</p>
    <p>Professora: Silvia Moraes</p>
    <br />
    <div class="grid grid-cols-3 gap-2">
      <div
        v-for="(i, index) in 9"
        :key="index"
        class="w-20 h-20 sm:w-28 sm:h-28 bg-gray-200 flex justify-center items-center text-4xl cursor-pointer"
        @click="handleSquareClick(index)"
      >
        {{ squares[index] }}
      </div>
    </div>
    <div class="flex gap-4">
      <button
        class="mt-5 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="restartGame"
      >
        Reiniciar
      </button>
      <button
        class="mt-5 bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
        @click="callIA"
      >
        Verificar posição
      </button>
    </div>
  </div>
</template>
