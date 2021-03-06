Physiological measures and confounds
************************************

:status: hidden
:slug: mod_physio

**Cardiac and respiratory trace, and motion estimates**

Cardiac and respiratory trace
=============================

These data have been obtained simultaneously with the fMRI recording.  Files
are in plain text format (compressed) for each scanning run separately and
includes the MR scanner's volume acquisition trigger signal for precise
temporal alignment. For example, these data are useful for de-noising fMRI time
series. In addition, the data files include an estimate of the blood oxygen
saturation. The interactive plot below shows an exemplary minute of data at 50
Hz sampling rate (actual data is sampled at 200 Hz).


.. raw:: html

  <div class="row">
   <div class="col-md-12">
       <button type="button" class="btn btn-default" onclick="showLF()">Show trends</button>
       <button type="button" class="btn btn-default" onclick="showDetail()">Show details</button>
     <span id="physio_legend" style='width:100%;text-align:right'></span>
     <div id="physio_chart" style='margin-top:10px;width:100%;height=300px'></div>
    <div class="dygraph_help">
         <p>Usage: Click and drag on the lower focus chart to move along the
         time axis. Perform temporal data averaging by specifying the length
         of the window (in samples) in the lower left of the plot.</p>
    </div>
   </div><!-- /.col-md-12 -->
  </div><!-- /.row -->

Participant motion estimates
============================

These data have been estimated from the raw BOLD images prior to distortion
correction. Files are provided in a six-column plain text format for each fMRI
scanning run separately. The plots below show example data from one subject for
an entire movie segment (approx. 15 min).

.. raw:: html

  <div class="row">
   <div class="col-md-12">
     <span id="moco_transl_legend" style='width:100%;text-align:right'></span>
     <div id="moco_transl_chart" style='margin-top:10px;width:100%;height=200px'></div>
     <span id="moco_rot_legend" style='width:100%;text-align:right'></span>
     <div id="moco_rot_chart" style='margin-top:10px;width:100%;height=200px'></div>
    <div class="dygraph_help">
         <p>Usage: Click and drag on the lower focus chart to move along the
         time axis. Perform temporal data averaging by specifying the length
         of the window (in samples) in the lower left of the plot.</p>
    </div>
   </div><!-- /.col-md-12 -->
  </div><!-- /.row -->
 
.. raw:: html

  <script type="text/javascript" src="/js/dygraph-combined.js"></script>
  <script type="text/javascript" src="/js/jquery.min.js"></script>
  
  <script>
  $(document).ready(function () {
   phys = new Dygraph(
    document.getElementById("physio_chart"),
    "/data/physio.csv",
    {
      ylabel: "relative amplitude",
      xlabel: "time (s)",
      legend: "always",
      showRangeSelector: true,
      showRoller: true,
      labelsDiv: "physio_legend",
      dateWindow: [0, 10]
    }
   );
   // moco
   plt_ids = ['transl', 'rot'];
   plts = [];
   var blockRedraw = false;
   for (var i = 0; i < plt_ids.length; i++) {
   console.log(i);
   console.log("moco_" + plt_ids[i] + "_chart");
    plts.push(
     new Dygraph(
      document.getElementById("moco_" + plt_ids[i] + "_chart"),
       "/data/moco_" + plt_ids[i] + ".csv",
       {
        ylabel: plt_ids[i] + "ation",
        xlabel: "time (s)",
        legend: "always",
        showRoller: true,
        showRangeSelector: i > 0,
        labelsDiv: "moco_" + plt_ids[i] + "_legend",
        drawCallback: function(me, initial) {
         if (blockRedraw || initial) return;
         blockRedraw = true;
         var range = me.xAxisRange();
         var yrange = me.yAxisRange();
         for (var j = 0; j < plt_ids.length; j++) {
          if (plts[j] == me) continue;
          plts[j].updateOptions( {
           dateWindow: range
          });
         }
         blockRedraw = false;
        }
       }
      )
     )
    }
  });
  
  function showLF() {
    phys.updateOptions({
     dateWindow: [0, 60],
     rollPeriod: 500
    });
  }
  
  function showDetail() {
    phys.updateOptions({
     dateWindow: [0, 10],
     rollPeriod: 1
    });
  }
  
  
  </script>
