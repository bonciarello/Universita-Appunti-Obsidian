---
aliases: [ME]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## Hello World

```c
#include <stdio.h>
#include <string.h>
#include "mpi.h"

main(int argc, char* argv[]) {
    int my_rank; // rank of process
    int p; // number of processes
    int source; // rank of sender
    int dest; // rank of receiver
    int tag = 0; // tag for message
    char message[100]; // storage for message
    MPI_Status status; // return status for receive

    // start up MPI
    MPI_Init(&argc, &argv);

    // find out process rank
    MPI_Comm rank(MPI_COMM_WORLD, &my_rank);

    // find out number of processes
    MPI_Comm_size(MPI_COMM_WORLD, &p);

    if(my_rank != 0) {
        // create message
        sprintf(message, "Greetings from process %d!", my_rank);
        dest = 0;
        // use strlen+1 so that '\0' gets transmitted
        MPI_Send(message, strlen(message)+1, MPI_CHAR, dest, tag, MPI_COMM_WORLD);
    } else {
        for(source = 1; source < p; source++) {
            MPI_Recv(message, 100, MPI_CHAR, source, tag, MPI_COMM_WORLD, &status);
        }
    }

    // shut down MPI
    MPI_Finalize();
}
```

## Ping Pong

```c
#include "mpi.h"
#include <stdio.h>

int main(int argc, char *argv[]) {
    int numtasks, rank, dest, source, rc, count, tag, inmsg, outmsg=59;
    MPI_Status Stat;
    MPI_Init(&argc,&argv);
    MPI_Comm_size(MPI_COMM_WORLD, &numtasks);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    if (rank == 0) {
        dest = 1;
        source = 1;
        MPI_Send(&outmsg, 1, MPI_INT, dest, 17, MPI_COMM_WORLD);
        MPI_Recv(&inmsg, 1, MPI_INT, source, 23, MPI_COMM_WORLD, &Stat);
    } else if (rank == 1) {
        dest = 0;
        source = 0;
        MPI_Send(&outmsg, 1, MPI_INT, dest, 23, MPI_COMM_WORLD);
        MPI_Recv(&inmsg, 1, MPI_INT, source, 17, MPI_COMM_WORLD, &Stat);
    }
    rc = MPI_Get_count(&Stat, MPI_INT, &count);
    printf("Task %d: Received %d char(s) from task %d with tag %d \n", rank, count, Stat.MPI_SOURCE, Stat.MPI_TAG);

    MPI_Finalize();
}
```

## Vector Sum (senza broadcast e reduce)

```c
#include <mpi.h>
#include <stdio.h>
#define MAXSIZE 10

int main(int argc, char *argv[]) {
    int myid, numprocs;
    int data[MAXSIZE], i, x, low, high, myresult, result, result_temp;
    int dest, source;

    MPI_Init(&argc,&argv);
    MPI_Status status;
    MPI_Comm_size(MPI_COMM_WORLD, &numprocs);
    MPI_Comm_rank(MPI_COMM_WORLD, &myid);

    result = 0;
    myresult = 0;

    for(i = 0; i < MAXSIZE; i++) { data[i] = i; }

    x = MAXSIZE/numprocs;
    low = myid * x;
    high = low + x;

    for(i = low; i < high; i++)
        myresult = myresult + data[i];

    if (myid == 0) {
        result = myresult;
        for(source = 1; source < numprocs; source++) {
            MPI_Recv(&myresult, 1, MPI_INT, source, 0, MPI_COMM_WORLD, &status);
            result = result + myresult;
        }
    } else if (myid == 1) {
        MPI_Send(&myresult, 1, MPI_INT, 0, 0, MPI_COMM_WORLD);
    }

    if(myid == 0) printf("La somma e' %d.\n", result);

    MPI_Finalize();
    exit(0)
```
