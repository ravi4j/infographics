<template>
  <svg :width="width" :height="height" ref="svg" />
</template>

<script>
import * as d3 from "d3";
const xSelector = d => d.x;
const ySelector = d => d.y;
const colorValue = d => d.risk;
export default {
  name: "ScatterChart",
  props: ["payload"],
  data() {
    return {
      width: 960,
      height: 500,
      xLabel: "Risk Score",
      yLabel: "On Time Probability"
    };
  },
  mounted() {
    console.log("scatter chart..." + JSON.stringify(this.payload));
    const margin = { top: 30, left: 100, bottom: 100, right: 300 };
    const chartWidth = this.width - (margin.left + margin.right);
    const chartHeight = this.height - (margin.top + margin.bottom);
    const xLabel = this.xLabel;
    const yLabel = this.yLabel;
    const colorScale = d3
      .scaleOrdinal()
      .range(["#ff0000", "#00ff00", "#FFA500"]);
    const xScale = d3.scaleLinear().range([0, chartWidth]);
    const yScale = d3.scaleLinear().range([chartHeight, 0]);
    const xAxis = d3
      .axisBottom()
      .scale(xScale)
      .tickSizeInner(-chartHeight)
      .tickPadding(10);
    const yAxis = d3
      .axisLeft()
      .scale(yScale)
      .tickSizeInner(-chartWidth)
      .ticks(10)
      .tickPadding(10);
    const svg = d3.select("svg");
    const g = svg
      .append("g")
      .attr("transform", `translate(${margin.left}, ${margin.top})`);
    const xAxisG = g
      .append("g")
      .attr("class", "axis")
      .attr("transform", `translate(0, ${chartHeight})`);
    const yAxisG = g.append("g").attr("class", "axis");

    xAxisG
      .append("text")
      .attr("class", "axis__label")
      .attr("x", chartWidth / 2)
      .attr("y", 85)
      .text(xLabel);

    yAxisG
      .append("text")
      .attr("class", "axis__label")
      .attr("transform", "rotate(-90)")
      .attr("x", -chartHeight / 2)
      .attr("y", -45)
      .text(yLabel);

    this.render(this.payload, {
      svg:svg,  
      g: g,
      margin: margin,
      xScale: xScale,
      yScale: yScale,
      colorScale: colorScale,
      xAxis: xAxis,
      yAxis: yAxis,
      xAxisG: xAxisG,
      yAxisG: yAxisG
    });
  },
  methods: {
    render(data, props) {
      const svg = props.svg;
      const g = props.g;
      const margin = props.margin;
      const xScale = props.xScale;
      const yScale = props.yScale;
      const colorScale = props.colorScale;
      const xAxis = props.xAxis;
      const yAxis = props.yAxis;
      const xAxisG = props.xAxisG;
      const yAxisG = props.yAxisG;
      xScale.domain(d3.extent(data, xSelector)).nice();
      yScale.domain(d3.extent(data, ySelector)).nice();
      colorScale.domain(
        d3
          .set(data.map(colorValue))
          .values()
          .sort()
      );

      xAxisG.call(xAxis);
      yAxisG.call(yAxis);

      data.forEach((d, i) => {
        console.log("i is not used" + i);
        g.attr("transform", `translate(${margin.left}, ${margin.top})`)
          .append("circle")
          .attr("cx", xScale(xSelector(d)))
          .attr("cy", yScale(ySelector(d)))
          .attr("r", "5")
          .attr("stroke", "#fff")
          .attr("strokeWidth", 2)
          .attr("fill", colorScale(colorValue(d)));
      });
      svg.call(this.colorLegend, {
          colorScale: colorScale,
          positionX: 750,
          positionY: 200,
          tickRadius: 12,
          tickSpacing: 35,
          tickPadding: 6,
          label: "Risk Level",
          labelX: -20,
          labelY: -30
        });
    },
    colorLegend(selection, props){
        const colorScale = props.colorScale;
        const positionX = props.positionX;
        const positionY = props.positionY;
        const tickRadius = props.tickRadius;
        const tickSpacing = props.tickSpacing;
        const tickPadding = props.tickPadding;
        const label = props.label;
        const labelX = props.labelX;
        const labelY = props.labelY;
        
        let legendG = selection
          .selectAll(".legend--color")
          .data([null]);
        legendG = legendG
          .enter().append("g")
            .attr("class", "legend legend--color")
          .merge(legendG)
            .attr("transform", `translate(${positionX}, ${positionY})`);
        
        const legendLabel = legendG
          .selectAll(".legend__label")
          .data([null]);
        legendLabel
          .enter().append("text")
            .attr("class", "legend__label")
          .merge(legendLabel)
            .attr("x", labelX)
            .attr("y", labelY)
            .text(label);
        
        const ticks = legendG
          .selectAll(".tick")
          .data(colorScale.domain());
        const ticksEnter = ticks
          .enter().append("g")
            .attr("class", "tick");
        ticksEnter
          .merge(ticks)
            .attr("transform", (d, i) => `translate(0, ${i * tickSpacing})`);
        ticks.exit().remove();
        
        ticksEnter
          .append("circle")
          .merge(ticks.select("circle"))
            .attr("r", tickRadius)
            .attr("fill", colorScale);
        
        ticksEnter
          .append("text")
          .merge(ticks.select("text"))
            .attr("x", tickRadius + tickPadding)
            .text(d => d);
      }
  }
};
</script>


<style>
.axis .tick line {
  stroke-width: 2px;
  stroke: #dddddd;
}
.axis .tick text {
  fill: #8e8883;
}
.axis .domain {
  display: none;
}
.axis__label {
  text-anchor: middle;
  font-size: 25px;
  fill: #635f5d;
}
.legend .tick text {
  fill: #8e8883;
  font-family: sans-serif;
  alignment-baseline: middle;
}
.legend__label {
  font-size: 20px;
  fill: #635f5d;
  font-family: sans-serif;
}
</style>