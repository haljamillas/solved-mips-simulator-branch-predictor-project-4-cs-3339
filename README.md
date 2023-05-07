Download Link: https://assignmentchef.com/product/solved-mips-simulator-branch-predictor-project-4-cs-3339
<br>
<h1>PROBLEM STATEMENT</h1>

In this project, you will reduce the number of pipeline flushes required by adding a branch predictor to your simulator.  This predictor will predict only conditional branch instructions, i.e. <em>beq</em> and <em>bne</em>.




You should begin with a copy of your Project 3 submission.  I have provided a new Makefile as well as a class skeleton for a BranchPred class intended to be instantiated inside your existing CPU class.




You should simulate a dynamic branch predictor that uses a 64-entry direct-mapped table with no tag and no valid bits.  Each entry stores a 2-bit saturating counter (described below) used to generate a “taken” or “not-taken” prediction, as well as a target PC (i.e., the PC the branch is predicted to jump to if it is taken).

The table should be indexed as follows: index = (PC<sub>branch</sub> &gt;&gt; 2) % BPRED_SIZE.  (I have defined BPRED_SIZE for you in BranchPred.h).




All counters should be initialized to zero.  Whenever a branch is taken, update the corresponding 2-bit saturating counter by incrementing it, unless it is already at its maximum value (3).  Whenever a branch is not taken, update the counter by decrementing it, unless it is already at its minimum value (0).  The prediction should be “branch taken” if PC<sub>branch</sub>’s count is at least 2 and “branch not taken” otherwise.




You may structure the interaction between your CPU and branch predictor however you’d like.  However, <strong>be careful: for any branch instruction, first make a prediction, <em><u>then</u></em> update the predictor</strong>.  In other words, you can only use <em><u>past</u></em> information to make a prediction.  To make sure, it’s simplest to have separate BranchPred::predict() and BranchPred::update() functions.




Up until now, your processor has flushed the pipeline behind any taken branch instruction.  Modify your code so that a flush occurs only when a branch has been <strong><em>mispredicted</em></strong>.




Here’s my recommended approach from a CPU.cpp decode of <em>beq</em> and <em>bne</em> perspective:

<ul>

 <li>Based on the PC, predict whether the operation will be a taken branch and to what address.</li>

 <li>Determine its actual direction (taken vs. not-taken) and target PC.</li>

 <li>Determine if there was a misprediction. A misprediction can be one of two types:

  <ol>

   <li>Direction mispredict: compare the predicted and actual taken vs. not-taken determination</li>

   <li>Target mispredict: <em>if the branch was correctly predicted taken</em>, compare the predicted and actual target PCs</li>

  </ol></li>

</ul>

If there was a misprediction, flush the pipeline behind the branch as you did for all taken branches in Project 3.  For a correct prediction no pipeline flush is necessary.

<ul>

 <li>Update the 2-bit saturating counter for the branch’s PC, based on its actual taken or not-taken determination<em>. If the branch was taken</em>, also update the predictor’s target PC.</li>

</ul>




Your branch predictor model will calculate and report the following statistics:

<ul>

 <li>The total number of <strong>predicted branches</strong></li>

 <li>The number of branches that were <strong>predicted taken</strong> <strong>predicted not-taken</strong></li>

 <li>The total number of <strong>mispredictions</strong></li>

 <li>The number of <strong>direction mispredictions</strong> (i.e., T vs. NT decision was wrong)</li>

 <li>The number of <strong>target mispredictions</strong> (i.e., the branch was correctly predicted taken but the target PC was wrong)</li>

 <li>The <strong>overall predictor accuracy</strong></li>

</ul>




I have provided the following new files:

<ul>

 <li>A h class specification file, which you’ll need to add member variables and function prototypes to</li>

 <li>A cpp class implementation file, which you should enhance to model the described branch predictor and count predictions and mispredictions</li>

 <li>A new Makefile</li>

 <li>A sample output file out for you to confirm your output and check formatting.</li>

 <li>A debug output for mips in a separate tar file.</li>

</ul>




In addition to enhancing the BranchPred.h/.cpp skeleton, you will also need to modify CPU.h to instantiate a BranchPred object, and CPU.cpp to call your BranchPred class functions and call

Stats::flush() only on mispredictions.  You’ll also need to change CPU::printFinalStats() to match my expected output format (see below).




<strong> </strong>

<h1>ASSIGNMENT SPECIFICS</h1>




All provided source files are on TRACS cs3339_project4bp.tgz.




Begin by copying your project3 files into a new project4 directory.  Then extract the additional files in cs3339_project4bp.tgz and add them to your project4 directory.  You can compile and run the simulator program identically to previous projects, and test it using the same *.mips inputs.




Here are a few steps to get you up and running after setting up your directory.




<ul>

 <li>Write your name in the header of BranchPred.cpp and also confirm your name is in your CPU.cpp and Stats.cpp as well.</li>

 <li>Include BranchPred.h in your CPU.h file and instantiate an object named Refer to the ALU instantiation as an example.</li>

 <li>At the bottom of CPU.cpp remove the unneeded MemOps, Branches, Taken p3 outputs and add printFinalStats();</li>

 <li>You should now be able to compile and run your project, but the branch prediction output will be incorrect. It is highly recommended that your compile frequently as you work on your code.</li>

</ul>







<h1>CHECK YOUR BASELINE CODE WITHOUT BRANCH PREDICTION</h1>




Since you have not implemented the branch prediction code the output will be incorrect.   Before you implement branch prediction you should see:




CS 3339 MIPS Simulator

Branch Predictor Entries: 64

Running: ../../infiles/sssp.mips




7 1




Program finished at pc = 0x400440  (449513 instructions executed)




Cycles: 1627234

CPI: 3.62




Bubbles: 1125724                       This is before

Flushes: 51990                       branch prediction




Branches predicted: 4198352          – not final output

Pred T: 0 (0.0%)

Pred NT: 4198352

Mispredictions: 4212392 (100.3%)

Mispredicted direction: 0

Mispredicted target: 1385737673

Predictor accuracy: -0.3%




Table 1 contains the expected pre-branch-predictor cycle, flush, and CPI metrics for some of the inputs.  <strong><em>You should not begin implementing your branch predictor until your baseline code matches these numbers!</em></strong>  (The other 3 inputs have un-interesting branch behavior and will not be tested).




<table width="403">

 <tbody>

  <tr>

   <td width="115"><strong>Input </strong></td>

   <td width="120"><strong>Cycle Count </strong></td>

   <td width="102"><strong>Flushes </strong></td>

   <td width="66"><strong>CPI </strong></td>

  </tr>

  <tr>

   <td width="115">hash.mips</td>

   <td width="120">134904</td>

   <td width="102">6526</td>

   <td width="66">3.45</td>

  </tr>

  <tr>

   <td width="115">nqueens.mips</td>

   <td width="120">629285095</td>

   <td width="102">24671130</td>

   <td width="66">3.03</td>

  </tr>

  <tr>

   <td width="115">prime.mips</td>

   <td width="120">659619</td>

   <td width="102">54450</td>

   <td width="66">3.60</td>

  </tr>

  <tr>

   <td width="115">qsort.mips</td>

   <td width="120">167458</td>

   <td width="102">8334</td>

   <td width="66">3.64</td>

  </tr>

  <tr>

   <td width="115">sssp.mips</td>

   <td width="120">1627234</td>

   <td width="102">51990</td>

   <td width="66">3.62</td>

  </tr>

 </tbody>

</table>

<em>Table 1:  Initial values before Branch Prediction</em>




<h1>IMPLEMENT BRANCH PREDICTION</h1>




The following is the expected output for sssp.mips after branch prediction has been implemented.  Your output must match this format verbatim; you should check it by using <em>diff</em> and the provided sssp.out file.  Misspellings and capitalization will throw off the grading scripts.

<strong> </strong>




CS 3339 MIPS Simulator

Branch Predictor Entries: 64

Running: sssp.mips




7 1




Program finished at pc = 0x400440  (449513 instructions executed)




Cycles: 1586032

CPI: 3.53




Bubbles: 1125724

Flushes: 10788




Branches predicted: 42954

Pred T: 25851 (60.2%)

Pred NT: 17103

Mispredictions: 5393 (12.6%)

Mispredicted direction: 2803

Mispredicted target: 2590

Predictor accuracy: 87.4%




Table 2 shows the expected cycle count, flush count, CPI, and predictor accuracy after the inclusion of the simulated branch predictor.




<table width="482">

 <tbody>

  <tr>

   <td width="115"><strong>Input </strong></td>

   <td width="120"><strong>Cycle Count </strong></td>

   <td width="102"><strong>Flushes </strong></td>

   <td width="66"><strong>CPI </strong></td>

   <td width="79"><strong>Predictor Accuracy </strong></td>

  </tr>

  <tr>

   <td width="115">hash.mips</td>

   <td width="120">128836</td>

   <td width="102">458</td>

   <td width="66">3.29</td>

   <td width="79">98.0%</td>

  </tr>

  <tr>

   <td width="115">nqueens.mips</td>

   <td width="120">611789985</td>

   <td width="102">7176020</td>

   <td width="66">2.95</td>

   <td width="79">92.8%</td>

  </tr>

  <tr>

   <td width="115">prime.mips</td>

   <td width="120">940807</td>

   <td width="102">6482</td>

   <td width="66">3.42</td>

   <td width="79">91.6%</td>

  </tr>

  <tr>

   <td width="115">qsort.mips</td>

   <td width="120">160130</td>

   <td width="102">1006</td>

   <td width="66">3.49</td>

   <td width="79">95.6%</td>

  </tr>

  <tr>

   <td width="115">sssp.mips</td>

   <td width="120">1586032</td>

   <td width="102">10788</td>

   <td width="66">3.53</td>

   <td width="79">87.4%</td>

  </tr>

 </tbody>

</table>

<em>Table 2:  Expected values with Branch Prediction </em>




Additional Requirements:

<ul>

 <li><strong>Your code must compile with the given </strong><strong>Makefile and run on zeus.cs.txstate.edu </strong></li>

 <li>Your code must be well-commented, sufficient to prove you understand its operation</li>

 <li>Make sure your code doesn’t produce unwanted output such as debugging messages. (You can accomplish this by using the D(x) macro defined in h)</li>

 <li>Make sure your code’s runtime is not excessive</li>

 <li>Make sure your code is correctly indented and uses a consistent coding style</li>

 <li>Clean up your code before submitting i.e., make sure there are no unused variables, unreachable code, etc.</li>

</ul>




<h1>DEBUGGING</h1>




Most importantly make sure your initial outputs match the values in Table 1.   There is a D(); output in the provided BranchPred.cpp file which will be enabled when debug outputs are enabled in Debug.h.   You can use this to trace the operation of your branch predictor; a debug output file is provided for qsort.mips.   Remember the debug output files are VERY LARGE be sure to delete them and never run with the nqueen.mips input.   Turn off debug outputs before submission.




To redirect your output to a file:

$ ./simulator qsort.mips &gt; temp.txt




Or if you want to see the output on the screen at the same time use:

$ ./simulator qsort.mips | tee temp.txt




The following command will show you just the branch instructions:




$ grep -B1 -A10 ‘BPRED’ temp.txt | more

The grep command extracts all lines containing ‘BPRED’, the -B1 option displays one line before the match and the -A10 option displays 10 lines after the match so you can see the next instruction and tell whether the branch was taken.   10 points extra-credit to the first person who emails me a <u>simple</u> way to remove the register dump from this output.




<strong> </strong>

<strong>           </strong>