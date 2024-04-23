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
  callIA();
};

function randomNumber() {
  let random = Math.floor(Math.random() * 9);
  if (squares.value[random] === null) {
    squares.value[random] = 'O';
  } else {
    randomNumber();
  }
}

function restartGame() {
  squares.value = [null, null, null, null, null, null, null, null, null];
  algoritms.value = [];
}

const algoritms = ref([]);

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
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Headers': '*',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ status: stringGame }),
  })
    .then((response) => response.json())
    .then((data) => {
      console.log('Success:', data);
      algoritms.value = [];
      for (let key in data) {
        let obj = {
          key: key,
          value: data[key],
        };
        algoritms.value.push(obj);
      }

      console.log(algoritms.value);
    })
    .catch((error) => {
      console.error('Error:', error);
    });
}

const subtitles = ref([
  {
    title: 'X perde',
    number: 0,
  },
  {
    title: 'X vence',
    number: 1,
  },
  {
    title: 'Empate',
    number: 2,
  },
  {
    title: 'Tem jogo',
    number: 3,
  },
]);
</script>

<template>
  <div
    class="min-w-full w-screen h-screen flex flex-col items-center p-5 text-center overflow-x-hidden"
  >
    <h1 class="text-4xl my-5">T1 - Tic Tac Toe com ML</h1>
    <p>Alunos: Eduardo Garcia e Matheus Fernandes</p>
    <p>Professora: Silvia Moraes</p>
    <br />
    <div class="grid grid-cols-3 gap-2">
      <div
        v-for="(i, index) in 9"
        :key="index"
        class="w-20 h-20 bg-gray-200 flex justify-center items-center text-4xl cursor-pointer"
        @click="handleSquareClick(index)"
      >
        {{ squares[index] }}
      </div>
    </div>
    <div class="flex gap-4 flex-wrap">
      <table class="w-[200px] my-4">
        <tr class="text-justify">
          <td>Classe</td>
          <td>Número</td>
        </tr>
        <tr v-for="(subtitle, index) in subtitles" :key="index">
          <td class="text-justify py-3">{{ subtitle.title }}</td>
          <td class="text-justify py-3">{{ subtitle.number }}</td>
        </tr>
      </table>
      <table class="w-[200px] my-4">
        <tr class="text-justify">
          <td>Algoritmo</td>
          <td>Resultado</td>
        </tr>
        <tr v-for="(algorithm, index) in algoritms" :key="index">
          <td class="text-justify py-3">{{ algorithm.key.split('_')[1] }}</td>
          <td class="text-justify py-3">{{ algorithm.value[0] }}</td>
        </tr>
      </table>
    </div>
    <div class="flex gap-4">
      <button
        class="mt-5 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="restartGame"
      >
        Reiniciar
      </button>
    </div>
  </div>
</template>
