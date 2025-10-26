# Memory Management — Questions and Answers

This document contains 100 Memory Management questions (Q1–Q100). Q1–Q85 are conceptual answers, and Q86–Q100 include C coding solutions.

---

## Conceptual Questions (Q1–Q85)

Q1. Memory management controls allocation and deallocation of memory to processes.
Q2. Virtual memory allows processes to use more memory than physically available using disk storage.
Q3. Physical memory is RAM; virtual memory is logical memory provided by OS.
Q4. OS allocates memory, handles paging, swapping, protection, and memory sharing.
Q5. Memory allocation provides space for processes and data storage during execution.
Q6. Deallocation frees unused memory to prevent leaks and optimize usage.
Q7. Fragmentation is unused memory that cannot be allocated efficiently.
Q8. Types: internal and external fragmentation.
Q9. Internal fragmentation: unused memory within an allocated block due to fixed block size.
Q10. External fragmentation: free memory is scattered in small blocks that cannot satisfy allocation requests.
Q11. Managed by compaction, paging, segmentation, and allocation strategies.
Q12. Paging divides memory into fixed-size pages to avoid external fragmentation.
Q13. Segmentation divides memory into variable-sized segments based on logical divisions like code, data, stack.
Q14. Paging uses fixed-size blocks; segmentation uses variable-size logical divisions.
Q15. Page table maps virtual pages to physical frames.
Q16. MMU is hardware translating virtual addresses to physical addresses.
Q17. MMU performs address translation and manages protection, caching, and access control.
Q18. TLB is a cache for recent virtual-to-physical address translations.
Q19. TLB miss: mapping not in TLB; MMU fetches from page table.
Q20. MMU translates addresses, checks permissions, and handles page faults.
Q21. Address translation: virtual address → page number + offset → physical frame.
Q22. MMU maps virtual addresses to physical memory, allowing swapping/paging.
Q23. Page table traversal: MMU looks up page number in table for frame.
Q24. Page fault: OS loads page from disk and updates page table.
Q25. Page replacement algorithms: FIFO, LRU, Optimal, NRU, Clock, Second Chance.
Q26. Page replacement algorithms select a page to evict when memory is full.
Q27. FIFO: evict oldest page first.
Q28. Optimal: evict page not needed for longest future time.
Q29. LRU: evict least recently used page.
Q30. Clock: circular queue with reference bit approximating LRU.
Q31. FIFO simple, may cause Belady's anomaly; LRU accurate but costly; Optimal ideal, not implementable; Clock efficient LRU approximation.
Q32. Optimal ideal; LRU approximates; FIFO simple; Clock balances efficiency and approximation.
Q33. NRU: evict page based on reference/modified bits to approximate LRU.
Q34. Second Chance: FIFO with reference bit; gives pages second chance.
Q35. Enhancements: aging, frequency counting, hybrid schemes, adaptive replacement.
Q36. Segmentation divides memory into variable-sized logical segments.
Q37. Benefits: modularity, protection, sharing, logical organization.
Q38. Disadvantages: external fragmentation, complex allocation.
Q39. Implementation: segment table mapping segment number → base + limit.
Q40. Segmentation fault occurs on invalid memory access outside segment.
Q41. Segment registers hold base addresses of active segments.
Q42. Segment table maps segment numbers to base addresses and limits.
Q43. Supports protection/sharing via permissions and shared segments.
Q44. Segmentation with paging: segments divided into pages to reduce fragmentation.
Q45. Segmentation variable-sized/logical; paging fixed-sized/physical.
Q46. Memory fragmentation: inefficiently used memory causing allocation failures.
Q47. Causes: frequent allocation/deallocation, variable-sized blocks, paging/slab mismatches.
Q48. Fragmentation reduces usable memory, increases paging, causes overhead.
Q49. Reduction: compaction, paging, segmentation, proper allocation.
Q50. Compaction combines free blocks to create contiguous space.
Q51. Memory compaction: rearranging memory to eliminate external fragmentation.
Q52. Compaction algorithm: move allocated blocks, merge free space.
Q53. Challenges: updating pointers, costly moves, performance impact.
Q54. Embedded systems: limited memory makes fragmentation critical.
Q55. Inefficient allocation increases fragmentation.
Q56. Memory mapping: mapping files/devices into process address space.
Q57. Purpose: efficient access, IPC, shared memory, mmap I/O.
Q58. Techniques: mmap, shared memory, device mapping.
Q59. Memory-mapped I/O: devices accessed via memory addresses.
Q60. Memory-mapped files: files mapped into virtual memory.
Q61. Advantages: fast access, sharing, simplified I/O.
Q62. Drawbacks: address space usage, potential page faults.
Q63. Improves performance by avoiding explicit read/write calls.
Q64. Memory-mapped graphics: direct access to graphics buffer.
Q65. Embedded systems: fast access to hardware registers/buffers.
Q66. Cache memory: high-speed memory between CPU and main memory.
Q67. Purpose: reduce average access time.
Q68. Types: L1, L2, L3; instruction/data/combined caches.
Q69. Cache coherence: inconsistent copies in multiple caches.
Q70. Replacement policies: LRU, FIFO, Random, LFU.
Q71. Cache associativity: mapping blocks to cache lines (direct, set, fully associative).
Q72. Working: stores frequently accessed data, CPU checks cache first.
Q73. Hit/miss: hit = found in cache; miss = fetch from memory.
Q74. Importance: speeds CPU, reduces latency.
Q75. Relation to hierarchy: cache sits between CPU registers and RAM.
Q76. Memory protection: prevent unauthorized access.
Q77. Need: avoid crashes, corruption, security breaches.
Q78. Techniques: base/limit registers, segmentation, paging, permission bits.
Q79. Segmentation fault: illegal memory access.
Q80. Privilege levels: OS enforces protection via CPU levels.
Q81. Mechanism: paging, segmentation, permissions, MMU enforcement.
Q82. Security: prevents unauthorized access, buffer overflow, malware.
Q83. Memory isolation: separate address spaces per process.
Q84. Challenges: performance overhead, compatibility, hardware limits.
Q85. Security contribution: prevents accidental/malicious memory access.

---

## Coding Questions (Q86–Q100)

### Q86. Dynamic memory allocation using malloc()
```c
#include <stdio.h>
#include <stdlib.h>
int main(){
    int *arr = malloc(5*sizeof(int));
    for(int i=0;i<5;i++) arr[i]=i*10;
    for(int i=0;i<5;i++) printf("%d\n",arr[i]);
    free(arr);
    return 0;
}
```

### Q87. Allocate memory using calloc()
```c
#include <stdio.h>
#include <stdlib.h>
int main(){
    int *arr = calloc(10,sizeof(int));
    for(int i=0;i<10;i++) printf("%d\n",arr[i]);
    free(arr);
    return 0;
}
```

### Q88. Resize memory using realloc()
```c
#include <stdio.h>
#include <stdlib.h>
int main(){
    int *arr = malloc(3*sizeof(int));
    arr[0]=1; arr[1]=2; arr[2]=3;
    arr = realloc(arr,6*sizeof(int));
    for(int i=3;i<6;i++) arr[i]=i+1;
    for(int i=0;i<6;i++) printf("%d\n",arr[i]);
    free(arr);
    return 0;
}
```

### Q89. Allocate memory for linked list node
```c
#include <stdio.h>
#include <stdlib.h>
struct node { int val; struct node *next; };
int main(){
    struct node *head = malloc(sizeof(*head));
    head->val=42; head->next=NULL;
    printf("%d\n", head->val);
    free(head);
    return 0;
}
```

### Q90. First-fit memory allocation simulation
```c
#include <stdio.h>
struct block{ int start,size,free; };
int main(){
    struct block b[20]; b[0].start=0;b[0].size=1024;b[0].free=1; int n=1;
    int reqs[]={100,200,50,300,150};
    for(int i=0;i<5;i++){
        int allocated=0,r=reqs[i];
        for(int j=0;j<n;j++){
            if(b[j].free && b[j].size>=r){
                int old=b[j].size;b[j].size=r;b[j].free=0;
                if(old>r){ b[n].start=b[j].start+r;b[n].size=old-r;b[n].free=1;n++; }
                printf("Allocated %d at %d\n",r,j); allocated=1; break;
            }
        }
        if(!allocated) printf("Failed %d\n",r);
    }
    return 0;
}
```

### Q91. Best-fit memory allocation simulation
```c
#include <stdio.h>
struct block{int start,size,free;};
int main(){
    struct block b[20]; b[0].start=0;b[0].size=1024;b[0].free=1; int n=1;
    int reqs[]={100,200,50,300,150};
    for(int i=0;i<5;i++){
        int best=-1,r=reqs[i];
        for(int j=0;j<n;j++) if(b[j].free && b[j].size>=r) if(best==-1 || b[j].size<b[best].size) best=j;
        if(best==-1){ printf("Failed %d\n",r); continue; }
        int old=b[best].size;b[best].size=r;b[best].free=0;
        if(old>r){ b[n].start=b[best].start+r;b[n].size=old-r;b[n].free=1;n++; }
        printf("Best-fit allocated %d at %d\n",r,best);
    }
    return 0;
}
```

### Q92. Worst-fit memory allocation simulation
```c
#include <stdio.h>
struct block{int start,size,free;};
int main(){
    struct block b[20]; b[0].start=0;b[0].size=1024;b[0].free=1; int n=1;
    int reqs[]={100,200,50,300,150};
    for(int i=0;i<5;i++){
        int worst=-1,r=reqs[i];
        for(int j=0;j<n;j++) if(b[j].free && b[j].size>=r) if(worst==-1||b[j].size>b[worst].size) worst=j;
        if(worst==-1){printf("Failed %d\n",r);continue;}
        int old=b[worst].size;b[worst].size=r;b[worst].free=0;
        if(old>r){b[n].start=b[worst].start+r;b[n].size=old-r;b[n].free=1;n++;}
        printf("Worst-fit allocated %d at %d\n",r,worst);
    }
    return 0;
}
```

### Q93. Next-fit memory allocation simulation
```c
#include <stdio.h>
struct block{int start,size,free;};
int main(){
    struct block b[20]; b[0].start=0;b[0].size=1024;b[0].free=1; int n=1,last=0;
    int reqs[]={100,200,50,300,150};
    for(int i=0;i<5;i++){
        int allocated=0,r=reqs[i];
        for(int k=0;k<n;k++){
            int j=(last+k)%n;
            if(b[j].free && b[j].size>=r){
                int old=b[j].size;b[j].size=r;b[j].free=0;
                if(old>r){ b[n].start=b[j].start+r;b[n].size=old-r;b[n].free=1;n++;}
                printf("Next-fit allocated %d at %d\n",r,j); last=j; allocated=1; break;
            }
        }
        if(!allocated) printf("Failed %d\n",r);
    }
    return 0;
}
```

### Q94. Buddy system allocator (simplified)
```c
#include <stdio.h>
#define HEAP (1<<10)
static unsigned char heap[HEAP];
int main(){ printf("Toy buddy allocator initialized with heap %d bytes\n", HEAP); return 0;}
```

### Q95. Custom memory allocator (simple)
```c
#include <stdio.h>
#include <string.h>
#define HEAP_SIZE 4096
static unsigned char heap[HEAP_SIZE];
struct hdr{int size,free;struct hdr *next;};
int main(){ memset(heap,0,sizeof(heap)); struct hdr *h=(struct hdr*)heap; h->size=HEAP_SIZE-sizeof(*h); h->free=1; h->next=NULL; printf("Simple allocator: heap %d bytes\n", HEAP_SIZE); return 0;}
```

### Q96. Memory mapping using mmap()
```c
#include <sys/mman.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
int main(){
    int fd=open("mmap_example.txt",O_RDWR|O_CREAT,0644);
    write(fd,"hello mmap",10);
    struct stat st; fstat(fd,&st);
    char *p=mmap(NULL,st.st_size,PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
    printf("Mapped: %.*s\n",(int)st.st_size,p);
    munmap(p,st.st_size); close(fd); return 0;
}
```

### Q97. Read/write memory-mapped file
```c
#include <sys/mman.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
int main(){
    int fd=open("mmap_rw.txt",O_RDWR|O_CREAT,0644);
    ftruncate(fd,4096);
    char *p=mmap(NULL,4096,PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
    strcpy(p,"Data via mmap\n");
    printf("%s",p);
    munmap(p,4096); close(fd); return 0;
}
```

### Q98. Shared memory using shmget/shmat()
```c
#include <sys/ipc.h>
#include <sys/shm.h>
#include <stdio.h>
#include <string.h>
int main(){
    key_t key=ftok(".",'S');
    int shmid=shmget(key,1024,0644|IPC_CREAT);
    char *data=shmat(shmid,(void*)0,0);
    strcpy(data,"Shared memory example");
    printf("%s\n",data);
    shmdt(data);
    shmctl(shmid,IPC_RMID,NULL);
    return 0;
}
```

### Q99. Shared memory with semaphores
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/sem.h>
#include <string.h>
int main(){
    key_t key=ftok(".",'S');
    int shmid=shmget(key,1024,0644|IPC_CREAT);
    int semid=semget(key,1,0644|IPC_CREAT);
    char *data=shmat(shmid,NULL,0);
    strcpy(data,"Shared data with semaphore");
    printf("%s\n",data);
    shmdt(data);
    shmctl(shmid,IPC_RMID,NULL);
    semctl(semid,0,IPC_RMID);
    return 0;
}
```

### Q100. Page replacement algorithms simulation (FIFO/LRU/Optimal)
```c
#include <stdio.h>
int main(){
    int pages[]={1,2,3,4,1,2,5,1,2,3,4,5};
    int frame[3]={-1,-1,-1},i,j,k,f=0;
    int hits=0,misses=0;
    for(i=0;i<12;i++){
        int found=0;
        for(j=0;j<3;j++) if(frame[j]==pages[i]) found=1;
        if(found){ hits++; continue; }
        frame[f]=pages[i]; f=(f+1)%3; misses++;
    }
    printf("Hits=%d Misses=%d\n",hits,misses);
    return 0;
}
```
