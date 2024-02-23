Varad Deouskar - varad@wustl.edu
Peter Rong - l.rong@wustl.edu

# Module Design

Inside the single/lab.c file, we declared two global static unsigned long variables (log_sec, log_nsec). 
We then used these two lines: 
```module_param(log_sec, ulong, 0444)```
```module_param(log_nsec, ulong, 0444)```
To define the two ulong module parameters.

# Timer Design and Evaluation

We first created a static method call "timer_callback" that returns in type enum hrtimer_restart for the timer's expiration. 
Inside the init functon, we called hrtimer_init, and we used "my_hrtimer.function = &timer_callback;" to have the module's timer variable point to the timer expiration function. Lastly, we used "hrtimer_start(&my_hrtimer, interval, HRTIMER_MODE_REL);" to call hrtimer_start
Inside the exit function, we called "hrtimer_cancel(&my_hrtimer);" to cancel the module's timer.

Part of the system log: 
```
Feb 23 15:53:13 peterrpi kernel: [102015.718385] Hey there again
Feb 23 15:53:13 peterrpi kernel: [102015.718444] Iteration 567: Thread Woken Up, Voluntary CS: 567, Involuntary CS: 0
Feb 23 15:53:13 peterrpi kernel: [102015.818385] Hey there again
Feb 23 15:53:13 peterrpi kernel: [102015.818444] Iteration 568: Thread Woken Up, Voluntary CS: 568, Involuntary CS: 0
Feb 23 15:53:13 peterrpi kernel: [102015.918387] Hey there again
Feb 23 15:53:13 peterrpi kernel: [102015.918446] Iteration 569: Thread Woken Up, Voluntary CS: 569, Involuntary CS: 0
Feb 23 15:53:13 peterrpi kernel: [102016.018389] Hey there again
Feb 23 15:53:13 peterrpi kernel: [102016.018448] Iteration 570: Thread Woken Up, Voluntary CS: 570, Involuntary CS: 0
```
As we can see, there's log of thread waking up approximately every 0.1 seconds, which is really close to what we expect it to perform. (off by at most 0.0001 escond)

