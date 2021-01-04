<template>
  <div>
    <div class="py-4">
      <select @change="onChange($event)" v-model="selectedChart">
        <optgroup label = "Bar Chart">
          <option value="grouped" selected="selected">Grouped</option>
          <option value="stacked">Stacked</option>
        </optgroup>
      </select>
    </div>
    <div v-show="selectedChart === 'grouped'" id="grouped"></div>
    <div  v-show="selectedChart === 'stacked'" id="stacked"></div>
  </div>
</template>

<script>
import * as d3 from "d3";
export default {
  name: "HelloWorld",
  props: ['chartData'],
  data() {
    return {
      employees: this.chartData,
      isActive: true,
      selectedChart: 'grouped'
    };
  },
  mounted() {    
    this.createGroupedBarchart();
    this.createStackedBarchart();
  },
  methods: {
    createGroupedBarchart() {
      let container = d3.select("#grouped"),
        margin = { top: 20, right: 20, bottom: 30, left: 40 },
        width = 960 - margin.left - margin.right,
        height = 500 - margin.top - margin.bottom,
        barPadding = 0.2,
        axisTicks = { qty: 5, outerSize: 0, dateFormat: "%m-%d" };
      let svg = container
        .append("svg")
        .attr("width", width)
        .attr("height", height)
        .append("g")
        .attr("transform", `translate(${margin.left},${margin.top})`);

      let xScale0 = d3
        .scaleBand()
        .range([0, width - margin.left - margin.right])
        .padding(barPadding);
      let xScale1 = d3.scaleBand();
      let yScale = d3
        .scaleLinear()
        .range([height - margin.top - margin.bottom, 0]);

      let xAxis = d3.axisBottom(xScale0).tickSizeOuter(axisTicks.outerSize);
      let yAxis = d3
        .axisLeft(yScale)
        .ticks(axisTicks.qty)
        .tickSizeOuter(axisTicks.outerSize);

      xScale0.domain(this.employees.map((d) => d.month));
      xScale1
        .domain(["delivered", "undelivered"])
        .range([0, xScale0.bandwidth()]);
      yScale.domain([
        0,
        d3.max(this.employees, (d) =>
          d.delivered > d.undelivered ? d.delivered : d.undelivered
        ),
      ]);

      let month = svg
        .selectAll(".month")
        .data(this.employees)
        .enter()
        .append("g")
        .attr("class", "month")
        .attr("transform", (d) => `translate(${xScale0(d.month)},0)`);

      month
        .selectAll(".bar.delivered")
        .data((d) => [d])
        .enter()
        .append("rect")
        .attr("class", "bar delivered")
        .style("fill", "#98abc5")
        .attr("x", (d) => xScale1("delivered"))
        .attr("y", (d) => yScale(d.delivered))
        .attr("width", xScale1.bandwidth())
        .on("click", (d) => this.$router.push('about'))
        .attr("height", (d) => {
          return height - margin.top - margin.bottom - yScale(d.delivered);
        });

      month
        .selectAll(".bar.undelivered")
        .data((d) => [d])
        .enter()
        .append("rect")
        .attr("class", "bar undelivered")
        .style("fill", "#d0743c")
        .attr("x", (d) => xScale1("undelivered"))
        .attr("y", (d) => yScale(d.undelivered))
        .on("click", (d) => this.$router.push('about'))
        .attr("width", xScale1.bandwidth())
        .attr("height", (d) => {
          return height - margin.top - margin.bottom - yScale(d.undelivered);
        });
      svg
        .append("g")
        .attr("class", "x axis")
        .attr(
          "transform",
          `translate(0,${height - margin.top - margin.bottom})`
        )
        .call(xAxis);

      svg.append("g").attr("class", "y axis").call(yAxis);

      let color = d3.scaleOrdinal().range(["#98abc5", "#d0743c"]);
      var legend = svg.selectAll(".legend")
                .data(["delivered", "undelivered"].slice().reverse())
              .enter().append("g")
                .attr("class", "legend")
                .attr("transform", function (d, i) { return "translate(-100," + i * 20 + ")"; });
            legend.append("rect")
                .attr("x", width - 18)
                .attr("width", 18)
                .attr("height", 18)
                .style("fill", color);
            legend.append("text")
                .attr("x", width - 24)
                .attr("y", 9)
                .attr("dy", ".35em")
                .style("text-anchor", "end")
                .text(function (d) { return d; });
    },
    createStackedBarchart() {
      let container = d3.select("#stacked"),
        margin = { top: 20, right: 20, bottom: 30, left: 40 },
        width = 960 - margin.left - margin.right,
        height = 500 - margin.top - margin.bottom,
        barPadding = 0.2,
        axisTicks = { qty: 5, outerSize: 0, dateFormat: "%m-%d" };
      let svg = container
        .append("svg")
        .attr("width", width)
        .attr("height", height)
        .append("g")
        .attr("transform", `translate(${margin.left},${margin.top})`);

      let xScale0 = d3
        .scaleBand()
        .range([0, width - margin.left - margin.right])
        .padding(barPadding);
      let xScale1 = d3.scaleBand();
      let yScale = d3
        .scaleLinear()
        .range([height - margin.top - margin.bottom, 0]);

      let xAxis = d3.axisBottom(xScale0).tickSizeOuter(axisTicks.outerSize);
      let yAxis = d3
        .axisLeft(yScale)
        .ticks(axisTicks.qty)
        .tickSizeOuter(axisTicks.outerSize);

      xScale0.domain(this.employees.map((d) => d.month));
      xScale1
        .domain(["delivered", "undelivered"])
        .range([0, xScale0.bandwidth()]);
      yScale.domain([
        0,
        d3.max(this.employees, (d) =>
          d.delivered > d.undelivered ? d.delivered : d.undelivered
        ),
      ]);

      let month = svg
        .selectAll(".month")
        .data(this.employees)
        .enter()
        .append("g")
        .attr("class", "month")
        .attr("transform", (d) => `translate(${xScale0(d.month)},0)`);

      month
        .selectAll(".bar.delivered")
        .data((d) => [d])
        .enter()
        .append("rect")
        .attr("class", "bar delivered")
        .style("fill", "#98abc5")
        .attr("y", (d) => yScale(d.delivered))
        .attr("width", xScale1.bandwidth())
        .attr("height", (d) => {
          return height - margin.top - margin.bottom - yScale(d.delivered);
        });

      /* Add field2 bars */
      month
        .selectAll(".bar.undelivered")
        .data((d) => [d])
        .enter()
        .append("rect")
        .attr("class", "bar undelivered")
        .style("fill", "#d0743c")
        .attr("y", (d) => yScale(d.undelivered))
        .attr("width", xScale1.bandwidth())
        .attr("height", (d) => {
          return height - margin.top - margin.bottom - yScale(d.undelivered);
        });
      svg
        .append("g")
        .attr("class", "x axis")
        .attr(
          "transform",
          `translate(0,${height - margin.top - margin.bottom})`
        )
        .call(xAxis);

      svg.append("g").attr("class", "y axis").call(yAxis);

      let color = d3.scaleOrdinal().range(["#98abc5", "#d0743c"]);
      var legend = svg.selectAll(".legend")
                .data(["delivered", "undelivered"].slice().reverse())
              .enter().append("g")
                .attr("class", "legend")
                .attr("transform", function (d, i) { return "translate(-100," + i * 20 + ")"; });
            legend.append("rect")
                .attr("x", width - 18)
                .attr("width", 18)
                .attr("height", 18)
                .style("fill", color);
            legend.append("text")
                .attr("x", width - 24)
                .attr("y", 9)
                .attr("dy", ".35em")
                .style("text-anchor", "end")
                .text(function (d) { return d; });
    },
    onChange(event) {
      this.selectedChart = event.target.value;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

path {
  stroke: steelblue;
  stroke-width: 2;
  fill: none;
}
.axis path,
.axis line {
  fill: none;
  stroke: grey;
  stroke-width: 1;
  shape-rendering: crispEdges;
}
.legend {
  font-size: 16px;
  font-weight: bold;
  text-anchor: middle;
}
</style>
