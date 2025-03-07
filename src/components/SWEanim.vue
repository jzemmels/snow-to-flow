<template>
  <!---VizSection-->
  <VizSection
    id="firstSection"
    :take-away="true"
  >
    <!-- TAKEAWAY TITLE -->
    <template #takeAway>
      <h2>Changes in snowmelt have downstream consequences</h2>
    </template>
    <!-- EXPLANATION -->
    <template #aboveExplanation>
      <p />
      <p>
        Seasonal snowpack varies widely from place to place, and from year to year<sup>14</sup>, and this variability can have a strong influence on the timing and magnitude of snowmelt, delivery to a watershed, and subsequent streamflow response. At the sites shown below in the Upper Colorado River Basin, there was a two-fold difference in the magnitude of SWE between 2011 and 2012, illustrating how changes in snowmelt can shape the timing and magnitude of streamflow, and subsequently, water availability. Use the buttons below to explore how differences in snow between two years affect streamflow dynamics measured by USGS streamgages.
      </p>
    </template>
    <!-- FIGURES -->
    <template #figures>
      <div
        id="figs"
        class="single one"
      >
        <div class="compare">
          <div
            class="btn-group"
            data-toggle="buttons"
          >
            <h4 class="butt-head">
              Show: 
            </h4> 
            <div class="inputsContainer">
              <div class="inputs">
                <input
                  id="cb1"
                  class="butt"
                  type="checkbox"
                  name="checkboxgroup2"
                  checked="true"
                  value="swe"
                  @change="showSWE"
                >
                <label
                  class="butt"
                  for="cb1"
                >SWE (in)</label>
                <input
                  id="cb2"
                  class="butt"
                  type="checkbox"
                  name="checkboxgroup1"
                  checked="true"
                  value="flow"
                  @change="showFlow"
                >
                <label
                  class="butt"
                  for="cb2"
                >Streamflow (<sup>mm</sup>&frasl;<sub>day</sub>)</label>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div
        id="figs"
        class="single one"
      >
        <div id="mmd-container-both">
          <svg
            id="mmd-line-both"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            viewBox="-50 -50 600 500"
            aria-labelledby="page-title page-desc"
            width="100%"
            height="100%"
          >

            <text
              id="melt11"
              class="yr-label"
              x="200"
              y="20"
            >later and <tspan
              x="200"
              y="40px"
            >faster melt</tspan></text>
            <text
              id="melt12"
              class="yr-label"
              x="300"
              y="290"
            >earlier and <tspan
              x="300"
              y="310px"
            >longer melt</tspan></text>

            <text
              id="peak11"
              class="yr-label"
              x="80"
              y="230"
            >high snow</text>
            <text
              id="peak12"
              class="yr-label"
              x="350"
              y="320"
            >low snow</text>
            
            <text
              id="el11"
              class="yr-label"
              x="20"
              y="20"
            >high elevation</text>
            <text
              id="el12"
              class="yr-label"
              x="-20"
              y="390"
            >low</text>
          </svg>
        </div>
        <div class="compare">
          <div
            class="btn-group"
            data-toggle="buttons"
          >
            <h4 class="butt-head">
              Compare: 
            </h4> 
            <div class="inputsContainer">
              <div class="inputs">
                <input
                  id="rb1"
                  class="butt"
                  type="radio"
                  name="radiogroup1"
                  checked="true"
                  value="time"
                  @change="changePos"
                >
                <label
                  class="butt"
                  for="rb1"
                >timing</label>
                <input
                  id="rb2"
                  class="butt"
                  type="radio"
                  name="radiogroup1"
                  value="peak"
                  @change="changePos"
                >
                <label
                  class="butt"
                  for="rb2"
                >magnitude</label>
                <input
                  id="rb3"
                  class="butt"
                  type="radio"
                  name="radiogroup1"
                  value="el"
                  @change="changePos"
                >
                <label
                  class="butt"
                  for="rb3"
                >by elevation</label>
              </div>
            </div>
          </div>
        </div>
      </div>
    </template>
    <!-- FIGURE CAPTION -->
    <template #figureCaption>
      <p>
        Use the buttons to reorganize the chart and compare SWE and streamflow across a selection of USGS streamgages.
      </p>
    </template>
    <template #belowExplanation>
      <p>
        Elevation is intertwined with numerous factors like snow persistence, wind redistribution, and slope that drive complex snow-to-flow dynamics from site to site. At higher elevations, fallen snow can be blown over ridges, scouring windward rises or trees, leading to snow accumulating on the leeward side of the ridge. This results in spatial variation in snowpack depth and snowmelt timing.
      </p>
      <!-- <Sidebar>
        <template v-slot:sidebarTitle>
          What is a "water year"?
        </template>
        <template v-slot:sidebarMessage>
          <p>Instead starting the year at Janauary 1, USGS hydrologists mark the start of the <span class="emph">water year</span> on October 1, three months early.  The USGS has been using water years since 1911 to mark the start of hydrologic activity, and use it here because it captures a full snow cycle.</p>
        </template>
      </Sidebar> -->
    </template>
  </VizSection>
</template>
<script>
import VizSection from '@/components/VizSection';
import * as d3Base from "d3";
// import Sidebar from '@/components/Sidebar';
export default {
    name: "SWEanim",
    components:{
        VizSection,
        // Sidebar
    },
    data() {
            return {
              isActive: false,
              flowActive: true,
              sweActive: true,
              title: process.env.VUE_APP_TITLE,
              publicPath: process.env.BASE_URL,
              d3: null,
              mmd_2011: null,
              mmd_2012: null,
              svg: null,
              width: 600,
              height: 400,
              margin: 25,

              line_swe: null,
              area_swe: null,
              line_mmd: null,
              area_mmd: null,
              group: null,
              ridge_o: 0.3,
              site_elev: [],
              site_sp: [],
              tickDates: ["Oct", "Jan", "Apr", "July"],
              highlabel: null,
              lowlabel: null,
              xAxis: null,
              yAxis_mmd: null,
              yAxis_swe: null,
              x11: null,
              x12: null,
              y11_swe: null,
              y12_swe: null,
              y11_mmd: null,
              y12_mmd: null,
              ridge11:null,
              ridge12: null,

              color_mmd: "dodgerblue",
              color_swe: "grey",

            }
        },
    mounted() {
          const self = this;
          this.d3 = Object.assign(d3Base);

          this.loadData();

        },
    methods: {
      loadData() {
        const self = this;
        // read in data to draw hydrographs - eventually want to animate with d3 over gif
        let promises = [self.d3.csv(self.publicPath + "data/gage_sp.csv",  this.d3.autotype),
        self.d3.csv(self.publicPath + "data/mmd_df_2011.csv",  this.d3.autotype),
        self.d3.csv(self.publicPath + "data/mmd_df_2012.csv",  this.d3.autotype),
        self.d3.csv(self.publicPath + "data/swe_df_2011.csv",  this.d3.autotype),
        self.d3.csv(self.publicPath + "data/swe_df_2012.csv",  this.d3.autotype)];

        Promise.all(promises).then(self.callback); 
      },
      callback(data) {
        const  self  = this;

        // same data served up 2 ways
        var gage_sp = data[0]; // snow persistence for gages
        var data_2011 = data[1]; // mmd
        var data_2012 = data[2]; // mmd
        var swe_2011 = data[3]; // swe
        var swe_2012 = data[4]; // swe

        // prep data for plotting
        var sites = data_2011.columns // array of all site_no - each gets a ridge
        sites.shift(); // drop first column
        var n = sites.length;

        // sort gages by elevation
        this.site_elev = gage_sp.slice().sort((a,b) => this.d3.ascending(a.elev, b.elev)).map(d => d.site_no)

        //sort ages by snow persistence
        this.site_sp = gage_sp.slice().sort((a,b) => this.d3.ascending(a.sp, b.sp)).map(d => d.site_no)

        // nest data to iterate over in plot
        // sort of inverse of series - array of objects where objs = site containing key  for site_no, mmd and day vars
         data.mmd11 = [];
          for (let i = 1; i < n; i++) {
              var key = this.site_elev[i];
              var mmd = data_2011.map(d => d[key]);
              var swe = swe_2011.map(d => d[key]);
              var day = data_2011.map(d => d['site_water_day']);
              data.mmd11.push({key: key, mmd: mmd, day:day, swe:swe})
          };

          data.mmd12 = [];
          for (let i = 1; i < n; i++) {
              var key = this.site_elev[i];
              var mmd = data_2012.map(d => d[key]);
              var swe = swe_2012.map(d => d[key]);
              var day = data_2012.map(d => d['site_water_day']);
              data.mmd12.push({key: key, mmd: mmd, day:day, swe:swe})
          };

        // array of j days for good luck
        data.days = data_2011.length > data_2012.length ? data_2011.map(d => d['site_water_day']) : data_2012.map(d => d['site_water_day'])

        // set up g that holds ridgelines
        this.svgboth = this.d3.select('svg#mmd-line-both');
        this.svgstack = this.d3.select('svg#mmd-line-stack');

         // draw hydro chart elements
        var x_long = this.width-(this.margin*3);
        var mid = x_long/2;
        var mar = mid*0.05

        // set chart - separate for each year, using 2011 for max values to set the scales, except
        // x scale of days, which is set based on whichever year has more days (2012)
        //  first draw is MMD
        self.initRidges(this.svgboth, 'ridge_2011', data.mmd11, data.days, 0, x_long, 0, this.height/2-10);
        self.initRidges(this.svgboth, 'ridge_2012', data.mmd12, data.days, 0, x_long, this.height/2+10,  this.height);

      },
      initRidges(svg, ridge_class, data_nest, days, x_start, x_end,  y_start, y_end){
        const self = this;

        // x axis - time
        var x = this.d3.scaleLinear()
          .domain([1,365])
          .range([x_start, x_end]);

        // y mmd
        var y_mmd = this.d3.scaleLinear()
          .domain([30, 0]).nice()
          .range([y_start, y_end])

          // y swe
        var y_swe = this.d3.scaleLinear()
          .domain([1100, 0]).nice()
          .range([y_start, y_end])

        // define & style x &  y axes
        this.xAxis = g => g
          .attr("transform", `translate(0,${y_end})`)
          .call(this.d3.axisBottom(x)
              .tickValues([1, 93, 183, 273])
              .tickFormat(function(d,i) { return self.tickDates[i] })
              .tickSizeOuter(0).tickSize(0))

        // mmd axes
        this.yAxis_mmd = g => g
          .attr("transform", `translate(${this.width-75},0)`)
          .call(this.d3.axisRight(y_mmd).tickSize(0).tickPadding(4).tickValues([0, 10, 20, 30]))

        // mmd axes
        this.yAxis_swe = g => g
          .attr("transform", `translate(${0},0)`)
          .call(this.d3.axisLeft(y_swe).tickSize(0).tickPadding(4).tickValues([0, 250, 500, 750, 1000]))

        // append axes
        svg.append("g").classed("xaxis", true).classed(ridge_class, true).call(this.xAxis).attr("font-style", "italic");
        svg.append("g").classed("yaxis", true).classed(ridge_class, true).call(this.yAxis_mmd).attr("font-style", "italic").attr("color", this.color_mmd).classed("mmd", true);
        svg.append("g").classed("yaxis", true).classed(ridge_class, true).call(this.yAxis_swe).attr("font-style", "italic").attr("color", this.color_swe).classed("swe", true);

        // define area chart parameters
        this.area_mmd = this.d3.area()
          .curve(this.d3.curveBasis)
          .defined(d => !isNaN(d))
          .x((d, i) => x(days[i]))
          .y0(0)
          .y1(d => y_mmd(d))

        this.area_swe = this.d3.area()
          .curve(this.d3.curveBasis)
          .defined(d => !isNaN(d))
          .x((d, i) => x(days[i]))
          .y0(0)
          .y1(d => y_swe(d))
          
        this.line_mmd = this.area_mmd.lineY1()
          .defined(d => !isNaN(d));

        this.line_swe = this.area_swe.lineY1()
          .defined(d => !isNaN(d));

        // append g for each ridgeline/site_no
        this.group = svg.append("g")
          .classed(ridge_class, true).classed("curve", true)
          .selectAll("g")
          .data(data_nest)
          .join("g")
          .attr("class", d => "ridge_group " + d.key)
          .attr("transform", d => `translate(0,0)`)

        // draw MMD curves
        this.group.append("path")
          .attr("stroke", this.color_mmd)
          .attr("fill", this.color_mmd)
          .attr("stroke-opacity", .5)
          .attr("fill-opacity",.1)
          .attr("d", d => this.line_mmd(d.mmd))
          .attr("stroke-width", "1px")
          .attr("class", d => d.key)
          .classed("ridge", true)
          .classed("mmd", true)
          .attr('pointer-events', 'visibleStroke')
          .on("mouseover", d => self.hover(d))
          .on("mouseout", d => self.hoverOut(d));

         // draw SWE curves
        this.group.append("path")
          .attr("stroke", this.color_swe)
          .attr("fill", this.color_swe)
          .attr("stroke-opacity", .5)
          .attr("fill-opacity", .1)
          .attr("d", d => this.line_swe(d.swe))
          .attr("stroke-width", "1px")
          .attr("class", d => d.key)
          .classed("ridge", true)
          .classed("swe", true)
          .attr('pointer-events', 'visibleStroke')
          .on("mouseover", d => self.hover(d))
          .on("mouseout", d => self.hoverOut(d));


        this.y2011 = this.svgboth.selectAll("g.ridge_2011") // ridge group
        this.y2012 = this.svgboth.selectAll("g.ridge_2012")
        this.ridge11 = this.d3.selectAll("g.ridge_2011.curve g") // ridge themselves for staggered animation
        this.ridge12 = this.d3.selectAll("g.ridge_2012.curve g") 

        this.highlabel = this.svgboth.select("text#peak11") // high and low snow labels
        this.lowlabel = this.svgboth.select("text#peak12")
        this.highel = this.svgboth.select("text#el11") // high and low snow labels
        this.lowel = this.svgboth.select("text#el12")
        this.fast = this.svgboth.select("text#melt11") // high and low snow labels
        this.slow = this.svgboth.select("text#melt12")    

        this.y11_swe = this.d3.select(".yaxis.ridge_2011.swe") // axes
        this.y12_swe = this.d3.select(".yaxis.ridge_2012.swe")
        this.y11_mmd = this.d3.select(".yaxis.ridge_2011.mmd") // axes
        this.y12_mmd = this.d3.select(".yaxis.ridge_2012.mmd")
        this.x11 = this.d3.select(".xaxis.ridge_2011")
        this.x12 = this.d3.select(".xaxis.ridge_2012")

        this.lowlabel.transition().duration(0).attr("opacity",0)
        this.highlabel.transition().duration(0).attr("opacity",0)
        this.lowel.transition().duration(0).attr("opacity",0)
        this.highel.transition().duration(0).attr("opacity",0)
        this.slow.transition().duration(0).attr("opacity",1)
        this.fast.transition().duration(0).attr("opacity",1)

      },
      hover(data){
         const self = this;

          self.d3.selectAll('g.ridge_group')
            .lower()

          self.d3.selectAll('g.ridge_group.' + data.key)
            .raise()

          self.d3.selectAll('g.curve path.mmd.' + data.key)
            .attr('stroke-width', "2px")
            .attr('stroke', "darkblue")
            .attr('stroke-opacity', .8)

          self.d3.selectAll('g.curve path.swe.' + data.key)
            .attr('stroke-width', "2px")
            .attr('stroke', "black")
            .attr('stroke-opacity', .8)

      },
      hoverOut(data){
         const self = this;

         self.d3.selectAll('g.ridge_group')
            .lower()

         self.d3.selectAll('g.curve path.mmd.' + data.key)
            .attr("stroke-width", "1px")
            .attr("stroke", this.color_mmd)
            .attr('stroke-opacity', .5)

        self.d3.selectAll('g.curve path.swe.' + data.key)
            .attr("stroke-width", "1px")
            .attr("stroke", this.color_swe)
            .attr('stroke-opacity', .5)

        self.d3.selectAll('g.curve path.mmd')
            .attr("stroke-width", "1px")
            .attr("stroke", this.color_mmd)
            .attr('stroke-opacity', .5)

        self.d3.selectAll('g.curve path.swe')
            .attr("stroke-width", "1px")
            .attr("stroke", this.color_swe)
            .attr('stroke-opacity', .5)

      },
      showSWE(){
        if (this.sweActive == true){
          this.sweActive = false;

          this.d3.selectAll(".ridge.swe")
          .transition()
          .delay(50)
          .duration(300)
          .attr("opacity", 0)
        } else if (this.sweActive == false){
          this.sweActive = true;

          this.d3.selectAll(".ridge.swe")
          .transition()
          .delay(50)
          .duration(300)
          .attr("opacity", 0.5)
        }

      },
      showFlow(){
          if (this.flowActive == true){
          this.flowActive = false;

          this.d3.selectAll(".ridge.mmd")
          .transition()
          .delay(50)
          .duration(300)
          .attr("opacity", 0)
        } else if (this.flowActive == false){
          this.flowActive = true;

          this.d3.selectAll(".ridge.mmd")
          .transition()
          .delay(50)
          .duration(300)
          .attr("opacity", 0.5)
        }
      },
      changePos(e){
        const self = this;
          if(e.target.value == "peak"){
            self.toMagnitude()
          }
          if(e.target.value == "time"){
            self.toTiming();
          }
           if(e.target.value == "el"){
            self.toMagnitude()
            self.toElevation();
          }
      },
      transPosition(el, delay, duration, x, y, xscale, yscale){
        el
        .transition()
          .delay(delay)
          .duration(duration)
          .attr("transform", "translate(" + (x) + ", " + (y) + ") scale(" + xscale + "," + yscale + ")")

      },
      transFade(el, delay, duration, alpha){
       el
        .transition()
        .duration(duration)
        .delay(delay)
        .attr("opacity", alpha)
      },
      transAxis(el, delay, duration, axis, x, y, xscale, yscale){
        el
        .transition()
        .duration(duration)
        .delay(delay)
        .call(axis)
        .attr("transform", "translate(" + (x) + ", " + (y) + ") scale(" + xscale + "," + yscale + ")" )

      },
      toTiming(){
        const self = this;
        
        // fade in y axis if coming from elevation
        self.transFade(this.d3.selectAll("g.yaxis g"), 300, 500, 1)

        // make sure ridges are stacked flat
        this.y2011.selectAll("path.ridge")
          .transition()
          .delay(function(d,i) { return i*15 })
          .duration(1100)
          .attr("transform", "translate(0," + (0) + ")" )

        // move labels
        //self.transPosition(self.lowlabel, 600, 800, 0, 0, 1, 1)
        //self.transPosition(self.highlabel, 700, 700, 0, 0, 1, 1)

        this.lowlabel.transition().delay(50).duration(300).attr("opacity",0)
        this.highlabel.transition().delay(50).duration(300).attr("opacity",0)
        this.lowel.transition().delay(50).duration(300).attr("opacity",0)
        this.highel.transition().delay(50).duration(300).attr("opacity",0)
        this.slow.transition().delay(50).duration(300).attr("opacity",1)
        this.fast.transition().delay(50).duration(300).attr("opacity",1)

        // transform axes
        self.transAxis(this.x11, 50, 300, self.xAxis, 0, this.height/2-10, 1, 1)
        self.transAxis(this.x12, 50, 500, self.xAxis, 0, this.height, 1, 1)
        self.transAxis(this.y11_swe, 50, 300, self.yAxis_swe, 0, -this.height/2-10, 1, 1)
        self.transAxis(this.y12_swe, 50, 500, self.yAxis_swe, 0, 0, 1, 1)
        self.transAxis(this.y11_mmd, 50, 300, self.yAxis_mmd, this.width-75, -this.height/2-10, 1, 1)
        self.transAxis(this.y12_mmd, 50, 500, self.yAxis_mmd, this.width-75, 0, 1, 1)

        // stretch ridges to full x and y extent
        self.transPosition(this.d3.selectAll("g.ridge_2011.curve"), 270, 500,  0, 0, 1, 1)
        self.transPosition(this.d3.selectAll("g.ridge_2012.curve"), 250, 500,  0, 0, 1, 1)

        // un-spread ridge
        this.ridge11
        .transition()
        .delay(function(d,i) { return i*15 })
        .duration(400)
        .attr("transform", function(d, i) { 
          return "translate(0," + (0+i*0)  + ")"
        })

        this.ridge12
        .transition()
        .delay(function(d,i) { return i*15 })
        .duration(400)
        .attr("transform", function(d, i) { 
          return "translate(0," + (0) + ")"
        })


      },
      toMagnitude(){
        const self = this;
        
        self.shiftRidges();

        // flatten stacks
        this.y2011.selectAll("path.ridge")
          .transition()
          .delay(function(d,i) { return i*15 })
          .duration(1000)
          .attr("transform", "translate(0," + (this.height/2+10) + ")" )

        this.ridge11
        .transition()
        .delay(50)
        .duration(400)
        .attr("transform", function(d, i) { 
          return "translate(0," + (0+i*0)  + ") scale(1, 1)"
        })

        this.ridge12
        .transition()
        .delay(50)
        .duration(400)
        .attr("transform", function(d, i) { 
          return "translate(0," + (0) + ") scale(1, 1)"
        })

        // y mmd
        var y_mmd = this.d3.scaleLinear()
          .domain([30, 0]).nice()
          .range([this.height/2+10, this.height])

          // y swe
        var y_swe = this.d3.scaleLinear()
          .domain([1100, 0]).nice()
          .range([this.height/2+10, this.height])

      // mmd axes
        var y_mmd_low = g => g
          .attr("transform", `translate(${this.width-75},0)`)
          .call(this.d3.axisRight(y_mmd).tickSize(0).tickPadding(4).tickValues([0, 10, 20, 30]))

          // mmd axes
        var y_swe_low = g => g
          .attr("transform", `translate(${0},0)`)
          .call(this.d3.axisLeft(y_swe).tickSize(0).tickPadding(4).tickValues([0, 250, 500, 750, 1000]))
        
        self.transFade(this.d3.selectAll("g.yaxis g"), 300, 500, 1)

         // transform axes
        self.transAxis(this.y11_mmd, 150, 500, y_mmd_low, this.width-75,0, 1, 1)
        self.transAxis(this.y12_mmd, 150, 500, y_mmd_low, this.width-75, 0,1, 1)
        self.transAxis(this.y11_swe, 150, 500, y_swe_low, 0, 0, 1, 1)
        self.transAxis(this.y12_swe, 150, 500, y_swe_low, 0, 0, 1, 1) 

        this.lowlabel.transition().delay(50).duration(300).attr("opacity",1)
        this.highlabel.transition().delay(50).duration(300).attr("opacity",1)
        this.lowel.transition().delay(50).duration(300).attr("opacity",0)
        this.highel.transition().delay(50).duration(300).attr("opacity",0)
        this.slow.transition().delay(50).duration(300).attr("opacity",0)
        this.fast.transition().delay(50).duration(300).attr("opacity",0)

      },
      toElevation(){
        const self = this;

        // spread ridges
        this.ridge11
        .transition()
        .delay(50)
        .duration(500)
        .attr("transform", function(d, i) { 
          return "translate(0," + (40+i*-10)  + ") scale(1, .9)"
        })

        this.ridge12
        .transition()
        .delay(50)
        .duration(500)
        .attr("transform", function(d, i) { 
          return "translate(0," + (40+i*-10) + ") scale(1, .9)"
        })

        //var color = this.d3.interpolateRainbow;

        // make bigger axes
         var y = this.d3.scaleLinear()
          .domain([25, 0]).nice()
          .range([0, this.height])

        var yAxisTall = g => g
          .attr("transform", `translate(${0},0)`)
          .call(this.d3.axisLeft(y).tickSize(0).tickPadding(4))

        // move labels
        //self.transPosition(self.highlabel, 350, 600, 20, -100, 1, 1)
        //self.transPosition(self.lowlabel, 450, 500, 320, -300, 1, 1)
        this.lowlabel.transition().delay(50).duration(300).attr("opacity",0)
        this.highlabel.transition().delay(50).duration(300).attr("opacity",0)
        this.lowel.transition().delay(50).duration(300).attr("opacity",1)
        this.highel.transition().delay(50).duration(300).attr("opacity",1)
        this.slow.transition().delay(50).duration(300).attr("opacity",0)
        this.fast.transition().delay(50).duration(300).attr("opacity",0)

       // transform axes
        self.transAxis(this.y11_swe, 150, 500, self.yAxis_swe, 0, -100, 1, 1)
        self.transAxis(this.y12_swe, 150, 500, self.yAxis_swe, 0, 0, 1, 1) 
        self.transAxis(this.y11_mmd, 150, 500, self.yAxis_mmd, this.width-75, -100, 1, 1)
        self.transAxis(this.y12_mmd, 150, 500, self.yAxis_mmd, this.width-75, 0, 1, 1) 

        self.transFade(this.d3.selectAll("g.yaxis g"), 300, 400, 0)
      },
      shiftRidges(){
        const self = this;
        var x_long = this.width-(this.margin*3);
        var mid = x_long/2;
        var mar = mid*0.05

        var xhalfL = this.d3.scaleLinear()
          .domain([1,365])
          .range([0, mid-5]);

        var xhalfR = this.d3.scaleLinear()
          .domain([1,365])
          .range([mid+5, x_long]);

        
        var xAxisL = g => g
          .attr("transform", `translate(0,${this.height})`)
          .call(this.d3.axisBottom(xhalfL)
              .tickValues([1, 93, 183, 273])
              .tickFormat(function(d,i) { return self.tickDates[i] })
              .tickSizeOuter(0).tickSize(0))

         var xAxisR = g => g
          .attr("transform", `translate(0,${this.height})`)
          .call(this.d3.axisBottom(xhalfR)
              .tickValues([1, 93, 183, 273])
              .tickFormat(function(d,i) { return self.tickDates[i] })
              .tickSizeOuter(0).tickSize(0))

   // transform axes
        self.transAxis(this.x11, 150, 400, xAxisL, 0, this.height, 1, 1)
        self.transAxis(this.x12, 150, 500, xAxisR, 0, this.height, 1, 1)
         /*    self.transAxis(this.y11_swe, 350, 500, self.yAxis_swe, 0, this.height/2, 1, 1)
        self.transAxis(this.y12_swe, 350, 400, self.yAxis_swe, 0, 0, 1, 1)
        self.transAxis(this.y11_mmd, 350, 500, self.yAxis_mmd, this.width-75, this.height/2, 1, 1)
        self.transAxis(this.y12_mmd, 350, 400, self.yAxis_mmd, this.width-75, 0, 1, 1) */

        this.d3.selectAll("g.ridge_2011.curve")
        .transition()
        .delay(170)
        .duration(500)
        .attr("transform", "translate(0, 0) scale(.49, 1)")

         this.d3.selectAll("g.ridge_2012.curve")
        .transition()
        .delay(150)
        .duration(500)
        .attr("transform", "translate(270, 0) scale(.49, 1)")

      }
    }
}
</script>
<style lang="scss" scoped>
$familySerif:  'Noto Serif', serif;
.maxWidth {
  width: 90vw;
  margin-left: 5vw;
  max-width: 700px;
  margin: auto;
}

.compare {
  border: 0px solid black;
  display: inline-block;
  width: 80vw;
  max-width: 600px;
  font-size: 18px;
  text-align: center;
  padding: 15px 0px;
  margin: auto;
  position: relative;
  h4{
    margin-bottom: 15px;
  }
}
.butt {
  padding: 5px 10px;
  margin: 5px 5px;
  cursor: pointer;
  display: inline-block;
}
.yr-label {
  font-size: 16px;
  font-weight: 400;
  text-anchor: middle;
  font-style: italic;
  fill: rgb(165, 163, 163);
}
.inputsContainer{
  .inputs{
    font-size:.85em;
  }
}
#mmd-container-both {
  width: 90vw;
  max-width: 900px;
  margin: auto;
}
svg#mmd-line-both{
  transform: translate(-5px, 0);
}

input[name="radiogroup1"] {
            display: none;
        }
         input[name="radiogroup1"]+label {
            /* style passive state as you like */
            background-color: rgb(221,221,221);
            border: 2px solid transparent;
            color: black;
            font-weight: 400;
            transition: background-color .1s, border .1s;
        }

    input[name="radiogroup1"]:checked+label {
        /* style checked state as you like */
        border: 7px solid dodgerblue;
        background-color: dodgerblue;
        color: white;
    }
input[name="checkboxgroup1"] {
            display: none;
        }
         input[name="checkboxgroup1"]+label {
            /* style passive state as you like */
            background-color: rgb(221,221,221);
            border: 2px solid transparent;
            color: black;
            font-weight: 400;
            transition: background-color .1s, border .1s;
        }

    input[name="checkboxgroup1"]:checked+label {
        /* style checked state as you like */
        border: 7px solid dodgerblue;
        background-color: dodgerblue;
        color: white;
    }
    input[name="checkboxgroup2"] {
            display: none;
        }
         input[name="checkboxgroup2"]+label {
            /* style passive state as you like */
            background-color: rgb(221,221,221);
            border: 2px solid transparent;
            color: black;
            font-weight: 400;
            transition: background-color .1s, border .1s;
        }

    input[name="checkboxgroup2"]:checked+label {
        /* style checked state as you like */
        border: 7px solid grey;
        background-color: grey;
        color: white;
        transition: background-color .1s, border .1s;
    }
    

@media screen and (min-width: 650px){
  .compare{
    width: 100%;
    max-width: 600px;
    padding: 5px 5px;
    .btn-group{
      display: flex;
      align-items: center;
      .inputsContainer{
        flex: 2;
        position: relative;
        display: flex;
        align-items: center;
        justify-content: space-between;
        .inputs{
          position: absolute;
          left: 10px;
          font-size: 18px;
          .butt{
            margin-right: 10px;
          }
          .butt:last-child{
            margin-right: 0;
          }
        }
      }
      #mmd-container-both {
        width: 90vw;
        max-width: 1200px;
        margin: auto;
      }
      h4{
        flex: 1;
        margin-bottom: 0;
      }
    }
  } 
}
// adding a break for full screen laptop adjustments
@media screen and (max-height: 750px){
  #mmd-container-both {
    width: 90vw;
    max-width: 700px;
    margin: auto;
  }

}
</style>
