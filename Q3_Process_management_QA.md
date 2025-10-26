1. Explain the concept of process creation in operating systems.

A process is an executing instance of a program. When a process is created, the OS allocates memory, initializes its PCB (Process Control Block), assigns it a PID, sets up execution context (stack, heap, code), and schedules it for CPU time. On UNIX systems, process creation is usually done using fork() and exec().

2. Differentiate between the fork() and exec() system calls.

fork() creates a duplicate of the calling process (child) with its own PID. Both run concurrently.

exec() replaces the current process image with a new program. It doesn’t return if successful.
Used together, fork() + exec() enable multitasking.

3. Write a C program to demonstrate the use of fork() system call.
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();
    if (pid == 0)
        printf("Child process, PID = %d\n", getpid());
    else
        printf("Parent process, PID = %d\n", getpid());
    return 0;
}

4. What is the purpose of the wait() system call?

wait() makes the parent process pause execution until one of its child processes terminates, collecting its exit status and preventing zombie creation.

5. Describe the role of the exec() family of functions.

exec() family (execl, execv, execvp, etc.) replaces the current process image with a new executable. The PID remains the same but code, stack, and data segments are replaced.

6. Write a C program to illustrate execvp().
#include <stdio.h>
#include <unistd.h>

int main() {
    char *args[] = {"ls", "-l", NULL};
    execvp("ls", args);
    perror("execvp failed");
    return 0;
}

7. How does vfork() differ from fork()?

vfork() doesn’t copy the parent’s address space. The child shares the parent’s memory until it calls _exit() or exec(). It’s faster and used to optimize process creation.

8. Discuss getpid() and getppid().

getpid() → returns the current process’s PID.

getppid() → returns its parent’s PID.
They help identify process hierarchy and debugging.

9. Explain process termination in UNIX.

A process terminates using exit(), returning a status to its parent. The OS releases memory and resources, updates PCB, and notifies the parent (if waiting).

10. Write a C program to create a child process using fork() and print its PID.
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();
    if (pid == 0)
        printf("Child PID: %d, Parent PID: %d\n", getpid(), getppid());
    else
        printf("Parent PID: %d\n", getpid());
    return 0;
}

11. Describe process hierarchy in UNIX.

Every process (except init/systemd) has a parent. The hierarchy forms a tree where each parent can have multiple children. The init process is the ancestor of all.

12. What is the purpose of the exit() function?

exit(status) terminates the calling process, flushing open I/O streams and returning a status code to the parent via wait().

13. Explain how execve() works.

execve(path, argv, envp) loads a new executable from disk into the process memory, replaces code/data segments, and starts execution at the entry point.

execve("/bin/ls", argv, envp);

14. Discuss the role of fork() in multitasking.

fork() enables parallel execution by duplicating the process. Each process runs independently, allowing multiple tasks to proceed simultaneously on the CPU.

15. Write a C program to create multiple child processes using fork().
#include <stdio.h>
#include <unistd.h>

int main() {
    for (int i = 0; i < 3; i++) {
        if (fork() == 0) {
            printf("Child %d PID: %d\n", i+1, getpid());
            _exit(0);
        }
    }
    return 0;
}

16. How does exec() replace a process image?

When exec() is invoked, the OS discards the current process memory layout (text, data, stack) and loads a new program, preserving PID and file descriptors.

17. Explain process scheduling.

Scheduling decides which process runs next on the CPU. The OS scheduler manages ready queues and uses policies like FCFS, Round Robin, or Priority scheduling.

18. Describe the role of clone() in process management.

clone() is a Linux-specific call allowing fine control over resource sharing. It can share memory, file descriptors, and signals, used internally for thread creation.

19. Write a C program to create a zombie process.
#include <stdio.h>
#include <unistd.h>

int main() {
    if (fork() == 0)
        _exit(0);
    sleep(30); // Parent sleeps, child becomes zombie
    return 0;
}


Avoidance: Use wait() or signal(SIGCHLD, SIG_IGN).

20. Discuss setuid() and setgid().

setuid() changes a process’s effective user ID, allowing controlled privilege elevation. setgid() does the same for group ID — essential for security and access control.

21. Explain process groups.

A process group is a collection of related processes identified by a PGID. It enables group signal handling (e.g., sending Ctrl+C to all foreground processes).

22. Write a C program to demonstrate waitpid().
#include <sys/wait.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    pid_t pid = fork();
    if (pid == 0)
        _exit(5);
    else {
        int status;
        waitpid(pid, &status, 0);
        printf("Child exited with %d\n", WEXITSTATUS(status));
    }
}

23. Discuss execle().

execle(path, arg0, ..., NULL, envp) allows specifying a custom environment for the new program, unlike execl, which inherits the current one.

24. Describe the purpose of nice().

nice() adjusts a process’s priority (niceness). Lower values mean higher priority; range typically -20 (high) to +19 (low). It influences CPU scheduling fairness.

25. Write a C program to create a daemon process.
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

int main() {
    pid_t pid = fork();
    if (pid > 0) exit(0);
    setsid();
    chdir("/");
    close(0); close(1); close(2);
    while (1) sleep(10);
}


26. Explain the role of getpid() and getppid() in process management.

getpid() returns the PID of the current process, while getppid() returns the parent’s PID. They help in tracking relationships and managing process trees.

27. Difference between fork() and clone().

fork() creates a child with separate memory, while clone() allows shared memory and resources — used for thread creation and containers in Linux.

28. Write a C program to demonstrate system().
#include <stdlib.h>
int main() {
    system("ls -l");
    return 0;
}


system() runs a shell command as a child process.

29. Explain process states.

Typical UNIX states: New, Ready, Running, Waiting (Blocked), Zombie, Terminated — representing lifecycle phases from creation to exit.

30. Purpose of chroot().

chroot() changes the root directory for the process, isolating it from the rest of the filesystem — commonly used for sandboxing or recovery environments.

31. Role of execv().

execv(path, argv) executes a new program specified by path with argument array argv, replacing the current process image.

32. C program to create a process using fork() and pass arguments.
#include <stdio.h>
#include <unistd.h>
int main() {
    char *args[] = {"echo", "Hello, Child!", NULL};
    if (fork() == 0) execvp("echo", args);
}

33. Significance of PIDs.

Each process is uniquely identified by a PID, used for scheduling, signaling, and tracking in the process table.

34. Orphan process.

A process whose parent terminates before it. It is re-parented to init (PID 1), which later cleans it up.

35. Process synchronization using semaphores.
#include <semaphore.h>
#include <pthread.h>


Semaphores control access to shared resources by multiple processes, preventing race conditions.

36. Process priority.

Every process has a scheduling priority affecting CPU time. It can be changed using nice() or setpriority().

37. fork() in COW (Copy-on-Write).

When forked, parent and child share the same pages marked copy-on-write. Memory pages are duplicated only upon modification — optimizing performance.

38. Role of execvp().

execvp() searches the executable in the PATH environment variable and executes it, replacing the current process image.

39. execvpe() demonstration.
#include <unistd.h>
int main() {
    char *env[] = {"PATH=/bin", NULL};
    char *args[] = {"ls", NULL};
    execvpe("ls", args, env);
}

40. Process context switching.

Switching CPU from one process to another involves saving and loading registers, PC, and memory mapping. Excessive switching reduces performance.

41. clone() in thread creation.

clone() is used by pthread_create() internally to create threads sharing the same memory and resources.

42. Process group creation and PGID change.
#include <unistd.h>
#include <stdio.h>
int main() {
    setpgid(0, 0);
    printf("PGID: %d\n", getpgrp());
}

43. fork() vs pthread_create().

fork() creates a new process with a separate memory space; pthread_create() creates a thread within the same address space.

44. execvp() and PATH.

execvp() automatically searches the PATH directories for the executable, simplifying execution of common commands.

45. IPC using shared memory.
#include <sys/ipc.h>
#include <sys/shm.h>
#include <string.h>


Shared memory enables fast data exchange between processes.

46. fork() in shell job control.

The shell uses fork() to spawn jobs and manage foreground/background execution via process groups.

47. execlp() example.
#include <unistd.h>
int main() {
    execlp("ls", "ls", "-l", NULL);
}

48. setpgid() role.

Changes the process group ID, used in job control and signal propagation to related processes.

49. vfork() demonstration.
#include <unistd.h>
#include <stdio.h>
int main() {
    if (vfork() == 0) {
        execlp("ls", "ls", NULL);
        _exit(0);
    }
}

50. Process priority inheritance.

Ensures that lower-priority processes temporarily inherit higher priority to prevent priority inversion in real-time systems.

51. fork() in COW mechanism.

Reduces memory overhead by sharing pages between parent and child until one modifies data — improving efficiency.

52. Pipeline using pipe().
#include <unistd.h>
#include <stdio.h>
int main() {
    int fd[2];
    pipe(fd);
    if (fork() == 0) {
        dup2(fd[1], 1);
        execlp("ls", "ls", NULL);
    } else {
        dup2(fd[0], 0);
        execlp("wc", "wc", "-l", NULL);
    }
}

53. Scheduling policies.

FIFO: Non-preemptive, first-come-first-serve

Round Robin: Time-slice-based

Priority: Based on static or dynamic priorities.

54. execve() significance.

It’s the core system call for executing new binaries — all other exec variants use it internally.

55. nice() demonstration.
#include <unistd.h>
#include <stdio.h>
int main() {
    nice(5);
    while(1);
}

56. Process swapping.

Moving inactive processes from RAM to disk to free memory, improving multitasking efficiency.

57. fork() vs pthread_create() (again).

Processes are independent; threads share memory. Context switching between threads is cheaper.

58. prctl() purpose.

Used to set or get process attributes like dumpability, name, and death signals.

59. clone() to create a thread.
#define _GNU_SOURCE
#include <sched.h>
#include <stdio.h>
#include <stdlib.h>
int thread_func(void *arg) { printf("Thread via clone\n"); return 0; }
int main() {
    char stack[1024*1024];
    clone(thread_func, stack+sizeof(stack), SIGCHLD, NULL);
}

60. Process preemption.

Preemption allows the OS to forcibly switch processes for fairness and responsiveness in multitasking systems.

61. exec() and file descriptors.

By default, open file descriptors are preserved across exec() unless marked with FD_CLOEXEC.

62. fork() + pipes communication.
#include <unistd.h>
#include <stdio.h>
int main() {
    int fd[2]; pipe(fd);
    if (fork() == 0) {
        close(fd[0]);
        write(fd[1], "Hi", 2);
    } else {
        char buf[3];
        close(fd[1]);
        read(fd[0], buf, 2);
        buf[2] = 0;
        printf("%s\n", buf);
    }
}

63. Process priorities and scheduling.

Priorities influence which process gets CPU time — high-priority tasks preempt low ones.

64. Process termination.

Occurs via exit(), signals, or errors. OS deallocates resources and marks it as “terminated.”

65. Exit status.

A child’s exit status can be retrieved using wait() or waitpid(), indicating normal or abnormal termination.

66. fork() + wait() synchronization.

Parent uses wait() to ensure it executes after child completion, preventing zombies.

67. Suspension and resumption.

Signals like SIGSTOP suspend, and SIGCONT resume process execution.

68. Scheduling algorithms.

Determine CPU allocation — e.g., FCFS, SJF, RR, Multilevel Queue. Affect throughput and response time.

69. sched_setscheduler() example.
#include <sched.h>
struct sched_param p = { .sched_priority = 10 };
sched_setscheduler(0, SCHED_RR, &p);

70. Process migration.

Moving processes between CPUs or machines for load balancing in distributed systems.

71. PID uniqueness.

Each PID uniquely identifies a process within a system until it terminates.

72. Shared memory IPC example.
#include <sys/ipc.h>
#include <sys/shm.h>


Shared segments allow communication without kernel intervention.

73. Process tracing.

ptrace() enables debugging and system call tracing (used by gdb, strace).

74. COW and memory usage.

COW reduces physical memory duplication; modifications create private copies per process.

75. Process Control Block (PCB).

PCB stores process state, registers, PID, scheduling info, and memory mappings — the OS uses it to manage processes.

76. prctl() example.
#include <sys/prctl.h>
prctl(PR_SET_NAME, "myproc", 0, 0, 0);

77. Scheduling classes.

Define priority domains like normal, real-time (SCHED_FIFO, SCHED_RR), and idle.

78. vfork() role.

Used for performance-optimized process creation before exec — shares memory.

79. sigaction().

Defines signal handlers with fine-grained control compared to signal().

80. fork() + semaphores synchronization.
#include <semaphore.h>


Semaphores coordinate shared resource access between parent and child.

81. Process migration in distributed computing.

Processes can be checkpointed and resumed on another node for load balancing or fault tolerance.

82. Message queue IPC.
#include <sys/msg.h>


Allows structured message passing between processes.

83. sigprocmask().

Blocks or unblocks signals by manipulating the signal mask.

84. setrlimit().

Sets resource limits like max CPU time, memory, or open files per process.

85. Process groups and job control.

Enable collective signal handling and management of related processes.

86. Condition variables.

Used for synchronization when processes wait for certain conditions.

87. prlimit().

Combines getrlimit() and setrlimit() in one call to manage resource limits.

88. Scheduling on multi-core systems.

Schedulers distribute threads among cores to maximize parallelism and minimize latency.

89. setpriority().

Modifies the nice value dynamically to adjust scheduling priority.

90. Socket IPC.

Processes communicate over network sockets using socketpair() or TCP/IP APIs.

91. Scheduling latency.

Time delay between when a process becomes runnable and when it actually runs — affects real-time performance.

92. fork() + mutex synchronization.

Mutexes protect shared data sections in concurrent execution.

93. prlimit64().

Extended version of prlimit() supporting 64-bit resource limits.

94. sched_getaffinity().

Retrieves which CPUs a process can execute on.

95. Process checkpointing.

Saving process state to resume later, used in fault-tolerant or distributed systems.

96. Named pipes (FIFOs).
#include <sys/stat.h>
mkfifo("fifo", 0666);


Enable communication between unrelated processes.

97. /proc filesystem.

A virtual filesystem exposing process and kernel information via /proc/<pid> directories.

98. sched_setaffinity().

Binds a process to specific CPU cores for performance optimization.

99. Process re-parenting.

Occurs when a parent terminates — orphaned children are re-parented to init or systemd.

100. Shared memory + semaphores IPC.

Combines shared memory for data and semaphores for synchronization.

101. Process vs Program.

A program is passive code; a process is that code in execution with state and resources.

102. PCB contents.

PID, state, CPU registers, scheduling info, memory mapping, open files, signals, and accounting data.

103. Memory segments.

Code (text), Data (global/static), Heap (dynamic), Stack (function calls), and BSS (uninitialized).

104. What happens on fork().

Memory and registers are duplicated (COW), PID assigned, both processes resume from fork().

105. ps vs top.

ps shows a snapshot of current processes; top continuously updates and displays real-time process stats.

106. Types of processes.

Foreground, background, daemon, zombie, orphan — based on execution mode and control.

107. PPID of orphan.

Reassigned to init (PID 1).

108. exit() vs return.

return exits only the current function; exit() terminates the entire process.

109. Access child’s exit code.

Parent uses wait() or waitpid() to retrieve child’s exit status.

110. Zombie vs Orphan.

Zombie = child finished, parent hasn’t waited.
Orphan = parent terminated before child.

111. Use of fork().

Used to create new processes for multitasking.

112. Blocking system calls.

Examples: read(), wait(), accept() — block until completion.

113. Why not execute from disk?

Programs must be loaded into RAM for CPU access — CPU can’t directly access disk.

114. Copy data from RAM to CPU.

Via CPU instructions that load data into registers (e.g., MOV).

115. Copy data from CPU to RAM.

Using store instructions — e.g., MOV to memory address.

116. Paging technique.

Divides memory into fixed-size pages mapped to frames; enables virtual memory.

117. Why pages not fixed.

Page count depends on process size and system page size — varies dynamically.

118. Shared memory.

Allows multiple processes to access the same memory segment — fastest IPC.

119. Multiple process data sharing.

Via shared memory, pipes, message queues, or sockets.

120. Shared frames.

Multiple processes can map the same physical frame using shared memory mechanisms.

121. Child memory usage.

Initially shares parent memory (COW). Only changed pages are copied.

122. Instruction tracking.

Each process has its own Program Counter (PC) stored in its PCB.

123. Different code blocks after fork().

Conditional logic on fork() return value decides which code executes.

124. Write on shared pages.

Triggers page fault — OS copies the page for that process (COW).

125. Address bus address.

CPU places physical memory address on address bus to access data.

126. Virtual address generation.

Generated by CPU during program execution before MMU translation.

127. Physical address generation.

MMU translates virtual addresses into physical ones using page tables.

128. exec() family and need.

Used to execute new programs from current process context. Includes execl, execv, execlp, execvp, execve, etc.

129. Why system calls return errors.

System calls interact with hardware; many fail due to permission, resource, or I/O issues. Errors return -1 and set errno.

130. Execl() vs Execv().

execl() uses variable arguments; execv() takes an array of arguments.
//
131. Execlp() vs Execvp().

Both search PATH; execlp() uses variable args, execvp() uses array form.
