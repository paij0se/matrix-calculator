<script lang="ts">
  import { onMount } from "svelte";
  import { browser } from "$app/environment";

  // Función para obtener los valores de los inputs
  function getMatrixValues(ids: string[]): number[] {
    return ids.map((id) => {
      const input = document.getElementById(id) as HTMLInputElement;
      return Number(input.value) || 0;
    });
  }

  // Función para sumar matrices
  function sumMatrices(matrixA: number[], matrixB: number[]): number[] {
    return matrixA.map((value, index) => value + matrixB[index]);
  }

  // Función para restar matrices
  function subtractMatrices(matrixA: number[], matrixB: number[]): number[] {
    return matrixA.map((value, index) => value - matrixB[index]);
  }

  // Función para multiplicar matrices
  function multiplyMatrices(matrixA: number[], matrixB: number[]): number[] {
    return [
      matrixA[0] * matrixB[0] +
        matrixA[1] * matrixB[3] +
        matrixA[2] * matrixB[6],
      matrixA[0] * matrixB[1] +
        matrixA[1] * matrixB[4] +
        matrixA[2] * matrixB[7],
      matrixA[0] * matrixB[2] +
        matrixA[1] * matrixB[5] +
        matrixA[2] * matrixB[8],

      matrixA[3] * matrixB[0] +
        matrixA[4] * matrixB[3] +
        matrixA[5] * matrixB[6],
      matrixA[3] * matrixB[1] +
        matrixA[4] * matrixB[4] +
        matrixA[5] * matrixB[7],
      matrixA[3] * matrixB[2] +
        matrixA[4] * matrixB[5] +
        matrixA[5] * matrixB[8],

      matrixA[6] * matrixB[0] +
        matrixA[7] * matrixB[3] +
        matrixA[8] * matrixB[6],
      matrixA[6] * matrixB[1] +
        matrixA[7] * matrixB[4] +
        matrixA[8] * matrixB[7],
      matrixA[6] * matrixB[2] +
        matrixA[7] * matrixB[5] +
        matrixA[8] * matrixB[8],
    ];
  }

  // Función para mostrar el resultado
  function displayResult(result: number[]) {
    const resultDiv = document.getElementById("result");
    if (resultDiv) {
      resultDiv.innerHTML = `
        <h2>Resultado:</h2>
        <p>[${result[0]}] [${result[1]}] [${result[2]}]</p>
        <p>[${result[3]}] [${result[4]}] [${result[5]}]</p
        <p>[${result[6]}] [${result[7]}] [${result[8]}]</p>
      `;
    }
  }

  // Función para manejar las operaciones
  function handleOperation(operation: string) {
    if (!browser) return;

    const matrixA = getMatrixValues([
      "1",
      "2",
      "3",
      "4",
      "5",
      "6",
      "7",
      "8",
      "9",
    ]);
    const matrixB = getMatrixValues([
      "10",
      "11",
      "12",
      "13",
      "14",
      "15",
      "16",
      "17",
      "18",
    ]);
    let result: number[] = [];

    switch (operation) {
      case "sum":
        result = sumMatrices(matrixA, matrixB);
        break;
      case "res":
        result = subtractMatrices(matrixA, matrixB);
        break;
      case "mult":
        result = multiplyMatrices(matrixA, matrixB);
        break;
    }

    displayResult(result);
  }

  onMount(() => {
    const sumButton = document.getElementById("sum");
    const resButton = document.getElementById("res");
    const multButton = document.getElementById("mult");

    if (sumButton) {
      sumButton.addEventListener("click", () => handleOperation("sum"));
    }
    if (resButton) {
      resButton.addEventListener("click", () => handleOperation("res"));
    }
    if (multButton) {
      multButton.addEventListener("click", () => handleOperation("mult"));
    }
  });
</script>

<div>
  <h1>Calculadora de Matrices</h1>
  <h1>A</h1>
  <input type="number" id="1" />
  <input type="number" id="2" />
  <input type="number" id="3" />
  <br />
  <input type="number" id="4" />
  <input type="number" id="5" />
  <input type="number" id="6" />
  <br />
  <input type="number" id="7" />
  <input type="number" id="8" />
  <input type="number" id="9" />
  <h1>B</h1>
  <input type="number" id="10" />
  <input type="number" id="11" />
  <input type="number" id="12" />
  <br />
  <input type="number" id="13" />
  <input type="number" id="14" />
  <input type="number" id="15" />
  <br />
  <input type="number" id="16" />
  <input type="number" id="17" />
  <input type="number" id="18" />
</div>
<br />
<button id="sum">➕</button>
<button id="res">➖</button>
<button id="mult">✖️</button>
<div id="result"></div>
<footer>
  <div class="center">
    <p>
      Made with ❤️ by
      <a href="https://github.com/paij0se" target="_blank">paij0se</a>
    </p>
  </div>
</footer>

<style>
  input {
    align-items: center;
    width: 50px;
    height: 50px;
    text-align: center;
    margin: 2px;
  }
  input::-webkit-outer-spin-button,
  input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }
  button {
    width: 50px;
    height: 50px;
    margin: 10px;
  }

</style>
