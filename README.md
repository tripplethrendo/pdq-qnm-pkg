# PDQ: Pretty Damn Quick 
## Major release 7.0.0

This is a **development** repository. 
The current release, **PDQ 6.2.0**, has its 
own [repository](http://www.perfdynamics.com/Tools/PDQcode.html) with separate download and installation instructions.


PDQ is a library of functions for solving queueing-network models of 
systems of resources such as, distributed computer systems, manufacturing systems, 
and packet networks. 
PDQ models can be written optionally in a variety of conventional programming languages. 
The book [Analyzing Computer System Performance with Perl::PDQ](http://www.perfdynamics.com/iBook/ppa_new.html) 
presents example applications for computer system performance analysis written in Perl. 
Another example below is written in Python. 

**Overview:**	[What is PDQ?](http://www.perfdynamics.com/Tools/PDQ.html)

**Languages:**	
This release only supports the [C](https://en.wikibooks.org/wiki/C_Programming) language 
(the native language of the PDQ library)  and the very popular 
[R](http://www.perfdynamics.com/Tools/PDQ-R.html) statistical language. 
Example PDQ models in R can be found in the `pdq-qnm-pkg/interfaces/R/pdq/demo/` directory.

The migration of [Python from 2 to 3](https://wiki.python.org/moin/Python2orPython3) 
has introduced maintenance complications for PDQ. 
Python 3 may eventually be accommodated in future PDQ releases.

[Perl](http://www.perfdynamics.com/Tools/PDQperl.html) maintenance has officially ended with 
PDQ release 6.2.0 in order to remain compatible with the 2nd edition of the 
[Perl PDQ book](http://www.perfdynamics.com/iBook/ppa_new.html) (2011).

**Platforms:**	Linux, MacOS, Windows

**Maintainers:** Neil Gunther and Paul Puglia

**Contributors:** Denny Chen, Phil Feller, Neil Gunther, Peter Harding, Paul Puglia, Sam Zallocco

**License:** PDQ is distributed as OSS under the [MIT license](https://en.wikipedia.org/wiki/MIT_License#License_terms).

**Synopsis:** [PDQ library functions](http://www.perfdynamics.com/Tools/PDQman.html)

**Example:** 

The following PDQ code, written in C, is a  model of an AWS cloud application 
that uses the new `CreateMultiserverClosed` function. You can read the background 
behind this work in the paper entitled 
[Linux-Tomcat Application Performance on Amazon AWS](https://arxiv.org/abs/1811.12341). 

```C
#include <string.h> 
#include <stdio.h> 
#include <stdlib.h> 
#include <math.h>
#include "PDQ_Lib.h"  

int main(void) {

	int      requests = 400;
	int      threads  = 300;
	float    stime    = 0.444;
	float    think    = 0.0;

	PDQ_Init("AWS-Tomcat Cloud Model");  
	PDQ_CreateClosed("Requests", TERM, requests, think);

	PDQ_CreateMultiserverClosed(threads, "Threads", MSC, FCFS); 

	PDQ_SetDemand("Threads", "Requests", stime); 
	PDQ_SetWUnit("Reqs");
	PDQ_SetTUnit("Sec");
	PDQ_Solve(EXACT); // can't use APPROX method
	PDQ_Report();
}
```

In this model, the Tomcat threads on AWS play the role of queueing servers.

```
                        PRETTY DAMN QUICK REPORT         
               ==========================================
               ***  on   Wed Nov 18 11:43:29 2020     ***
               ***  for  AWS-Tomcat Cloud Model       ***
               ***  PDQ  Version 7.0.0 Build 111820   ***
               ==========================================

               ==========================================
               ********    PDQ Model INPUTS      ********
               ==========================================

Queueing Network Parameters

Node Sched Resource   Workload   Class    Service time
---- ----- --------   --------   -----    ------------
MSC  FCFS  Threads    Requests   TERM         0.444000

Network type:   CLOSED
Workload streams:    1
Queueing nodes:      1


Workload      Users        Demand      Thinktime
--------      -----        ------      ---------
Requests     400.00        0.4440           0.00


               ==========================================
               ********   PDQ Model OUTPUTS      ********
               ==========================================

Solution method: EXACT

               ********   SYSTEM Performance     ********

Metric                   Value      Unit
------                  -------     ----
Workload: "Requests"
Mean concurrency        400.0000    Reqs
Mean throughput         675.6757    Reqs/Sec
Response time             0.5920    Sec
Round trip time           0.5920    Sec
Stretch factor            1.3333

Bounds Analysis:
Max throughput          675.6757    Reqs/Sec
Min response              0.4440    Sec
Max demand                0.0015    Sec
Total demand              0.4440    Sec
Think time                0.0000    Sec
Optimal load            300.0000    Reqs


               ********   RESOURCE Performance   ********

Metric          Resource     Work               Value    Unit
------          --------     ----              -------   ----
Capacity        Threads      Requests              300   Servers
Throughput      Threads      Requests         675.6757   Reqs/Sec
In service      Threads      Requests         300.0000   Reqs
Utilization     Threads      Requests         100.0000   Percent
Queue length    Threads      Requests         400.0000   Reqs
Waiting line    Threads      Requests         100.0000   Reqs
Waiting time    Threads      Requests           0.1480   Sec
Residence time  Threads      Requests           0.5920   Sec
```

