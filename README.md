# Linux-File-IO-Systems-locking
Ex07-Linux File-IO Systems-locking
# AIM:
To Write a C program that illustrates files copying and locking

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux IO Systems locking

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## 1.To Write a C program that illustrates files copying 


```
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "Usage: %s <source_file> <destination_file>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    char block[1024];
    int in, out;
    ssize_t nread;

    in = open(argv[1], O_RDONLY);

    if (in == -1) {
        perror("Error opening source file");
        exit(EXIT_FAILURE);
    }

    out = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, S_IRUSR | S_IWUSR);

    if (out == -1) {
        perror("Error opening destination file");
        close(in);
        exit(EXIT_FAILURE);
    }

    while ((nread = read(in, block, sizeof(block))) > 0) {

        if (write(out, block, nread) != nread) {
            perror("Error writing to destination file");
            close(in);
            close(out);
            exit(EXIT_FAILURE);
        }
    }

    if (nread == -1) {
        perror("Error reading source file");
    }

    close(in);
    close(out);

    return EXIT_SUCCESS;
}

```




## 2.To Write a C program that illustrates files locking

```
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>

int main()
{
    int fd;
    struct flock lock;

    fd = open("lockfile.txt", O_RDWR | O_CREAT, 0644);

    if (fd == -1)
    {
        perror("Error opening file");
        return 1;
    }

    lock.l_type = F_WRLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start = 0;
    lock.l_len = 0;
    lock.l_pid = getpid();

    printf("Trying to acquire file lock...\n");

    if (fcntl(fd, F_SETLKW, &lock) == -1)
    {
        perror("Error locking file");
        close(fd);
        return 1;
    }

    printf("File locked successfully.\n");
    printf("Process ID: %d\n", getpid());
    printf("File is locked for 10 seconds...\n");

    write(fd, "File is locked by this process.\n", 32);

    sleep(10);

    lock.l_type = F_UNLCK;

    if (fcntl(fd, F_SETLK, &lock) == -1)
    {
        perror("Error unlocking file");
        close(fd);
        return 1;
    }

    printf("File unlocked successfully.\n");

    close(fd);

    return 0;
}


```


## OUTPUT

<img width="348" height="403" alt="image" src="https://github.com/user-attachments/assets/bd70381f-dbe5-4cf1-b5da-618a7ae4e3fa" />

<img width="373" height="272" alt="image" src="https://github.com/user-attachments/assets/b139e0bd-8055-45c8-a409-9c3dcd957c33" />



# RESULT:
The programs are executed successfully.
