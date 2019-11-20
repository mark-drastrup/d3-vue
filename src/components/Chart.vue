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

        this.edges = svg
          .selectAll("line")
          .data(this.linksArr)
          .enter()
          .append("line")
            .attr("stroke", "black")
            .attr("stroke-width", 2)

        this.nodes = svg
          .selectAll("circle")
          .data(this.nodesArr)
          .enter()
          .append("circle")
            .attr("r", 20)
            .attr("fill", "red")
            .style("cursor", "pointer")
            .on("mouseover", function() {
              d3.select(this).transition()
                .duration(250)
                .attr("r", 25)
            })
            .on("mouseout", function() {
              d3.select(this).transition()
                .duration(250)
                .attr("r", 20)
            })
            .call(d3.drag()
              .on("start", this.dragStart)
              .on("drag", this.drag)
              .on("end", this.dragEnd)
            ) 
            
        this.edgepaths = svg.selectAll(".edgepath")
          .data(this.linksArr)
          .enter()
          .append('path')
          .attr("class","edgepath")
          .attr("fill-opacity", 0)
          .attr("stroke-opacity", 0)
          .attr("fill", "black")
          .attr("stroke", "black")
          .attr("id", (d, i) => `edgepath${i}`)
          .attr('marker-end','url(#arrowhead)')
            .style("stroke","#ccc")
            .style("pointer-events", "none");

        this.edgelabels = svg.selectAll(".edgelabel")
          .data(this.linksArr)
          .enter()
          .append('text')
          .style("pointer-events", "none")
          .attr("class", "edgelabel")
          .attr("id", (d, i) => `edgelabel${i}`)
          .attr("dy", -2)
          .attr("font-size", 12)
          .attr("fill", "black")

        this.edgelabels.append('textPath')
          .attr('xlink:href',function(d,i) {return '#edgepath'+i})
          .attr("startOffset", "50%")
          .style("text-anchor", "middle")
          .style("pointer-events", "none")
          .text(function(d,i){return 'label '+i});

        svg.append('defs').append('marker')
          .attr("id", "arrowhead")
          .attr("viewBox", "-0 -5 10 10")
          .attr("refX", 30)
          .attr("refY", 0)
          .attr("orient", "auto")
          .attr("markerWidth", 10)
          .attr("markerHeight", 10)
          .attr("xoverflow", "visible")
          .append('svg:path')
              .attr('d', 'M 0,-5 L 10 ,0 L 0,5')
              .attr('fill', "black")
              .attr('stroke',"black");

        this.labels = svg
          .selectAll(".nodelabel")
          .data(this.nodesArr)
          .enter()
          .append("text")
            .text(d => d.name)
            .attr("font-family", "sans-serif")
            .attr("font-size", "16px")
            .attr("fill", "black")
            .attr("text-anchor", "middle")
            .attr("dy", "-25")
            .style("cursor", "pointer")
            .style("font-weight", "bold")

        this.simulation = d3.forceSimulation(this.nodesArr)
          .force("link", d3.forceLink()
            .id(d => d.id)
            .links(this.linksArr).distance(80).strength(1)
          )
          .force("charge", d3.forceManyBody().strength(-2000))
          .force("center", d3.forceCenter(this.width / 2, this.height / 2))
          .on("tick", this.ticked)

    },
    methods: {  
      ticked() {
        this.edges
          .attr("x1", d => d.source.x)
          .attr("y1", d => d.source.y)
          .attr("x2", d => d.target.x)
          .attr("y2", d => d.target.y)

         this.nodes
          .attr("cx", d => d.x )
          .attr("cy", d => d.y )

        this.labels
          .attr("x", d => d.x) 
          .attr("y", d => d.y);

        this.edgepaths.attr("d", d => {
          const path = 'M '+d.source.x+' '+d.source.y+' L '+ d.target.x +' '+d.target.y;
          return path
        })

        
        /* this.edgelabels
          .attr('transform',function(d,i) {
            if (d.target.x<d.source.x) {
              bbox = this.getBBox();
              rx = bbox.x+bbox.width/2;
              ry = bbox.y+bbox.height/2;
              return 'rotate(180 '+rx+' '+ry+')';
            } else {
              return 'rotate(0)';
            }
          }) */
      },
      dragStart(d) {
        this.simulation.alphaTarget(0.5).restart()
        d.fx = d.x;
        d.fy = d.y;
      },
      drag(d) {
        d.fx = d3.event.x;
        d.fy = d3.event.y;
      },
      dragEnd(d) {
        this.simulation.alphaTarget(0)
        d.fx = null;
        d.fy = null
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
        nodesArr: [
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
        linksArr: [
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
        ],
        nodes:null,
        edges: null,
        labels: null,
        simulation: null,
        edgepaths: null,
        edgelabels: null
    })
}
</script>

<style scoped>
  g {
    width: 1000px;
    height: 1000px;
  }
</style>