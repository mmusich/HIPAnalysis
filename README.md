# HIPAnalysis

#### Where am I?

This is a CMSSW 'analyzer' running onto tracker DPG `calibTree`, centrally produced, for analyses purposes. This package was designed for the investigation the SiStrip dynamic inefficiency in 2015-2016. Initially, the phenomenon was (incorrectly) believed to be due mainly to Heavy Ionizing Particles, aka HIP. Hence the name.
_I'm sorry if the package is not clean with plenty of unused stuff all over the place, this was meant for my own usage at first, I'll be back and clean it at some point._

#### Why CMSSW and not a plain ROOT macro?

Because there are a lot of `calibTree` out there. So, to have a better scaling for analyzing a lot of them, and because I'm lazy and the trees are available on `eos`, I decided to have CRAB do the job handling, copying the output back to storage, etc. On the plus side, this also makes this code batch-system independent. So feel free to run this on lxplus or your local cluster, it does not matter.

#### CRAB, really? but `calibTree` are not EDM files?

Yes, the trick here is to have CRAB believes it is doing MC production, which gives some more freedom about this
. The analyzer will read one `calibTree` file per produced 'MC event'. This system do have some overhead, but I believe it's scaling much better.

#### Ok, let's go!



## First time setup

```bash
cmsrel CMSSW_9_2_3_patch2
cd CMSSW_9_2_3_patch2/src
cmsenv
git cms-init
cd ${CMSSW_BASE}/src 
git clone -o upstream git@github.com:blinkseb/TreeWrapper.git CalibTracker/TreeWrapper
git clone -o upstream git@github.com:OlivierBondu/HIPAnalysis.git CalibTracker/HIPAnalysis
cd ${CMSSW_BASE}/src/
scram b -j 4
cd CalibTracker/HIPAnalysis
```

## Code orientation

#### For plotting S/N as a function of the BX (a.k.a. charge/path vs BX)

1. First, you need to list the `calibTree` you are interested in
   * Usually they are in places like `/store/group/dpg_tracker_strip/comm_tracker/Strip/Calibration/calibrationtree/GR17/`
   * Create a file in `test/data` containing the full (eos) path of the files your interested in, see for example [`test/data/list_calibTrees_Fill-5750_Run-296173.txt`](https://github.com/OlivierBondu/HIPAnalysis/blob/master/test/data/list_calibTrees_Fill-5750_Run-296173.txt) (note: putting `#` at the beginning of the line will skip the file)
1. Then, you want to create the histograms from the `calibTree`
   * The core code reading the `calibTree` and producing the histograms is [`plugins/CalibTreesLayerAnalysis.cc`](https://github.com/OlivierBondu/HIPAnalysis/blob/master/plugins/CalibTreesLayerAnalysis.cc)
   * The corresponding configuration file is [`test/CalibTreesLayerAnalysis.py`](https://github.com/OlivierBondu/HIPAnalysis/blob/master/test/CalibTreesLayerAnalysis.py): edit, then run it via `cmsRun CalibTreesLayerAnalysis.py` 
      * or through CRAB if you have a lot of files (**Work in progress**, *crab config file to be updated*)
   * **Important Note**: the number of events *should not exceed the number of files you are reading*
1. You know have the histograms! yeah! all you have to do now is plot them:
   * The plotter is [`python/plotLayerHistos.py`](https://github.com/OlivierBondu/HIPAnalysis/blob/master/python/plotLayerHistos.py): edit, then run it via `python plotLayerHistos.py`
1. Look at the plots, and finally do the physics: interpret them

#### For plotting something else: instructions not ready yet

But you're welcome to have a look around of course! Beware there are pieces of code that are useless at this point. There has been no thorough clean-up yet.
