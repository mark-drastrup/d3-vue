<template>
    <div>
        <h2>Graph</h2>
        <div id="network">
        </div>
    </div>
</template>

<script>
import * as d3 from 'd3'

export default {
    mounted () {
        const svg = d3.select("#network")
          .append("svg")
            .attr("height", this.height)
            .attr("width", this.width)
          .append("g")
            .attr("transform",
              `translate(${40}, ${10})`)

        const edges = svg
          .selectAll("line")
          .data(this.links)
          .enter()
          .append("line")
            .attr("stroke", "black")
            .attr("stroke-width", 2)

        const nodes = svg
          .selectAll("circle")
          .data(this.nodes)
          .enter()
          .append("circle")
            .attr("r", 20)
            .attr("fill", "red")
            
        const edgepaths = svg.selectAll(".edgepath")
          .data(this.links)
          .enter()
          .append('path')
          .attr("class","edgepath")
          .attr("fill-opacity", 0)
          .attr("stroke-opacity", 0)
          .attr("fill", "black")
          .attr("stroke", "black")
          .attr("id", (d, i) => `edgepath${i}`)

        const simulation = d3.forceSimulation(this.nodes)
          .force("link", d3.forceLink()
            .id(d => d.id)
            .links(this.links).distance(80).strength(1)
          )
          .force("charge", d3.forceManyBody().strength(-2000))
          .force("center", d3.forceCenter(this.width / 2, this.height / 2))
          .on("tick", () => this.ticked(edges, nodes))

    },
    methods: {  
      ticked(edges, nodes) {
        /* console.log('called') */
        edges
          .attr("x1", d => d.source.x)
          .attr("y1", d => d.source.y)
          .attr("x2", d => d.target.x)
          .attr("y2", d => d.target.y)

         nodes
        .attr("cx", d => d.x )
        .attr("cy", d => d.y )
      }
    },
    data: () => ({
        height: 800,
        width: 800,
        margin: {
            top: 10,
            bottom: 30,
            left: 40,
            right: 40
        },
        nodes: [
            { id: 1, name: "A", type: "primary" },
            { id: 2, name: "B", type: "primary" },
            { id: 3, name: "C", type: "danger" },
            { id: 4, name: "D", type: "secondary" },
            { id: 5, name: "E", type: "primary" },
            { id: 6, name: "F", type: "primary" },
            { id: 7, name: "G", type: "danger" },
            { id: 8, name: "H", type: "secondary" },
            { id: 9, name: "I", type: "secondary" },
            { id: 10, name: "J", type: "primary" }
        ],
        links: [
            { source: 1, target: 2, value: 12 },
            { source: 1, target: 5, value: 5 },
            { source: 1, target: 6, value: 13 },
            { source: 2, target: 3, value: 2 },
            { source: 2, target: 7, value: 18 },
            { source: 3, target: 4, value: 17 },
            { source: 8, target: 3, value: 9 },
            { source: 4, target: 5, value: 3 },
            { source: 4, target: 9, value: 3 },
            { source: 5, target: 10, value: 7 }
        ]
    })
}
</script>

<style scoped>
  g {
    width: 1000px;
    height: 1000px;
  }
</style>