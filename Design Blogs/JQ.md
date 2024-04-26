Recently we were facing a problem as following:

* There was a huge and complex json file that contained uid as a field (there were around 25000 uids)
* In a separate folder, there were those 25000 uuid.json files which needed to be parsed, a particular field had to be extracted and a consolidated report had to be generated.

We tried normal JS, with the obvious doubt that it might throw OOM error, and guess what, it did!

We tried different ways, by batching them, by not storing anything in-memory and writing them to a file, but nothing worked.

Then i came across a tool, JQ (https://jqlang.github.io/jq/). 
JQ is high performance JSON parser written in C for cmd line. It has a complex syntax, but it makes up for it with its performance.

So we thought we finally found the solution. We wrote the jq syntax for the problem as follows:
* Extracted each uids and stored in a separate file
* Went through each of those files and extracted the required field

But it wasn't enough, we found that doing all these parsing, going to each filepaths, reading them and extracting the field was taking too long (more than 2 hours, maybe the jq code we wrote wasn't as performant).

We were stuck again. We started thinking of other approaches, but then we thought why not make use of the multiple cores of the cpu.

So, we finally decided to try the following:
* Use the same jq code as we did before for storing and parsing
* Use the GNU parallel package for multi core performance (we used 4 cores)

Now, all those parsing and extracting the fields of those 25000 huge json took only around 300 seconds.

I am still fascinated by the seer optimization this parallel execution of jq did. 
All the deadlocks, and consolidation were handled by the GNU Parallel package, we only had to think about the jq command.

Cheers to the power of parallelism and the endless possibilities it unlocks!