<template>
     <div ref="chartId" class="fill"></div>
</template>

<script>
import { lightningChart  } from '@arction/lcjs'
import { ColorPalettes, Themes, PalettedFill, emptyLine, LUT, AxisTickStrategies} from '@arction/lcjs'
//import { LUT, Themes, ColorRGBA, PalettedFill} from '@arction/lcjs'

export default {
  name: 'ChartData',
  props: ['points'],
   data() {
    // Add the chart to the data in a way that Vue will not attach it's observers to it.
    // If the chart variable would be added in the return object, Vue would attach the observers and 
    // every time LightningChart JS made a change to any of it's internal variables, vue would try to observe the change and update.
    // Observing would slow down the chart a lot.
    this.chart = null
    return {
      chartId: null,
    }
  },
  methods: {
    createChart() {
      // Specify the resolution used for the heatmap.
      // Create chartXY

      const nRows = 10000;
      const nCols = 10000;

      var data = Array( nRows );
      var row = Array( nCols );

      for( var ii = 0; ii < nRows; ii++){

        row = Array( nCols );
        for( var jj = 0; jj < nCols; jj++){
          row[jj] = 2.5 * Math.sin( 10 * ii / nRows) * Math.cos( 10 * jj / nCols) + 2.5;
        }
        data[ii] = row;
      }

      console.log( data )

      const palette = ColorPalettes.fullSpectrum(5);

      const paletteLUT = new LUT({
        units: "intensity",
        steps: [
          { value: 0, color: palette(0) },
          { value: 1, color: palette(1) },
          { value: 2, color: palette(2) },
          { value: 3, color: palette(3) },
          { value: 4, color: palette(4) },
          { value: 5, color: palette(5) },
        ],
        interpolate: true,
      });

      const resolutionX = nCols;
      const resolutionY = nRows;
      var chart = lightningChart().ChartXY({container: this.$refs.chartId, theme: Themes.lightNew})
      
      chart
      .addHeatmapGridSeries({
        columns: resolutionX,
        rows: resolutionY,
        dataOrder: "rows",
      })
      .setWireframeStyle(emptyLine)
      .setFillStyle(new PalettedFill({ lut: paletteLUT }))
      .invalidateIntensityValues( data )
      .onMouseDoubleClick( (event) => {
        console.log( event )
        console.log("Double click")
      })
      console.log( palette )

      chart.onSeriesBackgroundMouseDoubleClick( ( obj, event, button, start) => {
        console.log( event )
        console.log( start )
        console.log( "Double click" )
      })

      chart.onSeriesBackgroundMouseDragStop( (obj, event, button, start) => {
        console.log( event )
        console.log( "Dragging" )
        console.log( obj )
        console.log( start )
        console.log
        console.log( button )
        console
      })

      chart.setTitle("")

      var xAxis = chart.getDefaultAxisX();
      var yAxis = chart.getDefaultAxisY();

      xAxis
      // Specify tick strategy and configure it via callback function.
      .setTickStrategy(AxisTickStrategies.Numeric, (ticks) => ticks
          .setMajorTickStyle((style) => style
              .setLabelFont((font) => font.setSize(20))
          )
      );
      
      xAxis
      // Specify tick strategy and configure it via callback function.
      .setTickStrategy(AxisTickStrategies.Numeric, (ticks) => ticks
          .setMinorTickStyle((style) => style
              .setLabelFont((font) => font.setSize(20))
          )
      );

      yAxis
      // Specify tick strategy and configure it via callback function.
      .setTickStrategy(AxisTickStrategies.Numeric, (ticks) => ticks
          .setMajorTickStyle((style) => style
              .setLabelFont((font) => font.setSize(20))
          )
      );

      yAxis
      // Specify tick strategy and configure it via callback function.
      .setTickStrategy(AxisTickStrategies.Numeric, (ticks) => ticks
          .setMinorTickStyle((style) => style
              .setLabelFont((font) => font.setSize(20))
          )
      );


    }
  },
  beforeMount() {
    // Generate random ID to us as the containerId for the chart and the target div id
    this.chartId = Math.trunc(Math.random() * 1000000)
  },
   mounted() {
    // Chart can only be created when the component has mounted the DOM because 
    // the chart needs the element with specified containerId to exist in the DOM
    this.createChart();
  }
}

</script>
<style scoped>
  .fill {
    height: 100%;
  }
</style>