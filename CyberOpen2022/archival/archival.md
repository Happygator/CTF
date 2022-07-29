> Check out this weird file I found. I think this program has something to do with it too?  
> Author: rajat  

This problem gives us three files: `extract`, `extract.c`, and `arc.bin`. Upon looking at extract.c, we can see that arc.bin is a file containing multiple other files, and that extract.c is a program to extract them.  
Here is the code with comments replacing some code to make it shorter:
```
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>

typedef struct fblk_t {
    char *name;
    char *data;
    int length;
} fblk_t;

#define INLINE static inline __attribute__((always_inline))

INLINE char *read_file(char *fname, long *sz) {
    //reads the file
}

INLINE int parse_fblk(char *buff, long sz, int off, fblk_t *out) {
    if (off >= sz) return -1;

    char *blk = buff + off;
    int bsz = *(int *) blk;

    if (bsz + off > sz) return -1;

    char k1 = blk[4], k2 = blk[5];
    char *name = blk + 6;
    int namelen = (int) strnlen(name, bsz-6);
    if (namelen >= bsz-6) return -1;
    
    //XOR wizardry that I don't want to figure out
    for (int i = namelen + 7; i < bsz; i += 2) {
        if (i+1 < bsz) {
            char tmp = blk[i];
            blk[i] = blk[i+1] ^ k1;
            blk[i+1] = tmp ^ k2;
        } else {
            blk[i] ^= k1;
        }
    }

    out->name = name;
    out->data = name + namelen + 1;
    out->length = bsz - namelen - 7;

    return 0;
}

int main(int argc, char **argv) {
    if (argc < 3) {
        printf("Usage: %s <archive> <output directory>\n", argv[0]);
        return 1;
    }

    long sz = 0;
    char *buff = read_file(argv[1], &sz);
    printf("sz = %d\n", sz);
    if (!buff) {
        puts("Error reading file");
        return 1;
    }

    //checks if the file is 4 bytes or longer

    int filecnt = *(int *) buff;
    int *fileoffs = ((int *) buff) + 1;

    //a bunch of error checking
    
    FILE *fp = fopen(blk.name, "wb");
    
    //lots of other error checking

        fclose(fp);
    }

    free(buff);
}
```  

This code is a bit annoying to understand, but there are several takeaways:  

- The header of the file consists of a number(the number of files) and their addresses in the file. The program simply takes each of these addresses and begins reading the file from that chunk to decrypt a file.
- Each file's filename is not changed, but the file itself is(so you can't just rip the file out with binwalk/dd)
- Very little extra information is added, so the archive size should be only slightly larger than the combined length of all the files.  

Running `extract` on `arc.bin` *does* work, but theres a slight discrepancy: running ls -al results in:
```

899967 Jul 29 10:42 arc.bin

   629 Jul 29 10:42 aaaa.png
457093 Jul 29 10:42 paint.png
  3718 Jul 29 10:42 sentence.png
435718 Jul 29 10:42 yeehaw.png
```  
899967 - 629 - 457093 - 3718 - 435718 = 2809, easily enough to fit another file in.  
Because the filenames aren't changed, I use strings on the file, and:  
```
yeehaw.png
<many random strings here>
aaaa.png
sentence.png
<some random strings here>
paint.png
<even more random strings>
flag.png
```  
Y'know, I have a slight hunch that there might be a fifth file named flag.png, and *just maybe* it might contain the flag.  
Because the files in the archive are indicated at the header with their addresses, we can simply change the address of one of the files in the header to the beginning of flag.png, and have the program extract it.  
Now, all we have to do is find where the filename is in relation to the beginning of the file.
Looking at the hexedit for arc.bin, we see: 
```
Num. files|  |      file addresses for each of the files      |  |   some stuff idk                  |  |00000020 address| |yeehaw.png in ASCII|
04 00 00 00  6A B7 06 00  3A A6 06 00  CF A8 06 00  20 00 00 00  62 53 68 F4  D6 40 25 9C  E6 BC 05 82  17 A6 06 00  E1 91 79 65  65 68 61 77 
```
We see that 20 is the beginning of the file and yeehaw.png is 6 bytes after this, so that means the start of flag.png is 6 bytes before the first character of flag.png in the hexedit.  
With some more hexedit, we can find the address of flag.png. Then, we can simply change the address and run the program to get the flag.  

Flag: `uscg{l05t_buT_n0t_f0rGotT3n_18a9b735}`



  
  
  
