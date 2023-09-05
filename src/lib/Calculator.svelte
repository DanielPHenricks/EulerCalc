<script>
  import Katex from "./katex.svelte";
  import { parse, compile, evaluate, round } from "mathjs";
  let isShown = false;
  let equation = "";
  let initialX, initialY;
  let stepSize, numSteps;
  let allValues = [];
  const calcValues = () => {
    const expression = parse(equation);
    const compiledExpression = expression.compile();
    allValues = [];
    let scope = {
      x: initialX,
      y: initialY,
    };
    for (let i = 0; i <= numSteps; i++) {
      let arr = {
        i: i,
        x: round(scope.x, 4),
        y: round(scope.y, 4),
      };
      let valAtPt = compiledExpression.evaluate(scope);
      scope.x += stepSize;
      scope.y += stepSize * valAtPt;
      allValues.push(arr);
    }
    isShown = true;
    console.log(allValues);
  };
</script>

<main>
  {#if !isShown}
    <div class="block">
      <label for="equation"> Equation</label><input
        type="text"
        bind:value={equation}
        id="equation"
      />
      <label for="xzero"><Katex math={"x_0"} /></label><input
        type="number"
        bind:value={initialX}
        id="xzero"
      />
      <label for="yzero"><Katex math={"y_0"} /></label><input
        type="number"
        bind:value={initialY}
        id="yzero"
      />
      <label for="stepSize">Step Size - <Katex math={"h"} /></label><input
        type="number"
        bind:value={stepSize}
        id="stepSize"
      />
      <label for="numSteps">Number of Steps - <Katex math={"n"} /></label><input
        type="number"
        bind:value={numSteps}
        id="numSteps"
      />
    </div>
    <button on:click={() => calcValues()}>Submit</button>
  {/if}
  {#if isShown}
    <p>
      Your equation was parsed as <Katex math={equation} />. If this is
      incorrect, click this button:
    </p>
    <button on:click={() => (isShown = !isShown)}>Click</button>
    <table id="rounded-corner">
      <thead>
        <tr>
          <th>Iteration</th>
          <th>X Value</th>
          <th>Y Value</th>
        </tr>
      </thead>

      <tbody>
        {#each allValues as row}
          <tr class="row">
            {#each Object.values(row) as cell}
              <td>{cell}</td>
            {/each}
          </tr>
        {/each}
      </tbody>
    </table>
  {/if}
</main>

<style type="text/css">
  table {
    width: 100%;
    border-collapse: collapse;
    border: 1px solid #ccc;
  }

  th,
  td {
    padding: 8px;
    border: 1px solid #ccc;
    text-align: left;
  }

  th {
    background-color: #303030;
  }
  label {
    display: block;
  }
</style>
