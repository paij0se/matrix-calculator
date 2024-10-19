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

  // Función para calcular la transpuesta de una matriz
  function transposeMatrix(matrix: number[]): number[] {
    return [
      matrix[0],
      matrix[3],
      matrix[6],
      matrix[1],
      matrix[4],
      matrix[7],
      matrix[2],
      matrix[5],
      matrix[8],
    ];
  }

  // funcion para calcular la determinante de una matriz
  /**
   * Calcula la determinante de una matriz 3x3.
   * @param matrix - Un arreglo de 9 elementos representando una matriz 3x3.
   * @returns La determinante de la matriz.
   */
  function determinantMatrix(matrix: number[]): number {
    // Verifica que la matriz tenga exactamente 9 elementos (una matriz 3x3)
    if (matrix.length !== 9) {
      throw new Error("La matriz debe ser de 3x3 (9 elementos).");
    }

    // Calcula la determinante usando la fórmula estándar para matrices 3x3
    return (
      matrix[0] * matrix[4] * matrix[8] + // aei
      matrix[1] * matrix[5] * matrix[6] + // bfg
      matrix[2] * matrix[3] * matrix[7] - // cdh
      matrix[2] * matrix[4] * matrix[6] - // ceg
      matrix[1] * matrix[3] * matrix[8] - // bdi
      matrix[0] * matrix[5] * matrix[7] // afh
    );
  }
  // Función para calcular la inversa de una matriz
  function inverseMatrix(matrix: number[]): number[] {
    // Calcula la determinante de la matriz
    const det = determinantMatrix(matrix);

    // Verifica que la matriz sea invertible (su determinante no es 0)
    if (det === 0) {
      alert("La matriz no es invertible.");
      window.location.reload();
    }

    // Calcula la matriz adjunta
    const adj = [
      matrix[4] * matrix[8] - matrix[5] * matrix[7],
      matrix[2] * matrix[7] - matrix[1] * matrix[8],
      matrix[1] * matrix[5] - matrix[2] * matrix[4],
      matrix[5] * matrix[6] - matrix[3] * matrix[8],
      matrix[0] * matrix[8] - matrix[2] * matrix[6],
      matrix[2] * matrix[3] - matrix[0] * matrix[5],
      matrix[3] * matrix[7] - matrix[4] * matrix[6],
      matrix[1] * matrix[6] - matrix[0] * matrix[7],
      matrix[0] * matrix[4] - matrix[1] * matrix[3],
    ];

    // Calcula la matriz inversa dividiendo la adjunta por la determinante
    return adj.map((value) => value / det);
  }
  function gauss(matrix: number[]) {
    // Tomar los valores de la matriz extendida (3x4) con las incógnitas y resultados
    const matrixD = getMatrixValues([
      "1d",
      "2d",
      "3d",
      "4d",
      "5d",
      "6d",
      "7d",
      "8d",
      "9d",
      "10d",
      "11d",
      "12d",
    ]);

    // Realizar eliminación gaussiana
    for (let i = 0; i < 3; i++) {
      const pivot = matrixD[4 * i + i]; // Pivote diagonal

      // Verificar que el pivote no sea 0 para evitar división por cero
      if (pivot === 0) {
        alert(
          `Error: pivote en la fila ${i + 1} es cero. La matriz no se puede resolver con este método.`
        );
        return;
      }

      // Hacer 1 el pivote dividiendo la fila completa por el pivote
      for (let k = i; k < 4; k++) {
        matrixD[4 * i + k] /= pivot;
      }

      // Hacer 0 los elementos debajo del pivote
      for (let j = i + 1; j < 3; j++) {
        const factor = matrixD[4 * j + i];
        for (let k = i; k < 4; k++) {
          matrixD[4 * j + k] -= factor * matrixD[4 * i + k];
        }
      }
    }

    // Realizar sustitución hacia atrás para obtener las soluciones
    const result = [0, 0, 0];
    for (let i = 2; i >= 0; i--) {
      result[i] = matrixD[4 * i + 3]; // El valor constante en la ecuación
      for (let j = i + 1; j < 3; j++) {
        result[i] -= matrixD[4 * i + j] * result[j];
      }
      result[i] /= matrixD[4 * i + i];
    }

    // Mostrar el resultado en una alerta
    alert(
      `x = ${result[0].toFixed(2)}\ny = ${result[1].toFixed(2)}\nz = ${result[2].toFixed(2)}`
    );
  }

  // Función para mostrar el resultado
  function displayResult(result: number[]) {
    const resultDiv = document.getElementById("result");
    if (resultDiv) {
      resultDiv.innerHTML = `
        <h2>Resultado:</h2>
        <p>[${result[0]}] [${result[1]}] [${result[2]}]</p>
        <p>[${result[3]}] [${result[4]}] [${result[5]}]</p>
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
      case "trans":
        result = transposeMatrix(matrixA); // Transponer la matriz A
        break;
      case "deter":
        result = [determinantMatrix(matrixA)]; // Calcular la determinante de la matriz A
        break;
      case "inverse":
        result = inverseMatrix(matrixA); // Calcular la inversa de la matriz A
        break;
      case "gauss":
        gauss(matrixA); // Calcular la eliminación gaussiana de la matriz A
        break;
    }
    displayResult(result);
  }

  onMount(() => {
    const sumButton = document.getElementById("sum");
    const resButton = document.getElementById("res");
    const multButton = document.getElementById("mult");
    const transButton = document.getElementById("trans");
    const deterButton = document.getElementById("deter");
    const inverseButton = document.getElementById("inverse");
    const gaussButton = document.getElementById("gauss");

    if (sumButton) {
      sumButton.addEventListener("click", () => handleOperation("sum"));
    }
    if (resButton) {
      resButton.addEventListener("click", () => handleOperation("res"));
    }
    if (multButton) {
      multButton.addEventListener("click", () => handleOperation("mult"));
    }
    if (transButton) {
      transButton.addEventListener("click", () => handleOperation("trans"));
    }
    if (deterButton) {
      deterButton.addEventListener("click", () => handleOperation("deter"));
    }
    if (inverseButton) {
      inverseButton.addEventListener("click", () => handleOperation("inverse"));
    }
    if (gaussButton) {
      gaussButton.addEventListener("click", () => handleOperation("gauss"));
    }
  });
</script>

<div class="center">
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

  <br />
  <button id="sum">➕</button>
  <button id="res">➖</button>
  <button id="mult">✖️</button>
  <br />
  <button id="trans">🔄</button>
  <button id="deter">Det</button>
  <button id="inverse">Inv</button>
  <div id="result"></div>
  <h1>Eliminacion Gaussiana</h1>
  <h1>A</h1>
  <input type="number" id="1d" placeholder="x" />
  <input type="number" id="2d" placeholder="y" />
  <input type="number" id="3d" placeholder="z" />
  <span style="border-left: 1px solid black; height: 100px; margin: 10px;"
  ></span>
  <input type="number" id="4d" />
  <br />
  <input type="number" id="5d" placeholder="x" />
  <input type="number" id="6d" placeholder="y" />
  <input type="number" id="7d" placeholder="z" />
  <span style="border-left: 1px solid black; height: 100px; margin: 10px;"
  ></span>
  <input type="number" id="8d" />
  <br />
  <!-- pon una linea vertical -->
  <input type="number" id="9d" placeholder="x" />
  <input type="number" id="10d" placeholder="y" />
  <input type="number" id="11d" placeholder="z" />
  <span style="border-left: 1px solid black; height: 100px; margin: 10px;"
  ></span>
  <input type="number" id="12d" />
  <br />
  <button id="gauss">Gauss</button>
  <footer>
    <div class="center">
      <p>
        Made with ❤️ by
        <a href="https://github.com/paij0se" target="_blank">paij0se</a>
      </p>
    </div>
  </footer>
</div>

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
