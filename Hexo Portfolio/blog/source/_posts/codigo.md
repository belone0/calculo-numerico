---
title: App de interpolação
---

Nesta aula/atividade, utilizamos para desenvolver um app que calcula interpolação usando a formula de newton.

![Front do app](/images/front.png)

Nesse front, você escolhe o número de pontos, coloca o ponto a ser interpolado, e também os pontos conhecidos. Ao clicar interpolar, o javascript pega os pontos dos inputs, trata e aplica as funçoes de diferenças divididas e usa elas para obter o resultado da interpolação.

Esse é HTML básico abaixo.
```html
<!DOCTYPE html>
<html>
<head>
    <title>Interpolacao Newton</title>
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Interpolacao Newton</h2>
        <form id="interpolationForm" class="form-inline">
            <div class="form-group mb-2">
                <label for="num_values" class="mr-2">Number of Points:</label>
                <input type="number" class="form-control mr-3" id="num_values" name="num_values" required>
            </div>
            <div class="form-group mb-2">
                <label for="x_value" class="mr-2">X Value:</label>
                <input type="text" class="form-control mr-3" id="x_value" name="x_value" required>
            </div>
            <div id="pointsContainer" class="form-group mb-2"></div>
            <button type="submit" class="btn btn-primary mb-2">Interpolate</button>
        </form>
        <div id="result" class="alert alert-info mt-3" style="display:none;"></div>
    </div>
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.5.4/dist/umd/popper.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
```
Essa é a parte do JS que trata as informações e faz tudo aparecer na tela. Coloca todos os inputs em um array 
```js
        document.getElementById('num_values').addEventListener('change', function() {
            const numValues = parseInt(this.value);
            const pointsContainer = document.getElementById('pointsContainer');
            pointsContainer.innerHTML = '';

            for (let i = 1; i <= numValues; i++) {
                const inputGroup = document.createElement('div');
                inputGroup.className = 'input-group mb-2 mr-sm-2';

                const input = document.createElement('input');
                input.type = 'text';
                input.name = `xy_values_${i}`;
                input.placeholder = `Point ${i} (format: x,y)`;
                input.className = 'form-control';
                input.required = true;

                inputGroup.appendChild(input);
                pointsContainer.appendChild(inputGroup);
            }
        });

        document.getElementById('interpolationForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const numValues = parseInt(document.getElementById('num_values').value);
            const xValue = parseFloat(document.getElementById('x_value').value);
            const values = [];

            for (let i = 1; i <= numValues; i++) {
                const value = document.querySelector(`input[name="xy_values_${i}"]`).value;
                if (value) {
                    const [x, y] = value.split(',').map(parseFloat);
                    values.push([x, y]);
                }
            }

            const differences = dividedDifferences(values);
            const result = newtonInterpolation(xValue, values, differences);
            const resultDiv = document.getElementById('result');
            resultDiv.textContent = `Interpolated Value: ${result}`;
            resultDiv.style.display = 'block';
        });
```
Essa função recebe os pontos e organiza as diferenças dividas em uma tabela e retorna a tabela.
```js
        function dividedDifferences(points) {
            const n = points.length;
            const diffTable = Array.from({ length: n }, () => Array(n).fill(0));

            for (let i = 0; i < n; i++) {
                diffTable[i][0] = points[i][1];
            }

            for (let j = 1; j < n; j++) {
                for (let i = 0; i < n - j; i++) {
                    diffTable[i][j] = (diffTable[i + 1][j - 1] - diffTable[i][j - 1]) / (points[i + j][0] - points[i][0]);
                }
            }

            return diffTable[0].slice(0, n);
        }

```
Essa função recebe o X, os pontos e as diferenças divididas, organiza a equação e retorna o resultado da interpolação do ponto desejado.
```js
        function newtonInterpolation(x, points, coefficients) {
            const n = points.length;
            let result = coefficients[0];

            for (let i = 1; i < n; i++) {
                let term = coefficients[i];
                for (let j = 0; j < i; j++) {
                    term *= (x - points[j][0]);
                }
                result += term;
            }

            return result;
        }
```
E por fim, o resultado aparece na tela.

![Resultado](/images/result.png)