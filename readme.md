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
Feb 23 15:53:13 peterrpi kernel: [102015.818385] Hey there again
Feb 23 15:53:13 peterrpi kernel: [102015.918387] Hey there again
Feb 23 15:53:13 peterrpi kernel: [102016.018389] Hey there again
```
As we can see, there's log of thread waking up approximately every 0.1 seconds, which is really close to what we expect it to perform. (off by at most 0.0001 escond)

# Thread Design and Evaluation

Inside the thread function, we have a printk call that logs the iteration, voluntary and involuntary cs. Right after that, we have ```set_current_state(TASK_INTERRUPTIBLE)``` and calls ```schedule()```. We have a while loop wrapped around these to check if there's ```kthread_should_stop``` flag being true.

Part of system log for 1 second interval:
```
Feb 23 16:25:06 peterrpi kernel: [103929.096321] Iteration 2: Thread Woken Up, Voluntary CS: 2, Involuntary CS: 0
Feb 23 16:25:07 peterrpi kernel: [103930.096392] Iteration 3: Thread Woken Up, Voluntary CS: 3, Involuntary CS: 0
Feb 23 16:25:08 peterrpi kernel: [103931.096365] Iteration 4: Thread Woken Up, Voluntary CS: 4, Involuntary CS: 0
```

Part of system log for 1,000,000 nanosecond (1 millisecond) interval:
```
Feb 23 16:33:48 peterrpi kernel: [  154.804145] Iteration 8568: Thread Woken Up, Voluntary CS: 8568, Involuntary CS: 4
Feb 23 16:33:48 peterrpi kernel: [  154.805145] Iteration 8569: Thread Woken Up, Voluntary CS: 8569, Involuntary CS: 4
Feb 23 16:33:48 peterrpi kernel: [  154.806146] Iteration 8570: Thread Woken Up, Voluntary CS: 8570, Involuntary CS: 4
Feb 23 16:33:48 peterrpi kernel: [  154.807146] Iteration 8571: Thread Woken Up, Voluntary CS: 8571, Involuntary CS: 4
Feb 23 16:33:48 peterrpi kernel: [  154.808146] Iteration 8572: Thread Woken Up, Voluntary CS: 8572, Involuntary CS: 4
```

1) The time interval varies a bit more for using 1 millisecond than using 1 second as parameter.
2) TODO

