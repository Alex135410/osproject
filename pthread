#include <pthread.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

#define MAX 10240
#define NUM_THREADS 10

int n1, n2;
char *s1, *s2;
FILE *fp;
int countArray[NUM_THREADS] = {0};

// Read input file and generate string s1/s2 and length n1/n2
int readf(FILE *fp) {
    if ((fp = fopen("strings.txt", "r")) == NULL) {
        printf("ERROR: can't open strings.txt!\n");
        return 0;
    }

    s1 = (char *)malloc(sizeof(char) * MAX);
    if (s1 == NULL) {
        printf("ERROR: Out of memory!\n");
        return -1;
    }

    s2 = (char *)malloc(sizeof(char) * MAX);
    if (s2 == NULL) {
        printf("ERROR: Out of memory\n");
        return -1;
    }

    /* Read s1 and s2 from the file */
    s1 = fgets(s1, MAX, fp);
    s2 = fgets(s2, MAX, fp);
    n1 = strlen(s1);   // Length of s1
    n2 = strlen(s2) - 1; // Length of s2 (excluding newline)

    if (s1 == NULL || s2 == NULL || n1 < n2) { // Error checking
        printf("ERROR: Input strings are invalid!\n");
        return -1;
    }
    
    return 0;
}

// Function to count substrings in a segment of s1 assigned to each thread
int num_substring(int t) {
    int i, j, k;
    int start = t * (n1 / NUM_THREADS);
    int end = (t == NUM_THREADS - 1) ? n1 - n2 : start + (n1 / NUM_THREADS) - n2 + 1;
    int local_count = 0;

    for (i = start; i < end; i++) {
        int count = 0;
        for (j = i, k = 0; k < n2; j++, k++) {
            if (s1[j] != s2[k])
                break;
            else
                count++;
        }
        if (count == n2)
            local_count++;
    }

    return local_count;
}

// Thread function to count substrings and store results in countArray
void *calSubStringThread(void *threadid) {
    long tid = (long)threadid;
    printf("Thread %ld started.\n", tid);
    countArray[tid] = num_substring(tid);
    printf("Thread %ld found %d substrings.\n", tid, countArray[tid]);
    pthread_exit(NULL);
}

int main(int argc, char *argv[]) {
    pthread_t threads[NUM_THREADS];
    int t, rc;
    int totalNum = 0;

    // Open file and read strings
    if (readf(fp) != 0) {
        printf("Failed to read input strings from file.\n");
        return -1;
    }

    // Create threads to count substrings
    for (t = 0; t < NUM_THREADS; t++) {
        rc = pthread_create(&threads[t], NULL, calSubStringThread, (void *)(size_t)t);
        if (rc) {
            printf("ERROR: return code from pthread_create() is %d\n", rc);
            exit(-1);
        }
    }

    // Wait for all threads to complete
    for (t = 0; t < NUM_THREADS; t++) {
        pthread_join(threads[t], NULL);
        totalNum += countArray[t]; // Sum up results from each thread
    }

    // Print total number of substrings found
    printf("The total number of substrings is: %d\n", totalNum);

    // Clean up allocated memory
    free(s1);
    free(s2);
    fclose(fp);

    return 0;
}
