<template>
  <div>
    <section class="hero is-primary">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            Investment Calculator
          </h1>
          <h2 class="subtitle">
            Measure investments efficiently
          </h2>
        </div>
      </div>
    </section>
    <div class="container">
      <div class="columns">
        <div class="column is-4">
          <div class="tile is-parent">
            <section class="tile is-child notification">
              <form class="content">
                <div class="field">
                  <label class="label">Invested Amount:</label>
                  <div class="control">
                    <input class="input" type="number" min="0" v-model="input.investedAmount" placeholder="Starting balance">
                  </div>
                </div>
                <div class="field">
                  <label class="label">Contribution per Year:</label>
                  <div class="control">
                    <input class="input" type="number" min="0" v-model="input.contribution" placeholder="Amount added every year">
                  </div>
                </div>
                <div class="field">
                  <label class="label">Interest % Rate:</label>
                  <div class="control">
                    <input class="input" type="number" min="0" v-model="input.interest" placeholder="Percentage per year">
                  </div>
                </div>
                <div class="field">
                  <label class="label">Number of Years:</label>
                  <div class="control">
                    <input class="input" type="number" min="0" v-model="input.years" placeholder="Investing period">
                  </div>
                </div>
                <div class="field">
                  <label class="label">Profit % Reinvested:</label>
                  <div class="control">
                    <input class="input" type="number" min="0" max="100" v-model="input.reinvestedAmount" placeholder="How much profit is reinvested">
                  </div>
                </div>
                <div class="field is-grouped">
                  <div class="control">
                    <button class="button is-primary" @click="calculate">Calculate</button>
                  </div>
                  <div class="control">
                    <button class="button is-text" @click="reset">reset</button>
                  </div>
                </div>
              </form>
            </section>
          </div>
        </div>
        <div class="column is-8">
          <div v-if="results.length">
            <section class="section">
              <div class="message is-info">
                <div class="message-body">
                  <strong>Period:</strong> <span v-text="input.years"></span> years
                  <strong>Total Investment:</strong> <span v-text="totalInvested.toFixed(2)"></span>
                  <strong>Total Withdrawn:</strong> <span v-text="totalWithdrawn.toFixed(2)"></span>
                  <strong>Total ROI:</strong> <span v-text="`${totalROI.toFixed(0)}%`"></span>
                </div>
              </div>
            <table class="table is-fullwidth is-hoverable">
              <thead>
                <tr>
                  <th width="15%"># Year</th>
                  <th width="15%">Balance</th>
                  <th width="20%">Withdrawn</th>
                  <th width="30%">Total Withdrawn</th>
                  <th width="20%">Total ROI</th>
                </tr>
              </thead>
            </table>
            <div style="overflow: auto; max-height: 250px;">
              <table class="table is-fullwidth is-hoverable">
                <tbody>
                  <tr v-for="result in results">
                    <td width="15%" v-text="result.year"></td>
                    <td width="15%" v-text="result.balance.toFixed(2)"></td>
                    <td width="20%" v-text="result.withdrawn.toFixed(2)"></td>
                    <td width="30%" v-text="result.totalWithdrawn.toFixed(2)"></td>
                    <td width="20%" v-text="`${result.totalROI.toFixed(2)}%`"></td>
                  </tr>
                </tbody>
              </table>
            </div>
            </section>
          </div>
          <div v-else>
            <section class="section">
              <div class="notification">
                <h1 class="title">How to use the calculator?</h1>
                <p>
                    The calculator allows you to calculate how much your investments are going to return over the years.
                    If you don't intend to withdrawn any money before the end period enter 100% the "Profit % Reinvested" field.
                </p>
              </div>
            </section>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data: function data() {
      return {
        totalInvested: 0,
        totalWithdrawn: 0,
        totalROI: 0,
        input: {
          investedAmount: null,
          contribution: null,
          interest: null,
          years: null,
          reinvestedAmount: null,
        },
        results: [],
      };
    },
    methods: {
      calculate() {
        this.results = [];
        this.totalInvested = parseFloat(this.input.investedAmount);
        this.totalWithdrawn = 0;
        this.totalROI = 0;
        let balance = parseFloat(this.input.investedAmount);
        for (let year = 1; year <= this.input.years; year += 1) {
          const interest = balance * (this.input.interest / 100);
          const withdrawn = interest - (interest * (this.input.reinvestedAmount / 100));
          this.totalWithdrawn += withdrawn;
          balance += interest - withdrawn;
          balance += parseFloat(this.input.contribution);
          this.totalROI = (this.totalInvested > 0) ?
            (this.totalWithdrawn / this.totalInvested) * 100 : 0;
          this.totalInvested += parseFloat(this.input.contribution);
          this.results.push({
            year,
            balance,
            withdrawn,
            totalWithdrawn: this.totalWithdrawn,
            totalROI: this.totalROI,
          });
        }
      },
      reset() {
        this.input = {
          investedAmount: null,
          contribution: null,
          interest: null,
          years: null,
          reinvestedAmount: null,
        };
        this.results = [];
      },
    },
  };
</script>
