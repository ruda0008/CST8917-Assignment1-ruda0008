# Name: Aryan Rudani
# Student Number: 041171391
# Course Code: CST8917
# Assignment no: 1
# Date: February 13, 2026


## Part 1: Paper Summary

- The 2019 paper by Hellerstein et al. presents a nuanced critique of serverless computing, particularly Functions-as-a-Service (FaaS) platforms, naming  it as `One Step Forward, Two Steps Back.` The thesis claims that while serverless advances cloud with automatic scaling and paying only for the time resources it also takes us backwards when it comes to handling data intensive and distributed applications efficiently.
 The "one step forward" is part straightforward by these. The author means that once  Code is written, deployed, the platform handles everything else. The pay per use model is great for applications that don't run constantly. The two steps back the authors argue that there are several problems related to performance at hinders innovation in areas like machine, learning are big data analytics.

- The authors identify several key limitations in first-generation FaaS, exemplified by AWS Lambda. **First,** Execution time constraints impose strict timeouts, such as Lambda's 15-minute limit, which enforces statelessness and prevents long-running computations. For instance, training a machine learning model on large datasets requires sequential invocations, inflating latency and costs compared to virtual machines. **Second,** communication between functions is surprisingly slow. Functions had to go to shared storage like as free, creating a bottleneck. Resulting in bandwidth as low as 28.7 Mbps under concurrency. **Third,** Limited hardware access restricts functions to basic CPUs and RAM (up to 3GB), excluding GPUs or specialized accelerators essential for AI and scientific computing, forcing costly workarounds or infeasible implementations.

- Looking ahead, the authors propose what they think the future of cloud programming should look like. They want flexible code and data placement using declarative languages like SQL with user defined functions so the system can automatically optimize where to run code and where to keep data. They want support for different types of hardware, including GPUs and accelerators, with the platform automatically matching applications to the right resources

## Part 2: Azure Durable Functions Deep Dive
- Orchestration Model
  -  Azure Durable Functions adds to basic FaaS with orchestrators, activities, and clients. Orchestrators control the flow, calling activities async and handling loops or choices, with auto-saves at waits. Clients start it. Unlike plain Azure Functions (just isolated triggers), this avoids manual setups like queues. It fixes the paper's distributed weakness by supporting sequences or parallels without state hassle. Code must be repeatable for replays

- State Management
  -  It uses event sourcing, saves, and replays to keep state, storing history in Azure Storage for restarts even if instances stop. Saves at waits, replays rebuild from events. This solves the paper's stateless complaint, where you'd use outside storage. For long flows, no lost progress, like ongoing loops. Rare issues like reruns on crashes, and some languages miss features.

- Execution Timeouts
  - Orchestrators skip normal timeouts (5-10 minutes) as waits don't count – they pause forever with timers/events, resume from saves. Allows day-long runs, fixing paper's limit issues that kill long tasks. Activities keep timeouts (up to 10 minutes on Consumption) to stop hangs, but chainable. Useful for monitors, but watch costs. Since 2019, eases extended work without tricks. 

- Communication between Functions
  - Orchestrators call activities direct with methods like CallActivityAsync, sharing data in-memory, no extras. For outsides, WaitForExternalEvent uses storage but not internals. Reduces paper's slow storage problem, cutting waits in flows like chains. Beats basic FaaS queues, but some delays for externals, not full peer. Improved since 2019 for coords. 

- Parallel Execution (Fan-Out/Fan-In)
  - Fan-out starts many activities at once in loops with CallActivityAsync, fan-in collects with Task.WhenAll, saving progress. Scales for batches, helps paper's distributed concerns by skipping storage-only coords. Simpler than basic FaaS queues. Limits: replays on fails, costs for big, no deep consensus. Grown since 2019 for parallel state.

## Part 3: Critical Evaluation
- Durable Functions doesn't solve all paper points. Hardware stays limited no GPUs or specials, like regular Functions. Orchestrate to others, but for heavy like AI, need elsewhere; serverless favors basic for scale. Limits innovation types. Data shipping lingers coords help, but pull from Blobs is slow/costly for big in parallels.  

- I think Durable Functions moves toward authors vision but mostly patches issues, not solves deep. It handles state and longs well with sourcing and flows, like agents and placement fan-out aids distribution, no timeouts.


## References

- Hellerstein, J. M., et al. (2019). Serverless Computing: One Step Forward, Two Steps Back. CIDR. https://www.cidrdb.org/cidr2019/papers/p119-hellerstein-cidr19.pdf

- Azure Functions vs. Azure Durable Functions. Medium. https://medium.com/@gema.correa/azure-functions-vs-azure-durable-functions-fce473fc6d38

- Durable Functions Overview. Microsoft Learn. https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview?tabs=csharp 


## AI Disclosure
 - I used Grok AI to check paper and Azure info, summarize and explain. Rewrote myself and used Grok to polish the tone and structure. Fact checked for accuracy.