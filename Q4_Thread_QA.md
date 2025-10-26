# Thread Programming in C — Batch 1 (Q1–Q45)

**Compile with:** `gcc -pthread file.c -o program`

---

## 1. Create a thread that prints "Hello, World!"

```c
#include <stdio.h>
#include <pthread.h>

void* print_message(void* arg) {
    printf("Hello, World!\n");
    return NULL;
}

int main() {
    pthread_t thread;
    pthread_create(&thread, NULL, print_message, NULL);
    pthread_join(thread, NULL);
    return 0;
}
```

---

## 2. Create multiple threads, each printing its own message

```c
#include <stdio.h>
#include <pthread.h>

void* print_message(void* arg) {
    char* msg = (char*)arg;
    printf("%s\n", msg);
    return NULL;
}

int main() {
    pthread_t threads[3];
    char* messages[] = {"Thread 1", "Thread 2", "Thread 3"};

    for (int i = 0; i < 3; i++)
        pthread_create(&threads[i], NULL, print_message, messages[i]);

    for (int i = 0; i < 3; i++)
        pthread_join(threads[i], NULL);

    return 0;
}
```

---

## 3. Two threads that print numbers from 1 to 10 concurrently

```c
#include <stdio.h>
#include <pthread.h>

void* print_numbers(void* arg) {
    long id = (long)arg;
    for (int i = 1; i <= 10; i++)
        printf("Thread %ld: %d\n", id, i);
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, print_numbers, (void*)1);
    pthread_create(&t2, NULL, print_numbers, (void*)2);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    return 0;
}
```

---

## 4. Create a thread that calculates factorial of a given number

```c
#include <stdio.h>
#include <pthread.h>

void* factorial(void* arg) {
    int n = *(int*)arg;
    unsigned long long result = 1;
    for (int i = 2; i <= n; i++) result *= i;
    printf("Factorial of %d is %llu\n", n, result);
    return NULL;
}

int main() {
    pthread_t t;
    int n = 5;
    pthread_create(&t, NULL, factorial, &n);
    pthread_join(t, NULL);
    return 0;
}
```

---

## 5. Create two threads that print their thread IDs

```c
#include <stdio.h>
#include <pthread.h>

void* print_tid(void* arg) {
    printf("Thread ID: %lu\n", (unsigned long)pthread_self());
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, print_tid, NULL);
    pthread_create(&t2, NULL, print_tid, NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    return 0;
}
```

---

## 6. Create a thread that prints the sum of two numbers

```c
#include <stdio.h>
#include <pthread.h>

typedef struct { int a,b; } pair;
void* sum_thread(void* arg) {
    pair* p = (pair*)arg;
    printf("Sum: %d\n", p->a + p->b);
    return NULL;
}

int main() {
    pthread_t t;
    pair p = {5, 7};
    pthread_create(&t, NULL, sum_thread, &p);
    pthread_join(t, NULL);
    return 0;
}
```

---

## 7. Create a thread that calculates the square of a number

```c
#include <stdio.h>
#include <pthread.h>

void* square(void* arg) {
    int n = *(int*)arg;
    printf("Square of %d is %d\n", n, n*n);
    return NULL;
}

int main() {
    pthread_t t;
    int x = 8;
    pthread_create(&t, NULL, square, &x);
    pthread_join(t, NULL);
    return 0;
}
```

---

## 8. Create a thread that prints the current date and time

```c
#include <stdio.h>
#include <pthread.h>
#include <time.h>

void* print_datetime(void* arg) {
    time_t t = time(NULL);
    printf("Current time: %s", ctime(&t));
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t, NULL, print_datetime, NULL);
    pthread_join(t, NULL);
    return 0;
}
```

---

## 9. Create a thread that checks if a number is prime

```c
#include <stdio.h>
#include <pthread.h>
#include <math.h>

void* is_prime(void* arg) {
    int n = *(int*)arg;
    if (n <= 1) { printf("%d is not prime\n", n); return NULL; }
    for (int i = 2; i <= (int)sqrt(n); i++)
        if (n % i == 0) { printf("%d is not prime\n", n); return NULL; }
    printf("%d is prime\n", n);
    return NULL;
}

int main() {
    pthread_t t;
    int n = 29;
    pthread_create(&t, NULL, is_prime, &n);
    pthread_join(t, NULL);
    return 0;
}
```

---

## 10. Create a thread that checks if a given string is a palindrome

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>

void* is_pal(void* arg) {
    char* s = (char*)arg;
    int i=0, j=strlen(s)-1;
    while (i<j) if (s[i++] != s[j--]) { printf("%s is NOT palindrome\n", s); return NULL; }
    printf("%s is palindrome\n", s);
    return NULL;
}

int main() {
    pthread_t t;
    char s[] = "radar";
    pthread_create(&t, NULL, is_pal, s);
    pthread_join(t, NULL);
    return 0;
}
```

---

## 11. Print "Hello, World!" with thread synchronization (mutex)

```c
#include <stdio.h>
#include <pthread.h>

pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* synced_hello(void* arg) {
    pthread_mutex_lock(&lock);
    printf("Hello, World!\n");
    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t, NULL, synced_hello, NULL);
    pthread_mutex_lock(&lock);
    // main could also print here safely
    pthread_mutex_unlock(&lock);
    pthread_join(t, NULL);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 12. Two threads print their thread IDs and synchronize output

```c
#include <stdio.h>
#include <pthread.h>

pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* print_id_sync(void* arg) {
    pthread_mutex_lock(&lock);
    printf("Thread synchronized ID: %lu\n", (unsigned long)pthread_self());
    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, print_id_sync, NULL);
    pthread_create(&t2, NULL, print_id_sync, NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 13. Thread generates random numbers and synchronizes access to a shared buffer

```c
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <unistd.h>

#define BUF_SIZE 5
int buffer[BUF_SIZE], count=0;
pthread_mutex_t m=PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t not_empty=PTHREAD_COND_INITIALIZER, not_full=PTHREAD_COND_INITIALIZER;

void* producer(void* arg) {
    for (int i=0;i<10;i++) {
        pthread_mutex_lock(&m);
        while (count==BUF_SIZE) pthread_cond_wait(&not_full,&m);
        buffer[count++] = rand()%100;
        printf("Produced %d\n", buffer[count-1]);
        pthread_cond_signal(&not_empty);
        pthread_mutex_unlock(&m);
        sleep(1);
    }
    return NULL;
}
void* consumer(void* arg) {
    for (int i=0;i<10;i++) {
        pthread_mutex_lock(&m);
        while (count==0) pthread_cond_wait(&not_empty,&m);
        int v = buffer[--count];
        printf("Consumed %d\n", v);
        pthread_cond_signal(&not_full);
        pthread_mutex_unlock(&m);
        sleep(2);
    }
    return NULL;
}

int main() {
    pthread_t p,c;
    pthread_create(&p,NULL,producer,NULL);
    pthread_create(&c,NULL,consumer,NULL);
    pthread_join(p,NULL);
    pthread_join(c,NULL);
    return 0;
}
```

---

## 14. Addition of two numbers with mutex locks

```c
#include <stdio.h>
#include <pthread.h>

int result = 0;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

typedef struct { int a,b; } pair;
void* add(void* arg) {
    pair* p = (pair*)arg;
    pthread_mutex_lock(&lock);
    result = p->a + p->b;
    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t;
    pair p = {10, 20};
    pthread_create(&t,NULL,add,&p);
    pthread_join(t,NULL);
    printf("Result = %d\n", result);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 15. Two threads increment and decrement a shared variable using mutex locks

```c
#include <stdio.h>
#include <pthread.h>

int shared = 0;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* inc(void* arg) {
    for (int i=0;i<10000;i++) {
        pthread_mutex_lock(&lock);
        shared++;
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}
void* dec(void* arg) {
    for (int i=0;i<10000;i++) {
        pthread_mutex_lock(&lock);
        shared--;
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}

int main() {
    pthread_t t1,t2;
    pthread_create(&t1,NULL,inc,NULL);
    pthread_create(&t2,NULL,dec,NULL);
    pthread_join(t1,NULL);
    pthread_join(t2,NULL);
    printf("Shared = %d\n", shared);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 16. Create a thread that reads input from user and synchronizes access to shared resources

```c
#include <stdio.h>
#include <pthread.h>
#include <string.h>

char shared[100];
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* reader(void* arg) {
    pthread_mutex_lock(&lock);
    printf("Shared content: %s\n", shared);
    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_mutex_lock(&lock);
    strcpy(shared, "Hello from main");
    pthread_mutex_unlock(&lock);

    pthread_create(&t,NULL,reader,NULL);
    pthread_join(t,NULL);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 17. Print prime numbers up to a limit with mutex locks

```c
#include <stdio.h>
#include <pthread.h>
#include <math.h>

int limit = 50;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* print_primes(void* arg) {
    for (int n=2;n<=limit;n++) {
        int isprime=1;
        for (int i=2;i<= (int)sqrt(n); i++) if (n%i==0) { isprime=0; break; }
        if (isprime) {
            pthread_mutex_lock(&lock);
            printf("%d ", n);
            pthread_mutex_unlock(&lock);
        }
    }
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,print_primes,NULL);
    pthread_join(t,NULL);
    printf("\n");
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 18. Calculate sum of Fibonacci numbers up to limit using DP with mutex locks

```c
#include <stdio.h>
#include <pthread.h>

int limit = 10;
long fib[100];
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* fib_sum(void* arg) {
    pthread_mutex_lock(&lock);
    fib[0]=0; fib[1]=1;
    long sum = fib[0]+fib[1];
    for (int i=2;i<=limit;i++) {
        fib[i]=fib[i-1]+fib[i-2];
        sum += fib[i];
    }
    printf("Sum of first %d Fibonacci numbers = %ld\n", limit+1, sum);
    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,fib_sum,NULL);
    pthread_join(t,NULL);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 19. Check if a given year is a leap year using thread (mutex used to protect output)

```c
#include <stdio.h>
#include <pthread.h>

int year = 2024;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* is_leap(void* arg) {
    int y = *(int*)arg;
    int leap = (y%4==0 && (y%100!=0 || y%400==0));
    pthread_mutex_lock(&lock);
    printf("%d is %s leap year\n", y, leap? "a": "not a");
    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,is_leap,&year);
    pthread_join(t,NULL);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 20. Check if a string is palindrome using thread with mutex

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>

char s[] = "level";
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void* check_pal(void* arg) {
    pthread_mutex_lock(&lock);
    int i=0,j=strlen(s)-1, ok=1;
    while (i<j) if (s[i++]!=s[j--]) { ok=0; break; }
    printf("%s is %s palindrome\n", s, ok? "": "NOT ");
    pthread_mutex_unlock(&lock);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,check_pal,NULL);
    pthread_join(t,NULL);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 21. Create a thread that performs selection sort on an array of integers

```c
#include <stdio.h>
#include <pthread.h>

int arr[] = {64,25,12,22,11};
int n = 5;

void* selection_sort(void* arg) {
    for (int i=0;i<n-1;i++) {
        int min=i;
        for (int j=i+1;j<n;j++) if (arr[j]<arr[min]) min=j;
        int tmp = arr[min]; arr[min]=arr[i]; arr[i]=tmp;
    }
    printf("Sorted: ");
    for (int i=0;i<n;i++) printf("%d ", arr[i]);
    printf("\n");
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,selection_sort,NULL);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 22. Thread that calculates area of a triangle (base & height)

```c
#include <stdio.h>
#include <pthread.h>

typedef struct { double b,h; } tri;
void* area(void* arg) {
    tri* t = (tri*)arg;
    printf("Area = %f\n", 0.5 * t->b * t->h);
    return NULL;
}

int main() {
    pthread_t t;
    tri val={10.0,5.0};
    pthread_create(&t,NULL,area,&val);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 23. Thread that calculates sum of squares of numbers from 1 to 100

```c
#include <stdio.h>
#include <pthread.h>

void* sum_squares(void* arg) {
    long sum=0;
    for (int i=1;i<=100;i++) sum += i*i;
    printf("Sum of squares 1..100 = %ld\n", sum);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,sum_squares,NULL);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 24. Thread that generates a random array of integers

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <time.h>

#define N 10
int arr[N];

void* gen_rand(void* arg) {
    srand(time(NULL));
    for (int i=0;i<N;i++) arr[i]=rand()%100;
    printf("Generated: ");
    for (int i=0;i<N;i++) printf("%d ", arr[i]);
    printf("\n");
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,gen_rand,NULL);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 25. Thread that performs bubble sort on an array

```c
#include <stdio.h>
#include <pthread.h>

int arr[] = {5,1,4,2,8};
int n = 5;

void* bubble_sort(void* arg) {
    for (int i=0;i<n-1;i++)
        for (int j=0;j<n-i-1;j++)
            if (arr[j]>arr[j+1]) { int t=arr[j]; arr[j]=arr[j+1]; arr[j+1]=t; }
    printf("Sorted: ");
    for (int i=0;i<n;i++) printf("%d ", arr[i]);
    printf("\n");
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,bubble_sort,NULL);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 26. Thread that calculates GCD of two numbers

```c
#include <stdio.h>
#include <pthread.h>

typedef struct { int a,b; } pair;
void* gcd_thread(void* arg) {
    pair* p = (pair*)arg;
    int a=p->a, b=p->b;
    while (b) { int t=a%b; a=b; b=t; }
    printf("GCD = %d\n", a);
    return NULL;
}

int main() {
    pthread_t t;
    pair p = {48,18};
    pthread_create(&t,NULL,gcd_thread,&p);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 27. Thread that calculates sum of Fibonacci numbers up to limit

```c
#include <stdio.h>
#include <pthread.h>

int limit = 10;
void* fib_sum2(void* arg) {
    long a=0,b=1,sum=a+b;
    if (limit==0) { printf("Sum=0\n"); return NULL; }
    for (int i=2;i<=limit;i++){
        long c=a+b; sum+=c; a=b; b=c;
    }
    printf("Sum of first %d Fibonacci numbers = %ld\n", limit+1, sum);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,fib_sum2,NULL);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 28. Thread that calculates sum of even numbers from 1 to 100

```c
#include <stdio.h>
#include <pthread.h>

void* sum_even(void* arg) {
    int sum=0;
    for (int i=2;i<=100;i+=2) sum += i;
    printf("Sum of even numbers 1..100 = %d\n", sum);
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,sum_even,NULL);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 29. Factorial using iteration in a thread

```c
#include <stdio.h>
#include <pthread.h>

void* factorial_iter(void* arg) {
    int n = *(int*)arg;
    unsigned long long res=1;
    for (int i=1;i<=n;i++) res*=i;
    printf("Factorial(%d) = %llu\n", n, res);
    return NULL;
}

int main(){
    pthread_t t; int n=6;
    pthread_create(&t,NULL,factorial_iter,&n);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 30. Thread that checks if a given year is a leap year

```c
#include <stdio.h>
#include <pthread.h>

void* leap_thread(void* arg) {
    int y = *(int*)arg;
    int leap = (y%4==0 && (y%100!=0 || y%400==0));
    printf("%d is %s leap year\n", y, leap? "a":"not a");
    return NULL;
}

int main() {
    pthread_t t; int year=2000;
    pthread_create(&t,NULL,leap_thread,&year);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 31. Thread that performs multiplication of two matrices

```c
#include <stdio.h>
#include <pthread.h>

int A[2][2] = {{1,2},{3,4}};
int B[2][2] = {{5,6},{7,8}};
int C[2][2];

void* multiply(void* arg) {
    for (int i=0;i<2;i++)
        for (int j=0;j<2;j++) {
            C[i][j]=0;
            for (int k=0;k<2;k++) C[i][j]+=A[i][k]*B[k][j];
        }
    printf("Result:\n");
    for (int i=0;i<2;i++){ for (int j=0;j<2;j++) printf("%d ", C[i][j]); printf("\n"); }
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t,NULL,multiply,NULL);
    pthread_join(t,NULL);
    return 0;
}
```

---

## 32. Thread that calculates average of numbers from 1 to 100

```c
#include <stdio.h>
#include <pthread.h>

void* average(void* arg) {
    long sum=0;
    for (int i=1;i<=100;i++) sum+=i;
    printf("Average = %f\n", sum/100.0);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,average,NULL); pthread_join(t,NULL); return 0; }
```

---

## 33. Thread that generates a random string

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>
#include <time.h>

void rand_str(char*s,int n){ static const char charset[]="abcdefghijklmnopqrstuvwxyz"; for(int i=0;i<n;i++) s[i]=charset[rand()% (int)(sizeof(charset)-1)]; s[n]=0; }

void* gen_str(void* arg) {
    srand(time(NULL));
    char s[9];
    rand_str(s,8);
    printf("Random string: %s\n", s);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,gen_str,NULL); pthread_join(t,NULL); return 0; }
```

---

## 34. Thread that checks if a number is a perfect square

```c
#include <stdio.h>
#include <math.h>
#include <pthread.h>

void* is_perfect(void* arg) {
    int n = *(int*)arg;
    int r = (int) sqrt(n);
    printf("%d is %sperfect square\n", n, r*r==n? "":"NOT ");
    return NULL;
}

int main(){ pthread_t t; int n=49; pthread_create(&t,NULL,is_perfect,&n); pthread_join(t,NULL); return 0; }
```

---

## 35. Thread that calculates sum of digits of a given number

```c
#include <stdio.h>
#include <pthread.h>

void* sum_digits(void* arg) {
    int n = *(int*)arg; int sum=0;
    while (n) { sum += n%10; n/=10; }
    printf("Sum of digits = %d\n", sum);
    return NULL;
}

int main(){ pthread_t t; int n=12345; pthread_create(&t,NULL,sum_digits,&n); pthread_join(t,NULL); return 0; }
```

---

## 36. Factorial of a number using recursion in a thread

```c
#include <stdio.h>
#include <pthread.h>

unsigned long long fact(int n){ if(n<=1) return 1; return n*fact(n-1); }

void* fact_thread(void* arg){
    int n = *(int*)arg;
    printf("Factorial(%d) = %llu\n", n, fact(n));
    return NULL;
}

int main(){ pthread_t t; int n=7; pthread_create(&t,NULL,fact_thread,&n); pthread_join(t,NULL); return 0; }
```

---

## 37. Thread that finds maximum element in an array

```c
#include <stdio.h>
#include <pthread.h>

int arr[] = {3, 7, 2, 9, 5};
int n = 5;

void* find_max(void* arg) {
    int mx = arr[0];
    for (int i=1;i<n;i++) if (arr[i]>mx) mx=arr[i];
    printf("Max = %d\n", mx);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,find_max,NULL); pthread_join(t,NULL); return 0; }
```

---

## 38. Thread that sorts an array of strings

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>

char* arr[] = {"banana","apple","cherry","date"};
int n = 4;

void* sort_strs(void* arg) {
    for (int i=0;i<n-1;i++)
        for (int j=i+1;j<n;j++)
            if (strcmp(arr[i],arr[j])>0) { char* t=arr[i]; arr[i]=arr[j]; arr[j]=t; }
    printf("Sorted: ");
    for (int i=0;i<n;i++) printf("%s ", arr[i]);
    printf("\n");
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,sort_strs,NULL); pthread_join(t,NULL); return 0; }
```

---

## 39. Thread that calculates square root of a number

```c
#include <stdio.h>
#include <math.h>
#include <pthread.h>

void* sqrt_thread(void* arg) {
    double x = *(double*)arg;
    printf("Sqrt(%f) = %f\n", x, sqrt(x));
    return NULL;
}

int main(){ pthread_t t; double x=49.0; pthread_create(&t,NULL,sqrt_thread,&x); pthread_join(t,NULL); return 0; }
```

---

## 40. Thread that calculates average of numbers in an array

```c
#include <stdio.h>
#include <pthread.h>

double arr[] = {1.0,2.0,3.0,4.0,5.0};
int n = 5;

void* avg_arr(void* arg) {
    double sum=0;
    for (int i=0;i<n;i++) sum+=arr[i];
    printf("Average = %f\n", sum/n);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,avg_arr,NULL); pthread_join(t,NULL); return 0; }
```

---

## 41. Thread that generates a random password

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>
#include <time.h>

void* gen_pass(void* arg) {
    const char *chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    char s[13]; srand(time(NULL));
    for (int i=0;i<12;i++) s[i]=chars[rand()%62];
    s[12]=0;
    printf("Password: %s\n", s);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,gen_pass,NULL); pthread_join(t,NULL); return 0; }
```

---

## 42. Thread that checks if a number is even or odd

```c
#include <stdio.h>
#include <pthread.h>

void* even_odd(void* arg) {
    int n = *(int*)arg;
    printf("%d is %s\n", n, (n%2==0)?"even":"odd");
    return NULL;
}

int main(){ pthread_t t; int n=42; pthread_create(&t,NULL,even_odd,&n); pthread_join(t,NULL); return 0; }
```

---

## 43. Thread that calculates sum of elements in an array

```c
#include <stdio.h>
#include <pthread.h>

int arr[] = {1,2,3,4,5};
int n = 5;

void* sum_arr(void* arg){
    int s=0; for (int i=0;i<n;i++) s+=arr[i];
    printf("Sum = %d\n", s);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,sum_arr,NULL); pthread_join(t,NULL); return 0; }
```

---

## 44. Thread that calculates factorial of numbers from 1 to 10

```c
#include <stdio.h>
#include <pthread.h>

void* facts(void* arg) {
    for (int n=1;n<=10;n++) {
        unsigned long long f=1;
        for (int i=1;i<=n;i++) f*=i;
        printf("%d! = %llu\n", n, f);
    }
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,facts,NULL); pthread_join(t,NULL); return 0; }
```

---

## 45. Thread that calculates area of a rectangle

```c
#include <stdio.h>
#include <pthread.h>

typedef struct { double l,w; } rect;
void* area_rect(void* arg) {
    rect* r = (rect*)arg;
    printf("Area = %f\n", r->l * r->w);
    return NULL;
}

int main()
{ pthread_t t; rect r={5.0,3.0}; pthread_create(&t,NULL,area_rect,&r); pthread_join(t,NULL); return 0; }





## 46. Create a thread that generates random numbers

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <time.h>

void* gen_randoms(void* arg) {
    srand(time(NULL));
    for (int i=0;i<5;i++) printf("Rand: %d\n", rand()%100);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,gen_randoms,NULL); pthread_join(t,NULL); return 0; }
```

---

## 47. Thread that sorts an array of integers (using qsort)

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

int arr[] = {9,4,6,2,8};
int n = 5;

int cmp(const void*a,const void*b){ return (*(int*)a - *(int*)b); }

void* sort_thread(void* arg) {
    qsort(arr, n, sizeof(int), cmp);
    printf("Sorted: ");
    for (int i=0;i<n;i++) printf("%d ", arr[i]);
    printf("\n");
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,sort_thread,NULL); pthread_join(t,NULL); return 0; }
```

---

## 48. Thread that searches for a given number in an array

```c
#include <stdio.h>
#include <pthread.h>

int arr[] = {1,3,5,7,9};
int n = 5;

void* search(void* arg) {
    int key = *(int*)arg;
    for (int i=0;i<n;i++) if (arr[i]==key) { printf("Found %d at %d\n", key, i); return NULL; }
    printf("%d not found\n", key);
    return NULL;
}

int main(){ pthread_t t; int key=7; pthread_create(&t,NULL,search,&key); pthread_join(t,NULL); return 0; }
```

---

## 49. Thread that reverses a given string

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>

void* reverse_str(void* arg) {
    char* s = (char*)arg;
    int i=0,j=strlen(s)-1;
    while (i<j) { char t=s[i]; s[i]=s[j]; s[j]=t; i++; j--; }
    printf("Reversed: %s\n", s);
    return NULL;
}

int main(){ pthread_t t; char s[]="hello"; pthread_create(&t,NULL,reverse_str,s); pthread_join(t,NULL); return 0; }
```

---

## 50. Thread that reads input from the user

```c
#include <stdio.h>
#include <pthread.h>

void* read_input(void* arg) {
    char buf[100];
    printf("Enter text: ");
    if (fgets(buf, sizeof(buf), stdin)) printf("You entered: %s", buf);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,read_input,NULL); pthread_join(t,NULL); return 0; }
```

---

## 51. Thread that performs addition of two matrices

```c
#include <stdio.h>
#include <pthread.h>

int A[2][2]={{1,2},{3,4}}, B[2][2]={{5,6},{7,8}}, C[2][2];

void* add_matrices(void* arg) {
    for (int i=0;i<2;i++) for (int j=0;j<2;j++) C[i][j]=A[i][j]+B[i][j];
    printf("Sum:\n"); for (int i=0;i<2;i++){ for (int j=0;j<2;j++) printf("%d ", C[i][j]); printf("\n"); }
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,add_matrices,NULL); pthread_join(t,NULL); return 0; }
```

---

## 52. Thread that calculates the length of a given string

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>

void* str_len(void* arg) {
    char* s = (char*)arg;
    printf("Length = %zu\n", strlen(s));
    return NULL;
}

int main(){ pthread_t t; char s[]="pthread"; pthread_create(&t,NULL,str_len,s); pthread_join(t,NULL); return 0; }
```

---

## 53. Create two threads using pthreads; each prints "Hello, World!" with its thread ID

```c
#include <stdio.h>
#include <pthread.h>

void* hello_tid(void* arg) {
    printf("Hello, World! from thread %lu\n", (unsigned long)pthread_self());
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_create(&t1,NULL,hello_tid,NULL);
    pthread_create(&t2,NULL,hello_tid,NULL);
    pthread_join(t1,NULL);
    pthread_join(t2,NULL);
    return 0;
}
```

---

## 54. Modify previous program to pass arguments to threads and print them with thread ID

```c
#include <stdio.h>
#include <pthread.h>

void* hello_arg(void* arg) {
    char* msg = (char*)arg;
    printf("Thread %lu: %s\n", (unsigned long)pthread_self(), msg);
    return NULL;
}

int main() {
    pthread_t t1,t2;
    char* a = "Argument A";
    char* b = "Argument B";
    pthread_create(&t1,NULL,hello_arg,a);
    pthread_create(&t2,NULL,hello_arg,b);
    pthread_join(t1,NULL);
    pthread_join(t2,NULL);
    return 0;
}
```

---

## 55. Demonstrate thread synchronization using mutex locks: two threads increment shared variable

```c
#include <stdio.h>
#include <pthread.h>

int counter=0;
pthread_mutex_t lock=PTHREAD_MUTEX_INITIALIZER;

void* inc_thread(void* arg) {
    for (int i=0;i<100000;i++) {
        pthread_mutex_lock(&lock);
        counter++;
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}

int main(){
    pthread_t t1,t2;
    pthread_create(&t1,NULL,inc_thread,NULL);
    pthread_create(&t2,NULL,inc_thread,NULL);
    pthread_join(t1,NULL); pthread_join(t2,NULL);
    printf("Counter=%d\n", counter);
    pthread_mutex_destroy(&lock);
    return 0;
}
```

---

## 56. Use semaphore instead of mutex for synchronization

```c
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>

int counter=0;
sem_t sem;

void* inc_sem(void* arg) {
    for (int i=0;i<100000;i++) {
        sem_wait(&sem);
        counter++;
        sem_post(&sem);
    }
    return NULL;
}

int main(){
    pthread_t t1,t2;
    sem_init(&sem,0,1);
    pthread_create(&t1,NULL,inc_sem,NULL);
    pthread_create(&t2,NULL,inc_sem,NULL);
    pthread_join(t1,NULL); pthread_join(t2,NULL);
    printf("Counter=%d\n", counter);
    sem_destroy(&sem);
    return 0;
}
```

---

## 57. Implement producer-consumer using pthreads (producer and consumer threads)

```c
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <unistd.h>

#define SIZE 5
int buf[SIZE], count=0;
pthread_mutex_t m=PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t not_full=PTHREAD_COND_INITIALIZER, not_empty=PTHREAD_COND_INITIALIZER;

void* producer(void* arg) {
    for (int i=0;i<10;i++) {
        pthread_mutex_lock(&m);
        while (count==SIZE) pthread_cond_wait(&not_full,&m);
        buf[count++]=i;
        printf("Produced %d\n", i);
        pthread_cond_signal(&not_empty);
        pthread_mutex_unlock(&m);
        sleep(1);
    }
    return NULL;
}

void* consumer(void* arg) {
    for (int i=0;i<10;i++) {
        pthread_mutex_lock(&m);
        while (count==0) pthread_cond_wait(&not_empty,&m);
        int v = buf[--count];
        printf("Consumed %d\n", v);
        pthread_cond_signal(&not_full);
        pthread_mutex_unlock(&m);
        sleep(2);
    }
    return NULL;
}

int main(){
    pthread_t p,c;
    pthread_create(&p,NULL,producer,NULL);
    pthread_create(&c,NULL,consumer,NULL);
    pthread_join(p,NULL); pthread_join(c,NULL);
    return 0;
}
```

---

## 58. Multithreaded file copy: multiple reader threads and one writer (simple demo)

```c
// Note: For simplicity, this demo spawns threads that read fixed parts and write sequentially.
// Proper multi-threaded file copy needs careful offset management and I/O synchronization.

#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>

#define CHUNK 1024
typedef struct { FILE *src; long offset; size_t size; } job;

void* read_chunk(void* arg) {
    job* j = (job*)arg;
    char *buf = malloc(j->size);
    fseek(j->src, j->offset, SEEK_SET);
    fread(buf,1,j->size,j->src);
    // In real usage, send to writer queue; here we just print chunk size
    printf("Read chunk at %ld size %zu\n", j->offset, j->size);
    free(buf);
    return NULL;
}

int main(){
    // This is illustrative; not a production-ready file copy.
    printf("See comments in code.\n");
    return 0;
}
```

---

## 59. Thread cancellation demo: cancel a running thread

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

void* worker(void* arg) {
    while (1) {
        printf("Working...\n");
        sleep(1);
        pthread_testcancel(); // cancellation point
    }
    return NULL;
}

int main(){
    pthread_t t;
    pthread_create(&t,NULL,worker,NULL);
    sleep(3);
    pthread_cancel(t);
    pthread_join(t,NULL);
    printf("Worker cancelled\n");
    return 0;
}
```

---

## 60. Implement reader-writer lock using pthreads and pthread_rwlock_t

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

pthread_rwlock_t rwlock = PTHREAD_RWLOCK_INITIALIZER;
int shared = 0;

void* reader(void* arg) {
    pthread_rwlock_rdlock(&rwlock);
    printf("Reader sees %d\n", shared);
    pthread_rwlock_unlock(&rwlock);
    return NULL;
}

void* writer(void* arg) {
    pthread_rwlock_wrlock(&rwlock);
    shared++;
    printf("Writer updated to %d\n", shared);
    pthread_rwlock_unlock(&rwlock);
    return NULL;
}

int main(){
    pthread_t r1,r2,w;
    pthread_create(&r1,NULL,reader,NULL);
    pthread_create(&w,NULL,writer,NULL);
    pthread_create(&r2,NULL,reader,NULL);
    pthread_join(r1,NULL); pthread_join(r2,NULL); pthread_join(w,NULL);
    pthread_rwlock_destroy(&rwlock);
    return 0;
}
```

---

## 61. Thread that prints even numbers between 1 and 20

```c
#include <stdio.h>
#include <pthread.h>

void* print_even(void* arg) {
    for (int i=2;i<=20;i+=2) printf("%d ", i);
    printf("\n");
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,print_even,NULL); pthread_join(t,NULL); return 0; }
```

---

## 62. Two threads print odd and even numbers alternately

```c
#include <stdio.h>
#include <pthread.h>

int turn = 0; // 0 -> even, 1 -> odd
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

void* print_even(void* arg) {
    for (int i=2;i<=10;i+=2) {
        pthread_mutex_lock(&lock);
        while (turn != 0) pthread_cond_wait(&cond,&lock);
        printf("%d ", i);
        turn = 1;
        pthread_cond_signal(&cond);
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}

void* print_odd(void* arg) {
    for (int i=1;i<=9;i+=2) {
        pthread_mutex_lock(&lock);
        while (turn != 1) pthread_cond_wait(&cond,&lock);
        printf("%d ", i);
        turn = 0;
        pthread_cond_signal(&cond);
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}

int main(){
    pthread_t e,o;
    pthread_create(&e,NULL,print_even,NULL);
    pthread_create(&o,NULL,print_odd,NULL);
    pthread_join(e,NULL); pthread_join(o,NULL);
    printf("\n");
    pthread_mutex_destroy(&lock);
    pthread_cond_destroy(&cond);
    return 0;
}
```

---

## 63. Thread that calculates sum of squares from 1 to 10

```c
#include <stdio.h>
#include <pthread.h>

void* sumsq(void* arg) {
    int s=0;
    for (int i=1;i<=10;i++) s+=i*i;
    printf("Sum squares = %d\n", s);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,sumsq,NULL); pthread_join(t,NULL); return 0; }
```

---

## 64. Thread that calculates product of numbers from 1 to 5

```c
#include <stdio.h>
#include <pthread.h>

void* product(void* arg) {
    int p=1;
    for (int i=1;i<=5;i++) p*=i;
    printf("Product = %d\n", p);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,product,NULL); pthread_join(t,NULL); return 0; }
```

---

## 65. Thread that prints first 10 terms of Fibonacci sequence

```c
#include <stdio.h>
#include <pthread.h>

void* fib10(void* arg) {
    int a=0,b=1;
    printf("%d %d ", a,b);
    for (int i=2;i<10;i++) { int c=a+b; printf("%d ", c); a=b; b=c; }
    printf("\n");
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,fib10,NULL); pthread_join(t,NULL); return 0; }
```

---

## 66. Thread that finds maximum element in array (given arbitrary array)

```c
#include <stdio.h>
#include <pthread.h>

int arr[] = {-1, -5, 7, 3, 10, 2};
int n = 6;

void* max_thread(void* arg) {
    int mx = arr[0];
    for (int i=1;i<n;i++) if (arr[i]>mx) mx=arr[i];
    printf("Max = %d\n", mx);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,max_thread,NULL); pthread_join(t,NULL); return 0; }
```

---

## 67. Thread that prints ASCII values of characters in a given string

```c
#include <stdio.h>
#include <pthread.h>
#include <string.h>

void* ascii_vals(void* arg) {
    char* s = (char*)arg;
    for (int i=0;i<strlen(s);i++) printf("%c: %d\n", s[i], (unsigned char)s[i]);
    return NULL;
}

int main(){ pthread_t t; char s[]="Hi!"; pthread_create(&t,NULL,ascii_vals,s); pthread_join(t,NULL); return 0; }
```

---

## 68. Thread that calculates sum of all prime numbers up to a given limit

```c
#include <stdio.h>
#include <pthread.h>
#include <math.h>

int limit68 = 50;
void* sum_primes(void* arg) {
    int sum=0;
    for (int n=2;n<=limit68;n++){
        int p=1;
        for (int i=2;i<= (int)sqrt(n); i++) if (n%i==0){ p=0; break; }
        if (p) sum+=n;
    }
    printf("Sum of primes up to %d = %d\n", limit68, sum);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,sum_primes,NULL); pthread_join(t,NULL); return 0; }
```

---

## 69. Thread that calculates area of a circle given radius

```c
#include <stdio.h>
#include <pthread.h>

void* area_circle(void* arg) {
    double r = *(double*)arg;
    printf("Area = %f\n", 3.141592653589793 * r * r);
    return NULL;
}

int main(){ pthread_t t; double r=5.0; pthread_create(&t,NULL,area_circle,&r); pthread_join(t,NULL); return 0; }
```

---

## 70. Thread that calculates average of given array of floating-point numbers

```c
#include <stdio.h>
#include <pthread.h>

double arr70[] = {2.5, 3.5, 4.0, 5.0};
int n70 = 4;

void* avg_float(void* arg) {
    double s=0;
    for (int i=0;i<n70;i++) s+=arr70[i];
    printf("Average = %f\n", s/n70);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,avg_float,NULL); pthread_join(t,NULL); return 0; }
```

---

## 71. Thread that prints factors of a given number

```c
#include <stdio.h>
#include <pthread.h>

void* factors(void* arg) {
    int n = *(int*)arg;
    printf("Factors: ");
    for (int i=1;i<=n;i++) if (n%i==0) printf("%d ", i);
    printf("\n");
    return NULL;
}

int main(){ pthread_t t; int n=28; pthread_create(&t,NULL,factors,&n); pthread_join(t,NULL); return 0; }
```

---

## 72. Thread that prints English alphabet in uppercase

```c
#include <stdio.h>
#include <pthread.h>

void* alpha(void* arg) {
    for (char c='A'; c<='Z'; c++) printf("%c ", c);
    printf("\n");
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,alpha,NULL); pthread_join(t,NULL); return 0; }
```

---

## 73. Thread that checks if a given number is a perfect square

```c
#include <stdio.h>
#include <math.h>
#include <pthread.h>

void* check_perfect(void* arg) {
    int n = *(int*)arg;
    int r = (int)sqrt(n);
    printf("%d is %sperfect square\n", n, r*r==n ? "" : "NOT ");
    return NULL;
}

int main(){ pthread_t t; int n=36; pthread_create(&t,NULL,check_perfect,&n); pthread_join(t,NULL); return 0; }
```

---

## 74. Thread that checks if a given number is divisible by another

```c
#include <stdio.h>
#include <pthread.h>

typedef struct { int a,b; } pair74;
void* divisible(void* arg) {
    pair74* p = (pair74*)arg;
    printf("%d is %sdivisible by %d\n", p->a, (p->a % p->b == 0) ? "" : "NOT ", p->b);
    return NULL;
}

int main(){ pthread_t t; pair74 p={20,4}; pthread_create(&t,NULL,divisible,&p); pthread_join(t,NULL); return 0; }
```

---

## 75. Thread that prints multiplication table of a given number up to a limit

```c
#include <stdio.h>
#include <pthread.h>

typedef struct { int n, limit; } tbl;
void* mult_table(void* arg) {
    tbl* t = (tbl*)arg;
    for (int i=1;i<=t->limit;i++) printf("%d x %d = %d\n", t->n, i, t->n*i);
    return NULL;
}

int main(){ pthread_t t; tbl p={5,10}; pthread_create(&t,NULL,mult_table,&p); pthread_join(t,NULL); return 0; }
```

---

## 76. Thread that prints current system time

```c
#include <stdio.h>
#include <time.h>
#include <pthread.h>

void* print_time(void* arg) {
    time_t t = time(NULL);
    printf("Now: %s", ctime(&t));
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,print_time,NULL); pthread_join(t,NULL); return 0; }
```

---

## 77. Two threads increment and decrement a shared variable using mutex locks (repeat)

```c
#include <stdio.h>
#include <pthread.h>

int shared77=0;
pthread_mutex_t lock77=PTHREAD_MUTEX_INITIALIZER;

void* inc77(void* arg) { for(int i=0;i<10000;i++){ pthread_mutex_lock(&lock77); shared77++; pthread_mutex_unlock(&lock77);} return NULL; }
void* dec77(void* arg) { for(int i=0;i<10000;i++){ pthread_mutex_lock(&lock77); shared77--; pthread_mutex_unlock(&lock77);} return NULL; }

int main(){ pthread_t t1,t2; pthread_create(&t1,NULL,inc77,NULL); pthread_create(&t2,NULL,dec77,NULL); pthread_join(t1,NULL); pthread_join(t2,NULL); printf("Shared=%d\n", shared77); pthread_mutex_destroy(&lock77); return 0; }
```

---

## 78. Thread that prints numbers from 10 to 1 in descending order using mutex locks

```c
#include <stdio.h>
#include <pthread.h>

pthread_mutex_t lock78 = PTHREAD_MUTEX_INITIALIZER;

void* desc(void* arg) {
    pthread_mutex_lock(&lock78);
    for (int i=10;i>=1;i--) printf("%d ", i);
    printf("\n");
    pthread_mutex_unlock(&lock78);
    return NULL;
}

int main(){ pthread_t t; pthread_create(&t,NULL,desc,NULL); pthread_join(t,NULL); pthread_mutex_destroy(&lock78); return 0; }
```

---

## 79. Why use pthread_create instead of clone() for creating threads? (theory)

```c
// Explanation (not code):
/*
pthread_create provides a portable, standard API (POSIX) for thread creation across Unix-like systems.
clone() is Linux-specific and requires manual handling of flags, stack, and signal behaviors.
pthread abstracts details like thread attributes, joining, cancellation, and synchronization primitives.
*/
int main(){ return 0; }
```

---

## 80. Why does a stack grow? (theory)

```c
// Explanation:
/*
A stack grows because functions push return addresses, local variables, and saved registers onto it on each call.
Growth direction (up or down in memory) depends on architecture/ABI.
Stack growth enables nested calls and local storage without dynamic allocation.
*/
int main(){ return 0; }
```

---

## 81. What segments are shared by multiple threads within a process? (theory)

```c
// Explanation:
/*
Threads within the same process share:
- Code (text) segment
- Data (global/static) segment
- Heap
Each thread has its own stack and registers.
*/
int main(){ return 0; }
```

---

## 82. Can you fetch the thread entry point return value in your main thread?

```c
#include <stdio.h>
#include <pthread.h>

void* worker(void* arg) {
    int *res = malloc(sizeof(int));
    *res = 42;
    return res;
}

int main(){
    pthread_t t; void* ret;
    pthread_create(&t,NULL,worker,NULL);
    pthread_join(t,&ret);
    printf("Return value = %d\n", *(int*)ret);
    free(ret);
    return 0;
}
```

---

## 83. What happens when main function is invoked? (theory)

```c
// Explanation:
/*
When main is invoked:
- OS sets up process address space, initializes runtime, argc/argv/environment.
- main executes; on return, exit handlers run and process terminates.
*/
int main(){ return 0; }
```

---

## 84. What happens when CPU stops executing? (theory)

```c
// Explanation:
/*
If CPU stops (halt/power off/crash), executing process halts. OS may reboot, or kernel panic occurs.
Context and volatile state are lost unless saved to persistent storage.
*/
int main(){ return 0; }
```

---

## 85. During context switch, which instruction copies CPU registers to PCB? (theory)

```c
// Explanation:
/*
No single instruction universally copies registers; context switch is performed by OS using assembly
to save registers (push/pop or mov to memory). Exact sequence depends on architecture.
*/
int main(){ return 0; }
```

---

## 86. How do you create a separate process?

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main(){
    pid_t pid = fork();
    if (pid == 0) { // child
        printf("Child process\n");
    } else {
        printf("Parent process, child pid=%d\n", pid);
    }
    return 0;
}
```

---

## 87. How does a server create a separate thread? (example using pthreads)

```c
#include <stdio.h>
#include <pthread.h>

void* handle_client(void* arg) {
    // handle client connection 'arg' (socket fd)
    return NULL;
}

int main(){
    pthread_t t;
    // after accept()
    // pthread_create(&t, NULL, handle_client, (void*)(intptr_t)client_sock);
    return 0;
}
```

---

## 88. Advantages of thread over process (theory)

```c
// Explanation:
/*
Threads are lightweight: lower creation/teardown cost, shared memory space (fast communication), and efficient context switching.
*/
int main(){ return 0; }
```

---

## 89. Advantages of process over thread (theory)

```c
// Explanation:
/*
Processes provide isolation (separate address spaces), better fault isolation and security.
*/
int main(){ return 0; }
```

---

## 90. How to overcome synchronization issues when multiple threads access global variables?

```c
// Explanation:
/*
Use synchronization primitives: mutexes, semaphores, condition variables, read-write locks.
Design to minimize contention and use atomic operations when suitable.
*/
int main(){ return 0; }
```

---



## 91. How much CPU time is given to user space thread and kernel space thread?

**Answer:**\
User-space threads are scheduled by the user-level thread library, not
by the OS kernel, so they share CPU time according to the library's
scheduler.\
Kernel-space threads (POSIX pthreads) are scheduled directly by the OS
kernel and share CPU time equally based on priority and scheduling
policy.

------------------------------------------------------------------------

## 92. Explain POSIX and System-V difference?

**Answer:**\
\| Feature \| POSIX \| System-V \| \|----------\|--------\|----------\|
\| Origin \| IEEE standard for portability \| AT&T UNIX variant \| \|
Focus \| Threading, signals, I/O, process control \| IPC (shared memory,
semaphores, message queues) \| \| Thread Model \| pthreads (POSIX
threads) \| Not thread-focused \| \| Portability \| Cross-platform \|
UNIX-specific \|

------------------------------------------------------------------------

## 93. What are the points to remember when mutex locks are used to protect the critical section?

**Answer:** 1. Always initialize the mutex before use.\
2. Lock before entering the critical section.\
3. Unlock after exiting the critical section.\
4. Avoid deadlocks by locking in consistent order.\
5. Destroy mutex after use.

------------------------------------------------------------------------

## 94. By using `pthread_mutex_lock` what are you achieving?

**Answer:**\
You achieve **mutual exclusion**, ensuring that only one thread at a
time can access the shared resource protected by that mutex.

------------------------------------------------------------------------

## 95. Difference between mutex locks and semaphore?

  Aspect      Mutex                      Semaphore
  ----------- -------------------------- --------------------------------
  Ownership   Owned by a thread          Not owned
  Type        Binary (locked/unlocked)   Counting or binary
  Use case    Mutual exclusion           Signaling or resource counting

------------------------------------------------------------------------

## 96. Explain the variants of `pthread_mutex_lock`?

**Answer:** 1. `pthread_mutex_lock()` --- blocks until the mutex is
acquired.\
2. `pthread_mutex_trylock()` --- tries to acquire without blocking.\
3. `pthread_mutex_timedlock()` --- waits until timeout.

------------------------------------------------------------------------

## 97. How to create a thread?

``` c
pthread_t tid;
pthread_create(&tid, NULL, function_name, argument);
```

This creates a new thread executing `function_name`.

------------------------------------------------------------------------

## 98. Explain the compilation of a thread?

**Answer:**\
Compile with the `-pthread` flag:

``` bash
gcc program.c -o program -pthread
```

This links the pthread library and ensures thread-safe compilation.

------------------------------------------------------------------------

## 99. What are the arguments of `pthread_create`?

``` c
int pthread_create(
    pthread_t *thread,
    const pthread_attr_t *attr,
    void *(*start_routine)(void *),
    void *arg
);
```

**Explanation:**\
- `thread`: thread identifier variable.\
- `attr`: thread attributes (NULL for default).\
- `start_routine`: function to execute.\
- `arg`: argument to that function.

------------------------------------------------------------------------

## 100. Explain the return value of thread?

**Answer:**\
A thread function returns a `void*` value. It can be retrieved in the
main thread using:

``` c
void *retval;
pthread_join(thread, &retval);
```

------------------------------------------------------------------------

## 101. Explain the working of `pthread_mutex_trylock()`?

**Answer:**\
It attempts to lock the mutex.\
If the mutex is already locked, it **returns immediately** with `EBUSY`
instead of blocking.

------------------------------------------------------------------------

## 102. Explain the application of `pthread_mutex_timedlock()`?

**Answer:**\
Used when a thread must try acquiring a lock but can't wait
indefinitely.\
It takes a **timeout period**, and if the mutex isn't free within that
time, it returns `ETIMEDOUT`.

------------------------------------------------------------------------

## 103. What is mutual exclusion?

**Answer:**\
Mutual exclusion ensures that **only one thread** accesses a critical
section or shared resource at a time.\
It prevents **race conditions** and ensures data consistency, typically
enforced using mutexes or semaphores.

------------------------------------------------------------------------

**End of Document --- Thread Programming Q91--Q103**
