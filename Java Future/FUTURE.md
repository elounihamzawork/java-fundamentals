Blocking calls?

- A blocking or long-running call occurs when a thread is tied up for a long period performing computation, or waiting for resources
- During this period, threads, memory, and other resources can not be released for use by other processes.
- Code following the blocking cannot be executed until the blocking completes.
- Blocking is a potential bottleneck. It limits an applications ability to scale.
- For exemple: Database calls, calls to external service(Eg. Rest calls), writing to a file, Long running computations could cause Blocking 