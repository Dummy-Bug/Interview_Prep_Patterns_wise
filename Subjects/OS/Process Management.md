# @ Process Management

Note: Kernel is the first process to be created, is the ancestor of all other processes and is at the root of the process tree.

<br>

### 1. Zombie process
A zombie process is a process that has terminated but its PCB still exists because its parent has not yet accepted its return value.

<br>

### 2. Difference between process and thread
1. processes are typically independent while threads exist as parts of a process
2. processes carry considerably more state information than threads, whereas multiple threads within a process share process state as well as memory and other resources
3. processes have separate address spaces, whereas threads share their address space
4. processes interact only through system-provided inter-process communication mechanisms
5. context switching between threads in the same process is typically faster than context switching between processes

<br>

### 3. Dispatcher
A dispatcher is the module of the operating system that gives control of the CPU to the process selected by the CPU scheduler. Dispatch latency is the time taken to stop a process and start another. Dispatch latency is a pure overhead.

<br>

### 4. Priority inversion
A low-priority process gets the priority of a high-priority process waiting for it.

<br>

### 5. Scheduling Chart

![img](https://github.com/naman14310/Interview_Prep/blob/main/Subjects/OS/scheduling%20chart%20os.png)

<br>

### 6. Independent vs Co-operative process
An independent process is not affected by the execution of other processes while a co-operating process can be affected by other executing processes.

<br>



## @ Process Synchronisation

**Race condition** is a situation where several processes access and manipulate the same data concurrently and the outcome of the execution depends on the particular order in which the accesses take place. To avoid such situations, it must be ensured that only one process can manipulate the data at a
given time. This can be done by process synchronization.

<br>

### 1. Critical Section
1. Each process has a section of code, called the critical section, in which the process access shared resources, changes common variables and files.
2. The problem is to ensure that when one process is executing in its critical section then no other process can execute its own critical section
3. The critical section is preceded by an *entry section* in which a process seeks permission from other processes. The critical section is followed by an *exit section.*

<br>

### 2. Condition for Synchronisation mechanisms
A solution to the critical section problem must satisfy the following properties:
1. Mutual exclusion
2. Progress 
3. Bounded waiting
4. Architectural Neutrality

<br>

### 3. Semaphores
A semaphore is an integer variable that, apart from initialization, is accessed only through two atomic operations called wait() and signal().

```cpp
wait() is used for acquiring lock

wait(S){
    while(S<=0);    // --> Busy Waiting
    S--;
}


signal() is used for releasing lock

signal(S){
    S++;
}
```

<br>

### 4. Binary Semaphores (Mutexes)
It is used to implement solution of critical section problem with multiple processes. Initialized to 1.

```cpp
struct semaphore {
    enum value(0, 1);                 // --> Since it is Binary Semaphore, it'll only contains 0 or 1
  
    /* q contains all Process Control Blocks (PCBs) corresponding to processes got blocked while performing wait() operation */
    
    queue<process> q;
}; 


wait(semaphore s){
    if (s.value == 1) {
        s.value = 0;
    }
    else {
        q.push(P);                  // --> add the process to the waiting queue
        sleep();
    }
}


signal(Semaphore s){
    if (s.q is empty()) {
        s.value = 1;
    }
    else {  
        Process p=q.pop();         // --> select a process from waiting queue and give entry to critical section
        wakeup(p);
    }
}
```

<br>

### 5. Counting semaphore
It is used to control access to a resource that has multiple instances. Initialized to n, where n is the number of resources.

```cpp
struct Semaphore {
    int value;                    // --> Since it is counting semaphore, It will contain any integer val depends on number of resources 
    Queue<process> q;
};


wait(Semaphore s){
    s.value = s.value - 1;
    if (s.value < 0) {
        q.push(p);               // --> add process to queue here p is a process which is currently executing
        block();
    }
    else
        return;
}
  
  
signal(Semaphore s){
    s.value = s.value + 1;
    if (s.value <= 0) {
        Process p=q.pop();      // --> remove process p from queue
        wakeup(p);
    }
    else
        return;
}
```

<br>

### 6. Do Semaphore suffers from deadlock
Semaphores may lead to indefinite wait (starvation) and deadlocks.

```cpp
Example of two prcoess, where use of semaphores leads to deadlock

   P0                            P1
   
 wait (S);                    wait (Q);
 wait (Q);                    wait (S);
  ...                           ...
signal (S);                  signal (Q);
signal (Q);                  signal (S);
```

<br>

### 7. Inter Process Communication (IPC)
Inter-process communication (IPC) is a mechanism that allows processes to communicate with each other and synchronize their actions. rocesses can communicate with each other through:
1. Shared Memory
2. Message passing

![img](https://forns.lmu.build/assets/images/spring-2018/cmsi-387/week-8/ipc-1.png)

**Shared Memory Method**

Processes can use shared memory for extracting information as a record from another process as well as for delivering any specific information to other processes. Eg: Producer-Consumer problem 

<br>

**Message Passing Method**

If two processes p1 and p2 want to communicate with each other, they proceed as follows:
1. Establish a communication link (if a link already exists, no need to establish it again.)
2. Start exchanging messages using basic primitives.

We need at least two primitives: 

– send(message, destination) or send(message) 

– receive(message, host) or receive(message)

[Detailed Explaination](https://www.geeksforgeeks.org/inter-process-communication-ipc/)

<br>

### 8. Producer Consumer Problem (Bounded Buffer)
We have a buffer of fixed size. A producer can produce an item and can place in the buffer. A consumer can pick items and can consume them. We need to ensure that when a producer is placing an item in the buffer, then at the same time consumer should not consume any item. In this problem, buffer is the critical section. 

To solve this problem, we need two counting semaphores – Full and Empty. “Full” keeps track of number of items in the buffer at any given time and “Empty” keeps track of number of unoccupied slots. 

```cpp
Initialization of semaphores:

mutex = 1; 
Full = 0;               // --> Initially, all slots are empty. Thus full slots are 0 
Empty = n;              // --> All slots are empty initially 

/* --------------------------------------------------------------------------------------------------*/

Solution for Producer:

do {
    produce_item();

    wait(empty);
    wait(mutex);

    place_in_buffer();

    signal(mutex);
    signal(full);

} while(true)

/* --------------------------------------------------------------------------------------------------*/

Solution for Consumer:

do {

    wait(full);
    wait(mutex);

    remove_item_from_buffer();

    signal(mutex);
    signal(empty);

    consume_item();

} while(true)
```

<br>

### 9. Reader Writer Problem
Consider a situation where we have a file shared between many people. Then,
1. If one of the people tries editing the file, no other person should be reading or writing at the same time.
2. However if some person is reading the file, then others may read it at the same time.

<br>

**Solution when Reader has the Priority over Writer**

Here priority means, no reader should wait if the share is currently opened for reading. Three variables are used: mutex, wrt, readcnt to implement solution.


```cpp
/*
    mutex   : used to ensure mutual exclusion when readcnt is updated i.e. when any reader enters or exit from the critical section.
    wrt     : used to restrict access of writers if atleast 1 reader is present.
    readcnt : tells the number of processes performing read in the critical section, initially 0.
*/

semaphore mutex, wrt;           
int readcnt;  

/* ---------------------------------------------------------------------------------------*/

Writer process:

do {
    wait(wrt);                      // --> writer requests for critical section
    write();
    signal(wrt);                    // --> leaves the critical section

} while(true);

/* ---------------------------------------------------------------------------------------*/

Reader process:

do {
   wait(mutex);         // --> Reader wants to enter the critical section (mutex is used to ensure mutual exclusion on readcnt)
   
   readcnt++;           // --> The number of readers has now increased by 1                   

   /*   
        If there is atleast one reader in the critical section
        we will ensure that no writer can enter..
        thus we give preference to readers here
   */
   
   if (readcnt==1)     
      wait(wrt);                    

   signal(mutex);       // --> other readers can enter while this current reader is inside the critical section (hence release mutex) 


   reading();
   
   
   wait(mutex);         // --> a reader wants to leave, again we will apply acquire lock on mutex before changing readcnt value

   readcnt--;

   /* 
        If no reader is left in the critical section,
        release wrt lock so that writer can perform their actions
   */
    
   if (readcnt == 0) 
       signal(wrt);         

   signal(mutex);       // --> reader leaves and releasing mutex lock

} while(true);

```

<br>

### 10. Dinning Philosopher Problem
The Dining Philosopher Problem states that K philosophers seated around a circular table with one chopstick between each pair of philosophers. A philosopher may eat if he can pick up the two chopsticks adjacent to him. One chopstick may be picked up by any one of its adjacent followers but not both. 

```cpp
Semaphores:

chopsticks[5];      // --> all semaphores initialized to 1

Pi:

do { 
    wait (chopstick[i]);            // --> acquire lock on left chopstick
    wait (chopStick[(i+1)%5]);      // --> acquire lock on right chopstick
    
    eat()
    
    signal (chopstick[i]);          // --> release lock for left
    signal (chopstick[(i+1)%5]);    // --> release lock for right
    
} while (true);
```

Above system may suffer from deadlock if all philosopher pick there left chopstick at same instant. 

**Solution for Deadlock:** We make (n-1) philospher to pick left chopstick first and we will reverse the order for 1 philosopher so he will pick his right chopstick first.
